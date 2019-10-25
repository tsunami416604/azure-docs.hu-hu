---
title: Frissítés a Azure Search .NET SDK 1,1-es verziójára
titleSuffix: Azure Cognitive Search
description: Telepítse át a kódot a Azure Search .NET SDK 1,1-es verziójára a régebbi API-verziókból. Ismerje meg az újdonságokat és a szükséges kód változásait.
manager: nitinme
author: brjohnstmsft
ms.author: brjohnst
ms.service: cognitive-search
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 159aaa8424c3d7a711b587464b80696929f02186
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/23/2019
ms.locfileid: "72792384"
---
# <a name="upgrade-to-azure-search-net-sdk-version-11"></a>Frissítés a Azure Search .NET SDK 1,1-es verziójára

Ha a [Azure Search .net SDK](https://aka.ms/search-sdk)-hoz tartozó 1.0.2-Preview vagy régebbi verziót használja, ez a cikk segítséget nyújt az alkalmazás frissítéséhez az 1,1-es verzió használatára.

Az SDK-val kapcsolatos általános áttekintést a példákat lásd: [Azure Search használata .NET-alkalmazásokból](search-howto-dotnet-sdk.md).

> [!NOTE]
> A 1,1-es verzióra való frissítés után, vagy ha már használ egy 1,1 és 2,0 közötti verziót – előzetes verzió, akkor a 3-as verzióra kell frissítenie. Útmutatásért lásd: [a Azure Search .net SDK 3-as verziójára való frissítés](search-dotnet-sdk-migration.md) .
>

Először frissítse a `Microsoft.Azure.Search` NuGet-hivatkozását a NuGet csomagkezelő konzol használatával, vagy kattintson a jobb gombbal a projekt hivatkozásaira, és válassza a "NuGet-csomagok kezelése..." lehetőséget. a Visual Studióban.

Miután a NuGet letöltötte az új csomagokat és azok függőségeit, építse újra a projektet.

Ha korábban már használta a 1.0.0-Preview, a 1.0.1-Preview vagy a 1.0.2-Preview verziót, a buildnek sikeresnek kell lennie, és készen áll arra, hogy eljusson!

Ha korábban már használta a 0.13.0-Preview vagy régebbi verziót, az alábbihoz hasonló Build-hibákat kell látnia:

    Program.cs(137,56,137,62): error CS0117: 'Microsoft.Azure.Search.Models.IndexBatch' does not contain a definition for 'Create'
    Program.cs(137,99,137,105): error CS0117: 'Microsoft.Azure.Search.Models.IndexAction' does not contain a definition for 'Create'
    Program.cs(146,41,146,54): error CS1061: 'Microsoft.Azure.Search.IndexBatchException' does not contain a definition for 'IndexResponse' and no extension method 'IndexResponse' accepting a first argument of type 'Microsoft.Azure.Search.IndexBatchException' could be found (are you missing a using directive or an assembly reference?)
    Program.cs(163,13,163,42): error CS0246: The type or namespace name 'DocumentSearchResponse' could not be found (are you missing a using directive or an assembly reference?)

A következő lépés a Build-hibák egyenkénti kijavítása. A legtöbb esetben módosítania kell az SDK-ban átnevezni kívánt osztály-és metódusok nevét. Az [1,1-es verzióban feltört változások listája](#ListOfChangesV1) tartalmazza a nevek változásának listáját.

Ha egyéni osztályokat használ a dokumentumok modellezéséhez, és ezek az osztályok nem null értékű egyszerű típusok (például `int` vagy `bool` C#) tulajdonságaival rendelkeznek, az SDK 1,1-es verziójában van egy hibajavítás, amelyről tisztában kell lennie. További részletekért tekintse [meg a 1,1-es verzió hibajavításait](#BugFixesV1) .

Végül a felépítési hibák kijavítása után módosíthatja az alkalmazást, és igény szerint kihasználhatja az új funkciókat.

<a name="ListOfChangesV1"></a>

## <a name="list-of-breaking-changes-in-version-11"></a>Az 1,1-es verzióban feltört változások listája
A következő lista a valószínűsége, hogy a módosítás hatással lesz az alkalmazás kódjára.

### <a name="indexbatch-and-indexaction-changes"></a>IndexBatch-és IndexAction-változások
`IndexBatch.Create` átnevezve `IndexBatch.New`re, és már nem rendelkezik `params` argumentummal. A `IndexBatch.New` a különböző típusú műveleteket (egyesítés, törlés stb.) összekeverő kötegekhez használhatja. Emellett új statikus módszerek is léteznek a kötegek létrehozásához, ahol az összes művelet azonos: `Delete`, `Merge`, `MergeOrUpload`és `Upload`.

`IndexAction` már nem rendelkezik nyilvános konstruktorokkal, és annak tulajdonságai mostantól nem változtathatók meg. Az új statikus metódusok használatával különböző célokra hozhat létre műveleteket: `Delete`, `Merge`, `MergeOrUpload`és `Upload`. a `IndexAction.Create` el lett távolítva. Ha a túlterhelést használta, amely csak a dokumentumot veszi figyelembe, ne felejtse el használni a `Upload`.

#### <a name="example"></a>Példa
Ha a kód így néz ki:

    var batch = IndexBatch.Create(documents.Select(doc => IndexAction.Create(doc)));
    indexClient.Documents.Index(batch);

A fordítási hibák kijavításához módosíthatja a következőt:

    var batch = IndexBatch.New(documents.Select(doc => IndexAction.Upload(doc)));
    indexClient.Documents.Index(batch);

Ha szeretné, tovább egyszerűsítheti a következőket:

    var batch = IndexBatch.Upload(documents);
    indexClient.Documents.Index(batch);

### <a name="indexbatchexception-changes"></a>IndexBatchException változásai
A `IndexBatchException.IndexResponse` tulajdonság átnevezve lett `IndexingResults`re, és a típusa már `IList<IndexingResult>`.

#### <a name="example"></a>Példa
Ha a kód így néz ki:

    catch (IndexBatchException e)
    {
        Console.WriteLine(
            "Failed to index some of the documents: {0}",
            String.Join(", ", e.IndexResponse.Results.Where(r => !r.Succeeded).Select(r => r.Key)));
    }

A fordítási hibák kijavításához módosíthatja a következőt:

    catch (IndexBatchException e)
    {
        Console.WriteLine(
            "Failed to index some of the documents: {0}",
            String.Join(", ", e.IndexingResults.Where(r => !r.Succeeded).Select(r => r.Key)));
    }

<a name="OperationMethodChanges"></a>

### <a name="operation-method-changes"></a>Működési módszer módosításai
A Azure Search .NET SDK-ban minden művelet elérhetővé vált a szinkron és aszinkron hívók számára. A metódushoz tartozó aláírások és az ezekből való kiszámítások a 1,1-es verzióban módosultak.

Az SDK régebbi verzióiban az "index-statisztika beolvasása" művelet például az alábbi aláírásokat tették elérhetővé:

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

A metódus aláírásai az 1,1-es verzióban ugyanezen művelethez hasonlóan jelennek meg:

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

A 1,1-es verziótól kezdődően a Azure Search .NET SDK különböző műveleti módszereket szervez:

* A választható paraméterek mostantól alapértelmezett paraméterekként lettek modellezve, nem pedig a további metódusok túlterheltsége. Ez csökkenti a metódusok túlterhelésének számát, esetenként drámai módon.
* A bővítményi metódusok mostantól a hívótól származó HTTP-adatok nagy részét is elrejtik. Az SDK régebbi verziói például egy HTTP-állapotkód által visszaadott Response objektumot adtak vissza, amelyet gyakran nem kell ellenőriznie, mert a műveleti metódusok `CloudException` minden olyan állapotkódot eldobjanak, amely hibát jelez. Az új bővítményi metódusok csak a modell objektumokat adják vissza, így a hiba miatt nem kell kicsomagolni őket a kódban.
* Ezzel szemben a központi felületek mostantól olyan metódusokat tesznek elérhetővé, amelyekkel hatékonyabban vezérelheti a HTTP-szintet, ha szüksége van rá. Mostantól megadhatja a kérések részét képező egyéni HTTP-fejléceket, és az új `AzureOperationResponse<T>` visszatérési típusa közvetlen hozzáférést biztosít a művelet `HttpRequestMessage` és `HttpResponseMessage`ához. a `AzureOperationResponse` a `Microsoft.Rest.Azure` névtérben van definiálva, és a `Hyak.Common.OperationResponse`váltja fel.

### <a name="scoringparameters-changes"></a>ScoringParameters változásai
A legújabb SDK-ban egy új, `ScoringParameter` nevű osztály lett hozzáadva, amely megkönnyíti a paraméterek megadását a keresési lekérdezésekben lévő profilok kereséséhez. Korábban a `SearchParameters` osztály `ScoringProfiles` tulajdonsága `IList<string>`ként lett beírva. Most `IList<ScoringParameter>`ként van beírva.

#### <a name="example"></a>Példa
Ha a kód így néz ki:

    var sp = new SearchParameters();
    sp.ScoringProfile = "jobsScoringFeatured";      // Use a scoring profile
    sp.ScoringParameters = new[] { "featuredParam-featured", "mapCenterParam-" + lon + "," + lat };

A fordítási hibák kijavításához módosíthatja a következőt: 

    var sp = new SearchParameters();
    sp.ScoringProfile = "jobsScoringFeatured";      // Use a scoring profile
    sp.ScoringParameters =
        new[]
        {
            new ScoringParameter("featuredParam", new[] { "featured" }),
            new ScoringParameter("mapCenterParam", GeographyPoint.Create(lat, lon))
        };

### <a name="model-class-changes"></a>Modell osztály változásai
A [műveleti módszer változásaiban](#OperationMethodChanges)leírt aláírási változások miatt a `Microsoft.Azure.Search.Models` névtér számos osztálya át lett nevezve vagy el lett távolítva. Példa:

* `IndexDefinitionResponse` váltotta fel `AzureOperationResponse<Index>`
* A `DocumentSearchResponse` új nevet kapott: `DocumentSearchResult`
* A `IndexResult` új nevet kapott: `IndexingResult`
* a `Documents.Count()` mostantól egy `long` ad vissza, amely a dokumentumok számának `DocumentCountResponse` helyett szerepel.
* A `IndexGetStatisticsResponse` új nevet kapott: `IndexGetStatisticsResult`
* A `IndexListResponse` új nevet kapott: `IndexListResult`

Összefoglalva, `OperationResponse`származtatott osztályok, amelyek csak a Model objektum becsomagolásához lettek eltávolítva. A fennmaradó osztályok utótagja `Response`ról `Result`ra módosult.

#### <a name="example"></a>Példa
Ha a kód így néz ki:

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

A fordítási hibák kijavításához módosíthatja a következőt:

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
Egy további változás, amely hatással lehet a kódra, hogy a gyűjteményeket tároló válasz-osztályok már nem implementálják `IEnumerable<T>`. Ehelyett közvetlenül a Collection tulajdonságot érheti el. Ha például a kód így néz ki:

    DocumentSearchResponse<Hotel> response = indexClient.Documents.Search<Hotel>(searchText, sp);
    foreach (SearchResult<Hotel> result in response)
    {
        Console.WriteLine(result.Document);
    }

A fordítási hibák kijavításához módosíthatja a következőt:

    DocumentSearchResult<Hotel> response = indexClient.Documents.Search<Hotel>(searchText, sp);
    foreach (SearchResult<Hotel> result in response.Results)
    {
        Console.WriteLine(result.Document);
    }

#### <a name="special-case-for-web-applications"></a>Speciális eset webalkalmazásokhoz
Ha van olyan webalkalmazása, amely `DocumentSearchResponse` közvetlenül szerializál, hogy a keresési eredményeket a böngészőbe küldje, módosítania kell a kódot, vagy az eredmények nem lesznek megfelelően szerializálva. Ha például a kód így néz ki:

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

A keresési válasz `.Results` tulajdonságának lekérésével módosíthatja a keresési eredmények kijavítását:

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

A kódban meg kell keresnie az ilyen eseteket; **A fordító nem figyelmeztet,** mert `JsonResult.Data` `object`típusú.

### <a name="cloudexception-changes"></a>CloudException változásai
A `CloudException` osztály át lett helyezve a `Hyak.Common` névtérből a `Microsoft.Rest.Azure` névtérbe. Továbbá a `Error` tulajdonsága átnevezve lett `Body`re.

### <a name="searchserviceclient-and-searchindexclient-changes"></a>SearchServiceClient-és SearchIndexClient-változások
A `Credentials` tulajdonság típusa `SearchCredentials`ról az alaposztályra módosult, `ServiceClientCredentials`. Ha egy `SearchIndexClient` vagy `SearchServiceClient``SearchCredentials`hoz kell hozzáférnie, használja az új `SearchCredentials` tulajdonságot.

Az SDK régebbi verzióiban `SearchServiceClient` és `SearchIndexClient` olyan konstruktorokkal rendelkezett, amelyek `HttpClient` paramétert vettek igénybe. Ezeket olyan konstruktorok váltotta fel, amelyek `HttpClientHandler` és `DelegatingHandler` objektumok tömbjét is elvégezték. Ez megkönnyíti az egyéni kezelők telepítését a HTTP-kérések előzetes feldolgozásához, ha szükséges.

Végül a `Uri` és `SearchCredentials` megváltoztatott konstruktorok módosultak. Ha például a következőhöz hasonló kód szerepel:

    var client =
        new SearchServiceClient(
            new SearchCredentials("abc123"),
            new Uri("http://myservice.search.windows.net"));

A fordítási hibák kijavításához módosíthatja a következőt:

    var client =
        new SearchServiceClient(
            new Uri("http://myservice.search.windows.net"),
            new SearchCredentials("abc123"));

Azt is vegye figyelembe, hogy a hitelesítő adatok paraméter típusa `ServiceClientCredentials`ra módosult. Ez nem valószínű, hogy hatással van a kódra, mivel a `SearchCredentials` `ServiceClientCredentials`ból származik.

### <a name="passing-a-request-id"></a>Kérelem AZONOSÍTÓjának átadása
Az SDK régebbi verzióiban megadhatja a kérelem AZONOSÍTÓját a `SearchServiceClient` vagy a `SearchIndexClient`, és minden kérelembe belekerül a REST API. Ez akkor hasznos, ha segítségre van szüksége a keresési szolgáltatással kapcsolatos problémák megoldásához. Azonban hasznos lehet egyedi kérelmeket beállítani minden művelethez ahelyett, hogy ugyanazt az azonosítót szeretné használni az összes művelethez. Emiatt a `SearchServiceClient` és a `SearchIndexClient` `SetClientRequestId` metódusa el lett távolítva. Ehelyett a kérelem AZONOSÍTÓját átadhatja minden egyes művelethez a választható `SearchRequestOptions` paraméter használatával.

> [!NOTE]
> Az SDK egy későbbi kiadásában egy új mechanizmust adunk hozzá a kérelem AZONOSÍTÓjának globális beállításához, amely összhangban van a más Azure SDK-k által használt megközelítéssel.
> 
> 

### <a name="example"></a>Példa
Ha a következőhöz hasonló kód szerepel:

    client.SetClientRequestId(Guid.NewGuid());
    ...
    long count = client.Documents.Count();

A fordítási hibák kijavításához módosíthatja a következőt:

    long count = client.Documents.Count(new SearchRequestOptions(requestId: Guid.NewGuid()));

### <a name="interface-name-changes"></a>Illesztőfelület nevének módosítása
A műveleti csoport illesztőfelületének nevei mindegyike konzisztensnek felel meg a hozzájuk tartozó tulajdonságnév-nevekkel:

* A `ISearchServiceClient.Indexes` típusa `IIndexOperations`ról `IIndexesOperations`ra lett átnevezve.
* A `ISearchServiceClient.Indexers` típusa `IIndexerOperations`ról `IIndexersOperations`ra lett átnevezve.
* A `ISearchServiceClient.DataSources` típusa `IDataSourceOperations`ról `IDataSourcesOperations`ra lett átnevezve.
* A `ISearchIndexClient.Documents` típusa `IDocumentOperations`ról `IDocumentsOperations`ra lett átnevezve.

Ez a módosítás nem valószínű, hogy csak akkor befolyásolja a kódokat, ha tesztelési célból hozta létre a felületek mintáit.

<a name="BugFixesV1"></a>

## <a name="bug-fixes-in-version-11"></a>Hibajavítások az 1,1-es verzióban
Hiba történt a Azure Search .NET SDK régebbi verzióiban az egyéni modell osztályainak szerializálásával kapcsolatban. A hiba akkor fordulhat elő, ha egy egyéni modell osztályt hozott létre egy nem null értékű értéktípus tulajdonságával.

### <a name="steps-to-reproduce"></a>A reprodukálni kívánt lépések
Hozzon létre egy egyéni modell osztályt nem null értékű értéktípus tulajdonságával. Például adjon hozzá egy `int` típusú nyilvános `UnitCount` tulajdonságot `int?`helyett.

Ha egy olyan dokumentumot indexel, amely az alapértelmezett értéket adja meg (például: 0, `int`), akkor a mező értéke Azure Search. Ha ezt követően megkeresi a dokumentumot, a `Search` hívása `JsonSerializationException` arra panaszkodik, hogy nem tudja konvertálni a `null`t `int`re.

Emellett előfordulhat, hogy a szűrők nem a várt módon működnek, mivel a null értéket az indexbe írta a kívánt érték helyett.

### <a name="fix-details"></a>Javítás részletei
Ezt a problémát az SDK 1,1-es verziójában javítottuk. Most, ha a következőhöz hasonló modellt tartalmaz:

    public class Model
    {
        public string Key { get; set; }

        public int IntValue { get; set; }
    }

Ha pedig a `IntValue` 0-ra állítja be, akkor az érték helyesen szerializálható 0-ként a vezetéken, és az indexben 0-ként van tárolva. A ciklikus megbotlás is a várt módon működik.

Ennek a módszernek az egyik lehetséges problémája a következő: Ha nem null értékű tulajdonságú modellt használ, **garantálnia** kell, hogy az indexben egyetlen dokumentum sem tartalmaz null értéket a megfelelő mezőhöz. Sem az SDK, sem a Azure Search REST API nem fogja tudni kikényszeríteni ezt.

Ennek nem csupán elméleti jelentősége van: képzeljünk el például egy olyan alkalmazási helyzetet, ahol egy `Edm.Int32` típusú, meglévő indexhez új mezőt kell hozzáadnunk. Az indexdefiníció frissítését követően ehhez a mezőhöz minden dokumentumban null érték tartozik (mivel az Azure Search szolgáltatásban az összes értéktípus nullázható). Ha ezt követően egy modellosztályt úgy alkalmaz, hogy ehhez a mezőhöz nem nullázható `int` tulajdonságot ad meg, a dokumentumok lekérdezésének megkísérlésekor egy ehhez hasonló `JsonSerializationException` választ kap:

    Error converting value {null} to type 'System.Int32'. Path 'IntValue'.

Ezért azt javasoljuk, hogy az ajánlott eljárás szerint a modell osztályaiban is használjon null értékű típusokat.

A hibával és a javítással kapcsolatos további részletekért tekintse [meg ezt a problémát a githubon](https://github.com/Azure/azure-sdk-for-net/issues/1063).

