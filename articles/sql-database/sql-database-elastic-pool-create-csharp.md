<properties
    pageTitle="Rugalmas adatbáziskészlet létrehozása a C# használatával | Microsoft Azure"
    description="Skálázható rugalmas adatbáziskészlet létrehozása az Azure SQL Database-ben C#-alapú adatbázis-fejlesztői módszerek használatával az erőforrások több adatbázis közötti megosztásához."
    services="sql-database"
    documentationCenter=""
    authors="stevestein"
    manager="jhubbard"
    editor=""/>

<tags
    ms.service="sql-database"
    ms.devlang="NA"
    ms.topic="get-started-article"
    ms.tgt_pltfrm="csharp"
    ms.workload="data-management"
    ms.date="08/18/2016"
    ms.author="sstein"/>

# Új rugalmas adatbáziskészlet létrehozása a C&#x23; használatával.

> [AZURE.SELECTOR]
- [Azure Portal](sql-database-elastic-pool-create-portal.md)
- [PowerShell](sql-database-elastic-pool-create-powershell.md)
- [C##](sql-database-elastic-pool-create-csharp.md)


Ebből a cikkből megtudhatja, hogyan hozhat létre [rugalmas adatbáziskészletet](sql-database-elastic-pool.md) a C# használatával.

A gyakori hibakódokat megtalálja az [SQL error codes for SQL Database client applications: Database connection error and other issues](sql-database-develop-error-messages.md) (SQL Database-ügyfélalkalmazások SQL-hibakódjai: adatbázis-kapcsolati és más hibák) című cikkben.

A példák az [SQL Database .NET-es kódtárát](https://msdn.microsoft.com/library/azure/mt349017.aspx) veszik alapul, úgyhogy amennyiben még nincs telepítve, a továbblépés előtt telepítse ezt a kódtárat. A kódtár telepítését elvégezheti a Visual Studio [csomagkezelő konzolján](http://docs.nuget.org/Consume/Package-Manager-Console) (**Eszközök** > **NuGet Csomagkezelő** > **Csomagkezelő konzol**) az alábbi paranccsal:

    Install-Package Microsoft.Azure.Management.Sql –Pre

## Készlet létrehozása

Hozzon létre egy [SqlManagementClient](https://msdn.microsoft.com/library/microsoft.azure.management.sql.sqlmanagementclient) példányt az [Azure Active Directory](sql-database-client-id-keys.md) értékeinek használatával. Hozzon létre egy [ElasticPoolCreateOrUpdateParameters](https://msdn.microsoft.com/library/microsoft.azure.management.sql.models.elasticpoolcreateorupdateparameters) példányt, majd hívja meg a [CreateOrUpdate](https://msdn.microsoft.com/library/microsoft.azure.management.sql.databaseoperationsextensions.createorupdate) metódust. A készletenkénti eDTU-értékek, valamint a DTU-k minimális és maximális értékei a szolgáltatásszinttől (alapszintű, standard vagy prémium) függenek. Lásd: [Rugalmas készletek és rugalmas adatbázisok eDTU- és tárterületi korlátozásai](sql-database-elastic-pool.md#eDTU-and-storage-limits-for-elastic-pools-and-elastic-databases).


    ElasticPoolCreateOrUpdateParameters newPoolParameters = new ElasticPoolCreateOrUpdateParameters()
    {
        Location = "South Central US",
        Properties = new ElasticPoolCreateOrUpdateProperties()
        {
            Edition = "Standard",
            Dtu = 400,
            DatabaseDtuMin = 0,
            DatabaseDtuMax = 100
         }
    };

    // Create the pool
    var newPoolResponse = sqlClient.ElasticPools.CreateOrUpdate("resourcegroup-name", "server-name", "ElasticPool1", newPoolParameters);

## Adatbázis létrehozása egy készletben

Hozzon létre egy [DataBaseCreateorUpdateProperties](https://msdn.microsoft.com/library/microsoft.azure.management.sql.models.databasecreateorupdateproperties) példányt, és állítsa be az új adatbázis tulajdonságait. Ezután hívja meg a CreateOrUpdate metódust az erőforráscsoport, a kiszolgáló és az új adatbázis nevével.

    // Create a database: configure create or update parameters and properties explicitly
    DatabaseCreateOrUpdateParameters newPooledDatabaseParameters = new DatabaseCreateOrUpdateParameters()
    {
        Location = currentServer.Location,
        Properties = new DatabaseCreateOrUpdateProperties()
        {
            Edition = "Standard",
            RequestedServiceObjectiveName = "ElasticPool",
            ElasticPoolName = "ElasticPool1",
            MaxSizeBytes = 268435456000, // 250 GB,
            Collation = "SQL_Latin1_General_CP1_CI_AS"
        }
    };

    var poolDbResponse = sqlClient.Databases.CreateOrUpdate("resourcegroup-name", "server-name", "Database2", newPooledDatabaseParameters);

További információ a már létező adatbázisok készletbe való áthelyezéséről: [Adatbázis áthelyezése rugalmas készletbe](sql-database-elastic-pool-manage-csharp.md#Move-a-database-into-an-elastic-pool).

## Példa: Készlet létrehozása a C&#x23 használatával

A példa egy Azure-erőforráscsoportot, egy Azure SQL-kiszolgálót és egy rugalmas készletet hoz létre. 
 

A példa futtatásához az alábbi kódtárak szükségesek. A telepítést elvégezheti a Visual Studio [csomagkezelő konzolján](http://docs.nuget.org/Consume/Package-Manager-Console) (**Eszközök** > **NuGet Csomagkezelő** > **Csomagkezelő konzol**) az alábbi paranccsal.

    Install-Package Microsoft.Azure.Management.Sql –Pre
    Install-Package Microsoft.Azure.Management.ResourceManager –Pre
    Install-Package Microsoft.Azure.Common.Authentication –Pre

Hozzon létre egy konzolalkalmazást, és cserélje le a Program.cs tartalmát az alábbira. A szükséges ügyfél-azonosító és a kapcsolódó értékek beszerzéséhez hozzon létre egy natív alkalmazást a következő cikk segítségével: [Register your app and get the required client values for connecting your app to SQL Database](sql-database-client-id-keys.md) (Az alkalmazás regisztrálása és a szükséges ügyfélértékek beszerzése az alkalmazás az SQL Database-hez való csatlakoztatásához).

    using Microsoft.Azure;
    using Microsoft.Azure.Management.ResourceManager;
    using Microsoft.Azure.Management.ResourceManager.Models;
    using Microsoft.Azure.Management.Sql;
    using Microsoft.Azure.Management.Sql.Models;
    using Microsoft.IdentityModel.Clients.ActiveDirectory;
    using System;
    
    namespace SqlDbElasticPoolsSample
    {
    class Program
    {
        // authentication variables
        static string subscriptionId = "<your Azure subscription>";
        static string clientId = "<your client id>";
        static string redirectUri = "<your redirect URI>";
        static string domainName = "<i.e. microsoft.onmicrosoft.com>";
        static AuthenticationResult token;

        // resource group variables
        static string datacenterLocation = "<location>";
        static string resourceGroupName = "<resource group name>";

        // server variables
        static string serverName = "<server name>";
        static string adminLogin = "<server admin>";
        static string adminPassword = "<server password (store it securely!)>";
        static string serverVersion = "12.0";

        // pool variables
        static string elasticPoolName = "<pool name>";
        static string poolEdition = "Standard";
        static int poolDtus = 400;
        static int databaseMinDtus = 0;
        static int databaseMaxDtus = 100;


        static void Main(string[] args)
        {
            // Sign in to Azure
            token = GetAccessToken();
            Console.WriteLine("Logged in as: " + token.UserInfo.DisplayableId);

            // Create a resource group
            ResourceGroup rg = CreateResourceGroup();
            Console.WriteLine(rg.Name + " created.");

            // Create a server
            Console.WriteLine("Creating server... ");
            ServerGetResponse srvr = CreateServer();
            Console.WriteLine("Creation of server " + srvr.Server.Name + ": " + srvr.StatusCode.ToString());

            // Create a pool
            Console.WriteLine("Creating elastic database pool... ");
            ElasticPoolCreateOrUpdateResponse epool = CreateElasticDatabasePool();
            Console.WriteLine("Creation of pool " + epool.ElasticPool.Name + ": " + epool.Status.ToString());


            // keep the console open until Enter is pressed!
            Console.WriteLine("Press Enter to exit.");
            Console.ReadLine();
        }


        static ElasticPoolCreateOrUpdateResponse CreateElasticDatabasePool()
        {
            // Create a SQL Database management client
            SqlManagementClient sqlClient = new SqlManagementClient(new TokenCloudCredentials(subscriptionId, token.AccessToken));

            // Create elastic pool: configure create or update parameters and properties explicitly
            ElasticPoolCreateOrUpdateParameters newPoolParameters = new ElasticPoolCreateOrUpdateParameters()
            {
                Location = datacenterLocation,
                Properties = new ElasticPoolCreateOrUpdateProperties()
                {
                    Edition = poolEdition,
                    Dtu = poolDtus, 
                    DatabaseDtuMin = databaseMinDtus,
                    DatabaseDtuMax = databaseMaxDtus
                }
            };

            // Create the pool
            var newPoolResponse = sqlClient.ElasticPools.CreateOrUpdate(resourceGroupName, serverName, elasticPoolName, newPoolParameters);
            return newPoolResponse;
        }



        static ServerGetResponse CreateServer()
        {

            // Create a SQL Database management client
            SqlManagementClient sqlClient = new SqlManagementClient(new TokenCloudCredentials(subscriptionId, token.AccessToken));

            // Create a server
            ServerCreateOrUpdateParameters serverParameters = new ServerCreateOrUpdateParameters()
            {
                Location = datacenterLocation,
                Properties = new ServerCreateOrUpdateProperties()
                {
                    AdministratorLogin = adminLogin,
                    AdministratorLoginPassword = adminPassword,
                    Version = serverVersion
                }
            };

            var serverResult = sqlClient.Servers.CreateOrUpdate(resourceGroupName, serverName, serverParameters);
            return serverResult;

        }


        static ResourceGroup CreateResourceGroup()
        {
           
            Microsoft.Rest.TokenCredentials creds = new Microsoft.Rest.TokenCredentials(token.AccessToken);
            // Create a resource management client 
            ResourceManagementClient resourceClient = new ResourceManagementClient(creds);

            // Resource group parameters
            ResourceGroup resourceGroupParameters = new ResourceGroup()
            {
                Location = datacenterLocation,
            };

            //Create a resource group
            resourceClient.SubscriptionId = subscriptionId;
            var resourceGroupResult = resourceClient.ResourceGroups.CreateOrUpdate(resourceGroupName, resourceGroupParameters);
            return resourceGroupResult;
        }


        private static AuthenticationResult GetAccessToken()
        {
            AuthenticationContext authContext = new AuthenticationContext
                ("https://login.windows.net/" + domainName /* Tenant ID or AAD domain */);

            AuthenticationResult token = authContext.AcquireToken
                ("https://management.azure.com/"/* the Azure Resource Management endpoint */,
                    clientId,
            new Uri(redirectUri) /* redirect URI */,
            PromptBehavior.Auto /* with Auto user will not be prompted if an unexpired token is cached */);

            return token;
        }
    }
    }

  

## Következő lépések

- [Készlet kezelése](sql-database-elastic-pool-manage-csharp.md)
- [Rugalmas feladat létrehozása](sql-database-elastic-jobs-overview.md): a rugalmas feladatok lehetővé teszik a T-SQL-szkriptek használatát egy készletben lévő tetszőleges számú adatbázishoz.
- [Horizontális felskálázás az Azure SQL Database-ben](sql-database-elastic-scale-introduction.md): horizontális skálázáshoz rugalmas adatbáziseszközöket használhat.

## További források

- [SQL Database](https://azure.microsoft.com/documentation/services/sql-database/)
- [Az Azure erőforrás-kezelési API-jai](https://msdn.microsoft.com/library/azure/dn948464.aspx)



<!--HONumber=sep16_HO1-->


