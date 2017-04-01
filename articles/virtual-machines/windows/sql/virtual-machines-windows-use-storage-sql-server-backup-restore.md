---
title: How to use Azure storage for SQL Server backup and restore | Azure
description: Learn how to back up SQL Server to Azure Storage. Explains the benefits of backing up SQL databases to Azure Storage.
services: virtual-machines-windows
documentationcenter: ''
author: MikeRayMSFT
manager: jhubbard
tags: azure-service-management

ms.assetid: 0db7667d-ef63-4e2b-bd4d-574802090f8b
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 01/31/2017
wacn.date: ''
ms.author: mikeray

---
# Use Azure Storage for SQL Server Backup and Restore
## Overview
Starting with SQL Server 2012 SP1 CU2, you can now write SQL Server backups directly to the Azure Blob storage service. You can use this functionality to back up to and restore from the Azure Blob service with an on-premises SQL Server database or a SQL Server database in an Azure virtual machine. Backup to cloud offers benefits of availability, limitless geo-replicated off-site storage, and ease of migration of data to and from the cloud. You can issue BACKUP or RESTORE statements by using Transact-SQL or SMO.

SQL Server 2016 introduces new capabilities; you can use [file-snapshot backup](http://msdn.microsoft.com/zh-cn/library/mt169363.aspx) to perform nearly instantaneous backups and incredibly quick restores.

This topic explains why you might choose to use Azure storage for SQL backups and then describes the components involved. You can use the resources provided at the end of the article to access walkthroughs and additional information to start using this service with your SQL Server backups.

## Benefits of Using the Azure Blob Service for SQL Server Backups
There are several challenges that you face when backing up SQL Server. These challenges include storage management, risk of storage failure, access to off-site storage, and hardware configuration. Many of these challenges are addressed by using the Azure Blob store service for SQL Server backups. Consider the following benefits:

* **Ease of use**: Storing your backups in Azure blobs can be a convenient, flexible, and easy to access off-site option. Creating off-site storage for your SQL Server backups can be as easy as modifying your existing scripts/jobs to use the **BACKUP TO URL** syntax. Off-site storage should typically be far enough from the production database location to prevent a single disaster that might impact both the off-site and production database locations. By choosing to [geo-replicate your Azure blobs](../../../storage/storage-redundancy.md), you have an extra layer of protection in the event of a disaster that could affect the whole region.
* **Backup archive**: The Azure Blob Storage service offers a better alternative to the often used tape option to archive backups. Tape storage might require physical transportation to an off-site facility and measures to protect the media. Storing your backups in Azure Blob Storage provides an instant, highly available, and a durable archiving option.
* **Managed hardware**: There is no overhead of hardware management with Azure services. Azure services manage the hardware and provide geo-replication for redundancy and protection against hardware failures.
* **Unlimited storage**: By enabling a direct backup to Azure blobs, you have access to virtually unlimited storage. Alternatively, backing up to an Azure virtual machine disk has limits based on machine size. There is a limit to the number of disks you can attach to an Azure virtual machine for backups. This limit is 16 disks for an extra large instance and fewer for smaller instances.
* **Backup availability**: Backups stored in Azure blobs are available from anywhere and at any time and can easily be accessed for restores to either an on-premises SQL Server or another SQL Server running in an Azure Virtual Machine, without the need for database attach/detach or downloading and attaching the VHD.
* **Cost**: Pay only for the service that is used. Can be cost-effective as an off-site and backup archive option. See the [Azure pricing calculator](https://www.azure.cn/pricing/calculator/ "Pricing Calculator"), and the [Azure Pricing article](https://www.azure.cn/pricing/overview/ "Pricing article") for more information.
* **Storage snapshots**: When database files are stored in an Azure blob and you are using SQL Server 2016, you can use [file-snapshot backup](http://msdn.microsoft.com/zh-cn/library/mt169363.aspx) to perform nearly instantaneous backups and incredibly quick restores.

For more details, see [SQL Server Backup and Restore with Azure Blob Storage Service](https://msdn.microsoft.com/zh-cn/library/jj919148.aspx).

The following two sections introduce the Azure Blob storage service, including the required SQL Server components. It is important to understand the components and their interaction to successfully use backup and restore from the Azure Blob storage service.

## Azure Blob Storage Service Components
The following Azure components are used when backing up to the Azure Blob storage service.

| Component | Description |
| --- | --- |
| **Storage Account** |The storage account is the starting point for all storage services. To access an Azure Blob Storage service, first create an Azure Storage account. For more information about Azure Blob storage service, see [How to use the Azure Blob Storage Service](/azure/storage/storage-dotnet-how-to-use-blobs/) |
| **Container** |A container provides a grouping of a set of blobs, and can store an unlimited number of Blobs. To write a SQL Server backup to an Azure Blob service, you must have at least the root container created. |
| **Blob** |A file of any type and size. Blobs are addressable using the following URL format: **https://[storage account].blob.core.chinacloudapi.cn/[container]/[blob]**. For more information about page Blobs, see [Understanding Block and Page Blobs](http://msdn.microsoft.com/zh-cn/library/azure/ee691964.aspx) |

## SQL Server Components
The following SQL Server components are used when backing up to the Azure Blob storage service.

| Component | Description |
| --- | --- |
| **URL** |A URL specifies a Uniform Resource Identifier (URI) to a unique backup file. The URL is used to provide the location and name of the SQL Server backup file. The URL must point to an actual blob, not just a container. If the blob does not exist, it is created. If an existing blob is specified, BACKUP fails, unless the > WITH FORMAT option is specified. The following is an example of the URL you would specify in the BACKUP command: **http[s]://[storageaccount].blob.core.chinacloudapi.cn/[container]/[FILENAME.bak]**. HTTPS is recommended but not required. |
| **Credential** |The information that is required to connect and authenticate to Azure Blob storage service is stored as a Credential.  In order for SQL Server to write backups to an Azure Blob or restore from it, a SQL Server credential must be created. For more information, see [SQL Server Credential](https://msdn.microsoft.com/zh-cn/library/ms189522.aspx). |

> [!NOTE]
> If you choose to copy and upload a backup file to the Azure Blob storage service, you must use a page blob type as your storage option if you are planning to use this file for restore operations. RESTORE from a block blob type will fail with an error.
> 
> 

## Next steps
1. Create an Azure account if you don't already have one. If you are evaluating Azure, consider the [trial](https://www.azure.cn/pricing/1rmb-trial/).
2. Then go through one of the following tutorials that walk you through creating a storage account and performing a restore.

    * **SQL Server 2014**: [Tutorial: SQL Server 2014 Backup and Restore to Azure Blob Storage Service](https://msdn.microsoft.com/zh-cn/library/jj720558\(v=sql.120\).aspx).
    * **SQL Server 2016**: [Tutorial: Using the Azure Blob storage service with SQL Server 2016 databases](https://msdn.microsoft.com/zh-cn/library/dn466438.aspx)
3. Review additional documentation starting with [SQL Server Backup and Restore with Azure Blob Storage Service](https://msdn.microsoft.com/zh-cn/library/jj919148.aspx).

If you have any problems, review the topic [SQL Server Backup to URL Best Practices and Troubleshooting](https://msdn.microsoft.com/zh-cn/library/jj919149.aspx).

For other SQL Server backup and restore options, see [Backup and Restore for SQL Server in Azure Virtual Machines](virtual-machines-windows-sql-backup-recovery.md).