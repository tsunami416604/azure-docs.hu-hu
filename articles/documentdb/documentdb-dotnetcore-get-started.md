---
title: "NoSQL-oktatóanyag: DocumentDB .NET Core SDK | Microsoft Docs"
description: "NoSQL-oktatóanyag, amely létrehoz egy online adatbázist és egy C# konzolalkalmazást a DocumentDB .NET SDK Core használatával. A DocumentDB egy NoSQL-alapú adatbázis a JSON formátumhoz."
services: documentdb
documentationcenter: .net
author: arramac
manager: jhubbard
editor: 
ms.assetid: 9f93e276-9936-4efb-a534-a9889fa7c7d2
ms.service: documentdb
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: hero-article
ms.date: 02/11/2017
ms.author: arramac
translationtype: Human Translation
ms.sourcegitcommit: fba82c5c826da7d1912814b61c5065ca7f726011
ms.openlocfilehash: e31be1a97b9b3d7718e4e6f58f22a2c6f5550beb
ms.lasthandoff: 02/23/2017


---
# <a name="nosql-tutorial-build-a-documentdb-c-console-application-on-net-core"></a>NoSQL-oktatóanyag: DocumentDB C# konzolalkalmazás létrehozása .NET Core-on
> [!div class="op_single_selector"]
> * [.NET](documentdb-get-started.md)
> * [.NET Core](documentdb-dotnetcore-get-started.md)
> * [Java](documentdb-java-get-started.md)
> * [Node.js](documentdb-nodejs-get-started.md)
> * [C++](documentdb-cpp-get-started.md)
>  
> 

Köszönti az Azure DocumentDB .NET Core SDK-hoz készült NoSQL-oktatóanyag! Az oktatóanyag lépéseinek követésével egy olyan konzolalkalmazással fog rendelkezni, amely DocumentDB erőforrásokat hoz létre és kérdez le.

Az oktatóanyag a következőket ismerteti:

* DocumentDB-fiók létrehozása, és az ahhoz való csatlakozás
* A Visual Studio megoldás konfigurálása
* Online adatbázis létrehozása
* Gyűjtemény létrehozása
* JSON-dokumentumok létrehozása
* A gyűjtemény lekérdezése
* Dokumentum cseréje
* Dokumentum törlése
* Adatbázis törlése

Nincs elég ideje? Ne aggódjon! A teljes megoldás elérhető a [GitHubon](https://github.com/Azure-Samples/documentdb-dotnet-core-getting-started). A gyors utasításokért ugorjon [A teljes megoldás beszerzése szakaszra](#GetSolution).

Utána a lap tetején és alján található szavazógomb használatával küldjön visszajelzést felénk. Ha szeretne közvetlenül kapcsolatba lépni velünk, a hozzászólásaiban tüntesse fel az e-mail-címét.

> [!NOTE]
> Az ebben az oktatóanyagban használt DocumentDB .NET Core SDK-k egyelőre nem kompatibilisek az Univerzális Windows-platformra (UWP) épülő alkalmazásokkal. A .NET Core SDK UWP-alkalmazásokat is támogató előzetes verziójáért küldjön e-mailt a következő címre: [askdocdb@microsoft.com](mailto:askdocdb@microsoft.com).

Most pedig lássunk neki!

## <a name="prerequisites"></a>Előfeltételek
Győződjön meg róla, hogy rendelkezik az alábbiakkal:

* Aktív Azure-fiók. Ha még nincs fiókja, létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/). 
    * Vagy használhatja az [Azure DocumentDB Emulatort](documentdb-nosql-local-emulator.md) ebben az oktatóanyagban.
