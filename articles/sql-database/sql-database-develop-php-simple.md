---
title: Connect to SQL Database by using PHP on Windows | Azure
description: Presents a sample PHP program that connects to Azure SQL Database from a Windows client, and provides links to the necessary software components needed by the client.
services: sql-database
documentationcenter: ''
author: meet-bhagdev
manager: jhubbard
editor: ''

ms.assetid: 4e71db4a-a22f-4f1c-83e5-4a34a036ecf3
ms.service: sql-database
ms.custom: development
ms.workload: drivers
ms.tgt_pltfrm: na
ms.devlang: php
ms.topic: article
ms.date: 02/03/2017
wacn.date: ''
ms.author: meetb
---

# Connect to SQL Database by using PHP on Windows

[!INCLUDE [sql-database-develop-includes-selector-language-platform-depth](../../includes/sql-database-develop-includes-selector-language-platform-depth.md)] 

This topic illustrates how you can connect to Azure SQL Database from a client application written in PHP that runs on Windows.

## Step 1:  Configure Development Environment
[Configure development environment for PHP development](https://docs.microsoft.com/sql/connect/php/step-1-configure-development-environment-for-php-development/)

## Step 2: Create a SQL database

See the [getting started page](./sql-database-get-started.md) to learn how to create a sample database.  It is important you follow the guide to create an **AdventureWorks database template**. The samples shown below only work with the **AdventureWorks schema**.

## Step 3: Get Connection Details

[!INCLUDE [sql-database-include-connection-string-details-20-portalshots](../../includes/sql-database-include-connection-string-details-20-portalshots.md)]

## Step 4: Run sample code
* [Proof of concept connecting to SQL using PHP](https://docs.microsoft.com/sql/connect/php/step-3-proof-of-concept-connecting-to-sql-using-php/)
* [Connect resiliently to SQL with PHP](https://docs.microsoft.com/sql/connect/php/step-4-connect-resiliently-to-sql-with-php/)

## Next steps

* Review the [SQL Database Development Overview](./sql-database-develop-overview.md)
* More information on the [Microsoft PHP Driver for SQL Server](https://docs.microsoft.com/sql/connect/php/microsoft-php-driver-for-sql-server/)
* For more information regarding PHP installation and usage, see [Accessing SQL Server Databases with PHP](http://social.technet.microsoft.com/wiki/contents/articles/1258.accessing-sql-server-databases-from-php.aspx).

## Additional resources 

* [Design Patterns for Multi-tenant SaaS Applications with Azure SQL Database](./sql-database-design-patterns-multi-tenancy-saas-applications.md)
* Explore all the [capabilities of SQL Database](https://www.azure.cn/home/features/sql-databases/)