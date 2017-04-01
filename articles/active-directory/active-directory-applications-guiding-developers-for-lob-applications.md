---
title: Develop apps for Azure AD | Azure'
description: Written for the IT Pro, this article provides guidelines for integrating Azure applications with Active Directory.
services: active-directory
documentationcenter: ''
author: kgremban
manager: femila
editor: ''

ms.assetid: dd69f2bc-37c5-457c-857d-27acb84267fb
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/27/2017
wacn.date: ''
ms.author: kgremban
---

# Develop line-of-business apps for Azure Active Directory
This guide provides an overview of developing line-of-business (LoB) applications for Azure Active Directory (AD).The intended audience is Active Directory/Office 365 global administrators.

## Overview
Building applications integrated with Azure AD gives users in your organization single sign-on with Office 365. Having the application in Azure AD gives you control over the authentication policy for the application.

Register your application to use Azure Active Directory. Registering the application means that your developers can use Azure AD to authenticate users and request access to user resources such as email, calendar, and documents.

Any member of your directory (not guests) can register an application, otherwise known as *creating an application object*.

Registering an application allows any user to do the following:

- Get an identity for their application that Azure AD recognizes
- Get one or more secrets/keys that the application can use to authenticate itself to AD
- Brand the application in the Azure portal with a custom name, logo, etc.
- Apply Azure AD authorization features to their app, including:

  - Role-Based Access Control (RBAC)
  - Azure Active Directory as oAuth authorization server (secure an API exposed by the application)
- Declare required permissions necessary for the application to function as expected, including:

      - App permissions (global administrators only). For example: Role membership in another Azure AD application or role membership relative to an Azure Resource, Resource Group, or Subscription
      - Delegated permissions (any user). For example: Azure AD, Sign-in, and Read Profile

> [!NOTE]
> By default, any member can register an application. To learn how to restrict permissions for registering applications to specific members, see [How applications are added to Azure AD](./active-directory-how-applications-are-added.md#who-has-permission-to-add-applications-to-my-azure-ad-instance).
>
>

Here’s what you, the global administrator, need to do to help developers make their application ready for production:

- Configure access rules (access policy/MFA)
- Configure the app to require user assignment and assign users
- Suppress the default user consent experience

## Configure the app to require user assignment and assign users
By default, users can access applications without being assigned. However, if the application exposes roles or if you want the application to appear on a user’s access panel, you should require user assignment.

[Requiring user assignment](./active-directory-applications-guiding-developers-requiring-user-assignment.md)

If you’re an Azure AD Premium or Enterprise Mobility Suite (EMS) subscriber, we strongly recommend using groups. Assigning groups to the application allows you to delegate ongoing access management to the owner of the group. You can create the group or ask the responsible party in your organization to create the group using your group management facility.

[Assigning users to an application](./active-directory-applications-guiding-developers-assigning-users.md)  

## Suppress user consent
By default, each user goes through a consent experience to sign in. The consent experience, asking users to grant permissions to an application, can be disconcerting for users who are unfamiliar with making such decisions.

For applications that you trust, you can simplify the user experience by consenting to the application on behalf of your organization.

For more information about user consent and the consent experience in Azure, see [Integrating Applications with Azure Active Directory](./active-directory-integrating-applications.md).

## Related Articles
- [Managing access to apps with Azure AD](./active-directory-managing-access-to-apps.md)
- [Article Index for Application Management in Azure Active Directory](./active-directory-apps-index.md)