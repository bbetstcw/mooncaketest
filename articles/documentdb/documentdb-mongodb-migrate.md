---
title: Use mongoimport & mongorestore with Azure DocumentDB | Azure
description: Learn how to use mongoimport and mongorestore to import data to a DocumentDB: API for MongoDB account
keywords: mongoimport, mongorestore
services: documentdb
author: AndrewHoh
manager: jhubbard
editor: ''
documentationcenter: ''

ms.assetid: 352c5fb9-8772-4c5f-87ac-74885e63ecac
ms.service: documentdb
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/06/2017
wacn.date: ''
ms.author: anhoh
---

# Migrate data to DocumentDB by using mongoimport and mongorestore
> [!div class="op_single_selector"]
>- [Import to DocumentDB](./documentdb-import-data.md)
>- [Import to API for MongoDB](./documentdb-mongodb-migrate.md)

To migrate data to an Azure DocumentDB: API for MongoDB account, you must:

- Download either *mongoimport.exe* or *mongorestore.exe* from the [MongoDB Download Center](https://www.mongodb.com/download-center).
- Get your [DocumentDB support for MongoDB connection string](./documentdb-connect-mongodb-account.md).

## Before you begin

- Increase throughput: The duration of your data migration depends on the amount of throughput you set up for your collections. Be sure to increase the throughput for larger data migrations. After you've completed the migration, decrease the throughput to save costs. For more information about increasing throughput in the [Azure portal](https://portal.azure.cn), see [Performance levels and pricing tiers in DocumentDB](./documentdb-performance-levels.md).

- Enable SSL: DocumentDB has strict security requirements and standards. Be sure to enable SSL when you interact with your account. The procedures in the rest of the article include how to enable SSL for *mongoimport* and *mongorestore*.

## Find your connection string information (host, port, username, and password)

1. In the [Azure portal](https://portal.azure.cn), in the left pane, click the **NoSQL (DocumentDB)** entry.
2. In the **Subscriptions** pane, select your account name.
3. In the **Connection String** blade, click **Connection String**.  
The right pane contains all the information you need to successfully connect to your account.

    ![The "Connection String" blade](./media/documentdb-mongodb-migrate/ConnectionStringBlade.png)

## Import data to API for MongoDB with mongoimport

To import data to your DocumentDB account, use the following template to execute the import. Fill in *host*, *username*, and *password* with the values that are specific to your account.  

Template:

```
mongoimport.exe --host <your_hostname>:10250 -u <your_username> -p <your_password> --db <your_database> --collection <your_collection> --ssl --sslAllowInvalidCertificates --type json --file C:\sample.json
```

Example:  

```
mongoimport.exe --host anhoh-host.documents.azure.com:10250 -u anhoh-host -p tkvaVkp4Nnaoirnouenrgisuner2435qwefBH0z256Na24frio34LNQasfaefarfernoimczciqisAXw== --ssl --sslAllowInvalidCertificates --db sampleDB --collection sampleColl --type json --file C:\Users\anhoh\Desktop\*.json
```

## Import data to API for MongoDB with mongorestore

To restore data to your DocumentDB account, use the following template to execute the import. Fill in *host*, *username*, and *password* with the values specific to your account.

Template:

```
mongorestore.exe --host <your_hostname>:10250 -u <your_username> -p <your_password> --db <your_database> --collection <your_collection> --ssl --sslAllowInvalidCertificates <path_to_backup>
```

Example:

```
mongorestore.exe --host anhoh-host.documents.azure.com:10250 -u anhoh-host -p tkvaVkp4Nnaoirnouenrgisuner2435qwefBH0z256Na24frio34LNQasfaefarfernoimczciqisAXw== --ssl --sslAllowInvalidCertificates ./dumps/dump-2016-12-07
```

## Next steps
- For more information, explore [DocumentDB: API for MongoDB samples](./documentdb-mongodb-samples.md).