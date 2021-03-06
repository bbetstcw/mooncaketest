---
title: How to create a Web App with Redis Cache | Azure
description: Learn how to create a Web App with Redis Cache
services: redis-cache
documentationcenter: ''
author: steved0x
manager: douge
editor: ''

ms.assetid: 454e23d7-a99b-4e6e-8dd7-156451d2da7c
ms.service: cache
ms.workload: tbd
ms.tgt_pltfrm: cache-redis
ms.devlang: na
ms.topic: hero-article
ms.date: 03/27/2017
wacn.date: ''
ms.author: sdanie

---
# How to create a Web App with Redis Cache
> [!div class="op_single_selector"]
> * [.NET](cache-dotnet-how-to-use-azure-redis-cache.md)
> * [ASP.NET](cache-web-app-howto.md)
> * [Node.js](cache-nodejs-get-started.md)
> * [Java](cache-java-get-started.md)
> * [Python](cache-python-get-started.md)
> 
> 

[!INCLUDE [azure-sdk-developer-differences](../../includes/azure-sdk-developer-differences.md)]

This tutorial shows how to create and deploy an ASP.NET web application to a web app in Azure App Service using Visual Studio 2017. The sample application displays a list of team statistics from a database and shows different ways to use Azure Redis Cache to store and retrieve data from the cache. When you complete the tutorial you'll have a running web app that reads and writes to a database, optimized with Azure Redis Cache, and hosted in Azure.

You'll learn:

* How to create an ASP.NET MVC 5 web application in Visual Studio.
* How to access data from a database using Entity Framework.
* How to improve data throughput and reduce database load by storing and retrieving data using Azure Redis Cache.
* How to use a Redis sorted set to retrieve the top 5 teams.
* How to provision the Azure resources for the application using a Resource Manager template.
* How to publish the application to Azure using Visual Studio.

## Prerequisites
To complete the tutorial, you must have the following prerequisites.

