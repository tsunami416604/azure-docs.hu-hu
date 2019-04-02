---
title: 'Oktatóanyag: Az Azure Cosmos DB SQL API-fiókban tárolt adatok kezelésére egy .NET Core-alkalmazás létrehozása'
description: Ebben az oktatóanyagban létrehoz egy online adatbázist és C# Konzolalkalmazás az Azure Cosmos DB SQL API .NET Core SDK használatával.
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: dotnet
ms.topic: tutorial
ms.date: 03/12/2018
ms.author: sngun
Customer intent: As a developer, I want to build a .NET Core application to access and manage Azure Cosmos DB resources so that customers can utilize the global distribution, elastic scaling, multi-master, and other capabilities that Azure Cosmos DB offers.
ms.openlocfilehash: 81c80dbd7ffa8e5c4e7b2ebb1c9d17eb6007ae9f
ms.sourcegitcommit: 3341598aebf02bf45a2393c06b136f8627c2a7b8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/01/2019
ms.locfileid: "58802351"
---
# <a name="tutorial-build-a-net-core-app-to-manage-data-stored-in-a-sql-api-account"></a>Oktatóanyag: Egy SQL API-fiókban tárolt adatok kezelésére egy .NET Core-alkalmazás létrehozása

> [!div class="op_single_selector"]
> * [.NET Core](sql-api-dotnetcore-get-started.md)
> * [A .NET core (előzetes verzió)](sql-api-dotnet-core-get-started-preview.md)
> * [.NET](sql-api-get-started.md)
> * [.NET (előzetes verzió)](sql-api-dotnet-get-started-preview.md)
> * [Java](sql-api-java-get-started.md)
> * [Aszinkron Java](sql-api-async-java-get-started.md)
> * [Node.js](sql-api-nodejs-get-started.md)
> 

A fejlesztők lehetséges, hogy NoSQL dokumentum-adatokat használó alkalmazások. Segítségével az SQL API-fiókot az Azure Cosmos DB-ben Ez a dokumentum adatok tárolása és elérése. Ez az oktatóanyag bemutatja, hogyan hozhat létre egy .NET Core-alkalmazás létrehozása és az Azure Cosmos DB SQL API-fiókban tárolt adatokat lekérdezni. 

Ez az oktatóanyag a következő feladatokat mutatja be:

> [!div class="checklist"]
> * Létrehozása és csatlakozás az Azure Cosmos-fiók
> * A Visual Studio megoldás konfigurálása
> * Online adatbázis létrehozása
> * Gyűjtemény létrehozása
> * JSON-dokumentumok létrehozása
> * CRUD-műveletek végrehajtása az elemeken, a tárolón és az adatbázison

