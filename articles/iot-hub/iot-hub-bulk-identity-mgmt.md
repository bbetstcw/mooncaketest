---
title: Import export of Azure IoT Hub device identities | Azure
description: How to use the Azure IoT service SDK to perform bulk operations against the identity registry to import and export device identities. Import operations enable you to create, update, and delete device identities in bulk.
services: iot-hub
documentationcenter: .net
author: dominicbetts
manager: timlt
editor: ''

ms.assetid: 2ade1494-45ea-46a7-ade7-cf6e11ce62da
ms.service: iot-hub
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/04/2017
wacn.date: ''
ms.author: dobett
---

# Manage your IoT Hub device identities in bulk
Each IoT hub has an identity registry you can use to create per-device resources in the service, such as a queue that contains in-flight cloud-to-device messages. The identity registry also enables you to control access to the device-facing endpoints. This article describes how to import and export device identities in bulk to and from an identity registry.

Import and export operations take place in the context of *Jobs* that enable you to execute bulk service operations against an IoT hub.

The **RegistryManager** class includes the **ExportDevicesAsync** and **ImportDevicesAsync** methods that use the **Job** framework. These methods enable you to export, import, and synchronize the entirety of an IoT hub identity registry.

## What are Jobs?
Identity registry operations use the **Job** system when the operation:

* Has a potentially long execution time compared to standard run-time operations, or
* Returns a large amount of data to the user.

In these cases, instead of a single API call waiting or blocking on the result of the operation, the operation asynchronously creates a **Job** for that IoT hub. The operation then immediately returns a **JobProperties** object.

The following C# code snippet shows how to create an export job:

```
// Call an export job on the IoT Hub to retrieve all devices
JobProperties exportJob = await registryManager.ExportDevicesAsync(containerSasUri, false);
```

> [!NOTE]
> To use the **RegistryManager** class in your C# code, add the **Microsoft.Azure.Devices** NuGet package to your project. The **RegistryManager** class is in the **Microsoft.Azure.Devices** namespace.

Then you can use the **RegistryManager** class to query the state of the **Job** using the returned **JobProperties** metadata.

The following C# code snippet shows how to poll every five seconds to see if the job has finished executing:

```
// Wait until job is finished
while(true)
{
  exportJob = await registryManager.GetJobAsync(exportJob.JobId);
  if (exportJob.Status == JobStatus.Completed || 
      exportJob.Status == JobStatus.Failed ||
      exportJob.Status == JobStatus.Cancelled)
  {
    // Job has finished executing
    break;
  }

  await Task.Delay(TimeSpan.FromSeconds(5));
}
```

## Export devices