* [Visual Studio 2015 Update 3](https://go.microsoft.com/fwlink/?LinkId=691129) és [.NET Core 1.0.1 - VS 2015 Tooling Preview 2](https://go.microsoft.com/fwlink/?LinkID=827546)
    * Ha MacOS vagy Linux rendszeren dolgozik, a parancssorból is fejleszthet .NET Core alkalmazásokat, ha telepíti a [.NET Core SDK-t](https://www.microsoft.com/net/core#macos) a választott platformra. 
    * Ha Windows rendszeren dolgozik, a parancssorból is fejleszthet .NET Core alkalmazásokat, ha telepíti a [.NET Core SDK-t](https://www.microsoft.com/net/core#windows). 
    * Használhat saját szerkesztőt is, vagy letöltheti az ingyenes [Visual Studio Code](https://code.visualstudio.com/) alkalmazást, amely Windows, Linux és MacOS rendszeren egyaránt működik. 

## <a name="step-1-create-a-documentdb-account"></a>1. lépés: DocumentDB-fiók létrehozása
Hozzon létre egy DocumentDB-fiókot. Ha van már olyan fiókja, amelyet használni szeretne, ugorjon előre a [Visual Studio megoldás beállítása](#SetupVS) című lépésre. Ha a DocumentDB Emulatort használja, kövesse az [Azure DocumentDB Emulatornál](documentdb-nosql-local-emulator.md) leírt lépéseket az emulátor telepítéséhez, majd ugorjon előre [A Visual Studio megoldás beállítása](#SetupVS) című lépésre.

[!INCLUDE [documentdb-create-dbaccount](../../includes/documentdb-create-dbaccount.md)]

## <a name="a-idsetupvsastep-2-setup-your-visual-studio-solution"></a><a id="SetupVS"></a>2. lépés: A Visual Studio megoldás beállítása
1. Nyissa meg a **Visual Studio 2015-öt** a számítógépén.
2. A **Fájl** menüben válassza az **Új**, majd a **Projekt** elemet.
3. Az **Új projekt** párbeszédpanelen válassza a **Sablonok** / **Visual C#** / **.NET Core**/**Konzolalkalmazás (.NET Core)** elemet, nevezze el a projektet, majd kattintson az **OK** gombra.
   ![Képernyőfelvétel az Új projekt ablakról](./media/documentdb-dotnetcore-get-started/nosql-tutorial-new-project-2.png)
4. A **Megoldáskezelőben** kattintson a jobb gombbal az új konzolalkalmazásra, amely a Visual Studio megoldás alatt található.
5. Ezután maradjon a menüben, és kattintson a következőre: **NuGet-csomagok kezelése...**
   ![Képernyőfelvétel a projekt helyi menüjéről](./media/documentdb-dotnetcore-get-started/nosql-tutorial-manage-nuget-pacakges.png)
6. A **NuGet** lapon kattintson a **Tallózás** elemre, majd írja be az **azure documentdb** kifejezést a keresőmezőbe.
7. A találatok között keresse meg a **Microsoft.Azure.DocumentDB.Core** elemet, majd kattintson a **Telepítés** lehetőségre.
   A .NET Core DocumentDB ügyfélkódtárának csomagazonosítója a következő: [Microsoft.Azure.DocumentDB.Core](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB.Core). Ha olyan .NET-keretrendszer verziót céloz meg (például net461), amelyet ez a .NET Core Nuget csomag nem támogat, akkor használja a [Microsoft.Azure.DocumentDB](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB)-t, amely a .NET keretrendszer 4.5-től kezdődően minden .NET-keretrendszer verziót támogat.

Remek! Most, hogy befejeztük a beállítást, lássunk neki a kód megírásának! A [GitHubon](https://github.com/Azure-Samples/documentdb-dotnet-core-getting-started) megtalálhatja az oktatóanyagban szereplő kódprojekt befejezett változatát.

## <a name="a-idconnectastep-3-connect-to-a-documentdb-account"></a><a id="Connect"></a>3. lépés: Csatlakozás DocumentDB-fiókhoz
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
> A NoSQL-oktatóanyag befejezéséhez mindenképpen adja hozzá az alábbi függőségeket.
> 
> 

Most adja hozzá ezt a két állandót és az *ügyfél* változót a *Program* nyilvános osztály alatt.

    public class Program
    {
        // ADD THIS PART TO YOUR CODE
        private const string EndpointUri = "<your endpoint URI>";
        private const string PrimaryKey = "<your key>";
        private DocumentClient client;

Ezután látogasson el az [Azure-portálra](https://portal.azure.com) az URI és az elsődleges kulcs beszerzéséért. A DocumentDB URI és az elsődleges kulcs azért szükséges, hogy az alkalmazás tudja, hova kell csatlakoznia, a DocumentDB pedig megbízzon az alkalmazás által létesített kapcsolatban.

Az Azure portálon lépjen a DocumentDB-fiókra, majd kattintson a **Kulcsok** elemre.

Másolja ki az URI-t a portálról, és illessze be a program.cs fájl `<your endpoint URI>` elemébe. Ezután másolja ki a PRIMARY KEY kulcsot a portálról, és illessze be a `<your key>` elembe. Ha az Azure DocumentDB Emulatort használja, használjon `https://localhost:8081` értéket végpontként, valamint a jól definiált engedélyezési kulcsot a [Fejlesztés a DocumentDB Emulator használatával](documentdb-nosql-local-emulator.md) című részből.

![Képernyőfelvétel a NoSQL-oktatóanyagban a C# konzolalkalmazás létrehozásához használt Azure-portálról. Megjelenít egy DocumentDB-fiókot, amelyen az ACTIVE központ, a DocumentDB-fiók panelén lévő KEYS gomb, valamint a Kulcsok panelen lévő URI, PRIMARY KEY és SECONDARY KEY értékek vannak kiemelve.][keys]

Először létrehozunk egy új **DocumentClient** példányt az első lépések alkalmazáshoz.

A **Fő** metódus alatt adja hozzá a **GetStartedDemo** elnevezésű új aszinkron feladatot, amely létrehozza nekünk az új **DocumentClient** példányt.

    static void Main(string[] args)
    {
    }

    // ADD THIS PART TO YOUR CODE
    private async Task GetStartedDemo()
    {
        this.client = new DocumentClient(new Uri(EndpointUri), PrimaryKey);
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

Az alkalmazás futtatásához nyomja le az **F5** billentyűt.

Gratulálunk! Sikeresen csatlakozott egy DocumentDB-fiókhoz. Most vessünk egy pillantást a DocumentDB-erőforrások használatára.  

## <a name="step-4-create-a-database"></a>4. lépés: Adatbázis létrehozása
Mielőtt hozzáadja a kódot az adatbázis létrehozásához, adjon hozzá egy segédmetódust a konzolba való íráshoz.

Másolja, majd illessze be a **WriteToConsoleAndPromptToContinue** metódust a **GetStartedDemo** metódus alá.

    // ADD THIS PART TO YOUR CODE
    private void WriteToConsoleAndPromptToContinue(string format, params object[] args)
    {
            Console.WriteLine(format, args);
            Console.WriteLine("Press any key to continue ...");
            Console.ReadKey();
    }

A DocumentDB [adatbázis](documentdb-resources.md#databases) a **DocumentClient** osztály [CreateDatabaseAsync](https://msdn.microsoft.com/library/microsoft.azure.documents.client.documentclient.createdatabaseasync.aspx) metódusának használatával hozható létre. Az adatbázis a JSON-dokumentumtároló gyűjtemények között particionált logikai tárolója.

Másolja, majd illessze be az alábbi kódot a **GetStartedDemo** metódusba az ügyfél létrehozása alatt. Ezzel létrehoz egy *FamilyDB* elnevezésű adatbázist.

    private async Task GetStartedDemo()
    {
        this.client = new DocumentClient(new Uri(EndpointUri), PrimaryKey);

        // ADD THIS PART TO YOUR CODE
        await this.client.CreateDatabaseIfNotExistsAsync(new Database { Id = "FamilyDB_oa" });

Az alkalmazás futtatásához nyomja le az **F5** billentyűt.

Gratulálunk! Sikeresen létrehozott egy DocumentDB-adatbázist.  

## <a name="a-idcreatecollastep-5-create-a-collection"></a><a id="CreateColl"></a>5. lépés: Gyűjtemény létrehozása
> [!WARNING]
> A **CreateDocumentCollectionAsync** létrehoz egy fenntartott adattovábbítási kapacitással rendelkező új gyűjteményt, amely költségeket von maga után. További részletekért látogasson el az [árképzést ismertető oldalra](https://azure.microsoft.com/pricing/details/documentdb/).
> 
> 

Egy [gyűjtemény](documentdb-resources.md#collections) a **DocumentClient** osztály [CreateDocumentCollectionAsync](https://msdn.microsoft.com/library/microsoft.azure.documents.client.documentclient.createdocumentcollectionasync.aspx) metódusának használatával hozható létre. A gyűjtemény egy JSON-dokumentumokat és a kapcsolódó JavaScript-alkalmazáslogikát tartalmazó tároló.

Másolja, majd illessze be az alábbi kódot a **GetStartedDemo** metódusba az adatbázis létrehozása alatt. Ezzel létrehoz egy *FamilyCollection_oa* elnevezésű dokumentumgyűjteményt.

        this.client = new DocumentClient(new Uri(EndpointUri), PrimaryKey);

        await this.CreateDatabaseIfNotExists("FamilyDB_oa");

        // ADD THIS PART TO YOUR CODE
        await this.CreateDocumentCollectionIfNotExists("FamilyDB_oa", "FamilyCollection_oa");

Az alkalmazás futtatásához nyomja le az **F5** billentyűt.

Gratulálunk! Sikeresen létrehozott egy DocumentDB-dokumentumgyűjteményt.  

## <a name="a-idcreatedocastep-6-create-json-documents"></a><a id="CreateDoc"></a>6. lépés: JSON-dokumentumok létrehozása
A [dokumentumok](documentdb-resources.md#documents) a **DocumentClient** osztály [CreateDocumentAsync](https://msdn.microsoft.com/library/microsoft.azure.documents.client.documentclient.createdocumentasync.aspx) metódusának használatával hozhatók létre. A dokumentumok a felhasználó által megadott (tetszőleges) JSON-tartalmak. Most már beilleszthetünk egy vagy több dokumentumot. Ha van olyan adat, amelyet szeretne az adatbázisban tárolni, használhatja a DocumentDB [adatáttelepítési eszközét](documentdb-import-data.md).

Először létre kell hozni egy **Család** osztályt, amely ebben a mintában a DocumentDB-ben tárolt objektumokat képviseli. Létrehozunk még egy **Szülő**, **Gyermek**, **Háziállat** és **Cím** alosztályt is a **Család** osztályban való használatra. Ne feledje, hogy a dokumentumoknak rendelkezniük kell egy **Azonosító** tulajdonsággal, amely a JSON-fájlban **id**-ként van szerializálva. Az osztályok létrehozásához adja hozzá az alábbi belső alosztályokat a **GetStartedDemo** metódus után.

Másolja, majd illessze be a **Család**, **Szülő**, **Gyermek**, **Háziállat** és **Cím** osztályokat a **WriteToConsoleAndPromptToContinue** metódus alá.

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

Másolja, majd illessze be a **CreateFamilyDocumentIfNotExists** metódust a **CreateDocumentCollectionIfNotExists** metódus alá.

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

Másolja, majd illessze be az alábbi kódot a **GetStartedDemo** metódusba a dokumentumgyűjtemény létrehozása alatt.

    await this.CreateDatabaseIfNotExists("FamilyDB_oa");

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

Az alkalmazás futtatásához nyomja le az **F5** billentyűt.

Gratulálunk! Sikeresen létrehozott két DocumentDB-dokumentumot.  

![A diagram a NoSQL-oktatóanyagban a C# konzolalkalmazás létrehozásához használt fiók, online adatbázis, gyűjtemény és dokumentumok hierarchikus kapcsolatát ábrázolja.](./media/documentdb-dotnetcore-get-started/nosql-tutorial-account-database.png)

## <a name="a-idqueryastep-7-query-documentdb-resources"></a><a id="Query"></a>7. lépés: DocumentDB-erőforrások lekérdezése
A DocumentDB támogatja az egyes gyűjteményekben tárolt JSON-dokumentumokon végzett részletes [lekérdezéseket](documentdb-sql-query.md).  Az alábbi kódminta több olyan lekérdezést mutat be – a DocumentDB SQL-szintaxis és a LINQ használatával egyaránt – amelyeket az előző lépésben beszúrt dokumentumokon futtathatunk.

Másolja, majd illessze be a **ExecuteSimpleQuery** metódust a **CreateFamilyDocumentIfNotExists** metódus alá.

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

Másolja, majd illessze be az alábbi kódot a **GetStartedDemo** metódusba a második dokumentum létrehozása alá.

    await this.CreateFamilyDocumentIfNotExists("FamilyDB_oa", "FamilyCollection_oa", wakefieldFamily);

    // ADD THIS PART TO YOUR CODE
    this.ExecuteSimpleQuery("FamilyDB_oa", "FamilyCollection_oa");

Az alkalmazás futtatásához nyomja le az **F5** billentyűt.

Gratulálunk! Sikeres lekérdezést végzett egy DocumentDB-gyűjteményen.

Az alábbi diagram bemutatja, hogyan indít hívást a DocumentDB SQL-lekérdezési szintaxisa a létrehozott gyűjteményre. Ugyanez a logika vonatkozik a LINQ-lekérdezésekre is.

![A NoSQL-oktatóanyagban a C# konzolalkalmazás létrehozásához használt lekérdezés hatókörét és jelentését ábrázoló diagram.](./media/documentdb-dotnetcore-get-started/nosql-tutorial-collection-documents.png)

A [FROM](documentdb-sql-query.md#FromClause) kulcsszó kihagyható a lekérdezésből, mivel a DocumentDB-lekérdezések hatóköre eleve egyetlen gyűjtemény. Ezért a „FROM Families f” lecserélhető a „FROM root r” vagy bármilyen tetszőleges változónévre. A DocumentDB úgy veszi, hogy a Families, a root vagy a tetszőleges változónév alapértelmezés szerint az aktuális gyűjteményre hivatkozik.

## <a name="a-idreplacedocumentastep-8-replace-json-document"></a><a id="ReplaceDocument"></a>8. lépés: JSON-dokumentumok cseréje
A DocumentDB támogatja a JSON-dokumentumok cseréjét.  

Másolja, majd illessze be a **ReplaceFamilyDocument** metódust az **ExecuteSimpleQuery** metódus alá.

    // ADD THIS PART TO YOUR CODE
    private async Task ReplaceFamilyDocument(string databaseName, string collectionName, string familyName, Family updatedFamily)
    {
        try
        {
            await this.client.ReplaceDocumentAsync(UriFactory.CreateDocumentUri(databaseName, collectionName, familyName), updatedFamily);
            this.WriteToConsoleAndPromptToContinue("Replaced Family {0}", familyName);
        }
        catch (DocumentClientException de)
        {
            throw;
        }
    }

Másolja, majd illessze be az alábbi kódot a **GetStartedDemo** metódusba a lekérdezés végrehajtása alá. Ezáltal a dokumentum cseréje után ismét ugyanaz a lekérdezés fog lefutni a megváltozott dokumentum megtekintéséhez.

    await this.CreateFamilyDocumentIfNotExists("FamilyDB_oa", "FamilyCollection_oa", wakefieldFamily);

    this.ExecuteSimpleQuery("FamilyDB_oa", "FamilyCollection_oa");

    // ADD THIS PART TO YOUR CODE
    // Update the Grade of the Andersen Family child
    andersenFamily.Children[0].Grade = 6;

    await this.ReplaceFamilyDocument("FamilyDB_oa", "FamilyCollection_oa", "Andersen.1", andersenFamily);

    this.ExecuteSimpleQuery("FamilyDB_oa", "FamilyCollection_oa");

Az alkalmazás futtatásához nyomja le az **F5** billentyűt.

Gratulálunk! Sikeresen kicserélt egy DocumentDB-dokumentumot.

## <a name="a-iddeletedocumentastep-9-delete-json-document"></a><a id="DeleteDocument"></a>9. lépés: JSON-dokumentumok törlése
A DocumentDB támogatja a JSON-dokumentumok törlését.  

Másolja, majd illessze be a **DeleteFamilyDocument** metódust a **ReplaceFamilyDocument** metódus alá.

    // ADD THIS PART TO YOUR CODE
    private async Task DeleteFamilyDocument(string databaseName, string collectionName, string documentName)
    {
        try
        {
            await this.client.DeleteDocumentAsync(UriFactory.CreateDocumentUri(databaseName, collectionName, documentName));
            Console.WriteLine("Deleted Family {0}", documentName);
        }
        catch (DocumentClientException de)
        {
            throw;
        }
    }

Másolja, majd illessze be az alábbi kódot a **GetStartedDemo** metódusba a második lekérdezés végrehajtása alá.

    await this.ReplaceFamilyDocument("FamilyDB_oa", "FamilyCollection_oa", "Andersen.1", andersenFamily);

    this.ExecuteSimpleQuery("FamilyDB_oa", "FamilyCollection_oa");

    // ADD THIS PART TO CODE
    await this.DeleteFamilyDocument("FamilyDB_oa", "FamilyCollection_oa", "Andersen.1");

Az alkalmazás futtatásához nyomja le az **F5** billentyűt.

Gratulálunk! Sikeresen törölt egy DocumentDB-dokumentumot.

## <a name="a-iddeletedatabaseastep-10-delete-the-database"></a><a id="DeleteDatabase"></a>10. lépés: Az adatbázis törlése
A létrehozott adatbázis törlésével az adatbázis és az összes gyermekerőforrás (gyűjtemények, dokumentumok stb.) is törlődik.

Másolja, majd illessze be az alábbi kódot a **GetStartedDemo** metódusba a dokumentum törlése alá az adatbázis és az összes gyermek-erőforrás törléséhez.

    this.ExecuteSimpleQuery("FamilyDB_oa", "FamilyCollection_oa");

    await this.DeleteFamilyDocument("FamilyDB_oa", "FamilyCollection_oa", "Andersen.1");

    // ADD THIS PART TO CODE
    // Clean up/delete the database
    await this.client.DeleteDatabaseAsync(UriFactory.CreateDatabaseUri("FamilyDB_oa"));

Az alkalmazás futtatásához nyomja le az **F5** billentyűt.

Gratulálunk! Sikeresen törölt egy DocumentDB-adatbázist.

## <a name="a-idrunastep-11-run-your-c-console-application-all-together"></a><a id="Run"></a>11. lépés: Futtassa a teljes C# konzolalkalmazást!
Nyomja le az F5 billentyűt a Visual Studióban az alkalmazás hibakeresési módban történő összeállításához.

Meg kell jelennie az első lépések alkalmazás kimenetének. A kimenet megjeleníti a hozzáadott lekérdezések eredményeit, amelynek meg kell egyeznie az alábbi mintaszöveggel.

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

Gratulálunk! Elvégezte a NoSQL-oktatóanyagot, és egy működőképes C# konzolalkalmazással rendelkezik!

## <a name="a-idgetsolutiona-get-the-complete-nosql-tutorial-solution"></a><a id="GetSolution"></a>A NoSQL-oktatóanyagban szereplő teljes megoldás beszerzése
A cikkben szereplő összes mintát tartalmazó GetStarted-megoldás összeállításához az alábbiakra lesz szüksége:

* Aktív Azure-fiók. Ha még nincs fiókja, létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/).
* Egy [DocumentDB-fiók][documentdb-create-account].
* A GitHubon elérhető [GetStarted](https://github.com/Azure-Samples/documentdb-dotnet-core-getting-started) megoldás.

Ahhoz, hogy visszaállítsa a DocumentDB .NET Core SDK-ra mutató hivatkozásokat a Visual Studióban, kattintson a jobb gombbal a **GetStarted** megoldásra a Megoldáskezelőben, majd kattintson a **NuGet-csomagok visszaállításának engedélyezése** lehetőségre. Ezután a Program.cs fájlban frissítse az EndpointUrl és az AuthorizationKey értékeket a [Csatlakozás DocumentDB-fiókhoz](#Connect) című részben leírtak szerint.

## <a name="next-steps"></a>Következő lépések
* Összetettebb ASP.NET MVC NoSQL-oktatóanyagot szeretne? Tekintse meg a következőt: [ASP.NET MVC-vel rendelkező webalkalmazás összeállítása a DocumentDB használatával](documentdb-dotnet-application.md).
* Méret- és teljesítménytesztelést szeretne elvégezni a DocumentDB használatával? Tekintse meg a következőt: [Teljesítmény- és mérettesztelés az Azure DocumentDB használatával](documentdb-performance-testing.md)
* Ismerje meg, hogyan [felügyelhet egy DocumentDB-fiókot](documentdb-monitor-accounts.md).
* Futtasson lekérdezéseket a minta-adatkészleteken a [Query Playground](https://www.documentdb.com/sql/demo) (Tesztlekérdezések) használatával.
* A programozási modellel kapcsolatos további tudnivalókat a [DocumentDB dokumentációs oldalának](https://azure.microsoft.com/documentation/services/documentdb/) Develop (Fejlesztés) szakaszában találja.

[documentdb-create-account]: documentdb-create-account.md
[keys]: media/documentdb-dotnetcore-get-started/nosql-tutorial-keys.png

