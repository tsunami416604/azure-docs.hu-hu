---
title: 'Az Azure Cosmos DB: Az SQL API kezdeti lépéseket ismertető oktatóanyag'
description: Ez az oktatóanyag létrehoz egy online adatbázist és egy C# konzolalkalmazást az SQL API használatával.
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: dotnet
ms.topic: tutorial
ms.date: 08/16/2017
ms.author: sngun
ms.openlocfilehash: d73638e0c02af5dc07e80084dd2ce5a7e090ecd2
ms.sourcegitcommit: 8330a262abaddaafd4acb04016b68486fba5835b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/04/2019
ms.locfileid: "54043447"
---
# <a name="azure-cosmos-db-sql-api-getting-started-tutorial"></a>Az Azure Cosmos DB: Az SQL API kezdeti lépéseket ismertető oktatóanyag

> [!div class="op_single_selector"]
> * [.NET](sql-api-get-started.md)
> * [.NET (előzetes verzió)](sql-api-dotnet-get-started-preview.md)
> * [.NET Core](sql-api-dotnetcore-get-started.md)
> * [A .NET core (előzetes verzió)](sql-api-dotnet-core-get-started-preview.md)
> * [Java](sql-api-java-get-started.md)
> * [Aszinkron Java](sql-api-async-java-get-started.md)
> * [Node.js](sql-api-nodejs-get-started.md)
> 

Üdvözöljük az Azure Cosmos DB: Az SQL API kezdeti lépéseit ismertető oktatóanyagban! Az oktatóanyag lépéseinek követésével egy olyan konzolalkalmazást készít, amely Azure Cosmos DB-erőforrásokat hoz létre és kérdez le.

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

