---
title: Manage a web app in Azure App Service
description: Links to resources for managing a web app in Azure App Service.
services: app-service\web
documentationcenter: ''
author: erikre
manager: erikre
editor: ''

ms.assetid: d5e2887a-84f9-4747-a573-867635cb8b39
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/24/2016
wacn.date: ''
ms.author: rachelap

---
# Manage a web app in Azure App Service
This topic contains links to resources for managing a web app in [Azure App Service](/azure/app-service-web/app-service-changes-existing-services/). Management includes all of the tasks that keep your web app running smoothly. 

Over the lifetime of a web app, you will perform different management tasks, as you move from initial deployment to normal operation, maintenance, and updates.

Many web app management tasks can be performed in the Azure Portal Preview.

## Before you deploy your web app to production
### Choose a tier
Azure App Service is offered in five tiers: Free, Shared, Basic, Standard, and Premium. For information about the features and pricing for each tier, see [Pricing details](https://www.azure.cn/pricing/details/app-service/). 

* [App Service plans](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md) let you group multiple web apps under the same tier.
* You can always [switch tiers](web-sites-scale.md) after you create your web app.

### Configuration
Use the [Azure Portal Preview](https://portal.azure.cn/) to set various configuration options. For details, see [Configure web apps in Azure App Service](web-sites-configure.md). Here is a quick checklist:

* Select **runtime versions** for .NET, PHP, Java, or Python, if needed.
* Enable **WebSockets** if your web app uses the WebSocket protocol. (This includes apps that use [ASP.NET SignalR](http://www.asp.net/signalr) or [socket.io](web-sites-nodejs-chat-app-socketio.md).)
* Are you running continuous web jobs? If so, enable **Always On**.
* Set the **default document**, such as index.html.

In addition to these basic configuration settings, you may want to configure the following:

* **Secure Socket Layer (SSL)** encryption. To use SSL with a custom domain name, you must get an SSL certificate and configure your web app to use it. See [Enable HTTPS for a web app in Azure App Service](web-sites-configure-ssl-certificate.md).
* **Custom domain name.** Your web app automatically has a subdomain under chinacloudsites.cn. You can associate a custom domain name, such as contoso.com. See [Configure a custom domain name in Azure App Service](web-sites-custom-domain-name.md).

Language-specific configuration:

* **PHP**: [Configure PHP in Azure App Service Web Apps](web-sites-php-configure.md).
* **Python**: [Configuring Python with Azure App Service Web Apps](web-sites-python-configure.md)

## While your web app is running
While your web app is running, you want to make sure it is available, and that it scales to meet user traffic. You may also need to troubleshoot errors.

### Monitoring
* Through the Azure Portal Preview, you can [add performance metrics](web-sites-monitor.md) such as CPU usage and number of client requests.
* [Scale your web app](web-sites-scale.md) in response to traffic. Depending on your tier, you can scale the number of VMs and/or the size of the VM instances. In the Standard and Premium tiers, you can also set up autoscaling, so your web app scales automatically, either on a fixed schedule, or in response to load.  

### Backups
* Set [automatic backups](web-sites-backup.md) of your web app.
* Learn about the options for [database recovery](../sql-database/sql-database-business-continuity.md) in Azure SQL Database.

### Troubleshooting
* If something goes wrong, you can [troubleshoot in Visual Studio](web-sites-dotnet-troubleshoot-visual-studio.md#remotedebug), using diagnostic logs and live debugging in the cloud. 
* Outside of Visual Studio, there are various ways to collect diagnostic logs. See [Enable diagnostics logging for web apps in Azure App Service](web-sites-enable-diagnostic-log.md).
* For Node.js applications, see [How to debug a Node.js web app in Azure App Service](web-sites-nodejs-debug.md).

### Restoring Data
* [Restore](web-sites-restore.md) a web app that was previously backed up.

## When you update your web app
If you have not enabled automatic backups, you can create a [manual backup](web-sites-backup.md).

Consider using [staged deployment](web-sites-staged-publishing.md). This option lets you publish updates to a staging deployment that runs side-by-side with your production deployment. 

<!-- Anchors. -->

[Before you deploy your site to production]: #before-you-deploy-your-site-to-production
[While your website is running]: #while-your-website-is-running
[When you update your website]: #when-you-update-your-website