---
title: Troubleshoot common connection issues to Azure SQL Database
description: Steps to identify and resolve common connection errors for Azure SQL Database.
services: sql-database
documentationcenter: ''
author: dalechen
manager: cshepard
editor: ''

ms.assetid: ac463d1c-aec8-443d-b66e-fa5eadcccfa8
ms.service: sql-database
ms.custom: troubleshoot
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/03/2017
ms.author: daleche

---
# Troubleshoot connection issues to Azure SQL Database
When the connection to Azure SQL Database fails, you receive [error messages](sql-database-develop-error-messages.md). This article is a centralized topic that helps you troubleshoot Azure SQL Database connectivity issues. It introduces [the common causes](#cause) of connection issues, recommends [a troubleshooting tool](#try-the-troubleshooter-for-azure-sql-database-connectivity-issues) that helps you identity the problem, and provides troubleshooting steps to solve [transient errors](#troubleshoot-transient-errors) and [persistent or non-transient errors](#troubleshoot-the-persistent-errors). Finally, it lists [all the relevant articles for Azure SQL Database connectivity issues](#all-topics-for-azure-sql-database-connection-problems).

If you encounter the connection issues, try the troubleshoot steps that are described in this article.
[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## Cause
Connection problems may be caused by any of the following:

* Failure to apply best practices and design guidelines during the application design process.  See [SQL Database Development Overview](sql-database-develop-overview.md) to get started.
* Azure SQL Database reconfiguration
* Firewall settings
* Connection time-out
* Incorrect login information
* Maximum limit reached on some Azure SQL Database resources

Generally, connection issues to Azure SQL Database can be classified as follows:

* [Transient errors (short-lived or intermittent)](#troubleshoot-transient-errors)
* [Persistent or non-transient errors (errors that regularly recur)](#troubleshoot-the-persistent-errors)

## Try the troubleshooter for Azure SQL Database connectivity issues
If you encounter a specific connection error, try [this tool](https://support.microsoft.com/help/10085/troubleshooting-connectivity-issues-with-microsoft-azure-sql-database), which will help you quickly identity and resolve your problem.

## Troubleshoot transient errors

When an application connects to an Azure SQL database, you receive the following error message:

```
Error code 40613: "Database <x> on server <y> is not currently available. Please retry the connection later. If the problem persists, contact customer support, and provide them the session tracing ID of <z>"
```

> [!NOTE]
> This error message is typically transient (short-lived).
> 
> 

This error occurs when the Azure database is being moved (or reconfigured) and your application loses its connection to the SQL database. SQL database reconfiguration events occurs because of a planned event (for example, a software upgrade) or an unplanned event (for example, a process crash, or load balancing). Most reconfiguration events are generally short-lived and should be completed in less than 60 seconds at most. However, these events can occasionally take longer to finish, such as when a large transaction causes a long-running recovery.

### Steps to resolve transient connectivity issues

1. Check the [Azure Service Dashboard](https://www.azure.cn/support/service-dashboard/) for any known outages that occurred during the time during which the errors were reported by the application.
2. Applications that connect to a cloud service such as Azure SQL Database should expect periodic reconfiguration events and implement retry logic to handle these errors instead of surfacing these as application errors to users. Review the [Transient errors](sql-database-connectivity-issues.md) section and the best practices and design guidelines at [SQL Database Development Overview](sql-database-develop-overview.md) for more information and general retry strategies. Then, see code samples at [Connection Libraries for SQL Database and SQL Server](sql-database-libraries.md) for specifics.
3. As a database approaches its resource limits, it can seem to be a transient connectivity issue. See [Troubleshooting Performance Issues](sql-database-troubleshoot-performance.md).
4. If connectivity problems continue, or if the duration for which your application encounters the error exceeds 60 seconds or if you see multiple occurrences of the error in a given day, file an Azure support request by selecting **Get Support** on the [Azure Support](https://www.azure.cn/support/contact/) site.

## Troubleshoot persistent errors (non-transient errors)
If the application persistently fails to connect to Azure SQL Database, it usually indicates an issue with one of the following:

* Firewall configuration. The Azure SQL database or client-side firewall is blocking connections to Azure SQL Database.
* Network reconfiguration on the client side: for example, a new IP address or a proxy server.
* User error: for example, mistyped connection parameters, such as the server name in the connection string.

### Steps to resolve persistent connectivity issues
1. Set up [firewall rules](sql-database-configure-firewall-settings.md) to allow the client IP address. For temporary testing purposes, set up a firewall rule using 0.0.0.0 as the starting IP address range and using 255.255.255.255 as the ending IP address range. This will open the server to all IP addresses. If this resolves your connectivity issue, remove this rule and create a firewall rule for a appropriately limited IP address or address range. 
2. On all firewalls between the client and the Internet, make sure that port 1433 is open for outbound connections. Review [Configure the Windows Firewall to Allow SQL Server Access](https://msdn.microsoft.com/library/cc646023.aspx) and [Hybrid Identity Required Ports and Protocols](../active-directory/connect/active-directory-aadconnect-ports.md) for additional pointers related to additional ports that you need to open for Azure Active Directory authentication.
3. Verify your connection string and other connection settings. See the Connection String section in the [connectivity issues topic](sql-database-connectivity-issues.md#connections-to-azure-sql-database).
4. Check service health in the dashboard. If you think there’s a regional outage, see [Recover from an outage](sql-database-disaster-recovery.md) for steps to recover to a new region.

## All topics for Azure SQL Database connection problems
The following table lists every connection problem topic that applies directly to the Azure SQL Database service.

| &nbsp; | Title | Description |
| ---:|:--- |:--- |
| 1 |[Troubleshoot connection issues to Azure SQL Database](sql-database-troubleshoot-common-connection-issues.md) |This is the landing page for troubleshooting connectivity issues in Azure SQL Database. It describes how to identify and resolve transient errors and persistent or non-transient errors in Azure SQL Database. |
| 2 |[Troubleshoot, diagnose, and prevent SQL connection errors and transient errors for SQL Database](sql-database-connectivity-issues.md) |Learn how to troubleshoot, diagnose, and prevent a SQL connection error or transient error in Azure SQL Database. |

| 3 | [Troubleshoot connectivity issues with Microsoft Azure SQL Database](https://support.microsoft.com/help/10085/troubleshooting-connectivity-issues-with-microsoft-azure-sql-database) | This tool helps identity your problem solve connection errors. |
| 4 |[Troubleshoot "Database &lt;x&gt; on server &lt;y&gt; is not currently available. Please retry the connection later" error](sql-database-troubleshoot-connection.md) |Describes how to identify and resolve a 40613 error: "Database &lt;x&gt; on server &lt;y&gt; is not currently available. Please retry the connection later." |
| 5 |[SQL error codes for SQL Database client applications: Database connection error and other issues](sql-database-develop-error-messages.md) |Provides info about SQL error codes for SQL Database client applications, such as common database connection errors, database copy issues, and general errors. |
| 6 |[Azure SQL Database performance guidance for single databases](sql-database-performance-guidance.md) |Provides guidance to help you determine which service tier is right for your application. Also provides recommendations for tuning your application to get the most out of your Azure SQL Database. |
| 7 |[SQL Database Development Overview](sql-database-develop-overview.md) |Provides links to code samples for various technologies that you can use to connect to and interact with Azure SQL Database. |

## Next steps
* [Troubleshoot Azure SQL Database performance issues](sql-database-troubleshoot-performance.md)


## Additional resources
* [SQL Database Development Overview](sql-database-develop-overview.md)
* [General transient fault-handling guidance](../best-practices-retry-general.md)
* [Connection libraries for SQL Database and SQL Server](sql-database-libraries.md)

