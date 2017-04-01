---
title: Deploy your first HTML web app to Azure in five minutes | Azure
description: Learn how easy it is to run web apps in App Service by deploying a sample app. Start doing real development quickly and see results immediately.
services: app-service\web
documentationcenter: ''
author: cephalin
manager: erikre
editor: ''

ms.assetid: 27c50be7-421a-47c9-8279-506519e404a4
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 01/04/2017
wacn.date: ''
ms.author: cephalin

---
# Deploy your first HTML web app to Azure in five minutes
[!INCLUDE [app-service-web-selector-get-started-nodejs-cli](../../includes/app-service-web-selector-get-started-nodejs-cli.md)]  

This tutorial helps you deploy a simple HTML+CSS web app to [Azure App Service](../app-service/app-service-value-prop-what-is.md).
You can use App Service to create web apps, [mobile app back ends](/azure/app-service-mobile/),
and [API apps](../app-service-api/app-service-api-apps-why-best-platform.md).

You will: 

* Create a web app in Azure App Service.
* Deploy HTML and CSS to it.
* See your pages running live in production.
* Update your content the same way you would [push Git commits](https://git-scm.com/docs/git-push).

## CLI versions to complete the task

You can complete the task using one of the following CLI versions:

- [Azure CLI 1.0](app-service-web-get-started-html-cli-nodejs.md) - our CLI for the classic and resource management deployment models
- [Azure CLI 2.0](app-service-web-get-started-html.md) - our next generation CLI for the resource management deployment model

## <a name="Prerequisites"></a> Prerequisites
* [Git](http://www.git-scm.com/downloads).
* [Azure CLI](../cli-install-nodejs.md).
* A Azure account. If you don't have an account, you can 
  [sign up for a trial](https://www.azure.cn/pricing/1rmb-trial/?WT.mc_id=A261C142F).

## Deploy a simple HTML site
1. Open a new Windows command prompt, PowerShell window, Linux shell, or OS X terminal. Run `git --version` and `azure --version` to verify that Git and Azure CLI
   are installed on your machine.

    ![Test installation of CLI tools for your first web app in Azure](./media/app-service-web-get-started/1-test-tools.png)

    If you haven't installed the tools, see [Prerequisites](#Prerequisites) for download links.
2. Log in to Azure like this:

        azure login -e AzureChinaCloud

    Follow the help message to continue the login process.

    ![Log in to Azure to create your first web app](./media/app-service-web-get-started/3-azure-login.png)

3. Change Azure CLI into ASM mode, then set the deployment user for App Service. You will deploy code using the credentials later.

        azure config mode asm
        azure site deployment user set --username <username> --pass <password>

4. Change to a working directory (`CD`) and clone the sample HTML site like this:

        git clone https://github.com/Azure-Samples/app-service-web-html-get-started.git

5. Change to the repository of your sample app. 

        cd app-service-web-html-get-started

6. Create the App Service app resource in Azure with a unique app name and the deployment user you configured earlier. When you're prompted, specify the number of the desired region.

        azure site create <app_name> --git --gitusername <username>

    ![Create the Azure resource for your first web app in Azure](./media/app-service-web-get-started/4-create-site.png)

    Your app is created in Azure now. Also, your current directory is Git-initialized and connected to the new App Service app as a Git remote.
    You can browse to the app URL (http://&lt;app_name>.chinacloudsites.cn) to see the beautiful default HTML page, but let's actually get your code there now.
7. Deploy your sample code to your Azure app like you would push any code with Git. When prompted, use the password you configured earlier.

        git push azure master

    ![Push code to your first web app in Azure](./media/app-service-web-get-started/5-push-code.png)

    If you used one of the language frameworks, you'll see different output. This is because `git push` not only puts code in Azure, but also triggers deployment tasks
    in the deployment engine. If you have any package.json
    (Node.js) or requirements.txt (Python) files in your project (repository) root, or if you have a packages.config file in your ASP.NET project, the deployment
    script restores the required packages for you. You can also [enable the Composer extension](web-sites-php-mysql-deploy-use-git.md#composer) to automatically process composer.json files
    in your PHP app.

Congratulations, you have deployed your app to Azure App Service.

## See your app running live
To see your app running live in Azure, run this command from any directory in your repository:

    azure site browse

## Make updates to your app
You can now use Git to push from your project (repository) root anytime to make an update to the live site. You do it the same way as when you deployed your code
the first time. For example, every time you want to push a new change that you've tested locally, just run the following commands from your project 
(repository) root:

    git add .
    git commit -m "<your_message>"
    git push azure master

## Next steps
Find the preferred development and deployment steps for your language framework:

* [.NET](web-sites-dotnet-get-started.md)
* [PHP](app-service-web-php-get-started-cli-nodejs.md)
* [Node.js](app-service-web-nodejs-get-started-cli-nodejs.md)
* [Python](web-sites-python-ptvs-django-mysql.md)
* [Java](web-sites-java-get-started.md)

Or, do more with your first web app. For example:

* Try out [other ways to deploy your code to Azure](web-sites-deploy.md).
* Take your Azure app to the next level. Authenticate your users. Scale it based on demand. Set up some performance alerts. All with a few clicks. See 
  [Add functionality to your first web app](app-service-web-get-started-2.md).