<properties
    pageTitle="Az SQL Database kipróbálása: SQL-adatbázis létrehozása a C# segítségével | Microsoft Azure"
    description="Próbálja ki az SQL Database-t az SQL- és C#-alkalmazások fejlesztésére, és hozzon létre egy Azure SQL Database adatbázist a C# segítségével és az SQL Database .NET-es kódtárával."
    keywords="sql kipróbálása, sql c#"   
    services="sql-database"
    documentationCenter=""
    authors="stevestein"
    manager="jhubbard"
    editor="cgronlun"/>

<tags
   ms.service="sql-database"
   ms.devlang="NA"
   ms.topic="hero-article"
   ms.tgt_pltfrm="csharp"
   ms.workload="data-management"
   ms.date="05/26/2016"
   ms.author="sstein"/>

# Az SQL Database kipróbálása: SQL-adatbázis létrehozása a C# használatával és az SQL Database .NET-es kódtárával


> [AZURE.SELECTOR]
- [Azure Portal](sql-database-get-started.md)
- [C##](sql-database-get-started-csharp.md)
- [PowerShell](sql-database-get-started-powershell.md)

Cikkünkből megtanulhatja, hogyan hozhat létre Azure SQL-adatbázisokat C#-parancsok segítségével és az [Azure SQL Database .NET-es kódtárával](https://www.nuget.org/packages/Microsoft.Azure.Management.Sql). Az SQL Database-t most egy önálló adatbázisnak az SQL és C# segítségével való létrehozásán keresztül próbálhatja ki. A rugalmas adatbáziskészletek létrehozásával kapcsolatban lásd: [Új rugalmas adatbáziskészlet létrehozása](sql-database-elastic-pool-create-portal.md). A jobb érthetőség kedvéért bizonyos kódrészleteket kiemeltünk, a cikk végén pedig egy minta-konzolalkalmazás is található, amelyben az összes parancs egyszerre szerepel.

Az Azure SQL Database .NET-es kódtára az [Azure Resource Manageren](../resource-group-overview.md) alapuló API-t tartalmaz, amely a [Resource Manager-alapú SQL Database REST API-t](https://msdn.microsoft.com/library/azure/mt163571.aspx) burkolja. Az ügyféloldali kódtár a Resource Manageren alapuló ügyféloldali kódtáraknál megszokott mintát követi. A Resource Manager erőforráscsoportokat, valamint az [Azure Active Directoryval](https://msdn.microsoft.com/library/azure/mt168838.aspx) (AAD-vel) történő hitelesítést igényel.

<br>

> [AZURE.NOTE] Az SQL Database .NET-es kódtára jelenleg előzetes verzióban érhető el.

<br>

A cikkben leírt lépések elvégzéséhez a következőkre lesz szüksége:

- Azure-előfizetés. Ha szüksége van Azure-előfizetésre, kattintson a lap tetején látható **INGYENES KIPRÓBÁLÁS** elemre, majd térjen vissza ehhez a cikkhez.
- Visual Studio. A Visual Studio ingyenes példánya beszerezhető a [Visual Studio Downloads](https://www.visualstudio.com/downloads/download-visual-studio-vs) (Visual Studio-letöltések) lapon.


## A szükséges kódtárak telepítése

Az SQL-adatbázisok C# segítségével történő beállításához szerezze be a szükséges kezelési kódtárakat, ehhez telepítse a következő csomagokat a [csomagkezelő konzol](http://docs.nuget.org/Consume/Package-Manager-Console) segítségével a Visual Studióban (**Tools (Eszközök)** > **NuGet Package Manager (NuGet csomagkezelő)** > **Package Manager Console (Csomagkezelő konzol)**):

    Install-Package Microsoft.Azure.Management.Sql –Pre
    Install-Package Microsoft.Azure.Management.ResourceManager –Pre -Version 1.1.1-preview
    Install-Package Microsoft.Azure.Common.Authentication –Pre


## Hitelesítés beállítása az Azure Active Directoryval

Először engedélyeznie kell az ügyfélalkalmazás számára az SQL Database szolgáltatás elérését. Ehhez állítsa be a megfelelő hitelesítést.

Ahhoz, hogy az aktuális felhasználó alapján hitelesítse az ügyfélalkalmazást, először regisztrálnia kell az alkalmazást az ahhoz az előfizetéshez kapcsolódó AAD-tartományban, amelyben az Azure-erőforrásokat létrehozták. Ha Azure-előfizetését nem munkahelyi vagy iskolai, hanem Microsoft-fiókkal hozták létre, az azt jelenti, hogy Ön rendelkezik alapértelmezett AAD-tartománnyal.

Az új alkalmazás létrehozásához, illetve a megfelelő Active Directoryban való regisztrálásához tegye a következőket:

1. Nyissa meg a [klasszikus Azure portált](https://manage.windowsazure.com/).
1. A bal oldalon válassza ki az **Active Directory** szolgáltatást, majd az alkalmazás hitelesítéséhez használt címtárat, kattintson rá ennek nevére (**Név** mező).

    ![Az SQL Database kipróbálása: Az Azure Active Directory (AAD) beállítása.][1]

2. A címtár lapján kattintson az **ALKALMAZÁSOK** elemre.

    ![A címtár lap az alkalmazásokkal.][5]

4. Az új alkalmazás létrehozásához kattintson az **HOZZÁADÁS**  gombra.

5. Válassza az **Add an application my organization is developing** (A vállalatom által fejlesztett alkalmazás hozzáadása) lehetőséget.

5. Adjon nevet az alkalmazásnak (**NAME** mező), majd válassza a **NATIVE CLIENT APPLICATION** (Natív ügyfélalkalmazás) lehetőséget.

    ![Adjon meg információkat az SQL C#-alkalmazásról.][7]

6. Adja meg a **REDIRECT URI** (Átirányítási URI) értékét. Ennek nem szükséges tényleges végpontnak lennie, elég, ha az URI érvényes.

    ![Adja meg az SQL C#-alkalmazás átirányítási URL-címét.][8]

7. Véglegesítse az alkalmazást. Kattintson a **CONFIGURE** (Konfigurálás) elemre, majd másolja a **CLIENT ID** (Ügyfél-azonosító) értékét (az ügyfél-azonosítóra a kód későbbi részében még szükség lesz).

    ![Szerezze be az SQL C#-alkalmazás ügyfél-azonosítóját.][9]


1. Kattintson a lap alján található **Add application** (Alkalmazás hozzáadása) elemre.
1. Válassza a **Microsoft Apps** (Microsoft-alkalmazások) lehetőséget.
1. Válassza az **Azure Service Management API** (Azure szolgáltatásfelügyeleti API) lehetőséget.
2. Most, hogy kiválasztotta az API-t, meg kell adnia az API eléréséhez szükséges konkrét engedélyeket. Ehhez válassza az **Access Azure Service Management (preview)** (Az Azure szolgáltatáskezelés elérése (előzetes verzió)) lehetőséget.

    ![Állítsa be az engedélyeket.][2]

2. Kattintson a **SAVE** (Mentés) gombra.



### A tartománynév azonosítása

A kódban szükség lesz a tartománynévre. Egyszerűen azonosíthatja a megfelelő tartománynevet:

1. Nyissa meg az [Azure Portalt](http://portal.azure.com).
2. Vigye a kurzort a jobb felső sarokban látható név fölé, és jegyezze fel az előugró ablakban megjelenő tartományt.

    ![A tartománynév azonosítása.][3]





**További AAD-források**  

Az Azure Active Directory hitelesítésre való használatáról további információkat tudhat meg [ebből a hasznos blogbejegyzésből](http://www.cloudidentity.com/blog/2013/09/12/active-directory-authentication-library-adal-v1-for-net-general-availability/).


### Az aktuális felhasználóra vonatkozó hozzáférési jogkivonat beszerzése

Az ügyfélalkalmazásnak le kell kérnie az aktuális felhasználó alkalmazás-hozzáférési jogkivonatát. Amikor egy felhasználó első alkalommal futtatja a kódot, a rendszer felkéri a felhasználói hitelesítő adatok megadására, és az így létrejövő jogkivonatot a helyi gyorsítótárba helyezi. A későbbi végrehajtások során a rendszer a gyorsítótárból kéri le a jogkivonatot, és csak akkor kéri a felhasználót ismét a bejelentkezésre, ha a jogkivonat lejárt.

Ez a kód azt a Microsoft.IdentityModel.Clients.ActiveDirectory.AuthenticationResult értéket adja vissza, amelyre az alábbi kódrészletben is szüksége lesz.

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



> [AZURE.NOTE] A cikkben szereplő példák az egyes API-kérések és -blokkok szinkron formáját használja, amíg a mögöttes szolgáltatás REST-hívása el nem készül. Aszinkron metódusok is elérhetők.



## Erőforráscsoport létrehozása

A Resource Manager használata esetén az összes erőforrást erőforráscsoportban kell létrehozni. Az erőforráscsoport egy tároló, amely alkalmazásokhoz kapcsolódó erőforrásokat tárol. Az Azure SQL Database-nél az adatbázis-kiszolgálót egy meglévő erőforráscsoporton belül kell létrehozni.

        static void CreateResourceGroup()
        {
            creds = new Microsoft.Rest.TokenCredentials(token.AccessToken);

            // Create a resource management client
            ResourceManagementClient resourceClient = new ResourceManagementClient(creds);

            // Resource group parameters
            ResourceGroup resourceGroupParameters = new ResourceGroup()
            {
                Location = location,
            };

            //Create a resource group
            resourceClient.SubscriptionId = subscriptionId;
            var resourceGroupResult = resourceClient.ResourceGroups.CreateOrUpdate(resourceGroupName, resourceGroupParameters);
        }


## A kiszolgáló létrehozása

Az SQL-adatbázisokat kiszolgálók tárolják. A kiszolgáló nevének globálisan, az összes Azure SQL-kiszolgáló között egyedinek kell lennie. Ha a kiszolgálónév már foglalt, hibaüzenet jelenik meg. Érdemes azt is megjegyezni, hogy a parancs végrehajtása több percet is igénybe vehet.

    static void CreateServer()
    {
        // Create a SQL Database management client
        SqlManagementClient sqlClient = new SqlManagementClient(new TokenCloudCredentials(subscriptionId, token.AccessToken));

        // Create a server
        ServerCreateOrUpdateParameters serverParameters = new ServerCreateOrUpdateParameters()
        {
            Location = location,
            Properties = new ServerCreateOrUpdateProperties()
            {
                AdministratorLogin = administratorLogin,
                AdministratorLoginPassword = administratorPassword,
                Version = serverVersion
            }
        };
            var serverResult = sqlClient.Servers.CreateOrUpdate(resourceGroupName, serverName, serverParameters);
    }


### Külső kiszolgáló-rendszergazda létrehozása


    // Create a server external admin
    ServerAdministratorCreateOrUpdateParameters aadAdminParameters = new ServerAdministratorCreateOrUpdateParameters()
    {
        Properties = new ServerAdministratorCreateOrUpdateProperties()
        {
            AdministratorType = "ActiveDirectory",
            Login = "<login>",
            Sid = new Guid("<Azure AD admin sid>"),
            TenantId = new Guid("<Azure AD tenant id>")
        }
    };

    var adminResult = sqlClient.ServerAdministrators.CreateOrUpdate(resourceGroupName, serverName, "ActiveDirectory", aadAdminParameters);
    var adminGetResult = sqlClient.ServerAdministrators.Get(resourceGroupName, serverName, "ActiveDirectory");




## A kiszolgálóhoz való hozzáférést engedélyező tűzfalszabály létrehozása

Alapértelmezés szerint a kiszolgálóhoz sehonnan nem lehet csatlakozni. A kiszolgálóhoz vagy az azon tárolt adatbázisokhoz való csatlakozáshoz be kell állítani egy [tűzfalszabályt](https://msdn.microsoft.com/library/azure/ee621782.aspx), amely lehetővé teszi az elérést a ügyfelek IP-címéről.

A következő példa olyan szabályt hoz létre, amely bármely IP-címről engedélyezi a kiszolgáló elérését. Javasoljuk, hogy az adatbázis behatolás elleni védelme érdekében hozzon létre megfelelő SQL-felhasználóneveket és -jelszavakat, és ne bízza magát a tűzfalszabályokra.


        static void CreateFirewallRule()
        {
            // Create a firewall rule on the server
            FirewallRuleCreateOrUpdateParameters firewallParameters = new FirewallRuleCreateOrUpdateParameters()
            {
                Properties = new FirewallRuleCreateOrUpdateProperties()
                {
                    StartIpAddress = "0.0.0.0",
                    EndIpAddress = "255.255.255.255"
                }
            };
            var firewallResult = sqlClient.FirewallRules.CreateOrUpdate(resourceGroupName, serverName, firewallRuleName, firewallParameters);
        }




Ha engedélyezni szeretné más Azure-szolgáltatásoknak a kiszolgálóhoz való hozzáférést, vegyen fel egy tűzfalszabályt, és állítsa a StartIpAddress és az EndIpAddress paraméter értékét egyaránt 0.0.0.0.-ra. Vegye figyelembe, hogy ezzel *bármely* Azure-előfizetésből engedélyezi a kiszolgálón az Azure-forgalmat.


## SQL-adatbázis létrehozása C&#x23; segítségével

Az alábbi C#-parancs létrehozza az új SQL-adatbázist, ha még nincs a kiszolgálón ilyen nevű adatbázis. Ha van, a parancs frissíti az adatbázist.


        static void CreateDatabase()
        {
            // Create a database

            // Retrieve the server on which the database will be created
            Server currentServer = sqlClient.Servers.Get(resourceGroupName, serverName).Server;

            // Create a database: configure create or update parameters and properties explicitly
            DatabaseCreateOrUpdateParameters newDatabaseParameters = new DatabaseCreateOrUpdateParameters()
            {
                Location = currentServer.Location,
                Properties = new DatabaseCreateOrUpdateProperties()
                {
                    Edition = databaseEdition,
                    RequestedServiceObjectiveName = databasePerfLevel,
                }
            };
            var dbResponse = sqlClient.Databases.CreateOrUpdate(resourceGroupName, serverName, databaseName, newDatabaseParameters);
        }



## C&#x23; konzolos mintaalkalmazás

Az alábbi minta létrehoz egy erőforráscsoportot, egy kiszolgálót, egy tűzfalszabályt és egy SQL-adatbázist. A cikk elején található *Hitelesítés beállítása az Azure Active Directoryval* szakaszból megtudhatja, hol szerezheti be a clientId, a redirectUri, és a domainName változókhoz szükséges értékeket.


    using Microsoft.Azure;
    using Microsoft.Azure.Management.ResourceManager;
    using Microsoft.Azure.Management.ResourceManager.Models;
    using Microsoft.Azure.Management.Sql;
    using Microsoft.Azure.Management.Sql.Models;
    using Microsoft.IdentityModel.Clients.ActiveDirectory;
    using System;
    using System.Collections.Generic;
    using System.Linq;
    using System.Text;
    using System.Threading.Tasks;

    namespace SqlDbConsoleApp
    {
    class Program
    {
        // Get these values from the Azure portal
        static string subscriptionId = "<Azure subscription id>";
        static string clientId = "<Azure App clientId>";
        static string redirectUri = "<Azure App redirectURI>";
        static string domainName = "<domain>";

        // You create these values
        static string resourceGroupName = "<your resource group name>";
        static string location = "<Azure data center location>";

        static string serverName = "<your server name>";
        static string administratorLogin = "<your server admin>";

        // store your password securely!
        static string administratorPassword = "<your server admin password>";
        static string serverVersion = "12.0";

        static string firewallRuleName = "<your firewall rule name>";

        static string databaseName = "dbfromcsharparticle";
        static string databaseEdition = "Basic"; // Basic, Standard, or Premium
        static string databasePerfLevel = "";

        static AuthenticationResult token;
        static Microsoft.Rest.TokenCredentials creds;

        static SqlManagementClient sqlClient;


        static void Main(string[] args)
        {
            Console.WriteLine("Logging in...");

            token = GetAccessToken();

            Console.WriteLine("Logged in as: " + token.UserInfo.DisplayableId);

            Console.WriteLine("Creating resource group...");
            CreateResourceGroup();

            sqlClient = new SqlManagementClient(new TokenCloudCredentials(subscriptionId, token.AccessToken));

            Console.WriteLine("Creating server...");
            CreateServer();

            Console.WriteLine("Creating firewall rule...");
            CreateFirewallRule();

            Console.WriteLine("Creating database...");

            DatabaseCreateOrUpdateResponse dbResponse = CreateDatabase();
            Console.WriteLine("Status: " + dbResponse.Status.ToString()
                + " Code: " + dbResponse.StatusCode.ToString());

            Console.WriteLine("Press enter to exit...");
            Console.ReadLine();
        }

        static void CreateResourceGroup()
        {
            creds = new Microsoft.Rest.TokenCredentials(token.AccessToken);

            // Create a resource management client
            ResourceManagementClient resourceClient = new ResourceManagementClient(creds);

            // Resource group parameters
            ResourceGroup resourceGroupParameters = new ResourceGroup()
            {
                Location = location,
            };

            //Create a resource group
            resourceClient.SubscriptionId = subscriptionId;
            var resourceGroupResult = resourceClient.ResourceGroups.CreateOrUpdate(resourceGroupName, resourceGroupParameters);
        }

        static void CreateServer()
        {
            // Create a SQL Database management client
            //SqlManagementClient sqlClient = new SqlManagementClient(new TokenCloudCredentials(subscriptionId, token.AccessToken));

            // Create a server
            ServerCreateOrUpdateParameters serverParameters = new ServerCreateOrUpdateParameters()
            {
                Location = location,
                Properties = new ServerCreateOrUpdateProperties()
                {
                    AdministratorLogin = administratorLogin,
                    AdministratorLoginPassword = administratorPassword,
                    Version = serverVersion
                }
            };
            var serverResult = sqlClient.Servers.CreateOrUpdate(resourceGroupName, serverName, serverParameters);
        }

        static void CreateFirewallRule()
        {
            // Create a firewall rule on the server
            FirewallRuleCreateOrUpdateParameters firewallParameters = new FirewallRuleCreateOrUpdateParameters()
            {
                Properties = new FirewallRuleCreateOrUpdateProperties()
                {
                    // replace with your client IP address
                    StartIpAddress = "0.0.0.0",
                    EndIpAddress = "255.255.255.255"
                }
            };
            var firewallResult = sqlClient.FirewallRules.CreateOrUpdate(resourceGroupName, serverName, firewallRuleName, firewallParameters);
        }

        static DatabaseCreateOrUpdateResponse CreateDatabase()
        {
            // Create a database

            // Retrieve the server on which the database will be created
            Server currentServer = sqlClient.Servers.Get(resourceGroupName, serverName).Server;

            // Create a database: configure create or update parameters and properties explicitly
            DatabaseCreateOrUpdateParameters newDatabaseParameters = new DatabaseCreateOrUpdateParameters()
            {
                Location = currentServer.Location,
                Properties = new DatabaseCreateOrUpdateProperties()
                {
                    Edition = databaseEdition,
                    RequestedServiceObjectiveName = databasePerfLevel,
                }
            };
            var dbResponse = sqlClient.Databases.CreateOrUpdate(resourceGroupName, serverName, databaseName, newDatabaseParameters);
            return dbResponse;
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
Most, hogy kipróbálta az SQL Database-t, és létrehozott egy adatbázist a C# segítségével, készen áll a következő cikkek elolvasására is:

- [Csatlakozás az SQL Database-hez az SQL Server Management Studio segítségével, és T-SQL-mintalekérdezés futtatása](sql-database-connect-query-ssms.md)

## További források

- [SQL Database](https://azure.microsoft.com/documentation/services/sql-database/)





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



<!--HONumber=sep16_HO1-->


