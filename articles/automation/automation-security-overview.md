---
title: Intro to authentication in Azure Automation | Azure
description: This article provides an overview of Automation security and the different authentication methods available for Automation Accounts in Azure Automation.
services: automation
documentationcenter: ''
author: MGoedtel
manager: jwhit
editor: tysonn
keywords: automation security, secure automation; automation authentication

ms.assetid: 4a6bc2f5-c5a2-4dfb-b10d-7950d750dee8
ms.service: automation
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/01/2017
wacn.date: ''
ms.author: magoedte

---
# Introduction to authentication in Azure Automation  
Azure Automation allows you to automate tasks against resources in Azure, on-premises.  In order for a runbook to perform its required actions, it must have permissions to securely access the resources with the minimal rights required within the subscription.

This article will cover the various authentication scenarios supported by Azure Automation and will show you how to get started based on the environment or environments you need to manage.  

## Automation Account overview
When you start Azure Automation for the first time, you must create at least one Automation account. Automation accounts allow you to isolate your Automation resources (runbooks, assets, configurations) from the resources contained in other Automation accounts. You can use Automation accounts to separate resources into separate logical environments. For example, you might use one account for development, another for production, and another for your on-premises environment.  An Azure Automation account is different from your Azure account or accounts created in your Azure subscription.

The Automation resources for each Automation account are associated with a single Azure region, but Automation accounts can manage all the resources in your subscription. The main reason to create Automation accounts in different regions would be if you have policies that require data and resources to be isolated to a specific region.

All of the tasks that you perform against resources using Azure Resource Manager and the Azure cmdlets in Azure Automation must authenticate to Azure using Azure Active Directory organizational identity credential-based authentication.  Certificate-based  authentication was the original authentication method with Azure Service Management mode, but it was complicated to setup.  Authenticating to Azure with Azure AD user was introduced back in 2014 to not only simplify the process to configure an Authentication account, but also support the ability to non-interactively authenticate to Azure with a single user account that worked with both Azure Resource Manager and classic resources.   

Role-based access control is available with Azure Resource Manager to grant permitted actions to an Azure AD user account and Run As account, and authenticate that service principal.  Please read [Role-based access control in Azure Automation article](automation-role-based-access-control.md) for further information to help develop your model for managing Automation permissions.  

## Authentication methods
The following table summarizes the different authentication methods for each environment supported by Azure Automation and the article describing how to setup authentication for your runbooks.

| Method | Environment | Article |
| --- | --- | --- |
| Azure AD User Account |Azure Resource Manager and Azure Service Management |[Authenticate Runbooks with Azure AD User account](automation-sec-configure-aduser-account.md) |
| Azure Run As Account |Azure Resource Manager | None |
| Azure Classic Run As Account |Azure Service Management | None |