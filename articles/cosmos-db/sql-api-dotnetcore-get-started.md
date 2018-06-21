---
title: 'Azure Cosmos DB: Bevezetés az SQL API és a .NET Core használatába – oktatóanyag | Microsoft Docs'
description: Ez az oktatóanyag létrehoz egy online adatbázist és egy C# konzolalkalmazást az Azure Cosmos DB SQL API .NET Core SDK használatával.
services: cosmos-db
author: SnehaGunda
manager: kfile
editor: ''
ms.service: cosmos-db
ms.component: cosmosdb-sql
ms.devlang: dotnet
ms.topic: tutorial
ms.date: 03/12/2018
ms.author: sngun
ms.custom: devcenter
ms.openlocfilehash: 2d1f93e839d4ad2d7c857916996f2ad5d09626c5
ms.sourcegitcommit: 6116082991b98c8ee7a3ab0927cf588c3972eeaa
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/05/2018
ms.locfileid: "34796587"
---
# <a name="azure-cosmos-db-getting-started-with-the-sql-api-and-net-core"></a>Azure Cosmos DB: Bevezetés az SQL API és a .NET Core használatába
> [!div class="op_single_selector"]
> * [.NET](sql-api-get-started.md)
> * [.NET Core](sql-api-dotnetcore-get-started.md)
> * [Node.js MongoDB-hez](mongodb-samples.md)
> * [Node.js](sql-api-nodejs-get-started.md)
> * [Java](sql-api-java-get-started.md)
> * [C++](sql-api-cpp-get-started.md)
>  
> 

Üdvözöljük az Azure Cosmos DB SQL API .NET Core-ral való használatához készült, kezdeti lépéseket ismertető oktatóanyagban! Az oktatóanyag lépéseinek követésével egy olyan konzolalkalmazást készít, amely Azure Cosmos DB-erőforrásokat hoz létre és kérdez le.

Ez az oktatóanyag az alábbiakkal foglalkozik:

* Azure Cosmos DB-fiók létrehozása és csatlakoztatása
* A Visual Studio megoldás konfigurálása
* Online adatbázis létrehozása
* Gyűjtemény létrehozása
* JSON-dokumentumok létrehozása
* A gyűjtemény lekérdezése
* Dokumentum cseréje
* Dokumentum törlése
* Adatbázis törlése

