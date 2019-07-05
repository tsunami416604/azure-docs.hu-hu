---
title: Az Azure Search .NET-alkalmazás – Azure Search használata
description: Ismerje meg az Azure Search használata a .NET alkalmazás használatával C# és a .NET SDK-val. Kódalapú feladatok közé tartozik a csatlakozni a szolgáltatáshoz, a tartalom indexelése és a egy index lekérdezése.
author: brjohnstmsft
manager: jlembicz
services: search
ms.service: search
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 06/19/2019
ms.author: brjohnst
ms.openlocfilehash: 9f0af40d442747181636b50612f7d2162ead6a86
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/28/2019
ms.locfileid: "67450019"
---
# <a name="how-to-use-azure-search-from-a-net-application"></a>Az Azure Search .NET-alkalmazás használata

Ez a cikk ahhoz, hogy működik és a egy forgatókönyv a [Azure Search .NET SDK](https://aka.ms/search-sdk). A .NET SDK használatával egy fejlett keresési funkciókat megvalósítása az Azure Search használatával az alkalmazás.

## <a name="whats-in-the-azure-search-sdk"></a>Mi az az Azure SDK keresése
Az SDK a következőket tartalmazza, amelyek lehetővé teszik az Indexek kezelése néhány klienskódtárak, adatforrások, indexelőre és szinonimát térképek, valamint a feltöltési és dokumentumok kezeléséhez, hajtsa végre a lekérdezéseket, mindezt úgy, hogy HTTP vagy JSON részleteit bajlódnia. Ezen klienskódtárak NuGet-csomagként oszlanak.

A fő NuGet-csomag `Microsoft.Azure.Search`, azaz egy meta-csomagot, amely tartalmazza a függőségek szerint az összes többi csomagot. Ez a csomag használatára, ha csak megkezdéséhez, vagy ha ismeri az alkalmazásnak szüksége lesz az Azure Search összes funkcióját.

Az SDK-t a többi NuGet csomagot a következők:
 
  - `Microsoft.Azure.Search.Data`: Használja ezt a csomagot, ha használja az Azure Search .NET-alkalmazást fejleszt, és csak szeretne lekérdezni vagy frissíteni az indexek a dokumentumok. Ha is kell létrehozni vagy frissíteni az indexek, szinonimatérképet, vagy más szolgáltatásiszint-erőforrások, használja a `Microsoft.Azure.Search` csomag helyette.
  - `Microsoft.Azure.Search.Service`: Használja ezt a csomagot, ha a .NET-keretrendszerben Azure Search-indexek, szinonimatérképet, az indexelők, adatforrásokat vagy más szolgáltatásiszint-erőforrások kezelése automation fejleszt. Ha csak kell lekérdezés vagy a frissítés dokumentum az indexben, használja a `Microsoft.Azure.Search.Data` csomag helyette. Ha a funkciókat az Azure Search van szüksége, használja a `Microsoft.Azure.Search` csomag helyette.
  - `Microsoft.Azure.Search.Common`: Az Azure Search .NET-kódtárakra által igényelt gyakori típust. Nem kell ezt a csomagot használja a saját alkalmazásában. Csak hivatott függőségként használható.

A különböző klienskódtárak például osztályok definiálása `Index`, `Field`, és `Document`, illetve műveletek, például `Indexes.Create` és `Documents.Search` a a `SearchServiceClient` és `SearchIndexClient` osztályokat. Ezeket az osztályokat vannak szervezve a következő névterek:

* [Microsoft.Azure.Search](https://docs.microsoft.com/dotnet/api/microsoft.azure.search)
* [Microsoft.Azure.Search.Models](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models)

Ha visszajelzést küldhet az SDK egy következő frissítés, szeretné, tekintse meg a [visszajelzésküldő oldala](https://feedback.azure.com/forums/263029-azure-search/) , vagy hozzon létre egy problémát a [GitHub](https://github.com/azure/azure-sdk-for-net/issues) és az "Azure Search" díjszabásunkban a probléma címe.

A .NET SDK-verziót támogatja `2019-05-06` , a [Azure Search REST API](https://docs.microsoft.com/rest/api/searchservice/). Ez a verzió támogatja az [komplex típusok](search-howto-complex-data-types.md), [cognitive search](cognitive-search-concept-intro.md), [automatikus kiegészítés](https://docs.microsoft.com/rest/api/searchservice/autocomplete), és [JsonLines elemzési mód](search-howto-index-json-blobs.md) során az Azure-Blobok indexelése. 

Ez az SDK nem támogatja a [felügyeleti műveletek](https://docs.microsoft.com/rest/api/searchmanagement/) például létrehozása és a keresési szolgáltatások méretezése és API-kulcsok kezelése. Ha a Search-erőforrások kezelése a .NET-alkalmazásból van szüksége, használhatja a [Azure Search .NET SDK-t felügyeleti](https://aka.ms/search-mgmt-sdk).

## <a name="upgrading-to-the-latest-version-of-the-sdk"></a>Az SDK legújabb verziójára
Ha már használ az Azure Search .NET SDK egy régebbi verzióját, és szeretné, frissítsen a legújabb általánosan elérhető verziót [Ez a cikk](search-dotnet-sdk-migration-version-9.md) azt ismerteti, hogyan.

## <a name="requirements-for-the-sdk"></a>Az SDK-követelményei
1. A Visual Studio 2017-es vagy újabb verziója.
2. A saját Azure Search szolgáltatást. Az SDK használatához szüksége lesz a szolgáltatás és a egy vagy több API-kulcs neve. [Szolgáltatás létrehozása a portálon](search-create-service-portal.md) segít a fenti lépéseket.
3. Az Azure Search .NET SDK letöltése [NuGet-csomag](https://www.nuget.org/packages/Microsoft.Azure.Search) "NuGet-csomagok kezelése" a Visual Studio használatával. Csak keresse meg a csomag neveként `Microsoft.Azure.Search` on NuGet.org (vagy egy másik csomagot a fenti nevek, ha csak az funkciók egy részét).

Az Azure Search .NET SDK támogatja az alkalmazások a .NET-keretrendszer 4.5.2-es vagy újabb, valamint a .NET Core 2.0-s és újabb.

## <a name="core-scenarios"></a>Használhatók a legfontosabb forgatókönyvek
Számos dolgot kell hajtsa végre az alkalmazás. Ebben az oktatóanyagban alapvető forgatókönyvekben foglalkozik:

* Az index létrehozása
* Hozzáláthat a tárgymutató dokumentumok
* A teljes szöveges keresés és a szűrők használatával dokumentumok keresése

Az alábbi mintakód bemutatja az egyes forgatókönyvek esetében. Nyugodtan kódrészletek használhatja a saját alkalmazásában.

### <a name="overview"></a>Áttekintés
A mintaalkalmazás, azt fogja felfedezése létrehoz egy új "Hotels" nevet, index tölti fel, néhány dokumentumot, majd néhány keresési lekérdezéseket futtat. A fő program, az általános folyamatot bemutató a következő:

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

Végigvezetjük a lépésről lépésre. Először létre kell hozni egy új `SearchServiceClient`. Ez az objektum teszi indexeket lehet kezelni. Annak érdekében, hogy hozhat létre egyet, meg kell adnia az Azure Search-szolgáltatásnév, valamint egy rendszergazdai API-kulcsot. Ezt az információt adhat meg a `appsettings.json` -fájlját a [mintaalkalmazás](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowTo).

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
> Ha megad egy helytelen kulcsot (például egy lekérdezési kulcsot, egy adminisztrációs kulcsot volt szükség), a `SearchServiceClient` kivételt fogja kijelezni egy `CloudException` , a hiba-üzenet "Tiltott" egy művelet metódust hívja meg, mint például az első alkalommal `Indexes.Create`. Ha ez történik, ellenőrizze az API-kulcsot.
> 
> 

A következő néhány sort a "Hotels" nevet, először törlése, ha már létezik egy index létrehozásának hívjuk. Végigvezetjük ezek a metódusok egy kicsit később.

```csharp
Console.WriteLine("{0}", "Deleting index...\n");
DeleteIndexIfExists(indexName, serviceClient);

Console.WriteLine("{0}", "Creating index...\n");
CreateIndex(indexName, serviceClient);
```

Ezután az index kell kell feltöltenie. Az index feltöltésére, szükségünk lesz a `SearchIndexClient`. Beszerzése utólag két módja van: hozhat létre, vagy meghívásával `Indexes.GetClient` a a `SearchServiceClient`. Használjuk az utóbbi kényelmi célokat szolgál.

```csharp
ISearchIndexClient indexClient = serviceClient.Indexes.GetClient(indexName);
```

> [!NOTE]
> Egy tipikus keresőalkalmazást, az index kezelését és feltöltését előfordulhat, hogy kell kezelnie egy külön keresési lekérdezések az összetevőt. `Indexes.GetClient` egy kényelmes megoldás az index feltöltése, mivel így nem szükséges további `SearchCredentials`. Ezt azon rendszergazdai kulcs átadásával hajtja végre, amelyet a `SearchServiceClient` elemnek az új `SearchIndexClient` objektumban történő létrehozásakor használt. Azonban a lekérdezéseket végrehajtó alkalmazás részeként érdemes létrehozni a `SearchIndexClient` közvetlenül, hogy formájában adható át, amely csak lehetővé teszi, hogy olvassa el az adatokat, egy rendszergazdai kulcs helyett lekérdezési kulcs. Ez megfelel a minimális jogosultság elvének, és biztonságosabbá teszi az alkalmazás segítségével. További információk az adminisztrációs kulcsok és a lekérdezési kulcsok annak [Itt](https://docs.microsoft.com/rest/api/searchservice/#authentication-and-authorization).
> 
> 

Most, hogy egy `SearchIndexClient`, hogy töltheti fel az indexet. Index feltöltése végigvezetjük később más módon történik.

```csharp
Console.WriteLine("{0}", "Uploading documents...\n");
UploadDocuments(indexClient);
```

Végül azt néhány keresési lekérdezéseket, és megjeleníti az eredményeket. Most használjuk a különböző `SearchIndexClient`:

```csharp
ISearchIndexClient indexClientForQueries = CreateSearchIndexClient(indexName, configuration);

RunQueries(indexClientForQueries);
```

Hogy közelebbről vesz igénybe a `RunQueries` metódus később. Íme a kódot, amellyel létrehozza az új `SearchIndexClient`:

```csharp
private static SearchIndexClient CreateSearchIndexClient(string indexName, IConfigurationRoot configuration)
{
    string searchServiceName = configuration["SearchServiceName"];
    string queryApiKey = configuration["SearchServiceQueryApiKey"];

    SearchIndexClient indexClient = new SearchIndexClient(searchServiceName, indexName, new SearchCredentials(queryApiKey));
    return indexClient;
}
```

Most használjuk egy lekérdezési kulcsot, mert nem kell írási hozzáférést az index. Ezt az információt adhat meg a `appsettings.json` -fájlját a [mintaalkalmazás](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowTo).

Futtatásakor az alkalmazás egy érvényes szolgáltatásnév és API-kulcsokat, a kimeneti példához hasonlóan kell kinéznie: (Egyes konzolkimenet helyébe a "..." illusztrációs célokat szolgálnak.)

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

Ez a cikk végén található az alkalmazás a teljes forráskódot biztosítunk.

Ezután azt fogja annak minden egyes meghívott módszerek közelebbről is `Main`.

### <a name="creating-an-index"></a>Az index létrehozása
Miután létrehozott egy `SearchServiceClient`, `Main` törli a "hotels" index, ha már létezik. A törlés végzi el a következő metódust:

```csharp
private static void DeleteIndexIfExists(string indexName, SearchServiceClient serviceClient)
{
    if (serviceClient.Indexes.Exists(indexName))
    {
        serviceClient.Indexes.Delete(indexName);
    }
}
```

Ez a módszer az adott `SearchServiceClient` annak ellenőrzéséhez, ha az index már létezik, és ha igen, törölje azt.

> [!NOTE]
> Ebben a cikkben a példakód az egyszerűség érdekében az Azure Search .NET SDK szinkron módszereit használja. Azt javasoljuk, hogy a méretezhetőség és a gyors válaszadás érdekében használja saját alkalmazásaiban az aszinkron módszereket. Ha például a fenti metódus használhat `ExistsAsync` és `DeleteAsync` helyett `Exists` és `Delete`.
> 
> 

Ezután `Main` hoz létre egy új "hotels" index a metódus meghívásának hatására:

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

Ez a módszer létrehoz egy új `Index` objektum listáját `Field` objektumok sémájának az új index. Minden mező rendelkezik egy név, adattípus és számos attribútum, amely a keresés viselkedését. A `FieldBuilder` osztály tükröződés használatával hozzon létre egy `Field` objektumok a nyilvános tulajdonságok és az attribútumok megvizsgálásával index számára az adott `Hotel` model class. Viszonozhatják közelebbről a `Hotel` később osztály.

> [!NOTE]
> Bármikor létrehozhat listájának `Field` objektumok használata helyett közvetlenül `FieldBuilder` szükség esetén. Például előfordulhat, hogy nem szeretné egy modellosztályt, vagy előfordulhat, hogy kell használnia, amelyet szeretne attribútumokat hozzáadásával módosítsa a meglévő modellosztály.
>
> 

Mezők mellett is adhat pontozási profilok, javaslattevőket vagy CORS-beállítások az Index (ezeket a paramétereket a mintát az áttekinthetőség). Az Index objektum és azok részlegei a további információt talál a [SDK-leírás](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.index)is, a a [Azure Search REST API-referencia](https://docs.microsoft.com/rest/api/searchservice/).

### <a name="populating-the-index"></a>Hozzáláthat a tárgymutató
A következő lépés `Main` tölti fel az újonnan létrehozott indexben. Az index feltöltése történik, a következő metódusban: (Néhány kódot írni a "..." illusztrációs célokat szolgálnak.  Tekintse meg a teljes minta megoldás az adatok teljes sokaság kódot.)

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

Ez a metódus négy részből áll. Az első létrehoz egy tömböt a 3-ból `Hotel` objektumok mindegyike 3 `Room` objektumok erre a célra a bemeneti adatok feltöltése az indexbe. Ezek az adatok nem változtatható az egyszerűség kedvéért. A saját alkalmazásában az adatok valószínűleg például egy SQL database egy külső adatforrásból származnak.

A második rész létrehoz egy `IndexBatch` tartalmazó dokumentumokat. Azt adja meg a műveletet, létrehozása, ebben az esetben meghívásával, a batch a alkalmazni kívánt `IndexBatch.Upload`. A batch majd töltenek fel az Azure Search-index által a `Documents.Index` metódust.

> [!NOTE]
> Ebben a példában azt csak tölt fel dokumentumokat. Ha meglévő dokumentumok egyesítse a módosításokat, vagy törölhetnek dokumentumokat szeretne, létrehozhat meghívásával kötegek `IndexBatch.Merge`, `IndexBatch.MergeOrUpload`, vagy `IndexBatch.Delete` helyette. Az egy kötegben különböző műveleteiről használhatók vegyesen meghívásával `IndexBatch.New`, gyűjteménye, amely veszi `IndexAction` objektumok, amelyek arra utasítja az Azure Search egy dokumentum egy adott művelet végrehajtásához. Hozhat létre minden egyes `IndexAction` saját műveletet a megfelelő metódus meghívásával `IndexAction.Merge`, `IndexAction.Upload`, és így tovább.
> 
> 

Ez a módszer harmadik része a "catch" blokk az indexelés egy fontos hibaesetét kezeli. Ha az Azure Search-szolgáltatásnak nem sikerül indexelnie a kötegben szereplő fájlok valamelyikét, a `Documents.Index` rendszer `IndexBatchException` választ ad. Ez a kivétel akkor fordulhat elő, ha vannak indexeli a dokumentumokat, amíg a szolgáltatás nagy terhelés alatt áll. **Javasoljuk, hogy a kódban explicit módon kezelje ezt az esetet.** Azon dokumentumok esetében, ahol az indexelés meghiúsult, elhalaszthatja azt, majd később újra megpróbálkozhat az indexeléssel, vagy a mintának megfelelően naplózhatja azt, és folytathatja a munkáját, esetleg – az alkalmazás adatkonzisztencia-követelményeitől függően – más műveletbe kezdhet.

> [!NOTE]
> Használhatja a [ `FindFailedActionsToRetry` ](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.indexbatchexception.findfailedactionstoretry) metódus csak az előző hívása sikertelen műveleteket tartalmazó új kötegelt `Index`. Nincs megfelelően használatával hatásának a megbeszélését [a StackOverflow-n](https://stackoverflow.com/questions/40012885/azure-search-net-sdk-how-to-use-findfailedactionstoretry).
>
>

Végül a `UploadDocuments` két másodperces metódus késéseket. Az Azure Search-szolgáltatásban az indexelés aszinkron módon történik, így a mintaalkalmazásnak egy rövid ideig várnia kell, amíg a rendszer meggyőződik arról, hogy a dokumentum kereshető. Ilyen mértékű késleltetésre kizárólag demók, tesztek és mintaalkalmazások esetében van szükség.

<a name="how-dotnet-handles-documents"></a>

#### <a name="how-the-net-sdk-handles-documents"></a>A .NET SDK dokumentumkezelési módszere
Megfordulhat a fejében, hogy miként képes az Azure Search .NET SDK felhasználó által meghatározott `Hotel` osztályhoz hasonló példányok feltöltésére az indexbe. Annak érdekében, hogy kapcsolatos kérdésére választ kaphat, nézzük meg a `Hotel` osztály:

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

Az első szembetűnő dolog, hogy, hogy minden egyes nyilvános tulajdonsága nevére a `Hotel` osztály ugyanazzal a névvel, az Indexdefiníció egy mező lesz leképezve. Ha szeretné az egyes mezők kezdje kisbetűvel ("nagybetűs"), utasíthatja az SDK-t a tulajdonságnevek tulajdonságnevek automatikus kisbetűs leképezésére a `[SerializePropertyNamesAsCamelCase]` attribútum az osztály. Ebben a forgatókönyvben szokás a .NET-alkalmazások, amelyek hajtható végre adatkötés, ahol a célséma kívül esik a vezérlő az alkalmazás fejlesztőjének pokyny Pro pojmenování a .NET-ben a "Pascal eset" megsértő nélkül.

> [!NOTE]
> Az Azure Search .NET SDK a [NewtonSoft JSON.NET](https://www.newtonsoft.com/json/help/html/Introduction.htm) könyvtárat használja az egyéni modellek JSON-ból és JSON-ba történő szerializálására és deszerializálására. A szerializálás szükség szerint testre szabható. További információkért lásd: [egyéni szerializálás a JSON.NET](#JsonDotNet).
> 
> 

A második szembetűnő dolog, hogy minden tulajdonság van kitüntetett attribútumokkal rendelkező például `IsFilterable`, `IsSearchable`, `Key`, és `Analyzer`. Ezek az attribútumok leképezése közvetlenül a [megfelelő mezőt az Azure Search-index attribútumok](https://docs.microsoft.com/rest/api/searchservice/create-index#request). A `FieldBuilder` osztályt használja ezeket a tulajdonságokat Meződefiníciók az index létrehozására.

Tudnivalók a harmadik lényeg a `Hotel` osztály a nyilvános tulajdonságok adattípusa. Az indexdefinícióban a rendszer ezen .NET tulajdonságtípusokat képezi le a nekik megfelelő mezőtípusokra. Például a rendszer a `Edm.String` típusú `Category` szöveges tulajdonságot a `category` mezőbe képezi le. Nincsenek közötti hasonló Típusleképezés `bool?`, `Edm.Boolean`, `DateTimeOffset?`, és `Edm.DateTimeOffset` és így tovább. A típusleképezés vonatkozó szabályainak dokumentációja az [Azure Search .NET SDK-referenciában](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.documentsoperationsextensions.get), a `Documents.Get` metódusnál található. A `FieldBuilder` osztály a megfelelőségről gondoskodik, de továbbra is lehet annak megértése, abban az esetben szerializációs hárítsa el kell.

Adott figyelje meg, hogy történjen a `SmokingAllowed` tulajdonság?

```csharp
[JsonIgnore]
public bool? SmokingAllowed => (Rooms != null) ? Array.Exists(Rooms, element => element.SmokingAllowed == true) : (bool?)null;
```

A `JsonIgnore` attribútum a tulajdonság arra utasítja a `FieldBuilder` nem szerializálható, mezőként az indexbe.  Ez az ügyféloldali számított tulajdonságok segítők, használhatja az alkalmazás létrehozása nagyszerű lehetőséget.  Ebben az esetben a `SmokingAllowed` tulajdonság mutatja, hogy `Room` a a `Rooms` gyűjtemény lehetővé teszi, hogy a fogyasztási.  Ha az összes false (hamis), az azt jelzi, hogy a teljes Szálloda nem engedélyezi a fogyasztási.

Egyes tulajdonságok, például `Address` és `Rooms` olyan .NET-osztályok példányai.  Ezek a Tulajdonságok adatstruktúrákat jelölnek az adattárakon összetettebb, és emiatt szükséges mezőket egy [összetett adattípusú](https://docs.microsoft.com/azure/search/search-howto-complex-data-types) az indexben.

A `Address` tulajdonság több értékek egy halmazát jelölik a `Address` alább meghatározott osztály:

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

Ez az osztály az Egyesült Államokban vagy Kanadában címek leírására szolgáló standard értékeket tartalmazza. Használhatja például a logikai mezőket csoportosíthatja az indexben.

A `Rooms` tulajdonság tömbjét adja `Room` objektumok:

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

Az adatmodellben a .NET-keretrendszer és az annak megfelelő indexsémát úgy kell megtervezni a keresési funkciót, a végfelhasználó számára szeretné támogatásához. A .NET-ben, vagyis a dokumentum az indexben, minden felső szintű objektum egy keresési eredmény, bemutatja az Ön felhasználói felületén felel meg. Például egy Szálloda keresési alkalmazásban a végfelhasználók számára is szeretne keresni Szálloda neve, Szálloda funkcióját, vagy egy adott hely jellemzőit. Ismertetjük, néhány lekérdezési példa egy kicsit később.

A saját osztályok használatát az indexben található dokumentumok interakcióba képessége mindkét irányban; működik Is lekérdezheti a keresési eredményeket, és rendelkezik az SDK-val automatikusan deszerializáltathatja azokat a választott típusra láthatóak lesznek a következő szakaszban.

> [!NOTE]
> Az Azure Search .NET SDK támogatja a `Document` osztályt használó, dinamikus dokumentumtípusokat is, amely alatt a mezők neveinek értékekre történő kulcs/érték-leképezését értjük. Ez olyan helyzetekben hasznos, ha például a tervezés időpontjában az indexséma még nem ismert, illetve ha az adott modellosztályokhoz történő kötés nehézkes volna. Az SDK-ban lévő összes, dokumentumokkal foglalkozó módszer a `Document` osztállyal kompatibilis túlterhelésekkel rendelkezik, valamint olyan szigorú típusmegadású túlterhelésekkel, amelyek általános típusú paramétert vesznek fel. Kizárólag az utóbbiakat használjuk a mintakód ebben az oktatóanyagban. A [ `Document` osztály](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.document) örököl `Dictionary<string, object>`.
> 
>

**Miért használjon nullázható adattípusokat?**

Az Azure Search-indexre leképezést végző, saját modellosztályok létrehozásakor javasoljuk, hogy például a `bool` és `int` értéktípusok tulajdonságainak megadása nullázhatóként történjen (például `bool` helyett `bool?`). Nem nullázható tulajdonság használatakor **garantálnia** kell, hogy az index egyetlen dokumentuma sem tartalmaz az adott mezőben null értéket. Ennek kényszerítéséhez sem az SDK, sem az Azure Search szolgáltatás nem nyújt segítséget.

Ez nem csupán elméleti szempont: Képzelje el egy forgatókönyvet, ahol hozzáadhat egy új mezőt típusú, meglévő indexhez `Edm.Int32`. Az indexdefiníció frissítését követően ehhez a mezőhöz minden dokumentumban null érték tartozik (mivel az Azure Search szolgáltatásban az összes értéktípus nullázható). Ha ezt követően egy modellosztályt úgy alkalmaz, hogy ehhez a mezőhöz nem nullázható `int` tulajdonságot ad meg, a dokumentumok lekérdezésének megkísérlésekor egy ehhez hasonló `JsonSerializationException` választ kap:

    Error converting value {null} to type 'System.Int32'. Path 'IntValue'.

Ezért javasoljuk, hogy a modellosztályokban nullázható értéktípusokat használjon.

<a name="JsonDotNet"></a>

#### <a name="custom-serialization-with-jsonnet"></a>Egyéni szerializálás a JSON.NET használatával
Az SDK-t használ JSON.NET szerializálásához és deszerializálásához dokumentumokat. Testre szabhatja a szerializálási és szükség esetén definiálásával saját deszerializálási `JsonConverter` vagy `IContractResolver`. További információkért lásd: a [JSON.NET dokumentáció](https://www.newtonsoft.com/json/help/html/Introduction.htm). Ez hasznos lehet, ha meg szeretné alkalmazkodnak az alkalmazás használatát az Azure Search, és más speciális forgatókönyvekhez egy meglévő modell osztály. Ha például egyéni sorba rendezésre segítségével:

* Belefoglalhat vagy kizárhat bizonyos tulajdonságok modellosztály-dokumentum mezőként tárolják.
* Képezze le a kódban a tulajdonságnevek és az index mezőnevek között.
* Hozzon létre egyéni attribútumokat, amelyek tulajdonságok hozzárendelése a dokumentum mezők is használható.

Példák egyedi szerializálás az egységteszteket a végrehajtása az Azure Search .NET SDK a Githubon találja. Van egy jó kiindulási pont [Ez a mappa](https://github.com/Azure/azure-sdk-for-net/tree/AutoRest/src/Search/Search.Tests/Tests/Models). Az egyéni sorba rendezésre tesztek által használt osztályok tartalmazza.

### <a name="searching-for-documents-in-the-index"></a>Az indexben található dokumentumok keresése
A mintaalkalmazásban az utolsó lépés, hogy az index bizonyos dokumentumok keresése:

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

Minden alkalommal, amikor a lekérdezés végrehajtása, ezt a módszert először létrehoz egy új `SearchParameters` objektum. Ez az objektum segítségével adja meg a lekérdezés például rendezéssel, szűréssel, lapozási és jellemzőkezelés további beállításokat. Ez a módszer a beállítás folyamatban van a `Filter`, `Select`, `OrderBy`, és `Top` különböző lekérdezéseket tulajdonsága. Az összes a `SearchParameters` tulajdonságok szerepelnek [Itt](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.searchparameters).

A következő lépés, hogy ténylegesen hajtsa végre a keresési lekérdezést. A keresés futó történik használatával a `Documents.Search` metódust. Az egyes lekérdezések használandó karakterlánc a keresett szöveget adjuk át (vagy `"*"` Ha nincs keresési szöveg), valamint a korábban létrehozott keresési paraméterek. Azt is megadhatja `Hotel` a típusát paraméterként `Documents.Search`, amely tájékoztatja, hogy az SDK-val dokumentumok a keresési eredmények deszerializálása típusú objektumokat be `Hotel`.

> [!NOTE]
> További információ a search lekérdezési kifejezés szintaxisa annak [Itt](https://docs.microsoft.com/rest/api/searchservice/Simple-query-syntax-in-Azure-Search).
> 
> 

Végül minden lekérdezés után ez a módszer végighalad a minden találatot a keresési eredmények között, a konzol minden egyes dokumentum nyomtatása:

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

Vegyük viszont minden, a lekérdezések közelebbről. Az első lekérdezés végrehajtásához a kód itt látható:

```csharp
parameters =
    new SearchParameters()
    {
        Select = new[] { "HotelName" }
    };

results = indexClient.Documents.Search<Hotel>("motel", parameters);

WriteDocuments(results);
```

Ebben az esetben azt a "amelyben" szót a teljes indexben keres bármely kereshető mezőjében, és csak szeretnénk lekérni a Szálloda neve által megadott a `Select` paraméter. Az eredmények a következők:

    Name: Secret Point Motel

    Name: Twin Dome Motel

A következő lekérdezés az érdekesebb egy kicsit.  Bármilyen egy éjszakai arány kisebb, mint 100 USD elegendő hellyel rendelkezik, és csak a Szálloda-Azonosítót és a leírást ad vissza, amely a következők:

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

Ez a lekérdezés használ egy OData `$filter` kifejezés, `Rooms/any(r: r/BaseRate lt 100)`, a dokumentumok indexben szűréséhez. Ez a [bármely szereplő](https://docs.microsoft.com/azure/search/search-query-odata-collection-operators) a alkalmazni az "BaseRate lt 100' a termek gyűjtemény minden eleméhez. Talál további információt az OData-szűrőszintaxis, amely támogatja az Azure Search [Itt](https://docs.microsoft.com/azure/search/query-odata-filter-orderby-syntax).

Az alábbiakban a lekérdezési eredmények:

    HotelId: 1
    Description: The hotel is ideally located on the main commercial artery of the city in the heart of New York...

    HotelId: 2
    Description: The hotel is situated in a nineteenth century plaza, which has been expanded and renovated to...

Ezután szeretnénk megtalálni a felső két hotels, amely rendelkezik lett utoljára felújított és a Szálloda neve és az utolsó felújítás dátumának megjelenítése. A kód itt látható: 

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

Ebben az esetben újra használjuk OData-szűrőszintaxis adja meg a `OrderBy` paraméterrel `lastRenovationDate desc`. Azt is beállíthatja `Top` a 2. Győződjön meg arról, hogy csak az első két dokumentum beolvasása. Korábban, hogy állíthat be `Select` mezőket a rendszer visszalépteti megadásához.

Az eredmények a következők:

    Name: Fancy Stay        Last renovated on: 6/27/2010 12:00:00 AM +00:00
    Name: Roach Motel       Last renovated on: 4/28/1982 12:00:00 AM +00:00

Végül szeretnénk található összes Szálloda neve megegyezik a "hotel" szót:

```csharp
parameters = new SearchParameters()
{
    SearchFields = new[] { "HotelName" }
};
results = indexClient.Documents.Search<Hotel>("hotel", parameters);

WriteDocuments(results);
```

Az alábbiakban az eredményeket, amely az összes mező tartalmazza, mivel nem azt adta meg, és a `Select` tulajdonság:

    HotelId: 3
    Name: Triple Landscape Hotel
    ...

Ez a lépés befejezi az oktatóanyag, de itt nem állnak le. ** A következő lépések további erőforrások az Azure Search-ról további adja meg.

## <a name="next-steps"></a>További lépések
* Nézze át a [.NET SDK](https://docs.microsoft.com/dotnet/api/microsoft.azure.search) és a [REST API](https://docs.microsoft.com/rest/api/searchservice/) referenciáit.
* Felülvizsgálat [elnevezési konvenciók](https://docs.microsoft.com/rest/api/searchservice/Naming-rules) további a különféle objektumok elnevezési szabályai.
* Felülvizsgálat [által támogatott adattípusokkal](https://docs.microsoft.com/rest/api/searchservice/Supported-data-types) az Azure Search szolgáltatásban.
