---
title: What's New in the Azure Toolkit for IntelliJ | Azure
description: Learn about the latest features in the Azure Toolkit for IntelliJ.
services: ''
documentationcenter: java
author: rmcmurray
manager: erikre
editor: ''

ms.assetid: 46ed791f-df59-416a-809e-f52345ad973c
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: multiple
ms.devlang: Java
ms.topic: article
ms.date: 12/22/2016
wacn.date: ''
ms.author: robmcm;asirveda;martinsawicki
---

# What's New in the Azure Toolkit for IntelliJ
## Azure Toolkit for IntelliJ Releases
This article contains information on the various releases and latest updates to the Azure Toolkit for IntelliJ.

> [!NOTE]
> There is also an Azure Toolkit for the Eclipse IDE. For more information, see [Azure Toolkit for Eclipse].

### August 26, 2016
The Azure Toolkit for IntelliJ - August 2016 release includes the following enhancements:

* **Custom JDK Distributions**. The Azure Toolkit for IntelliJ now supports specifying and deploying an arbitrary JDK version to your Azure WebApp container:
  * In addition to the JDKs provided by Azure, you can also choose from a wide selection of Zulu OpenJDK versions made available on Azure by Azul Systems.
  * You can also specify your own JDK distribution if you upload it as a ZIP file to your storage account.
* **Enhancements to the Azure Explorer view**:
  * Support for Virtual Machine management using Azure's new Resource Manager model: you can list, create and delete resource manager-based virtual machines without leaving the IDE.
  * Support for Storage Account blob management using Azure's Resource Manager, which complements the existing functionality for managing "classic" storage accounts.
* **Microsoft JDBC Driver 6.0 for SQL Server**. This update includes the latest JDBC driver for Microsoft SQL Server (v6.0), which is now included as a library that you can easily add to your Java projects, thereby replacing the older version.

### June 29, 2016
The Azure Toolkit for IntelliJ - June 2016 release includes the following enhancements:

* **Java 8 Requirement**. The Azure Toolkit for IntelliJ now requires Java 8, although this requirement is only for the toolkit - your applications can continue to use all versions of Java that are supported by Azure.
* **Support for the latest Java JDKs**. The latest versions of the Java JDKs are now supported by the Azure Toolkit for IntelliJ.
* **Support for Azure SDK v2.9.1**. The latest version of the Azure SDK is now the minimum pre-requisite for the Azure Toolkit for IntelliJ.
* **Integrated Samples**. The Azure Toolkit for IntelliJ now features several sample applications to help developers get started.
* **HDInsight Tool Integration**. Azure's HDInsight Tools are now bundled with the Azure Toolkit for IntelliJ. 
* **Remote Debugging of Java Web Apps**. The Azure Toolkit for IntelliJ now supports remote debugging of Java web apps on Azure App Service.

### April 12, 2016
The Azure Toolkit for IntelliJ - April 2016 release includes the following enhancements:

* **Support for Azure SDK v2.9.0**. The latest version of the Azure SDK is now the minimum pre-requisite for the Azure Toolkit for IntelliJ.
* **Miscellaneous usability, responsiveness and performance improvements related to Azure Web App support**. A number of performance optimizations in how the Toolkit communicates with Azure result in a more responsive UI.
* **Ability to delete an existing Web Application container in Azure from within IntelliJ**. The Azure Toolkit for IntelliJ now allows you to delete an existing Azure Web container without leaving IntelliJ.

## See Also
For more information about the Azure Toolkits for Java IDEs, see the following links:

* [Azure Toolkit for Eclipse]
  * [Installing the Azure Toolkit for Eclipse]
  * [What's New in the Azure Toolkit for Eclipse]
* [Azure Toolkit for IntelliJ]
  * [Installing the Azure Toolkit for IntelliJ]
  * *What's New in the Azure Toolkit for IntelliJ (This Article)*

For more information about using Azure with Java, see the [Azure Java Developer Center].

<!-- URL List -->

[Azure Toolkit for Eclipse]:./azure-toolkit-for-eclipse.md
[Azure Toolkit for IntelliJ]:./azure-toolkit-for-intellij.md
[Installing the Azure Toolkit for Eclipse]:./azure-toolkit-for-eclipse-installation.md
[Installing the Azure Toolkit for IntelliJ]:./azure-toolkit-for-intellij-installation.md
[What's New in the Azure Toolkit for Eclipse]:./azure-toolkit-for-eclipse-whats-new.md
[What's New in the Azure Toolkit for IntelliJ]:./azure-toolkit-for-intellij-whats-new.md

[Azure Java Developer Center]:/develop/java/