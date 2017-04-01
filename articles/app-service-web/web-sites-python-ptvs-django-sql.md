---
title: Django and SQL Database on Azure with Python Tools 2.2 for Visual Studio
description: Learn how to use the Python Tools for Visual Studio to create a Django web app that stores data in a SQL database instance and deploy it to Azure App Service Web Apps.
services: app-service\web
tags: python
documentationcenter: python
author: huguesv
manager: erikre
editor: ''

ms.assetid: 3a677e64-b5a9-4d43-b9c0-66246368b483
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: python
ms.topic: article
ms.date: 07/07/2016
wacn.date: ''
ms.author: huvalo

---
# Django and SQL Database on Azure with Python Tools 2.2 for Visual Studio

[!INCLUDE [azure-sdk-developer-differences](../../includes/azure-sdk-developer-differences.md)]

In this tutorial, we'll use [Python Tools for Visual Studio] to create a simple polls web app using one of the PTVS sample templates.

We'll learn how to use a SQL database hosted on Azure, how to configure the web app to use a SQL database, and how to publish the web app to [Azure App Service Web Apps](/azure/app-service-web/app-service-changes-existing-services/).

See the [Python Developer Center] for more articles that cover development of Azure App Service Web Apps with PTVS using Bottle, Flask and Django web frameworks, with Azure Table Storage, MySQL and SQL Database services. While this article focuses on App Service, the steps are similar when developing [Azure Cloud Services].

## <a name="prerequisites"></a> Prerequisites
* Visual Studio 2015
* [Python 2.7 32-bit]
* [Python Tools 2.2 for Visual Studio]
* [Python Tools 2.2 for Visual Studio Samples VSIX]
* [Azure SDK Tools for VS 2015]
* Django 1.9 or later

[!INCLUDE [create-account-and-websites-note](../../includes/create-account-and-websites-note.md)]

## Create the Project
In this section, we'll create a Visual Studio project using a sample template. We'll create a virtual environment and install required packages. We'll create a local database using sqlite. Then we'll run the web app locally.

1. In Visual Studio, select **File**, **New Project**.
2. The project templates from the [Python Tools 2.2 for Visual Studio Samples VSIX] are available under **Python**, **Samples**. Select **Polls Django Web Project** and click OK to create the project.

     ![New Project Dialog](./media/web-sites-python-ptvs-django-sql/PollsDjangoNewProject.png)
3. You will be prompted to install external packages. Select **Install into a virtual environment**.

     ![External Packages Dialog](./media/web-sites-python-ptvs-django-sql/PollsDjangoExternalPackages.png)
4. Select **Python 2.7** as the base interpreter.

     ![Add Virtual Environment Dialog](./media/web-sites-python-ptvs-django-sql/PollsCommonAddVirtualEnv.png)
5. In **Solution Explorer**, right-click on the project node and select **Python**, and then select **Django Migrate**.  Then select **Django Create Superuser**.
6. This will open a Django Management Console and create a sqlite database in the project folder. Follow the prompts to create a user.
7. Confirm that the application works by pressing <kbd>F5</kbd>.
8. Click **Log in** from the navigation bar at the top.

     ![Web Browser](./media/web-sites-python-ptvs-django-sql/PollsDjangoCommonBrowserLocalMenu.png)
9. Enter the credentials for the user you created when you synchronized the database.

     ![Web Browser](./media/web-sites-python-ptvs-django-sql/PollsDjangoCommonBrowserLocalLogin.png)
10. Click **Create Sample Polls**.

      ![Web Browser](./media/web-sites-python-ptvs-django-sql/PollsDjangoCommonBrowserNoPolls.png)
11. Click on a poll and vote.

      ![Web Browser](./media/web-sites-python-ptvs-django-sql/PollsDjangoSqliteBrowser.png)

## Create a SQL Database
For the database, we'll create an Azure SQL database.

You can create a database by following these steps.

1. Log into the [Azure Portal Preview].
2. At the bottom of the navigation pane, click **NEW**. , click **Data + Storage** > **SQL Database**.
3. Configure the new SQL Database by creating a new resource group and select the appropriate location for it.
4. Once the SQL Database is created, click **Open in Visual Studio** in the database blade.
5. Click **Configure your firewall**.
6. In the **Firewall Settings** blade, add a firewall rule with **START IP** and **END IP** set to the public IP address of your development machine. Click **Save**.

    This will allow connections to the database server from your development machine.
7. Back in the database blade, click **Properties**, then click **Show database connection strings**.
8. Use the copy button to put the value of **ADO.NET** on the clipboard.

