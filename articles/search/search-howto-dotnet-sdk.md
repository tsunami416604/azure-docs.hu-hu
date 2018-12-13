---
title: Az Azure Search .NET-alkalmazás – Azure Search használata
description: Ismerje meg az Azure Search használata a .NET alkalmazás használatával C# és a .NET SDK-val. Kódalapú feladatok közé tartozik a csatlakozni a szolgáltatáshoz, a tartalom indexelése és a egy index lekérdezése.
author: brjohnstmsft
manager: jlembicz
services: search
ms.service: search
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 04/20/2018
ms.author: brjohnst
ms.custom: seodec2018
ms.openlocfilehash: c2dd9d7c7567a7c57def093c4d611ab09c870d84
ms.sourcegitcommit: eb9dd01614b8e95ebc06139c72fa563b25dc6d13
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/12/2018
ms.locfileid: "53310674"
---
# <a name="how-to-use-azure-search-from-a-net-application"></a>Az Azure Search .NET-alkalmazás használata
Ez a cikk ahhoz, hogy működik és a egy forgatókönyv a [Azure Search .NET SDK](https://aka.ms/search-sdk). A .NET SDK használatával egy fejlett keresési funkciókat megvalósítása az Azure Search használatával az alkalmazás.

## <a name="whats-in-the-azure-search-sdk"></a>Mi az az Azure SDK keresése
Az SDK a következőket tartalmazza, amelyek lehetővé teszik az Indexek kezelése néhány klienskódtárak, adatforrások, indexelőre és szinonimát térképek, valamint a feltöltési és dokumentumok kezeléséhez, hajtsa végre a lekérdezéseket, mindezt úgy, hogy HTTP vagy JSON részleteit bajlódnia. Ezen klienskódtárak NuGet-csomagként oszlanak.

A fő NuGet-csomag `Microsoft.Azure.Search`, azaz egy meta-csomagot, amely tartalmazza a függőségek szerint az összes többi csomagot. Ez a csomag használatára, ha csak megkezdéséhez, vagy ha ismeri az alkalmazásnak szüksége lesz az Azure Search összes funkcióját.

Az SDK-t a többi NuGet csomagot a következők:
 
  - `Microsoft.Azure.Search.Data`: Használja ezt a csomagot, ha használja az Azure Search .NET-alkalmazást fejleszt, és csak szeretne lekérdezni vagy frissíteni az indexek a dokumentumok. Ha is kell létrehozni vagy frissíteni az indexek, szinonimatérképet, vagy más szolgáltatásiszint-erőforrások, használja a `Microsoft.Azure.Search` csomag helyette.
  - `Microsoft.Azure.Search.Service`: Használja ezt a csomagot, ha a .NET-keretrendszerben Azure Search-indexek, szinonimatérképet, az indexelők, adatforrásokat vagy más szolgáltatásiszint-erőforrások kezelése automation fejleszt. Ha csak kell lekérdezés vagy a frissítés dokumentum az indexben, használja a `Microsoft.Azure.Search.Data` csomag helyette. Ha a funkciókat az Azure Search van szüksége, használja a `Microsoft.Azure.Search` csomag helyette.
  - `Microsoft.Azure.Search.Common`: Az Azure Search .NET-kódtárakra által igényelt gyakori típust. Nem kell saját alkalmazásában; ez a csomag használatára Csak hivatott függőségként használható.

A különböző klienskódtárak például osztályok definiálása `Index`, `Field`, és `Document`, illetve műveletek, például `Indexes.Create` és `Documents.Search` a a `SearchServiceClient` és `SearchIndexClient` osztályokat. Ezeket az osztályokat vannak szervezve a következő névterek:

* [A Microsoft.Azure.Search](https://docs.microsoft.com/dotnet/api/microsoft.azure.search)
* [Microsoft.Azure.Search.Models](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models)

Az Azure Search .NET SDK jelenlegi verziója már általánosan elérhető. Ha szeretné, hogy ahhoz, hogy a következő verziójában építhet be visszajelzést, kérjük, látogasson el a [visszajelzésküldő oldala](https://feedback.azure.com/forums/263029-azure-search/).

A .NET SDK-verziót támogatja `2017-11-11` , a [Azure Search REST API](https://docs.microsoft.com/rest/api/searchservice/). Ez a verzió mostantól támogatja a szinonimák, valamint az indexelő növekményes fejlesztései. Előzetes verziójú funkciók, amelyek *nem* szerepelnek ebben a verzióban például a JSON-tömbök és CSV-fájlok, az indexelés támogatása része [előzetes](search-api-2016-09-01-preview.md) és keresztül elérhető [.NETSDK4.0-s–előzetesverzió](https://aka.ms/search-sdk-preview).

Ez az SDK nem támogatja a [felügyeleti műveletek](https://docs.microsoft.com/rest/api/searchmanagement/) például létrehozása és a keresési szolgáltatások méretezése és API-kulcsok kezelése. Ha a Search-erőforrások kezelése a .NET-alkalmazásból van szüksége, használhatja a [Azure Search .NET SDK-t felügyeleti](https://aka.ms/search-mgmt-sdk).

## <a name="upgrading-to-the-latest-version-of-the-sdk"></a>Az SDK legújabb verziójára
Ha már használ az Azure Search .NET SDK egy régebbi verzióját, és szeretné az új általánosan elérhető verzióra frissít, [Ez a cikk](search-dotnet-sdk-migration-version-5.md) azt ismerteti, hogyan.

## <a name="requirements-for-the-sdk"></a>Az SDK-követelményei
1. Visual Studio 2017.
2. A saját Azure Search szolgáltatást. Az SDK használatához szüksége lesz a szolgáltatás és a egy vagy több API-kulcs neve. [Szolgáltatás létrehozása a portálon](search-create-service-portal.md) segít a fenti lépéseket.
3. Az Azure Search .NET SDK letöltése [NuGet-csomag](http://www.nuget.org/packages/Microsoft.Azure.Search) "NuGet-csomagok kezelése" a Visual Studio használatával. Csak keresse meg a csomag neveként `Microsoft.Azure.Search` on NuGet.org (vagy egy másik csomagot a fenti nevek, ha csak az funkciók egy részét).

Az Azure Search .NET SDK támogatja az alkalmazások a .NET-keretrendszer 4.5.2-es vagy újabb, valamint a .NET Core.

## <a name="core-scenarios"></a>Használhatók a legfontosabb forgatókönyvek
Számos dolgot kell hajtsa végre az alkalmazás. Ebben az oktatóanyagban alapvető forgatókönyvekben foglalkozik:

* Az index létrehozása
* Hozzáláthat a tárgymutató dokumentumok
* A teljes szöveges keresés és a szűrők használatával dokumentumok keresése

A következő példakód azt szemlélteti, minden egyes. Nyugodtan kódrészletek használhatja a saját alkalmazásában.

### <a name="overview"></a>Áttekintés
A mintaalkalmazás, azt fogja felfedezése létrehoz egy új "Hotels" nevet, index tölti fel, néhány dokumentumot, majd néhány keresési lekérdezéseket futtat. A fő program, az általános folyamatot bemutató a következő:

```csharp
// This sample shows how to delete, create, upload documents and query an index
static void Main(string[] args)
{
    IConfigurationBuilder builder = new ConfigurationBuilder().AddJsonFile("appsettings.json");
    IConfigurationRoot configuration = builder.Build();

    SearchServiceClient serviceClient = CreateSearchServiceClient(configuration);

    Console.WriteLine("{0}", "Deleting index...\n");
    DeleteHotelsIndexIfExists(serviceClient);

    Console.WriteLine("{0}", "Creating index...\n");
    CreateHotelsIndex(serviceClient);

    ISearchIndexClient indexClient = serviceClient.Indexes.GetClient("hotels");

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
DeleteHotelsIndexIfExists(serviceClient);

Console.WriteLine("{0}", "Creating index...\n");
CreateHotelsIndex(serviceClient);
```

Ezután az index kell kell feltöltenie. Ehhez fel kell egy `SearchIndexClient`. Beszerzése utólag két módja van: hozhat létre, vagy meghívásával `Indexes.GetClient` a a `SearchServiceClient`. Használjuk az utóbbi kényelmi célokat szolgál.

```csharp
ISearchIndexClient indexClient = serviceClient.Indexes.GetClient("hotels");
```

> [!NOTE]
> A keresőalkalmazásokban az indexkezelést és -feltöltést általában a keresési lekérdezésektől eltérő elem végzi. A `Indexes.GetClient` kényelmes megoldás az index feltöltésére, mivel így nem szükséges újabb `SearchCredentials` objektumot biztosítania. Ezt azon rendszergazdai kulcs átadásával hajtja végre, amelyet a `SearchServiceClient` elemnek az új `SearchIndexClient` objektumban történő létrehozásakor használt. A lekérdezéseket végrehajtó alkalmazás részeként azonban jobb megoldás a `SearchIndexClient` közvetlen létrehozása, így az egy rendszergazdai kulcs helyett lekérdezési kulcs formájában adható át. Ez megfelel a minimális jogosultság elvének, és biztonságosabbá teszi az alkalmazás segítségével. További információk az adminisztrációs kulcsok és a lekérdezési kulcsok annak [Itt](https://docs.microsoft.com/rest/api/searchservice/#authentication-and-authorization).
> 
> 

Most, hogy egy `SearchIndexClient`, hogy töltheti fel az indexet. Ez történik, végigvezetjük később egy másik módszerrel.

```csharp
Console.WriteLine("{0}", "Uploading documents...\n");
UploadDocuments(indexClient);
```

Végül azt néhány keresési lekérdezéseket, és megjeleníti az eredményeket. Most használjuk a különböző `SearchIndexClient`:

```csharp
ISearchIndexClient indexClientForQueries = CreateSearchIndexClient(configuration);

RunQueries(indexClientForQueries);
```

Hogy közelebbről vesz igénybe a `RunQueries` metódus később. Íme a kódot, amellyel létrehozza az új `SearchIndexClient`:

```csharp
private static SearchIndexClient CreateSearchIndexClient(IConfigurationRoot configuration)
{
    string searchServiceName = configuration["SearchServiceName"];
    string queryApiKey = configuration["SearchServiceQueryApiKey"];

    SearchIndexClient indexClient = new SearchIndexClient(searchServiceName, "hotels", new SearchCredentials(queryApiKey));
    return indexClient;
}
```

Most használjuk egy lekérdezési kulcsot, mert nem kell írási hozzáférést az index. Ezt az információt adhat meg a `appsettings.json` -fájlját a [mintaalkalmazás](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowTo).

Ez az alkalmazás futtatásakor egy érvényes szolgáltatásnév és API-kulcsokat, a kimenet ehhez hasonló kell kinéznie:

    Deleting index...
    
    Creating index...
    
    Uploading documents...
    
    Waiting for documents to be indexed...
    
    Search the entire index for the term 'budget' and return only the hotelName field:
    
    Name: Roach Motel
    
    Apply a filter to the index to find hotels cheaper than $150 per night, and return the hotelId and description:
    
    ID: 2   Description: Cheapest hotel in town
    ID: 3   Description: Close to town hall and the river
    
    Search the entire index, order by a specific field (lastRenovationDate) in descending order, take the top two results, and show only hotelName and lastRenovationDate:
    
    Name: Fancy Stay        Last renovated on: 6/27/2010 12:00:00 AM +00:00
    Name: Roach Motel       Last renovated on: 4/28/1982 12:00:00 AM +00:00
    
    Search the entire index for the term 'motel':
    
    ID: 2   Base rate: 79.99        Description: Cheapest hotel in town     Description (French): Hôtel le moins cher en ville      Name: Roach Motel       Category: Budget        Tags: [motel, budget]   Parking included: yes   Smoking allowed: yes    Last renovated on: 4/28/1982 12:00:00 AM +00:00 Rating: 1/5     Location: Latitude 49.678581, longitude -122.131577
    
    Complete.  Press any key to end application...

Ez a cikk végén található az alkalmazás a teljes forráskódot biztosítunk.

Ezután azt fogja annak minden egyes meghívott módszerek közelebbről is `Main`.

### <a name="creating-an-index"></a>Az index létrehozása
Miután létrehozott egy `SearchServiceClient`, a következő lépésként `Main` does esetén törölje a "hotels" index már létezik. Ezt a következő módszerrel:

```csharp
private static void DeleteHotelsIndexIfExists(SearchServiceClient serviceClient)
{
    if (serviceClient.Indexes.Exists("hotels"))
    {
        serviceClient.Indexes.Delete("hotels");
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
private static void CreateHotelsIndex(SearchServiceClient serviceClient)
{
    var definition = new Index()
    {
        Name = "hotels",
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

Mezők mellett is adhat pontozási profilok, javaslattevőket vagy CORS-beállítások az Index (ezek kimaradnak a mintát az áttekinthetőség). Az Index objektum és azok részlegei a további információt talál a [SDK-leírás](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.index#microsoft_azure_search_models_index)is, a a [Azure Search REST API-referencia](https://docs.microsoft.com/rest/api/searchservice/).

### <a name="populating-the-index"></a>Hozzáláthat a tárgymutató
A következő lépés `Main` , hogy az újonnan létrehozott index feltöltéséhez. Ez történik, a következő metódusban:

```csharp
private static void UploadDocuments(ISearchIndexClient indexClient)
{
    var hotels = new Hotel[]
    {
        new Hotel()
        { 
            HotelId = "1", 
            BaseRate = 199.0, 
            Description = "Best hotel in town",
            DescriptionFr = "Meilleur hôtel en ville",
            HotelName = "Fancy Stay",
            Category = "Luxury", 
            Tags = new[] { "pool", "view", "wifi", "concierge" },
            ParkingIncluded = false, 
            SmokingAllowed = false,
            LastRenovationDate = new DateTimeOffset(2010, 6, 27, 0, 0, 0, TimeSpan.Zero), 
            Rating = 5, 
            Location = GeographyPoint.Create(47.678581, -122.131577)
        },
        new Hotel()
        { 
            HotelId = "2", 
            BaseRate = 79.99,
            Description = "Cheapest hotel in town",
            DescriptionFr = "Hôtel le moins cher en ville",
            HotelName = "Roach Motel",
            Category = "Budget",
            Tags = new[] { "motel", "budget" },
            ParkingIncluded = true,
            SmokingAllowed = true,
            LastRenovationDate = new DateTimeOffset(1982, 4, 28, 0, 0, 0, TimeSpan.Zero),
            Rating = 1,
            Location = GeographyPoint.Create(49.678581, -122.131577)
        },
        new Hotel() 
        { 
            HotelId = "3", 
            BaseRate = 129.99,
            Description = "Close to town hall and the river"
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

Ez a metódus négy részből áll. Az első létrehoz egy tömbjét `Hotel` objektumok erre a célra a bemeneti adatok feltöltése az indexbe. Ezek az adatok nem változtatható az egyszerűség kedvéért. A saját alkalmazásában az adatok valószínűleg például egy SQL database egy külső adatforrásból származnak.

A második rész létrehoz egy `IndexBatch` tartalmazó dokumentumokat. Azt adja meg a műveletet, létrehozása, ebben az esetben meghívásával, a batch a alkalmazni kívánt `IndexBatch.Upload`. A batch majd töltenek fel az Azure Search-index által a `Documents.Index` metódust.

> [!NOTE]
> Ebben a példában azt csak tölt fel dokumentumokat. Ha meglévő dokumentumok egyesítse a módosításokat, vagy törölhetnek dokumentumokat szeretne, létrehozhat meghívásával kötegek `IndexBatch.Merge`, `IndexBatch.MergeOrUpload`, vagy `IndexBatch.Delete` helyette. Az egy kötegben különböző műveleteiről használhatók vegyesen meghívásával `IndexBatch.New`, gyűjteménye, amely veszi `IndexAction` objektumok, amelyek arra utasítja az Azure Search egy dokumentum egy adott művelet végrehajtásához. Hozhat létre minden egyes `IndexAction` saját műveletet a megfelelő metódus meghívásával `IndexAction.Merge`, `IndexAction.Upload`, és így tovább.
> 
> 

Ez a módszer harmadik része a "catch" blokk az indexelés egy fontos hibaesetét kezeli. Ha az Azure Search-szolgáltatásnak nem sikerül indexelnie a kötegben szereplő fájlok valamelyikét, a `Documents.Index` rendszer `IndexBatchException` választ ad. Ez akkor történhet meg, ha olyankor végzi a dokumentumok indexelését, amikor a szolgáltatás nagy terhelés alatt áll. **Javasoljuk, hogy a kódban explicit módon kezelje ezt az esetet.** Azon dokumentumok esetében, ahol az indexelés meghiúsult, elhalaszthatja azt, majd később újra megpróbálkozhat az indexeléssel, vagy a mintának megfelelően naplózhatja azt, és folytathatja a munkáját, esetleg – az alkalmazás adatkonzisztencia-követelményeitől függően – más műveletbe kezdhet.

> [!NOTE]
> Használhatja a `FindFailedActionsToRetry` metódus csak az előző hívása sikertelen műveleteket tartalmazó új kötegelt `Index`. A metódus dokumentált [Itt](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.indexbatchexception#Microsoft_Azure_Search_IndexBatchException_FindFailedActionsToRetry_Microsoft_Azure_Search_Models_IndexBatch_System_String_) , és nincs megfelelően használatával hatásának a megbeszélését [a StackOverflow-n](http://stackoverflow.com/questions/40012885/azure-search-net-sdk-how-to-use-findfailedactionstoretry).
>
>

Végül a `UploadDocuments` két másodperces metódus késéseket. Az Azure Search-szolgáltatásban az indexelés aszinkron módon történik, így a mintaalkalmazásnak egy rövid ideig várnia kell, amíg a rendszer meggyőződik arról, hogy a dokumentum kereshető. Ilyen mértékű késleltetésre kizárólag demók, tesztek és mintaalkalmazások esetében van szükség.

#### <a name="how-the-net-sdk-handles-documents"></a>A .NET SDK dokumentumkezelési módszere
Megfordulhat a fejében, hogy miként képes az Azure Search .NET SDK felhasználó által meghatározott `Hotel` osztályhoz hasonló példányok feltöltésére az indexbe. Annak érdekében, hogy kapcsolatos kérdésére választ kaphat, nézzük meg a `Hotel` osztály:

```csharp
using System;
using Microsoft.Azure.Search;
using Microsoft.Azure.Search.Models;
using Microsoft.Spatial;
using Newtonsoft.Json;

// The SerializePropertyNamesAsCamelCase attribute is defined in the Azure Search .NET SDK.
// It ensures that Pascal-case property names in the model class are mapped to camel-case
// field names in the index.
[SerializePropertyNamesAsCamelCase]
public partial class Hotel
{
    [System.ComponentModel.DataAnnotations.Key]
    [IsFilterable]
    public string HotelId { get; set; }

    [IsFilterable, IsSortable, IsFacetable]
    public double? BaseRate { get; set; }

    [IsSearchable]
    public string Description { get; set; }

    [IsSearchable]
    [Analyzer(AnalyzerName.AsString.FrLucene)]
    [JsonProperty("description_fr")]
    public string DescriptionFr { get; set; }

    [IsSearchable, IsFilterable, IsSortable]
    public string HotelName { get; set; }

    [IsSearchable, IsFilterable, IsSortable, IsFacetable]
    public string Category { get; set; }

    [IsSearchable, IsFilterable, IsFacetable]
    public string[] Tags { get; set; }

    [IsFilterable, IsFacetable]
    public bool? ParkingIncluded { get; set; }

    [IsFilterable, IsFacetable]
    public bool? SmokingAllowed { get; set; }

    [IsFilterable, IsSortable, IsFacetable]
    public DateTimeOffset? LastRenovationDate { get; set; }

    [IsFilterable, IsSortable, IsFacetable]
    public int? Rating { get; set; }

    [IsFilterable, IsSortable]
    public GeographyPoint Location { get; set; }
}
```

Az első szembetűnő dolog, hogy, hogy minden egyes nyilvános tulajdonsága `Hotel` felel meg az index definícióját, de egy lényeges különbséggel mező: Az egyes mezők neve során minden egyes nyilvános tulajdonsága neve kezdődik ("nagybetűs"), kisbetűvel `Hotel` egy nagybetűt ("Pascal eset") kezdődik. Gyakran kerül sor erre olyan adatkötést végző .NET-alkalmazások esetében, ahol a célséma vezérlése az alkalmazás fejlesztőjének hatáskörén kívül esik. A .NET elnevezési irányelveinek megsértése helyett (a tulajdonságnevek kisbetűs megadásával), utasíthatja az SDK-t a tulajdonságnevek automatikus kisbetűs leképezésére a `[SerializePropertyNamesAsCamelCase]` attribútummal.

> [!NOTE]
> Az Azure Search .NET SDK a [NewtonSoft JSON.NET](http://www.newtonsoft.com/json/help/html/Introduction.htm) könyvtárat használja az egyéni modellek JSON-ból és JSON-ba történő szerializálására és deszerializálására. A szerializálás szükség szerint testre szabható. További részletekért lásd: [egyéni szerializálás a JSON.NET](#JsonDotNet).
> 
> 

Figyelje meg, hogy a második lépésben olyan attribútumok, amelyek például `IsFilterable`, `IsSearchable`, `Key`, és `Analyzer` , amely minden egyes nyilvános tulajdonsága megadhat. Ezek az attribútumok leképezése közvetlenül a [megfelelő attribútumok az Azure Search-index](https://docs.microsoft.com/rest/api/searchservice/create-index#request). A `FieldBuilder` osztályt használja ezeket az index Meződefiníciók létrehozására.

Tudnivalók a harmadik lényeg a `Hotel` osztály a nyilvános tulajdonságok adattípusa. Ezeket a tulajdonságokat a .NET-típusú képezze le az index definícióját a mezőtípusokra. Például a rendszer a `Edm.String` típusú `Category` szöveges tulajdonságot a `category` mezőbe képezi le. Hasonló típusleképezés történik a `bool?` és `Edm.Boolean`, illetve a `DateTimeOffset?` és `Edm.DateTimeOffset` között is. A típusleképezés vonatkozó szabályainak dokumentációja az [Azure Search .NET SDK-referenciában](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.idocumentsoperations#Microsoft_Azure_Search_IDocumentsOperations_GetWithHttpMessagesAsync__1_System_String_System_Collections_Generic_IEnumerable_System_String__Microsoft_Azure_Search_Models_SearchRequestOptions_System_Collections_Generic_Dictionary_System_String_System_Collections_Generic_List_System_String___System_Threading_CancellationToken_), a `Documents.Get` metódusnál található. A `FieldBuilder` osztály a megfelelőségről gondoskodik, de továbbra is lehet annak megértése, abban az esetben szerializációs hárítsa el kell.

A saját osztályok dokumentumokként használandó képessége mindkét irányban; működik Is lekérdezheti a keresési eredményeket, és rendelkezik az SDK-val automatikusan deszerializáltathatja azokat a választott típusra láthatóak lesznek a következő szakaszban.

> [!NOTE]
> Az Azure Search .NET SDK támogatja a `Document` osztályt használó, dinamikus dokumentumtípusokat is, amely alatt a mezők neveinek értékekre történő kulcs/érték-leképezését értjük. Ez olyan helyzetekben hasznos, ha például a tervezés időpontjában az indexséma még nem ismert, illetve ha az adott modellosztályokhoz történő kötés nehézkes volna. Az SDK-ban lévő összes, dokumentumokkal foglalkozó módszer a `Document` osztállyal kompatibilis túlterhelésekkel rendelkezik, valamint olyan szigorú típusmegadású túlterhelésekkel, amelyek általános típusú paramétert vesznek fel. Kizárólag az utóbbiakat használjuk a mintakód ebben az oktatóanyagban. A `Document` osztály örökli `Dictionary<string, object>`. Egyéb információk [Itt](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.document#microsoft_azure_search_models_document).
> 
> 

**Miért használjon nullázható adattípusokat?**

Az Azure Search-indexre leképezést végző, saját modellosztályok létrehozásakor javasoljuk, hogy például a `bool` és `int` értéktípusok tulajdonságainak megadása nullázhatóként történjen (például `bool` helyett `bool?`). Nem nullázható tulajdonság használatakor **garantálnia** kell, hogy az index egyetlen dokumentuma sem tartalmaz az adott mezőben null értéket. Ennek kényszerítéséhez sem az SDK, sem az Azure Search szolgáltatás nem nyújt segítséget.

Ez nem csupán elméleti szempont: Képzelje el egy forgatókönyvet, ahol hozzáadhat egy új mezőt típusú, meglévő indexhez `Edm.Int32`. Az indexdefiníció frissítését követően ehhez a mezőhöz minden dokumentumban null érték tartozik (mivel az Azure Search szolgáltatásban az összes értéktípus nullázható). Ha ezt követően egy modellosztályt úgy alkalmaz, hogy ehhez a mezőhöz nem nullázható `int` tulajdonságot ad meg, a dokumentumok lekérdezésének megkísérlésekor egy ehhez hasonló `JsonSerializationException` választ kap:

    Error converting value {null} to type 'System.Int32'. Path 'IntValue'.

Ezért javasoljuk, hogy a modellosztályokban nullázható értéktípusokat használjon.

<a name="JsonDotNet"></a>

#### <a name="custom-serialization-with-jsonnet"></a>Egyéni szerializálás a JSON.NET használatával
Az SDK-t használ JSON.NET szerializálásához és deszerializálásához dokumentumokat. Testre szabhatja a szerializálási és szükség esetén definiálásával saját deszerializálási `JsonConverter` vagy `IContractResolver` (lásd a [JSON.NET dokumentáció](http://www.newtonsoft.com/json/help/html/Introduction.htm) további részletekért). Ez hasznos lehet, ha meg szeretné alkalmazkodnak az alkalmazás használatát az Azure Search, és más speciális forgatókönyvekhez egy meglévő modell osztály. Ha például egyéni sorba rendezésre segítségével:

* Belefoglalhat vagy kizárhat bizonyos tulajdonságok modellosztály-dokumentum mezőként tárolják.
* Képezze le a kódban a tulajdonságnevek és az index mezőnevek között.
* Hozzon létre egyéni attribútumokat, amelyek tulajdonságok hozzárendelése a dokumentum mezők is használható.

Példák egyedi szerializálás az egységteszteket a végrehajtása az Azure Search .NET SDK a Githubon találja. Van egy jó kiindulási pont [Ez a mappa](https://github.com/Azure/azure-sdk-for-net/tree/AutoRest/src/Search/Search.Tests/Tests/Models). Az egyéni sorba rendezésre tesztek által használt osztályok tartalmazza.

### <a name="searching-for-documents-in-the-index"></a>Az indexben található dokumentumok keresése
A mintaalkalmazásban az utolsó lépés az egyes dokumentumok indexben kereshet. A következő metódust teszi ezt:

```csharp
private static void RunQueries(ISearchIndexClient indexClient)
{
    SearchParameters parameters;
    DocumentSearchResult<Hotel> results;

    Console.WriteLine("Search the entire index for the term 'budget' and return only the hotelName field:\n");

    parameters =
        new SearchParameters()
        {
            Select = new[] { "hotelName" }
        };

    results = indexClient.Documents.Search<Hotel>("budget", parameters);

    WriteDocuments(results);

    Console.Write("Apply a filter to the index to find hotels cheaper than $150 per night, ");
    Console.WriteLine("and return the hotelId and description:\n");

    parameters =
        new SearchParameters()
        {
            Filter = "baseRate lt 150",
            Select = new[] { "hotelId", "description" }
        };

    results = indexClient.Documents.Search<Hotel>("*", parameters);

    WriteDocuments(results);

    Console.Write("Search the entire index, order by a specific field (lastRenovationDate) ");
    Console.Write("in descending order, take the top two results, and show only hotelName and ");
    Console.WriteLine("lastRenovationDate:\n");

    parameters =
        new SearchParameters()
        {
            OrderBy = new[] { "lastRenovationDate desc" },
            Select = new[] { "hotelName", "lastRenovationDate" },
            Top = 2
        };

    results = indexClient.Documents.Search<Hotel>("*", parameters);

    WriteDocuments(results);

    Console.WriteLine("Search the entire index for the term 'motel':\n");

    parameters = new SearchParameters();
    results = indexClient.Documents.Search<Hotel>("motel", parameters);

    WriteDocuments(results);
}
```

Minden alkalommal, amikor a lekérdezés végrehajtása, ezt a módszert először létrehoz egy új `SearchParameters` objektum. Ez szolgál, például rendezés, szűrés, lapozási és értékkorlátozás a lekérdezés további beállítások megadásához. Ez a módszer a beállítás folyamatban van a `Filter`, `Select`, `OrderBy`, és `Top` különböző lekérdezéseket tulajdonsága. Az összes a `SearchParameters` tulajdonságok szerepelnek [Itt](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.searchparameters).

A következő lépés, hogy ténylegesen hajtsa végre a keresési lekérdezést. Ebben az esetben használja a `Documents.Search` metódust. Az egyes lekérdezések használandó karakterlánc a keresett szöveget adjuk át (vagy `"*"` Ha nincs keresési szöveg), valamint a korábban létrehozott keresési paraméterek. Azt is megadhatja `Hotel` a típusát paraméterként `Documents.Search`, amely tájékoztatja, hogy az SDK-val dokumentumok a keresési eredmények deszerializálása típusú objektumokat be `Hotel`.

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
        Select = new[] { "hotelName" }
    };

results = indexClient.Documents.Search<Hotel>("budget", parameters);

WriteDocuments(results);
```

Ebben az esetben azt hotels, amelyek megfelelnek a "budget" szóra keres, és szeretnénk visszatéréshez csak a Szálloda neve, azokat a `Select` paraméter. Az eredmények a következők:

    Name: Roach Motel

Ezután szeretnénk keresse meg a "Hotels" kisebb, mint 150 USD éjszakai arány, és csak a Szálloda azonosítója és a leírását adja vissza:

```csharp
parameters =
    new SearchParameters()
    {
        Filter = "baseRate lt 150",
        Select = new[] { "hotelId", "description" }
    };

results = indexClient.Documents.Search<Hotel>("*", parameters);

WriteDocuments(results);
```

Ez a lekérdezés használ egy OData `$filter` kifejezés, `baseRate lt 150`, a dokumentumok indexben szűréséhez. Talál további információt az OData-szűrőszintaxis, amely támogatja az Azure Search [Itt](https://docs.microsoft.com/rest/api/searchservice/OData-Expression-Syntax-for-Azure-Search).

Az alábbiakban a lekérdezési eredmények:

    ID: 2   Description: Cheapest hotel in town
    ID: 3   Description: Close to town hall and the river

Ezután szeretnénk megtalálni a felső két hotels, amely rendelkezik lett utoljára felújított és a Szálloda neve és az utolsó felújítás dátumának megjelenítése. A kód itt látható: 

```csharp
parameters =
    new SearchParameters()
    {
        OrderBy = new[] { "lastRenovationDate desc" },
        Select = new[] { "hotelName", "lastRenovationDate" },
        Top = 2
    };

results = indexClient.Documents.Search<Hotel>("*", parameters);

WriteDocuments(results);
```

Ebben az esetben újra használjuk OData-szűrőszintaxis adja meg a `OrderBy` paraméterrel `lastRenovationDate desc`. Azt is beállíthatja `Top` a 2. Győződjön meg arról, hogy csak az első két dokumentum beolvasása. Korábban, hogy állíthat be `Select` mezőket a rendszer visszalépteti megadásához.

Az eredmények a következők:

    Name: Fancy Stay        Last renovated on: 6/27/2010 12:00:00 AM +00:00
    Name: Roach Motel       Last renovated on: 4/28/1982 12:00:00 AM +00:00

Végül szeretnénk található összes "Hotels" megfelelő "amelyben" szót:

```csharp
parameters = new SearchParameters();
results = indexClient.Documents.Search<Hotel>("motel", parameters);

WriteDocuments(results);
```

Az alábbiakban az eredményeket, amely az összes mező tartalmazza, mivel nem azt adta meg, és a `Select` tulajdonság:

    ID: 2   Base rate: 79.99        Description: Cheapest hotel in town     Description (French): Hôtel le moins cher en ville      Name: Roach Motel       Category: Budget        Tags: [motel, budget]   Parking included: yes   Smoking allowed: yes    Last renovated on: 4/28/1982 12:00:00 AM +00:00 Rating: 1/5     Location: Latitude 49.678581, longitude -122.131577

Ez a lépés befejezi az oktatóanyag, de itt nem állnak le. **További lépések** Azure Search-ról további további forrásokat biztosít.

## <a name="next-steps"></a>További lépések
* Nézze át a [.NET SDK](https://docs.microsoft.com/dotnet/api/microsoft.azure.search) és a [REST API](https://docs.microsoft.com/rest/api/searchservice/) referenciáit.
* Felülvizsgálat [elnevezési konvenciók](https://docs.microsoft.com/rest/api/searchservice/Naming-rules) további a különféle objektumok elnevezési szabályai.
* Felülvizsgálat [által támogatott adattípusokkal](https://docs.microsoft.com/rest/api/searchservice/Supported-data-types) az Azure Search szolgáltatásban.
