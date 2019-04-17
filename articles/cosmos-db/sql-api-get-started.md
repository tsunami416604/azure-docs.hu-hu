---
title: Adatkezelés az Azure Cosmos DB SQL API-fiók egy .NET-Konzolalkalmazás létrehozása
description: Oktatóanyag, amely létrehoz egy online adatbázist és C# konzolalkalmazást az SQL API használatával.
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: dotnet
ms.topic: tutorial
ms.date: 04/15/2019
ms.author: sngun
ms.openlocfilehash: a8d144b2cb8ee18c69dc4c4768b09422d44bade2
ms.sourcegitcommit: fec96500757e55e7716892ddff9a187f61ae81f7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2019
ms.locfileid: "59617320"
---
# <a name="build-a-net-console-app-to-manage-data-in-azure-cosmos-db-sql-api-account"></a>Adatkezelés az Azure Cosmos DB SQL API-fiók egy .NET-Konzolalkalmazás létrehozása

> [!div class="op_single_selector"]
> * [.NET](sql-api-get-started.md)
> * [.NET (előzetes verzió)](sql-api-dotnet-get-started-preview.md)
> * [.NET Core](sql-api-dotnetcore-get-started.md)
> * [A .NET core (előzetes verzió)](sql-api-dotnet-core-get-started-preview.md)
> * [Java](sql-api-java-get-started.md)
> * [Aszinkron Java](sql-api-async-java-get-started.md)
> * [Node.js](sql-api-nodejs-get-started.md)
> 

Üdvözli az Azure Cosmos DB SQL API get az első lépések oktatóanyag. Ebben az oktatóanyagban elvégezte, lesz egy konzolalkalmazást, amely létrehoz és a lekérdezések az Azure Cosmos DB-erőforrásokat.

Ez az oktatóanyag a következőket mutatja be:

> [!div class="checklist"]
>
> - Hozzon létre egy Azure Cosmos DB-fiókot, és csatlakozhat hozzá
> - A Visual Studio megoldás konfigurálása
> - Adatbázis létrehozása
> - Gyűjtemény létrehozása
> - JSON-dokumentumok létrehozása
> - A gyűjtemény lekérdezése
> - JSON-dokumentumok frissítése
> - Dokumentum törlése
> - Az adatbázis törlése

## <a name="prerequisites"></a>Előfeltételek

A telepített Azure-fejlesztési munkafolyamat a Visual Studio 2017:
- Letöltheti és használhatja a **ingyenes** [Visual Studio 2017 Community Edition](https://www.visualstudio.com/downloads/). Ügyeljen arra, hogy engedélyezze az **Azure Development** használatát a Visual Studio telepítése során. 

Az Azure-előfizetés vagy a Cosmos DB ingyenes próbaverziós fiókját:
- [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)] 
  
- [!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]  
  
