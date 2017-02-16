---
title: "C#: Ismerkedés az Azure SQL Database szolgáltatással | Microsoft Docs"
description: "Próbálja ki az SQL Database-t az SQL- és C#-alkalmazások fejlesztésére, és hozzon létre egy Azure SQL Database adatbázist a C# segítségével és az SQL Database .NET-es kódtárával."
keywords: "sql kipróbálása, sql c#"
services: sql-database
documentationcenter: 
author: stevestein
manager: jhubbard
editor: cgronlun
ms.assetid: cfff2299-a474-4054-8d99-759af1ae5188
ms.service: sql-database
ms.custom: single databases
ms.devlang: NA
ms.topic: hero-article
ms.tgt_pltfrm: csharp
ms.workload: data-management
ms.date: 10/04/2016
ms.author: sstein
translationtype: Human Translation
ms.sourcegitcommit: 10b40214ad4c7d7bb7999a5abce1c22100b617d8
ms.openlocfilehash: d55c08dbd1d2372e5396f40e74b4f63f3e2f0022


---
# <a name="use-c-to-create-a-sql-database-with-the-sql-database-library-for-net"></a>SQL Database létrehozása a C# használatával és az SQL Database .NET-es kódtárával

Cikkünkből megtanulhatja, hogyan hozhat létre Azure SQL-adatbázisokat C# nyelven a [Microsoft Azure SQL Management .NET-es kódtárával](https://www.nuget.org/packages/Microsoft.Azure.Management.Sql). A cikk azt írja le, hogyan hozható létre egy önálló adatbázis az SQL és C# segítségével. A rugalmas készletek létrehozásával kapcsolatban lásd: [Rugalmas készlet létrehozása](sql-database-elastic-pool-create-csharp.md).

