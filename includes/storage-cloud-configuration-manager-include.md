The [Microsoft Azure Configuration Manager Library for .NET](https://www.nuget.org/packages/Microsoft.WindowsAzure.ConfigurationManager/) provides a class for parsing a connection string from a configuration file. The [CloudConfigurationManager](https://msdn.microsoft.com/zh-cn/library/azure/mt634650.aspx) class parses configuration settings regardless of whether the client application is running on the desktop, on a mobile device, in an Azure virtual machine, or in an Azure cloud service.

To reference the CloudConfigurationManager package, add the following `using` directive:

```csharp
using Microsoft.Azure;	//Namespace for CloudConfigurationManager
```

Here's an example that shows how to retrieve a connection string from a configuration file:

```csharp
// Parse the connection string and return a reference to the storage account.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));
```

Using the Azure Configuration Manager is optional. You can also use an API like the .NET Framework's [ConfigurationManager](https://msdn.microsoft.com/zh-cn/library/system.configuration.configurationmanager.aspx) class.