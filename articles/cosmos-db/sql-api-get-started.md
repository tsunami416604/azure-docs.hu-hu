---
title: 'Oktatóanyag: .NET konzolalkalmazás létrehozása az Azure Cosmos DB SQL API-fiók adatainak kezeléséhez'
description: 'Oktatóanyag: Ismerje meg, hogyan hozhat létre Azure Cosmos DB SQL API-erőforrásokat egy C# konzolalkalmazás használatával.'
author: kirankumarkolli
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: dotnet
ms.topic: tutorial
ms.date: 11/05/2019
ms.author: kirankk
ms.openlocfilehash: 2681b2199f321f695bc621ed5580319a5e907b34
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "78274025"
---
# <a name="tutorial-build-a-net-console-app-to-manage-data-in-azure-cosmos-db-sql-api-account"></a>Oktatóanyag: .NET konzolalkalmazás létrehozása az Azure Cosmos DB SQL API-fiók adatainak kezeléséhez

> [!div class="op_single_selector"]
> * [.NET](sql-api-get-started.md)
> * [Java](sql-api-java-get-started.md)
> * [Aszinkron Java](sql-api-async-java-get-started.md)
> * [Node.js](sql-api-nodejs-get-started.md)
>

Üdvözli az Azure Cosmos DB SQL API első lépések oktatóanyaga. Az oktatóanyag lépéseinek követésével egy olyan konzolalkalmazást készít, amely Azure Cosmos DB-erőforrásokat hoz létre és kérdez le.

