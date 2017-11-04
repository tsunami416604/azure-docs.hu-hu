---
title: "Hogyan használható az Azure Search .NET-alkalmazásból |} Microsoft Docs"
description: "Azure Search .NET-alkalmazás használata"
services: search
documentationcenter: 
author: brjohnstmsft
manager: jlembicz
editor: 
ms.assetid: 93653341-c05f-4cfd-be45-bb877f964fcb
ms.service: search
ms.devlang: dotnet
ms.workload: search
ms.topic: article
ms.tgt_pltfrm: na
ms.date: 05/22/2017
ms.author: brjohnst
ms.openlocfilehash: 7273ae6a698f2af52e78ea2aae9ca5cd80f6a2b1
ms.sourcegitcommit: f8437edf5de144b40aed00af5c52a20e35d10ba1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/03/2017
---
# <a name="how-to-use-azure-search-from-a-net-application"></a>Azure Search .NET-alkalmazás használata
Ez a cikk a forgatókönyv az első működik, és a rendszer a [Azure Search .NET SDK](https://aka.ms/search-sdk). A .NET SDK használatával valósít meg egy hatékony keresési élményt biztosít az alkalmazás Azure Search használatával.

## <a name="whats-in-the-azure-search-sdk"></a>Mi az az Azure SDK keresése
Az SDK-t tartalmaz egy ügyféloldali kódtár `Microsoft.Azure.Search`. Lehetővé teszi kezeli az indexek, az adatforrások és az indexelők, valamint feltöltése és dokumentumok kezeléséhez, és hajtsa végre a lekérdezéseket, anélkül, HTTP és a JSON részleteit kezelésére.

Az ügyféloldali kódtár határozza meg, például osztályok `Index`, `Field`, és `Document`, illetve műveletek, például `Indexes.Create` és `Documents.Search` a a `SearchServiceClient` és `SearchIndexClient` osztályok. Ezeket az osztályokat a következő névterek szerint vannak csoportosítva:

* [Microsoft.Azure.Search](https://docs.microsoft.com/dotnet/api/microsoft.azure.search)
* [Microsoft.Azure.Search.Models](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models)

Az aktuális az Azure Search .NET SDK verziója most általánosan elérhető. Ha azt szeretné, vegye figyelembe a következő verziójára való visszajelzést, kérjük, látogasson el a [visszajelzés](https://feedback.azure.com/forums/263029-azure-search/).

Támogatja a .NET SDK `2016-09-01` , a [Azure Search REST API](https://docs.microsoft.com/rest/api/searchservice/). Ebben a verzióban mostantól tartalmazza az egyéni lekérdezések és Azure-Blob és az Azure tábla indexelő támogatást. Az előzetes funkciók, amelyek *nem* verzióhoz, például az indexelés JSON és a CSV-fájlok támogatását szerepelnek [előzetes](search-api-2016-09-01-preview.md) és keresztül elérhető [4.0.1-preview verziójának a .NET SDK](https://aka.ms/search-sdk-preview).

Ez az SDK nem támogatja a [felügyeleti műveletek](https://docs.microsoft.com/rest/api/searchmanagement/) például létrehozása és a keresési szolgáltatás méretezése és API-kulcsokat kezelése. Ha a keresési erőforrások kezelése a .NET-alkalmazás van szüksége, használhatja a [Azure Search .NET SDK-t felügyeleti](https://aka.ms/search-mgmt-sdk).

## <a name="upgrading-to-the-latest-version-of-the-sdk"></a>Az SDK legújabb verziójára
Ha már használja az Azure Search .NET SDK régebbi verziója, és frissítse az általánosan elérhető új verzióra szeretné [Ez a cikk](search-dotnet-sdk-migration.md) ismerteti, hogyan.

## <a name="requirements-for-the-sdk"></a>Az SDK követelményei
1. A Visual Studio 2017.
2. A saját Azure Search szolgáltatás. Az SDK használatához szüksége lesz a nevét, valamint a szolgáltatás egy vagy több API-kulcsokat. [A szolgáltatás létrehozása a portálon](search-create-service-portal.md) segít a fenti lépéseket.
3. Töltse le az Azure Search .NET SDK [NuGet-csomag](http://www.nuget.org/packages/Microsoft.Azure.Search) a Visual Studio "Manage NuGet Packages" használatával. A csomag neve csak keressen `Microsoft.Azure.Search` NuGet.org meg.

Az Azure Search .NET SDK támogatja a .NET-keretrendszer 4.6 és a .NET Core célzó alkalmazásokat.

## <a name="core-scenarios"></a>Legfontosabb forgatókönyvek
Számos szempontot következőket kell tennie az alkalmazás. Ez az oktatóanyag azt érintünk core forgatókönyvekben:

* Az index létrehozása
* A dokumentumok index feltöltése
* A teljes szöveges keresés és a szűrők használatával dokumentumok keresése

Az alábbi mintakód bemutatja ezen. Szabadon használhatja a kódrészleteket a saját alkalmazásban.

### <a name="overview"></a>Áttekintés
A mintaalkalmazás, azt fogja felfedezése létrehoz egy új "hotels" nevű index tölti fel az néhány dokumentumok, akkor néhány keresési lekérdezést hajt végre. A fő program, megjeleníti az általános folyamat a következő:

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

Végigvezetjük a lépésről lépésre. Először létre kell hozni egy új `SearchServiceClient`. Ez az objektum indexek kezelését teszi lehetővé. Egy kiszámításához meg kell adnia az Azure Search szolgáltatás neve, valamint az adminisztrációs API-kulcsot. Ezt az információt is megadhatja a `appsettings.json` fájlt a [mintaalkalmazás](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowTo).

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
> Ha megad egy helytelen kulcsot (például egy lekérdezési kulcsot ahol egy adminisztrációs kulcsot volt szükség), a `SearchServiceClient` kivételhibát egy `CloudException` , a hiba üzenet "Tiltott" egy művelet metódust hívja meg, mint például az első alkalommal `Indexes.Create`. Ha ez történik, ellenőrizze az API-kulcsot.
> 
> 

A következő néhány sor metódushívások "Hotels" nevet adtuk, először törlése, ha már létezik egy index létrehozásához. Végigvezetjük ezen módszerek egy kicsit később.

```csharp
Console.WriteLine("{0}", "Deleting index...\n");
DeleteHotelsIndexIfExists(serviceClient);

Console.WriteLine("{0}", "Creating index...\n");
CreateHotelsIndex(serviceClient);
```

Ezt követően kell kell feltöltenie. Ehhez fel kell egy `SearchIndexClient`. Az beszerzése utólag két módja van: hozhat létre, azt, vagy meghívásával `Indexes.GetClient` a a `SearchServiceClient`. Használjuk az utóbbi kényelmét szolgálja.

```csharp
ISearchIndexClient indexClient = serviceClient.Indexes.GetClient("hotels");
```

> [!NOTE]
> A keresőalkalmazásokban az indexkezelést és -feltöltést általában a keresési lekérdezésektől eltérő elem végzi. A `Indexes.GetClient` kényelmes megoldás az index feltöltésére, mivel így nem szükséges újabb `SearchCredentials` objektumot biztosítania. Ezt azon rendszergazdai kulcs átadásával hajtja végre, amelyet a `SearchServiceClient` elemnek az új `SearchIndexClient` objektumban történő létrehozásakor használt. A lekérdezéseket végrehajtó alkalmazás részeként azonban jobb megoldás a `SearchIndexClient` közvetlen létrehozása, így az egy rendszergazdai kulcs helyett lekérdezési kulcs formájában adható át. Ez a legalacsonyabb jogosultsági szint elvét konzisztens, és segít biztonságosabbá teszi az alkalmazás. További információk az adminisztrációs kulcsok és a lekérdezési kulcsok található [Itt](https://docs.microsoft.com/rest/api/searchservice/#authentication-and-authorization).
> 
> 

Most, hogy egy `SearchIndexClient`, azt töltheti fel az index. Ez végigvezetjük később más módon történik.

```csharp
Console.WriteLine("{0}", "Uploading documents...\n");
UploadDocuments(indexClient);
```

Végül azt néhány keresési-lekérdezéseket hajt végre, és az eredmények megjelenítéséhez. Most használjuk a különböző `SearchIndexClient`:

```csharp
ISearchIndexClient indexClientForQueries = CreateSearchIndexClient(configuration);

RunQueries(indexClientForQueries);
```

Most elindítjuk részletes bemutatása a `RunQueries` metódus később. Új kód `SearchIndexClient`:

```csharp
private static SearchIndexClient CreateSearchIndexClient(IConfigurationRoot configuration)
{
    string searchServiceName = configuration["SearchServiceName"];
    string queryApiKey = configuration["SearchServiceQueryApiKey"];

    SearchIndexClient indexClient = new SearchIndexClient(searchServiceName, "hotels", new SearchCredentials(queryApiKey));
    return indexClient;
}
```

Most használjuk a lekérdezési kulcs mivel jelenleg nem írási hozzáférésre van szüksége az index. Ezt az információt is megadhatja a `appsettings.json` fájlt a [mintaalkalmazás](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowTo).

Ha az alkalmazás futtatásához a egy érvényes szolgáltatásnevet és API-kulcsokat, a kimenet ehhez hasonló kell kinéznie:

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

Ez a cikk végén az alkalmazás a teljes forráskód valósul meg.

A következő most elindítjuk szorosabb meg az egyes módszerek által meghívott `Main`.

### <a name="creating-an-index"></a>Az index létrehozása
Miután létrehozta a `SearchServiceClient`, a következő lépésként `Main` does esetén törlése a "Hotels nevű" index már létezik. Ezt a következő módszerrel:

```csharp
private static void DeleteHotelsIndexIfExists(SearchServiceClient serviceClient)
{
    if (serviceClient.Indexes.Exists("hotels"))
    {
        serviceClient.Indexes.Delete("hotels");
    }
}
```

Ezt a módszert használja az adott `SearchServiceClient` ellenőrizze, hogy létezik-e az indexet, és ha igen, törölje azt.

> [!NOTE]
> Ebben a cikkben a példakód az egyszerűség érdekében az Azure Search .NET SDK szinkron módszereit használja. Azt javasoljuk, hogy a méretezhetőség és a gyors válaszadás érdekében használja saját alkalmazásaiban az aszinkron módszereket. Például a metódusban használhat `ExistsAsync` és `DeleteAsync` helyett `Exists` és `Delete`.
> 
> 

Ezt követően `Main` hoz létre egy új "Hotels nevű" index által a metódus hívása:

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

Ezzel a módszerrel hoz létre egy új `Index` listáját tartalmazó objektum `Field` objektumok sémájának definiálásához az új index. Egyes mezők nevét, adattípus és a keresés viselkedését meghatározó számos attribútum rendelkezik. A `FieldBuilder` osztály reflexió használatával létrehozhat egy `Field` objektumok nyilvános tulajdonságokat és az attribútumok megvizsgálásával az index az adott `Hotel` osztály modell. Részletes bemutatása lesz vesszük a `Hotel` osztály később.

> [!NOTE]
> Bármikor létrehozhat listájának `Field` objektumok közvetlenül helyett `FieldBuilder` szükség esetén. Például előfordulhat, hogy nem használni kívánt egy modellosztállyal, vagy használjon egy meglévő modell osztály nem kívánt attribútumok hozzáadásával módosítani szeretne.
>
> 

Mezők mellett azt is megteheti pontozási profil, a javaslattevők vagy a CORS-beállítások az indexhez (ezek hiányoznak kivonatosan mutatja a mintából). Az Index objektum és a bennük foglalt részeit további információ található a [SDK referenciáit](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.index#microsoft_azure_search_models_index), valamint ennek a a [Azure Search REST API-referencia](https://docs.microsoft.com/rest/api/searchservice/).

### <a name="populating-the-index"></a>Az index feltöltése
A következő lépéssel `Main` az újonnan létrehozott index feltöltése. Ezt a következő metódust:

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

Ez a metódus négy részből áll. Az első létrehoz egy tömbjét `Hotel` objektumok feltölteni az index a bemeneti adatok erre a célra. Ezek az adatok nem változtatható az egyszerűség érdekében. A saját alkalmazásban az adatok valószínűleg határozza meg egy külső adatforrásból, például az SQL-adatbázis.

A második rész létrehoz egy `IndexBatch` tartalmazó a dokumentumokat. Megadja a kötegelt hoz létre, ebben az esetben meghívásával időpontjában alkalmazni kívánt műveletet `IndexBatch.Upload`. A kötegelt majd tölt fel az Azure Search-index által az a `Documents.Index` metódust.

> [!NOTE]
> Ebben a példában azt csak feltölteni dokumentumokat. Ha szeretne változtatások egyesítése a létező dokumentumok vagy törölhetnek dokumentumokat, létrehozhat meghívásával kötegek `IndexBatch.Merge`, `IndexBatch.MergeOrUpload`, vagy `IndexBatch.Delete` helyette. A kötegek különböző műveletei adjunk meghívásával `IndexBatch.New`, gyűjteménye, amely veszi `IndexAction` objektumok, amelyek arra utasítja a Azure Search a dokumentum az adott művelet végrehajtására. Minden egyes hozhat létre `IndexAction` saját műveletet a megfelelő metódus meghívásával `IndexAction.Merge`, `IndexAction.Upload`, és így tovább.
> 
> 

Ez a módszer harmadik része egy catch blokkba, amely kezeli az egyik fontos hiba esetében az indexeléshez. Ha az Azure Search-szolgáltatásnak nem sikerül indexelnie a kötegben szereplő fájlok valamelyikét, a `Documents.Index` rendszer `IndexBatchException` választ ad. Ez akkor történhet meg, ha olyankor végzi a dokumentumok indexelését, amikor a szolgáltatás nagy terhelés alatt áll. **Javasoljuk, hogy a kódban explicit módon kezelje ezt az esetet.** Azon dokumentumok esetében, ahol az indexelés meghiúsult, elhalaszthatja azt, majd később újra megpróbálkozhat az indexeléssel, vagy a mintának megfelelően naplózhatja azt, és folytathatja a munkáját, esetleg – az alkalmazás adatkonzisztencia-követelményeitől függően – más műveletbe kezdhet.

> [!NOTE]
> Használhatja a `FindFailedActionsToRetry` metódus csak egy korábbi hívás a sikertelen műveleteket tartalmazó új köteg összeállításához `Index`. A metódus dokumentált [Itt](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.indexbatchexception#Microsoft_Azure_Search_IndexBatchException_FindFailedActionsToRetry_Microsoft_Azure_Search_Models_IndexBatch_System_String_) , és annak megfelelően használatát tárgyalja [a StackOverflow](http://stackoverflow.com/questions/40012885/azure-search-net-sdk-how-to-use-findfailedactionstoretry).
>
>

Végezetül a `UploadDocuments` metódus késések két másodpercig. Az Azure Search-szolgáltatásban az indexelés aszinkron módon történik, így a mintaalkalmazásnak egy rövid ideig várnia kell, amíg a rendszer meggyőződik arról, hogy a dokumentum kereshető. Ilyen mértékű késleltetésre kizárólag demók, tesztek és mintaalkalmazások esetében van szükség.

#### <a name="how-the-net-sdk-handles-documents"></a>A .NET SDK dokumentumkezelési módszere
Megfordulhat a fejében, hogy miként képes az Azure Search .NET SDK felhasználó által meghatározott `Hotel` osztályhoz hasonló példányok feltöltésére az indexbe. A kérdés megválaszolásához, vizsgáljuk meg a `Hotel` osztály:

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

Az első szembetűnő dolog, hogy a `Hotel` minden egyes nyilvános tulajdonsága az indexdefiníció egy-egy mezőjének felel meg, egy lényeges különbséggel: a mezők neve minden esetben kisbetűvel, míg a `Hotel` nyilvános tulajdonságainak neve nagybetűvel kezdődik. Gyakran kerül sor erre olyan adatkötést végző .NET-alkalmazások esetében, ahol a célséma vezérlése az alkalmazás fejlesztőjének hatáskörén kívül esik. A .NET elnevezési irányelveinek megsértése helyett (a tulajdonságnevek kisbetűs megadásával), utasíthatja az SDK-t a tulajdonságnevek automatikus kisbetűs leképezésére a `[SerializePropertyNamesAsCamelCase]` attribútummal.

> [!NOTE]
> Az Azure Search .NET SDK a [NewtonSoft JSON.NET](http://www.newtonsoft.com/json/help/html/Introduction.htm) könyvtárat használja az egyéni modellek JSON-ból és JSON-ba történő szerializálására és deszerializálására. A szerializálás szükség szerint testre szabható. További részletekért lásd: [JSON.NET az egyéni szerializálási](#JsonDotNet).
> 
> 

Figyelje meg, a második lépés olyan attribútumok, amelyek például `IsFilterable`, `IsSearchable`, `Key`, és `Analyzer` , amely minden egyes nyilvános tulajdonsága adja. Ezek az attribútumok hozzárendelését közvetlenül a [megfelelő attribútumok az Azure Search-index](https://docs.microsoft.com/rest/api/searchservice/create-index#request). A `FieldBuilder` osztály használja ezeket az index definícióját mező összeállításához.

A harmadik fontos dolog kapcsolatos a `Hotel` osztály az adattípusok a nyilvános tulajdonságainak. A .NET típusú ezeket a tulajdonságokat a megfelelő típusú az index definícióját a hozzárendelése. Például a rendszer a `Edm.String` típusú `Category` szöveges tulajdonságot a `category` mezőbe képezi le. Hasonló típusleképezés történik a `bool?` és `Edm.Boolean`, illetve a `DateTimeOffset?` és `Edm.DateTimeOffset` között is. A típusleképezés vonatkozó szabályainak dokumentációja az [Azure Search .NET SDK-referenciában](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.idocumentsoperations#Microsoft_Azure_Search_IDocumentsOperations_GetWithHttpMessagesAsync__1_System_String_System_Collections_Generic_IEnumerable_System_String__Microsoft_Azure_Search_Models_SearchRequestOptions_System_Collections_Generic_Dictionary_System_String_System_Collections_Generic_List_System_String___System_Threading_CancellationToken_), a `Documents.Get` metódusnál található. A `FieldBuilder` osztály gondoskodik meg ezt a hozzárendelést, de továbbra is hasznos lehet megérteni, abban az esetben kell elhárítania a szerializálási probléma merül fel.

Ez nem tudják használni a saját osztályok-dokumentumokként működik mindkét irányban; Keresési eredmények beolvasásához is, és látható lesz a következő szakaszban az SDK-val automatikusan deszerializálni őket a kiválasztott típusú lehet.

> [!NOTE]
> Az Azure Search .NET SDK támogatja a `Document` osztályt használó, dinamikus dokumentumtípusokat is, amely alatt a mezők neveinek értékekre történő kulcs/érték-leképezését értjük. Ez olyan helyzetekben hasznos, ha például a tervezés időpontjában az indexséma még nem ismert, illetve ha az adott modellosztályokhoz történő kötés nehézkes volna. Az SDK-ban lévő összes, dokumentumokkal foglalkozó módszer a `Document` osztállyal kompatibilis túlterhelésekkel rendelkezik, valamint olyan szigorú típusmegadású túlterhelésekkel, amelyek általános típusú paramétert vesznek fel. Csak az utóbbi használnak a mintakódot az oktatóanyag. A `Document` osztály örökli `Dictionary<string, object>`. Más részletei [Itt](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.document#microsoft_azure_search_models_document).
> 
> 

**Miért használjon nullázható adattípusokat?**

Az Azure Search-indexre leképezést végző, saját modellosztályok létrehozásakor javasoljuk, hogy például a `bool` és `int` értéktípusok tulajdonságainak megadása nullázhatóként történjen (például `bool` helyett `bool?`). Nem nullázható tulajdonság használatakor **garantálnia** kell, hogy az index egyetlen dokumentuma sem tartalmaz az adott mezőben null értéket. Ennek kényszerítéséhez sem az SDK, sem az Azure Search szolgáltatás nem nyújt segítséget.

Ennek nem csupán elméleti jelentősége van: képzeljünk el például egy olyan alkalmazási helyzetet, ahol egy `Edm.Int32` típusú, meglévő indexhez új mezőt kell hozzáadnunk. Az indexdefiníció frissítését követően ehhez a mezőhöz minden dokumentumban null érték tartozik (mivel az Azure Search szolgáltatásban az összes értéktípus nullázható). Ha ezt követően egy modellosztályt úgy alkalmaz, hogy ehhez a mezőhöz nem nullázható `int` tulajdonságot ad meg, a dokumentumok lekérdezésének megkísérlésekor egy ehhez hasonló `JsonSerializationException` választ kap:

    Error converting value {null} to type 'System.Int32'. Path 'IntValue'.

Ezért javasoljuk, hogy a modellosztályokban nullázható értéktípusokat használjon.

<a name="JsonDotNet"></a>

#### <a name="custom-serialization-with-jsonnet"></a>A JSON.NET egyedi szerializálás
Az SDK JSON.NET szerializálása és deszerializálása dokumentumok használ. Testre szabhatja a szerializálás és szükség esetén meghatározhat egy saját deszerializálási `JsonConverter` vagy `IContractResolver` (lásd a [JSON.NET dokumentáció](http://www.newtonsoft.com/json/help/html/Introduction.htm) további részletekért). Ez akkor lehet hasznos, ha meg szeretné alkalmazkodnak az alkalmazás Azure Search és egyéb speciális forgatókönyvekhez használható modellt egy meglévő osztály. Például az egyedi szerializálás lehetővé teszi:

* Tartalmaznak, vagy zárja ki a modell osztály egyes tulajdonságai dokumentum mezői tárolt.
* Képezze le a kódban nevei és az indexben mezőnevek között.
* Hozzon létre egyéni attribútumok tulajdonságok hozzárendelése dokumentum mező használható.

Példák egyedi szerializálás egység tesztek végrehajtása az Azure Search .NET SDK a Githubon található. A jó kiindulási pont [Ez a mappa](https://github.com/Azure/azure-sdk-for-net/tree/AutoRest/src/Search/Search.Tests/Tests/Models). Az egyéni szerializálási tesztek által használt osztályok tartalmaz.

### <a name="searching-for-documents-in-the-index"></a>Az indexelt dokumentumok keresése
A mintaalkalmazás utolsó lépése, hogy az index egyes dokumentumok keresése. A következő metódus végzi ezt:

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

Minden alkalommal, amikor végrehajtja a lekérdezést, ez a módszer először létrehoz egy új `SearchParameters` objektum. Ez például rendezési, szűrési, lapozás és értékkorlátozás a lekérdezés további beállítások megadására szolgál. Ezzel a módszerrel beállítás van a `Filter`, `Select`, `OrderBy`, és `Top` különböző lekérdezések tulajdonság. Minden a `SearchParameters` tulajdonságok szerepelnek [Itt](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.searchparameters).

A következő lépésre ténylegesen a keresési lekérdezés végrehajtásához. Ebben az esetben használja a `Documents.Search` metódust. Minden egyes lekérdezés esetén, azt át kívánja használni, mint a karakterlánc a keresési szöveget (vagy `"*"` nincs a keresési szöveget esetén), és a keresési paramétereket, korábban létrehozott. Azt is megadhatja, `Hotel` a típust paraméterként `Documents.Search`, amely közli a dokumentumok a keresési eredmények deszerializálása típusú objektumot az SDK `Hotel`.

> [!NOTE]
> További információ a keresési lekérdezés kifejezésszintaxist található [Itt](https://docs.microsoft.com/rest/api/searchservice/Simple-query-syntax-in-Azure-Search).
> 
> 

Végül minden egyes lekérdezés után ez a módszer telepítéseket összes a találat a keresési eredmények között, a konzol minden egyes dokumentum nyomtatása:

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

Vegyük pedig egyes lekérdezések részletes bemutatása. Az első lekérdezés végrehajtása a kód itt látható:

```csharp
parameters =
    new SearchParameters()
    {
        Select = new[] { "hotelName" }
    };

results = indexClient.Documents.Search<Hotel>("budget", parameters);

WriteDocuments(results);
```

Ebben az esetben azt keresünk, amelyek megfelelnek a word "költségvetés" hotels, és azt szeretné, hogy vissza csak a szállodák neveket, leírtak szerint a `Select` paraméter. Az alábbi történik:

    Name: Roach Motel

A következő azt szeretnénk, a szállodák egy éjszakánként sebessége kisebb, mint 150 $ található, és csak a szállodák Azonosítóját és a leírását adja vissza:

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

Ez a lekérdezés használ egy OData `$filter` kifejezését `baseRate lt 150`, a dokumentumok az indexben szűrése. További információk az OData-szintaxis, amely támogatja az Azure Search található [Itt](https://docs.microsoft.com/rest/api/searchservice/OData-Expression-Syntax-for-Azure-Search).

Az alábbiakban a lekérdezés eredményét:

    ID: 2   Description: Cheapest hotel in town
    ID: 3   Description: Close to town hall and the river

A következő szeretnénk keresse meg a felső két szállodák, amely rendelkezik lett utoljára felújított, és a szállodák nevét és az utolsó felújítás dátumának megjelenítése. A kód itt látható: 

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

Ebben az esetben újra használjuk OData szintaxis adja meg a `OrderBy` paraméterként `lastRenovationDate desc`. Azt is beállíthatja `Top` 2. Győződjön meg arról, azt csak a felső két dokumentumok beolvasása. Előtt, hogy állítsa `Select` adhatja meg, melyik mezőkre vissza kell adni.

Az alábbi történik:

    Name: Fancy Stay        Last renovated on: 6/27/2010 12:00:00 AM +00:00
    Name: Roach Motel       Last renovated on: 4/28/1982 12:00:00 AM +00:00

Végül azt szeretnénk található összes szállodák, amelyek megfelelnek a "motel" szót:

```csharp
parameters = new SearchParameters();
results = indexClient.Documents.Search<Hotel>("motel", parameters);

WriteDocuments(results);
```

Az alábbiakban az eredményeket, amely az összes mezők szerepelhetnek, mivel jelenleg nem adta meg, és a `Select` tulajdonság:

    ID: 2   Base rate: 79.99        Description: Cheapest hotel in town     Description (French): Hôtel le moins cher en ville      Name: Roach Motel       Category: Budget        Tags: [motel, budget]   Parking included: yes   Smoking allowed: yes    Last renovated on: 4/28/1982 12:00:00 AM +00:00 Rating: 1/5     Location: Latitude 49.678581, longitude -122.131577

Ez a lépés az oktatóanyag befejezése, de itt nem állnak le. **További lépések** Azure Search többet további forrásokat biztosít.

## <a name="next-steps"></a>Következő lépések
* Nézze át a [.NET SDK](https://docs.microsoft.com/dotnet/api/microsoft.azure.search) és a [REST API](https://docs.microsoft.com/rest/api/searchservice/) referenciáit.
* A Tudásbázis keresztül elmélyítsék [videókat és más mintákat és oktatóanyagok](search-video-demo-tutorial-list.md).
* Felülvizsgálati [elnevezési konvenciói](https://docs.microsoft.com/rest/api/searchservice/Naming-rules) további a különféle objektumok elnevezési szabályai.
* Felülvizsgálati [a támogatott adattípusokat](https://docs.microsoft.com/rest/api/searchservice/Supported-data-types) az Azure Search.
