---
title: 'Azure AD Connect: When you already have Azure AD | Microsoft Docs'
description: This topic describes how to use Connect when you have an existing Azure AD tenant.
services: active-directory
documentationcenter: ''
author: andkjell
manager: femila
editor: ''

ms.assetid:
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/22/2017
ms.author: billmath
---

# Azure AD Connect: When you have an existent tenant
Most of the topics for how to use Azure AD Connect assumes you start with a new Azure AD tenant and that there are no users or other objects there. But if you have started with an Azure AD tenant, populated it with users and other objects, and now want to use Connect, then this topic is for you.

## The basics
An object in Azure AD is either mastered in the cloud (Azure AD) or on-premises. For one single object, you cannot manage some attributes on-premises and some other attributes in Azure AD. Each object has a flag indicating where the object is managed.

You can manage some users on-premises and other in the cloud. A common scenario for this configuration is an organization with a mix of accounting workers and sales workers. The accounting workers have an on-premises AD account, but the sales workers do not, they have an account in Azure AD. You would manage some users on-premises and some in Azure AD.

If you started to manage users in Azure AD that are also in on-premises AD and later want to use Connect, then there are some additional concerns you need to consider.

## Sync with existing users in Azure AD
When you install Azure AD Connect and you start synchronizing, the Azure AD sync service (in Azure AD) does a check on every new object and try to find an existing object to match. There are three attributes used for this process: **userPrincipalName**, **proxyAddresses**, and **sourceAnchor**/**immutableID**. A match on **userPrincipalName** and **proxyAddresses** is known as a **soft match**. A match on **sourceAnchor** is known as **hard match**. For the **proxyAddresses** attribute only the value with **SMTP:**, that is the primary email address, is used for the evaluation.

The match is only evaluated for new objects coming from Connect. If you change an exiting object so it is matching any of these attributes, then you see an error instead.

If Azure AD finds an object where the attribute values are the same for an object coming from Connect and that it is already present in Azure AD, then the object in Azure AD is taken over by Connect. The previously cloud-managed object is flagged as on-premises managed. All attributes in Azure AD with a value in on-premises AD are overwritten with the on-premises value. The exception is when an attribute has a **NULL** value on-premises. In this case, the value in Azure AD remains, but you can still only change it on-premises to something else.

> [!WARNING]
> Since all attributes in Azure AD are going to be overwritten by the on-premises value, make sure you have good data on-premises. For example, if you only have managed email address in Office 365 and not kept it updated in on-premises AD DS, then you lose any values in Azure AD/Office 365 not present in AD DS.

> [!IMPORTANT]
> If you use password sync, which is always used by express settings, then the password in Azure AD is overwritten with the password in on-premises AD. If your users are used to manage different passwords, then you need to inform them that they should use the on-premises password when you have installed Connect.

The previous section and warning must be considered in your planning. If you have made many changes in Azure AD not reflected in on-premises AD DS, then you need to plan for how to populate AD DS with the updated values before you sync your objects with Azure AD Connect.

If you matched your objects with a soft-match, then the **sourceAnchor** is added to the object in Azure AD so a hard match can be used later.

### Hard-match vs Soft-match
For a new installation of Connect, there is no practical difference between a soft- and a hard-match. The difference is in a disaster recovery situation. If you have lost your server with Azure AD Connect, you can reinstall a new instance without losing any data. An object with a sourceAnchor is sent to Connect during initial install. The match can then be evaluated by the client (Azure AD Connect), which is a lot faster than doing the same in Azure AD. A hard match is evaluated both by Connect and by Azure AD. A soft match is only evaluated by Azure AD.

### Other objects than users
Users usually have both userPrincipalName and proxyAddresses, making the match easy. But other objects, such as security groups, do not have those. In this case, you can only match on a hard match using the sourceAnchor. The sourceAnchor is always the Base64 converted **objectGUID** on-premises, so you must update the value in Azure AD when you need two objects to match. The sourceAnchor/immutableID can only be updated with PowerShell and not through the portals.

## Create a new on-premises Active Directory from data in Azure AD
Some customers start with a cloud-only solution with Azure AD and they do not have an on-premises AD. Later they want to consume on-premises resources and want to build an on-premises AD based on Azure AD data. Azure AD Connect cannot help you for this scenario. It does not create users on-premises and it does not have any ability to set the password on-premises to the same as in Azure AD.

If the only reason why you plan to add on-premises AD is to support LOBs (Line-of-Business apps), then maybe you should consider to use [Azure AD domain services](../../active-directory-domain-services/index.md) instead.

## Next steps
Learn more about [Integrating your on-premises identities with Azure Active Directory](active-directory-aadconnect.md).
