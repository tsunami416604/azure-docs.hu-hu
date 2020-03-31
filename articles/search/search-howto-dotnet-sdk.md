---
title: Az Azure Cognitive Search használata a .NET-ben
titleSuffix: Azure Cognitive Search
description: Ismerje meg, hogyan használhatja az Azure Cognitive Search egy .NET alkalmazás a C# és a .NET SDK használatával. A kódalapú feladatok közé tartozik a szolgáltatáshoz való csatlakozás, az indextartalom és az index lekérdezése.
manager: nitinme
author: brjohnstmsft
ms.author: brjohnst
ms.devlang: dotnet
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: b31a4e40c1e9095499faf265673ab4213ad6bde0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79283068"
---
# <a name="how-to-use-azure-cognitive-search-from-a-net-application"></a>Az Azure Cognitive Search használata .NET alkalmazásból

Ez a cikk egy forgatókönyv az Azure [Cognitive Search .NET SDK](https://aka.ms/search-sdk)használatával. A .NET SDK használatával gazdag keresési élményt valósíthat meg az alkalmazásban az Azure Cognitive Search használatával.

## <a name="whats-in-the-azure-cognitive-search-sdk"></a>Az Azure Cognitive Search SDK újdonságai
Az SDK néhány ügyfélkódtárakból áll, amelyek lehetővé teszik az indexek, adatforrások, indexelők és szinonimaleképezések kezelését, valamint a dokumentumok feltöltését és kezelését, valamint a lekérdezések végrehajtását anélkül, hogy a HTTP és a JSON részleteivel kellene foglalkozniuk. Ezek az ügyfélkönyvtárak nuget csomagokként vannak terjesztve.

A fő NuGet `Microsoft.Azure.Search`csomag , amely egy metacsomag, amely tartalmazza az összes többi csomagot függőségként. Akkor használja ezt a csomagot, ha még csak most kezdte el, vagy ha tudja, hogy az alkalmazásnak szüksége lesz az Azure Cognitive Search összes szolgáltatására.

Az SDK többi NuGet csomagja:
 
  - `Microsoft.Azure.Search.Data`: Használja ezt a csomagot, ha az Azure Cognitive Search használatával .NET alkalmazást fejleszt, és csak az indexekben lévő dokumentumokat kell lekérdeznie vagy frissítenie. Ha indexeket, szinonimátleképezéseket vagy más szolgáltatásszintű erőforrásokat `Microsoft.Azure.Search` is létre kell hoznia vagy frissítenie, használja inkább a csomagot.
  - `Microsoft.Azure.Search.Service`: Használja ezt a csomagot, ha a .NET automatizálási fejlesztése az Azure Cognitive Search-indexek, szinonimatérképek, indexelők, adatforrások vagy más szolgáltatásszintű erőforrások kezeléséhez. Ha csak az indexekben lévő dokumentumokat kell lekérdeznie vagy frissítenie, használja inkább a `Microsoft.Azure.Search.Data` csomagot. Ha az Azure Cognitive Search összes funkciójára `Microsoft.Azure.Search` szüksége van, használja inkább a csomagot.
  - `Microsoft.Azure.Search.Common`: Az Azure Cognitive Search .NET kódtárai hoz szükséges gyakori típusok. Ezt a csomagot nem kell közvetlenül az alkalmazásban használnia. Ez csak azt jelentette, hogy kell használni, mint a függőség.

A különböző ügyfélkönyvtárak olyan `Index` `Field`osztályokat `Document`határoznak meg, `Indexes.Create` mint `Documents.Search` a `SearchServiceClient` `SearchIndexClient` , és a , valamint a hasonló műveleteket, valamint az osztályokat. Ezek az osztályok a következő névterekbe vannak rendezve:

* [Microsoft.Azure.Keresés](https://docs.microsoft.com/dotnet/api/microsoft.azure.search)
* [Microsoft.Azure.Search.Modellek](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models)

Ha visszajelzést szeretne adni az SDK jövőbeli frissítéséhez, tekintse meg [visszajelzési oldalunkat,](https://feedback.azure.com/forums/263029-azure-search/) vagy hozzon létre egy problémát a [GitHubon,](https://github.com/azure/azure-sdk-for-net/issues) és megemlítse az "Azure Cognitive Search" kifejezést a probléma címében.

A .NET SDK `2019-05-06` támogatja az [Azure Cognitive Search REST API verzióját.](https://docs.microsoft.com/rest/api/searchservice/) Ez a verzió támogatja az [összetett típusok,](search-howto-complex-data-types.md) [AI-bővítés](cognitive-search-concept-intro.md), [automatikus kiegészítés,](https://docs.microsoft.com/rest/api/searchservice/autocomplete)és [jsonlines elemzési mód](search-howto-index-json-blobs.md) indexelése során az Azure Blobs. 

Ez az SDK nem támogatja [a felügyeleti műveleteket,](https://docs.microsoft.com/rest/api/searchmanagement/) például a keresési szolgáltatások létrehozását és méretezését, valamint az API-kulcsok kezelését. Ha a keresési erőforrásokat egy .NET alkalmazásból kell kezelnie, használhatja az [Azure Cognitive Search .NET Management SDK -t.](https://aka.ms/search-mgmt-sdk)

## <a name="upgrading-to-the-latest-version-of-the-sdk"></a>Frissítés az SDK legújabb verziójára
Ha már használja az Azure Cognitive Search .NET SDK egy régebbi verzióját, és a legújabb általánosan elérhető verzióra szeretne frissíteni, ez a [cikk](search-dotnet-sdk-migration-version-9.md) ismerteti, hogyan.

## <a name="requirements-for-the-sdk"></a>Az SDK-ra vonatkozó követelmények
1. Visual Studio 2017-es vagy újabb verzió.
2. Saját Azure Cognitive Search szolgáltatás. Az SDK használatához szüksége lesz a szolgáltatás nevére és egy vagy több API-kulcsra. [Hozzon létre egy szolgáltatást a portálon](search-create-service-portal.md) segít átsegíteni ezeket a lépéseket.
3. Töltse le az Azure Cognitive Search .NET SDK [NuGet csomagot](https://www.nuget.org/packages/Microsoft.Azure.Search) a Visual Studio "NuGet csomagok kezelése" használatával. Csak keresse meg `Microsoft.Azure.Search` a csomag nevét NuGet.org (vagy a fenti csomagnevek egyikén, ha csak a funkció egy részhalmazára van szüksége).

Az Azure Cognitive Search .NET SDK támogatja a .NET Framework 4.5.2-es és újabb, valamint a .NET Core 2.0-s és újabb rendszert célzó alkalmazásokat.

## <a name="core-scenarios"></a>Alapvető forgatókönyvek
A keresési alkalmazásban több dolgot is el kell végeznie. Ebben az oktatóanyagban az alábbi alapvető forgatókönyveket fedjük le:

* Index létrehozása
* Az index feltöltése dokumentumokkal
* Dokumentumok keresése teljes szöveges kereséssel és szűrőkkel

A következő mintakód ezeket az eseteket mutatja be. Nyugodtan használja a kódrészleteket a saját alkalmazásában.

### <a name="overview"></a>Áttekintés
A minta alkalmazás leszünk feltárása létrehoz egy új index nevű "szállodák", feltölti azt néhány dokumentumot, majd végrehajtja néhány keresési lekérdezések. Itt van a fő program, amely bemutatja a teljes folyamat:

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

Lépésről lépésre haladunk át ezen. Először is létre `SearchServiceClient`kell hoznunk egy új . Ez az objektum lehetővé teszi az indexek kezelését. Annak érdekében, hogy egy, meg kell adnia az Azure Cognitive Search szolgáltatás nevét, valamint egy felügyeleti API-kulcs. Ezt az információt a `appsettings.json` [mintaalkalmazás](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowTo)fájljába adhatja meg.

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
> Ha helytelen kulcsot ad meg (például egy olyan lekérdezési `SearchServiceClient` kulcsot, `CloudException` ahol rendszergazdai kulcsra volt szükség), akkor a "Tiltott" `Indexes.Create`hibaüzenet jelenik meg, amikor először hív meg rajta egy műveletmetódust, például . Ha ez történik veled, ellenőrizze az API-kulcsot.
> 
> 

A következő néhány vonal metódusokat hív meg a "hotels" nevű index létrehozásához, és először akkor a törlést, ha már létezik. Ezeket a módszereket egy kicsit később fogjuk végigjárni.

```csharp
Console.WriteLine("{0}", "Deleting index...\n");
DeleteIndexIfExists(indexName, serviceClient);

Console.WriteLine("{0}", "Creating index...\n");
CreateIndex(indexName, serviceClient);
```

Ezután az indexet ki kell tölteni. Az index feltöltéséhez szükségünk lesz `SearchIndexClient`egy . Kétféleképpen lehet beszerezni egyet: megépíteni, `Indexes.GetClient` vagy `SearchServiceClient`a . Az utóbbit a kényelem érdekében használjuk.

```csharp
ISearchIndexClient indexClient = serviceClient.Indexes.GetClient(indexName);
```

> [!NOTE]
> Egy tipikus keresési alkalmazásban az indexkezelést és a sokaságt a keresési lekérdezésektől különálló összetevő is kezelheti. `Indexes.GetClient`kényelmes az index feltöltéséhez, mert megtakarítja a további `SearchCredentials`. Ezt azon rendszergazdai kulcs átadásával hajtja végre, amelyet a `SearchServiceClient` elemnek az új `SearchIndexClient` objektumban történő létrehozásakor használt. Azonban az alkalmazás, amely végrehajtja a lekérdezéseket, akkor `SearchIndexClient` jobb, ha közvetlenül hozza létre, hogy a lekérdezési kulcs, amely csak lehetővé teszi, hogy adatok olvasása helyett egy rendszergazdai kulcs. Ez megfelel a legalacsonyabb jogosultsági szint elvének, és segítségével alkalmazása biztonságosabbá tehető. A rendszergazdai kulcsokról és a lekérdezési kulcsokról [itt](https://docs.microsoft.com/rest/api/searchservice/#authentication-and-authorization)tudhat meg többet.
> 
> 

Most, hogy `SearchIndexClient`van egy, mi lehet benépesíti az indexet. Index népesség történik egy másik módszerrel, hogy mi fog járni később.

```csharp
Console.WriteLine("{0}", "Uploading documents...\n");
UploadDocuments(indexClient);
```

Végül végrehajtunk néhány keresési lekérdezést, és megjelenítjük az eredményeket. Ezúttal egy másik: `SearchIndexClient`

```csharp
ISearchIndexClient indexClientForQueries = CreateSearchIndexClient(indexName, configuration);

RunQueries(indexClientForQueries);
```

Majd egy közelebbi pillantást `RunQueries` a módszer később. Itt van a kód-hoz teremt a új: `SearchIndexClient`

```csharp
private static SearchIndexClient CreateSearchIndexClient(string indexName, IConfigurationRoot configuration)
{
    string searchServiceName = configuration["SearchServiceName"];
    string queryApiKey = configuration["SearchServiceQueryApiKey"];

    SearchIndexClient indexClient = new SearchIndexClient(searchServiceName, indexName, new SearchCredentials(queryApiKey));
    return indexClient;
}
```

Ezúttal lekérdezési kulcsot használunk, mivel nincs szükségünk írási hozzáférésre az indexhez. Ezt az információt a `appsettings.json` [mintaalkalmazás](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowTo)fájljába adhatja meg.

Ha az alkalmazást érvényes szolgáltatásnévvel és API-kulcsokkal futtatja, a kimenetnek a következő példához kell hasonlítania: (Néhány konzolkimenetet a "..." kifejezésre cserélt. illusztrációs célokra.)

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

Az alkalmazás teljes forráskódja a cikk végén található.

Ezután közelebbről megvizsgáljuk az által a `Main`.

### <a name="creating-an-index"></a>Index létrehozása
Létrehozása után `SearchServiceClient` `Main` a , törli a "szállodák" index, ha már létezik. A törlés a következő módszerrel történik:

```csharp
private static void DeleteIndexIfExists(string indexName, SearchServiceClient serviceClient)
{
    if (serviceClient.Indexes.Exists(indexName))
    {
        serviceClient.Indexes.Delete(indexName);
    }
}
```

Ez a módszer `SearchServiceClient` a megadott módszert használja annak ellenőrzésére, hogy az index létezik-e, és ha igen, törölje azt.

> [!NOTE]
> Ebben a cikkben a példakód az Azure Cognitive Search .NET SDK szinkron metódusait használja az egyszerűség kedvéért. Azt javasoljuk, hogy a méretezhetőség és a gyors válaszadás érdekében használja saját alkalmazásaiban az aszinkron módszereket. Például a fenti módszerben `ExistsAsync` használhatja, `Exists` `Delete`és `DeleteAsync` ahelyett, hogy .
> 
> 

Ezután `Main` létrehoz egy új "hotel" indexet a módszer felhívásával:

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

Ez a módszer `Index` új objektumot `Field` hoz létre az új index sémáját meghatározó objektumok listájával. Minden mezőnek van neve, adattípusa és számos attribútuma, amelyek meghatározzák a keresési viselkedését. Az `FieldBuilder` osztály tükröződést használ `Field` az objektumok listájának létrehozásához az indexhez `Hotel` az adott modellosztály nyilvános tulajdonságainak és attribútumainak vizsgálatával. Később közelebbről megnézzük az `Hotel` órát.

> [!NOTE]
> Az objektumok listáját bármikor `Field` létrehozhatja közvetlenül ahelyett, hogy szükség esetén használná. `FieldBuilder` Előfordulhat például, hogy nem szeretne modellosztályt használni, vagy olyan meglévő modellosztályt kell használnia, amelyet attribútumok hozzáadásával nem szeretne módosítani.
>
> 

A mezők mellett pontozási profilokat, javaslatajánlókat vagy CORS-beállításokat is hozzáadhat az indexhez (ezek a paraméterek rövidség ből kimaradnak a mintából). Az Index objektumról és annak alkotó részeiről további információt az [SDK-hivatkozásban,](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.index)valamint az [Azure Cognitive Search REST API-hivatkozásban](https://docs.microsoft.com/rest/api/searchservice/)talál.

### <a name="populating-the-index"></a>Az index feltöltése
A következő `Main` lépés az újonnan létrehozott index feltöltése. Ez az index sokaság a következő módszerrel történik: (Néhány kód helyébe "..." illusztrációs célokra.  Tekintse meg a teljes mintamegoldást a teljes adatpopuláció-kódhoz.)

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

Ez a módszer négy részből áll. Az első létrehoz egy `Hotel` tömb 3 `Room` objektumok mindegyike 3 objektumok, amelyek szolgálnak a bemeneti adatokat feltölteni az indexbe. Ezek az adatok az egyszerűség kedvéért kódoltak. A saját alkalmazásban az adatok valószínűleg egy külső adatforrásból, például egy SQL-adatbázisból származnak.

A második rész `IndexBatch` létrehoz egy dokumentumot tartalmazó részt. A kötegre a létrehozáskor alkalmazni kívánt műveletet, ebben az `IndexBatch.Upload`esetben a hívással adhatja meg. A köteg ezután feltölti az Azure `Documents.Index` Cognitive Search index a módszerrel.

> [!NOTE]
> Ebben a példában csak dokumentumokat töltünk fel. Ha a módosításokat meglévő dokumentumokba szeretné egyesíteni, vagy dokumentumokat `IndexBatch.Merge` `IndexBatch.MergeOrUpload`szeretne `IndexBatch.Delete` törölni, a program a , vagy inkább a kötegeket is létrehozhatja. A különböző műveleteket is keverheti egyetlen `IndexBatch.New`kötegben a `IndexAction` hívással, amely objektumok gyűjteményét veszi igénybe, amelyek mindegyike azt mondja az Azure Cognitive Search-nek, hogy egy adott műveletet hajtson végre egy dokumentumon. A megfelelő `IndexAction` metódus (például, `IndexAction.Merge` `IndexAction.Upload`, , ) meghívásával létrehozhatja mindegyiket saját művelettel.
> 
> 

A módszer harmadik része egy fogási blokk, amely egy fontos hibaesetet kezel az indexeléshez. Ha az Azure Cognitive Search szolgáltatás nem indexeli `IndexBatchException` a kötegben lévő dokumentumok egy részét, a program a program eldobja az ujját. `Documents.Index` Ez a kivétel akkor fordulhat elő, ha a szolgáltatás nagy terhelés alatt indexeli a dokumentumokat. **Javasoljuk ennek az esetnek az explicit módon való kezelését a kódban.** Azon dokumentumok esetében, ahol az indexelés meghiúsult, elhalaszthatja azt, majd később újra megpróbálkozhat az indexeléssel, vagy a mintának megfelelően naplózhatja azt, és folytathatja a munkáját, esetleg – az alkalmazás adatkonzisztencia-követelményeitől függően – más műveletbe kezdhet.

> [!NOTE]
> A [`FindFailedActionsToRetry`](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.indexbatchexception.findfailedactionstoretry) módszerrel létrehozhat egy új köteget, amely csak azokat a `Index`műveleteket tartalmazza, amelyek egy korábbi hívásban sikertelenek. Van egy vita, hogyan kell helyesen használni [a StackOverflow](https://stackoverflow.com/questions/40012885/azure-search-net-sdk-how-to-use-findfailedactionstoretry).
>
>

Végül a `UploadDocuments` metódus két másodpercig késlelteti. Az indexelés aszinkron módon történik az Azure Cognitive Search szolgáltatásban, így a mintaalkalmazásnak rövid ideig kell várnia, hogy a dokumentumok elérhetők legyenek a kereséshez. Ilyen mértékű késleltetésre kizárólag demók, tesztek és mintaalkalmazások esetében van szükség.

<a name="how-dotnet-handles-documents"></a>

#### <a name="how-the-net-sdk-handles-documents"></a>A .NET SDK dokumentumkezelési módszere
Előfordulhat, hogy kíváncsi, hogy az Azure Cognitive Search .NET SDK hogyan tölthet `Hotel` fel egy felhasználó által definiált osztály példányait az indexbe. Hogy segítsen megválaszolni ezt a kérdést, nézzük meg az `Hotel` osztály:

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

Az első dolog, amit észre kell venni, `Hotel` hogy az osztály minden egyes köztulajdonának neve egy azonos nevű mezőre lesz leképezve az indexdefinícióban. Ha azt szeretné, hogy minden mező kisbetűvel ("teveeset") kezdődjön, megmondhatja az SDK-nak, hogy a tulajdonságneveket automatikusan rendelje hozzá a teve-esethez az `[SerializePropertyNamesAsCamelCase]` osztály attribútumával. Ez a forgatókönyv gyakori a .NET alkalmazások, amelyek adatkötést, ahol a célséma kívül esik az alkalmazás fejlesztője anélkül, hogy megsérti a "Pascal eset" elnevezési irányelvek .NET.

> [!NOTE]
> Az Azure Cognitive Search .NET SDK a [NewtonSoft JSON.NET](https://www.newtonsoft.com/json/help/html/Introduction.htm) függvénytárat használja az egyéni modellobjektumok szerializálására és deszerializálására a JSON-ból. A szerializálás szükség szerint testre szabható. További információ: [Egyéni szerializálás JSON.NET.](#JsonDotNet)
> 
> 

A második dolog, hogy észre minden tulajdonság `IsFilterable` `IsSearchable`díszített `Key`attribútumok, mint például , , , és `Analyzer`. Ezek az attribútumok közvetlenül az [Azure Cognitive Search index megfelelő mezőattribútumaihoz felelnek](/rest/api/searchservice/create-index)meg. Az `FieldBuilder` osztály ezeket a tulajdonságokat használja az index meződefinícióinak létrehozásához.

A harmadik fontos `Hotel` dolog az osztályban a nyilvános tulajdonságok adattípusai. Az indexdefinícióban a rendszer ezen .NET tulajdonságtípusokat képezi le a nekik megfelelő mezőtípusokra. Például a rendszer a `Edm.String` típusú `Category` szöveges tulajdonságot a `category` mezőbe képezi le. Hasonló típusú leképezések `Edm.Boolean` `DateTimeOffset?`vannak `Edm.DateTimeOffset` `bool?`a , , és így tovább között. A típusleképezés revonatkozó szabályait `Documents.Get` a metódus dokumentálja az [Azure Cognitive Search .NET SDK-hivatkozásban.](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.documentsoperationsextensions.get) Az `FieldBuilder` osztály gondoskodik erről a leképezésről, de még mindig hasznos lehet megérteni, ha bármilyen szerializálási problémát el kell hárítania.

Nem vette észre `SmokingAllowed` véletlenül az ingatlant?

```csharp
[JsonIgnore]
public bool? SmokingAllowed => (Rooms != null) ? Array.Exists(Rooms, element => element.SmokingAllowed == true) : (bool?)null;
```

A `JsonIgnore` tulajdonság attribútuma azt `FieldBuilder` mondja, hogy ne szerializálják azt az indexben mezőként.  Ez egy nagyszerű módja annak, hogy ügyféloldali számított tulajdonságokat hozhat létre, amelyeket segítőként használhat az alkalmazásban.  Ebben az esetben `SmokingAllowed` a szálláshely `Room` azt `Rooms` tükrözi, hogy a gyűjteményben van-e dohányzás.  Ha minden hamis, azt jelzi, hogy az egész szálloda nem engedélyezi a dohányzást.

Egyes tulajdonságok, `Address` `Rooms` például a .NET osztályok példányai.  Ezek a tulajdonságok összetettebb adatstruktúrákat képviselnek, és ennek eredményeképpen [összetett adattípusú](https://docs.microsoft.com/azure/search/search-howto-complex-data-types) mezőket igényelnek az indexben.

A `Address` tulajdonság több értékből álló `Address` készletet képvisel az osztályban, az alábbiakban definiálva:

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

Ez az osztály az Egyesült Államokban vagy Kanadában található címek leírásához használt szabványos értékeket tartalmazza. Az ilyen típusú típusok segítségével csoportosíthatja a logikai mezőket az indexben.

A `Rooms` tulajdonság objektumok tömbjét `Room` jelöli:

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

A .NET és a hozzá tartozó indexséma adatmodelljét úgy kell megtervezni, hogy támogassa a végfelhasználónak megadni kívánt keresési élményt. Minden legfelső szintű objektum a .NET, azaz a dokumentum az indexben, megfelel a keresési eredményt, akkor jelenik meg a felhasználói felületen. Egy szállodai keresőalkalmazásban például a végfelhasználók a szálloda neve, a szálloda jellemzői vagy egy adott szoba jellemzői alapján szeretnének keresni. Majd terjed ki néhány lekérdezési példák egy kicsit később.

Ez a képesség, hogy használja a saját osztályok, hogy kölcsönhatásba lépnek a dokumentumok az index működik mindkét irányban; A keresési eredményeket is lekérheti, és az SDK automatikusan deszerializálja őket az Ön által választott típusra, amint azt a következő szakaszban látni fogjuk.

> [!NOTE]
> Az Azure Cognitive Search .NET SDK is támogatja a `Document` dinamikusan beírt dokumentumokat az osztály használatával, amely a mezőnevek kulcs-érték leképezése a mezőértékekhez. Ez olyan helyzetekben hasznos, ha például a tervezés időpontjában az indexséma még nem ismert, illetve ha az adott modellosztályokhoz történő kötés nehézkes volna. Az SDK-ban lévő összes, dokumentumokkal foglalkozó módszer a `Document` osztállyal kompatibilis túlterhelésekkel rendelkezik, valamint olyan szigorú típusmegadású túlterhelésekkel, amelyek általános típusú paramétert vesznek fel. Ebben az oktatóanyagban csak az utóbbiak at használják a mintakódban. Az [ `Document` osztály](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.document) a `Dictionary<string, object>`tól öröklődik.
> 
>

**Miért használjon nullázható adattípusokat?**

Ha saját modellosztályait tervezi az Azure Cognitive Search indexhez való hozzárendeléshez, `bool` `int` javasoljuk, hogy deklarálja az értéktípusok tulajdonságait, például és nullázhatónak (például `bool?` nem). `bool` Nem nullázható tulajdonság használatakor **garantálnia** kell, hogy az index egyetlen dokumentuma sem tartalmaz az adott mezőben null értéket. Sem az SDK, sem az Azure Cognitive Search szolgáltatás nem segít ennek érvényesítésében.

Ennek nem csupán elméleti jelentősége van: képzeljünk el például egy olyan alkalmazási helyzetet, ahol egy `Edm.Int32` típusú, meglévő indexhez új mezőt kell hozzáadnunk. Az indexdefiníció frissítése után az összes dokumentum null értékkel rendelkezik az adott új mezőhöz (mivel az Azure Cognitive Search minden típusa semmissé válik). Ha ezt követően egy modellosztályt úgy alkalmaz, hogy ehhez a mezőhöz nem nullázható `int` tulajdonságot ad meg, a dokumentumok lekérdezésének megkísérlésekor egy ehhez hasonló `JsonSerializationException` választ kap:

    Error converting value {null} to type 'System.Int32'. Path 'IntValue'.

Ezért javasoljuk, hogy a modellosztályokban nullázható értéktípusokat használjon.

<a name="JsonDotNet"></a>

#### <a name="custom-serialization-with-jsonnet"></a>Egyéni szerializálás JSON.NET
Az SDK JSON.NET használ a dokumentumok szerializálásához és deszerializálásához. A szerializálást és a deszerializálást szükség esetén testreszabhatja a saját `JsonConverter` vagy `IContractResolver`a. További információt a [JSON.NET dokumentációjában](https://www.newtonsoft.com/json/help/html/Introduction.htm)talál. Ez akkor lehet hasznos, ha egy meglévő modellosztályt szeretne módosítani az alkalmazásból az Azure Cognitive Search és más speciális forgatókönyvek használatához. Az egyéni szerializálás sal például a következőket teheti:

* A modellosztály bizonyos tulajdonságainak dokumentummezőként való tárolása vagy kizárása.
* Rendelje meg a kódban szereplő tulajdonságnevek és az indexben szereplő mezőnevek között.
* Egyéni attribútumok létrehozása, amelyek a tulajdonságok dokumentummezőkhöz rendelésére használhatók.

Az azure cognitive search .NET SDK a GitHubon az egységtesztekben példákat találhat az egyéni szerializálás megvalósítására. Egy jó kiindulási pont ez a [mappa](https://github.com/Azure/azure-sdk-for-net/tree/4f6f4e4c90200c1b0621c4cead302a91e89f2aba/sdk/search/Microsoft.Azure.Search/tests/Tests/Models). Az egyéni szerializálási tesztek által használt osztályokat tartalmaz.

### <a name="searching-for-documents-in-the-index"></a>Dokumentumok keresése az indexben
A mintaalkalmazás utolsó lépése az indexben szereplő dokumentumok keresése:

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

Minden alkalommal, amikor egy lekérdezést hajt `SearchParameters` végre, ez a módszer először létrehoz egy új objektumot. Ez az objektum további beállítások megadására szolgál a lekérdezéshez, például a rendezéshez, a szűréshez, a lapozáshoz és a kifejezésszámhoz. Ebben a módszerben a `Filter`, `Select` `OrderBy`, `Top` , és tulajdonságot állítjuk be a különböző lekérdezésekhez. Az `SearchParameters` összes tulajdonság [itt](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.searchparameters)van dokumentálva.

A következő lépés a keresési lekérdezés tényleges végrehajtása. A keresés futtatása `Documents.Search` a módszerrel történik. Minden lekérdezésnél átadjuk a keresési szöveget, `"*"` amelyet karakterláncként használhat (vagy ha nincs keresési szöveg), valamint a korábban létrehozott keresési paramétereket. A típusparaméterként is `Hotel` `Documents.Search`megadjuk, amely arra utasítja az SDK-t, hogy a keresési eredményekben szereplő dokumentumokat deszerializálja a típusú `Hotel`objektumokká.

> [!NOTE]
> A keresési lekérdezés kifejezés szintaxisáról további információt [itt](https://docs.microsoft.com/rest/api/searchservice/Simple-query-syntax-in-Azure-Search)talál.
> 
> 

Végül, minden lekérdezés után ez a módszer végighalad a keresési eredmények ben szereplő összes egyezésen, és minden dokumentumot a konzolra nyomtat:

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

Vessünk egy közelebbi pillantást az egyes lekérdezések viszont. Itt van az első lekérdezés végrehajtásához szükséges kód:

```csharp
parameters =
    new SearchParameters()
    {
        Select = new[] { "HotelName" }
    };

results = indexClient.Documents.Search<Hotel>("motel", parameters);

WriteDocuments(results);
```

Ebben az esetben a teljes indexben a "motel" szót keressük bármely kereshető mezőben, és csak `Select` a szálloda neveket szeretnénk beolvasni, a paraméter ben meghatározottak szerint. Itt vannak az eredmények:

    Name: Secret Point Motel

    Name: Twin Dome Motel

A következő kérdés egy kicsit érdekesebb.  Olyan szállodákat szeretnénk találni, amelyek 100 dollárnál kisebb éjszakánként idoben rendelkeznek, és csak a szálloda azonosítóját és leírását adják vissza:

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

Ez a lekérdezés `$filter` egy `Rooms/any(r: r/BaseRate lt 100)`OData kifejezést használ, a program az indexben lévő dokumentumok szűréséhez. Ez [bármely operátort](https://docs.microsoft.com/azure/search/search-query-odata-collection-operators) felhasználja a "BaseRate lt 100" alkalmazásához a Szobák gyűjtemény minden elemére. Az Azure Cognitive Search által támogatott OData-szintaxisról itt olvashat [bővebben.](https://docs.microsoft.com/azure/search/query-odata-filter-orderby-syntax)

A lekérdezés eredményei:

    HotelId: 1
    Description: The hotel is ideally located on the main commercial artery of the city in the heart of New York...

    HotelId: 2
    Description: The hotel is situated in a nineteenth century plaza, which has been expanded and renovated to...

Ezután meg akarjuk találni a két legnépszerűbb szállodát, amelyeket a közelmúltban felújítottak, és meg akarjuk mutatni a szálloda nevét és az utolsó felújítás időpontját. A kód itt látható: 

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

Ebben az esetben ismét az OData szintaxist használjuk a `OrderBy` paraméter megadásához `lastRenovationDate desc`. Azt is `Top` meg 2 annak biztosítása érdekében, hogy csak az első két dokumentumot. A hogy korábban is, megadtuk, `Select` hogy mely mezőket kell visszaadni.

Itt vannak az eredmények:

    Name: Fancy Stay        Last renovated on: 6/27/2010 12:00:00 AM +00:00
    Name: Roach Motel       Last renovated on: 4/28/1982 12:00:00 AM +00:00

Végül meg akarjuk találni az összes olyan szállodanevet, amely megfelel a "hotel" szónak:

```csharp
parameters = new SearchParameters()
{
    SearchFields = new[] { "HotelName" }
};
results = indexClient.Documents.Search<Hotel>("hotel", parameters);

WriteDocuments(results);
```

És itt vannak az eredmények, amelyek tartalmazzák `Select` az összes mezőt, mivel nem adtuk meg az ingatlant:

    HotelId: 3
    Name: Triple Landscape Hotel
    ...

Ez a lépés befejezi az oktatóanyagot, de ne álljon meg itt. **A következő lépések további forrásokat biztosítanak az Azure Cognitive Search szolgáltatással kapcsolatos további információkhoz.

## <a name="next-steps"></a>További lépések
* Nézze át a [.NET SDK](https://docs.microsoft.com/dotnet/api/microsoft.azure.search) és a [REST API](https://docs.microsoft.com/rest/api/searchservice/) referenciáit.
* Tekintse át az [elnevezési konvenciókat](https://docs.microsoft.com/rest/api/searchservice/Naming-rules) a különböző objektumok elnevezésére vonatkozó szabályok megismeréséhez.
* Tekintse át a [támogatott adattípusokat](https://docs.microsoft.com/rest/api/searchservice/Supported-data-types) az Azure Cognitive Search szolgáltatásban.
