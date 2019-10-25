---
title: Az Azure Cognitive Search használata .NET-alkalmazásokból
titleSuffix: Azure Cognitive Search
description: Ismerje meg, hogyan használható az Azure Cognitive Search egy .NET- C# alkalmazásban a és a .net SDK használatával. A kód alapú feladatok közé tartozik a szolgáltatáshoz való kapcsolódás, a tartalom indexelése és az index lekérdezése.
manager: nitinme
author: brjohnstmsft
ms.author: brjohnst
ms.devlang: dotnet
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: e1903b3b33e1dde5178fadbc37feee191a2eaacd
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/23/2019
ms.locfileid: "72792125"
---
# <a name="how-to-use-azure-cognitive-search-from-a-net-application"></a>Az Azure Cognitive Search használata .NET-alkalmazásokból

Ebből a cikkből megtudhatja, hogyan hozhatja létre és futtathatja az [Azure Cognitive Search .net SDK](https://aka.ms/search-sdk)-t. Az Azure Cognitive Search használatával a .NET SDK-val széles körű keresési élményt valósíthat meg az alkalmazásban.

## <a name="whats-in-the-azure-cognitive-search-sdk"></a>Az Azure Cognitive Search SDK bemutatása
Az SDK néhány ügyféloldali kódtárat tartalmaz, amelyek lehetővé teszik az indexek, az adatforrások, az indexelő és a szinonimák leképezésének kezelését, valamint dokumentumok feltöltését és kezelését, valamint lekérdezések végrehajtását anélkül, hogy a HTTP-és JSON-adatokkal kellene foglalkoznia. Ezek az ügyféloldali kódtárak mind NuGet-csomagként vannak elosztva.

A fő NuGet-csomag `Microsoft.Azure.Search`, amely egy olyan meta-csomag, amely az összes többi csomagot függőségként tartalmazza. Akkor használja ezt a csomagot, ha csak most kezdi el, vagy ha tudja, hogy az alkalmazásnak szüksége lesz az Azure Cognitive Search összes funkciójának használatára.

Az SDK-ban található egyéb NuGet-csomagok a következők:
 
  - `Microsoft.Azure.Search.Data`: akkor használja ezt a csomagot, ha .NET-alkalmazást fejleszt az Azure Cognitive Search használatával, és csak az indexekben lévő dokumentumok lekérdezésére vagy frissítésére van szükség. Ha indexeket, szinonimákat vagy más szolgáltatási szintű erőforrásokat is létre kell hoznia vagy frissítenie, használja helyette a `Microsoft.Azure.Search` csomagot.
  - `Microsoft.Azure.Search.Service`: akkor használja ezt a csomagot, ha a .NET-ben automatizálást fejleszt az Azure Cognitive Search indexek, a szinonimák, az indexelő, az adatforrások vagy más szolgáltatási szintű erőforrások kezeléséhez. Ha csak az indexekben lévő dokumentumokat kell lekérdezni vagy frissítenie, használja helyette a `Microsoft.Azure.Search.Data` csomagot. Ha az Azure Cognitive Search összes funkciója szükséges, használja helyette a `Microsoft.Azure.Search` csomagot.
  - `Microsoft.Azure.Search.Common`: az Azure Cognitive Search .NET-kódtárak számára szükséges általános típusok. Ezt a csomagot nem szükséges közvetlenül az alkalmazásban használni. Csak függőségként használható.

A különböző ügyféloldali kódtárak olyan osztályokat határoznak meg, mint a `Index`, a `Field`és a `Document`, valamint a `Documents.Search` és `SearchServiceClient` osztályok `Indexes.Create` és `SearchIndexClient` műveletei. Ezek az osztályok a következő névterekben vannak rendszerezve:

* [Microsoft. Azure. Search](https://docs.microsoft.com/dotnet/api/microsoft.azure.search)
* [Microsoft. Azure. Search. models](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models)

Ha visszajelzést szeretne küldeni az SDK jövőbeli frissítéséről, tekintse meg a [Visszajelzés oldalt](https://feedback.azure.com/forums/263029-azure-search/) , vagy hozzon létre egy problémát a [githubon](https://github.com/azure/azure-sdk-for-net/issues) , és nevezze el az "Azure Cognitive Search" kifejezést a probléma címében.

A .NET SDK az [Azure Cognitive Search REST API](https://docs.microsoft.com/rest/api/searchservice/)`2019-05-06` verzióját támogatja. Ebben a verzióban az Azure-Blobok indexelése során az [összetett típusok](search-howto-complex-data-types.md), az [AI](cognitive-search-concept-intro.md)-bővítés, az [automatikus kiegészítés](https://docs.microsoft.com/rest/api/searchservice/autocomplete)és a [JsonLines-elemzési mód](search-howto-index-json-blobs.md) támogatása szerepel. 

Ez az SDK nem támogatja a [felügyeleti műveleteket](https://docs.microsoft.com/rest/api/searchmanagement/) , például a keresési szolgáltatások létrehozását és méretezését, valamint az API-kulcsok kezelését. Ha a keresési erőforrásokat egy .NET-alkalmazásból szeretné kezelni, használhatja az [Azure Cognitive Search .net Management SDK](https://aka.ms/search-mgmt-sdk)-t.

## <a name="upgrading-to-the-latest-version-of-the-sdk"></a>Frissítés az SDK legújabb verziójára
Ha már használja az Azure Cognitive Search .NET SDK egy régebbi verzióját, és az általánosan elérhető legújabb verzióra szeretne frissíteni, [Ez a cikk](search-dotnet-sdk-migration-version-9.md) ismerteti, hogyan.

## <a name="requirements-for-the-sdk"></a>Az SDK követelményei
1. Visual Studio 2017 vagy újabb verzió.
2. Saját Azure Cognitive Search szolgáltatás. Az SDK használatához szüksége lesz a szolgáltatás nevére és egy vagy több API-kulcsra. [A portálon létrehozott szolgáltatás](search-create-service-portal.md) a következő lépésekkel segíti Önt.
3. Töltse le az Azure Cognitive Search .NET SDK [NuGet csomagot](https://www.nuget.org/packages/Microsoft.Azure.Search) a Visual Studióban a "NuGet-csomagok kezelése" használatával. Ha csak a funkció egy részhalmazára van szüksége, keresse meg a NuGet.org lévő `Microsoft.Azure.Search` (vagy a fenti csomagok egyikét).

Az Azure Cognitive Search .NET SDK támogatja a .NET-keretrendszer 4.5.2-es és újabb verzióit, valamint a .NET Core 2,0-es és újabb verzióit.

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

Ezt a lépésről lépésre mutatjuk be. Először létre kell hozni egy új `SearchServiceClient`. Ez az objektum lehetővé teszi az indexek kezelését. A létrehozáshoz meg kell adnia az Azure Cognitive Search szolgáltatás nevét, valamint egy felügyeleti API-kulcsot. Ezt az információt megadhatja a [minta alkalmazás](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowTo)`appsettings.json` fájljában.

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
> Ha helytelen kulcsot ad meg (például egy lekérdezési kulcsot, amelynél rendszergazdai kulcs szükséges), akkor a `SearchServiceClient` a "tiltott" hibaüzenettel `CloudException`, amikor első alkalommal hívja meg a műveletet, például `Indexes.Create`. Ha ez történik, tekintse meg az API-kulcsát.
> 
> 

A következő néhány sorban meghívja a "Hotels" nevű index létrehozását, ha már létezik, először törölje azt. Ezeket a metódusokat egy kicsit később fogjuk átjárni.

```csharp
Console.WriteLine("{0}", "Deleting index...\n");
DeleteIndexIfExists(indexName, serviceClient);

Console.WriteLine("{0}", "Creating index...\n");
CreateIndex(indexName, serviceClient);
```

Ezután az indexet fel kell tölteni. Az index feltöltéséhez szüksége lesz egy `SearchIndexClient`ra. Az egyiket kétféleképpen lehet megszerezni: a létrehozásával vagy a `SearchServiceClient``Indexes.GetClient` meghívásával. Az utóbbit az egyszerűség kedvéért használjuk.

```csharp
ISearchIndexClient indexClient = serviceClient.Indexes.GetClient(indexName);
```

> [!NOTE]
> Egy tipikus keresési alkalmazásban az indexek kezelését és a populációt a keresési lekérdezések egy külön összetevője is kezeli. `Indexes.GetClient` kényelmes az indexek feltöltéséhez, mert a további `SearchCredentials`megadásával gondot okoz. Ezt azon rendszergazdai kulcs átadásával hajtja végre, amelyet a `SearchServiceClient` elemnek az új `SearchIndexClient` objektumban történő létrehozásakor használt. A lekérdezéseket végrehajtó alkalmazás részeként azonban jobb, ha közvetlenül hozza létre a `SearchIndexClient`, így a lekérdezési kulcs átadható, amely csak az adatolvasást teszi lehetővé egy rendszergazdai kulcs helyett. Ez összhangban van a legalacsonyabb jogosultsági szint elvével, és segít az alkalmazás biztonságosabbá tételében. A rendszergazdai kulcsokról és a lekérdezési kulcsokról [itt](https://docs.microsoft.com/rest/api/searchservice/#authentication-and-authorization)talál további információt.
> 
> 

Most, hogy már van egy `SearchIndexClient`, feltöltheti az indexet. Az indexek populációját egy másik módszer hajtja végre, amelyet később fogunk eljárni.

```csharp
Console.WriteLine("{0}", "Uploading documents...\n");
UploadDocuments(indexClient);
```

Végül végrehajtunk néhány keresési lekérdezést, és megjeleníti az eredményeket. Ezúttal egy másik `SearchIndexClient`használunk:

```csharp
ISearchIndexClient indexClientForQueries = CreateSearchIndexClient(indexName, configuration);

RunQueries(indexClientForQueries);
```

Később részletesebben is szemügyre vesszük a `RunQueries` metódust. Az új `SearchIndexClient`létrehozásához a következő kód szükséges:

```csharp
private static SearchIndexClient CreateSearchIndexClient(string indexName, IConfigurationRoot configuration)
{
    string searchServiceName = configuration["SearchServiceName"];
    string queryApiKey = configuration["SearchServiceQueryApiKey"];

    SearchIndexClient indexClient = new SearchIndexClient(searchServiceName, indexName, new SearchCredentials(queryApiKey));
    return indexClient;
}
```

Ezúttal a lekérdezési kulcsot használjuk, mert nincs szükségünk írási hozzáférésre az indexhez. Ezt az információt megadhatja a [minta alkalmazás](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowTo)`appsettings.json` fájljában.

Ha az alkalmazást érvényes szolgáltatásnév és API-kulcsok használatával futtatja, a kimenetnek a következő példához hasonlóan kell kinéznie: (egyes konzolok kimenete "..." lett lecserélve. illusztrációs célokra.)

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

A következő lépés a `Main`által meghívott módszerek alaposabb áttekintése.

### <a name="creating-an-index"></a>Index létrehozása
`SearchServiceClient`létrehozása után `Main` törli a "Hotels" indexet, ha már létezik. Ezt a törlést a következő módszer hajtja végre:

```csharp
private static void DeleteIndexIfExists(string indexName, SearchServiceClient serviceClient)
{
    if (serviceClient.Indexes.Exists(indexName))
    {
        serviceClient.Indexes.Delete(indexName);
    }
}
```

Ez a metódus az adott `SearchServiceClient` használatával ellenőrizze, hogy létezik-e az index, és ha igen, törölje.

> [!NOTE]
> A jelen cikkben szereplő mintakód az Azure Cognitive Search .NET SDK egyszerűségének szinkron módszereit használja. Azt javasoljuk, hogy a méretezhetőség és a gyors válaszadás érdekében használja saját alkalmazásaiban az aszinkron módszereket. Például a fenti metódusban a `Exists` és a `Delete`helyett a `ExistsAsync` és a `DeleteAsync` is használható.
> 
> 

Ezután a `Main` létrehoz egy új "Hotels" indexet a metódus meghívásával:

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

Ez a metódus új `Index` objektumot hoz létre az új index sémáját meghatározó `Field` objektumok listájával. Minden mező rendelkezik egy névvel, adattípussal és számos olyan attribútummal, amely meghatározza a keresési viselkedését. A `FieldBuilder` osztály reflexió használatával hozza létre az indexhez tartozó `Field` objektumok listáját az adott `Hotel` Model osztály nyilvános tulajdonságainak és attribútumainak vizsgálatával. Részletesebben is megvizsgáljuk a `Hotel` osztályt később.

> [!NOTE]
> A `Field` objektumok listáját mindig közvetlenül a `FieldBuilder` használata helyett lehet létrehozni, ha szükséges. Előfordulhat például, hogy nem szeretné használni a modell osztályt, vagy egy meglévő modell osztályt kell használnia, amelyet attribútumok hozzáadásával nem kíván módosítani.
>
> 

A mezőkön kívül pontozási profilokat, javaslatokat vagy CORS lehetőségeket is hozzáadhat az indexhez (ezek a paraméterek kimaradnak a mintából a rövidség kedvéért). Az index objektumról és annak összetevőiről az [SDK-referenciában](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.index), valamint az [Azure Cognitive Search REST API-referenciában](https://docs.microsoft.com/rest/api/searchservice/)talál további információt.

### <a name="populating-the-index"></a>Az index feltöltése
A `Main` következő lépése az újonnan létrehozott indexet tölti fel. Az index populációja a következő módon történik: (néhány kód helyébe "..." illusztrációs célokra.  Tekintse meg a teljes adatpopulációs kód teljes minta megoldását.)

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

Ez a metódus négy részből áll. Az első három `Hotel` objektumból álló tömböt hoz létre, amelyek mindegyike 3 `Room` objektummal fog szolgálni, amely az indexbe feltöltendő bemeneti adatok. Ezek az adathalmazok az egyszerűség kedvéért nehezen kódoltak. A saját alkalmazásban az adatok valószínűleg egy külső adatforrásból származnak, például egy SQL-adatbázisból.

A második rész a dokumentumokat tartalmazó `IndexBatch` hoz létre. Megadhatja azt a műveletet, amelyet a kötegre alkalmazni szeretne a létrehozáskor, ebben az esetben a `IndexBatch.Upload`meghívásával. A köteget ezután a `Documents.Index` metódus feltölti az Azure Cognitive Search indexbe.

> [!NOTE]
> Ebben a példában csak dokumentumokat töltünk fel. Ha szeretné egyesíteni a módosításokat a meglévő dokumentumokban, vagy törölhet dokumentumokat, akkor `IndexBatch.Merge`, `IndexBatch.MergeOrUpload`vagy `IndexBatch.Delete` meghívásával hozhat létre kötegeket. Egy kötegben különböző műveleteket is összekeverheti, ha meghívja a `IndexBatch.New`t, amely `IndexAction` objektumok gyűjteményét veszi igénybe, amelyek mindegyike megadja az Azure Cognitive Search egy adott művelet végrehajtásához a dokumentumon. Mindegyik `IndexAction` saját művelettel hozhatja létre úgy, hogy meghívja a megfelelő metódust, például `IndexAction.Merge`, `IndexAction.Upload`stb.
> 
> 

A metódus harmadik része egy olyan Catch blokk, amely egy fontos, az indexeléshez szükséges hibát kezel. Ha az Azure Cognitive Search-szolgáltatás nem tudja indexelni a köteg egyes dokumentumait, akkor a `Documents.Index`a `IndexBatchException`. Ez a kivétel akkor fordulhat elő, ha a dokumentumok indexelése során a szolgáltatás nagy terhelés alatt áll. **Javasoljuk, hogy a kódban explicit módon kezelje ezt az esetet.** Azon dokumentumok esetében, ahol az indexelés meghiúsult, elhalaszthatja azt, majd később újra megpróbálkozhat az indexeléssel, vagy a mintának megfelelően naplózhatja azt, és folytathatja a munkáját, esetleg – az alkalmazás adatkonzisztencia-követelményeitől függően – más műveletbe kezdhet.

> [!NOTE]
> A [`FindFailedActionsToRetry`](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.indexbatchexception.findfailedactionstoretry) metódussal létrehozhat egy új köteget, amely csak azokat a műveleteket tartalmazza, amelyeken a `Index`korábbi hívása meghiúsult. Megtudhatja, hogyan kell megfelelően használni [a StackOverflow-on](https://stackoverflow.com/questions/40012885/azure-search-net-sdk-how-to-use-findfailedactionstoretry).
>
>

Végül a `UploadDocuments` metódus két másodpercig késlelteti. Az indexelés aszinkron módon történik az Azure Cognitive Search szolgáltatásban, így a minta alkalmazásnak várnia kell egy rövid időre, hogy a dokumentumok kereshetőek legyenek. Ilyen mértékű késleltetésre kizárólag demók, tesztek és mintaalkalmazások esetében van szükség.

<a name="how-dotnet-handles-documents"></a>

#### <a name="how-the-net-sdk-handles-documents"></a>A .NET SDK dokumentumkezelési módszere
Előfordulhat, hogy kíváncsi, hogy az Azure Cognitive Search .NET SDK hogyan tölthet fel egy felhasználó által definiált osztály példányait, például `Hotel` az indexbe. A kérdés megválaszolásához tekintsük át a `Hotel` osztályt:

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

Az első dolog, hogy a `Hotel` osztály minden nyilvános tulajdonságának neve az index definíciójában azonos nevű mezőre lesz leképezve. Ha azt szeretné, hogy az egyes mezők kisbetűvel kezdődjön ("teve-eset"), az SDK-val az osztály `[SerializePropertyNamesAsCamelCase]` attribútumával automatikusan leképezheti a tulajdonságok nevét a teve-esetre. Ez a forgatókönyv gyakori olyan .NET-alkalmazásokban, amelyek adatkötést hajtanak végre, ha a célként megadott séma kívül esik az alkalmazás fejlesztői felügyeletén anélkül, hogy meg kellene sérteni a "Pascal Case" elnevezési irányelveket a .NET-ben.

> [!NOTE]
> Az Azure Cognitive Search .NET SDK a [NewtonSoft JSON.net](https://www.newtonsoft.com/json/help/html/Introduction.htm) -függvénytár használatával szerializálja és deszerializálja az egyéni modell OBJEKTUMOKAT a JSON-be és a-ból. A szerializálás szükség szerint testre szabható. További információ: [Egyéni szerializálás a JSON.net](#JsonDotNet).
> 
> 

A második lehetőség, hogy az egyes tulajdonságok olyan attribútumokkal vannak díszítve, mint a `IsFilterable`, a `IsSearchable`, a `Key`és a `Analyzer`. Ezek az attribútumok közvetlenül [egy Azure Cognitive Search index megfelelő mező attribútumaira](https://docs.microsoft.com/rest/api/searchservice/create-index#request)mutatnak. A `FieldBuilder` osztály ezeket a tulajdonságokat használja az indexhez tartozó mező-definíciók összeállításához.

A `Hotel` osztály harmadik fontos dologa a nyilvános tulajdonságok adattípusa. Az indexdefinícióban a rendszer ezen .NET tulajdonságtípusokat képezi le a nekik megfelelő mezőtípusokra. Például a rendszer a `Edm.String` típusú `Category` szöveges tulajdonságot a `category` mezőbe képezi le. Hasonló típusú leképezések vannak `bool?`, `Edm.Boolean`, `DateTimeOffset?`és `Edm.DateTimeOffset` között, és így tovább. A típus-hozzárendelésre vonatkozó konkrét szabályokat az [Azure Cognitive Search .net SDK dokumentációjának](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.documentsoperationsextensions.get)`Documents.Get` metódusával dokumentálja. A `FieldBuilder` osztály gondoskodik a leképezésről, de hasznos lehet az esetleges szerializálási problémák elhárításához is.

Észrevette a `SmokingAllowed` tulajdonságot?

```csharp
[JsonIgnore]
public bool? SmokingAllowed => (Rooms != null) ? Array.Exists(Rooms, element => element.SmokingAllowed == true) : (bool?)null;
```

A tulajdonság `JsonIgnore` attribútuma azt jelzi, hogy a `FieldBuilder` nem szerializálja mezőként az indexbe.  Ez nagyszerű módja annak, hogy olyan ügyféloldali számított tulajdonságokat hozzon létre, amelyeket segítőként használhat az alkalmazásban.  Ebben az esetben a `SmokingAllowed` tulajdonság azt jelzi, hogy a `Rooms` gyűjtemény bármely `Room` engedélyezi-e a dohányzást.  Ha az összes hamis, az azt jelzi, hogy a teljes szálloda nem teszi lehetővé a dohányzást.

Bizonyos tulajdonságok, például a `Address` és a `Rooms` .NET-osztályok példányai.  Ezek a tulajdonságok összetettebb adatstruktúrákat jelentenek, és ennek eredményeképpen az indexben [összetett adattípusú](https://docs.microsoft.com/azure/search/search-howto-complex-data-types) mezőket igényelnek.

A `Address` tulajdonság több értéket jelöl a `Address` osztályban, amelyet alább határozhat meg:

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
> Az Azure Cognitive Search .NET SDK a `Document` osztály használatával is támogatja a dinamikusan begépelt dokumentumokat, amely a mezőnevek értékének kulcs/érték leképezése. Ez olyan helyzetekben hasznos, ha például a tervezés időpontjában az indexséma még nem ismert, illetve ha az adott modellosztályokhoz történő kötés nehézkes volna. Az SDK-ban lévő összes, dokumentumokkal foglalkozó módszer a `Document` osztállyal kompatibilis túlterhelésekkel rendelkezik, valamint olyan szigorú típusmegadású túlterhelésekkel, amelyek általános típusú paramétert vesznek fel. Ebben az oktatóanyagban csak az utóbbit használjuk a mintakód. A [`Document` osztály](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.document) a `Dictionary<string, object>`tól örökli.
> 
>

**Miért használjon nullázható adattípusokat?**

Ha egy Azure Cognitive Search indexhez rendeli hozzá a saját modell osztályait, javasoljuk, hogy a `bool` és az `int` típusú tulajdonságokat a rendszer nullára adja (például `bool`helyett `bool?`). Nem nullázható tulajdonság használatakor **garantálnia** kell, hogy az index egyetlen dokumentuma sem tartalmaz az adott mezőben null értéket. Sem az SDK, sem az Azure Cognitive Search szolgáltatás nem fog segíteni ennek betartatásában.

Ennek nem csupán elméleti jelentősége van: képzeljünk el például egy olyan alkalmazási helyzetet, ahol egy `Edm.Int32` típusú, meglévő indexhez új mezőt kell hozzáadnunk. Az index definíciójának frissítése után az összes dokumentum NULL értékkel fog rendelkezni az új mezőben (mivel az összes típus null értékű az Azure Cognitive Search). Ha ezt követően egy modellosztályt úgy alkalmaz, hogy ehhez a mezőhöz nem nullázható `int` tulajdonságot ad meg, a dokumentumok lekérdezésének megkísérlésekor egy ehhez hasonló `JsonSerializationException` választ kap:

    Error converting value {null} to type 'System.Int32'. Path 'IntValue'.

Ezért javasoljuk, hogy a modellosztályokban nullázható értéktípusokat használjon.

<a name="JsonDotNet"></a>

#### <a name="custom-serialization-with-jsonnet"></a>Egyéni szerializálás a JSON.NET
Az SDK JSON.NET használ a dokumentumok szerializálásához és deszerializálásához. Szükség szerint testre szabhatja a szerializálást és a deszerializálást, ha meghatározza a saját `JsonConverter` vagy `IContractResolver`. További információkért tekintse meg a [JSON.net dokumentációját](https://www.newtonsoft.com/json/help/html/Introduction.htm). Ez akkor lehet hasznos, ha egy meglévő modell osztályt szeretne alkalmazkodni az alkalmazásból az Azure Cognitive Search és más fejlettebb forgatókönyvek használatához. Egyéni szerializálással például a következőket teheti:

* Belefoglalhatja vagy kizárhatja a modell osztályának bizonyos tulajdonságait a dokumentum mezőinek tárolásához.
* A kód és a mezőnevek nevei közötti leképezés az indexben.
* Létrehozhat olyan egyéni attribútumokat, amelyek a tulajdonságok megfeleltetéséhez használhatók a dokumentumok mezőihez.

A GitHubon található Azure Cognitive Search .NET SDK-ban az egyéni szerializálás megvalósítására példát talál. [Ez a mappa](https://github.com/Azure/azure-sdk-for-net/tree/4f6f4e4c90200c1b0621c4cead302a91e89f2aba/sdk/search/Microsoft.Azure.Search/tests/Tests/Models)jó kiindulási pont. Az egyéni szerializálási tesztek által használt osztályokat tartalmazza.

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

Ez a metódus minden alkalommal egy új `SearchParameters` objektumot hoz létre, amikor lekérdezést hajt végre. Ez az objektum a lekérdezés további beállításainak megadására szolgál, például rendezés, szűrés, lapozás és aspektusok. Ezzel a módszerrel a különböző lekérdezésekhez a `Filter`, `Select`, `OrderBy`és `Top` tulajdonságot állítjuk be. A `SearchParameters` összes tulajdonsága dokumentálva [van](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.searchparameters).

A következő lépés a keresési lekérdezés tényleges végrehajtása. A keresés futtatása a `Documents.Search` metódus használatával történik. Minden lekérdezés esetében átadjuk a keresett szöveget karakterláncként (vagy `"*"`, ha nincs keresési szöveg), valamint a korábban létrehozott keresési paramétereket. Azt is megadhatjuk, hogy az `Hotel` mint a `Documents.Search`Type paraméterét, amely arra utasítja az SDK-t, hogy a keresési eredményekben lévő dokumentumokat deszerializálja `Hotel`típusú objektumokra.

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

Ebben az esetben a "Motel" szó teljes indexét keressük bármely kereshető mezőben, és a `Select` paraméter által meghatározottak szerint csak a szállodai neveket szeretnénk beolvasni. Az eredmények a következők:

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

Ez a lekérdezés egy OData `$filter` kifejezést használ `Rooms/any(r: r/BaseRate lt 100)`az indexben lévő dokumentumok szűréséhez. Ez a [bármely operátort](https://docs.microsoft.com/azure/search/search-query-odata-collection-operators) használja a "BaseRate lt 100" a szobák gyűjtemény minden elemébe való alkalmazásához. Az Azure Cognitive Search által támogatott OData szintaxissal kapcsolatban [itt](https://docs.microsoft.com/azure/search/query-odata-filter-orderby-syntax)talál további információt.

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

Ebben az esetben a OData szintaxist használjuk a `OrderBy` paraméter `lastRenovationDate desc`ként való megadásához. A 2. számú `Top` is beállítjuk, hogy csak az első két dokumentum legyen bebiztosítva. Ahogy korábban is, beállítjuk, hogy a `Select` mely mezőket adja vissza.

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

És itt vannak az eredmények, amelyek tartalmazzák az összes mezőt, mivel nem adta meg a `Select` tulajdonságot:

    HotelId: 3
    Name: Triple Landscape Hotel
    ...

Ez a lépés befejezi az oktatóanyagot, de nem áll le itt. \* * A következő lépések további forrásokat biztosítanak az Azure Cognitive Search megismeréséhez.

## <a name="next-steps"></a>Következő lépések
* Nézze át a [.NET SDK](https://docs.microsoft.com/dotnet/api/microsoft.azure.search) és a [REST API](https://docs.microsoft.com/rest/api/searchservice/) referenciáit.
* Tekintse át az [elnevezési konvenciókat](https://docs.microsoft.com/rest/api/searchservice/Naming-rules) a különböző objektumok elnevezési szabályainak megismeréséhez.
* Tekintse át az Azure Cognitive Search [támogatott adattípusait](https://docs.microsoft.com/rest/api/searchservice/Supported-data-types) .
