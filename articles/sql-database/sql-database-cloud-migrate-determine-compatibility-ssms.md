---
title: SSMS: Azure SQL Server database compatibility | Azure
description: In this article you learn to determine if a SQL Server database is compatible to migrate to SQL Database using the Export Data Tier Application Wizard in SQL Server Management Studio.
keywords: Microsoft Azure SQL Database, database migration, SQL Database compatibility, Export Data Tier Application Wizard
services: sql-database
documentationcenter: ''
author: CarlRabeler
manager: jhubbard
editor: ''

ms.assetid: c9ead868-aa1e-4a92-a099-6baf7c0dda32
ms.service: sql-database
ms.custom: migrate and move
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: sqldb-migrate
ms.date: 01/10/2017
wacn.date: ''
ms.author: carlrab
---

# Use SQL Server Management Studio to Determine SQL Database compatibility before migration to Azure SQL Database

> [!div class="op_single_selector"]
>- [SSDT](/documentation/articles/sql-database-cloud-migrate-fix-compatibility-issues-ssdt/)
>- [SqlPackage](./sql-database-cloud-migrate-determine-compatibility-sqlpackage.md)
>- [SSMS](./sql-database-cloud-migrate-determine-compatibility-ssms.md)
>- [SAMW](./sql-database-cloud-migrate-fix-compatibility-issues.md)

In this article you learn to determine if a SQL Server database is compatible to migrate to SQL Database using the Export Data Tier Application Wizard in SQL Server Management Studio.

## Using SQL Server Management Studio
1. Verify that you have the latest version of SQL Server Management Studio. New versions of Management Studio are updated monthly to remain in sync with updates to the Azure portal.

      > [!IMPORTANT]
      > It is recommended that you always use the latest version of Management Studio to remain synchronized with updates to Azure and SQL Database. [Update SQL Server Management Studio](https://msdn.microsoft.com/zh-cn/library/mt238290.aspx).

2. Open Management Studio and connect to your source database in Object Explorer.
3. Right-click the source database in the Object Explorer, point to **Tasks**, and click **Export Data-Tier Application…**

    ![Export a data-tier application from the Tasks menu](./media/sql-database-cloud-migrate/TestForCompatibilityUsingSSMS01.png)

4. In the export wizard, click **Next**, and then on the **Settings** tab, configure the export to save the BACPAC file to either a local disk location or to an Azure blob. A BACPAC file is saved if you have no database compatibility issues. If there are compatibility issues, they are be displayed on the console.

    ![Export settings](./media/sql-database-cloud-migrate/TestForCompatibilityUsingSSMS02.png)

5. To skip exporting data, click the **Advanced tab** and clear the **Select All** checkbox. Our goal at this point is only to test for compatibility.

    ![Export settings](./media/sql-database-cloud-migrate/TestForCompatibilityUsingSSMS03.png)

6. Click **Next** and then click **Finish**. Database compatibility issues, if any, appear after the wizard validates the schema.

    ![Export settings](./media/sql-database-cloud-migrate/TestForCompatibilityUsingSSMS04.png)

7. If no errors appear, your database is compatible and you are ready to migrate. If you have errors, you need to fix them. To see the errors, click **Error** for **Validating schema**. 
    ![Export settings](./media/sql-database-cloud-migrate/TestForCompatibilityUsingSSMS05.png)

8. If the *.BACPAC file is successfully generated, then your database is compatible with SQL Database, and you are ready to migrate.

## Next steps

- [Newest version of SSDT](https://msdn.microsoft.com/zh-cn/library/mt204009.aspx)
- [Newest version of SQL Server Management Studio](https://msdn.microsoft.com/zh-cn/library/mt238290.aspx)
- [Fix database migration compatibility issues](./sql-database-cloud-migrate.md#fix-database-migration-compatibility-issues)
- [Migrate a compatible SQL Server database to SQL Database](./sql-database-cloud-migrate.md#migrate-a-compatible-sql-server-database-to-sql-database)

## Additional resources

- [SQL Database V12](./sql-database-features.md)
- [Transact-SQL partially or unsupported functions](./sql-database-transact-sql-information.md)
- [Migrate non-SQL Server databases using SQL Server Migration Assistant](http://blogs.msdn.com/b/ssma/)