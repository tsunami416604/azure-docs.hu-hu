---
title: Az Azure Search .NET SDK 1.1-es – Azure Search verziójának frissítése
description: Az Azure Search .NET SDK 1.1-es verziója régebbi API-verziók a kód át. Megtudhatja, Miben változott, és milyen kódot módosításokra szükség.
author: brjohnstmsft
manager: jlembicz
services: search
ms.service: search
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 01/15/2018
ms.author: brjohnst
ms.custom: seodec2018
ms.openlocfilehash: 82823bae76521080634d4f7ff285d94ce8495fbf
ms.sourcegitcommit: eb9dd01614b8e95ebc06139c72fa563b25dc6d13
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/12/2018
ms.locfileid: "53317287"
---
# <a name="upgrading-to-the-azure-search-net-sdk-version-11"></a>Az Azure Search .NET SDK 1.1-es verziójának frissítése

Verzió 1.0.2-preview használata vagy a régebbi a [Azure Search .NET SDK](https://aka.ms/search-sdk), ez a cikk segít az alkalmazás használhatja az 1.1-es verziójának frissítése.

Az SDK-t, beleértve a példákat általános bemutatóért lásd: [használata az Azure Search .NET-alkalmazásból](search-howto-dotnet-sdk.md).

> [!NOTE]
> Miután az 1.1-es verzió, vagy ha már használja egy közötti 1.1-es és 2.0 – előzetes verzió, frissítenie kell a 3. verzió. Lásd: [az Azure Search .NET SDK 3-as verziójú frissítését](search-dotnet-sdk-migration.md) útmutatást.
>

Először frissítse a NuGet referenciája `Microsoft.Azure.Search` vagy a NuGet Package Manager konzol segítségével vagy a jobb gombbal a projekt hivatkozásait a és a Visual Studióban válassza a "Kezelése NuGet Packages …".

Miután NuGet töltött le az új csomagok és azok függőségeit, a projekt újraépítéséhez.

Ha korábban a verzió 1.0.0-preview, 1.0.1-preview vagy 1.0.2-preview, a build sikeres legyen, és készen áll a go!

Ha korábban a verzió 0.13.0-preview vagy régebbi kiadást használja, megtekintheti az felépítési hibák az alábbi módon:

    Program.cs(137,56,137,62): error CS0117: 'Microsoft.Azure.Search.Models.IndexBatch' does not contain a definition for 'Create'
    Program.cs(137,99,137,105): error CS0117: 'Microsoft.Azure.Search.Models.IndexAction' does not contain a definition for 'Create'
    Program.cs(146,41,146,54): error CS1061: 'Microsoft.Azure.Search.IndexBatchException' does not contain a definition for 'IndexResponse' and no extension method 'IndexResponse' accepting a first argument of type 'Microsoft.Azure.Search.IndexBatchException' could be found (are you missing a using directive or an assembly reference?)
    Program.cs(163,13,163,42): error CS0246: The type or namespace name 'DocumentSearchResponse' could not be found (are you missing a using directive or an assembly reference?)

A következő lépés, hogy az Összeállítási hibák egyenként javítása. A legtöbb lesz szükség, módosítás, néhány osztályt és metódust nevek, amelyek az SDK-ban át lett nevezve. [Listáját a parancsban történt használhatatlanná tévő 1.1-es verzió](#ListOfChangesV1) neve módosítások listáját tartalmazza.

Ha egyéni osztály használatával modellezheti a dokumentumok, és ezeket az osztályokat lehet egyszerű típusú nem nullázható tulajdonság (például `int` vagy `bool` a C#), a hibajavítás integrálását, amelyek ismernie kell az SDK 1.1-es verziójában van. Lásd: [hibajavítások 1.1-es verzió](#BugFixesV1) további részletekért.

Végül felépítési hibák már rögzített, ha módosításokat végezheti el az új funkciók előnyeinek kihasználása, ha szeretné az alkalmazáshoz.

<a name="ListOfChangesV1"></a>

## <a name="list-of-breaking-changes-in-version-11"></a>1.1-es verzió használhatatlanná tévő változásai listája
Az alábbi lista rendezésére a valószínűsége, hogy a változás érinteni fogja az alkalmazás kódjában.

### <a name="indexbatch-and-indexaction-changes"></a>IndexBatch és IndexAction módosítások
`IndexBatch.Create` kapott `IndexBatch.New` , és már nem rendelkezik egy `params` argumentum. Használhat `IndexBatch.New` kötegek, amelyek vegyesen különböző típusú műveletek (összevonása, törlés, stb.). Emellett új statikus módszer van a kötegek létrehozása ahol összes műveletet megegyeznek: `Delete`, `Merge`, `MergeOrUpload`, és `Upload`.

`IndexAction` már nem rendelkezik a nyilvános konstruktorok és a tulajdonságai már nem módosítható. Különböző felhasználási célokra műveletek létrehozásához használjon új statikus metódusainak: `Delete`, `Merge`, `MergeOrUpload`, és `Upload`. `IndexAction.Create` el lett távolítva. Ha csak egy dokumentumot túlterhelést használt, ügyeljen arra, hogy használjon `Upload` helyette.

#### <a name="example"></a>Példa
Ha a kód a következőhöz hasonló:

    var batch = IndexBatch.Create(documents.Select(doc => IndexAction.Create(doc)));
    indexClient.Documents.Index(batch);

Módosíthatja a build hibák elhárításához:

    var batch = IndexBatch.New(documents.Select(doc => IndexAction.Upload(doc)));
    indexClient.Documents.Index(batch);

Ha azt szeretné, tovább egyszerűsítheti, ehhez:

    var batch = IndexBatch.Upload(documents);
    indexClient.Documents.Index(batch);

### <a name="indexbatchexception-changes"></a>IndexBatchException módosítások
A `IndexBatchException.IndexResponse` tevékenységének neve megváltozott hogy `IndexingResults`, és a típus már `IList<IndexingResult>`.

#### <a name="example"></a>Példa
Ha a kód a következőhöz hasonló:

    catch (IndexBatchException e)
    {
        Console.WriteLine(
            "Failed to index some of the documents: {0}",
            String.Join(", ", e.IndexResponse.Results.Where(r => !r.Succeeded).Select(r => r.Key)));
    }

Módosíthatja a build hibák elhárításához:

    catch (IndexBatchException e)
    {
        Console.WriteLine(
            "Failed to index some of the documents: {0}",
            String.Join(", ", e.IndexingResults.Where(r => !r.Succeeded).Select(r => r.Key)));
    }

<a name="OperationMethodChanges"></a>

### <a name="operation-method-changes"></a>A művelet metódus módosítások
Egyes műveletek az Azure Search .NET SDK szinkron és aszinkron hívók metódus túlterheléssel készleteként van közzétéve. Az aláírások és az ezen metódus túlterheléssel faktoring 1.1-es verzióban megváltozott.

Ha például a "Indexstatisztikáit beolvasása" műveletet az SDK korábbi verzióinak elérhetővé tett ezen aláírások:

Az `IIndexOperations` szkriptben:

    // Asynchronous operation with all parameters
    Task<IndexGetStatisticsResponse> GetStatisticsAsync(
        string indexName,
        CancellationToken cancellationToken);

Az `IndexOperationsExtensions` szkriptben:

    // Asynchronous operation with only required parameters
    public static Task<IndexGetStatisticsResponse> GetStatisticsAsync(
        this IIndexOperations operations,
        string indexName);

    // Synchronous operation with only required parameters
    public static IndexGetStatisticsResponse GetStatistics(
        this IIndexOperations operations,
        string indexName);

1.1-es verzió ugyanehhez a művelethez a metódusainak aláírásai néznek ki:

Az `IIndexesOperations` szkriptben:

    // Asynchronous operation with lower-level HTTP features exposed
    Task<AzureOperationResponse<IndexGetStatisticsResult>> GetStatisticsWithHttpMessagesAsync(
        string indexName,
        SearchRequestOptions searchRequestOptions = default(SearchRequestOptions),
        Dictionary<string, List<string>> customHeaders = null,
        CancellationToken cancellationToken = default(CancellationToken));

Az `IndexesOperationsExtensions` szkriptben:

    // Simplified asynchronous operation
    public static Task<IndexGetStatisticsResult> GetStatisticsAsync(
        this IIndexesOperations operations,
        string indexName,
        SearchRequestOptions searchRequestOptions = default(SearchRequestOptions),
        CancellationToken cancellationToken = default(CancellationToken));

    // Simplified synchronous operation
    public static IndexGetStatisticsResult GetStatistics(
        this IIndexesOperations operations,
        string indexName,
        SearchRequestOptions searchRequestOptions = default(SearchRequestOptions));

1.1-es verzióval kezdődően az Azure Search .NET SDK rendszerezi műveleti metódusoknál eltérően:

* Választható paraméterek: mostantól modellezése eltér az alapértelmezett inkább a paraméterek, mint a további túlterheléssel. Ez néha jelentősen csökkenti a metódus túlterheléssel, száma.
* A bővítő metódusokat mostantól elrejtheti a hívó HTTP felesleges adatait rengeteg. Például az SDK korábbi verzióinak adott vissza a választ egy HTTP-állapotkódot, amelyek gyakran nem kell ellenőrizni, mert a műveleti metódusoknál throw objektum `CloudException` számára bármilyen hibát jelző állapotkód. A bővítmény új módszereket csak adatmodell-objektumokat, így nem szükséges kicsomagolása őket a kódban kelljen adja vissza.
* Ezzel szemben az alapvető felületeihez most adnak a HTTP-szintjén átfogóbb vezérlésre van szüksége, ha fiókszinten elérhető módszerek. Most már beadható foglalandó kérelmeket, illetve az új egyéni HTTP-fejlécek `AzureOperationResponse<T>` visszatérési típussal a közvetlen hozzáférést biztosít a `HttpRequestMessage` és `HttpResponseMessage` a művelethez. `AzureOperationResponse` van definiálva a `Microsoft.Rest.Azure` névteret, és lecseréli azokat `Hyak.Common.OperationResponse`.

### <a name="scoringparameters-changes"></a>ScoringParameters módosítások
Egy új osztályt `ScoringParameter` hozzá lett adva a legújabb SDK, hogy egyszerűbb legyen meg a paramétereket a pontozási profilok keresési lekérdezést. Korábban a `ScoringProfiles` tulajdonságát a `SearchParameters` osztály volt megadva, `IList<string>`; Most azt helyazonosítót `IList<ScoringParameter>`.

#### <a name="example"></a>Példa
Ha a kód a következőhöz hasonló:

    var sp = new SearchParameters();
    sp.ScoringProfile = "jobsScoringFeatured";      // Use a scoring profile
    sp.ScoringParameters = new[] { "featuredParam-featured", "mapCenterParam-" + lon + "," + lat };

Módosíthatja a build hibák elhárításához: 

    var sp = new SearchParameters();
    sp.ScoringProfile = "jobsScoringFeatured";      // Use a scoring profile
    sp.ScoringParameters =
        new[]
        {
            new ScoringParameter("featuredParam", new[] { "featured" }),
            new ScoringParameter("mapCenterParam", GeographyPoint.Create(lat, lon))
        };

### <a name="model-class-changes"></a>Osztály módosításokat modellje
A leírt aláírás-módosítások miatt [művelet metódus módosítások](#OperationMethodChanges), a sok osztály a `Microsoft.Azure.Search.Models` névtér átnevezték vagy eltávolítva. Példa:

* `IndexDefinitionResponse` felváltotta `AzureOperationResponse<Index>`
* A `DocumentSearchResponse` új nevet kapott: `DocumentSearchResult`
* A `IndexResult` új nevet kapott: `IndexingResult`
* `Documents.Count()` most adja vissza egy `long` a dokumentumok száma helyett a egy `DocumentCountResponse`
* A `IndexGetStatisticsResponse` új nevet kapott: `IndexGetStatisticsResult`
* A `IndexListResponse` új nevet kapott: `IndexListResult`

Összefoglalva, `OperationResponse`-származtatott osztályokat, amelyek léteztek csak burkolása egy modell objektumot el lettek távolítva. A fennmaradó osztályok kellett volna módosulás a következőről utótag `Response` való `Result`.

#### <a name="example"></a>Példa
Ha a kód a következőhöz hasonló:

    IndexerGetStatusResponse statusResponse = null;

    try
    {
        statusResponse = _searchClient.Indexers.GetStatus(indexer.Name);
    }
    catch (Exception ex)
    {
        Console.WriteLine("Error polling for indexer status: {0}", ex.Message);
        return;
    }

    IndexerExecutionResult lastResult = statusResponse.ExecutionInfo.LastResult;

Módosíthatja a build hibák elhárításához:

    IndexerExecutionInfo status = null;

    try
    {
        status = _searchClient.Indexers.GetStatus(indexer.Name);
    }
    catch (Exception ex)
    {
        Console.WriteLine("Error polling for indexer status: {0}", ex.Message);
        return;
    }

    IndexerExecutionResult lastResult = status.LastResult;

#### <a name="response-classes-and-ienumerable"></a>Válasz osztályok és IEnumerable
Egy további változás, amely hatással lehet a kód, hogy választ osztályokat tartalmazó gyűjtemények már nem valósítható meg `IEnumerable<T>`. Ehelyett a gyűjtemény tulajdonság közvetlenül is elérheti. Ha például a kód néz ki:

    DocumentSearchResponse<Hotel> response = indexClient.Documents.Search<Hotel>(searchText, sp);
    foreach (SearchResult<Hotel> result in response)
    {
        Console.WriteLine(result.Document);
    }

Módosíthatja a build hibák elhárításához:

    DocumentSearchResult<Hotel> response = indexClient.Documents.Search<Hotel>(searchText, sp);
    foreach (SearchResult<Hotel> result in response.Results)
    {
        Console.WriteLine(result.Document);
    }

#### <a name="special-case-for-web-applications"></a>Különleges eset webes alkalmazásokhoz
Ha rendelkezik egy webalkalmazást, amely szerializálja `DocumentSearchResponse` közvetlenül a keresési eredményeket kapjon a böngésző, módosítania kell a kódot kell, vagy az eredmények nebude serializovat megfelelően. Ha például a kód néz ki:

    public ActionResult Search(string q = "")
    {
        // If blank search, assume they want to search everything
        if (string.IsNullOrWhiteSpace(q))
            q = "*";

        return new JsonResult
        {
            JsonRequestBehavior = JsonRequestBehavior.AllowGet,
            Data = _featuresSearch.Search(q)
        };
    }

Úgy módosíthatja a `.Results` javítsa ki a keresési eredmények megjelenítési keresési válasz tulajdonságát:

    public ActionResult Search(string q = "")
    {
        // If blank search, assume they want to search everything
        if (string.IsNullOrWhiteSpace(q))
            q = "*";

        return new JsonResult
        {
            JsonRequestBehavior = JsonRequestBehavior.AllowGet,
            Data = _featuresSearch.Search(q).Results
        };
    }

Kell keresni ezekben az esetekben a kódban. **a fordító nem figyelmezteti,** mert `JsonResult.Data` típusú `object`.

### <a name="cloudexception-changes"></a>CloudException módosítások
A `CloudException` osztály át a `Hyak.Common` használni szeretné a `Microsoft.Rest.Azure` névtér. Emellett a `Error` tevékenységének neve megváltozott hogy `Body`.

### <a name="searchserviceclient-and-searchindexclient-changes"></a>SearchServiceClient és SearchIndexClient módosítások
Milyen típusú a `Credentials` tulajdonság megváltozott `SearchCredentials` annak alaposztályát, hogy `ServiceClientCredentials`. Ha hozzá kell férnie a `SearchCredentials` , egy `SearchIndexClient` vagy `SearchServiceClient`, használja az új `SearchCredentials` tulajdonság.

Az SDK korábbi verzióinak `SearchServiceClient` és `SearchIndexClient` konstruktorok igénybe vett volna egy `HttpClient` paraméter. Ezek a konstruktorok vette egy `HttpClientHandler` és a egy tömbjét `DelegatingHandler` objektumokat. Ez megkönnyíti az egyéni kezelők előzetesen feldolgozni a HTTP-kérések szükség esetén telepítse.

Végül az igénybe vett konstruktorok egy `Uri` és `SearchCredentials` megváltoztak. Ha például a következőhöz hasonló kódot rendelkezik:

    var client =
        new SearchServiceClient(
            new SearchCredentials("abc123"),
            new Uri("http://myservice.search.windows.net"));

Módosíthatja a build hibák elhárításához:

    var client =
        new SearchServiceClient(
            new Uri("http://myservice.search.windows.net"),
            new SearchCredentials("abc123"));

Azt is vegye figyelembe, hogy a hitelesítő adatok paraméter típusa megváltozott hogy `ServiceClientCredentials`. Ez nem valószínű, hogy a kódot, mert hatással `SearchCredentials` van származtatva `ServiceClientCredentials`.

### <a name="passing-a-request-id"></a>A kérelem azonosító továbbításához
Az SDK korábbi verzióinak, a sikerült beállítani a Kérelemazonosító a `SearchServiceClient` vagy `SearchIndexClient` és kellene szerepelnie a REST API-t minden kérés. Ez akkor hasznos, ha van szüksége, forduljon az ügyfélszolgálathoz kapcsolatos hibák elhárításának a keresési szolgáltatás. Azonban hasznos több minden művelet egy egyedi ügyfélkérési Azonosítójának beállítása helyett minden művelet használandó ugyanazzal az Azonosítóval. Ebből kifolyólag a `SetClientRequestId` módszerek `SearchServiceClient` és `SearchIndexClient` el lettek távolítva. Ehelyett átadható egy kérelem azonosítója minden művelet metódus az opcionális keresztül `SearchRequestOptions` paraméter.

> [!NOTE]
> Az SDK egy későbbi kiadásban hozzáadjuk állítja a Kérelemazonosító globálisan az ügyfélen lévő objektumok egy új mechanizmust, amely megfelel a más Azure SDK-k által használt módszert.
> 
> 

### <a name="example"></a>Példa
Ha rendelkezik a következőhöz hasonló kódot:

    client.SetClientRequestId(Guid.NewGuid());
    ...
    long count = client.Documents.Count();

Módosíthatja a build hibák elhárításához:

    long count = client.Documents.Count(new SearchRequestOptions(requestId: Guid.NewGuid()));

### <a name="interface-name-changes"></a>Adapter nevének módosítása
A művelet csoport illesztőneveket összhangban vannak a megfelelő tulajdonságneveket változásai:

* Milyen típusú `ISearchServiceClient.Indexes` lett `IIndexOperations` való `IIndexesOperations`.
* Milyen típusú `ISearchServiceClient.Indexers` lett `IIndexerOperations` való `IIndexersOperations`.
* Milyen típusú `ISearchServiceClient.DataSources` lett `IDataSourceOperations` való `IDataSourcesOperations`.
* Milyen típusú `ISearchIndexClient.Documents` lett `IDocumentOperations` való `IDocumentsOperations`.

Ez a változás valószínű, hogy a kód befolyásolja, hacsak az ezeken a felületeken tesztelési célokra mocks létrehozott.

<a name="BugFixesV1"></a>

## <a name="bug-fixes-in-version-11"></a>Hibajavítások 1.1-es verzió
Hiba történt az Azure Search .NET SDK a egyéni modell osztályok szerializálási régebbi verzióit. A hiba akkor fordulhat elő, ha létrehozott egy egyéni modellosztály értéktípus nullázható tulajdonság.

### <a name="steps-to-reproduce"></a>Reprodukálás lépései
Hozzon létre egy egyéni modellosztály tulajdonság nem nullázható érték típusú. Hozzáadhat például egy nyilvános `UnitCount` vlastnost typu `int` helyett `int?`.

Ha az alapértelmezett érték az adott típusú dokumentum indexel (például 0- `int`), a mező az Azure Search null értékű lesz. Ha ezt követően a dokumentum, a `Search` hívás kivételt fogja kijelezni `JsonSerializationException` panaszkodik, amely nem konvertálható `null` való `int`.

Emellett szűrők nem működnek, mivel az index a kívánt érték helyett írása null értékű volt.

### <a name="fix-details"></a>Javítsa ki a részletei
Hogy megoldottuk a probléma az SDK 1.1-es verziójában. Most, ha rendelkezik egy modellosztályt ehhez hasonló:

    public class Model
    {
        public string Key { get; set; }

        public int IntValue { get; set; }
    }

és be `IntValue` 0, ezt az értéket most már megfelelően szerializálni a 0 értéket a keresztülhaladnak a hálózaton és tárolja a 0 értéket az indexben. Round esetén a várt módon is működik.

Vegye figyelembe ezt a módszert használja egy potenciális probléma áll: Ha egy nem nullázható tulajdonság használja egy modell típusa, hogy **garantálni** , hogy az index egyetlen dokumentuma sem tartalmaz az adott mezőben null értékű. Az SDK-t és az Azure Search REST API nem segít, hogy ennek kényszerítéséhez.

Ez nem csupán elméleti szempont: Képzelje el egy forgatókönyvet, ahol hozzáadhat egy új mezőt típusú, meglévő indexhez `Edm.Int32`. Az indexdefiníció frissítését követően ehhez a mezőhöz minden dokumentumban null érték tartozik (mivel az Azure Search szolgáltatásban az összes értéktípus nullázható). Ha ezt követően egy modellosztályt úgy alkalmaz, hogy ehhez a mezőhöz nem nullázható `int` tulajdonságot ad meg, a dokumentumok lekérdezésének megkísérlésekor egy ehhez hasonló `JsonSerializationException` választ kap:

    Error converting value {null} to type 'System.Int32'. Path 'IntValue'.

Ebből kifolyólag továbbra is javasoljuk, hogy használjon nullázható típust a modell tanórák ajánlott eljárás.

Ezt a hibát, és a javítás további információkért tekintse meg [ezt a problémát a Githubban](https://github.com/Azure/azure-sdk-for-net/issues/1063).