- Az Azure Cosmos DB Emulatort használja, kövesse a lépéseket [Azure Cosmos DB Emulatort](local-emulator.md) az emulátor beállításához. Indítsa el az oktatóanyag következő [a Visual Studio megoldás beállítása](#SetupVS).
  
## <a name="get-the-completed-solution"></a>A kész megoldás beszerzése

Ha nincs ideje az oktatóanyag elvégzéséhez, vagy csak szeretné, Kódminták, letöltheti a teljes megoldás a [GitHub](https://github.com/Azure-Samples/documentdb-dotnet-getting-started). 

A letöltött teljes megoldás futtatásához: 

1. Ellenőrizze, hogy a [Előfeltételek](#prerequisites) telepítve. 
1. Nyissa meg a letöltött *GetStarted.sln* megoldásfájlt a Visual Studióban.
1. A **Megoldáskezelőben**, kattintson a jobb gombbal a **GetStarted** projektre, és válassza ki **NuGet-csomagok kezelése**.
1. Az a **NuGet** lapon jelölje be **visszaállítása** visszaállítása az Azure Cosmos DB .NET SDK mutató hivatkozásokat.
1. Az a *App.config* fájlt, frissítse a `EndpointUrl` és `PrimaryKey` értékek leírtak szerint a [csatlakozhat az Azure Cosmos DB-fiók](#Connect) szakaszban.
1. Válassza ki **Debug** > **Start Without Debugging** vagy nyomja le az **Ctrl**+**F5** össze és futtathatja az alkalmazást.

## <a name="create-an-azure-cosmos-db-account"></a>Azure Cosmos DB-fiók létrehozása

Kövesse ezeket az utasításokat az Azure Cosmos DB-fiók létrehozása az Azure Portalon. Ha már rendelkezik Azure Cosmos DB-fiók használatával, folytassa a [a Visual Studio megoldás beállítása](#SetupVS). 

[!INCLUDE [create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

## <a id="SetupVS"></a>A Visual Studio megoldás beállítása

1. Válassza ki a Visual Studio 2017 **fájl** > **új** > **projekt**.
   
1. Az a **új projekt** párbeszédablakban válassza **Visual C#**   >  **Console App (.NET Framework)**, nevezze el a projektet *AzureCosmosDBApp* , majd válassza ki **OK**.
   
   ![A New project (Új projekt) ablak képernyőképe](./media/sql-api-get-started/nosql-tutorial-new-project-2.png)
   
1. A **Megoldáskezelőben**, kattintson a jobb gombbal a **AzureCosmosDBApp** projektre, és válassza **NuGet-csomagok kezelése**.
   
   ![Projekt helyi menüjében](./media/sql-api-get-started/nosql-tutorial-manage-nuget-pacakges.png)
   
1. A a **NuGet** lapon jelölje be **keresse meg**, és adja meg *az azure documentdb* kifejezést a keresőmezőbe.
   
1. Keresse meg és válassza **Microsoft.Azure.DocumentDB**, és válassza ki **telepítése** ha van még nem telepítette.
   
   Az Azure Cosmos DB SQL API ügyfélkódtárának csomagazonosítója a következő: [Microsoft Azure Cosmos DB Client Library](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/).
   
   ![Képernyőfelvétel a NuGet menüről az Azure Cosmos DB ügyféloldali SDK megkereséséhez menü](./media/sql-api-get-started/nosql-tutorial-manage-nuget-pacakges-2.png)
   
   Ha a megoldás módosításainak előnézet szóló üzenetet kap, válassza ki a **OK**. Ha a licenc elfogadásáról szóló üzenetet kap, válassza ki a **elfogadom**.

## <a id="Connect"></a>Az Azure Cosmos DB-fiók csatlakoztatása

Most ismerkedjen meg néhány kódírás. A teljes *Project.cs* fájl szerepel ebben az oktatóanyagban a [GitHub](https://github.com/Azure-Samples/documentdb-dotnet-getting-started/blob/master/src/Program.cs).

1. A **Megoldáskezelőben**válassza *Program.cs*, és a Kódszerkesztő, adja hozzá a következő hivatkozásokat a fájl elejéhez:
   
   ```csharp
   using System.Net;
   using Microsoft.Azure.Documents;
   using Microsoft.Azure.Documents.Client;
   using Newtonsoft.Json;
   ```
   
1. Ezután adja hozzá a következő két állandót és a `client` változó `public class Program`.
   
   ```csharp
   
   public class Program
   {
      private const string EndpointUrl = "<your endpoint URL>";
      private const string PrimaryKey = "<your primary key>";
      private DocumentClient client;
   ```
   
1. A végponti URL-cím és az elsődleges kulcs lehetővé teszik az alkalmazás csatlakozni az Azure Cosmos DB-fiókot, és az Azure Cosmos DB-fiókot, hogy bízzon meg a kapcsolat. Másolja a helyenk a [az Azure portal](https://portal.azure.com), és illessze be őket a kód. 

   
   1. Az Azure Cosmos DB-fiók bal oldali navigációs sávján válassza **kulcsok**.
      
      ![Az Azure Portalon tárelérési kulcsok megtekintése és másolása](./media/sql-api-get-started/nosql-tutorial-keys.png)
      
   1. A **írható és olvasható kulcsok**, másolatot a **URI** értéket, a másolási gomb használatával jobb oldalán, és illessze be azt `<your endpoint URL>` a *Program.cs*. Példa: 
      
      `private const string EndpointUrl = "https://mysqlapicosmosdb.documents.azure.com:443/";`
      
   1. Másolás a **elsődleges kulcs** értékét, és illessze be azt `<your primary key>` a *Program.cs*. Példa: 
      
      `private const string PrimaryKey = "19ZDNJAiYL26tmnRvoez6hmtIfBGwjun50PWRjNYMC2ig8Ob9hYk7Fq1RYSv8FcIYnh1TdBISvCh7s6yyb0000==";`
   
1. Után az `Main` módot, adjon hozzá egy elnevezésű új aszinkron feladatot `GetStartedDemo`, amely példányosít egy új `DocumentClient` nevű `client`.
   
   ```csharp
      private async Task GetStartedDemo()
      {
        client = new DocumentClient(new Uri(EndpointUrl), PrimaryKey);
      }
   ```
   
1. Adja hozzá a következő kódot a `Main` metódust, hogy futtassa a `GetStartedDemo` feladat. A `Main` metódus kivételek elkapja, és a konzol írja őket.
   
   ```csharp
      static void Main(string[] args)
      {
        try
        {
           Program p = new Program();
           p.GetStartedDemo().Wait();
        }
        catch (DocumentClientException de)
        {
           Exception baseException = de.GetBaseException();
           Console.WriteLine($"{de.StatusCode} error occurred: {de.Message}, Message: {baseException.Message}");
        }
        catch (Exception e)
        {
           Exception baseException = e.GetBaseException();
           Console.WriteLine($"Error: {e.Message}, Message: {baseException.Message}");
        }
        finally
        {
           Console.WriteLine("End of demo, press any key to exit.");
           Console.ReadKey();
        }
      }
   ```
   
1. Nyomja meg **F5** az alkalmazás futtatásához. 
   
1. Amikor látja az üzenetet **záró bemutatóhoz, nyomja le bármelyik billentyűt, való kilépéshez** a konzolablakban, azt jelenti, hogy a kapcsolat nem volt sikeres. Nyomja le bármelyik billentyűt a konzolablak bezárásához. 

Sikeresen csatlakozott az Azure Cosmos DB-fiókot. Most néhány Azure Cosmos DB-erőforrásokkal való munka.  

## <a name="create-a-database"></a>Adatbázis létrehozása

Egy Azure Cosmos DB [adatbázis](databases-containers-items.md#azure-cosmos-databases) a JSON-dokumentumtároló gyűjtemények között particionált logikai tárolója. Az adatbázis létrehozása a [Createdatabaseasync](/dotnet/api/microsoft.azure.documents.client.documentclient.createdatabaseifnotexistsasync) módszer a `DocumentClient` osztály. 

1. Mielőtt hozzáadja a kódot az adatbázis létrehozásához, adjon hozzá egy segédmetódust a konzolba való íráshoz. Másolja és illessze be a következő `WriteToConsoleAndPromptToContinue` metódus után a `GetStartedDemo` metódus a kódban.
   
   ```csharp
   private void WriteToConsoleAndPromptToContinue(string format, params object[] args)
   {
      Console.WriteLine(format, args);
      Console.WriteLine("Press any key to continue...");
      Console.ReadKey();
   }
   ```
   
1. Másolja és illessze be a következő sort a `GetStartedDemo` metódust, miután a `client = new DocumentClient(new Uri(EndpointUrl), PrimaryKey);` sor. Ez a kód létrehoz egy adatbázist `FamilyDB`.
   
   ```csharp
      await client.CreateDatabaseIfNotExistsAsync(new Database { Id = "FamilyDB" });
   ```
   
1. Nyomja meg **F5** az alkalmazás futtatásához.

Sikeresen létrehozott egy Azure Cosmos DB-adatbázist. Láthatja, hogy az adatbázist a [az Azure portal](https://portal.azure.com) kiválasztásával **adatkezelő** az Azure Cosmos DB-fiók bal oldali navigációs. 

## <a id="CreateColl"></a>Gyűjtemény létrehozása

A gyűjtemény egy JSON-dokumentumokat és a kapcsolódó JavaScript-alkalmazáslogikát tartalmazó tároló. Használatával létrehozhat egy gyűjteményt a [Createdocumentcollectionasync](/dotnet/api/microsoft.azure.documents.client.documentclient.createdocumentcollectionifnotexistsasync#overloads) módszere a `DocumentClient` osztály. 

> [!IMPORTANT]
> **Createdocumentcollectionasync** létrehoz egy új gyűjteményt a fenntartott átviteli sebesség, amely hatással. További részletekért látogasson el a [díjszabását ismertető lapon](https://azure.microsoft.com/pricing/details/cosmos-db/).
> 

1. Másolja és illessze be a következő kódot a `GetStartedDemo` metódus után a `await client.CreateDatabaseIfNotExistsAsync(new Database { Id = "FamilyDB" });` sor. Ez a kód létrehoz egy dokumentum egy dokumentumgyűjteményben nevű `FamilyCollection`.
   
   ```csharp
      await client.CreateDocumentCollectionIfNotExistsAsync(UriFactory.CreateDatabaseUri("FamilyDB"), new DocumentCollection { Id = "FamilyCollection" });
   ```
   
1. Nyomja meg **F5** az alkalmazás futtatásához.

Sikeresen létrehozott egy Azure Cosmos DB-dokumentumgyűjteményt. A gyűjtemény alatt láthatja a **FamilyDB** adatbázist **adatkezelő** az Azure Portalon.  

## <a id="CreateDoc"></a>JSON-dokumentumok létrehozása

Dokumentumok a felhasználó által definiált, tetszőleges JSON-tartalmak. Dokumentumok rendelkeznie kell egy ID tulajdonsággal kézjegyként `id` JSON-fájlban. Dokumentumok használatával hoz létre a [CreateDocumentAsync](/dotnet/api/microsoft.azure.documents.client.documentclient.createdocumentasync#overloads) módszere a `DocumentClient` osztály. 

> [!TIP]
> Ha már rendelkezik adat, amelyet szeretne az adatbázisban tárolni, használhatja az Azure Cosmos DB [adatáttelepítési eszközét](import-data.md) importálásához.
>

A következő kódot hoz létre, és két dokumentumokat szúr be az adatbázis-gyűjtemény. Először létrehoz egy `Family` osztályt, és `Parent`, `Child`, `Pet`, és `Address` belül használandó alosztályok `Family`. Ezután létrehoz egy `CreateFamilyDocumentIfNotExists` módot, majd hozzon létre és szúrjon be két dokumentumot. 

1. Másolja és illessze be a következő `Family`, `Parent`, `Child`, `Pet`, és `Address` után osztályokat a `WriteToConsoleAndPromptToContinue` metódus a kódban.
   
   ```csharp
    public class Family
    {
        [JsonProperty(PropertyName = "id")]
        public string Id { get; set; }
        public string LastName { get; set; }
        public Parent[] Parents { get; set; }
        public Child[] Children { get; set; }
        public Address Address { get; set; }
        public bool IsRegistered { get; set; }
        public override string ToString()
        {
            return JsonConvert.SerializeObject(this);
        }
    }

    public class Parent
    {
        public string FamilyName { get; set; }
        public string FirstName { get; set; }
    }

    public class Child
    {
        public string FamilyName { get; set; }
        public string FirstName { get; set; }
        public string Gender { get; set; }
        public int Grade { get; set; }
        public Pet[] Pets { get; set; }
    }

    public class Pet
    {
        public string GivenName { get; set; }
    }

    public class Address
    {
        public string State { get; set; }
        public string County { get; set; }
        public string City { get; set; }
    }
   ```
   
1. Másolja és illessze be a következő `CreateFamilyDocumentIfNotExists` metódus után a `Address` osztály az előzőekben adott hozzá.
   
   ```csharp
    private async Task CreateFamilyDocumentIfNotExists(string databaseName, string collectionName, Family family)
    {
        try
        {
            await client.ReadDocumentAsync(UriFactory.CreateDocumentUri(databaseName, collectionName, family.Id));
            WriteToConsoleAndPromptToContinue($"Found {family.Id}");
        }
        catch (DocumentClientException de)
        {
            if (de.StatusCode == HttpStatusCode.NotFound)
            {
                await client.CreateDocumentAsync(UriFactory.CreateDocumentCollectionUri(databaseName, collectionName), family);
                WriteToConsoleAndPromptToContinue($"Created Family {family.Id}");
            }
            else
            {
                throw;
            }
        }
    }
   ```
   
1. Másolja és illessze be a következő kódot a végén a `GetStartedDemo` metódust, miután a `await client.CreateDocumentCollectionIfNotExistsAsync` sor. Ez a kód létrehozza, és szúrja be két dokumentumot, egyet az Andersen és Wakefield családhoz.
   
   ```csharp
    Family andersenFamily = new Family
    {
        Id = "AndersenFamily",
        LastName = "Andersen",
        Parents = new Parent[]
        {
            new Parent { FirstName = "Thomas" },
            new Parent { FirstName = "Mary Kay" }
        },
        Children = new Child[]
        {
            new Child
            {
                FirstName = "Henriette Thaulow",
                Gender = "female",
                Grade = 5,
                Pets = new Pet[]
                {
                    new Pet { GivenName = "Fluffy" }
                }
            }
        },
        Address = new Address { State = "WA", County = "King", City = "Seattle" },
        IsRegistered = true
    };

    await CreateFamilyDocumentIfNotExists("FamilyDB", "FamilyCollection", andersenFamily);

    Family wakefieldFamily = new Family
    {
        Id = "WakefieldFamily",
        LastName = "Wakefield",
        Parents = new Parent[]
        {
            new Parent { FamilyName = "Wakefield", FirstName = "Robin" },
            new Parent { FamilyName = "Miller", FirstName = "Ben" }
        },
        Children = new Child[]
        {
            new Child
            {
                FamilyName = "Merriam",
                FirstName = "Jesse",
                Gender = "female",
                Grade = 8,
                Pets = new Pet[]
                {
                    new Pet { GivenName = "Goofy" },
                    new Pet { GivenName = "Shadow" }
                }
            },
            new Child
            {
                FamilyName = "Miller",
                FirstName = "Lisa",
                Gender = "female",
                Grade = 1
            }
        },
        Address = new Address { State = "NY", County = "Manhattan", City = "NY" },
        IsRegistered = false
    };

    await CreateFamilyDocumentIfNotExists("FamilyDB", "FamilyCollection", wakefieldFamily);
   ```
   
1. Nyomja meg **F5** az alkalmazás futtatásához.

Sikeresen létrehozott két Azure Cosmos DB-dokumentumot. Láthatja, hogy a dokumentumok a **FamilyDB** adatbázis és **FamilyCollection** gyűjtemény **adatkezelő** az Azure Portalon.   

![A fiók, az online adatbázis, a gyűjtemény és a dokumentumok hierarchikus kapcsolatát ábrázoló diagram](./media/sql-api-get-started/nosql-tutorial-account-database.png)

## <a id="Query"></a>Azure Cosmos DB-erőforrások lekérdezése

Az Azure Cosmos DB támogatja a gazdag [lekérdezések](how-to-sql-query.md) gyűjteményekben tárolt JSON-dokumentumokon végzett. Az alábbi mintakód futtathat lekérdezéseket a minta-dokumentumokon végzett LINQ és az Azure Cosmos DB SQL szintaxisát használja.

1. Másolja és illessze be a következő `ExecuteSimpleQuery` metódus után a `CreateFamilyDocumentIfNotExists` metódus a kódban.
   
   ```csharp
    private void ExecuteSimpleQuery(string databaseName, string collectionName)
    {
        // Set some common query options.
        FeedOptions queryOptions = new FeedOptions { MaxItemCount = -1 };

        // Find the Andersen family by its LastName.
        IQueryable<Family> familyQuery = client.CreateDocumentQuery<Family>(
            UriFactory.CreateDocumentCollectionUri(databaseName, collectionName), queryOptions)
            .Where(f => f.LastName == "Andersen");

        // Execute the query synchronously. 
        // You could also execute it asynchronously using the IDocumentQuery<T> interface.
        Console.WriteLine("Running LINQ query...");
        foreach (Family family in familyQuery)
        {
            Console.WriteLine($"\tRead {family}");
        }

        // Now execute the same query using direct SQL.
        IQueryable<Family> familyQueryInSql = client.CreateDocumentQuery<Family>(
            UriFactory.CreateDocumentCollectionUri(databaseName, collectionName),
            "SELECT * FROM Family WHERE Family.LastName = 'Andersen'",
            queryOptions);

        Console.WriteLine("Running direct SQL query...");
        foreach (Family family in familyQueryInSql)
        {
            Console.WriteLine($"\tRead {family}");
        }

        Console.WriteLine("Press any key to continue ...");
        Console.ReadKey();
    }
   ```
   
1. Másolja és illessze be a következő kódot a végén a `GetStartedDemo` metódust, miután a `await CreateFamilyDocumentIfNotExists("FamilyDB", "FamilyCollection", wakefieldFamily);` sor.
   
   ```csharp
      ExecuteSimpleQuery("FamilyDB", "FamilyCollection");
   ```
   
1. Nyomja meg **F5** az alkalmazás futtatásához.

Az előző lekérdezés az Andersen családhoz tartozó teljes elemét adja vissza. Már sikeresen lekérdezett Azure Cosmos DB-gyűjteményen.

A következő ábra szemlélteti, hogyan az Azure Cosmos DB SQL-lekérdezési szintaxis meghívja a gyűjteményeken. Ugyanez a logika vonatkozik a LINQ-lekérdezésekre.

![A NoSQL-oktatóanyagban a C# konzolalkalmazás létrehozásához használt lekérdezés hatókörét és jelentését ábrázoló diagram.](./media/sql-api-get-started/nosql-tutorial-collection-documents.png)

A [FROM](how-to-sql-query.md#FromClause) kulcsszó, az SQL-lekérdezésben nem kötelező, mivel az Azure Cosmos DB-lekérdezések hatóköre eleve egyetlen gyűjtemény. Kicserélheti `FROM Families f` a `FROM root r`, vagy úgy dönt, változó neve. Az Azure Cosmos DB lesz kikövetkeztetni, amely `Families`, `root`, vagy a változó nevét, válassza ki az aktuális gyűjtemény hivatkozik.

## <a id="ReplaceDocument"></a>JSON-dokumentumok frissítése

Az Azure Cosmos DB SQL API támogatja a frissítések és JSON-dokumentumok cseréje.  

1. Másolja és illessze be a következő `ReplaceFamilyDocument` metódus után a `ExecuteSimpleQuery` metódus a kódban.
   
   ```csharp
    private async Task ReplaceFamilyDocument(string databaseName, string collectionName, string familyName, Family updatedFamily)
    {
       await client.ReplaceDocumentAsync(UriFactory.CreateDocumentUri(databaseName, collectionName, familyName), updatedFamily);
       WriteToConsoleAndPromptToContinue($"Replaced Family {familyName}");
    }
   ```
   
1. Másolja és illessze be a következő kódot a végén a `GetStartedDemo` metódust, miután a `ExecuteSimpleQuery("FamilyDB", "FamilyCollection");` sor. A kód frissíti az adatokat a dokumentumokat, és ezután futtatja újból a lekérdezést a megváltozott dokumentum megjelenítése.
   
   ```csharp
   // Update the Grade of the Andersen Family child
   andersenFamily.Children[0].Grade = 6;
   await ReplaceFamilyDocument("FamilyDB", "FamilyCollection", "AndersenFamily", andersenFamily);
   ExecuteSimpleQuery("FamilyDB", "FamilyCollection");
   ```
   
1. Nyomja meg **F5** az alkalmazás futtatásához.

A lekérdezés kimenetének azt mutatja, hogy a `Grade` frissítve lett az Andersen családhoz gyermek `5` való `6`. Hogy sikeresen frissítve, és az Azure Cosmos DB-dokumentum cseréje. 

## <a id="DeleteDocument"></a>Egy JSON-dokumentumok törlése

Az Azure Cosmos DB SQL API támogatja a JSON-dokumentumok törlését.  

1. Másolja és illessze be a következő `DeleteFamilyDocument` metódus után a `ReplaceFamilyDocument` metódust.
   
   ```csharp
    private async Task DeleteFamilyDocument(string databaseName, string collectionName, string documentName)
    {
        await client.DeleteDocumentAsync(UriFactory.CreateDocumentUri(databaseName, collectionName, documentName));
        Console.WriteLine($"Deleted Family {documentName}");
    }
   ```
   
1. Másolja és illessze be a következő kódot a végén a `GetStartedDemo` metódus után a második `ExecuteSimpleQuery("FamilyDB", "FamilyCollection");` sor.
   
   ```csharp
   await DeleteFamilyDocument("FamilyDB", "FamilyCollection", "AndersenFamily");
   ```
   
1. Nyomja meg **F5** az alkalmazás futtatásához.

Sikeresen törölt egy Azure Cosmos DB-dokumentumot. 

## <a id="DeleteDatabase"></a>Az adatbázis törlése

Törölje az adatbázist, és az ahhoz tartozó összes gyermek erőforrást, beleértve a gyűjtemény és dokumentumok eltávolítása létrehozott. 

1. Másolja és illessze be a következő kódot a végén a `GetStartedDemo` metódust, miután a `await DeleteFamilyDocument("FamilyDB", "FamilyCollection", "AndersenFamily");` sor. 
   
   ```csharp
   // Clean up - delete the database
   await client.DeleteDatabaseAsync(UriFactory.CreateDatabaseUri("FamilyDB"));
   ```
   
1. Nyomja meg **F5** az alkalmazás futtatásához.

Ön már az Azure Cosmos DB-adatbázis törlése sikerült. Az is látható a **adatkezelő** , hogy a FamilyDB adatbázis törlődik az Azure Cosmos DB-fiókja. 

## <a id="Run"></a>Futtassa a teljes C# Konzolalkalmazás

Nyomja meg **F5** a Visual Studióban való létrehozásához és futtatásához a teljes C# hibakeresési módban konzolalkalmazást. A konzolablakban a következő kimenetnek kell megjelennie:

```bash
Created Family AndersenFamily
Press any key to continue ...
 Created Family WakefieldFamily
Press any key to continue ...
 Running LINQ query...
        Read {"id":"AndersenFamily","LastName":"Andersen","Parents":[{"FamilyName":null,"FirstName":"Thomas"},{"FamilyName":null,"FirstName":"Mary Kay"}],"Children":[{"FamilyName":null,"FirstName":"Henriette Thaulow","Gender":"female","Grade":5,"Pets":[{"GivenName":"Fluffy"}]}],"Address":{"State":"WA","County":"King","City":"Seattle"},"IsRegistered":true}
Running direct SQL query...
        Read {"id":"AndersenFamily","LastName":"Andersen","Parents":[{"FamilyName":null,"FirstName":"Thomas"},{"FamilyName":null,"FirstName":"Mary Kay"}],"Children":[{"FamilyName":null,"FirstName":"Henriette Thaulow","Gender":"female","Grade":5,"Pets":[{"GivenName":"Fluffy"}]}],"Address":{"State":"WA","County":"King","City":"Seattle"},"IsRegistered":true}
Press any key to continue ...
 Replaced Family AndersenFamily
Press any key to continue ...
 Running LINQ query...
        Read {"id":"AndersenFamily","LastName":"Andersen","Parents":[{"FamilyName":null,"FirstName":"Thomas"},{"FamilyName":null,"FirstName":"Mary Kay"}],"Children":[{"FamilyName":null,"FirstName":"Henriette Thaulow","Gender":"female","Grade":6,"Pets":[{"GivenName":"Fluffy"}]}],"Address":{"State":"WA","County":"King","City":"Seattle"},"IsRegistered":true}
Running direct SQL query...
        Read {"id":"AndersenFamily","LastName":"Andersen","Parents":[{"FamilyName":null,"FirstName":"Thomas"},{"FamilyName":null,"FirstName":"Mary Kay"}],"Children":[{"FamilyName":null,"FirstName":"Henriette Thaulow","Gender":"female","Grade":6,"Pets":[{"GivenName":"Fluffy"}]}],"Address":{"State":"WA","County":"King","City":"Seattle"},"IsRegistered":true}
Press any key to continue ...
 Deleted Family AndersenFamily
End of demo, press any key to exit.
```

Gratulálunk! Elvégezte az oktatóanyagot, és rendelkezik egy működő C# konzolalkalmazást, amely létrehozza, lekérdezések, frissíti, és törli az Azure Cosmos DB erőforrásokat.  

## <a name="next-steps"></a>További lépések
* További információ az Azure Cosmos DB-ről: [Üdvözli az Azure Cosmos DB](introduction.md).
* Összetettebb ASP.NET MVC-oktatóanyagot, lásd: [ASP.NET MVC oktatóprogram: Webalkalmazások fejlesztése az Azure Cosmos DB](sql-api-dotnet-application.md).
* Méretezés és teljesítmény az Azure Cosmos DB tesztelés végrehajtásához lásd: [teljesítmény és méretezhetőség tesztelése az Azure Cosmos DB](performance-testing.md).
* Azure Cosmos DB-kérelmek, a használatot és a storage, lásd: [fiókok figyelése](monitor-accounts.md).
* Futtasson lekérdezéseket egy minta-adatkészleteken a [Query Playground](https://www.documentdb.com/sql/demo) (Tesztlekérdezések) használatával.