Nincs elég ideje? Ne aggódjon! A teljes megoldás elérhető a [GitHubon](https://github.com/Azure-Samples/documentdb-dotnet-core-getting-started). A gyors utasításokért ugorjon [A teljes megoldás beszerzése szakaszra](#GetSolution).

Xamarin iOS-, Android- vagy Forms-alkalmazást szeretne létrehozni az SQL API és a .NET Core SDK használatával? Tekintse meg a következőt: [Mobilalkalmazások létrehozása a Xamarin és az Azure Cosmos DB használatával](mobile-apps-with-xamarin.md).

Most pedig lássunk neki!

## <a name="prerequisites"></a>Előfeltételek

* Aktív Azure-fiók. Ha még nincs fiókja, létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/). 

  [!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

* Ha nincs telepítve a Visual Studio 2017, letöltheti és használhatja az ingyenes [Visual Studio 2017 Community Editiont](https://www.visualstudio.com/downloads/). Ha Univerzális Windows-platformon futó alkalmazást (UWP-alkalmazást) fejleszt, használja a **Visual Studio 2017 15.4-es** vagy újabb verzióját. Ügyeljen arra, hogy engedélyezze az **Azure Development** használatát a Visual Studio telepítése során.
    * Ha MacOS vagy Linux rendszeren dolgozik, a parancssorból is fejleszthet .NET Core-alkalmazásokat, ha telepíti a [.NET Core SDK-t](https://www.microsoft.com/net/core#macos) a választott platformra. 
    * Ha Windows rendszeren dolgozik, a parancssorból is fejleszthet .NET Core-alkalmazásokat, ha telepíti a [.NET Core SDK-t](https://www.microsoft.com/net/core#windows). 
    * Használhat saját szerkesztőt is, vagy letöltheti az ingyenes [Visual Studio Code](https://code.visualstudio.com/) alkalmazást, amely Windows, Linux és MacOS rendszeren egyaránt működik. 

## <a name="step-1-create-an-azure-cosmos-db-account"></a>1. lépés: Azure Cosmos DB-fiók létrehozása
Hozzunk létre egy Azure Cosmos DB-fiókot. Ha van már olyan fiókja, amelyet használni szeretne, ugorjon előre a [Visual Studio megoldás beállítása](#SetupVS) című lépésre. Ha az Azure Cosmos DB Emulatort használja, kövesse az [Azure Cosmos DB Emulatornál](local-emulator.md) leírt lépéseket az emulátor telepítéséhez, majd ugorjon előre [A Visual Studio megoldás beállítása](#SetupVS) című lépésre.

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

## <a id="SetupVS"></a>2. lépés: A Visual Studio megoldás beállítása
1. Nyissa meg a **Visual Studio 2017-et** a számítógépén.
2. A **Fájl** menüben válassza az **Új**, majd a **Projekt** elemet.
3. A **New Project** (Új projekt) párbeszédpanelen válassza a **Templates** (Sablonok)  / **Visual C#** / **.NET Core**/**Console Application (.NET Core)** (Konzolalkalmazás (.NET Core)) elemet, adja a projektnek a **DocumentDBGettingStarted** nevet, majd kattintson az **OK** gombra.

   ![Képernyőfelvétel az Új projekt ablakról](./media/sql-api-dotnetcore-get-started/nosql-tutorial-new-project-2.png)
4. A **Solution Explorer** (Megoldáskezelő) felületén kattintson a jobb gombbal a **DocumentDBGettingStarted** elemre.
5. Ezután továbbra is a menüben kattintson a **Manage NuGet Packages…** (NuGet-csomagok kezelése…) lehetőségre.

   ![A Projekt jobb gombos kattintással elérhető menüjének képernyőfelvétele](./media/sql-api-dotnetcore-get-started/nosql-tutorial-manage-nuget-pacakges.png)
6. A **NuGet** lapon kattintson a **Browse** (Tallózás) elemre az ablak tetején, majd írja be az **azure documentdb** kifejezést a keresőmezőbe.
7. A találatok között keresse meg a **Microsoft.Azure.DocumentDB.Core** elemet, majd kattintson a **Telepítés** lehetőségre.
   A kódtár csomagazonosítója a következő: [Microsoft.Azure.DocumentDB.Core](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB.Core). Ha a .NET-keretrendszer olyan verzióját célozza meg (például net461), amelyet ez a .NET Core NuGet csomag nem támogat, használja a [Microsoft.Azure.DocumentDB](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB)-t, amely a .NET-keretrendszer 4.5-ös verziójától kezdődően minden verziót támogat.
8. Amikor a rendszer kéri, fogadja el a NuGet-csomag telepítését és a licencmegállapodást.

Remek! A beállítás befejeztével nekiláthat a kód írásának. A [GitHubon](https://github.com/Azure-Samples/documentdb-dotnet-core-getting-started) megtalálhatja az oktatóanyagban szereplő kódprojekt befejezett változatát.

## <a id="Connect"></a>3. lépés: Csatlakozás egy Azure Cosmos DB-fiókhoz
Először adja hozzá az alábbi hivatkozásokat a C# alkalmazás elejéhez a Program.cs fájlban:

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

> [!IMPORTANT]
> Az oktatóanyag befejezéséhez mindenképpen adja hozzá az alábbi függőségeket.

Most adja hozzá ezt a két állandót és az *ügyfél* változót a *Program* nyilvános osztály alatt.

```csharp
class Program
{
    // ADD THIS PART TO YOUR CODE
    private const string EndpointUri = "<your endpoint URI>";
    private const string PrimaryKey = "<your key>";
    private DocumentClient client;
```

Ezután látogasson el az [Azure Portalra](https://portal.azure.com) az URI és az elsődleges kulcs beszerzéséhez. Az Azure Cosmos DB URI és az elsődleges kulcs ahhoz szükséges, hogy az alkalmazás tudja, hova kell csatlakoznia, az Azure Cosmos DB pedig megbízzon az alkalmazás által létesített kapcsolatban.

Az Azure Portalon lépjen az Azure Cosmos DB-fiókra, majd kattintson a **Kulcsok** elemre.

Másolja ki az URI-t a portálról, és illessze be a program.cs fájl `<your endpoint URI>` elemébe. Ezután másolja ki a PRIMARY KEY kulcsot a portálról, és illessze be a `<your key>` elembe. Ha az Azure Cosmos DB Emulatort használja, használjon `https://localhost:8081` értéket végpontként, valamint a jól definiált engedélyezési kulcsot a [Fejlesztés az Azure Cosmos DB Emulator használatával](local-emulator.md) című részből. Mindenképp távolítsa el a < és a > jelet, azonban az idézőjeleket hagyja meg a végpont és a kulcs körül.

![Képernyőkép a NoSQL-oktatóanyagban a C# konzolalkalmazás létrehozásához használt Azure Portalról. Megjelenít egy Azure Cosmos DB-fiókot, amelyen az ACTIVE központ, az Azure Cosmos DB-fiók oldalán lévő KEYS gomb, valamint a Kulcsok oldalon lévő URI, PRIMARY KEY és SECONDARY KEY értékek vannak kiemelve][keys]

Először létrehozunk egy új **DocumentClient** példányt az első lépések alkalmazáshoz.

A **Fő** metódus alatt adja hozzá a **GetStartedDemo** elnevezésű új aszinkron feladatot, amely létrehozza nekünk az új **DocumentClient** példányt.

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

Adja hozzá a következő kódot az aszinkron feladat **Fő** metódusból való futtatásához. A **Fő** metódus észleli a kivételeket, és a konzolba írja azokat.

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

Nyomja meg a **DocumentDBGettingStarted** gombot az alkalmazás létrehozásához és futtatásához.

Gratulálunk! Sikeresen csatlakozott egy Azure Cosmos DB-fiókhoz. Most vessünk egy pillantást az Azure Cosmos DB-erőforrások használatára.  

## <a name="step-4-create-a-database"></a>4. lépés: Adatbázis létrehozása
Mielőtt hozzáadja a kódot az adatbázis létrehozásához, adjon hozzá egy segédmetódust a konzolba való íráshoz.

Másolja, majd illessze be a **WriteToConsoleAndPromptToContinue** metódust a **GetStartedDemo** metódus alá.

```csharp
// ADD THIS PART TO YOUR CODE
private void WriteToConsoleAndPromptToContinue(string format, params object[] args)
{
        Console.WriteLine(format, args);
        Console.WriteLine("Press any key to continue ...");
        Console.ReadKey();
}
```

Az Azure Cosmos DB-[adatbázis](sql-api-resources.md#databases) a **DocumentClient** osztály [CreateDatabaseAsync](https://msdn.microsoft.com/library/microsoft.azure.documents.client.documentclient.createdatabaseasync.aspx) metódusának használatával hozható létre. Az adatbázis a JSON-dokumentumtároló gyűjtemények között particionált logikai tárolója.

Másolja, majd illessze be az alábbi kódot a **GetStartedDemo** metódusba az ügyfél létrehozása alatt. Ezzel létrehoz egy *FamilyDB* nevű adatbázist.

```csharp
private async Task GetStartedDemo()
{
    this.client = new DocumentClient(new Uri(EndpointUri), PrimaryKey);

    // ADD THIS PART TO YOUR CODE
    await this.client.CreateDatabaseIfNotExistsAsync(new Database { Id = "FamilyDB_oa" });
```

Nyomja meg a **DocumentDBGettingStarted** gombot az alkalmazás futtatásához.

Gratulálunk! Sikeresen létrehozott egy Azure Cosmos DB-adatbázist.  

## <a id="CreateColl"></a>5. lépés: Gyűjtemény létrehozása
> [!WARNING]
> A **CreateDocumentCollectionAsync** létrehoz egy fenntartott adatátviteli sebességgel rendelkező új gyűjteményt, amely költségeket von maga után. További részletekért látogasson el az [árképzést ismertető oldalra](https://azure.microsoft.com/pricing/details/cosmos-db/).

Egy [gyűjtemény](sql-api-resources.md#collections) a **DocumentClient** osztály [CreateDocumentCollectionAsync](https://msdn.microsoft.com/library/microsoft.azure.documents.client.documentclient.createdocumentcollectionasync.aspx) metódusának használatával hozható létre. A gyűjtemény egy JSON-dokumentumokat és a kapcsolódó JavaScript-alkalmazáslogikát tartalmazó tároló.

Másolja, majd illessze be az alábbi kódot a **GetStartedDemo** metódusba az adatbázis létrehozása alatt. Ezzel létrehoz egy *FamilyCollection_oa* nevű dokumentumgyűjteményt.

```csharp
    this.client = new DocumentClient(new Uri(EndpointUri), PrimaryKey);

    await this.client.CreateDatabaseIfNotExistsAsync("FamilyDB_oa");

    // ADD THIS PART TO YOUR CODE
    await this.client.CreateDocumentCollectionIfNotExistsAsync(UriFactory.CreateDatabaseUri("FamilyDB_oa"), new DocumentCollection { Id = "FamilyCollection_oa" });
```

Nyomja meg a **DocumentDBGettingStarted** gombot az alkalmazás futtatásához.

Gratulálunk! Sikeresen létrehozott egy Azure Cosmos DB-dokumentumgyűjteményt.  

## <a id="CreateDoc"></a>6. lépés: JSON-dokumentumok létrehozása
A [dokumentumok](sql-api-resources.md#documents) a **DocumentClient** osztály [CreateDocumentAsync](https://msdn.microsoft.com/library/microsoft.azure.documents.client.documentclient.createdocumentasync.aspx) metódusának használatával hozhatók létre. A dokumentumok a felhasználó által megadott (tetszőleges) JSON-tartalmak. Most már beilleszthetünk egy vagy több dokumentumot. Ha van olyan adat, amelyet szeretne az adatbázisban tárolni, használhatja az Azure Cosmos DB [adatmigrálási eszközét](import-data.md).

Először létre kell hozni egy **Család** osztályt, amely ebben a mintában az Azure Cosmos DB-ben tárolt objektumokat képviseli. Létrehozunk még egy **Szülő**, **Gyermek**, **Háziállat** és **Cím** alosztályt is a **Család** osztályban való használatra. Ne feledje, hogy a dokumentumoknak rendelkezniük kell egy **Azonosító** tulajdonsággal, amely a JSON-fájlban **id**-ként van szerializálva. Az osztályok létrehozásához adja hozzá az alábbi belső alosztályokat a **GetStartedDemo** metódus után.

Másolja, majd illessze be a **Család**, **Szülő**, **Gyermek**, **Háziállat** és **Cím** osztályokat a **WriteToConsoleAndPromptToContinue** metódus alá.

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

Ezután szúrjon be két dokumentumot, egyet az Andersen családhoz, egyet pedig a Wakefield családhoz.

Másolja, majd illessze be a `// ADD THIS PART TO YOUR CODE` után álló kódot a **GetStartedDemo** metódusba, a dokumentumgyűjtemény létrehozása alatt.

```csharp
await this.CreateDatabaseIfNotExistsAsync("FamilyDB_oa");

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

Nyomja meg a **DocumentDBGettingStarted** gombot az alkalmazás futtatásához.

Gratulálunk! Sikeresen létrehozott két Azure Cosmos DB-dokumentumot.  

![A diagram a NoSQL-oktatóanyagban a C# konzolalkalmazás létrehozásához használt fiók, online adatbázis, gyűjtemény és dokumentumok hierarchikus kapcsolatát ábrázolja.](./media/sql-api-dotnetcore-get-started/nosql-tutorial-account-database.png)

## <a id="Query"></a>7. lépés: Az Azure Cosmos DB-erőforrások lekérdezése
Az Azure Cosmos DB támogatja az egyes gyűjteményekben tárolt JSON-dokumentumokon végzett [részletes lekérdezéseket](sql-api-sql-query.md).  Az alábbi kódminta több olyan lekérdezést mutat be – az Azure Cosmos DB SQL-szintaxis és a LINQ használatával egyaránt – amelyeket az előző lépésben beszúrt dokumentumokon futtathatunk.

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

Nyomja meg a **DocumentDBGettingStarted** gombot az alkalmazás futtatásához.

Gratulálunk! Sikeres lekérdezést végzett egy Azure Cosmos DB-gyűjteményen.

Az alábbi diagram bemutatja, hogyan indít hívást az Azure Cosmos DB SQL-lekérdezési szintaxisa a létrehozott gyűjteményre. Ugyanez a logika vonatkozik a LINQ-lekérdezésekre is.

![A NoSQL-oktatóanyagban a C# konzolalkalmazás létrehozásához használt lekérdezés hatókörét és jelentését ábrázoló diagram.](./media/sql-api-dotnetcore-get-started/nosql-tutorial-collection-documents.png)

A [FROM](sql-api-sql-query.md#FromClause) kulcsszó kihagyható a lekérdezésből, mivel az Azure Cosmos DB-lekérdezések hatóköre eleve egyetlen gyűjtemény. Ezért a „FROM Families f” lecserélhető a „FROM root r” vagy bármilyen tetszőleges változónévre. Az Azure Cosmos DB úgy tekinti, hogy a Families, a root vagy a választott változónév alapértelmezés szerint az aktuális gyűjteményre hivatkozik.

## <a id="ReplaceDocument"></a>8. lépés: JSON-dokumentumok cseréje
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

Nyomja meg a **DocumentDBGettingStarted** gombot az alkalmazás futtatásához.

Gratulálunk! Sikeresen lecserélt egy Azure Cosmos DB-dokumentumot.

## <a id="DeleteDocument"></a>9. lépés: JSON-dokumentumok törlése
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

Nyomja meg a **DocumentDBGettingStarted** gombot az alkalmazás futtatásához.

Gratulálunk! Sikeresen törölt egy Azure Cosmos DB-dokumentumot.

## <a id="DeleteDatabase"></a>10. lépés: Az adatbázis törlése
A létrehozott adatbázis törlésével az adatbázis és az összes gyermekerőforrás (gyűjtemények, dokumentumok stb.) is törlődik.

Másolja, majd illessze be az alábbi kódot a **GetStartedDemo** metódusba a dokumentum törlése alá az adatbázis és az összes gyermek-erőforrás törléséhez.

```csharp
this.ExecuteSimpleQuery("FamilyDB_oa", "FamilyCollection_oa");

await this.DeleteFamilyDocument("FamilyDB_oa", "FamilyCollection_oa", "Andersen.1");

// ADD THIS PART TO CODE
// Clean up/delete the database
await this.client.DeleteDatabaseAsync(UriFactory.CreateDatabaseUri("FamilyDB_oa"));
```

Nyomja meg a **DocumentDBGettingStarted** gombot az alkalmazás futtatásához.

Gratulálunk! Sikeresen törölt egy Azure Cosmos DB-adatbázist.

## <a id="Run"></a>11. lépés: Futtassa a teljes C# konzolalkalmazást!
A Visual Studióban nyomja meg a **DocumentDBGettingStarted** gombot az alkalmazás hibakeresési módban történő létrehozásához.

A konzolablakban meg kell jelennie az Első lépések alkalmazás kimenetének. A kimenet megjeleníti a hozzáadott lekérdezések eredményeit, amelynek meg kell egyeznie az alábbi mintaszöveggel.

```
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

Gratulálunk! Elvégezte az oktatóanyagot, és egy működőképes C# konzolalkalmazással rendelkezik!

## <a id="GetSolution"></a> Az oktatóanyagban szereplő teljes megoldás beszerzése
A cikkben szereplő összes mintát tartalmazó GetStarted-megoldás összeállításához az alábbiakra lesz szüksége:

* Aktív Azure-fiók. Ha még nincs fiókja, létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/).
* Egy [Azure Cosmos DB-fiók][create-sql-api-dotnet.md#create-account].
* A GitHubon elérhető [GetStarted](https://github.com/Azure-Samples/documentdb-dotnet-core-getting-started) megoldás.

Ahhoz, hogy visszaállítsa az Azure Cosmos DB SQL API .NET Core SDK-ra mutató hivatkozásokat a Visual Studióban, kattintson a jobb gombbal a **GetStarted** megoldásra a Solution Explorer (Megoldáskezelő) felületén, majd kattintson az **Enable NuGet Package Restore** (NuGet-csomagok visszaállításának engedélyezése) lehetőségre. Ezután a Program.cs fájlban frissítse az EndpointUrl és az AuthorizationKey értéket a [Csatlakozás egy Azure Cosmos DB-fiókhoz](#Connect) részben leírtak szerint.

## <a name="next-steps"></a>További lépések
* Összetettebb ASP.NET MVC-oktatóanyagot szeretne? Tekintse meg a következőt: [ASP.NET MVC-oktatóanyag: webalkalmazás fejlesztése az Azure Cosmos DB szolgáltatással](sql-api-dotnet-application.md).
* Xamarin iOS-, Android- vagy Forms-alkalmazást szeretne fejleszteni az Azure Cosmos DB SQL API .NET Core SDK használatával? Tekintse meg a következőt: [Mobilalkalmazások létrehozása a Xamarin és az Azure Cosmos DB használatával](mobile-apps-with-xamarin.md).
* Méret- és teljesítménytesztelést szeretne végezni az Azure Cosmos DB használatával? Tekintse meg az [Azure Cosmos DB használatával történő teljesítmény- és mérettesztelést](performance-testing.md) ismertető cikket.
* Ismerje meg, hogyan [monitorozhatja az Azure Cosmos DB-kérelmeket, -használatot és -tárolást](monitor-accounts.md).
* Futtasson lekérdezéseket a minta-adatkészleteken a [Query Playground](https://www.documentdb.com/sql/demo) (Tesztlekérdezések) használatával.
* A programozási modellel kapcsolatos további tudnivalókat lásd: [Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/).

[create-sql-api-dotnet.md#create-account]: create-sql-api-dotnet.md#create-account
[keys]: media/sql-api-dotnetcore-get-started/nosql-tutorial-keys.png