* [Azure account](#azure-account)
* [Visual Studio 2017 with the Azure SDK for .NET](#visual-studio-2017-with-the-azure-sdk-for-net)

### <a name="azure-account"></a> Azure account
You need an Azure account to complete the tutorial. You can:

* [Open an Azure account](https://www.azure.cn/pricing/1rmb-trial/?WT.mc_id=redis_cache_hero). You get credits that can be used to try out paid Azure services. Even after the credits are used up, you can keep the account and use free Azure services and features.

### <a name="visual-studio-2015-with-the-azure-sdk-for-net"></a> Visual Studio 2017 with the Azure SDK for .NET
The tutorial is written for Visual Studio 2017 with the [Azure SDK for .NET](https://www.visualstudio.com/news/releasenotes/vs2017-relnotes#azuretools). The Azure SDK 2.9.5 is included with the Visual Studio installer.

If you have Visual Studio 2015, you can follow the tutorial with the [Azure SDK for .NET](../dotnet-sdk.md) 2.8.2 or later. [Download the latest Azure SDK for Visual Studio 2015 here](http://go.microsoft.com/fwlink/?linkid=518003). Visual Studio is automatically installed with the SDK if you don't already have it. Some screens may look different from the illustrations shown in this tutorial.

If you have Visual Studio 2013, you can [download the latest Azure SDK for Visual Studio 2013](http://go.microsoft.com/fwlink/?LinkID=324322). Some screens may look different from the illustrations shown in this tutorial.

## Create the Visual Studio project
1. Open Visual Studio and click **File**, **New**, **Project**.
2. Expand the **Visual C#** node in the **Templates** list, select **Cloud**, and click **ASP.NET Web Application**. Ensure that **.NET Framework 4.5.2** or higher is selected.  Type **ContosoTeamStats** into the **Name** textbox and click **OK**.

    ![Create project][cache-create-project]
3. Select **MVC** as the project type. 

    Ensure that **No Authentication** is specified for the **Authentication** settings. Depending on your version of Visual Studio, the default may be set to something else. To change it, click **Change Authentication** and select **No Authentication**.

    If you are following along with Visual Studio 2015, clear the **Host in the cloud** checkbox. You'll [provision the Azure resources](#provision-the-azure-resources) and [publish the application to Azure](#publish-the-application-to-azure) in subsequent steps in the tutorial. For an example of provisioning an App Service web app from Visual Studio by leaving **Host in the cloud** checked, see [Get started with Web Apps in Azure App Service, using ASP.NET and Visual Studio](../app-service-web/app-service-web-get-started-dotnet.md).

    ![Select project template][cache-select-template]
4. Click **OK** to create the project.

## Create the ASP.NET MVC application
In this section of the tutorial, you'll create the basic application that reads and displays team statistics from a database.

* [Add the Entity Framework NuGet package](#add-the-entity-framework-nuget-package)
* [Add the model](#add-the-model)
* [Add the controller](#add-the-controller)
* [Configure the views](#configure-the-views)

### Add the Entity Framework NuGet package

1. Click **NuGet Package Manager**, **Package Manager Console** from the **Tools** menu.
2. Run the following command from the **Package Manager Console** window.

    ```
    Install-Package EntityFramework
    ```

For more information about this package, see the [EntityFramework](https://www.nuget.org/packages/EntityFramework/) NuGet page.

### <a name="add-the-model"></a> Add the model
1. Right-click **Models** in **Solution Explorer**, and choose **Add**, **Class**. 

    ![Add model][cache-model-add-class]
2. Enter `Team` for the class name and click **Add**.

    ![Add model class][cache-model-add-class-dialog]
3. Replace the `using` statements at the top of the `Team.cs` file with the following `using` statements.

    ```c#
    using System;
    using System.Collections.Generic;
    using System.Data.Entity;
    using System.Data.Entity.SqlServer;
    ```

1. Replace the definition of the `Team` class with the following code snippet that contains an updated `Team` class definition as well as some other Entity Framework helper classes. For more information on the code first approach to Entity Framework that's used in this tutorial, see [Code first to a new database](https://msdn.microsoft.com/data/jj193542).

    ```c#
    public class Team
    {
        public int ID { get; set; }
        public string Name { get; set; }
        public int Wins { get; set; }
        public int Losses { get; set; }
        public int Ties { get; set; }

        static public void PlayGames(IEnumerable<Team> teams)
        {
            // Simple random generation of statistics.
            Random r = new Random();

            foreach (var t in teams)
            {
                t.Wins = r.Next(33);
                t.Losses = r.Next(33);
                t.Ties = r.Next(0, 5);
            }
        }
    }

    public class TeamContext : DbContext
    {
        public TeamContext()
            : base("TeamContext")
        {
        }

        public DbSet<Team> Teams { get; set; }
    }

    public class TeamInitializer : CreateDatabaseIfNotExists<TeamContext>
    {
        protected override void Seed(TeamContext context)
        {
            var teams = new List<Team>
            {
                new Team{Name="Adventure Works Cycles"},
                new Team{Name="Alpine Ski House"},
                new Team{Name="Blue Yonder Airlines"},
                new Team{Name="Coho Vineyard"},
                new Team{Name="Contoso, Ltd."},
                new Team{Name="Fabrikam, Inc."},
                new Team{Name="Lucerne Publishing"},
                new Team{Name="Northwind Traders"},
                new Team{Name="Consolidated Messenger"},
                new Team{Name="Fourth Coffee"},
                new Team{Name="Graphic Design Institute"},
                new Team{Name="Nod Publishers"}
            };

            Team.PlayGames(teams);

            teams.ForEach(t => context.Teams.Add(t));
            context.SaveChanges();
        }
    }

    public class TeamConfiguration : DbConfiguration
    {
        public TeamConfiguration()
        {
            SetExecutionStrategy("System.Data.SqlClient", () => new SqlAzureExecutionStrategy());
        }
    }
    ```

1. In **Solution Explorer**, double-click **web.config** to open it.

    ![Web.config][cache-web-config]
2. Add the following `connectionStrings` section. The name of the connection string must match the name of the Entity Framework database context class which is `TeamContext`.

    ```xml
    <connectionStrings>
        <add name="TeamContext" connectionString="Data Source=(LocalDB)\v11.0;AttachDbFilename=|DataDirectory|\Teams.mdf;Integrated Security=True"     providerName="System.Data.SqlClient" />
    </connectionStrings>
    ```

    You can add the new `connectionStrings` section so that it follows `configSections`, as shown in the following example.

    ```xml
    <configuration>
      <configSections>
        <!-- For more information on Entity Framework configuration, visit http://go.microsoft.com/fwlink/?LinkID=237468 -->
        <section name="entityFramework" type="System.Data.Entity.Internal.ConfigFile.EntityFrameworkSection, EntityFramework, Version=6.0.0.0, Culture=neutral, PublicKeyToken=b77a5c561934e089" requirePermission="false" />
      </configSections>
      <connectionStrings>
        <add name="TeamContext" connectionString="Data Source=(LocalDB)\v11.0;AttachDbFilename=|DataDirectory|\Teams.mdf;Integrated Security=True"     providerName="System.Data.SqlClient" />
      </connectionStrings>
      ...
      ```

### <a name="add-the-controller"></a> Add the controller
1. Press **F6** to build the project. 
2. In **Solution Explorer**, right-click the **Controllers** folder and choose **Add**, **Controller**.

    ![Add controller][cache-add-controller]
3. Choose **MVC 5 Controller with views, using Entity Framework**, and click **Add**. If you get an error after clicking **Add**, ensure that you have built the project first.

    ![Add controller class][cache-add-controller-class]
4. Select **Team (ContosoTeamStats.Models)** from the **Model class** drop-down list. Select **TeamContext (ContosoTeamStats.Models)** from the **Data context class** drop-down list. Type `TeamsController` in the **Controller** name textbox (if it is not automatically populated). Click **Add** to create the controller class and add the default views.

    ![Configure controller][cache-configure-controller]
5. In **Solution Explorer**, expand **Global.asax** and double-click **Global.asax.cs** to open it.

    ![Global.asax.cs][cache-global-asax]
6. Add the following two `using` statements at the top of the file under the other `using` statements.

    ```c#
    using System.Data.Entity;
    using ContosoTeamStats.Models;
    ```

1. Add the following line of code at the end of the `Application_Start` method.

    ```c#
    Database.SetInitializer<TeamContext>(new TeamInitializer());
    ```

1. In **Solution Explorer**, expand `App_Start` and double-click `RouteConfig.cs`.

    ![RouteConfig.cs][cache-RouteConfig-cs]
2. Replace `controller = "Home"` in the following code in the `RegisterRoutes` method with `controller = "Teams"` as shown in the following example.

    ```c#
    routes.MapRoute(
        name: "Default",
        url: "{controller}/{action}/{id}",
        defaults: new { controller = "Teams", action = "Index", id = UrlParameter.Optional }
    );
    ```

### <a name="configure-the-views"></a> Configure the views
1. In **Solution Explorer**, expand the **Views** folder and then the **Shared** folder, and double-click **_Layout.cshtml**. 

    ![_Layout.cshtml][cache-layout-cshtml]
2. Change the contents of the `title` element and replace `My ASP.NET Application` with `Contoso Team Stats` as shown in the following example.

    ```html
    <title>@ViewBag.Title - Contoso Team Stats</title>
    ```

1. In the `body` section, update the first `Html.ActionLink` statement and replace `Application name` with `Contoso Team Stats` and replace `Home` with `Teams`.

    * Before: `@Html.ActionLink("Application name", "Index", "Home", new { area = "" }, new { @class = "navbar-brand" })`
    * After: `@Html.ActionLink("Contoso Team Stats", "Index", "Teams", new { area = "" }, new { @class = "navbar-brand" })`

        ![Code changes][cache-layout-cshtml-code]
2. Press **Ctrl+F5** to build and run the application. This version of the application reads the results directly from the database. Note the **Create New**, **Edit**, **Details**, and **Delete** actions that were automatically added to the application by the **MVC 5 Controller with views, using Entity Framework** scaffold. In the next section of the tutorial you'll add Redis Cache to optimize the data access and provide additional features to the application.

![Starter application][cache-starter-application]

## <a name="configure-the-application-to-use-redis-cache"></a> Configure the application to use Redis Cache
In this section of the tutorial, you'll configure the sample application to store and retrieve Contoso team statistics from an Azure Redis Cache instance by using the [StackExchange.Redis](https://github.com/StackExchange/StackExchange.Redis) cache client.

* [Configure the application to use StackExchange.Redis](#configure-the-application-to-use-stackexchangeredis)
* [Update the TeamsController class to return results from the cache or the database](#update-the-teamscontroller-class-to-return-results-from-the-cache-or-the-database)
* [Update the Create, Edit, and Delete methods to work with the cache](#update-the-create-edit-and-delete-methods-to-work-with-the-cache)
* [Update the Teams Index view to work with the cache](#update-the-teams-index-view-to-work-with-the-cache)

### <a name="configure-the-application-to-use-stackexchangeredis"></a> Configure the application to use StackExchange.Redis
1. To configure a client application in Visual Studio using the StackExchange.Redis NuGet package, click **NuGet Package Manager**, **Package Manager Console** from the **Tools** menu.
2. Run the following command from the `Package Manager Console` window.

    ```
    Install-Package StackExchange.Redis
    ```

    The NuGet package downloads and adds the required assembly references for your client application to access Azure Redis Cache with the StackExchange.Redis cache client. If you prefer to use a strong-named version of the `StackExchange.Redis` client library, install the `StackExchange.Redis.StrongName` package.
3. In **Solution Explorer**, expand the **Controllers** folder and double-click **TeamsController.cs** to open it.

    ![Teams controller][cache-teamscontroller]
4. Add the following two `using` statements to **TeamsController.cs**.

    ```c#   
    using System.Configuration;
    using StackExchange.Redis;
    ```

5. Add the following two properties to the `TeamsController` class.

    ```c#   
    // Redis Connection string info
    private static Lazy<ConnectionMultiplexer> lazyConnection = new Lazy<ConnectionMultiplexer>(() =>
    {
        string cacheConnection = ConfigurationManager.AppSettings["CacheConnection"].ToString();
        return ConnectionMultiplexer.Connect(cacheConnection);
    });

    public static ConnectionMultiplexer Connection
    {
        get
        {
            return lazyConnection.Value;
        }
    }
    ```

6. Create a file on your computer named `WebAppPlusCacheAppSecrets.config` and place it in a location that won't be checked in with the source code of your sample application, should you decide to check it in somewhere. In this example the `AppSettingsSecrets.config` file is located at `C:\AppSecrets\WebAppPlusCacheAppSecrets.config`.

    Edit the `WebAppPlusCacheAppSecrets.config` file and add the following contents. If you run the application locally this information is used to connect to your Azure Redis Cache instance. Later in the tutorial you'll provision an Azure Redis Cache instance and update the cache name and password. If you don't plan to run the sample application locally you can skip the creation of this file and the subsequent steps that reference the file, because when you deploy to Azure the application retrieves the cache connection information from the app setting for the Web App and not from this file. Since the `WebAppPlusCacheAppSecrets.config` is not deployed to Azure with your application, you don't need it unless you are going to run the application locally.

    ```xml
    <appSettings>
      <add key="CacheConnection" value="MyCache.redis.cache.chinacloudapi.cn,abortConnect=false,ssl=true,password=..."/>
    </appSettings>
    ```

1. In **Solution Explorer**, double-click **web.config** to open it.

    ![Web.config][cache-web-config]
2. Add the following `file` attribute to the `appSettings` element. If you used a different file name or location, substitute those values for the ones shown in the example.

    * Before: `<appSettings>`
    * After: ` <appSettings file="C:\AppSecrets\WebAppPlusCacheAppSecrets.config">`

    The ASP.NET runtime merges the contents of the external file with the markup in the `<appSettings>` element. The runtime ignores the file attribute if the specified file cannot be found. Your secrets (the connection string to your cache) are not included as part of the source code for the application. When you deploy your web app to Azure, the `WebAppPlusCacheAppSecrests.config` file won't be deployed (that's what you want). There are several ways to specify these secrets in Azure, and in this tutorial they are configured automatically for you when you [provision the Azure resources](#provision-the-azure-resources) in a subsequent tutorial step. For more information about working with secrets in Azure, see [Best practices for deploying passwords and other sensitive data to ASP.NET and Azure App Service](http://www.asp.net/identity/overview/features-api/best-practices-for-deploying-passwords-and-other-sensitive-data-to-aspnet-and-azure).

### <a name="update-the-teamscontroller-class-to-return-results-from-the-cache-or-the-database"></a> Update the TeamsController class to return results from the cache or the database
In this sample, team statistics can be retrieved from the database or from the cache. Team statistics are stored in the cache as a serialized `List<Team>`, and also as a sorted set using Redis data types. When retrieving items from a sorted set, you can retrieve some, all, or query for certain items. In this sample you'll query the sorted set for the top 5 teams ranked by number of wins.

> [!NOTE]
> It is not required to store the team statistics in multiple formats in the cache in order to use Azure Redis Cache. This tutorial uses multiple formats to demonstrate some of the different ways and different data types you can use to cache data.
> 
> 

1. Add the following `using` statements to the `TeamsController.cs` file at the top with the other `using` statements.

    ```c#   
    using System.Diagnostics;
    using Newtonsoft.Json;
    ```

2. Replace the current `public ActionResult Index()` method implementation with the following implementation.

    ```c#
    // GET: Teams
    public ActionResult Index(string actionType, string resultType)
    {
        List<Team> teams = null;

        switch(actionType)
        {
            case "playGames": // Play a new season of games.
                PlayGames();
                break;

            case "clearCache": // Clear the results from the cache.
                ClearCachedTeams();
                break;

            case "rebuildDB": // Rebuild the database with sample data.
                RebuildDB();
                break;
        }

        // Measure the time it takes to retrieve the results.
        Stopwatch sw = Stopwatch.StartNew();

        switch(resultType)
        {
            case "teamsSortedSet": // Retrieve teams from sorted set.
                teams = GetFromSortedSet();
                break;

            case "teamsSortedSetTop5": // Retrieve the top 5 teams from the sorted set.
                teams = GetFromSortedSetTop5();
                break;

            case "teamsList": // Retrieve teams from the cached List<Team>.
                teams = GetFromList();
                break;

            case "fromDB": // Retrieve results from the database.
            default:
                teams = GetFromDB();
                break;
        }

        sw.Stop();
        double ms = sw.ElapsedTicks / (Stopwatch.Frequency / (1000.0));

        // Add the elapsed time of the operation to the ViewBag.msg.
        ViewBag.msg += " MS: " + ms.ToString();

        return View(teams);
    }
    ```

1. Add the following three methods to the `TeamsController` class to implement the `playGames`, `clearCache`, and `rebuildDB` action types from the switch statement added in the previous code snippet.

    The `PlayGames` method updates the team statistics by simulating a season of games, saves the results to the database, and clears the now outdated data from the cache.

    ```c#
    void PlayGames()
    {
        ViewBag.msg += "Updating team statistics. ";
        // Play a "season" of games.
        var teams = from t in db.Teams
                    select t;

        Team.PlayGames(teams);

        db.SaveChanges();

        // Clear any cached results
        ClearCachedTeams();
    }
    ```

    The `RebuildDB` method reinitializes the database with the default set of teams, generates statistics for them, and clears the now outdated data from the cache.

    ```c#
    void RebuildDB()
    {
        ViewBag.msg += "Rebuilding DB. ";
        // Delete and re-initialize the database with sample data.
        db.Database.Delete();
        db.Database.Initialize(true);

        // Clear any cached results
        ClearCachedTeams();
    }
    ```

    The `ClearCachedTeams` method removes any cached team statistics from the cache.

    ```c#
    void ClearCachedTeams()
    {
        IDatabase cache = Connection.GetDatabase();
        cache.KeyDelete("teamsList");
        cache.KeyDelete("teamsSortedSet");
        ViewBag.msg += "Team data removed from cache. ";
    } 
    ```

1. Add the following four methods to the `TeamsController` class to implement the various ways of retrieving the team statistics from the cache and the database. Each of these methods returns a `List<Team>` which is then displayed by the view.

    The `GetFromDB` method reads the team statistics from the database.

    ```c#
    List<Team> GetFromDB()
    {
        ViewBag.msg += "Results read from DB. ";
        var results = from t in db.Teams
            orderby t.Wins descending
            select t; 

        return results.ToList<Team>();
    }
    ```

    The `GetFromList` method reads the team statistics from cache as a serialized `List<Team>`. If there is a cache miss, the team statistics are read from the database and then stored in the cache for next time. In this sample we're using JSON.NET serialization to serialize the .NET objects to and from the cache. For more information, see [How to work with .NET objects in Azure Redis Cache](cache-dotnet-how-to-use-azure-redis-cache.md#work-with-net-objects-in-the-cache).

    ```c#
    List<Team> GetFromList()
    {
        List<Team> teams = null;

        IDatabase cache = Connection.GetDatabase();
        string serializedTeams = cache.StringGet("teamsList");
        if (!String.IsNullOrEmpty(serializedTeams))
        {
            teams = JsonConvert.DeserializeObject<List<Team>>(serializedTeams);

            ViewBag.msg += "List read from cache. ";
        }
        else
        {
            ViewBag.msg += "Teams list cache miss. ";
            // Get from database and store in cache
            teams = GetFromDB();

            ViewBag.msg += "Storing results to cache. ";
            cache.StringSet("teamsList", JsonConvert.SerializeObject(teams));
        }
        return teams;
    }
    ```

    The `GetFromSortedSet` method reads the team statistics from a cached sorted set. If there is a cache miss, the team statistics are read from the database and stored in the cache as a sorted set.

    ```c#
    List<Team> GetFromSortedSet()
    {
        List<Team> teams = null;
        IDatabase cache = Connection.GetDatabase();
        // If the key teamsSortedSet is not present, this method returns a 0 length collection.
        var teamsSortedSet = cache.SortedSetRangeByRankWithScores("teamsSortedSet", order: Order.Descending);
        if (teamsSortedSet.Count() > 0)
        {
            ViewBag.msg += "Reading sorted set from cache. ";
            teams = new List<Team>();
            foreach (var t in teamsSortedSet)
            {
                Team tt = JsonConvert.DeserializeObject<Team>(t.Element);
                teams.Add(tt);
            }
        }
        else
        {
            ViewBag.msg += "Teams sorted set cache miss. ";

            // Read from DB
            teams = GetFromDB();

            ViewBag.msg += "Storing results to cache. ";
            foreach (var t in teams)
            {
                Console.WriteLine("Adding to sorted set: {0} - {1}", t.Name, t.Wins);
                cache.SortedSetAdd("teamsSortedSet", JsonConvert.SerializeObject(t), t.Wins);
            }
        }
        return teams;
    }
    ```

    The `GetFromSortedSetTop5` method reads the top 5 teams from the cached sorted set. It starts by checking the cache for the existence of the `teamsSortedSet` key. If this key is not present, the `GetFromSortedSet` method is called to read the team statistics and store them in the cache. Next, the cached sorted set is queried for the top 5 teams which are returned.

    ```c#
    List<Team> GetFromSortedSetTop5()
    {
        List<Team> teams = null;
        IDatabase cache = Connection.GetDatabase();

        // If the key teamsSortedSet is not present, this method returns a 0 length collection.
        var teamsSortedSet = cache.SortedSetRangeByRankWithScores("teamsSortedSet", stop: 4, order: Order.Descending);
        if(teamsSortedSet.Count() == 0)
        {
            // Load the entire sorted set into the cache.
            GetFromSortedSet();

            // Retrieve the top 5 teams.
            teamsSortedSet = cache.SortedSetRangeByRankWithScores("teamsSortedSet", stop: 4, order: Order.Descending);
        }

        ViewBag.msg += "Retrieving top 5 teams from cache. ";
        // Get the top 5 teams from the sorted set
        teams = new List<Team>();
        foreach (var team in teamsSortedSet)
        {
            teams.Add(JsonConvert.DeserializeObject<Team>(team.Element));
        }
        return teams;
    }
    ```

### <a name="update-the-create-edit-and-delete-methods-to-work-with-the-cache"></a> Update the Create, Edit, and Delete methods to work with the cache
The scaffolding code that was generated as part of this sample includes methods to add, edit, and delete teams. Anytime a team is added, edited, or removed, the data in the cache becomes outdated. In this section you'll modify these three methods to clear the cached teams so that the cache won't be out of sync with the database.

1. Browse to the `Create(Team team)` method in the `TeamsController` class. Add a call to the `ClearCachedTeams` method, as shown in the following example.

    ```c#
    // POST: Teams/Create
    // To protect from overposting attacks, please enable the specific properties you want to bind to, for 
    // more details see http://go.microsoft.com/fwlink/?LinkId=317598.
    [HttpPost]
    [ValidateAntiForgeryToken]
    public ActionResult Create([Bind(Include = "ID,Name,Wins,Losses,Ties")] Team team)
    {
        if (ModelState.IsValid)
        {
            db.Teams.Add(team);
            db.SaveChanges();
            // When a team is added, the cache is out of date.
            // Clear the cached teams.
            ClearCachedTeams();
            return RedirectToAction("Index");
        }

        return View(team);
    }
    ```

1. Browse to the `Edit(Team team)` method in the `TeamsController` class. Add a call to the `ClearCachedTeams` method, as shown in the following example.

    ```c#
    // POST: Teams/Edit/5
    // To protect from overposting attacks, please enable the specific properties you want to bind to, for 
    // more details see http://go.microsoft.com/fwlink/?LinkId=317598.
    [HttpPost]
    [ValidateAntiForgeryToken]
    public ActionResult Edit([Bind(Include = "ID,Name,Wins,Losses,Ties")] Team team)
    {
        if (ModelState.IsValid)
        {
            db.Entry(team).State = EntityState.Modified;
            db.SaveChanges();
            // When a team is edited, the cache is out of date.
            // Clear the cached teams.
            ClearCachedTeams();
            return RedirectToAction("Index");
        }
        return View(team);
    }
    ```

1. Browse to the `DeleteConfirmed(int id)` method in the `TeamsController` class. Add a call to the `ClearCachedTeams` method, as shown in the following example.

    ```c#
    // POST: Teams/Delete/5
    [HttpPost, ActionName("Delete")]
    [ValidateAntiForgeryToken]
    public ActionResult DeleteConfirmed(int id)
    {
        Team team = db.Teams.Find(id);
        db.Teams.Remove(team);
        db.SaveChanges();
        // When a team is deleted, the cache is out of date.
        // Clear the cached teams.
        ClearCachedTeams();
        return RedirectToAction("Index");
    }
    ```

### <a name="update-the-teams-index-view-to-work-with-the-cache"></a> Update the Teams Index view to work with the cache
1. In **Solution Explorer**, expand the **Views** folder, then the **Teams** folder, and double-click **Index.cshtml**.

    ![Index.cshtml][cache-views-teams-index-cshtml]
2. Near the top of the file, look for the following paragraph element.

    ![Action table][cache-teams-index-table]

    This is the link to create a new team. Replace the paragraph element with the following table. This table has action links for creating a new team, playing a new season of games, clearing the cache, retrieving the teams from the cache in several formats, retrieving the teams from the database, and rebuilding the database with fresh sample data.

    ```html
    <table class="table">
        <tr>
            <td>
                @Html.ActionLink("Create New", "Create")
            </td>
            <td>
                @Html.ActionLink("Play Season", "Index", new { actionType = "playGames" })
            </td>
            <td>
                @Html.ActionLink("Clear Cache", "Index", new { actionType = "clearCache" })
            </td>
            <td>
                @Html.ActionLink("List from Cache", "Index", new { resultType = "teamsList" })
            </td>
            <td>
                @Html.ActionLink("Sorted Set from Cache", "Index", new { resultType = "teamsSortedSet" })
            </td>
            <td>
                @Html.ActionLink("Top 5 Teams from Cache", "Index", new { resultType = "teamsSortedSetTop5" })
            </td>
            <td>
                @Html.ActionLink("Load from DB", "Index", new { resultType = "fromDB" })
            </td>
            <td>
                @Html.ActionLink("Rebuild DB", "Index", new { actionType = "rebuildDB" })
            </td>
        </tr>    
    </table>
    ```

1. Scroll to the bottom of the **Index.cshtml** file and add the following `tr` element so that it is the last row in the last table in the file.

    ```html
    <tr><td colspan="5">@ViewBag.Msg</td></tr>
    ```

    This row displays the value of `ViewBag.Msg` which contains a status report about the current operation. The `ViewBag.Msg` is set when you click any of the action links from the previous step.   

    ![Status message][cache-status-message]
2. Press **F6** to build the project.

## <a name="provision-the-azure-resources"></a> Provision the Azure resources
To host your application in Azure, you must first provision the Azure services that your application requires. The sample application in this tutorial uses the following Azure services.

* Azure Redis Cache
* App Service Web App
* SQL Database

To deploy these services to a new or existing resource group of your choice, click the following **Deploy to Azure** button.

[![Deploy to Azure][deploybutton]](https://portal.azure.cn/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-web-app-redis-cache-sql-database%2Fazuredeploy.json)

>[!NOTE]
> Templates you are deploying from the GitHub Repo "azure-quickstart-templates" must be modified in order to fit in the Azure China Cloud Environment. For example, replace some endpoints -- "redis.cache.windows.net" by "redis.cache.chinacloudapi.cn", "cloudapp.azure.com" by "chinacloudapp.cn".

This **Deploy to Azure** button uses the [Create a Web App plus Redis Cache plus SQL Database](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-redis-cache-sql-database) [Azure Quickstart](https://github.com/Azure/azure-quickstart-templates) template to provision these services and set the connection string for the SQL Database and the application setting for the Azure Redis Cache connection string.

> [!NOTE]
> If you don't have an Azure account, you can [create a trial Azure account](https://www.azure.cn/pricing/1rmb-trial/?WT.mc_id=redis_cache_hero) in just a couple of minutes.
> 
> 

Clicking the **Deploy to Azure** button takes you to the Azure portal preview and initiates the process of creating the resources described by the template.

![Deploy to Azure][cache-deploy-to-azure-step-1]

1. In the **Basics** section, select the Azure subscription to use, and select an existing resource group or create a new one, and specify the resource group location.
2. In the **Settings** section, specify an **Administrator Login** (don't use **admin**), **Administrator Login Password**, and **Database Name**. The other parameters are configured for a free App Service hosting plan, and lower-cost options for the SQL Database and Azure Redis Cache, which don't come with a free tier.

    ![Deploy to Azure][cache-deploy-to-azure-step-2]

3. After configuring the desired settings, scroll to the end of the page, read the terms and conditions, and check the **I agree to the terms and conditions stated above** checkbox.
4. To begin provisioning the resources, click **Purchase**.

To view the progress of your deployment, click the notification icon and click **Deployment started**.

![Deployment started][cache-deployment-started]

You can view the status of your deployment on the **Microsoft.Template** blade.

![Deploy to Azure][cache-deploy-to-azure-step-3]

When provisioning is complete, you can publish your application to Azure from Visual Studio.

> [!NOTE]
> Any errors that may occur during the provisioning process are displayed on the **Microsoft.Template** blade. Common errors are too many SQL Servers or too many Free App Service hosting plans per subscription. Resolve any errors and restart the process by clicking **Redeploy** on the **Microsoft.Template** blade or the **Deploy to Azure** button in this tutorial.
> 
> 

## <a name="publish-the-application-to-azure"></a> Publish the application to Azure
[!INCLUDE [azure-sdk-developer-differences](../../includes/azure-sdk-developer-differences.md)]

In this step of the tutorial, you'll publish the application to Azure and run it in the cloud.

1. Right-click the **ContosoTeamStats** project in Visual Studio and choose **Publish**.

    ![Publish][cache-publish-app]
2. Click **Azure App Service**, choose **Select Existing**, and click **Publish**.

    ![Publish][cache-publish-to-app-service]
3. Select the subscription used when creating the Azure resources, expand the resource group containing the resources, and select the desired Web App. If you used the **Deploy to Azure** button your Web App name starts with **webSite** followed by some additional characters.

    ![Select Web App][cache-select-web-app]
4. Click **OK** to begin the publishing process. After a few moments the publishing process completes and a browser is launched with the running sample application. If you get a DNS error when validating or publishing, and the provisioning process for the Azure resources for the application has just recently completed, wait a moment and try again.

    ![Cache added][cache-added-to-application]

The following table describes each action link from the sample application.

| Action | Description |
| --- | --- |
| Create New |Create a new Team. |
| Play Season |Play a season of games, update the team stats, and clear any outdated team data from the cache. |
| Clear Cache |Clear the team stats from the cache. |
| List from Cache |Retrieve the team stats from the cache. If there is a cache miss, load the stats from the database and save to the cache for next time. |
| Sorted Set from Cache |Retrieve the team stats from the cache using a sorted set. If there is a cache miss, load the stats from the database and save to the cache using a sorted set. |
| Top 5 Teams from Cache |Retrieve the top 5 teams from the cache using a sorted set. If there is a cache miss, load the stats from the database and save to the cache using a sorted set. |
| Load from DB |Retrieve the team stats from the database. |
| Rebuild DB |Rebuild the database and reload it with sample team data. |
| Edit / Details / Delete |Edit a team, view details for a team, delete a team. |

Click some of the actions and experiment with retrieving the data from the different sources. Not the differences in the time it takes to complete the various ways of retrieving the data from the database and the cache.

## Delete the resources when you are finished with the application
When you are finished with the sample tutorial application, you can delete the Azure resources used in order to conserve cost and resources. If you use the **Deploy to Azure** button in the [Provision the Azure resources](#provision-the-azure-resources) section and all of your resources are contained in the same resource group, you can delete them together in one operation by deleting the resource group.

1. Sign in to the [Azure portal preview](https://portal.azure.cn) and click **Resource groups**.
2. Type the name of your resource group into the **Filter items...** textbox.
3. Click **...** to the right of your resource group.
4. Click **Delete**.

    ![Delete][cache-delete-resource-group]
5. Type the name of your resource group and click **Delete**.

    ![Confirm delete][cache-delete-confirm]

After a few moments the resource group and all of its contained resources are deleted.

> [!IMPORTANT]
> Note that deleting a resource group is irreversible and that the resource group and all the resources in it are permanently deleted. Make sure that you do not accidentally delete the wrong resource group or resources. If you created the resources for hosting this sample inside an existing resource group, you can delete each resource individually from their respective blades.
> 
> 

## Run the sample application on your local machine
To run the application locally on your machine, you need an Azure Redis Cache instance in which to cache your data. 

* If you have published your application to Azure as described in the previous section, you can use the Azure Redis Cache instance that was provisioned during that step.
* If you have another existing Azure Redis Cache instance, you can use that to run this sample locally.
* If you need to create an Azure Redis Cache instance, you can follow the steps in [Create a cache](cache-dotnet-how-to-use-azure-redis-cache.md#create-a-cache).

Once you have selected or created the cache to use, browse to the cache in the Azure portal preview and retrieve the [host name](cache-configure.md#properties) and [access keys](cache-configure.md#access-keys) for your cache. For instructions, see [Configure Redis cache settings](cache-configure.md#configure-redis-cache-settings).

1. Open the `WebAppPlusCacheAppSecrets.config` file that you created during the [Configure the application to use Redis Cache](#configure-the-application-to-use-redis-cache) step of this tutorial using the editor of your choice.
2. Edit the `value` attribute and replace `MyCache.redis.cache.chinacloudapi.cn` with the [host name](cache-configure.md#properties) of your cache, and specify either the [primary or secondary key](cache-configure.md#access-keys) of your cache as the password.

    ```xml
    <appSettings>
      <add key="CacheConnection" value="MyCache.redis.cache.chinacloudapi.cn,abortConnect=false,ssl=true,password=..."/>
    </appSettings>
    ```

1. Press **Ctrl+F5** to run the application.

> [!NOTE]
> Note that because the application, including the database, is running locally and the Redis Cache is hosted in Azure, the cache may appear to under-perform the database. For best performance, the client application and Azure Redis Cache instance should be in the same location. 
> 
> 

## Next steps
* Learn more about [Getting Started with ASP.NET MVC 5](http://www.asp.net/mvc/overview/getting-started/introduction/getting-started) on the [ASP.NET](http://asp.net/) site.
* For more examples of creating an ASP.NET Web App in App Service, see [Create and deploy an ASP.NET web app in Azure App Service](https://github.com/Microsoft/HealthClinic.biz/wiki/Create-and-deploy-an-ASP.NET-web-app-in-Azure-App-Service) from the [HealthClinic.biz](https://github.com/Microsoft/HealthClinic.biz) 2015 Connect [demo](https://blogs.msdn.microsoft.com/visualstudio/2015/12/08/connectdemos-2015-healthclinic-biz/).
    * For more quickstarts from the HealthClinic.biz demo, see [Azure Developer Tools Quickstarts](https://github.com/Microsoft/HealthClinic.biz/wiki/Azure-Developer-Tools-Quickstarts).
* Learn more about the [Code first to a new database](https://msdn.microsoft.com/data/jj193542) approach to Entity Framework that's used in this tutorial.
* Learn more about [web apps in Azure App Service](../app-service-web/app-service-web-overview.md).
* Learn how to [monitor](cache-how-to-monitor.md) your cache in the Azure portal preview.
* Explore Azure Redis Cache premium features

    * [How to configure persistence for a Premium Azure Redis Cache](cache-how-to-premium-persistence.md)
    * [How to configure clustering for a Premium Azure Redis Cache](cache-how-to-premium-clustering.md)
    * [How to configure Virtual Network support for a Premium Azure Redis Cache](cache-how-to-premium-vnet.md)
    * See the [Azure Redis Cache FAQ](cache-faq.md#what-redis-cache-offering-and-size-should-i-use) for more details about size, throughput, and bandwidth with premium caches.

<!-- IMAGES -->
[cache-starter-application]: ./media/cache-web-app-howto/cache-starter-application.png
[cache-added-to-application]: ./media/cache-web-app-howto/cache-added-to-application.png
[cache-create-project]: ./media/cache-web-app-howto/cache-create-project.png
[cache-select-template]: ./media/cache-web-app-howto/cache-select-template.png
[cache-model-add-class]: ./media/cache-web-app-howto/cache-model-add-class.png
[cache-model-add-class-dialog]: ./media/cache-web-app-howto/cache-model-add-class-dialog.png
[cache-add-controller]: ./media/cache-web-app-howto/cache-add-controller.png
[cache-add-controller-class]: ./media/cache-web-app-howto/cache-add-controller-class.png
[cache-configure-controller]: ./media/cache-web-app-howto/cache-configure-controller.png
[cache-global-asax]: ./media/cache-web-app-howto/cache-global-asax.png
[cache-RouteConfig-cs]: ./media/cache-web-app-howto/cache-RouteConfig-cs.png
[cache-layout-cshtml]: ./media/cache-web-app-howto/cache-layout-cshtml.png
[cache-layout-cshtml-code]: ./media/cache-web-app-howto/cache-layout-cshtml-code.png
[redis-cache-manage-nuget-menu]: ./media/cache-web-app-howto/redis-cache-manage-nuget-menu.png
[redis-cache-stack-exchange-nuget]: ./media/cache-web-app-howto/redis-cache-stack-exchange-nuget.png
[cache-teamscontroller]: ./media/cache-web-app-howto/cache-teamscontroller.png
[cache-web-config]: ./media/cache-web-app-howto/cache-web-config.png
[cache-views-teams-index-cshtml]: ./media/cache-web-app-howto/cache-views-teams-index-cshtml.png
[cache-teams-index-table]: ./media/cache-web-app-howto/cache-teams-index-table.png
[cache-status-message]: ./media/cache-web-app-howto/cache-status-message.png
[deploybutton]: ./media/cache-web-app-howto/deploybutton.png
[cache-deploy-to-azure-step-1]: ./media/cache-web-app-howto/cache-deploy-to-azure-step-1.png
[cache-deploy-to-azure-step-2]: ./media/cache-web-app-howto/cache-deploy-to-azure-step-2.png
[cache-deploy-to-azure-step-3]: ./media/cache-web-app-howto/cache-deploy-to-azure-step-3.png
[cache-deployment-started]: ./media/cache-web-app-howto/cache-deployment-started.png
[cache-publish-app]: ./media/cache-web-app-howto/cache-publish-app.png
[cache-publish-to-app-service]: ./media/cache-web-app-howto/cache-publish-to-app-service.png
[cache-select-web-app]: ./media/cache-web-app-howto/cache-select-web-app.png
[cache-publish]: ./media/cache-web-app-howto/cache-publish.png
[cache-delete-resource-group]: ./media/cache-web-app-howto/cache-delete-resource-group.png
[cache-delete-confirm]: ./media/cache-web-app-howto/cache-delete-confirm.png
