---
title: Frissítés az Azure Search .NET SDK 1.1-es verziójára
titleSuffix: Azure Cognitive Search
description: Kód áttelepítése az Azure Search .NET SDK 1.1-es verziója a régebbi API-verziók. További információ az újdonságokról és a kódmódosításról.
manager: nitinme
author: brjohnstmsft
ms.author: brjohnst
ms.service: cognitive-search
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 159aaa8424c3d7a711b587464b80696929f02186
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "72792384"
---
# <a name="upgrade-to-azure-search-net-sdk-version-11"></a>Frissítés az Azure Search .NET SDK 1.1-es verziójára

Ha az [Azure Search .NET SDK](https://aka.ms/search-sdk)1.0.2-es vagy régebbi verzióját használja, ez a cikk segít az alkalmazás frissítésében az 1.1-es verzió használatára.

Az SDK általánosabb forgatókönyvét példákkal együtt az [Azure Search használata .NET alkalmazásból](search-howto-dotnet-sdk.md)című témakörben talál.

> [!NOTE]
> Miután frissített az 1.1-es verzióra, vagy ha már használ egy 1.1-es és 2.0-előzetes verziót, akkor frissítsen a 3-as verzióra. További információt [az Azure Search .NET SDK 3-as verziójára való frissítés](search-dotnet-sdk-migration.md) című témakörben talál.
>

Először frissítse NuGet-referenciáját a NuGet Csomagkezelő konzol `Microsoft.Azure.Search` használatával, vagy kattintson a jobb gombbal a projektreferenciáira, és válassza a "NuGet csomagok kezelése..." parancsot. a Visual Studio alkalmazásban.

Miután a NuGet letöltötte az új csomagokat és azok függőségeit, építse újra a projektet.

Ha korábban az 1.0.0-preview, az 1.0.1-preview vagy az 1.0.2-preview verziót használta, a buildnek sikeresnek kell lennie, és készen áll!

Ha korábban a 0.13.0-as vagy régebbi verziót használta, a következőhez hasonló buildhibákat kell látnia:

    Program.cs(137,56,137,62): error CS0117: 'Microsoft.Azure.Search.Models.IndexBatch' does not contain a definition for 'Create'
    Program.cs(137,99,137,105): error CS0117: 'Microsoft.Azure.Search.Models.IndexAction' does not contain a definition for 'Create'
    Program.cs(146,41,146,54): error CS1061: 'Microsoft.Azure.Search.IndexBatchException' does not contain a definition for 'IndexResponse' and no extension method 'IndexResponse' accepting a first argument of type 'Microsoft.Azure.Search.IndexBatchException' could be found (are you missing a using directive or an assembly reference?)
    Program.cs(163,13,163,42): error CS0246: The type or namespace name 'DocumentSearchResponse' could not be found (are you missing a using directive or an assembly reference?)

A következő lépés a buildhibák egyenkénti javítása. A legtöbb lesz szükség változó néhány osztály és metódus nevét, hogy átnevezték az SDK. [Az 1.1-es verzióban történt törésmódosítások listája](#ListOfChangesV1) tartalmazza ezeknek a névváltozásoknak a listáját.

Ha egyéni osztályokat használ a dokumentumok modellezéséhez, és ezek az osztályok nem nullázható `int` `bool` primitív típusok (például vagy C#) tulajdonságokkal rendelkeznek, az SDK 1.1-es verziójában van egy hibajavítás, amelynek tudnia kell. További részletekért lásd [az 1.1-es verzió hibajavításait.](#BugFixesV1)

Végül, miután kijavította a buildelési hibákat, módosíthatja az alkalmazást, hogy kihasználja az új funkciókat, ha szeretné.

<a name="ListOfChangesV1"></a>

## <a name="list-of-breaking-changes-in-version-11"></a>Az 1.1-es verzióban végrehajtott változások listája
Az alábbi lista annak valószínűsége szerint van rendezve, hogy a módosítás hatással lesz-e az alkalmazáskódjára.

### <a name="indexbatch-and-indexaction-changes"></a>Az IndexBatch és az IndexAction módosításai
`IndexBatch.Create`átlett nevezve, `IndexBatch.New` és már `params` nincs argumentuma. Különböző típusú `IndexBatch.New` műveleteket (egyesítések, törlések stb.) keveredő kötegekhez használhat. Ezenkívül új statikus módszerek is léteznek a kötegek létrehozására, `MergeOrUpload`ahol `Upload`az összes művelet megegyezik: `Delete`, `Merge`, és .

`IndexAction`már nem rendelkeznek nyilvános konstruktorokkal, és tulajdonságai mostantól nem módosíthatók. Az új statikus módszereket különböző célokra történő `MergeOrUpload`műveletek `Upload`létrehozására kell használnia: `Delete`, `Merge`, és . `IndexAction.Create`Törölték. Ha a túlterhelést csak egy dokumentumot használ, győződjön meg róla, hogy inkább használja. `Upload`

#### <a name="example"></a>Példa
Ha a kód így néz ki:

    var batch = IndexBatch.Create(documents.Select(doc => IndexAction.Create(doc)));
    indexClient.Documents.Index(batch);

A buildelési hibák kijavításához módosíthatja:

    var batch = IndexBatch.New(documents.Select(doc => IndexAction.Upload(doc)));
    indexClient.Documents.Index(batch);

Ha szeretné, akkor tovább egyszerűsíteni, hogy ez:

    var batch = IndexBatch.Upload(documents);
    indexClient.Documents.Index(batch);

### <a name="indexbatchexception-changes"></a>IndexBatchException változások
A `IndexBatchException.IndexResponse` tulajdonság ot átnevezték a -ra, `IndexingResults`és a típusa most `IList<IndexingResult>`lett.

#### <a name="example"></a>Példa
Ha a kód így néz ki:

    catch (IndexBatchException e)
    {
        Console.WriteLine(
            "Failed to index some of the documents: {0}",
            String.Join(", ", e.IndexResponse.Results.Where(r => !r.Succeeded).Select(r => r.Key)));
    }

A buildelési hibák kijavításához módosíthatja:

    catch (IndexBatchException e)
    {
        Console.WriteLine(
            "Failed to index some of the documents: {0}",
            String.Join(", ", e.IndexingResults.Where(r => !r.Succeeded).Select(r => r.Key)));
    }

<a name="OperationMethodChanges"></a>

### <a name="operation-method-changes"></a>A műveleti módszer módosításai
Az Azure Search .NET SDK minden művelete a szinkron és aszinkron hívók metódustúlterheléseként érhető el. A módszer túlterhelésének aláírása és faktoringja megváltozott az 1.1-es verzióban.

Például a "Tárgymutató-statisztika bekerülése" művelet az SDK régebbi verzióiban a következő aláírásokat tette elérhetővé:

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

Az 1.1-es verzióban ugyanannak a műveletnek a metódusaláírásai a következőkre néznek ki:

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

Az Azure Search .NET SDK az 1.1-es verziótól kezdve eltérően rendezi a működési módszereket:

* A választható paraméterek mostantól alapértelmezett paraméterekként vannak modellezve, nem pedig további metódustúlterhelések. Ez csökkenti a módszer túlterhelések számát, néha drámaian.
* A bővítmény módszerek most elrejteni egy csomó idegen részleteket http a hívó. Például az SDK régebbi verziói egy HTTP-állapotkóddal rendelkező válaszobjektumot adnak vissza, amelyet gyakran nem kellett ellenőriznie, mert a működési módszerek minden olyan állapotkódot tartalmaznak, `CloudException` amely hibát jelez. Az új bővítménymódszerek csak modellobjektumokat adnak vissza, így nem kell kicsomagolnia őket a kódból.
* Ezzel szemben az alapvető felületek most olyan módszereket tárnak fel, amelyek nagyobb ellenőrzést biztosítanak a HTTP szintjén, ha szüksége van rá. Most már átadhatja az egyéni HTTP-fejléceket a `AzureOperationResponse<T>` kérelmekben való szerepeltetéshez, és az új visszatérési típus közvetlen hozzáférést biztosít a `HttpRequestMessage` művelethez és `HttpResponseMessage` a művelethez. `AzureOperationResponse`a `Microsoft.Rest.Azure` névtérben van definiálva, és lecseréli a programot. `Hyak.Common.OperationResponse`

### <a name="scoringparameters-changes"></a>ScoringParameters változások
A legújabb `ScoringParameter` SDK-ban egy új nevű osztály lett hozzáadva, hogy megkönnyítse a paraméterek megadását a keresési lekérdezésben lévő profilok pontozásához. Korábban `ScoringProfiles` az `SearchParameters` osztály tulajdonságát `IList<string>`a következőképpen írták be; Most ez gépelt mint `IList<ScoringParameter>`.

#### <a name="example"></a>Példa
Ha a kód így néz ki:

    var sp = new SearchParameters();
    sp.ScoringProfile = "jobsScoringFeatured";      // Use a scoring profile
    sp.ScoringParameters = new[] { "featuredParam-featured", "mapCenterParam-" + lon + "," + lat };

A buildelési hibák kijavításához módosíthatja: 

    var sp = new SearchParameters();
    sp.ScoringProfile = "jobsScoringFeatured";      // Use a scoring profile
    sp.ScoringParameters =
        new[]
        {
            new ScoringParameter("featuredParam", new[] { "featured" }),
            new ScoringParameter("mapCenterParam", GeographyPoint.Create(lat, lon))
        };

### <a name="model-class-changes"></a>Modellosztály-változások
A [Műveleti metódus változásaiban](#OperationMethodChanges)leírt aláírásváltozások miatt `Microsoft.Azure.Search.Models` a névtér számos osztályát átnevezték vagy eltávolították. Példa:

* `IndexDefinitionResponse`helyébe a`AzureOperationResponse<Index>`
* A `DocumentSearchResponse` új nevet kapott: `DocumentSearchResult`
* A `IndexResult` új nevet kapott: `IndexingResult`
* `Documents.Count()`most egy, `long` a dokumentumszámmal a d.`DocumentCountResponse`
* A `IndexGetStatisticsResponse` új nevet kapott: `IndexGetStatisticsResult`
* A `IndexListResponse` új nevet kapott: `IndexListResult`

Összefoglalva, `OperationResponse`a modellobjektum tördeléséhez csak létező -származtatott osztályokat eltávolították. A többi osztály utótagja `Response` `Result`a-ra változott.

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

A buildelési hibák kijavításához módosíthatja:

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

#### <a name="response-classes-and-ienumerable"></a>Válaszosztályok és IEnumerable
További változás, amely hatással lehet a kódra, hogy `IEnumerable<T>`a gyűjteményeket tartalmazó válaszosztályok már nem valósíthatók meg. Ehelyett közvetlenül elérheti a gyűjtemény tulajdonságát. Ha például a kód így néz ki:

    DocumentSearchResponse<Hotel> response = indexClient.Documents.Search<Hotel>(searchText, sp);
    foreach (SearchResult<Hotel> result in response)
    {
        Console.WriteLine(result.Document);
    }

A buildelési hibák kijavításához módosíthatja:

    DocumentSearchResult<Hotel> response = indexClient.Documents.Search<Hotel>(searchText, sp);
    foreach (SearchResult<Hotel> result in response.Results)
    {
        Console.WriteLine(result.Document);
    }

#### <a name="special-case-for-web-applications"></a>Speciális eset webes alkalmazásokhoz
Ha van egy webes alkalmazás, `DocumentSearchResponse` amely szerializálja közvetlenül küldeni keresési eredményeket a böngésző, meg kell változtatni a kódot, vagy az eredmények nem szerializálódik helyesen. Ha például a kód így néz ki:

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

Módosíthatja azt, ha `.Results` a keresési válasz tulajdonságát beszerzi a keresési eredmény megjelenítésének javításához:

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

Meg kell keresni az ilyen esetekben a kódot magad; **A fordító nem figyelmezteti Önt,** mert `JsonResult.Data` a típus `object`.

### <a name="cloudexception-changes"></a>CloudException változások
Az `CloudException` osztály átkerült `Hyak.Common` a névtérből a `Microsoft.Rest.Azure` névtérbe. A tulajdonságát `Error` átnevezték a `Body`névre is.

### <a name="searchserviceclient-and-searchindexclient-changes"></a>SearchServiceClient és SearchIndexClient változások
A `Credentials` tulajdonság típusa alaposztályról `SearchCredentials` változott. `ServiceClientCredentials` Ha `SearchCredentials` egy `SearchIndexClient` vagyoni `SearchServiceClient`vagyonára van `SearchCredentials` szüksége, kérjük, használja az új szálláshelyet.

Az SDK régebbi verzióiban, és `SearchServiceClient` `SearchIndexClient` olyan `HttpClient` konstruktorokkal rendelkeztek, amelyek egy paramétert vettek igénybe. Ezeket olyan konstruktorok váltották fel, amelyek egy `HttpClientHandler` és egy sor `DelegatingHandler` objektumot vesznek igénybe. Ez megkönnyíti az egyéni kezelők telepítését a HTTP-kérelmek előzetes feldolgozásához, ha szükséges.

Végül, a konstruktorok, hogy vett egy `Uri` és `SearchCredentials` megváltozott. Ha például a következővel készülő kód:

    var client =
        new SearchServiceClient(
            new SearchCredentials("abc123"),
            new Uri("http://myservice.search.windows.net"));

A buildelési hibák kijavításához módosíthatja:

    var client =
        new SearchServiceClient(
            new Uri("http://myservice.search.windows.net"),
            new SearchCredentials("abc123"));

Azt is vegye figyelembe, hogy a hitelesítő `ServiceClientCredentials`adatok paraméterének típusa a-ra változott. Ez valószínűleg nem befolyásolja `SearchCredentials` a `ServiceClientCredentials`kódot, mivel származik .

### <a name="passing-a-request-id"></a>Kérésazonosító átadása
Az SDK régebbi verzióiban beállíthatja a kérelemazonosítót a `SearchServiceClient` vagy, `SearchIndexClient` és a REST API minden kérésében szerepelni fog. Ez akkor hasznos, ha a keresési szolgáltatással kapcsolatos problémák elhárításához kell, ha kapcsolatba kell lépnie az ügyfélszolgálattal. Azonban hasznosabb, ha minden művelethez egyedi kérelemazonosítót állít be, és nem ugyanazt az azonosítót használja az összes művelethez. Emiatt a `SetClientRequestId` módszerek, `SearchServiceClient` `SearchIndexClient` és eltávolították. Ehelyett minden műveletmetódusnak átadhat egy kérelemazonosítót `SearchRequestOptions` a választható paraméteren keresztül.

> [!NOTE]
> Az SDK egy későbbi kiadásában egy új mechanizmust adunk hozzá egy kérelemazonosító globális beállításához az ügyfélobjektumokon, amely összhangban van más Azure SDK-k által használt megközelítéssel.
> 
> 

### <a name="example"></a>Példa
Ha a kód így néz ki:

    client.SetClientRequestId(Guid.NewGuid());
    ...
    long count = client.Documents.Count();

A buildelési hibák kijavításához módosíthatja:

    long count = client.Documents.Count(new SearchRequestOptions(requestId: Guid.NewGuid()));

### <a name="interface-name-changes"></a>A kapcsolat névváltozása
A műveletcsoport-illesztő nevek mindegyike megváltozott, hogy összhangban legyen a megfelelő tulajdonságnevekkel:

* A típus `ISearchServiceClient.Indexes` átlett nevezve `IIndexesOperations`a névre. `IIndexOperations`
* A típus `ISearchServiceClient.Indexers` átlett nevezve `IIndexersOperations`a névre. `IIndexerOperations`
* A típus `ISearchServiceClient.DataSources` átlett nevezve `IDataSourcesOperations`a névre. `IDataSourceOperations`
* A típus `ISearchIndexClient.Documents` átlett nevezve `IDocumentsOperations`a névre. `IDocumentOperations`

Ez a módosítás valószínűleg nem befolyásolja a kódot, hacsak nem hoztál létre tesztcélokra ilyen felületekről.

<a name="BugFixesV1"></a>

## <a name="bug-fixes-in-version-11"></a>Hibajavítások az 1.1-es verzióban
Az Azure Search .NET SDK régebbi verzióiban hiba történt az egyéni modellosztályok szerializálásával kapcsolatban. A hiba akkor fordulhat elő, ha nem nullázható értéktípusú tulajdonsággal létrehozott egy egyéni modellosztályt.

### <a name="steps-to-reproduce"></a>A reprodukálás lépései
Hozzon létre egy egyéni modellosztályt nem nullázható értéktípusú tulajdonsággal. Például adjon hozzá `UnitCount` egy `int` köztulajdont a `int?`helyett.

Ha indexel egy ilyen típusú (például 0) `int`alapértelmezett értéket tartalmazó dokumentumot, a mező null értékű lesz az Azure Search ben. Ha ezt követően megkeresi `Search` a dokumentumot, a hívás `JsonSerializationException` `null` arról panaszkodik, hogy nem konvertálható `int`.

Előfordulhat továbbá, hogy a szűrők nem a várt módon működnek, mivel null lett írva az indexbe a kívánt érték helyett.

### <a name="fix-details"></a>Részletek javítása
Ezt a problémát az SDK 1.1-es verziójában javítottuk. Most, ha van egy modell osztály, mint ez:

    public class Model
    {
        public string Key { get; set; }

        public int IntValue { get; set; }
    }

és akkor `IntValue` meg 0, hogy az érték most már helyesen szerializált 0 a vezetéket, és tárolja a 0 az indexben. A kerek botlás is a várt módon működik.

Van egy lehetséges probléma, hogy tudatában kell lennie ezzel a megközelítéssel: Ha egy modelltípus nem nullázható tulajdonság, **akkor garantálnia** kell, hogy nincs dokumentum az indexben tartalmaz null értéket a megfelelő mezőbe. Sem az SDK, sem az Azure Search REST API nem segít ennek érvényesítésében.

Ennek nem csupán elméleti jelentősége van: képzeljünk el például egy olyan alkalmazási helyzetet, ahol egy `Edm.Int32` típusú, meglévő indexhez új mezőt kell hozzáadnunk. Az indexdefiníció frissítését követően ehhez a mezőhöz minden dokumentumban null érték tartozik (mivel az Azure Search szolgáltatásban az összes értéktípus nullázható). Ha ezt követően egy modellosztályt úgy alkalmaz, hogy ehhez a mezőhöz nem nullázható `int` tulajdonságot ad meg, a dokumentumok lekérdezésének megkísérlésekor egy ehhez hasonló `JsonSerializationException` választ kap:

    Error converting value {null} to type 'System.Int32'. Path 'IntValue'.

Ezért továbbra is azt javasoljuk, hogy ajánlott eljárásként használjon nullázható típusokat a modellosztályokban.

A hibával és a javításokkal kapcsolatos további részletekért tekintse meg [ezt a problémát a GitHubon.](https://github.com/Azure/azure-sdk-for-net/issues/1063)