Nincs ideje az alkalmazás létrehozására? A teljes megoldás elérhető a [GitHubon](https://github.com/Azure-Samples/documentdb-dotnet-core-getting-started). 

## <a name="prerequisites"></a>Előfeltételek

* Aktív Azure-fiók. Ha még nincs fiókja, létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/). 

  [!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

* Letöltheti és használhatja az ingyenes [Visual Studio 2017 Community Edition](https://www.visualstudio.com/downloads/). Ha Univerzális Windows-platformon futó alkalmazást (UWP-alkalmazást) fejleszt, használja a **Visual Studio 2017 15.4-es** vagy újabb verzióját. Ügyeljen arra, hogy engedélyezze az **Azure Development** használatát a Visual Studio telepítése során.

    * MacOS vagy Linux esetén is fejleszthet .NET Core alkalmazásokat, a parancssorból telepítésével a [.NET Core SDK](https://www.microsoft.com/net/core#macos) a választott platformra. 

    * Windows, a telepítésével is fejleszthet .NET Core alkalmazásokat, a parancssorból a [.NET Core SDK](https://www.microsoft.com/net/core#windows). 

## <a name="create-an-azure-cosmos-account"></a>Egy Azure Cosmos-fiók létrehozása

A következő lépések segítségével hozzon létre egy Azure Cosmos-fiók:

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

## <a id="SetupVS"></a>A Visual Studio-megoldás beállítása

1. Nyissa meg a **Visual Studio 2017-et** a számítógépén.

2. A **Fájl** menüben válassza az **Új**, majd a **Projekt** elemet.

3. A **New Project** (Új projekt) párbeszédpanelen válassza a **Templates** (Sablonok) > **Visual C#** > **.NET Core** > **Console Application (.NET Core)** (Konzolalkalmazás (.NET Core)) elemet, adja a projektnek a **DocumentDBGettingStarted** nevet, majd kattintson az **OK** gombra.

   ![A New project (Új projekt) ablak képernyőképe](./media/sql-api-dotnetcore-get-started/nosql-tutorial-new-project-2.png)

4. Az a **Megoldáskezelőben**, kattintson a jobb gombbal **DocumentDBGettingStarted**.

5. Ugyanebben a menüben válassza ki a **NuGet-csomagok kezelése**.

   ![A helyi menüben a projekt képernyőképe](./media/sql-api-dotnetcore-get-started/nosql-tutorial-manage-nuget-pacakges.png)

6. Az a **NuGet** lapon jelölje be **Tallózás** felső részén az ablakot, és írja be **az azure documentdb** kifejezést a keresőmezőbe. Ügyeljen arra, hogy a **előzetes verzió** jelölőnégyzet be van jelölve.

7. Keresse meg az eredmények **Microsoft.Azure.DocumentDB.Core** válassza **telepítése**.

   A kódtár csomagazonosítója a következő: [Microsoft.Azure.DocumentDB.Core](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB.Core). Ha egy .NET-keretrendszer verziója (például net461), amely nem támogatja a .NET Core NuGet csomag céloz meg, használja a [Microsoft.Azure.DocumentDB](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB), amely támogatja a .NET-keretrendszer 4.5 kezdődően minden .NET-keretrendszerverziót.

8. Amikor a rendszer kéri, fogadja el a NuGet-csomag telepítését és a licencszerződést.

A beállítás befejeztével nekiláthat a kód írásának. Ez az oktatóanyag teljes kódja projekt találhat [GitHub](https://github.com/Azure-Samples/documentdb-dotnet-core-getting-started).

## <a id="Connect"></a>Csatlakozás az Azure Cosmos-fiókhoz

Csatlakozás az Azure Cosmos-fiókhoz a szükséges függőségek importálásával. Importálja a függőségeket, adja hozzá a következő kódot a Program.cs fájl elejéhez:

```csharp
using System;

// ADD THIS PART TO YOUR CODE
using System.Linq;
using System.Threading.Tasks;
using System.Net;
using Microsoft.Azure.Documents;
using Microsoft.Azure.Documents.Client;
using Newtonsoft.Json;
```

Ezután adja hozzá ezt a két állandót és a *ügyfél* változó nyilvános osztály alatt *Program*.

```csharp
class Program
{
    // ADD THIS PART TO YOUR CODE
    private const string EndpointUri = "<your endpoint URI>";
    private const string PrimaryKey = "<your key>";
    private DocumentClient client;
```

Ezután látogasson el az [Azure Portalra](https://portal.azure.com) az URI és az elsődleges kulcs beszerzéséhez. Az Azure Cosmos DB URI és az elsődleges kulcs ahhoz szükséges, hogy az alkalmazás tudja, hova kell csatlakoznia, az Azure Cosmos DB pedig megbízzon az alkalmazás által létesített kapcsolatban.

Az Azure Portalon nyissa meg az Azure Cosmos-fiók, és válassza ki **kulcsok**.

Másolja ki az URI-t a portálról, és illessze be a program.cs fájl `<your endpoint URI>` elemébe. Ezután másolja ki a PRIMARY KEY kulcsot a portálról, és illessze be a `<your key>` elembe. Mindenképp távolítsa el a < és a > jelet, azonban az idézőjeleket hagyja meg a végpont és a kulcs körül.

![Kulcsok beszerzése az Azure Portalról][keys]

Először létrehozunk egy új **DocumentClient** példányt az első lépések alkalmazáshoz.

Alább a **fő** metódus hozzáadása elnevezésű új aszinkron feladatot **GetStartedDemo**, amely létrehozza nekünk az új **DocumentClient**.

```csharp
static void Main(string[] args)
{
}

// ADD THIS PART TO YOUR CODE
private async Task GetStartedDemo()
{
    this.client = new DocumentClient(new Uri(EndpointUri), PrimaryKey);
}
```

Adja hozzá a következő kódot az aszinkron feladat **Fő** metódusból való futtatásához. A **Main** metódus észleli a kivételeket, és a konzolba írja azokat.

```csharp
static void Main(string[] args)
{
        // ADD THIS PART TO YOUR CODE
        try
        {
                Program p = new Program();
                p.GetStartedDemo().Wait();
        }
        catch (DocumentClientException de)
        {
                Exception baseException = de.GetBaseException();
                Console.WriteLine("{0} error occurred: {1}, Message: {2}", de.StatusCode, de.Message, baseException.Message);
        }
        catch (Exception e)
        {
                Exception baseException = e.GetBaseException();
                Console.WriteLine("Error: {0}, Message: {1}", e.Message, baseException.Message);
        }
        finally
        {
                Console.WriteLine("End of demo, press any key to exit.");
                Console.ReadKey();
        }
```

Válassza a **DocumentDBGettingStarted** gombot az alkalmazás létrehozásához és futtatásához.

## <a id="CreateDatabase"></a>Adatbázis létrehozása

Mielőtt hozzáadja a kódot az adatbázis létrehozásához, adjon hozzá egy segédmetódust a konzolba való íráshoz. Másolja, majd illessze be a **WriteToConsoleAndPromptToContinue** metódust a **GetStartedDemo** metódus alá.

```csharp
// ADD THIS PART TO YOUR CODE
private void WriteToConsoleAndPromptToContinue(string format, params object[] args)
{
        Console.WriteLine(format, args);
        Console.WriteLine("Press any key to continue ...");
        Console.ReadKey();
}
```

Az Azure Cosmos DB-adatbázis létrehozása a használatával a `CreateDatabaseAsync` módszere a **DocumentClient** osztály. Az adatbázis a JSON-dokumentumtároló gyűjtemények között particionált logikai tárolója. Másolja, majd illessze be az alábbi kódot a **GetStartedDemo** metódusba az ügyfél létrehozása alatt. Ezzel létrehoz egy *FamilyDB* nevű adatbázist.

```csharp
private async Task GetStartedDemo()
{
    this.client = new DocumentClient(new Uri(EndpointUri), PrimaryKey);

    // ADD THIS PART TO YOUR CODE
    await this.client.CreateDatabaseIfNotExistsAsync(new Database { Id = "FamilyDB_oa" });
```

Válassza a **DocumentDBGettingStarted** gombot az alkalmazás futtatásához.

## <a id="CreateColl"></a>Gyűjtemény létrehozása

A gyűjtemény létrehozása a `CreateDocumentCollectionAsync` módszere a **DocumentClient** osztály. A gyűjtemény egy JSON-dokumentumokat és a kapcsolódó JavaScript-alkalmazáslogikát tartalmazó tároló.

> [!WARNING]
> A **CreateDocumentCollectionAsync** létrehoz egy fenntartott adatátviteli sebességgel rendelkező új gyűjteményt, amely költségeket von maga után. További részletekért látogasson el a [díjszabását ismertető lapon](https://azure.microsoft.com/pricing/details/cosmos-db/).

Másolja, majd illessze be az alábbi kódot a **GetStartedDemo** metódusba az adatbázis létrehozása alatt. Ez a kód létrehoz egy *FamilyCollection_oa* nevű dokumentumgyűjteményt.

```csharp
    this.client = new DocumentClient(new Uri(EndpointUri), PrimaryKey);

    await this.client.CreateDatabaseIfNotExistsAsync(new Database { Id = "FamilyDB_oa" });

    // ADD THIS PART TO YOUR CODE
    await this.client.CreateDocumentCollectionIfNotExistsAsync(UriFactory.CreateDatabaseUri("FamilyDB_oa"), new DocumentCollection { Id = "FamilyCollection_oa" });
```

Válassza a **DocumentDBGettingStarted** gombot az alkalmazás futtatásához.

## <a id="CreateDoc"></a>JSON-dokumentumok létrehozása

A dokumentum létrehozása a `CreateDocumentAsync` módszere a **DocumentClient** osztály. A dokumentumok a felhasználó által megadott (tetszőleges) JSON-tartalmak. Most már beszúrhat egy vagy több dokumentumot. 

Először létrehoz egy **termékcsalád** osztály, amely az Azure Cosmos DB-ben tárolt objektumokat képviseli. Is létrehozhat **szülő**, **gyermek**, **Kisállat**, és **cím** belül használt alosztályok **termékcsalád**. A dokumentumoknak rendelkezniük kell egy **Id** tulajdonsággal, amely a JSON-fájlban **id**-ként van szerializálva. Az osztályok létrehozásához vegye fel az alábbi belső alosztályokat a **GetStartedDemo** metódus után. Másolja, majd illessze be a **Család**, **Szülő**, **Gyermek**, **Háziállat** és **Cím** osztályokat a **WriteToConsoleAndPromptToContinue** metódus alá.

```csharp
private void WriteToConsoleAndPromptToContinue(string format, params object[] args)
{
    Console.WriteLine(format, args);
    Console.WriteLine("Press any key to continue ...");
    Console.ReadKey();
}

// ADD THIS PART TO YOUR CODE
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

Másolja, majd illessze be a **CreateFamilyDocumentIfNotExists** metódust a **CreateDocumentCollectionIfNotExists** metódus alá.

```csharp
// ADD THIS PART TO YOUR CODE
private async Task CreateFamilyDocumentIfNotExists(string databaseName, string collectionName, Family family)
{
    try
    {
        await this.client.ReadDocumentAsync(UriFactory.CreateDocumentUri(databaseName, collectionName, family.Id));
        this.WriteToConsoleAndPromptToContinue("Found {0}", family.Id);
    }
    catch (DocumentClientException de)
    {
        if (de.StatusCode == HttpStatusCode.NotFound)
        {
            await this.client.CreateDocumentAsync(UriFactory.CreateDocumentCollectionUri(databaseName, collectionName), family);
            this.WriteToConsoleAndPromptToContinue("Created Family {0}", family.Id);
        }
        else
        {
            throw;
        }
    }
}
```

Ezután szúrjon be két dokumentumot, egyet az Andersen családhoz, egyet pedig a Wakefield családhoz. Másolja, majd illessze be a `// ADD THIS PART TO YOUR CODE` után álló kódot a **GetStartedDemo** metódusba, a dokumentumgyűjtemény létrehozása alatt.

```csharp
    await this.client.CreateDatabaseIfNotExistsAsync(new Database { Id = "FamilyDB_oa" });

await this.CreateDocumentCollectionIfNotExists("FamilyDB_oa", "FamilyCollection_oa");

// ADD THIS PART TO YOUR CODE
Family andersenFamily = new Family
{
        Id = "Andersen.1",
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

await this.CreateFamilyDocumentIfNotExists("FamilyDB_oa", "FamilyCollection_oa", andersenFamily);

Family wakefieldFamily = new Family
{
        Id = "Wakefield.7",
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

await this.CreateFamilyDocumentIfNotExists("FamilyDB_oa", "FamilyCollection_oa", wakefieldFamily);
```

Válassza a **DocumentDBGettingStarted** gombot az alkalmazás futtatásához.

![A fiók, az online adatbázis és a gyűjtemény hierarchikus kapcsolata](./media/sql-api-dotnetcore-get-started/nosql-tutorial-account-database.png)

## <a id="Query"></a>Azure Cosmos DB-erőforrások lekérdezése

Az Azure Cosmos DB támogatja az egyes gyűjteményekben tárolt JSON-dokumentumokon végzett részletes lekérdezéseket. Az alábbi mintakód bemutatja a különböző lekérdezéseket – Azure Cosmos DB SQL-szintaxis és a LINQ -, amely azt az előző lépésben beszúrt dokumentumokon futtatható.

Másolja, majd illessze be a **ExecuteSimpleQuery** metódust a **CreateFamilyDocumentIfNotExists** metódus alá.

```csharp
// ADD THIS PART TO YOUR CODE
private void ExecuteSimpleQuery(string databaseName, string collectionName)
{
    // Set some common query options
    FeedOptions queryOptions = new FeedOptions { MaxItemCount = -1 };

        // Here we find the Andersen family via its LastName
        IQueryable<Family> familyQuery = this.client.CreateDocumentQuery<Family>(
                UriFactory.CreateDocumentCollectionUri(databaseName, collectionName), queryOptions)
                .Where(f => f.LastName == "Andersen");

        // The query is executed synchronously here, but can also be executed asynchronously via the IDocumentQuery<T> interface
        Console.WriteLine("Running LINQ query...");
        foreach (Family family in familyQuery)
        {
            Console.WriteLine("\tRead {0}", family);
        }

        // Now execute the same query via direct SQL
        IQueryable<Family> familyQueryInSql = this.client.CreateDocumentQuery<Family>(
                UriFactory.CreateDocumentCollectionUri(databaseName, collectionName),
                "SELECT * FROM Family WHERE Family.LastName = 'Andersen'",
                queryOptions);

        Console.WriteLine("Running direct SQL query...");
        foreach (Family family in familyQueryInSql)
        {
                Console.WriteLine("\tRead {0}", family);
        }

        Console.WriteLine("Press any key to continue ...");
        Console.ReadKey();
}
```

Másolja, majd illessze be az alábbi kódot a **GetStartedDemo** metódusba a második dokumentum létrehozása alá.

```csharp
await this.CreateFamilyDocumentIfNotExists("FamilyDB_oa", "FamilyCollection_oa", wakefieldFamily);

// ADD THIS PART TO YOUR CODE
this.ExecuteSimpleQuery("FamilyDB_oa", "FamilyCollection_oa");
```

Válassza a **DocumentDBGettingStarted** gombot az alkalmazás futtatásához.

Az alábbi diagram bemutatja, hogyan indít hívást az Azure Cosmos DB SQL-lekérdezés szintaxisa a létrehozott gyűjteményre. Ugyanez a logika vonatkozik a LINQ-lekérdezésekre.

![A NoSQL-oktatóanyagban a C# konzolalkalmazás létrehozásához használt lekérdezés hatókörét és jelentését ábrázoló diagram.](./media/sql-api-dotnetcore-get-started/nosql-tutorial-collection-documents.png)

A `FROM` kulcsszó kihagyható a lekérdezésből, mert az Azure Cosmos DB-lekérdezések hatóköre eleve egyetlen gyűjtemény. Ezért a „FROM Families f” lecserélhető a „FROM root r” vagy bármilyen tetszőleges változónévre. Az Azure Cosmos DB kikövetkezteti, hogy a családot, a legfelső szintű vagy a választott változó nevét hivatkozik az aktuális gyűjtemény alapértelmezés szerint.

## <a id="ReplaceDocument"></a>Cserélje le a JSON-dokumentumok

Az Azure Cosmos DB támogatja a JSON-dokumentumok cseréjét.  

Másolja, majd illessze be a **ReplaceFamilyDocument** metódust az **ExecuteSimpleQuery** metódus alá.

```csharp
// ADD THIS PART TO YOUR CODE
private async Task ReplaceFamilyDocument(string databaseName, string collectionName, string familyName, Family updatedFamily)
{
    await this.client.ReplaceDocumentAsync(UriFactory.CreateDocumentUri(databaseName, collectionName, familyName), updatedFamily);
    this.WriteToConsoleAndPromptToContinue("Replaced Family {0}", familyName);
}
```

Másolja, majd illessze be az alábbi kódot a **GetStartedDemo** metódusba a lekérdezés végrehajtása alá. Ezáltal a dokumentum cseréje után ismét ugyanaz a lekérdezés fog lefutni a megváltozott dokumentum megtekintéséhez.

```csharp
await this.CreateFamilyDocumentIfNotExists("FamilyDB_oa", "FamilyCollection_oa", wakefieldFamily);

this.ExecuteSimpleQuery("FamilyDB_oa", "FamilyCollection_oa");

// ADD THIS PART TO YOUR CODE
// Update the Grade of the Andersen Family child
andersenFamily.Children[0].Grade = 6;

await this.ReplaceFamilyDocument("FamilyDB_oa", "FamilyCollection_oa", "Andersen.1", andersenFamily);

this.ExecuteSimpleQuery("FamilyDB_oa", "FamilyCollection_oa");
```

Válassza a **DocumentDBGettingStarted** gombot az alkalmazás futtatásához.

## <a id="DeleteDocument"></a>JSON-dokumentumok törlése

Az Azure Cosmos DB támogatja a JSON-dokumentumok törlését.  

Másolja, majd illessze be a **DeleteFamilyDocument** metódust a **ReplaceFamilyDocument** metódus alá.

```csharp
// ADD THIS PART TO YOUR CODE
private async Task DeleteFamilyDocument(string databaseName, string collectionName, string documentName)
{
    await this.client.DeleteDocumentAsync(UriFactory.CreateDocumentUri(databaseName, collectionName, documentName));
    Console.WriteLine("Deleted Family {0}", documentName);
}
```

Másolja, majd illessze be az alábbi kódot a **GetStartedDemo** metódusba a második lekérdezés végrehajtása alá.

```csharp
await this.ReplaceFamilyDocument("FamilyDB_oa", "FamilyCollection_oa", "Andersen.1", andersenFamily);

this.ExecuteSimpleQuery("FamilyDB_oa", "FamilyCollection_oa");

// ADD THIS PART TO CODE
await this.DeleteFamilyDocument("FamilyDB_oa", "FamilyCollection_oa", "Andersen.1");
```

Válassza a **DocumentDBGettingStarted** gombot az alkalmazás futtatásához.

## <a id="DeleteDatabase"></a>Az adatbázis törlése

A létrehozott adatbázis törlésével az adatbázis és az összes gyermekerőforrás (gyűjtemények, dokumentumok stb.). Másolja és illessze be a következő kódot a **GetStartedDemo** metódust a dokumentum a teljes adatbázist és az összes gyermek-erőforrás törléséhez.

```csharp
this.ExecuteSimpleQuery("FamilyDB_oa", "FamilyCollection_oa");

await this.DeleteFamilyDocument("FamilyDB_oa", "FamilyCollection_oa", "Andersen.1");

// ADD THIS PART TO CODE
// Clean up/delete the database
await this.client.DeleteDatabaseAsync(UriFactory.CreateDatabaseUri("FamilyDB_oa"));
```

Válassza a **DocumentDBGettingStarted** gombot az alkalmazás futtatásához.

## <a id="Run"></a>Futtassa a C# -Konzolalkalmazás

A Visual Studióban válassza a **DocumentDBGettingStarted** gombot az alkalmazás hibakeresési módban történő létrehozásához. A konzolablakban meg kell jelennie az Első lépések alkalmazás kimenetének. A kimenet megjeleníti a hozzáadott lekérdezések eredményeit, amelynek meg kell egyeznie az alábbi mintaszöveggel.

```bash
Created FamilyDB_oa
Press any key to continue ...
Created FamilyCollection_oa
Press any key to continue ...
Created Family Andersen.1
Press any key to continue ...
Created Family Wakefield.7
Press any key to continue ...
Running LINQ query...
    Read {"id":"Andersen.1","LastName":"Andersen","District":"WA5","Parents":[{"FamilyName":null,"FirstName":"Thomas"},{"FamilyName":null,"FirstName":"Mary Kay"}],"Children":[{"FamilyName":null,"FirstName":"Henriette Thaulow","Gender":"female","Grade":5,"Pets":[{"GivenName":"Fluffy"}]}],"Address":{"State":"WA","County":"King","City":"Seattle"},"IsRegistered":true}
Running direct SQL query...
    Read {"id":"Andersen.1","LastName":"Andersen","District":"WA5","Parents":[{"FamilyName":null,"FirstName":"Thomas"},{"FamilyName":null,"FirstName":"Mary Kay"}],"Children":[{"FamilyName":null,"FirstName":"Henriette Thaulow","Gender":"female","Grade":5,"Pets":[{"GivenName":"Fluffy"}]}],"Address":{"State":"WA","County":"King","City":"Seattle"},"IsRegistered":true}
Replaced Family Andersen.1
Press any key to continue ...
Running LINQ query...
    Read {"id":"Andersen.1","LastName":"Andersen","District":"WA5","Parents":[{"FamilyName":null,"FirstName":"Thomas"},{"FamilyName":null,"FirstName":"Mary Kay"}],"Children":[{"FamilyName":null,"FirstName":"Henriette Thaulow","Gender":"female","Grade":6,"Pets":[{"GivenName":"Fluffy"}]}],"Address":{"State":"WA","County":"King","City":"Seattle"},"IsRegistered":true}
Running direct SQL query...
    Read {"id":"Andersen.1","LastName":"Andersen","District":"WA5","Parents":[{"FamilyName":null,"FirstName":"Thomas"},{"FamilyName":null,"FirstName":"Mary Kay"}],"Children":[{"FamilyName":null,"FirstName":"Henriette Thaulow","Gender":"female","Grade":6,"Pets":[{"GivenName":"Fluffy"}]}],"Address":{"State":"WA","County":"King","City":"Seattle"},"IsRegistered":true}
Deleted Family Andersen.1
End of demo, press any key to exit.
```

Az oktatóanyag végrehajtotta, és rendelkezik egy működő C# Konzolalkalmazás.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Amikor szükség van rájuk már nem, törölheti az erőforráscsoportot, az Azure Cosmos-fiók és minden kapcsolódó erőforrás. Ehhez válassza ki a virtuális gép, jelölje be az erőforráscsoport **törlése**, majd erősítse meg a törölni kívánt erőforráscsoport nevét.

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban bemutattuk, hogyan hozhat létre egy .NET Core-alkalmazást az Azure Cosmos DB SQL API-fiókban tárolt adatok kezelésére. Továbbléphet a következő cikkre:

> [!div class="nextstepaction"]
> [Egy Java-konzolalkalmazást az Azure Cosmos DB SQL API-fiók létrehozása](sql-api-java-get-started.md)

[create-sql-api-dotnet.md#create-account]: create-sql-api-dotnet.md#create-account
[keys]: media/sql-api-dotnetcore-get-started/nosql-tutorial-keys.png
