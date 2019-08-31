---
title: A Azure Search használata .NET-alkalmazásokból – Azure Search
description: Ismerje meg, hogyan használhatja a Azure Searcht .NET- C# alkalmazásokban a és a .net SDK használatával. A kód alapú feladatok közé tartozik a szolgáltatáshoz való kapcsolódás, a tartalom indexelése és az index lekérdezése.
author: brjohnstmsft
manager: nitinme
services: search
ms.service: search
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 06/19/2019
ms.author: brjohnst
ms.openlocfilehash: 42d3a4a0840e7241666f66a09e7e6b11342cbfbc
ms.sourcegitcommit: 7a6d8e841a12052f1ddfe483d1c9b313f21ae9e6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/30/2019
ms.locfileid: "70182263"
---
# <a name="how-to-use-azure-search-from-a-net-application"></a>A Azure Search használata .NET-alkalmazásokból

Ebből a cikkből megtudhatja, hogyan hozhatja létre és futtathatja a [Azure Search .net SDK](https://aka.ms/search-sdk)-t. A .NET SDK-val a Azure Search használatával széles körű keresési élményt valósíthat meg az alkalmazásban.

## <a name="whats-in-the-azure-search-sdk"></a>A Azure Search SDK ismertetése
Az SDK néhány ügyféloldali kódtárat tartalmaz, amelyek lehetővé teszik az indexek, az adatforrások, az indexelő és a szinonimák leképezésének kezelését, valamint dokumentumok feltöltését és kezelését, valamint lekérdezések végrehajtását anélkül, hogy a HTTP-és JSON-adatokkal kellene foglalkoznia. Ezek az ügyféloldali kódtárak mind NuGet-csomagként vannak elosztva.

A fő NuGet `Microsoft.Azure.Search`-csomag, amely egy olyan meta-csomag, amely tartalmazza az összes többi csomagot függőségként. Akkor használja ezt a csomagot, ha csak most kezdi el, vagy ha tudja, hogy az alkalmazásnak szüksége lesz Azure Search összes szolgáltatására.

Az SDK-ban található egyéb NuGet-csomagok a következők:
 
  - `Microsoft.Azure.Search.Data`: Akkor használja ezt a csomagot, ha Azure Search használatával fejleszt .NET-alkalmazást, és csak az indexekben lévő dokumentumok lekérdezésére vagy frissítésére van szükség. Ha indexeket, szinonimákat vagy más szolgáltatási szintű erőforrásokat is létre kell hoznia vagy frissítenie, használja helyette `Microsoft.Azure.Search` a csomagot.
  - `Microsoft.Azure.Search.Service`: Akkor használja ezt a csomagot, ha az Automation szolgáltatást a .NET-ben fejleszti a Azure Search indexek, a szinonimák, az indexelő, az adatforrások vagy más szolgáltatási szintű erőforrások kezeléséhez. Ha csak az indexekben lévő dokumentumokat kell lekérdezni vagy frissítenie, használja `Microsoft.Azure.Search.Data` helyette a csomagot. Ha Azure Search összes funkcióját szeretné használni, használja helyette `Microsoft.Azure.Search` a csomagot.
  - `Microsoft.Azure.Search.Common`: A Azure Search .NET-kódtárak által igényelt gyakori típusok. Ezt a csomagot nem szükséges közvetlenül az alkalmazásban használni. Csak függőségként használható.

A `Index`különböző ügyféloldali kódtárak olyan osztályokat `Field`határoznak `Document`meg, mint a, és, `Documents.Search` valamint a `SearchServiceClient` és `SearchIndexClient` az osztályokhoz hasonló `Indexes.Create` műveletek. Ezek az osztályok a következő névterekben vannak rendszerezve:

* [Microsoft.Azure.Search](https://docs.microsoft.com/dotnet/api/microsoft.azure.search)
* [Microsoft.Azure.Search.Models](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models)

Ha visszajelzést szeretne küldeni az SDK jövőbeli frissítéséről, tekintse meg a [Visszajelzés oldalt](https://feedback.azure.com/forums/263029-azure-search/) , vagy hozzon létre egy problémát a [githubon](https://github.com/azure/azure-sdk-for-net/issues) , és nevezze el a "Azure Search" kifejezést a probléma címére.

A .net SDK a `2019-05-06` [Azure Search REST API](https://docs.microsoft.com/rest/api/searchservice/)verzióját támogatja. Ez a verzió az Azure-Blobok indexelése során az [összetett típusok](search-howto-complex-data-types.md), a [kognitív keresés](cognitive-search-concept-intro.md), az [automatikus kiegészítés](https://docs.microsoft.com/rest/api/searchservice/autocomplete)és a [JsonLines-elemzési mód](search-howto-index-json-blobs.md) támogatását tartalmazza. 

Ez az SDK nem támogatja a [felügyeleti műveleteket](https://docs.microsoft.com/rest/api/searchmanagement/) , például a keresési szolgáltatások létrehozását és méretezését, valamint az API-kulcsok kezelését. Ha .NET-alkalmazásból kell felügyelni a keresési erőforrásokat, használhatja a [Azure Search .net Management SDK](https://aka.ms/search-mgmt-sdk)-t.

## <a name="upgrading-to-the-latest-version-of-the-sdk"></a>Frissítés az SDK legújabb verziójára
Ha már használja a Azure Search .NET SDK egy régebbi verzióját, és frissíteni szeretné az általánosan elérhető legújabb verzióra, [Ez a cikk](search-dotnet-sdk-migration-version-9.md) azt ismerteti, hogyan.

## <a name="requirements-for-the-sdk"></a>Az SDK követelményei
1. Visual Studio 2017 vagy újabb verzió.
2. Saját Azure Search szolgáltatás. Az SDK használatához szüksége lesz a szolgáltatás nevére és egy vagy több API-kulcsra. [A portálon létrehozott szolgáltatás](search-create-service-portal.md) a következő lépésekkel segíti Önt.
3. Töltse le a Azure Search .NET SDK [NuGet csomagot](https://www.nuget.org/packages/Microsoft.Azure.Search) a Visual Studióban a "NuGet-csomagok kezelése" paranccsal. Csak keresse meg a csomag nevét `Microsoft.Azure.Search` a NuGet.org (vagy a fenti csomagok egyikén), ha csak a funkció egy részhalmazára van szüksége.

A Azure Search .NET SDK támogatja a .NET-keretrendszer 4.5.2-es és újabb verzióit, valamint a .NET Core 2,0-es és újabb verzióit.

## <a name="core-scenarios"></a>Alapvető forgatókönyvek
Több dolgot kell tennie a keresési alkalmazásban. Ebben az oktatóanyagban a következő alapvető forgatókönyveket mutatjuk be:

* Index létrehozása
* Az index feltöltése dokumentumokkal
* Dokumentumok keresése teljes szöveges keresés és szűrők használatával

Az alábbi mintakód ezeket a forgatókönyveket szemlélteti. Bátran használhatja a kódrészleteket a saját alkalmazásában.

### <a name="overview"></a>Áttekintés
A felderített minta alkalmazás létrehoz egy "Hotels" nevű új indexet, feltölti azt néhány dokumentummal, majd végrehajt néhány keresési lekérdezést. Itt látható a fő program, amely a teljes folyamatot mutatja:

```csharp
// This sample shows how to delete, create, upload documents and query an index
static void Main(string[] args)
{
    IConfigurationBuilder builder = new ConfigurationBuilder().AddJsonFile("appsettings.json");
    IConfigurationRoot configuration = builder.Build();

    SearchServiceClient serviceClient = CreateSearchServiceClient(configuration);

    string indexName = configuration["SearchIndexName"];

    Console.WriteLine("{0}", "Deleting index...\n");
    DeleteIndexIfExists(indexName, serviceClient);

    Console.WriteLine("{0}", "Creating index...\n");
    CreateIndex(indexName, serviceClient);

    ISearchIndexClient indexClient = serviceClient.Indexes.GetClient(indexName);

    Console.WriteLine("{0}", "Uploading documents...\n");
    UploadDocuments(indexClient);

    ISearchIndexClient indexClientForQueries = CreateSearchIndexClient(configuration);

    RunQueries(indexClientForQueries);

    Console.WriteLine("{0}", "Complete.  Press any key to end application...\n");
    Console.ReadKey();
}
```

> [!NOTE]
> A jelen útmutatóban használt mintaalkalmazás teljes forráskódját a [GitHub](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowTo) webhelyén találja.
> 
>

Ezt a lépésről lépésre mutatjuk be. Először létre kell hozni egy újat `SearchServiceClient`. Ez az objektum lehetővé teszi az indexek kezelését. Az egyik létrehozásához meg kell adnia a Azure Search szolgáltatás nevét, valamint egy felügyeleti API-kulcsot. Ezt az információt `appsettings.json` megadhatja a [minta alkalmazás](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowTo)fájljában.

```csharp
private static SearchServiceClient CreateSearchServiceClient(IConfigurationRoot configuration)
{
    string searchServiceName = configuration["SearchServiceName"];
    string adminApiKey = configuration["SearchServiceAdminApiKey"];

    SearchServiceClient serviceClient = new SearchServiceClient(searchServiceName, new SearchCredentials(adminApiKey));
    return serviceClient;
}
```

> [!NOTE]
> Ha helytelen kulcsot ad meg (például egy lekérdezési kulcsot, amelynél rendszergazdai kulcs szükséges), akkor `SearchServiceClient` `CloudException` a "tiltott" hibaüzenet jelenik `Indexes.Create`meg, amikor első alkalommal hívja meg a műveleti metódust, például:. Ha ez történik, tekintse meg az API-kulcsát.
> 
> 

A következő néhány sorban meghívja a "Hotels" nevű index létrehozását, ha már létezik, először törölje azt. Ezeket a metódusokat egy kicsit később fogjuk átjárni.

```csharp
Console.WriteLine("{0}", "Deleting index...\n");
DeleteIndexIfExists(indexName, serviceClient);

Console.WriteLine("{0}", "Creating index...\n");
CreateIndex(indexName, serviceClient);
```

Ezután az indexet fel kell tölteni. Az index feltöltéséhez szüksége lesz a `SearchIndexClient`következőre:. Kétféleképpen szerezhet be egyet: a létrehozásával vagy a `Indexes.GetClient` `SearchServiceClient`meghívásával. Az utóbbit az egyszerűség kedvéért használjuk.

```csharp
ISearchIndexClient indexClient = serviceClient.Indexes.GetClient(indexName);
```

> [!NOTE]
> Egy tipikus keresési alkalmazásban az indexek kezelését és a populációt a keresési lekérdezések egy külön összetevője is kezeli. `Indexes.GetClient`a kényelmes megoldás az index feltöltésére, mert a probléma a továbbiak `SearchCredentials`megadásával. Ezt azon rendszergazdai kulcs átadásával hajtja végre, amelyet a `SearchServiceClient` elemnek az új `SearchIndexClient` objektumban történő létrehozásakor használt. A lekérdezéseket végrehajtó alkalmazás részeként azonban jobb megoldás a `SearchIndexClient` közvetlen létrehozása, így a lekérdezési kulcs átadható, amely csak az adatolvasást teszi lehetővé rendszergazdai kulcs helyett. Ez összhangban van a legalacsonyabb jogosultsági szint elvével, és segít az alkalmazás biztonságosabbá tételében. A rendszergazdai kulcsokról és a lekérdezési kulcsokról [itt](https://docs.microsoft.com/rest/api/searchservice/#authentication-and-authorization)talál további információt.
> 
> 

Most, hogy már van `SearchIndexClient`egy, az indexet fel lehet tölteni. Az indexek populációját egy másik módszer hajtja végre, amelyet később fogunk eljárni.

```csharp
Console.WriteLine("{0}", "Uploading documents...\n");
UploadDocuments(indexClient);
```

Végül végrehajtunk néhány keresési lekérdezést, és megjeleníti az eredményeket. Ezúttal egy másikat `SearchIndexClient`használunk:

```csharp
ISearchIndexClient indexClientForQueries = CreateSearchIndexClient(indexName, configuration);

RunQueries(indexClientForQueries);
```

Később részletesebben is szemügyre vesszük a `RunQueries` metódust. Az új `SearchIndexClient`kód az alábbi kódot hozza létre:

```csharp
private static SearchIndexClient CreateSearchIndexClient(string indexName, IConfigurationRoot configuration)
{
    string searchServiceName = configuration["SearchServiceName"];
    string queryApiKey = configuration["SearchServiceQueryApiKey"];

    SearchIndexClient indexClient = new SearchIndexClient(searchServiceName, indexName, new SearchCredentials(queryApiKey));
    return indexClient;
}
```

Ezúttal a lekérdezési kulcsot használjuk, mert nincs szükségünk írási hozzáférésre az indexhez. Ezt az információt `appsettings.json` megadhatja a [minta alkalmazás](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowTo)fájljában.

Ha az alkalmazást érvényes szolgáltatásnév és API-kulcsok használatával futtatja, a kimenetnek az alábbi példához hasonlóan kell kinéznie: (Egyes konzolok kimenete a következővel lett lecserélve: "..." illusztrációs célokra.)

    Deleting index...

    Creating index...

    Uploading documents...

    Waiting for documents to be indexed...

    Search the entire index for the term 'motel' and return only the HotelName field:

    Name: Secret Point Motel

    Name: Twin Dome Motel


    Apply a filter to the index to find hotels with a room cheaper than $100 per night, and return the hotelId and description:

    HotelId: 1
    Description: The hotel is ideally located on the main commercial artery of the city in the heart of New York. A few minutes away is Times Square and the historic centre of the city, as well as other places of interest that make New York one of America's most attractive and cosmopolitan cities.

    HotelId: 2
    Description: The hotel is situated in a  nineteenth century plaza, which has been expanded and renovated to the highest architectural standards to create a modern, functional and first-class hotel in which art and unique historical elements coexist with the most modern comforts.


    Search the entire index, order by a specific field (lastRenovationDate) in descending order, take the top two results, and show only hotelName and lastRenovationDate:

    Name: Triple Landscape Hotel
    Last renovated on: 9/20/2015 12:00:00 AM +00:00

    Name: Twin Dome Motel
    Last renovated on: 2/18/1979 12:00:00 AM +00:00


    Search the hotel names for the term 'hotel':

    HotelId: 3
    Name: Triple Landscape Hotel
    ...

    Complete.  Press any key to end application... 

Az alkalmazás teljes forráskódját a cikk végén találja.

A következő lépésben részletesebben szemügyre vesszük a által `Main`hívott metódusokat.

### <a name="creating-an-index"></a>Index létrehozása
A `SearchServiceClient`létrehozása után a `Main` törli a "Hotels" indexet, ha már létezik. Ezt a törlést a következő módszer hajtja végre:

```csharp
private static void DeleteIndexIfExists(string indexName, SearchServiceClient serviceClient)
{
    if (serviceClient.Indexes.Exists(indexName))
    {
        serviceClient.Indexes.Delete(indexName);
    }
}
```

Ezzel a módszerrel `SearchServiceClient` ellenőrizhető, hogy az index létezik-e, és ha igen, törölje.

> [!NOTE]
> Ebben a cikkben a példakód az egyszerűség érdekében az Azure Search .NET SDK szinkron módszereit használja. Azt javasoljuk, hogy a méretezhetőség és a gyors válaszadás érdekében használja saját alkalmazásaiban az aszinkron módszereket. Például a fenti `ExistsAsync` metódusban a és a `DeleteAsync` `Exists` `Delete`helyett használhatja a és a értéket.
> 
> 

`Main` Ezután létrehoz egy új "Hotels" indexet a metódus meghívásával:

```csharp
private static void CreateIndex(string indexName, SearchServiceClient serviceClient)
{
    var definition = new Index()
    {
        Name = indexName,
        Fields = FieldBuilder.BuildForType<Hotel>()
    };
    
    serviceClient.Indexes.Create(definition);
}
```

Ez a metódus egy új `Index` objektumot hoz létre az új `Field` index sémáját meghatározó objektumok listájával. Minden mező rendelkezik egy névvel, adattípussal és számos olyan attribútummal, amely meghatározza a keresési viselkedését. Az `FieldBuilder` osztály reflexió használatával hozza létre az index `Field` objektumainak listáját az adott `Hotel` Model osztály nyilvános tulajdonságainak és attribútumainak vizsgálatával. Részletesebben is szemügyre vesszük az `Hotel` osztályt később.

> [!NOTE]
> Az `Field` objektumok listáját mindig közvetlenül a használata `FieldBuilder` helyett is létrehozhatja, ha szükséges. Előfordulhat például, hogy nem szeretné használni a modell osztályt, vagy egy meglévő modell osztályt kell használnia, amelyet attribútumok hozzáadásával nem kíván módosítani.
>
> 

A mezőkön kívül pontozási profilokat, javaslatokat vagy CORS lehetőségeket is hozzáadhat az indexhez (ezek a paraméterek kimaradnak a mintából a rövidség kedvéért). Az index objektumról és annak összetevőiről az [SDK](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.index)-referenciában, valamint a [Azure Search REST API](https://docs.microsoft.com/rest/api/searchservice/)-referenciában talál további információt.

### <a name="populating-the-index"></a>Az index feltöltése
A következő lépésben `Main` feltölti az újonnan létrehozott indexet. Az index populációja a következő módon történik: (Néhány kód a következővel lett lecserélve: "..." illusztrációs célokra.  Tekintse meg a teljes adatpopulációs kód teljes minta megoldását.)

```csharp
private static void UploadDocuments(ISearchIndexClient indexClient)
{
    var hotels = new Hotel[]
    {
        new Hotel()
        {
            HotelId = "1",
            HotelName = "Secret Point Motel",
            ...
            Address = new Address()
            {
                StreetAddress = "677 5th Ave",
                ...
            },
            Rooms = new Room[]
            {
                new Room()
                {
                    Description = "Budget Room, 1 Queen Bed (Cityside)",
                    ...
                },
                new Room()
                {
                    Description = "Budget Room, 1 King Bed (Mountain View)",
                    ...
                },
                new Room()
                {
                    Description = "Deluxe Room, 2 Double Beds (City View)",
                    ...
                }
            }
        },
        new Hotel()
        {
            HotelId = "2",
            HotelName = "Twin Dome Motel",
            ...
            {
                StreetAddress = "140 University Town Center Dr",
                ...
            },
            Rooms = new Room[]
            {
                new Room()
                {
                    Description = "Suite, 2 Double Beds (Mountain View)",
                    ...
                },
                new Room()
                {
                    Description = "Standard Room, 1 Queen Bed (City View)",
                    ...
                },
                new Room()
                {
                    Description = "Budget Room, 1 King Bed (Waterfront View)",
                    ...
                }
            }
        },
        new Hotel()
        {
            HotelId = "3",
            HotelName = "Triple Landscape Hotel",
            ...
            Address = new Address()
            {
                StreetAddress = "3393 Peachtree Rd",
                ...
            },
            Rooms = new Room[]
            {
                new Room()
                {
                    Description = "Standard Room, 2 Queen Beds (Amenities)",
                    ...
                },
                new Room ()
                {
                    Description = "Standard Room, 2 Double Beds (Waterfront View)",
                    ...
                },
                new Room()
                {
                    Description = "Deluxe Room, 2 Double Beds (Cityside)",
                    ...
                }
            }
        }
    };

    var batch = IndexBatch.Upload(hotels);

    try
    {
        indexClient.Documents.Index(batch);
    }
    catch (IndexBatchException e)
    {
        // Sometimes when your Search service is under load, indexing will fail for some of the documents in
        // the batch. Depending on your application, you can take compensating actions like delaying and
        // retrying. For this simple demo, we just log the failed document keys and continue.
        Console.WriteLine(
            "Failed to index some of the documents: {0}",
            String.Join(", ", e.IndexingResults.Where(r => !r.Succeeded).Select(r => r.Key)));
    }

    Console.WriteLine("Waiting for documents to be indexed...\n");
    Thread.Sleep(2000);
}
```

Ez a metódus négy részből áll. Az első három `Hotel` `Room` objektumból álló tömböt hoz létre, amely az indexbe feltöltendő bemeneti adatokként szolgál majd. Ezek az adathalmazok az egyszerűség kedvéért nehezen kódoltak. A saját alkalmazásban az adatok valószínűleg egy külső adatforrásból származnak, például egy SQL-adatbázisból.

A második rész létrehozza `IndexBatch` a dokumentumokat tartalmazó dokumentumot. Megadhatja a kötegre alkalmazni kívánt műveletet a létrehozáskor, ebben az esetben a hívásával `IndexBatch.Upload`. Ezután a rendszer feltölti a köteget a Azure Search indexbe a `Documents.Index` metódussal.

> [!NOTE]
> Ebben a példában csak dokumentumokat töltünk fel. Ha szeretné egyesíteni a változtatásokat a meglévő dokumentumokban, vagy törölhet dokumentumokat, létrehozhat kötegeket a meghívásával `IndexBatch.Merge` `IndexBatch.MergeOrUpload`, `IndexBatch.Delete` vagy ehelyett. Egy kötegben különböző műveleteket is összekeverheti úgy, hogy `IndexBatch.New`meghívja a (z) `IndexAction` függvényt, amely az objektumok egy gyűjteményét kéri, amelyek mindegyike megadja, hogy egy adott művelet elvégzése Azure Search egy dokumentumon. Létrehozhatja `IndexAction` a saját műveleteit úgy, hogy meghívja a megfelelő metódust `IndexAction.Merge`, `IndexAction.Upload`például, stb.
> 
> 

A metódus harmadik része egy olyan Catch blokk, amely egy fontos, az indexeléshez szükséges hibát kezel. Ha az Azure Search-szolgáltatásnak nem sikerül indexelnie a kötegben szereplő fájlok valamelyikét, a `Documents.Index` rendszer `IndexBatchException` választ ad. Ez a kivétel akkor fordulhat elő, ha a dokumentumok indexelése során a szolgáltatás nagy terhelés alatt áll. **Javasoljuk, hogy a kódban explicit módon kezelje ezt az esetet.** Azon dokumentumok esetében, ahol az indexelés meghiúsult, elhalaszthatja azt, majd később újra megpróbálkozhat az indexeléssel, vagy a mintának megfelelően naplózhatja azt, és folytathatja a munkáját, esetleg – az alkalmazás adatkonzisztencia-követelményeitől függően – más műveletbe kezdhet.

> [!NOTE]
> A [`FindFailedActionsToRetry`](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.indexbatchexception.findfailedactionstoretry) metódussal olyan új köteget hozhat létre, amely csak azokat a műveleteket tartalmazza, amelyeken a korábbi `Index`hívás sikertelen volt. Megtudhatja, hogyan kell megfelelően használni [a StackOverflow-on](https://stackoverflow.com/questions/40012885/azure-search-net-sdk-how-to-use-findfailedactionstoretry).
>
>

Végül a `UploadDocuments` metódus két másodpercig késlelteti. Az Azure Search-szolgáltatásban az indexelés aszinkron módon történik, így a mintaalkalmazásnak egy rövid ideig várnia kell, amíg a rendszer meggyőződik arról, hogy a dokumentum kereshető. Ilyen mértékű késleltetésre kizárólag demók, tesztek és mintaalkalmazások esetében van szükség.

<a name="how-dotnet-handles-documents"></a>

#### <a name="how-the-net-sdk-handles-documents"></a>A .NET SDK dokumentumkezelési módszere
Megfordulhat a fejében, hogy miként képes az Azure Search .NET SDK felhasználó által meghatározott `Hotel` osztályhoz hasonló példányok feltöltésére az indexbe. A kérdés megválaszolásához tekintsük át a `Hotel` következő osztályt:

```csharp
using System;
using Microsoft.Azure.Search;
using Microsoft.Azure.Search.Models;
using Microsoft.Spatial;
using Newtonsoft.Json;

public partial class Hotel
{
    [System.ComponentModel.DataAnnotations.Key]
    [IsFilterable]
    public string HotelId { get; set; }

    [IsSearchable, IsSortable]
    public string HotelName { get; set; }

    [IsSearchable]
    [Analyzer(AnalyzerName.AsString.EnLucene)]
    public string Description { get; set; }

    [IsSearchable]
    [Analyzer(AnalyzerName.AsString.FrLucene)]
    [JsonProperty("Description_fr")]
    public string DescriptionFr { get; set; }

    [IsSearchable, IsFilterable, IsSortable, IsFacetable]
    public string Category { get; set; }

    [IsSearchable, IsFilterable, IsFacetable]
    public string[] Tags { get; set; }

    [IsFilterable, IsSortable, IsFacetable]
    public bool? ParkingIncluded { get; set; }

    // SmokingAllowed reflects whether any room in the hotel allows smoking.
    // The JsonIgnore attribute indicates that a field should not be created 
    // in the index for this property and it will only be used by code in the client.
    [JsonIgnore]
    public bool? SmokingAllowed => (Rooms != null) ? Array.Exists(Rooms, element => element.SmokingAllowed == true) : (bool?)null;

    [IsFilterable, IsSortable, IsFacetable]
    public DateTimeOffset? LastRenovationDate { get; set; }

    [IsFilterable, IsSortable, IsFacetable]
    public double? Rating { get; set; }

    public Address Address { get; set; }

    [IsFilterable, IsSortable]
    public GeographyPoint Location { get; set; }

    public Room[] Rooms { get; set; }
}
```

Az első dolog, hogy az `Hotel` osztályban lévő egyes nyilvános tulajdonságok neve az index definíciójában azonos nevű mezőre lesz leképezve. Ha azt szeretné, hogy az egyes mezők kisbetűvel kezdődjön ("teve-eset"), az SDK-val az osztály `[SerializePropertyNamesAsCamelCase]` attribútumával automatikusan leképezheti a tulajdonságok nevét a Camel-Case értékre. Ez a forgatókönyv gyakori olyan .NET-alkalmazásokban, amelyek adatkötést hajtanak végre, ha a célként megadott séma kívül esik az alkalmazás fejlesztői felügyeletén anélkül, hogy meg kellene sérteni a "Pascal Case" elnevezési irányelveket a .NET-ben.

> [!NOTE]
> Az Azure Search .NET SDK a [NewtonSoft JSON.NET](https://www.newtonsoft.com/json/help/html/Introduction.htm) könyvtárat használja az egyéni modellek JSON-ból és JSON-ba történő szerializálására és deszerializálására. A szerializálás szükség szerint testre szabható. További információ: [Egyéni szerializálás a JSON.net](#JsonDotNet).
> 
> 

A második dolog, ami észreveszi, hogy az egyes tulajdonságok olyan attribútumokkal vannak `Key`díszítve `Analyzer`, mint `IsFilterable` `IsSearchable`a, a, és a. Ezek az attribútumok közvetlenül a [Azure Search index megfelelő mezőinek attribútumaihoz](https://docs.microsoft.com/rest/api/searchservice/create-index#request)képezhetők le. Az `FieldBuilder` osztály ezeket a tulajdonságokat használja az indexhez tartozó mező-definíciók összeállításához.

Az `Hotel` osztályban a harmadik fontos dolog a nyilvános tulajdonságok adattípusa. Az indexdefinícióban a rendszer ezen .NET tulajdonságtípusokat képezi le a nekik megfelelő mezőtípusokra. Például a rendszer a `Edm.String` típusú `Category` szöveges tulajdonságot a `category` mezőbe képezi le. Hasonló típusú leképezések találhatók a, `bool?`, `Edm.Boolean`és `DateTimeOffset?` `Edm.DateTimeOffset` stb. között. A típusleképezés vonatkozó szabályainak dokumentációja az [Azure Search .NET SDK-referenciában](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.documentsoperationsextensions.get), a `Documents.Get` metódusnál található. Az `FieldBuilder` osztály gondoskodik a leképezésről, de ez továbbra is hasznos lehet, ha a szerializálási problémák elhárításához szükséges.

Észrevette a `SmokingAllowed` tulajdonságot?

```csharp
[JsonIgnore]
public bool? SmokingAllowed => (Rooms != null) ? Array.Exists(Rooms, element => element.SmokingAllowed == true) : (bool?)null;
```

A `JsonIgnore` tulajdonság attribútuma azt jelzi, `FieldBuilder` hogy a nem szerializálja az indexet mezőként.  Ez nagyszerű módja annak, hogy olyan ügyféloldali számított tulajdonságokat hozzon létre, amelyeket segítőként használhat az alkalmazásban.  Ebben az esetben a `SmokingAllowed` tulajdonság azt jelzi, hogy a `Rooms` gyűjteményben lévő bármelyik `Room` a dohányzást teszi lehetővé.  Ha az összes hamis, az azt jelzi, hogy a teljes szálloda nem teszi lehetővé a dohányzást.

Bizonyos tulajdonságok, `Address` például a `Rooms` és a .net-osztályok példányai.  Ezek a tulajdonságok összetettebb adatstruktúrákat jelentenek, és ennek eredményeképpen az indexben [összetett](https://docs.microsoft.com/azure/search/search-howto-complex-data-types) adattípusú mezőket igényelnek.

A `Address` tulajdonság a (z) `Address` osztály több értékének egy készletét jelöli, az alábbiakban meghatározottak szerint:

```csharp
using System;
using Microsoft.Azure.Search;
using Microsoft.Azure.Search.Models;
using Newtonsoft.Json;

namespace AzureSearch.SDKHowTo
{
    public partial class Address
    {
        [IsSearchable]
        public string StreetAddress { get; set; }

        [IsSearchable, IsFilterable, IsSortable, IsFacetable]
        public string City { get; set; }

        [IsSearchable, IsFilterable, IsSortable, IsFacetable]
        public string StateProvince { get; set; }

        [IsSearchable, IsFilterable, IsSortable, IsFacetable]
        public string PostalCode { get; set; }

        [IsSearchable, IsFilterable, IsSortable, IsFacetable]
        public string Country { get; set; }
    }
}
```

Ez az osztály a Egyesült Államok vagy Kanada címeinek leírásához használt szabványos értékeket tartalmazza. Az alábbihoz hasonló típusokat használhat a logikai mezők és az index együttes csoportosításához.

A `Rooms` tulajdonság `Room` objektumok tömbjét jelöli:

```csharp
using System;
using Microsoft.Azure.Search;
using Microsoft.Azure.Search.Models;
using Newtonsoft.Json;

namespace AzureSearch.SDKHowTo
{
    public partial class Room
    {
        [IsSearchable]
        [Analyzer(AnalyzerName.AsString.EnMicrosoft)]
        public string Description { get; set; }

        [IsSearchable]
        [Analyzer(AnalyzerName.AsString.FrMicrosoft)]
        [JsonProperty("Description_fr")]
        public string DescriptionFr { get; set; }

        [IsSearchable, IsFilterable, IsFacetable]
        public string Type { get; set; }

        [IsFilterable, IsFacetable]
        public double? BaseRate { get; set; }

        [IsSearchable, IsFilterable, IsFacetable]
        public string BedOptions { get; set; }

        [IsFilterable, IsFacetable]
        public int SleepsCount { get; set; }

        [IsFilterable, IsFacetable]
        public bool? SmokingAllowed { get; set; }

        [IsSearchable, IsFilterable, IsFacetable]
        public string[] Tags { get; set; }
    }
}
```

A .NET-beli adatmodellt és a hozzá tartozó index-sémát úgy kell kialakítani, hogy támogassa a végfelhasználónak adni kívánt keresési élményt. A .NET-ben lévő minden legfelső szintű objektum, azaz az indexben lévő dokumentum, amely egy keresési eredménynek felel meg, amelyet a felhasználói felületen fog megjelenni. Egy szállodai keresési alkalmazásban például előfordulhat, hogy a végfelhasználók a szállodai név, a szálloda funkciói vagy egy adott helyiség jellemzői alapján kívánják keresni a keresést. Néhány lekérdezési példát egy kicsit később fogunk lefedni.

A saját osztályok használata lehetővé teszi, hogy az indexben lévő dokumentumokkal való kommunikáció mindkét irányban működjön. Lekérheti a keresési eredményeket, és az SDK automatikusan deszerializálhatja őket egy tetszőleges típusra, ahogy a következő szakaszban is látni fogjuk.

> [!NOTE]
> Az Azure Search .NET SDK támogatja a `Document` osztályt használó, dinamikus dokumentumtípusokat is, amely alatt a mezők neveinek értékekre történő kulcs/érték-leképezését értjük. Ez olyan helyzetekben hasznos, ha például a tervezés időpontjában az indexséma még nem ismert, illetve ha az adott modellosztályokhoz történő kötés nehézkes volna. Az SDK-ban lévő összes, dokumentumokkal foglalkozó módszer a `Document` osztállyal kompatibilis túlterhelésekkel rendelkezik, valamint olyan szigorú típusmegadású túlterhelésekkel, amelyek általános típusú paramétert vesznek fel. Ebben az oktatóanyagban csak az utóbbit használjuk a mintakód. `Dictionary<string, object>` [ Az`Document` osztály](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.document) örökli a következőt:.
> 
>

**Miért használjon nullázható adattípusokat?**

Az Azure Search-indexre leképezést végző, saját modellosztályok létrehozásakor javasoljuk, hogy például a `bool` és `int` értéktípusok tulajdonságainak megadása nullázhatóként történjen (például `bool` helyett `bool?`). Nem nullázható tulajdonság használatakor **garantálnia** kell, hogy az index egyetlen dokumentuma sem tartalmaz az adott mezőben null értéket. Ennek kényszerítéséhez sem az SDK, sem az Azure Search szolgáltatás nem nyújt segítséget.

Ez nem csupán egy feltételezett probléma: Képzelje el, hogy egy új mezőt ad hozzá egy típusú `Edm.Int32`meglévő indexhez. Az indexdefiníció frissítését követően ehhez a mezőhöz minden dokumentumban null érték tartozik (mivel az Azure Search szolgáltatásban az összes értéktípus nullázható). Ha ezt követően egy modellosztályt úgy alkalmaz, hogy ehhez a mezőhöz nem nullázható `int` tulajdonságot ad meg, a dokumentumok lekérdezésének megkísérlésekor egy ehhez hasonló `JsonSerializationException` választ kap:

    Error converting value {null} to type 'System.Int32'. Path 'IntValue'.

Ezért javasoljuk, hogy a modellosztályokban nullázható értéktípusokat használjon.

<a name="JsonDotNet"></a>

#### <a name="custom-serialization-with-jsonnet"></a>Egyéni szerializálás a JSON.NET
Az SDK JSON.NET használ a dokumentumok szerializálásához és deszerializálásához. Szükség szerint testre szabhatja a szerializálást és a deszerializálást, ha `JsonConverter` meghatározza `IContractResolver`a saját vagy a. További információkért tekintse meg a [JSON.net dokumentációját](https://www.newtonsoft.com/json/help/html/Introduction.htm). Ez akkor lehet hasznos, ha egy meglévő modell osztályt szeretne alkalmazkodni az alkalmazásból a Azure Search és más fejlettebb forgatókönyvekhez való használatra. Egyéni szerializálással például a következőket teheti:

* Belefoglalhatja vagy kizárhatja a modell osztályának bizonyos tulajdonságait a dokumentum mezőinek tárolásához.
* A kód és a mezőnevek nevei közötti leképezés az indexben.
* Létrehozhat olyan egyéni attribútumokat, amelyek a tulajdonságok megfeleltetéséhez használhatók a dokumentumok mezőihez.

Az egyéni szerializálás megvalósítására példákat talál a Azure Search .NET SDK-ban a GitHubon. [Ez a mappa](https://github.com/Azure/azure-sdk-for-net/tree/AutoRest/src/Search/Search.Tests/Tests/Models)jó kiindulási pont. Az egyéni szerializálási tesztek által használt osztályokat tartalmazza.

### <a name="searching-for-documents-in-the-index"></a>Dokumentumok keresése az indexben
A minta alkalmazás utolsó lépése az index egyes dokumentumainak megkeresése:

```csharp
private static void RunQueries(ISearchIndexClient indexClient)
{
    SearchParameters parameters;
    DocumentSearchResult<Hotel> results;

    Console.WriteLine("Search the entire index for the term 'motel' and return only the HotelName field:\n");

    parameters =
        new SearchParameters()
        {
            Select = new[] { "HotelName" }
        };

    results = indexClient.Documents.Search<Hotel>("motel", parameters);

    WriteDocuments(results);

    Console.Write("Apply a filter to the index to find hotels with a room cheaper than $100 per night, ");
    Console.WriteLine("and return the hotelId and description:\n");

    parameters =
        new SearchParameters()
        {
            Filter = "Rooms/any(r: r/BaseRate lt 100)",
            Select = new[] { "HotelId", "Description" }
        };

    results = indexClient.Documents.Search<Hotel>("*", parameters);

    WriteDocuments(results);

    Console.Write("Search the entire index, order by a specific field (lastRenovationDate) ");
    Console.Write("in descending order, take the top two results, and show only hotelName and ");
    Console.WriteLine("lastRenovationDate:\n");

    parameters =
        new SearchParameters()
        {
            OrderBy = new[] { "LastRenovationDate desc" },
            Select = new[] { "HotelName", "LastRenovationDate" },
            Top = 2
        };

    results = indexClient.Documents.Search<Hotel>("*", parameters);

    WriteDocuments(results);

    Console.WriteLine("Search the entire index for the term 'hotel':\n");

    parameters = new SearchParameters();
    results = indexClient.Documents.Search<Hotel>("hotel", parameters);

    WriteDocuments(results);
}
```

Ez a metódus minden alkalommal egy új `SearchParameters` objektumot hoz létre, amikor lekérdezést hajt végre. Ez az objektum a lekérdezés további beállításainak megadására szolgál, például rendezés, szűrés, lapozás és aspektusok. Ebben a metódusban a `Filter` `Select` `OrderBy`,, és `Top` tulajdonságot a különböző lekérdezésekhez állítja be. Az `SearchParameters` összes tulajdonság dokumentálva [van](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.searchparameters).

A következő lépés a keresési lekérdezés tényleges végrehajtása. A keresés futtatása a `Documents.Search` metódus használatával történik. Minden lekérdezés esetében a keresett szöveget karakterláncként kell használni (vagy `"*"` ha nincs keresési szöveg), valamint a korábban létrehozott keresési paramétereket. A ( `Hotel` `Documents.Search`z) Type paraméterként is megadható, amely azt jelzi, hogy az SDK a keresési eredményekben lévő dokumentumokat deszerializálja a típusú `Hotel`objektumokra.

> [!NOTE]
> A keresési lekérdezés kifejezésének szintaxisáról [itt](https://docs.microsoft.com/rest/api/searchservice/Simple-query-syntax-in-Azure-Search)talál további információt.
> 
> 

Végül az egyes lekérdezések után a metódus a keresési eredmények összes egyezését megismétli, és minden dokumentumot kinyomtat a konzolra:

```csharp
private static void WriteDocuments(DocumentSearchResult<Hotel> searchResults)
{
    foreach (SearchResult<Hotel> result in searchResults.Results)
    {
        Console.WriteLine(result.Document);
    }

    Console.WriteLine();
}
```

Ismerkedjen meg közelebbről a lekérdezésekkel. Az első lekérdezés végrehajtásához az alábbi kód szükséges:

```csharp
parameters =
    new SearchParameters()
    {
        Select = new[] { "HotelName" }
    };

results = indexClient.Documents.Search<Hotel>("motel", parameters);

WriteDocuments(results);
```

Ebben az esetben a "Motel" szó teljes indexét keressük bármely kereshető mezőben, és csak a (z) `Select` paraméterben megadott szállodai neveket szeretnénk beolvasni. Az eredmények a következők:

    Name: Secret Point Motel

    Name: Twin Dome Motel

A következő lekérdezés valamivel érdekesebb.  Olyan szállodákat szeretnénk találni, amelyeknek az ára kevesebb mint $100, és csak a szállodai azonosítót és a leírást kell visszaadnia:

```csharp
parameters =
    new SearchParameters()
    {
        Filter = "Rooms/any(r: r/BaseRate lt 100)",
        Select = new[] { "HotelId", "Description" }
    };

results = indexClient.Documents.Search<Hotel>("*", parameters);

WriteDocuments(results);
```

Ez a lekérdezés egy OData `$filter` `Rooms/any(r: r/BaseRate lt 100)`kifejezést használ az indexben lévő dokumentumok szűréséhez. Ez a [bármely operátort](https://docs.microsoft.com/azure/search/search-query-odata-collection-operators) használja a "BaseRate lt 100" a szobák gyűjtemény minden elemébe való alkalmazásához. A Azure Search által támogatott OData szintaxissal kapcsolatos további információkért tekintse [](https://docs.microsoft.com/azure/search/query-odata-filter-orderby-syntax)meg a következőt:.

A lekérdezés eredményei:

    HotelId: 1
    Description: The hotel is ideally located on the main commercial artery of the city in the heart of New York...

    HotelId: 2
    Description: The hotel is situated in a nineteenth century plaza, which has been expanded and renovated to...

A következő lépésben meg szeretnénk találni a legutóbb felújított legfelső szintű szállodákat, és megmutatjuk a szálloda nevét és utolsó felújítási dátumát. A kód itt látható: 

```csharp
parameters =
    new SearchParameters()
    {
        OrderBy = new[] { "LastRenovationDate desc" },
        Select = new[] { "HotelName", "LastRenovationDate" },
        Top = 2
    };

results = indexClient.Documents.Search<Hotel>("*", parameters);

WriteDocuments(results);
```

Ebben az esetben a `OrderBy` OData szintaxist használjuk a `lastRenovationDate desc`paraméter megadásához. A 2 érték `Top` megadásával biztosítjuk, hogy csak az első két dokumentumot kapja meg. Ahogy korábban is, beállítjuk `Select` , hogy mely mezőket adja vissza.

Az eredmények a következők:

    Name: Fancy Stay        Last renovated on: 6/27/2010 12:00:00 AM +00:00
    Name: Roach Motel       Last renovated on: 4/28/1982 12:00:00 AM +00:00

Végül a "Hotel" szót megegyező összes Hotel nevét meg szeretnénk találni:

```csharp
parameters = new SearchParameters()
{
    SearchFields = new[] { "HotelName" }
};
results = indexClient.Documents.Search<Hotel>("hotel", parameters);

WriteDocuments(results);
```

És itt vannak az eredmények, amelyek tartalmazzák az összes mezőt, mivel nem adjuk meg `Select` a tulajdonságot:

    HotelId: 3
    Name: Triple Landscape Hotel
    ...

Ez a lépés befejezi az oktatóanyagot, de nem áll le itt. \* * A következő lépések további forrásokat biztosítanak a Azure Search megismeréséhez.

## <a name="next-steps"></a>További lépések
* Nézze át a [.NET SDK](https://docs.microsoft.com/dotnet/api/microsoft.azure.search) és a [REST API](https://docs.microsoft.com/rest/api/searchservice/) referenciáit.
* Tekintse át az [elnevezési konvenciókat](https://docs.microsoft.com/rest/api/searchservice/Naming-rules) a különböző objektumok elnevezési szabályainak megismeréséhez.
* Tekintse át a Azure Search [támogatott](https://docs.microsoft.com/rest/api/searchservice/Supported-data-types) adattípusait.