Ez az oktatóanyag az Azure [Cosmos DB .NET SDK](https://www.nuget.org/packages/Microsoft.Azure.Cosmos)3.0-s vagy újabb verzióját használja. A .NET Framework vagy a [.NET Core segítségével](https://dotnet.microsoft.com/download)is dolgozhatunk.

Ez az oktatóanyag az alábbiakkal foglalkozik:

> [!div class="checklist"]
>
> * Azure Cosmos-fiók létrehozása és csatlakozás
> * A projekt konfigurálása a Visual Studióban
> * Adatbázis és tároló létrehozása
> * Elemek hozzáadása a tárolóhoz
> * A tároló lekérdezése
> * Létrehozási, olvasási, frissítési és törlési (CRUD) műveletek végrehajtása az elemen
> * Adatbázis törlése

Nincs elég ideje? Ne aggódjon! A teljes megoldás elérhető a [GitHubon](https://github.com/Azure-Samples/cosmos-dotnet-getting-started). Ugrás a [Get the complete tutorial megoldás szakasz](#GetSolution) gyors utasításokat.

Most pedig lássunk neki!

## <a name="prerequisites"></a>Előfeltételek

* Aktív Azure-fiók. Ha még nincs fiókja, létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/).

  [!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

* [!INCLUDE [cosmos-db-emulator-vs](../../includes/cosmos-db-emulator-vs.md)]

## <a name="step-1-create-an-azure-cosmos-db-account"></a>1. lépés: Azure Cosmos DB-fiók létrehozása

Hozzunk létre egy Azure Cosmos DB-fiókot. Ha már rendelkezik használni kívánt fiókkal, hagyja ki ezt a szakaszt. Az Azure Cosmos DB-emulátor használatához kövesse az [Azure Cosmos DB-emulátor](local-emulator.md) lépéseit az emulátor beállításához. Ezután ugorjon a [2.](#SetupVS)

[!INCLUDE [create-dbaccount-preview](../../includes/cosmos-db-create-dbaccount.md)]

## <a name="step-2-set-up-your-visual-studio-project"></a><a id="SetupVS"></a>2. lépés: A Visual Studio-projekt beállítása

1. Nyissa meg a Visual Studio alkalmazást, és válassza **az Új projekt létrehozása**lehetőséget.
1. Az **Új projekt létrehozása**csoportban válassza a Console App (.NET Framework) for C#(Konzolalkalmazás **(.NET Framework)** lehetőséget, majd válassza a **Tovább**gombot.
1. Nevezze el a *projektet CosmosGettingStartedTutorial*néven, majd válassza **a Létrehozás lehetőséget.**

    ![A projekt konfigurálása](./media/sql-api-get-started/configure-cosmos-getting-started-2019.png)

1. A **Megoldáskezelőben**kattintson a jobb gombbal az új konzolalkalmazásra, amely a Visual Studio-megoldás alatt található, és válassza **a NuGet-csomagok kezelése parancsot.**
1. A **NuGet csomagkezelőben**válassza a **Tallózás** és a *Microsoft.Azure.Cosmos*keresése lehetőséget. Válassza a **Microsoft.Azure.Cosmos** és a **Telepítés**lehetőséget.

   ![A NuGet az Azure Cosmos DB Ügyfél SDK-hoz telepítése](./media/sql-api-get-started/cosmos-getting-started-manage-nuget-2019.png)

   Az Azure Cosmos DB SQL API ügyfélkódtárának csomagazonosítója a következő: [Microsoft Azure Cosmos DB Client Library](https://www.nuget.org/packages/Microsoft.Azure.Cosmos/).

Remek! Most, hogy befejeztük a beállítást, lássunk neki a kód megírásának! Az oktatóanyag befejezett projektjéről a [.NET konzolalkalmazás fejlesztése az Azure Cosmos DB használatával](https://github.com/Azure-Samples/cosmos-dotnet-getting-started)című témakörben található.

## <a name="step-3-connect-to-an-azure-cosmos-db-account"></a><a id="Connect"></a>3. lépés: Csatlakozás egy Azure Cosmos DB-fiókhoz

1. Cserélje le a C# alkalmazás elején lévő hivatkozásokat a *Program.cs* fájlban a következő hivatkozásokkal:

   ```csharp
   using System;
   using System.Threading.Tasks;
   using System.Configuration;
   using System.Collections.Generic;
   using System.Net;
   using Microsoft.Azure.Cosmos;
   ```

1. Adja hozzá ezeket az `Program` állandókat és változókat az osztályába.

    ```csharp
    public class Program
    {
        // ADD THIS PART TO YOUR CODE

        // The Azure Cosmos DB endpoint for running this sample.
        private static readonly string EndpointUri = "<your endpoint here>";
        // The primary key for the Azure Cosmos account.
        private static readonly string PrimaryKey = "<your primary key>";

        // The Cosmos client instance
        private CosmosClient cosmosClient;

        // The database we will create
        private Database database;

        // The container we will create.
        private Container container;

        // The name of the database and container we will create
        private string databaseId = "FamilyDatabase";
        private string containerId = "FamilyContainer";
    }
    ```

   > [!NOTE]
   > Ha ismeri a .NET SDK korábbi verzióját, akkor talán ismeri a gyűjtemény és a *dokumentum* *kifejezéseket.* Mivel az Azure Cosmos DB több API-modellt is támogat, a .NET SDK 3.0-s verziója az általános kifejezések *tárolóját* és *elemét*használja. A *tároló* lehet gyűjtemény, grafikon vagy tábla. Az *elem* lehet dokumentum, él/csúcspont vagy sor, és a tárolóban lévő tartalom. További információ: [Adatbázisok, tárolók és elemek az Azure Cosmos DB-ben.](databases-containers-items.md)

1. Nyissa meg az [Azure Portalt](https://portal.azure.com). Keresse meg az Azure Cosmos DB-fiókját, és válassza a **Kulcsok lehetőséget.**

   ![Az Azure Cosmos DB-kulcsainak beszereznie az Azure Portalról](./media/sql-api-get-started/cosmos-getting-started-portal-keys.png)

1. A *Program.cs* `<your endpoint URL>` cserélje le az **URI**értékére. Cserélje `<your primary key>` ki az **ELSŐDLEGES KULCS**értékére.

1. A **Fő** módszer alatt adjon hozzá egy új aszinkron feladatot, a **GetStartedDemoAsync-et,** amely az új `CosmosClient`.

    ```csharp
    public static async Task Main(string[] args)
    {
    }

    // ADD THIS PART TO YOUR CODE
    /*
        Entry point to call methods that operate on Azure Cosmos DB resources in this sample
    */
    public async Task GetStartedDemoAsync()
    {
        // Create a new instance of the Cosmos Client
        this.cosmosClient = new CosmosClient(EndpointUri, PrimaryKey);
    }
    ```

    A **GetStartedDemoAsync-et** használjuk belépési pontként, amely az Azure Cosmos DB-erőforrásokon működő metódusokat hívja meg.

1. Adja hozzá a következő kódot a **GetStartedDemoAsync** aszinkron feladat **futtatásához** a fő metódusból. A **Main** metódus észleli a kivételeket, és a konzolba írja azokat.

    [!code-csharp[](~/cosmos-dotnet-getting-started/CosmosGettingStartedTutorial/Program.cs?name=Main)]

1. Az alkalmazás futtatásához válassza az F5 lehetőséget.

    A konzol a következő üzenetet jeleníti meg: **Vége a bemutatónak, nyomjon meg egy billentyűt a kilépéshez.** Ez az üzenet megerősíti, hogy az alkalmazás kapcsolatot létesített az Azure Cosmos DB-vel. Ezután bezárhatja a konzolablakot.

Gratulálunk! Sikeresen csatlakozott egy Azure Cosmos DB-fiókhoz.

## <a name="step-4-create-a-database"></a>4. lépés: Adatbázis létrehozása

Az adatbázis a tárolók között particionált elemek logikai tárolója. Vagy `CreateDatabaseIfNotExistsAsync` a `CreateDatabaseAsync` [CosmosClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.cosmosclient) osztály metódusa hozhat létre egy adatbázist.

1. Másolja be a `CreateDatabaseAsync` `GetStartedDemoAsync` módszert a módszer alá.

    [!code-csharp[](~/cosmos-dotnet-getting-started/CosmosGettingStartedTutorial/Program.cs?name=CreateDatabaseAsync&highlight=7)]

    `CreateDatabaseAsync`új adatbázist hoz `FamilyDatabase` létre azonosítóval, ha még nem létezik, és `databaseId` amelynek a mezőből megadott azonosítója van megadva.

1. Másolja és illessze be az alábbi kódot, ahol példányosítja a CosmosClient hívni a **CreateDatabaseAsync** metódust, amelyet most adott hozzá.

    ```csharp
    public async Task GetStartedDemoAsync()
    {
        // Create a new instance of the Cosmos Client
        this.cosmosClient = new CosmosClient(EndpointUri, PrimaryKey);

        //ADD THIS PART TO YOUR CODE
        await this.CreateDatabaseAsync();
    }
    ```

    A *Program.cs* most így kell kinéznie a végpont és az elsődleges kulcs kitöltésével.

    ```csharp
    using System;
    using System.Threading.Tasks;
    using System.Configuration;
    using System.Collections.Generic;
    using System.Net;
    using Microsoft.Azure.Cosmos;

    namespace CosmosGettingStartedTutorial
    {
        class Program
        {
            // The Azure Cosmos DB endpoint for running this sample.
            private static readonly string EndpointUri = "<your endpoint here>";
            // The primary key for the Azure Cosmos account.
            private static readonly string PrimaryKey = "<your primary key>";

            // The Cosmos client instance
            private CosmosClient cosmosClient;

            // The database we will create
            private Database database;

            // The container we will create.
            private Container container;

            // The name of the database and container we will create
            private string databaseId = "FamilyDatabase";
            private string containerId = "FamilyContainer";

            public static async Task Main(string[] args)
            {
                try
                {
                    Console.WriteLine("Beginning operations...");
                    Program p = new Program();
                    await p.GetStartedDemoAsync();
                }
                catch (CosmosException de)
                {
                    Exception baseException = de.GetBaseException();
                    Console.WriteLine("{0} error occurred: {1}\n", de.StatusCode, de);
                }
                catch (Exception e)
                {
                    Console.WriteLine("Error: {0}\n", e);
                }
                finally
                {
                    Console.WriteLine("End of demo, press any key to exit.");
                    Console.ReadKey();
                }
            }

            /// <summary>
            /// Entry point to call methods that operate on Azure Cosmos DB resources in this sample
            /// </summary>
            public async Task GetStartedDemoAsync()
            {
                // Create a new instance of the Cosmos Client
                this.cosmosClient = new CosmosClient(EndpointUri, PrimaryKey);
                await this.CreateDatabaseAsync();
            }

            /// <summary>
            /// Create the database if it does not exist
            /// </summary>
            private async Task CreateDatabaseAsync()
            {
                // Create a new database
                this.database = await this.cosmosClient.CreateDatabaseIfNotExistsAsync(databaseId);
                Console.WriteLine("Created Database: {0}\n", this.database.Id);
            }
        }
    }
    ```

1. Az alkalmazás futtatásához válassza az F5 lehetőséget.

   > [!NOTE]
   > Ha "503-as szolgáltatás nem érhető el kivétel" hibaüzenet jelenik meg, lehetséges, hogy a tűzfal blokkolja a közvetlen kapcsolódási módhoz szükséges [portokat.](performance-tips.md#networking) A probléma megoldásához nyissa meg a szükséges portokat, vagy használja az átjáró módú kapcsolatot az alábbi kód szerint:
   ```csharp
     // Create a new instance of the Cosmos Client in Gateway mode
     this.cosmosClient = new CosmosClient(EndpointUri, PrimaryKey, new CosmosClientOptions()
            {
                ConnectionMode = ConnectionMode.Gateway
            });
   ```

Gratulálunk! Sikeresen létrehozott egy Azure Cosmos-adatbázist.  

## <a name="step-5-create-a-container"></a><a id="CreateColl"></a>5. lépés: Tároló létrehozása

> [!WARNING]
> A `CreateContainerIfNotExistsAsync` metódus létrehoz egy új tárolót, amelynek árképzési vonatkozásai vannak. További részletekért látogasson el az [árképzést ismertető oldalra](https://azure.microsoft.com/pricing/details/cosmos-db/).
>
>

A tároló a [**CreateContainerIfNotExistsAsync**](/dotnet/api/microsoft.azure.cosmos.database.createcontainerifnotexistsasync?view=azure-dotnet#Microsoft_Azure_Cosmos_Database_CreateContainerIfNotExistsAsync_Microsoft_Azure_Cosmos_ContainerProperties_System_Nullable_System_Int32__Microsoft_Azure_Cosmos_RequestOptions_System_Threading_CancellationToken_) vagy a [**CreateContainerAsync**](/dotnet/api/microsoft.azure.cosmos.database.createcontainerasync?view=azure-dotnet#Microsoft_Azure_Cosmos_Database_CreateContainerAsync_Microsoft_Azure_Cosmos_ContainerProperties_System_Nullable_System_Int32__Microsoft_Azure_Cosmos_RequestOptions_System_Threading_CancellationToken_) metódus `CosmosDatabase` használatával hozható létre az osztályban. A tároló elemekből (JSON-dokumentumok, ha SQL API esetén) és a kapcsolódó kiszolgálóoldali alkalmazáslogikából áll javascriptben, például tárolt eljárásokból, felhasználó által definiált függvényekből és eseményindítókból.

1. Másolja be a `CreateContainerAsync` `CreateDatabaseAsync` módszert a módszer alá. `CreateContainerAsync`új tárolót hoz létre `FamilyContainer` az azonosítóval, ha még nem létezik, `containerId` a tulajdonság `LastName` által particionált mezőben megadott azonosító használatával.

    [!code-csharp[](~/cosmos-dotnet-getting-started/CosmosGettingStartedTutorial/Program.cs?name=CreateContainerAsync&highlight=9)]

1. Másolja és illessze be az alábbi kódot, ahol a CosmosClient-t példányosította az **imént** hozzáadott CreateContainer metódus hívásához.

    ```csharp
    public async Task GetStartedDemoAsync()
    {
        // Create a new instance of the Cosmos Client
        this.cosmosClient = new CosmosClient(EndpointUri, PrimaryKey);
        await this.CreateDatabaseAsync();

        //ADD THIS PART TO YOUR CODE
        await this.CreateContainerAsync();
    }
    ```

1. Az alkalmazás futtatásához válassza az F5 lehetőséget.

Gratulálunk! Sikeresen létrehozott egy Azure Cosmos-tárolót.  

## <a name="step-6-add-items-to-the-container"></a><a id="CreateDoc"></a>6. lépés: Elemek hozzáadása a tárolóhoz

Az `CosmosContainer` osztály [**CreateItemAsync**](/dotnet/api/microsoft.azure.cosmos.container.createitemasync?view=azure-dotnet#Microsoft_Azure_Cosmos_Container_CreateItemAsync__1___0_System_Nullable_Microsoft_Azure_Cosmos_PartitionKey__Microsoft_Azure_Cosmos_ItemRequestOptions_System_Threading_CancellationToken_) metódusa létrehozhat egy elemet. Az SQL API használatakor az elemek kivetítése dokumentumként történik, amelyek a felhasználó által definiált tetszőleges JSON-tartalom. Most már beszúrhat egy elemet az Azure Cosmos-tárolóba.

Először hozzon létre `Family` egy osztályt, amely az Azure Cosmos DB-ben ebben a mintában tárolt objektumokat jelöli. A területen használt `Parent` `Child` `Pet` `Address` alosztályokat is `Family`létrehozunk. Az elemnek `Id` a `id` JSON-hoz megfelelő en kell szerializált tulajdonsággal rendelkeznie.

1. Az Új elem hozzáadása eszköz megnyitásához válassza a Ctrl+Shift+A **billentyűkombinációt.** Új osztály `Family.cs` hozzáadása a projekthez.

    ![Képernyőkép: új Family.cs osztály hozzáadása a projekthez](./media/sql-api-get-started/cosmos-getting-started-add-family-class-2019.png)

1. Másolja és illessze `Child` `Pet`be `Address` a `Family` `Family.cs`, `Parent`, , , és osztályba .

    [!code-csharp[](~/cosmos-dotnet-getting-started/CosmosGettingStartedTutorial/Family.cs)]


1. Vissza *a Program.cs,* add hozzá a `AddItemsToContainerAsync` módszert, miután a `CreateContainerAsync` módszert.

    [!code-csharp[](~/cosmos-dotnet-getting-started/CosmosGettingStartedTutorial/Program.cs?name=AddItemsToContainerAsync)]


    A kód ellenőrzi, hogy egy azonos azonosítóval rendelkező elem még nem létezik-e. Két elemet helyezünk be, egyet-egyet az *Andersen családnak* és a *Wakefield családnak.*

1. Hívás hozzáadása `AddItemsToContainerAsync` a `GetStartedDemoAsync` metódushoz.

    ```csharp
    public async Task GetStartedDemoAsync()
    {
        // Create a new instance of the Cosmos Client
        this.cosmosClient = new CosmosClient(EndpointUri, PrimaryKey);
        await this.CreateDatabaseAsync();
        await this.CreateContainerAsync();

        //ADD THIS PART TO YOUR CODE
        await this.AddItemsToContainerAsync();
    }
    ```

1. Az alkalmazás futtatásához válassza az F5 lehetőséget.

Gratulálunk! Sikeresen létrehozott két Azure Cosmos-elemet.  

## <a name="step-7-query-azure-cosmos-db-resources"></a><a id="Query"></a>7. lépés: Az Azure Cosmos DB-erőforrások lekérdezése

Az Azure Cosmos DB támogatja az egyes tárolókban tárolt JSON-dokumentumokon végzett részletes lekérdezéseket. További információt az [SQL-lekérdezések első lépései](sql-api-sql-query.md)című témakörben talál. A következő mintakód bemutatja, hogyan futtathat lekérdezést az előző lépésben beszúrt elemeken.

1. Másolja és illessze `AddItemsToContainerAsync` be a módszert a `QueryItemsAsync` módszer után.

    [!code-csharp[](~/cosmos-dotnet-getting-started/CosmosGettingStartedTutorial/Program.cs?name=QueryItemsAsync&highlight=10-11,17-18)]

1. Hívás hozzáadása ``QueryItemsAsync`` a ``GetStartedDemoAsync`` metódushoz.

    ```csharp
    public async Task GetStartedDemoAsync()
    {
        // Create a new instance of the Cosmos Client
        this.cosmosClient = new CosmosClient(EndpointUri, PrimaryKey);
        await this.CreateDatabaseAsync();
        await this.CreateContainerAsync();
        await this.AddItemsToContainerAsync();

        //ADD THIS PART TO YOUR CODE
        await this.QueryItemsAsync();
    }
    ```

1. Az alkalmazás futtatásához válassza az F5 lehetőséget.

Gratulálunk! Sikeresen lekérdezett egy Azure Cosmos-tárolót.

## <a name="step-8-replace-a-json-item"></a><a id="ReplaceItem"></a>8. lépés: JSON-elem cseréje

Most frissítjük az Azure Cosmos DB-ben egy elemet. Megváltoztatjuk az `IsRegistered` `Family` egyik gyerek `Grade` tulajdonát.

1. Másolja és illessze `QueryItemsAsync` be a módszert a `ReplaceFamilyItemAsync` módszer után.

    [!code-csharp[](~/cosmos-dotnet-getting-started/CosmosGettingStartedTutorial/Program.cs?name=ReplaceFamilyItemAsync&highlight=15)]

1. Hívás hozzáadása `ReplaceFamilyItemAsync` a `GetStartedDemoAsync` metódushoz.

    ```csharp
    public async Task GetStartedDemoAsync()
    {
        // Create a new instance of the Cosmos Client
        this.cosmosClient = new CosmosClient(EndpointUri, PrimaryKey);
        await this.CreateDatabaseAsync();
        await this.CreateContainerAsync();
        await this.AddItemsToContainerAsync();
        await this.QueryItemsAsync();

        //ADD THIS PART TO YOUR CODE
        await this.ReplaceFamilyItemAsync();
    }
    ```

1. Az alkalmazás futtatásához válassza az F5 lehetőséget.

Gratulálunk! Sikeresen lecserélt egy Azure Cosmos-elemet.

## <a name="step-9-delete-item"></a><a id="DeleteDocument"></a>9. lépés: Elem törlése

Most törölünk egy elemet az Azure Cosmos DB-ben.

1. Másolja és illessze `ReplaceFamilyItemAsync` be a módszert a `DeleteFamilyItemAsync` módszer után.

    [!code-csharp[](~/cosmos-dotnet-getting-started/CosmosGettingStartedTutorial/Program.cs?name=DeleteFamilyItemAsync&highlight=10)]

1. Hívás hozzáadása `DeleteFamilyItemAsync` a `GetStartedDemoAsync` metódushoz.

    ```csharp
    public async Task GetStartedDemoAsync()
    {
        // Create a new instance of the Cosmos Client
        this.cosmosClient = new CosmosClient(EndpointUri, PrimaryKey);
        await this.CreateDatabaseAsync();
        await this.CreateContainerAsync();
        await this.AddItemsToContainerAsync();
        await this.QueryItemsAsync();
        await this.ReplaceFamilyItemAsync();

        //ADD THIS PART TO YOUR CODE
        await this.DeleteFamilyItemAsync();
    }
    ```

1. Az alkalmazás futtatásához válassza az F5 lehetőséget.

Gratulálunk! Sikeresen törölt egy Azure Cosmos-elemet.

## <a name="step-10-delete-the-database"></a><a id="DeleteDatabase"></a>10. lépés: Az adatbázis törlése

Most töröljük az adatbázisunkat. A létrehozott adatbázis törlése eltávolítja az adatbázist és az összes gyermekerőforrást. Az erőforrások közé tartoznak a tárolók, elemek, valamint a tárolt eljárások, a felhasználó által definiált függvények és eseményindítók. Mi is megszabadulunk a `CosmosClient` példánytól.

1. Másolja és illessze `DeleteFamilyItemAsync` be a módszert a `DeleteDatabaseAndCleanupAsync` módszer után.

    [!code-csharp[](~/cosmos-dotnet-getting-started/CosmosGettingStartedTutorial/Program.cs?name=DeleteDatabaseAndCleanupAsync)]

1. Hívás hozzáadása ``DeleteDatabaseAndCleanupAsync`` a ``GetStartedDemoAsync`` metódushoz.

    [!code-csharp[](~/cosmos-dotnet-getting-started/CosmosGettingStartedTutorial/Program.cs?name=GetStartedDemoAsync&highlight=14)]

1. Az alkalmazás futtatásához válassza az F5 lehetőséget.

Gratulálunk! Sikeresen törölt egy Azure Cosmos-adatbázist.

## <a name="step-11-run-your-c-console-application-all-together"></a><a id="Run"></a>11. lépés: Futtassa a teljes C# konzolalkalmazást!

Válassza az F5 lehetőséget a Visual Studióban az alkalmazás hibakeresési módban történő létrehozásához és futtatásához.

A teljes alkalmazás kimenetét egy konzolablakban kell látnia. A kimenet az általunk hozzáadott lekérdezések eredményeit mutatja. Meg kell egyeznie az alábbi példa szöveget.

```cmd
Beginning operations...

Created Database: FamilyDatabase

Created Container: FamilyContainer

Created item in database with id: Andersen.1 Operation consumed 11.43 RUs.

Created item in database with id: Wakefield.7 Operation consumed 14.29 RUs.

Running query: SELECT * FROM c WHERE c.LastName = 'Andersen'

        Read {"id":"Andersen.1","LastName":"Andersen","Parents":[{"FamilyName":null,"FirstName":"Thomas"},{"FamilyName":null,"FirstName":"Mary Kay"}],"Children":[{"FamilyName":null,"FirstName":"Henriette Thaulow","Gender":"female","Grade":5,"Pets":[{"GivenName":"Fluffy"}]}],"Address":{"State":"WA","County":"King","City":"Seattle"},"IsRegistered":false}

Updated Family [Wakefield,Wakefield.7].
        Body is now: {"id":"Wakefield.7","LastName":"Wakefield","Parents":[{"FamilyName":"Wakefield","FirstName":"Robin"},{"FamilyName":"Miller","FirstName":"Ben"}],"Children":[{"FamilyName":"Merriam","FirstName":"Jesse","Gender":"female","Grade":6,"Pets":[{"GivenName":"Goofy"},{"GivenName":"Shadow"}]},{"FamilyName":"Miller","FirstName":"Lisa","Gender":"female","Grade":1,"Pets":null}],"Address":{"State":"NY","County":"Manhattan","City":"NY"},"IsRegistered":true}

Deleted Family [Wakefield,Wakefield.7]

Deleted Database: FamilyDatabase

End of demo, press any key to exit.
```

Gratulálunk! Elvégezte az oktatóanyagot, és egy működőképes C# konzolalkalmazással rendelkezik!

## <a name="get-the-complete-tutorial-solution"></a><a id="GetSolution"></a> Az oktatóanyagban szereplő teljes megoldás beszerzése

Ha nem volt ideje az oktatóanyag lépéseinek elvégzésére, vagy csak le szeretné tölteni a kódmintákat, letöltheti azt.

A `GetStarted` megoldás létrehozásához a következő előfeltételekre van szükség:

* Aktív Azure-fiók. Ha még nincs fiókja, létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/).
* Egy [Azure Cosmos DB-fiók.][cosmos-db-create-account]
* A GitHubon elérhető [GetStarted](https://github.com/Azure-Samples/cosmos-dotnet-getting-started) megoldás.

Az Azure Cosmos DB .NET SDK-ra mutató hivatkozások visszaállításához kattintson a jobb gombbal a megoldásra a **Megoldáskezelőben,** majd válassza **a NuGet-csomagok visszaállítása parancsot.** Ezután az *App.config* fájlban `EndPointUri` `PrimaryKey` frissítse a [Step 3: Connect to an Azure Cosmos DB account](#Connect)3.

Ez az, építsd meg, és már úton is vagy!

## <a name="next-steps"></a>További lépések

* Összetettebb ASP.NET MVC-oktatóanyagot szeretne? Tekintse meg [az oktatóanyagot: Dolgozzon ki egy ASP.NET Core MVC webalkalmazást az Azure Cosmos DB használatával a .NET SDK használatával.](sql-api-dotnet-application.md)
* Méretezési és teljesítménytesztelést szeretne végezni az Azure Cosmos DB-vel? Lásd: [Teljesítmény- és méretezési tesztelés az Azure Cosmos DB-vel.](performance-testing.md)
* Az Azure Cosmos DB-kérelmek, -használat és -tárolás figyelése című témakörből [megtudhatja, hogy miként figyelheti a teljesítmény- és tárolási metrikákat az Azure Cosmos DB-ben.](monitor-accounts.md)
* A mintaadatkészletlekérdezéseinek futtatásához tekintse meg a [Lekérdezési játszóteret.](https://www.documentdb.com/sql/demo)
* További információ az Azure Cosmos DB-ről: [Üdvözli az Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/introduction).

[cosmos-db-create-account]: create-sql-api-java.md#create-a-database-account