Nincs elég ideje? Ne aggódjon! A teljes megoldás elérhető a [GitHubon](https://github.com/Azure-Samples/documentdb-dotnet-getting-started). A gyors utasításokért ugorjon [A teljes NoSQL-oktatóanyag megoldás beszerzése szakaszra](#GetSolution).

Most pedig lássunk neki!

## <a name="prerequisites"></a>Előfeltételek

* Aktív Azure-fiók. Ha még nincs fiókja, létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/). 

  [!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

* [!INCLUDE [cosmos-db-emulator-vs](../../includes/cosmos-db-emulator-vs.md)]

## <a name="step-1-create-an-azure-cosmos-db-account"></a>1. lépés: Azure Cosmos DB-fiók létrehozása
Hozzunk létre egy Azure Cosmos DB-fiókot. Ha van már olyan fiókja, amelyet használni szeretne, ugorjon előre a [Visual Studio megoldás beállítása](#SetupVS) című lépésre. Ha az Azure Cosmos DB Emulatort használja, kövesse az [Azure Cosmos DB Emulatornál](local-emulator.md) leírt lépéseket az emulátor telepítéséhez, majd ugorjon előre [A Visual Studio megoldás beállítása](#SetupVS) című lépésre.

[!INCLUDE [create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

## <a id="SetupVS"></a>2. lépés: A Visual Studio megoldás beállítása
1. Nyissa meg a **Visual Studio 2017-et** a számítógépén.
2. A **Fájl** menüben válassza az **Új**, majd a **Projekt** elemet.
3. Az **Új projekt** párbeszédpanelen válassza a **Sablonok** / **Visual C#** / **Konzolalkalmazás** elemet, nevezze el a projektet, majd kattintson az **OK** gombra.
   ![Képernyőfelvétel az Új projekt ablakról](./media/sql-api-get-started/nosql-tutorial-new-project-2.png)
4. A **Megoldáskezelőben** kattintson a jobb gombbal az új konzolalkalmazásra, amely a Visual Studio megoldás alatt található, majd kattintson a **NuGet-csomagok kezelése...** lehetőségre.
    
    ![A Projekt jobb gombos kattintással elérhető menüjének képernyőfelvétele](./media/sql-api-get-started/nosql-tutorial-manage-nuget-pacakges.png)
5. A **NuGet** lapon kattintson a **Browse** (Tallózás) elemre, majd írja be az **azure documentdb** kifejezést a keresőmezőbe.
6. A találatok között keresse meg a **Microsoft.Azure.DocumentDB** elemet, majd kattintson a **Telepítés** lehetőségre.
   Az Azure Cosmos DB SQL API ügyfélkódtárának csomagazonosítója a következő: [Microsoft Azure Cosmos DB Client Library](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/).
   ![Képernyőkép a NuGet menüről az Azure Cosmos DB ügyféloldali SDK megkereséséhez](./media/sql-api-get-started/nosql-tutorial-manage-nuget-pacakges-2.png)

    Ha a megoldás módosításainak áttekintéséről szóló üzenetet kap, kattintson az **OK** gombra. Ha a licenc elfogadásáról szóló üzenetet kap, kattintson az **Elfogadom** gombra.

Remek! Most, hogy befejeztük a beállítást, lássunk neki a kód megírásának! A [GitHubon](https://github.com/Azure-Samples/documentdb-dotnet-getting-started/blob/master/src/Program.cs) megtalálhatja az oktatóanyagban szereplő kódprojekt befejezett változatát.

## <a id="Connect"></a>3. lépés: Csatlakozás az Azure Cosmos DB-fiók
Először adja hozzá az alábbi hivatkozásokat a C# alkalmazás elejéhez a Program.cs fájlban:

    using System;
    using System.Linq;
    using System.Threading.Tasks;

    // ADD THIS PART TO YOUR CODE
    using System.Net;
    using Microsoft.Azure.Documents;
    using Microsoft.Azure.Documents.Client;
    using Newtonsoft.Json;

> [!IMPORTANT]
> Az oktatóanyag befejezéséhez mindenképpen adja hozzá az alábbi függőségeket.
> 
> 

Most adja hozzá ezt a két állandót és az *ügyfél* változót a *Program* nyilvános osztály alatt.

    public class Program
    {
        // ADD THIS PART TO YOUR CODE
        private const string EndpointUrl = "<your endpoint URL>";
        private const string PrimaryKey = "<your primary key>";
        private DocumentClient client;

Ezután lépjen vissza az [Azure Portalra](https://portal.azure.com) a végponti URL-cím és az elsődleges kulcs beszerzéséért. A végponti URL-cím és az elsődleges kulcs ahhoz szükséges, hogy az alkalmazás tudja, hova kell csatlakoznia, az Azure Cosmos DB pedig megbízzon az alkalmazás által létesített kapcsolatban.

Az Azure Portalon lépjen az Azure Cosmos DB-fiókra, majd kattintson a **Kulcsok** elemre.

Másolja ki az URI-t a portálról, és illessze be a program.cs fájl `<your endpoint URL>` elemébe. Ezután másolja ki a PRIMARY KEY kulcsot a portálról, és illessze be a `<your primary key>` elembe.

![Képernyőkép a NoSQL-oktatóanyagban a C# konzolalkalmazás létrehozásához használt Azure Portalról. Megjelenít egy Azure Cosmos DB-fiókot, amelyen az ACTIVE központ, az Azure Cosmos DB-fiók oldalán lévő KEYS gomb, valamint a Kulcsok oldalon lévő URI, PRIMARY KEY és SECONDARY KEY értékek vannak kiemelve][keys]

Ezt követően létrehozunk egy új **DocumentClient** példányt az alkalmazás elindításához.

A **Fő** metódus alatt adja hozzá a **GetStartedDemo** elnevezésű új aszinkron feladatot, amely létrehozza nekünk az új **DocumentClient** példányt.

    static void Main(string[] args)
    {
    }

    // ADD THIS PART TO YOUR CODE
    private async Task GetStartedDemo()
    {
        this.client = new DocumentClient(new Uri(EndpointUrl), PrimaryKey);
    }

Adja hozzá a következő kódot az aszinkron feladat **Fő** metódusból való futtatásához. A **Fő** metódus észleli a kivételeket, és a konzolba írja azokat.

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

Az alkalmazás futtatásához nyomja le az **F5** billentyűt. A konzolablak kimenete a következő üzenet megjelenítésével erősíti meg a kapcsolat létrejöttét: `End of demo, press any key to exit.`.  Ezután bezárhatja a konzolablakot. 

Gratulálunk! Sikeresen csatlakozott egy Azure Cosmos DB-fiókhoz. Most vessünk egy pillantást az Azure Cosmos DB-erőforrások használatára.  

## <a name="step-4-create-a-database"></a>4. lépés: Adatbázis létrehozása
Mielőtt hozzáadja a kódot az adatbázis létrehozásához, adjon hozzá egy segédmetódust a konzolba való íráshoz.

Másolja, majd illessze be a **WriteToConsoleAndPromptToContinue** metódust a **GetStartedDemo** metódus után.

    // ADD THIS PART TO YOUR CODE
    private void WriteToConsoleAndPromptToContinue(string format, params object[] args)
    {
            Console.WriteLine(format, args);
            Console.WriteLine("Press any key to continue ...");
            Console.ReadKey();
    }

Az Azure Cosmos DB-[adatbázis](databases-containers-items.md#azure-cosmos-databases) a **DocumentClient** osztály [CreateDatabaseIfNotExistAsync](https://msdn.microsoft.com/library/microsoft.azure.documents.client.documentclient.createdatabaseifnotexistsasync.aspx) metódusának használatával hozható létre. Az adatbázis a JSON-dokumentumtároló gyűjtemények között particionált logikai tárolója.

Az ügyfél létrehozása után másolja, majd illessze be az alábbi kódot a **GetStartedDemo** metódusba. Ezzel létrehoz egy *FamilyDB* elnevezésű adatbázist.

    private async Task GetStartedDemo()
    {
        this.client = new DocumentClient(new Uri(EndpointUrl), PrimaryKey);

        // ADD THIS PART TO YOUR CODE
        await this.client.CreateDatabaseIfNotExistsAsync(new Database { Id = "FamilyDB" });

Az alkalmazás futtatásához nyomja le az **F5** billentyűt.

Gratulálunk! Sikeresen létrehozott egy Azure Cosmos DB-adatbázist.  

## <a id="CreateColl"></a>5. lépés: Gyűjtemény létrehozása
> [!WARNING]
> A **CreateDocumentCollectionIfNotExistsAsync** létrehoz egy fenntartott adattovábbítási kapacitással rendelkező új gyűjteményt, amely költségeket von maga után. További részletekért látogasson el az [árképzést ismertető oldalra](https://azure.microsoft.com/pricing/details/cosmos-db/).
> 
> 

Egy gyűjtemény használatával hozható létre a [Createdocumentcollectionasync](https://msdn.microsoft.com/library/microsoft.azure.documents.client.documentclient.createdocumentcollectionifnotexistsasync.aspx) módszere a **DocumentClient** osztály. A gyűjtemény egy JSON-dokumentumokat és a kapcsolódó JavaScript-alkalmazáslogikát tartalmazó tároló.

Az adatbázis létrehozása után másolja, majd illessze be az alábbi kódot a **GetStartedDemo** metódusba. Ezzel létrehoz egy *FamilyCollection* elnevezésű dokumentumgyűjteményt.

        this.client = new DocumentClient(new Uri(EndpointUrl), PrimaryKey);

        await this.client.CreateDatabaseIfNotExistsAsync(new Database { Id = "FamilyDB" });

        // ADD THIS PART TO YOUR CODE
         await this.client.CreateDocumentCollectionIfNotExistsAsync(UriFactory.CreateDatabaseUri("FamilyDB"), new DocumentCollection { Id = "FamilyCollection" });

Az alkalmazás futtatásához nyomja le az **F5** billentyűt.

Gratulálunk! Sikeresen létrehozott egy Azure Cosmos DB-dokumentumgyűjteményt.  

## <a id="CreateDoc"></a>6. lépés: JSON-dokumentumok létrehozása
A dokumentum használatával hozható létre a [CreateDocumentAsync](https://msdn.microsoft.com/library/microsoft.azure.documents.client.documentclient.createdocumentasync.aspx) módszere a **DocumentClient** osztály. A dokumentumok a felhasználó által megadott (tetszőleges) JSON-tartalmak. Most már beilleszthetünk egy vagy több dokumentumot. Ha van olyan adat, amelyet szeretne az adatbázisban tárolni, az Azure Cosmos DB [adatmigrálási eszközével](import-data.md) adatokat importálhat az adatbázisba.

Először létre kell hozni egy **Család** osztályt, amely ebben a mintában az Azure Cosmos DB-ben tárolt objektumokat képviseli. Létrehozunk még egy **Szülő**, **Gyermek**, **Háziállat** és **Cím** alosztályt is a **Család** osztályban való használatra. Ne feledje, hogy a dokumentumoknak rendelkezniük kell egy **Azonosító** tulajdonsággal, amely a JSON-fájlban **id**-ként van szerializálva. Az osztályok létrehozásához adja hozzá az alábbi belső alosztályokat a **GetStartedDemo** metódus után.

Másolja, majd illessze be a **Család**, **Szülő**, **Gyermek**, **Háziállat** és **Cím** osztályokat a **WriteToConsoleAndPromptToContinue** metódus után.

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

Másolja, majd illessze be a **CreateFamilyDocumentIfNotExists** metódust az **Address** osztály alá.

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

Ezután szúrjon be két dokumentumot, egyet az Andersen családhoz, egyet pedig a Wakefield családhoz.

A dokumentumgyűjtemény létrehozása után másolja, majd illessze be az alábbi kódot a **GetStartedDemo** metódusba.

    await this.client.CreateDatabaseIfNotExistsAsync(new Database { Id = "FamilyDB" });
    
    await this.client.CreateDocumentCollectionIfNotExistsAsync(UriFactory.CreateDatabaseUri("FamilyDB"), new DocumentCollection { Id = "FamilyCollection" });


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

    await this.CreateFamilyDocumentIfNotExists("FamilyDB", "FamilyCollection", andersenFamily);

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

    await this.CreateFamilyDocumentIfNotExists("FamilyDB", "FamilyCollection", wakefieldFamily);

Az alkalmazás futtatásához nyomja le az **F5** billentyűt.

Gratulálunk! Sikeresen létrehozott két Azure Cosmos DB-dokumentumot.  

![A diagram a NoSQL-oktatóanyagban a C# konzolalkalmazás létrehozásához használt fiók, online adatbázis, gyűjtemény és dokumentumok hierarchikus kapcsolatát ábrázolja.](./media/sql-api-get-started/nosql-tutorial-account-database.png)

## <a id="Query"></a>7. lépés: Azure Cosmos DB-erőforrások lekérdezése
Az Azure Cosmos DB támogatja az egyes gyűjteményekben tárolt JSON-dokumentumokon végzett [részletes lekérdezéseket](how-to-sql-query.md).  Az alábbi kódminta több olyan lekérdezést mutat be – az Azure Cosmos DB SQL-szintaxis és a LINQ használatával egyaránt – amelyeket az előző lépésben beszúrt dokumentumokon futtathatunk.

Másolja, majd illessze be a **ExecuteSimpleQuery** metódust a **CreateFamilyDocumentIfNotExists** metódus után.

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

A második dokumentum létrehozása után másolja, majd illessze be az alábbi kódot a **GetStartedDemo** metódusba.

    await this.CreateFamilyDocumentIfNotExists("FamilyDB", "FamilyCollection", wakefieldFamily);

    // ADD THIS PART TO YOUR CODE
    this.ExecuteSimpleQuery("FamilyDB", "FamilyCollection");

Az alkalmazás futtatásához nyomja le az **F5** billentyűt.

Gratulálunk! Sikeres lekérdezést végzett egy Azure Cosmos DB-gyűjteményen.

Az alábbi diagram bemutatja, hogyan indít hívást az Azure Cosmos DB SQL-lekérdezési szintaxisa a létrehozott gyűjteményre. Ugyanez a logika vonatkozik a LINQ-lekérdezésekre is.

![A NoSQL-oktatóanyagban a C# konzolalkalmazás létrehozásához használt lekérdezés hatókörét és jelentését ábrázoló diagram.](./media/sql-api-get-started/nosql-tutorial-collection-documents.png)

A [FROM](how-to-sql-query.md#FromClause) kulcsszó kihagyható a lekérdezésből, mivel az Azure Cosmos DB-lekérdezések hatóköre eleve egyetlen gyűjtemény. Ezért a „FROM Families f” lecserélhető a „FROM root r” vagy bármilyen tetszőleges változónévre. Az Azure Cosmos DB úgy tekinti, hogy a Families, a root vagy a választott változónév alapértelmezés szerint az aktuális gyűjteményre hivatkozik.

## <a id="ReplaceDocument"></a>8. lépés: Cserélje le a JSON-dokumentumok
Az Azure Cosmos DB támogatja a JSON-dokumentumok cseréjét.  

Másolja, majd illessze be a **ReplaceFamilyDocument** metódust az **ExecuteSimpleQuery** metódus után.

    // ADD THIS PART TO YOUR CODE
    private async Task ReplaceFamilyDocument(string databaseName, string collectionName, string familyName, Family updatedFamily)
    {
         await this.client.ReplaceDocumentAsync(UriFactory.CreateDocumentUri(databaseName, collectionName, familyName), updatedFamily);
         this.WriteToConsoleAndPromptToContinue("Replaced Family {0}", familyName);
    }

A lekérdezés végrehajtása után másolja, majd illessze be az alábbi kódot a **GetStartedDemo** metódusba. Ezáltal a dokumentum cseréje után ismét ugyanaz a lekérdezés fog lefutni a megváltozott dokumentum megtekintéséhez.

    await this.CreateFamilyDocumentIfNotExists("FamilyDB", "FamilyCollection", wakefieldFamily);

    this.ExecuteSimpleQuery("FamilyDB", "FamilyCollection");

    // ADD THIS PART TO YOUR CODE
    // Update the Grade of the Andersen Family child
    andersenFamily.Children[0].Grade = 6;

    await this.ReplaceFamilyDocument("FamilyDB", "FamilyCollection", "Andersen.1", andersenFamily);

    this.ExecuteSimpleQuery("FamilyDB", "FamilyCollection");

Az alkalmazás futtatásához nyomja le az **F5** billentyűt.

Gratulálunk! Sikeresen lecserélt egy Azure Cosmos DB-dokumentumot.

## <a id="DeleteDocument"></a>9. lépés: JSON-dokumentumok törlése
Az Azure Cosmos DB támogatja a JSON-dokumentumok törlését.  

Másolja, majd illessze be a **DeleteFamilyDocument** metódust a **ReplaceFamilyDocument** metódus után.

    // ADD THIS PART TO YOUR CODE
    private async Task DeleteFamilyDocument(string databaseName, string collectionName, string documentName)
    {
         await this.client.DeleteDocumentAsync(UriFactory.CreateDocumentUri(databaseName, collectionName, documentName));
         Console.WriteLine("Deleted Family {0}", documentName);
    }

A második lekérdezés végrehajtása után másolja, majd illessze be az alábbi kódot a **GetStartedDemo** metódusba.

    await this.ReplaceFamilyDocument("FamilyDB", "FamilyCollection", "Andersen.1", andersenFamily);
    
    this.ExecuteSimpleQuery("FamilyDB", "FamilyCollection");
    
    // ADD THIS PART TO CODE
    await this.DeleteFamilyDocument("FamilyDB", "FamilyCollection", "Andersen.1");

Az alkalmazás futtatásához nyomja le az **F5** billentyűt.

Gratulálunk! Sikeresen törölt egy Azure Cosmos DB-dokumentumot.

## <a id="DeleteDatabase"></a>10. lépés: Az adatbázis törlése
A létrehozott adatbázis törlésével az adatbázis és az összes gyermekerőforrás (gyűjtemények, dokumentumok stb.) is törlődik.

Másolja, majd illessze be az alábbi kódot a **GetStartedDemo** metódusba a dokumentum törlése után az adatbázis és az összes gyermek-erőforrás törléséhez.

    this.ExecuteSimpleQuery("FamilyDB", "FamilyCollection");

    await this.DeleteFamilyDocument("FamilyDB", "FamilyCollection", "Andersen.1");

    // ADD THIS PART TO CODE
    // Clean up/delete the database
    await this.client.DeleteDatabaseAsync(UriFactory.CreateDatabaseUri("FamilyDB"));

Az alkalmazás futtatásához nyomja le az **F5** billentyűt.

Gratulálunk! Sikeresen törölt egy Azure Cosmos DB-adatbázist.

## <a id="Run"></a>11. lépés: Futtassa a C# Konzolalkalmazás minden egy helyen!
Nyomja le az F5 billentyűt a Visual Studióban az alkalmazás hibakeresési módban történő összeállításához.

Egy konzolablakban meg kell jelennie az Első lépések alkalmazás kimenetének. A kimenet megjeleníti a hozzáadott lekérdezések eredményeit, amelynek meg kell egyeznie az alábbi mintaszöveggel.

    Created FamilyDB
    Press any key to continue ...
    Created FamilyCollection
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

Gratulálunk! Elvégezte az oktatóanyagot, és egy működőképes C# konzolalkalmazással rendelkezik!

## <a id="GetSolution"></a> Az oktatóanyagban szereplő teljes megoldás beszerzése
Ha nincs ideje az oktatóanyag lépéseinek végrehajtására, vagy csak szeretné letölteni a mintakódokat, a [GitHubon](https://github.com/Azure-Samples/documentdb-dotnet-getting-started) beszerezhetőek. 

A GetStarted-megoldás létrehozásához a következőkre lesz szüksége:

* Aktív Azure-fiók. Ha még nincs fiókja, létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/).
* Egy [Azure Cosmos DB-fiók][cosmos-db-create-account].
* A GitHubon elérhető [GetStarted](https://github.com/Azure-Samples/documentdb-dotnet-getting-started) megoldás.

Ahhoz, hogy visszaállítsa az Azure Cosmos DB .NET SDK-ra mutató hivatkozásokat a Visual Studióban, kattintson a jobb gombbal a **GetStarted** megoldásra a Solution Explorer (Megoldáskezelő) felületén, majd kattintson az **Enable NuGet Package Restore** (NuGet-csomagok visszaállításának engedélyezése) lehetőségre. Ezután az App.config fájlban frissítse az EndpointUrl és az AuthorizationKey értékeket a [Csatlakozás Azure Cosmos DB-fiókhoz](#Connect) című részben leírtak szerint.

Ennyi az egész! Építse ki, és máris jó úton jár!


## <a name="next-steps"></a>További lépések
* Összetettebb ASP.NET MVC-oktatóanyagot szeretne? Lásd: [ASP.NET MVC oktatóprogram: Webalkalmazások fejlesztése az Azure Cosmos DB](sql-api-dotnet-application.md).
* Méret- és teljesítménytesztelést szeretne végezni az Azure Cosmos DB használatával? Tekintse meg az [Azure Cosmos DB használatával történő teljesítmény- és mérettesztelést](performance-testing.md) ismertető cikket.
* Ismerje meg, hogyan [monitorozhatja az Azure Cosmos DB-kérelmeket, -használatot és -tárolást](monitor-accounts.md).
* Futtasson lekérdezéseket a minta-adatkészleteken a [Query Playground](https://www.documentdb.com/sql/demo) (Tesztlekérdezések) használatával.
* További információ az Azure Cosmos DB-ről: [Üdvözli az Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/introduction).

[keys]: media/sql-api-get-started/nosql-tutorial-keys.png
[cosmos-db-create-account]: create-sql-api-dotnet.md#create-account
