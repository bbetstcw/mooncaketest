---
title: Export a SQL Server database to a BACPAC file using SqlPackage | Microsoft Docs
description: Microsoft Azure SQL Database, database migration, export database, export BACPAC file, sqlpackage
services: sql-database
documentationcenter: ''
author: CarlRabeler
manager: jhubbard
editor: ''

ms.assetid: 7b9541c5-5590-4c70-ad36-73007389f6dc
ms.service: sql-database
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: sqldb-migrate
ms.date: 11/08/2016
ms.author: carlrab

---
# Export a SQL Server database to a BACPAC file using SqlPackage

- [Azure portal](./sql-database-export.md)
- [SSMS](./sql-database-cloud-migrate-compatible-export-bacpac-ssms.md)
- [SqlPackage](./sql-database-cloud-migrate-compatible-export-bacpac-sqlpackage.md)
- [PowerShell](./sql-database-export-powershell.md)

This article shows how to export a SQL Server database to a [BACPAC](https://msdn.microsoft.com/zh-cn/library/ee210546.aspx#Anchor_4) file using the [SqlPackage](https://msdn.microsoft.com/zh-cn/library/hh550080.aspx) command-line utility. This utility ships with the latest versions of [SQL Server Management Studio](https://msdn.microsoft.com/zh-cn/library/mt238290.aspx) and [SQL Server Data Tools for Visual Studio](https://msdn.microsoft.com/zh-cn/library/mt204009.aspx), or you can download the latest version of [SqlPackage](https://www.microsoft.com/en-us/download/details.aspx?id=53876) directly from the Microsoft download center.

1. Open a command prompt and change a directory containing the sqlpackage.exe command-line utility - this utility ships with both Visual Studio and SQL Server. Use search on your computer to find the path in your environment.
2. Execute the following sqlpackage.exe command with the following arguments for your environment:

    sqlpackage.exe /Action:Export /ssn:< server_name > /sdn:< database_name > /tf:< target_file >

    | Argument  | Description  |
    |---|---|
    | < server_name >  | source server name  |
    | < database_name >  | source database name  |
    | < target_file >  | file name and location for BACPAC file  |

    ![Export a data-tier application from the Tasks menu](./media/sql-database-cloud-migrate/TestForCompatibilityUsingSQLPackage01b.png)

## Next steps
- [Newest version of SSDT](https://msdn.microsoft.com/zh-cn/library/mt204009.aspx)
- [Newest version of SQL Server Management Studio](https://msdn.microsoft.com/zh-cn/library/mt238290.aspx)
- [Import a BACPAC to Azure SQL Database using SSMS](./sql-database-cloud-migrate-compatible-import-bacpac-ssms.md)
- [Import a BACPAC to Azure SQL Database SqlPackage](./sql-database-cloud-migrate-compatible-import-bacpac-sqlpackage.md)
- [Import a BACPAC to Azure SQL Database Azure Portal](./sql-database-import.md)
- [Import a BACPAC to Azure SQL Database PowerShell](./sql-database-import-powershell.md)

## Additional resources

- [SQL Database V12](./sql-database-v12-whats-new.md)
- [Transact-SQL partially or unsupported functions](./sql-database-transact-sql-information.md)
- [Migrate non-SQL Server databases using SQL Server Migration Assistant](http://blogs.msdn.com/b/ssma/)