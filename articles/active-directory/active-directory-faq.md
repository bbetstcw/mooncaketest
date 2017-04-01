---
title: Azure Active Directory FAQ | Azure
description: Azure Active Directory FAQ answers questions about how to access Azure and Azure Active Directory, password management, and application access.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: femila
editor: ''

ms.assetid: b8207760-9714-4871-93d5-f9893de31c8f
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 02/07/2017
wacn.date: ''
ms.author: markvi
---

# Azure Active Directory FAQ
Azure Active Directory (Azure AD) is a comprehensive identity as a service (IDaaS) solution that spans all aspects of identity, access management, and security.

For more information, see [What is Azure Active Directory?](./active-directory-whatis.md).

## Access Azure and Azure Active Directory
**Q: Why do I get “No subscriptions found” when I try to access Azure AD in the Azure Classic Management Portal (https://manage.windowsazure.cn)?**

**A:** To access the Azure Classic Management Portal, each user needs permissions with an Azure subscription. If you have a paid Office 365 or Azure AD subscription, go to [http://aka.ms/accessAAD](http://aka.ms/accessAAD) for a one-time activation step. Otherwise, you will need to activate a [Azure account](https://www.azure.cn/pricing/1rmb-trial/) or a paid subscription.

For more information, see:

- [How Azure subscriptions are associated with Azure Active Directory](./active-directory-how-subscriptions-associated-directory.md)
- [Manage the directory for your Office 365 subscription in Azure](./active-directory-manage-o365-subscription.md)

- - -
**Q: What’s the relationship between Azure AD, Office 365, and Azure?**

**A:** Azure AD provides you with common identity and access capabilities to all web services. Whether you are using Office 365, Azure, Intune, or others, you're already using Azure AD to help turn on sign-on and access management for all these services.

All users who are set up to use web services are defined as user accounts in one or more Azure AD instances. You can set up these accounts for free Azure AD capabilities like cloud application access.

Azure AD paid services like Enterprise Mobility + Security complement other web services like Office 365 and Azure with comprehensive enterprise-scale management and security solutions.
- - -
**Q:  Why can I sign in to the Azure portal but not the Azure Classic Management Portal?**

**A:**  The Azure portal does not require a valid subscription, and the Classic Management Portal does require a valid subscription.  If you do not have a subscription, you can't sign in to the Classic Management Portal.
- - -
**Q:  What are the differences between Subscription Administrator and Directory Administrator?**

**A:** By default, you are assigned the Subscription Administrator role when you sign up for Azure. A subscription admin can use either a Microsoft account or a work or school account from the directory that the Azure subscription is associated with.  This role is authorized to manage services in the Azure portal.

If others need to sign in and access services by using the same subscription, you can add them as co-admins. This role has the same access privileges as the service admin, but can’t change the association of subscriptions to Azure directories.  For additional information on subscription admins, see [How Azure subscriptions are associated with Azure Active Directory](./active-directory-how-subscriptions-associated-directory.md).

Azure AD has a different set of admin roles to manage the directory and identity-related features.  These admins will have access to various features in the Azure portal or the Azure Classic Management Portal. The admin's role determines what they can do, like create or edit users, assign administrative roles to others, reset user passwords, manage user licenses, or manage domains.  For additional information on Azure AD directory admins and their roles, see [Assigning administrator roles in Azure Active Directory](./active-directory-assign-admin-roles.md).

Additionally, Azure AD paid services like Enterprise Mobility + Security complement other web services, such as Office 365 and Azure, with comprehensive enterprise-scale management and security solutions.

- - -
**Q: Is there a report that shows when my Azure AD user licenses will expire?**

**A:** No.  This is not currently available.

- - -

## Get started with Hybrid Azure AD

**Q: How do I leave a tenant when I am added as a collaborator?**

**A:** When you are added to another organization's tenant as a collaborator, you can use the "tenant switcher" in the upper right to switch between tenants.  Currently, there is no way to leave the inviting organization, and Microsoft is working on providing this functionality.  Until this feature is available, you can ask the inviting organization to remove you from their tenant.
- - -
**Q: How can I connect my on-premises directory to Azure AD?**

**A:** You can connect your on-premises directory to Azure AD by using Azure AD Connect.

For more information, see [Integrating your on-premises identities with Azure Active Directory](./active-directory-aadconnect.md).

- - -
**Q: How do I set up SSO between my on-premises directory and my cloud applications?**

**A:** You only need to set up single sign-on (SSO) between your on-premises directory and Azure AD. As long as you access your cloud applications through Azure AD, the service automatically drives your users to correctly authenticate with their on-premises credentials.

Implementing SSO from on-premises can be easily achieved with federation solutions such as Active Directory Federation Services (AD FS), or by configuring password hash sync. You can easily deploy both options by using the Azure AD Connect configuration wizard.

For more information, see [Integrating your on-premises identities with Azure Active Directory](./active-directory-aadconnect.md).

- - -
**Q: Does Azure AD provide a self-service portal for users in my organization?**

**A:** Yes, Azure AD provides you with the [Azure AD Access Panel](https://login.partner.microsoftonline.cn) for user self-service and application access. If you are an Office 365 customer, you can find many of the same capabilities in the Office 365 portal.

- - -
## Password management
**Q: Can I use Azure AD password write-back without password sync? (In this scenario, is it possible to use Azure AD self-service password reset (SSPR) with password write-back and not store passwords in the cloud?)**

**A:** You do not need to synchronize your Active Directory passwords to Azure AD to enable write-back. In a federated environment, Azure AD single sign-on (SSO) relies on the on-premises directory to authenticate the user. This scenario does not require the on-premises password to be tracked in Azure AD.

- - -
**Q: How long does it take for a password to be written back to Active Directory on-premises?**

**A:** Password write-back operates in real time.

For more information, see [Getting started with password management](./active-directory-passwords-getting-started.md).

- - -
**Q: Can I use password write-back with passwords that are managed by an admin?**

**A:** Yes, if you have password write-back enabled, the password operations performed by an admin are written back to your on-premises environment.  

For more answers to password-related questions, see [Password management frequently asked questions](./active-directory-passwords-faq.md).
- - -
**Q:  What can I do if I can't remember my existing Office 365/Azure AD password while trying to change my password?**

**A:** For this type of situation, there are a couple of options.  Use self-service password reset (SSPR) if it's available.  Whether SSPR works depends on how it's configured.  For more information, see [How does the password reset portal work](./active-directory-passwords-learn-more.md#how-does-the-password-reset-portal-work).

For Office 365 users, your admin can reset the password by using the steps outlined in [Reset user passwords](https://support.office.com/en-us/article/Admins-Reset-user-passwords-7A5D073B-7FAE-4AA5-8F96-9ECD041ABA9C?ui=en-US&rs=en-US&ad=US).

For Azure AD accounts, admins can reset passwords by using one of the following:

- [Reset accounts in the Classic Management Portal](./active-directory-create-users-reset-password.md)
- [Using PowerShell](https://docs.microsoft.com/en-us/powershell/msonline/v1/Set-MsolUserPassword?redirectedfrom=msdn)

- - -
## Application access
**Q: Where can I find a list of applications that are pre-integrated with Azure AD and their capabilities?**

**A:** Azure AD has more than 2,600 pre-integrated applications from Microsoft, application service providers, and partners. All pre-integrated applications support single sign-on (SSO). SSO lets you use your organizational credentials to access your apps. Some of the applications also support automated provisioning and de-provisioning.

For a complete list of the pre-integrated applications, see the [Active Directory Marketplace](https://azure.microsoft.com/marketplace/active-directory/).

- - -
**Q: What if the application I need is not in the Azure AD marketplace?**

**A:** With Azure AD Premium, you can add and configure any application that you want. Depending on your application’s capabilities and your preferences, you can configure SSO and automated provisioning.  

For more information, see:

- [Using SCIM to enable automatic provisioning of users and groups from Azure Active Directory to applications](./active-directory-scim-provisioning.md) 

- - -
**Q: How do users sign in to applications by using Azure AD?**

**A:** Azure AD provides several ways for users to view and access their applications, such as:

- The Azure AD access panel
- The Office 365 application launcher
- Direct sign-in to federated apps
- Deep links to federated, password-based, or existing apps

For more information, see [Deploying Azure AD integrated applications to users](./active-directory-appssoaccess-whatis.md#deploying-azure-ad-integrated-applications-to-users).

- - -
**Q: What are the different ways Azure AD enables authentication and single sign-on to applications?**

**A:** Azure AD supports many standardized protocols for authentication and authorization, such as SAML 2.0, OpenID Connect, OAuth 2.0, and WS-Federation. Azure AD also supports password vaulting and automated sign-in capabilities for apps that only support forms-based authentication.  

For more information, see:

- [Authentication Scenarios for Azure AD](./active-directory-authentication-scenarios.md)
- [Active Directory authentication protocols](./active-directory-developers-guide.md)
- [How does single sign-on with Azure Active Directory work?](./active-directory-appssoaccess-whatis.md#how-does-single-sign-on-with-azure-active-directory-work)

- - -

**Q: What is Automated User Provisioning for SaaS Apps?**

**A:** Use Azure AD to automate the creation, maintenance, and removal of user identities in many popular cloud SaaS apps.

- - -
**Q:  Can I set up a secure LDAP connection with Azure AD?**

**A:**  No.  Azure AD does not support the LDAP protocol.