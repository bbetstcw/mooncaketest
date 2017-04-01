---
title: Determine SQL Database compatibility using SqlPackage.exe | Microsoft Docs
description: Azure SQL Database, database migration, SQL Database compatibility, SqlPackage
services: sql-database
documentationcenter: ''
author: CarlRabeler
manager: jhubbard
editor: ''

ms.assetid: ebe2cf3e-9561-4ede-8fb9-f3e6ce3fb7a6
ms.service: sql-database
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: sqldb-migrate
ms.date: 11/08/2016
ms.author: carlrab

---
# Determine SQL Database compatibility using SqlPackage.exe

> [!div class="op_single_selector"]
>- [SSDT](/documentation/articles/sql-database-cloud-migrate-fix-compatibility-issues-ssdt/)
>- [SqlPackage](./sql-database-cloud-migrate-determine-compatibility-sqlpackage.md)
>- [SSMS](./sql-database-cloud-migrate-determine-compatibility-ssms.md)
>- [SAMW](./sql-database-cloud-migrate-fix-compatibility-issues.md)

In this article you learn to determine if a SQL Server database is compatible to migrate to SQL Database using the [SqlPackage](https://msdn.microsoft.com/zh-cn/library/hh550080.aspx) command-prompt utility.

## Using SqlPackage.exe
1. Open a command prompt and change a directory containing the newest version of sqlpackage.exe. This utility ships with the latest versions of [SQL Server Management Studio](https://msdn.microsoft.com/zh-cn/library/mt238290.aspx) and [SQL Server Data Tools for Visual Studio](https://msdn.microsoft.com/zh-cn/library/mt204009.aspx), or you can download the latest version of [SqlPackage](https://www.microsoft.com/en-us/download/details.aspx?id=53876) directly from the Microsoft download center.
2. Execute the following SqlPackage command with the following arguments for your environment:

    'sqlpackage.exe /Action:Export /ssn:< server_name > /sdn:< database_name > /tf:< target_file > /p:TableData=< schema_name.table_name > > < output_file > 2>&1'

    | Argument  | Description  |
    |---|---|
    | < server_name >  | source server name  |
    | < database_name >  | source database name  |
    | < target_file >  | file name and location for BACPAC file  |
    | < schema_name.table_name >  | the tables for which data are output to the target file  |
    | < output_file >  | the file name and location for the output file with errors, if any  |

    The reason for the /p:TableName argument is that we only want to test for database compatibility for export to Azure SQL DB V12 rather than export the data from all tables. Unfortunately, the export argument for sqlpackage.exe does not support extracting zero tables. You need to specify at least one table, such as a single small table. The < output_file > contains the report of any errors. The "> 2>&1" string pipes both the standard output and the standard error resulting from the command execution to specified output file.

    ![Export a data-tier application from the Tasks menu](./media/sql-database-cloud-migrate/TestForCompatibilityUsingSQLPackage01.png)

3. Open the output file and review the compatibility errors, if any. 

    ![Export a data-tier application from the Tasks menu](./media/sql-database-cloud-migrate/TestForCompatibilityUsingSQLPackage02.png)

## Next steps

- [Newest version of SSDT](https://msdn.microsoft.com/zh-cn/library/mt204009.aspx)
[Newest version of SQL Server Management Studio](https://msdn.microsoft.com/zh-cn/library/mt238290.aspx)
- [Fix database migration compatibility issues](./sql-database-cloud-migrate.md#fix-database-migration-compatibility-issues)
- [Migrate a compatible SQL Server database to SQL Database](./sql-database-cloud-migrate.md#migrate-a-compatible-sql-server-database-to-sql-database)

## Additional resources

- [SQL Database V12](./sql-database-v12-whats-new.md)
- [Transact-SQL partially or unsupported functions](./sql-database-transact-sql-information.md)
- [Migrate non-SQL Server databases using SQL Server Migration Assistant](http://blogs.msdn.com/b/ssma/)