Az Azure SQL Database Management .NET-es kódtára az [Azure Resource Manageren](../azure-resource-manager/resource-group-overview.md) alapuló API-t tartalmaz, amely a [Resource Manager-alapú SQL Database REST API-t](https://msdn.microsoft.com/library/azure/mt163571.aspx) burkolja.

> [!NOTE]
> Az SQL Database sok új funkciója csak az [Azure Resource Manager-alapú üzemi modellben](../azure-resource-manager/resource-group-overview.md) támogatott, ezért mindig használja az **Azure SQL Database Management legújabb .NET-es kódtárát ([dokumentáció](https://msdn.microsoft.com/library/azure/mt349017.aspx) | [NuGet-csomag](https://www.nuget.org/packages/Microsoft.Azure.Management.Sql))**. A korábbi, [klasszikus üzemi modellen alapuló kódtárak](https://www.nuget.org/packages/Microsoft.WindowsAzure.Management.Sql) csak visszamenőleges kompatibilitásra támogatottak, ezért inkább használjon újabb, Resource Manager-alapú kódtárakat.
> 
> 

A cikkben leírt lépések elvégzéséhez a következőkre lesz szüksége:

* Azure-előfizetés. Ha szüksége van Azure-előfizetésre, kattintson a lap tetején látható **INGYENES FIÓK** elemre, majd térjen vissza ehhez a cikkhez.
* Visual Studio. A Visual Studio ingyenes példánya beszerezhető a [Visual Studio Downloads](https://www.visualstudio.com/downloads/download-visual-studio-vs) (Visual Studio-letöltések) lapon.

> [!NOTE]
> Ez a cikk bemutatja egy új, üres SQL-adatbázis létrehozásának lépéseit. Adatbázis másolásához, méretezéséhez, készletbeli létrehozásához stb. módosítsa a *CreateOrUpdateDatabase(...)* metódust az alábbi példában. További információ: [DatabaseCreateMode](https://msdn.microsoft.com/library/microsoft.azure.management.sql.models.databasecreatemode.aspx) és [DatabaseProperties](https://msdn.microsoft.com/library/microsoft.azure.management.sql.models.databaseproperties.aspx) osztály.
> 
> 

## <a name="create-a-console-app-and-install-the-required-libraries"></a>Egy konzolalkalmazás létrehozása és a szükséges kódtárak telepítése
1. Indítsa el a Visual Studiót.
2. Kattintson a **File** (Fájl)  > **New** (Új)  > **Project** (Projekt) lehetőségre.
3. Hozzon létre egy C#-**konzolalkalmazást**, és adja neki a következő nevet: *SqlDbConsoleApp*

Egy SQL-adatbázis C# segítségével történő létrehozásához töltse be a szükséges kezelési kódtárakat (a [csomagkezelő konzol](http://docs.nuget.org/Consume/Package-Manager-Console) használatával):

1. Kattintson a **Tools** (Eszközök)  > **NuGet Package Manager** (NuGet-csomagkezelő) > **Package Manager Console** (Csomagkezelő konzol) elemre.
2. A [Microsoft Azure SQL Management Library](https://www.nuget.org/packages/Microsoft.Azure.Management.Sql) legújabb verziójának telepítéséhez gépelje be a következőt: `Install-Package Microsoft.Azure.Management.Sql -Pre`.
3. A [Microsoft Azure Resource Manager Library](https://www.nuget.org/packages/Microsoft.Azure.Management.ResourceManager) telepítéséhez gépelje be: `Install-Package Microsoft.Azure.Management.ResourceManager -Pre`.
4. A [Microsoft Azure Common Authentication Library](https://www.nuget.org/packages/Microsoft.Azure.Common.Authentication) telepítéséhez gépelje be: `Install-Package Microsoft.Azure.Common.Authentication -Pre`. 

> [!NOTE]
> A cikkben szereplő példák az egyes API-kérések és -blokkok szinkron formáját használja, amíg a mögöttes szolgáltatás REST-hívása el nem készül. Aszinkron metódusok is elérhetők.
> 
> 

## <a name="create-a-sql-database-server-firewall-rule-and-sql-database---c-example"></a>Egy SQL Database-kiszolgáló, egy tűzfalszabály és egy SQL-adatbázis létrehozása – C#-minta
Az alábbi minta létrehoz egy erőforráscsoportot, egy kiszolgálót, egy tűzfalszabályt és egy SQL-adatbázist. A `_subscriptionId, _tenantId, _applicationId, and _applicationSecret` változókkal kapcsolatban lásd: [Egyszerű szolgáltatás létrehozása erőforrások eléréséhez](#create-a-service-principal-to-access-resources).

Cserélje le a **Program.cs** tartalmát az alábbiakkal, és frissítse a `{variables}` részeket az alkalmazáshoz tartozó értékekkel (a `{}` nem szükséges).

    using Microsoft.Azure;
    using Microsoft.Azure.Management.ResourceManager;
    using Microsoft.Azure.Management.ResourceManager.Models;
    using Microsoft.Azure.Management.Sql;
    using Microsoft.Azure.Management.Sql.Models;
    using Microsoft.IdentityModel.Clients.ActiveDirectory;
    using System;

    namespace SqlDbConsoleApp
    {
    class Program
       {
        // For details about these four (4) values, see
        // https://azure.microsoft.com/documentation/articles/resource-group-authenticate-service-principal/
        static string _subscriptionId = "{xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx}";
        static string _tenantId = "{xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx}";
        static string _applicationId = "{xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx}";
        static string _applicationSecret = "{your-password}";

        // Create management clients for the Azure resources your app needs to work with.
        // This app works with Resource Groups, and Azure SQL Database.
        static ResourceManagementClient _resourceMgmtClient;
        static SqlManagementClient _sqlMgmtClient;

        // Authentication token
        static AuthenticationResult _token;

        // Azure resource variables
        static string _resourceGroupName = "{resource-group-name}";
        static string _resourceGrouplocation = "{Azure-region}";

        static string _serverlocation = _resourceGrouplocation;
        static string _serverName = "{server-name}";
        static string _serverAdmin = "{server-admin-login}";
        static string _serverAdminPassword = "{server-admin-password}";

        static string _firewallRuleName = "{firewall-rule-name}";
        static string _startIpAddress = "{0.0.0.0}";
        static string _endIpAddress = "{255.255.255.255}";

        static string _databaseName = "{dbfromcsarticle}";
        static string _databaseEdition = DatabaseEditions.Basic;
        static string _databasePerfLevel = ""; // "S0", "S1", and so on here for other tiers


        static void Main(string[] args)
        {
            // Authenticate:
            _token = GetToken(_tenantId, _applicationId, _applicationSecret);
            Console.WriteLine("Token acquired. Expires on:" + _token.ExpiresOn);

            // Instantiate management clients:
            _resourceMgmtClient = new ResourceManagementClient(new Microsoft.Rest.TokenCredentials(_token.AccessToken));
            _sqlMgmtClient = new SqlManagementClient(new TokenCloudCredentials(_subscriptionId, _token.AccessToken));


            Console.WriteLine("Resource group...");
            ResourceGroup rg = CreateOrUpdateResourceGroup(_resourceMgmtClient, _subscriptionId, _resourceGroupName, _resourceGrouplocation);
            Console.WriteLine("Resource group: " + rg.Id);


            Console.WriteLine("Server...");
            ServerGetResponse sgr = CreateOrUpdateServer(_sqlMgmtClient, _resourceGroupName, _serverlocation, _serverName, _serverAdmin, _serverAdminPassword);
            Console.WriteLine("Server: " + sgr.Server.Id);

            Console.WriteLine("Server firewall...");
            FirewallRuleGetResponse fwr = CreateOrUpdateFirewallRule(_sqlMgmtClient, _resourceGroupName, _serverName, _firewallRuleName, _startIpAddress, _endIpAddress);
            Console.WriteLine("Server firewall: " + fwr.FirewallRule.Id);

            Console.WriteLine("Database...");
            DatabaseCreateOrUpdateResponse dbr = CreateOrUpdateDatabase(_sqlMgmtClient, _resourceGroupName, _serverName, _databaseName, _databaseEdition, _databasePerfLevel);
            Console.WriteLine("Database: " + dbr.Database.Id);


            Console.WriteLine("Press any key to continue...");
            Console.ReadKey();
        }

        static ResourceGroup CreateOrUpdateResourceGroup(ResourceManagementClient resourceMgmtClient, string subscriptionId, string resourceGroupName, string resourceGroupLocation)
        {
            ResourceGroup resourceGroupParameters = new ResourceGroup()
            {
                Location = resourceGroupLocation,
            };
            resourceMgmtClient.SubscriptionId = subscriptionId;
            ResourceGroup resourceGroupResult = resourceMgmtClient.ResourceGroups.CreateOrUpdate(resourceGroupName, resourceGroupParameters);
            return resourceGroupResult;
        }

        static ServerGetResponse CreateOrUpdateServer(SqlManagementClient sqlMgmtClient, string resourceGroupName, string serverLocation, string serverName, string serverAdmin, string serverAdminPassword)
        {
            ServerCreateOrUpdateParameters serverParameters = new ServerCreateOrUpdateParameters()
            {
                Location = serverLocation,
                Properties = new ServerCreateOrUpdateProperties()
                {
                    AdministratorLogin = serverAdmin,
                    AdministratorLoginPassword = serverAdminPassword,
                    Version = "12.0"
                }
            };
            ServerGetResponse serverResult = sqlMgmtClient.Servers.CreateOrUpdate(resourceGroupName, serverName, serverParameters);
            return serverResult;
        }


        static FirewallRuleGetResponse CreateOrUpdateFirewallRule(SqlManagementClient sqlMgmtClient, string resourceGroupName, string serverName, string firewallRuleName, string startIpAddress, string endIpAddress)
        {
            FirewallRuleCreateOrUpdateParameters firewallParameters = new FirewallRuleCreateOrUpdateParameters()
            {
                Properties = new FirewallRuleCreateOrUpdateProperties()
                {
                    StartIpAddress = startIpAddress,
                    EndIpAddress = endIpAddress
                }
            };
            FirewallRuleGetResponse firewallResult = sqlMgmtClient.FirewallRules.CreateOrUpdate(resourceGroupName, serverName, firewallRuleName, firewallParameters);
            return firewallResult;
        }



        static DatabaseCreateOrUpdateResponse CreateOrUpdateDatabase(SqlManagementClient sqlMgmtClient, string resourceGroupName, string serverName, string databaseName, string databaseEdition, string databasePerfLevel)
        {
            // Retrieve the server that will host this database
            Server currentServer = sqlMgmtClient.Servers.Get(resourceGroupName, serverName).Server;

            // Create a database: configure create or update parameters and properties explicitly
            DatabaseCreateOrUpdateParameters newDatabaseParameters = new DatabaseCreateOrUpdateParameters()
            {
                Location = currentServer.Location,
                Properties = new DatabaseCreateOrUpdateProperties()
                {
                    CreateMode = DatabaseCreateMode.Default,
                    Edition = databaseEdition,
                    RequestedServiceObjectiveName = databasePerfLevel
                }
            };
            DatabaseCreateOrUpdateResponse dbResponse = sqlMgmtClient.Databases.CreateOrUpdate(resourceGroupName, serverName, databaseName, newDatabaseParameters);
            return dbResponse;
        }



        private static AuthenticationResult GetToken(string tenantId, string applicationId, string applicationSecret)
        {
            AuthenticationContext authContext = new AuthenticationContext("https://login.windows.net/" + tenantId);
            _token = authContext.AcquireToken("https://management.core.windows.net/", new ClientCredential(applicationId, applicationSecret));
            return _token;
        }
      }
    }





## <a name="create-a-service-principal-to-access-resources"></a>Egyszerű szolgáltatás létrehozása erőforrások eléréséhez
A következő PowerShell-parancsprogram létrehozza az Active Directory (AD)-alkalmazást, és a C#-alkalmazás hitelesítéséhez szükséges egyszerű szolgáltatást. A parancsfájl kimenetének értékeire szükség lesz az előbb említett C#-mintához. Részletes információk: [Egyszerű szolgáltatás létrehozása erőforrások eléréséhez az Azure PowerShell használatával](../azure-resource-manager/resource-group-authenticate-service-principal.md).

    # Sign in to Azure.
    Add-AzureRmAccount

    # If you have multiple subscriptions, uncomment and set to the subscription you want to work with.
    #$subscriptionId = "{xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx}"
    #Set-AzureRmContext -SubscriptionId $subscriptionId

    # Provide these values for your new AAD app.
    # $appName is the display name for your app, must be unique in your directory.
    # $uri does not need to be a real uri.
    # $secret is a password you create.

    $appName = "{app-name}"
    $uri = "http://{app-name}"
    $secret = "{app-password}"

    # Create a AAD app
    $azureAdApplication = New-AzureRmADApplication -DisplayName $appName -HomePage $Uri -IdentifierUris $Uri -Password $secret

    # Create a Service Principal for the app
    $svcprincipal = New-AzureRmADServicePrincipal -ApplicationId $azureAdApplication.ApplicationId

    # To avoid a PrincipalNotFound error, I pause here for 15 seconds.
    Start-Sleep -s 15

    # If you still get a PrincipalNotFound error, then rerun the following until successful. 
    $roleassignment = New-AzureRmRoleAssignment -RoleDefinitionName Contributor -ServicePrincipalName $azureAdApplication.ApplicationId.Guid


    # Output the values we need for our C# application to successfully authenticate

    Write-Output "Copy these values into the C# sample app"

    Write-Output "_subscriptionId:" (Get-AzureRmContext).Subscription.SubscriptionId
    Write-Output "_tenantId:" (Get-AzureRmContext).Tenant.TenantId
    Write-Output "_applicationId:" $azureAdApplication.ApplicationId.Guid
    Write-Output "_applicationSecret:" $secret



## <a name="next-steps"></a>Következő lépések
Most, hogy kipróbálta az SQL Database-t, és létrehozott egy adatbázist a C# segítségével, készen áll a következő cikkek elolvasására is:

* [Csatlakozás az SQL Database-hez az SQL Server Management Studio segítségével, és T-SQL-mintalekérdezés futtatása](sql-database-connect-query-ssms.md)

## <a name="additional-resources"></a>További források
* [SQL Database](https://azure.microsoft.com/documentation/services/sql-database/)
* [Adatbázisosztály](https://msdn.microsoft.com/library/azure/microsoft.azure.management.sql.models.database.aspx)

<!--Image references-->
[1]: ./media/sql-database-get-started-csharp/aad.png
[2]: ./media/sql-database-get-started-csharp/permissions.png
[3]: ./media/sql-database-get-started-csharp/getdomain.png
[4]: ./media/sql-database-get-started-csharp/aad2.png
[5]: ./media/sql-database-get-started-csharp/aad-applications.png
[6]: ./media/sql-database-get-started-csharp/add.png
[7]: ./media/sql-database-get-started-csharp/add-application.png
[8]: ./media/sql-database-get-started-csharp/add-application2.png
[9]: ./media/sql-database-get-started-csharp/clientid.png



<!--HONumber=Jan17_HO2-->