Use the **ExportDevicesAsync** method to export the entirety of an IoT hub identity registry to an [Azure storage](../storage/index.md) blob container using a [Shared Access Signature](https://msdn.microsoft.com/zh-cn/library/ee395415.aspx).

This method enables you to create reliable backups of your device information in a blob container that you control.

The **ExportDevicesAsync** method requires two parameters:

* A *string* that contains a URI of a blob container. This URI must contain a SAS token that grants write access to the container. The job creates a block blob in this container to store the serialized export device data. The SAS token must include these permissions:

   ```
   SharedAccessBlobPermissions.Write | SharedAccessBlobPermissions.Read | SharedAccessBlobPermissions.Delete
   ```
* A *boolean* that indicates if you want to exclude authentication keys from your export data. If **false**, authentication keys are included in export output. Otherwise, keys are exported as **null**.

The following C# code snippet shows how to initiate an export job that includes device authentication keys in the export data and then poll for completion:

```
// Call an export job on the IoT Hub to retrieve all devices
JobProperties exportJob = await registryManager.ExportDevicesAsync(containerSasUri, false);

// Wait until job is finished
while(true)
{
    exportJob = await registryManager.GetJobAsync(exportJob.JobId);
    if (exportJob.Status == JobStatus.Completed || 
        exportJob.Status == JobStatus.Failed ||
        exportJob.Status == JobStatus.Cancelled)
    {
    // Job has finished executing
    break;
    }

    await Task.Delay(TimeSpan.FromSeconds(5));
}
```

The job stores its output in the provided blob container as a block blob with the name **devices.txt**. The output data consists of JSON serialized device data, with one device per line.

The following example shows the output data:

```
{"id":"Device1","eTag":"MA==","status":"enabled","authentication":{"symmetricKey":{"primaryKey":"abc=","secondaryKey":"def="}}}
{"id":"Device2","eTag":"MA==","status":"enabled","authentication":{"symmetricKey":{"primaryKey":"abc=","secondaryKey":"def="}}}
{"id":"Device3","eTag":"MA==","status":"disabled","authentication":{"symmetricKey":{"primaryKey":"abc=","secondaryKey":"def="}}}
{"id":"Device4","eTag":"MA==","status":"disabled","authentication":{"symmetricKey":{"primaryKey":"abc=","secondaryKey":"def="}}}
{"id":"Device5","eTag":"MA==","status":"enabled","authentication":{"symmetricKey":{"primaryKey":"abc=","secondaryKey":"def="}}}
```

If you need access to this data in code, you can easily deserialize this data using the **ExportImportDevice** class. The following C# code snippet shows how to read device information that was previously exported to a block blob:

```
var exportedDevices = new List<ExportImportDevice>();

using (var streamReader = new StreamReader(await blob.OpenReadAsync(AccessCondition.GenerateIfExistsCondition(), RequestOptions, null), Encoding.UTF8))
{
  while (streamReader.Peek() != -1)
  {
    string line = await streamReader.ReadLineAsync();
    var device = JsonConvert.DeserializeObject<ExportImportDevice>(line);
    exportedDevices.Add(device);
  }
}
```

> [!NOTE]
>  You can also use the **GetDevicesAsync** method of the **RegistryManager** class to fetch a list of your devices. However, this approach has a hard cap of 1000 on the number of device objects that are returned. The expected use case for the **GetDevicesAsync** method is for development scenarios to aid debugging and is not recommended for production workloads.

## Import devices

The **ImportDevicesAsync** method in the **RegistryManager** class enables you to perform bulk import and synchronization operations in an IoT hub identity registry. Like the **ExportDevicesAsync** method, the **ImportDevicesAsync** method uses the **Job** framework.

Take care using the **ImportDevicesAsync** method because in addition to provisioning new devices in your identity registry, it can also update and delete existing devices.

> [!WARNING]
> An import operation cannot be undone. Always back up your existing data using the **ExportDevicesAsync** method to another blob container before you make bulk changes to your identity registry.
> 
> 

The **ImportDevicesAsync** method takes two parameters:

* A *string* that contains a URI of an [Azure Storage](../storage/index.md) blob container to use as *input* to the job. This URI must contain a SAS token that grants read access to the container. This container must contain a blob with the name **devices.txt** that contains the serialized device data to import into your identity registry. The import data must contain device information in the same JSON format that the **ExportImportDevice** job uses when it creates a **devices.txt** blob. The SAS token must include these permissions:

    ```
    SharedAccessBlobPermissions.Read
    ```

*  A *string* that contains a URI of an [Azure storage](../storage/index.md) blob container to use as *output* from the job. The job creates a block blob in this container to store any error information from the completed import **Job**. The SAS token must include these permissions:

    ```
    SharedAccessBlobPermissions.Write | SharedAccessBlobPermissions.Read | SharedAccessBlobPermissions.Delete
    ```

> [!NOTE]
>  The two parameters can point to the same blob container. The separate parameters simply enable more control over your data as the output container requires additional permissions.

The following C# code snippet shows how to initiate an import job:

```
JobProperties importJob = await registryManager.ImportDevicesAsync(containerSasUri, containerSasUri);
```

## Import behavior
You can use the **ImportDevicesAsync** method to perform the following bulk operations in your identity registry:

-   Bulk registration of new devices
-   Bulk deletions of existing devices
-   Bulk status changes (enable or disable devices)
-   Bulk assignment of new device authentication keys
-   Bulk auto-regeneration of device authentication keys

You can perform any combination of the preceding operations within a single **ImportDevicesAsync** call. For example, you can register new devices and delete or update existing devices at the same time. When used along with the **ExportDevicesAsync** method, you can completely migrate all your devices from one IoT hub to another.

Use the optional **importMode** property in the import serialization data for each device to control the import process per-device. The **importMode** property has the following options:

| importMode |  Description |
| -------- | ----------- |
| **createOrUpdate** | If a device does not exist with the specified **id**, it is newly registered. <br/>If the device already exists, existing information is overwritten with the provided input data without regard to the **ETag** value. |
| **create** | If a device does not exist with the specified **id**, it is newly registered. <br/>If the device already exists, an error is written to the log file. |
| **update** | If a device already exists with the specified **id**, existing information is overwritten with the provided input data without regard to the **ETag** value. <br/>If the device does not exist, an error is written to the log file. |
| **updateIfMatchETag** | If a device already exists with the specified **id**, existing information is overwritten with the provided input data only if there is an **ETag** match. <br/>If the device does not exist, an error is written to the log file. <br/>If there is an **ETag** mismatch, an error is written to the log file. |
| **createOrUpdateIfMatchETag** | If a device does not exist with the specified **id**, it is newly registered. <br/>If the device already exists, existing information is overwritten with the provided input data only if there is an **ETag** match. <br/>If there is an **ETag** mismatch, an error is written to the log file. |
| **delete** | If a device already exists with the specified **id**, it is deleted without regard to the **ETag** value. <br/>If the device does not exist, an error is written to the log file. |
| **deleteIfMatchETag** | If a device already exists with the specified **id**, it is deleted only if there is an **ETag** match. If the device does not exist, an error is written to the log file. <br/>If there is an ETag mismatch, an error is written to the log file. |

> [!NOTE]
> If the serialization data does not explicitly define an **importMode** flag for a device, it defaults to **createOrUpdate** during the import operation.

## Import devices example – bulk device provisioning 

The following C# code sample illustrates how to generate multiple device identities that:

- Include authentication keys.
- Write that device information to a block blob.
- Import the devices into the identity registry.

```
// Provision 1,000 more devices
var serializedDevices = new List<string>();

for (var i = 0; i < 1000; i++)
{
// Create a new ExportImportDevice
  var deviceToAdd = new ExportImportDevice()
  {
    Id = Guid.NewGuid().ToString(),
    Status = DeviceStatus.Enabled,
    Authentication = new AuthenticationMechanism()
    {
      SymmetricKey = new SymmetricKey()
      {
        PrimaryKey = CryptoKeyGenerator.GenerateKey(32),
        SecondaryKey = CryptoKeyGenerator.GenerateKey(32)
      }
    },
    ImportMode = ImportMode.Create
  };

  // Add device to existing list
  serializedDevices.Add(JsonConvert.SerializeObject(deviceToAdd));
}

// Write this list to the blob
var sb = new StringBuilder();
serializedDevices.ForEach(serializedDevice => sb.AppendLine(serializedDevice));
await blob.DeleteIfExistsAsync();

using (CloudBlobStream stream = await blob.OpenWriteAsync())
{
  byte[] bytes = Encoding.UTF8.GetBytes(sb.ToString());
  for (var i = 0; i < bytes.Length; i += 500)
  {
    int length = Math.Min(bytes.Length - i, 500);
    await stream.WriteAsync(bytes, i, length);
  }
}

// Call import using the same blob to add new devices!
// This normally takes 1 minute per 100 devices the normal way
JobProperties importJob = await registryManager.ImportDevicesAsync(containerSasUri, containerSasUri);

// Wait until job is finished
while(true)
{
  importJob = await registryManager.GetJobAsync(importJob.JobId);
  if (importJob.Status == JobStatus.Completed || 
      importJob.Status == JobStatus.Failed ||
      importJob.Status == JobStatus.Cancelled)
  {
    // Job has finished executing
    break;
  }

  await Task.Delay(TimeSpan.FromSeconds(5));
}
```

## Import devices example – bulk deletion
The following code sample shows you how to delete the devices you added using the previous code sample:

```
// Step 1: Update each device's ImportMode to be Delete
sb = new StringBuilder();
serializedDevices.ForEach(serializedDevice =>
{
  // Deserialize back to an ExportImportDevice
  var device = JsonConvert.DeserializeObject<ExportImportDevice>(serializedDevice);

  // Update property
  device.ImportMode = ImportMode.Delete;

  // Re-serialize
  sb.AppendLine(JsonConvert.SerializeObject(device));
});

// Step 2: Write the new import data back to the block blob
await blob.DeleteIfExistsAsync();
using (CloudBlobStream stream = await blob.OpenWriteAsync())
{
  byte[] bytes = Encoding.UTF8.GetBytes(sb.ToString());
  for (var i = 0; i < bytes.Length; i += 500)
  {
    int length = Math.Min(bytes.Length - i, 500);
    await stream.WriteAsync(bytes, i, length);
  }
}

// Step 3: Call import using the same blob to delete all devices!
importJob = await registryManager.ImportDevicesAsync(containerSasUri, containerSasUri);

// Wait until job is finished
while(true)
{
  importJob = await registryManager.GetJobAsync(importJob.JobId);
  if (importJob.Status == JobStatus.Completed || 
      importJob.Status == JobStatus.Failed ||
      importJob.Status == JobStatus.Cancelled)
  {
    // Job has finished executing
    break;
  }

  await Task.Delay(TimeSpan.FromSeconds(5));
}

```

## Getting the container SAS URI

The following code sample shows you how to generate a [SAS URI](../storage/storage-dotnet-shared-access-signature-part-2.md/) with read, write, and delete permissions for a blob container:

```
static string GetContainerSasUri(CloudBlobContainer container)
{
  // Set the expiry time and permissions for the container.
  // In this case no start time is specified, so the
  // shared access signature becomes valid immediately.
  var sasConstraints = new SharedAccessBlobPolicy();
  sasConstraints.SharedAccessExpiryTime = DateTime.UtcNow.AddHours(24);
  sasConstraints.Permissions = 
    SharedAccessBlobPermissions.Write | 
    SharedAccessBlobPermissions.Read | 
    SharedAccessBlobPermissions.Delete;

  // Generate the shared access signature on the container,
  // setting the constraints directly on the signature.
  string sasContainerToken = container.GetSharedAccessSignature(sasConstraints);

  // Return the URI string for the container,
  // including the SAS token.
  return container.Uri + sasContainerToken;
}

```

## Next steps
In this article, you learned how to perform bulk operations against the identity registry in an IoT hub. Follow these links to learn more about managing Azure IoT Hub:

* [IoT Hub metrics][lnk-metrics]
* [Operations monitoring][lnk-monitor]

To further explore the capabilities of IoT Hub, see:

* [IoT Hub developer guide][lnk-devguide]
* [Simulating a device with the IoT Gateway SDK][lnk-gateway]

[lnk-metrics]: ./iot-hub-metrics.md
[lnk-monitor]: ./iot-hub-operations-monitoring.md

[lnk-devguide]: ./iot-hub-devguide.md
[lnk-gateway]: ./iot-hub-linux-gateway-sdk-simulated-device.md