## Configure the Project
In this section, we'll configure our web app to use the SQL database
we just created. We'll also install additional Python packages required to use SQL
databases with Django. Then we'll run the web app locally.

1. In Visual Studio, open **settings.py**, from the *ProjectName* folder. Temporarily paste the connection string in the editor. The connection string is in this format:

        Server=<ServerName>,<ServerPort>;Database=<DatabaseName>;User ID=<UserName>;Password={your_password_here};Encrypt=True;TrustServerCertificate=False;Connection Timeout=30;

    Edit the definition of `DATABASES` to use the values above.

        DATABASES = {
            'default': {
                'ENGINE': 'sql_server.pyodbc',
                'NAME': '<DatabaseName>',
                'USER': '<UserName>',
                'PASSWORD': '{your_password_here}',
                'HOST': '<ServerName>',
                'PORT': '<ServerPort>',
                'OPTIONS': {
                    'driver': 'SQL Server Native Client 11.0',
                    'MARS_Connection': 'True',
                }
            }
        }

1. In Solution Explorer, under **Python Environments**, right-click on the virtual environment and select **Install Python Package**.
2. Install the package `pyodbc` using **pip**.

    ![Install Python Package Dialog](./media/web-sites-python-ptvs-django-sql/PollsDjangoSqlInstallPackagePyodbc.png)
3. Install the package `django-pyodbc-azure` using **pip**.

    ![Install Python Package Dialog](./media/web-sites-python-ptvs-django-sql/PollsDjangoSqlInstallPackageDjangoPyodbcAzure.png)
4. In **Solution Explorer**, right-click on the project node and select **Python**, and then select **Django Migrate**.  Then select **Django Create Superuser**.

    This will create the tables for the SQL database we created in the previous section. Follow the prompts to create a user, which doesn't have to match the user in the sqlite database created in the first section.
5. Run the application with `F5`. Polls that are created with **Create Sample Polls** and the data submitted by voting will be serialized in the SQL database.

## Publish the web app to Azure App Service
The Azure .NET SDK provides an easy way to deploy your web web app to Azure App Service Web Apps.

1. In **Solution Explorer**, right-click on the project node and select **Publish**.

     ![Publish Web Dialog](./media/web-sites-python-ptvs-django-sql/PollsCommonPublishWebSiteDialog.png)
2. Click on **Azure Web Apps**.
3. Click on **New** to create a new web app.
4. Fill in the following fields and click **Create**.

    * **Web App name**
    * **App Service plan**
    * **Resource group**
    * **Region**
    * Leave **Database server** set to **No database**
5. Accept all other defaults and click **Publish**.
6. Your web browser will open automatically to the published web app. You should see the web app working as expected, using the **SQL** database hosted on Azure.

    Congratulations!

    ![Web Browser](./media/web-sites-python-ptvs-django-sql/PollsDjangoAzureBrowser.png)

## Next steps
Follow these links to learn more about Python Tools for Visual Studio, Django and SQL Database.

* [Python Tools for Visual Studio Documentation]
    * [Web Projects]
    * [Cloud Service Projects]
    * [Remote Debugging on Azure]
* [Django Documentation]
* [SQL Database]

## What's changed
* For a guide to the change from Websites to App Service see: [Azure App Service and Its Impact on Existing Azure Services](/azure/app-service-web/app-service-changes-existing-services/)

<!--Link references-->
[Python Developer Center]: /develop/python/
[Azure Cloud Services]: ../cloud-services/cloud-services-python-ptvs.md

<!--External Link references-->
[Azure Portal Preview]: https://portal.azure.cn
[Python Tools for Visual Studio]: http://aka.ms/ptvs
[Python Tools 2.2 for Visual Studio]: http://go.microsoft.com/fwlink/?LinkID=624025
[Python Tools 2.2 for Visual Studio Samples VSIX]: http://go.microsoft.com/fwlink/?LinkID=624025
[Azure SDK Tools for VS 2015]: http://go.microsoft.com/fwlink/?LinkId=518003
[Python 2.7 32-bit]: http://go.microsoft.com/fwlink/?LinkId=517190
[Python Tools for Visual Studio Documentation]: http://aka.ms/ptvsdocs
[Remote Debugging on Azure]: http://go.microsoft.com/fwlink/?LinkId=624026
[Web Projects]: http://go.microsoft.com/fwlink/?LinkId=624027
[Cloud Service Projects]: http://go.microsoft.com/fwlink/?LinkId=624028
[Django Documentation]: https://www.djangoproject.com/
[SQL Database]: /azure/sql-database/