---
title: Az Azure Search .NET SDK 1.1-es verziójának frissítése |} Microsoft Docs
description: Az Azure Search .NET SDK 1.1-es verziójának frissítése
author: brjohnstmsft
manager: jlembicz
services: search
ms.service: search
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 01/15/2018
ms.author: brjohnst
ms.openlocfilehash: ccefd21e2aa89a2b46129956b3c4417d548cbf32
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2018
ms.locfileid: "31796744"
---
# <a name="upgrading-to-the-azure-search-net-sdk-version-11"></a>Az Azure Search .NET SDK 1.1-es verziójának frissítése

Verzió 1.0.2-preview használata vagy a régebbi a [Azure Search .NET SDK](https://aka.ms/search-sdk), ez a cikk segít frissíteni az alkalmazást, 1.1-es verziójának használatára.

Az SDK, beleértve a példákat általános útmutatást lásd: [használata az Azure Search .NET-alkalmazás](search-howto-dotnet-sdk.md).

> [!NOTE]
> 1.1-es verzióra frissítése után, vagy ha már használja egy közötti 1.1-es és 2.0-előzetes verzió, frissítenie kell a 3-as verziójú. Lásd: [frissítsen az Azure Search .NET SDK 3-as verziójú](search-dotnet-sdk-migration.md) utasításokat.
>

Először frissítse a NuGet referencia `Microsoft.Azure.Search` vagy a NuGet-Csomagkezelő konzol használatával vagy az csomagot jobb gombbal a projekt hivatkozásait, majd válassza a "Kezelése NuGet csomagjainak..." a Visual Studióban.

Miután NuGet töltött le az új csomagok és a függőségek, a projekt újraépítéséhez.

Ha korábban a verzió 1.0.0-preview, 1.0.1-preview vagy 1.0.2-preview, a build sikeres legyen, és készen áll!

Ha korábban a verzió 0.13.0-preview vagy régebbi, megtekintheti az létrehozása a következő hibákat:

    Program.cs(137,56,137,62): error CS0117: 'Microsoft.Azure.Search.Models.IndexBatch' does not contain a definition for 'Create'
    Program.cs(137,99,137,105): error CS0117: 'Microsoft.Azure.Search.Models.IndexAction' does not contain a definition for 'Create'
    Program.cs(146,41,146,54): error CS1061: 'Microsoft.Azure.Search.IndexBatchException' does not contain a definition for 'IndexResponse' and no extension method 'IndexResponse' accepting a first argument of type 'Microsoft.Azure.Search.IndexBatchException' could be found (are you missing a using directive or an assembly reference?)
    Program.cs(163,13,163,42): error CS0246: The type or namespace name 'DocumentSearchResponse' could not be found (are you missing a using directive or an assembly reference?)

A következő lépés a build egyenként hibák javítására. A legtöbb módosítása néhány osztály és a metódus nevét, az SDK átnevezett van szükség. [Módosítások megtörje 1.1-es verzió listája](#ListOfChangesV1) neve módosítások listáját tartalmazza.

Ha egyéni osztályok segítségével modellhez tartozó dokumentumok, és azon osztályok nem nullázható egyszerű típusú tulajdonságokhoz rendelkezik (például `int` vagy `bool` C#), nincs hibajavítás az 1.1-es verziója az SDK-t, amelynek tudnia kell. Lásd: [hibajavítások 1.1-es verzió](#BugFixesV1) további részleteket.

Végezetül build hibák megszüntetése után módosításokat végezheti el az új funkciók előnyeit, ha az alkalmazáshoz.

<a name="ListOfChangesV1"></a>

### <a name="list-of-breaking-changes-in-version-11"></a>Módosítások megtörje 1.1-es verzió listája
Az alábbi lista rendezve annak a valószínűségét, hogy a változás érinteni fogja az alkalmazás kódjában.

#### <a name="indexbatch-and-indexaction-changes"></a>IndexBatch és IndexAction módosítása
`IndexBatch.Create` átnevezték `IndexBatch.New` és már nem rendelkezik egy `params` argumentum. Használhat `IndexBatch.New` kötegek, amely különböző típusú műveletek (összevonása, törlés stb.) kombinálhatók. Emellett nincsenek új statikus módszereket biztosít a kötegek ahol a műveletek megegyeznek: `Delete`, `Merge`, `MergeOrUpload`, és `Upload`.

`IndexAction` már nem rendelkezik nyilvános konstruktorral, és most nem módosíthatók a tulajdonságait. Az új statikus metódusok többféle célra műveletek létrehozásához kell használnia: `Delete`, `Merge`, `MergeOrUpload`, és `Upload`. `IndexAction.Create` el lett távolítva. Ha csak egy dokumentumot a rendelkező túlterhelést, ügyeljen arra, hogy használjon `Upload` helyette.

##### <a name="example"></a>Példa
Ha a kód így néz ki:

    var batch = IndexBatch.Create(documents.Select(doc => IndexAction.Create(doc)));
    indexClient.Documents.Index(batch);

Módosíthatja a build hibák elhárításához:

    var batch = IndexBatch.New(documents.Select(doc => IndexAction.Upload(doc)));
    indexClient.Documents.Index(batch);

Ha azt szeretné, hogy tovább egyszerűsítheti azt a:

    var batch = IndexBatch.Upload(documents);
    indexClient.Documents.Index(batch);

#### <a name="indexbatchexception-changes"></a>IndexBatchException módosítások
A `IndexBatchException.IndexResponse` tulajdonság lett átnevezve `IndexingResults`, és a típus már `IList<IndexingResult>`.

##### <a name="example"></a>Példa
Ha a kód így néz ki:

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

#### <a name="operation-method-changes"></a>A művelet metódus változások
Az Azure Search .NET SDK minden műveletet a szinkron és aszinkron hívóknak metódus túlterhelések készletként van közzétéve. Az aláírás és az e metódus túlterhelések faktoring megváltozott 1.1-es verzió.

Például a "Get Index statisztika" művelet az SDK korábbi verzióiban érhető el ezen aláírások:

A `IIndexOperations`:

    // Asynchronous operation with all parameters
    Task<IndexGetStatisticsResponse> GetStatisticsAsync(
        string indexName,
        CancellationToken cancellationToken);

A `IndexOperationsExtensions`:

    // Asynchronous operation with only required parameters
    public static Task<IndexGetStatisticsResponse> GetStatisticsAsync(
        this IIndexOperations operations,
        string indexName);

    // Synchronous operation with only required parameters
    public static IndexGetStatisticsResponse GetStatistics(
        this IIndexOperations operations,
        string indexName);

A metódus-aláírása az 1.1-es verziója azonos műveletre néznek ki:

A `IIndexesOperations`:

    // Asynchronous operation with lower-level HTTP features exposed
    Task<AzureOperationResponse<IndexGetStatisticsResult>> GetStatisticsWithHttpMessagesAsync(
        string indexName,
        SearchRequestOptions searchRequestOptions = default(SearchRequestOptions),
        Dictionary<string, List<string>> customHeaders = null,
        CancellationToken cancellationToken = default(CancellationToken));

A `IndexesOperationsExtensions`:

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

1.1-es verziójától kezdve, az Azure Search .NET SDK rendszerezi művelet módszerek másképp:

* Választható paraméterek: most már van modellezve, ahelyett, hogy a paraméterek alapértelmezett további metódus túlterhelések-nál. Ez csökkenti metódus túlterhelések, egyes esetekben jelentős mértékben.
* A kiterjesztésmetódusok mostantól HTTP idegen részleteit a hívó számos elrejtése. Például az SDK régebbi verzióit objektumot adott vissza, a válasz a HTTP-állapotkódot, amelyek gyakran nem kell ellenőrizni, mert a művelet módszerek throw `CloudException` bármely status kód, amely jelzi a hiba. Az új kiterjesztésmetódusok csak térjen vissza a modell objektumait, így meg lehet spórolni a hiba, hogy azokat a kódban kicsomagolásához.
* Ezzel ellentétben a core felületeihez most az olyan teszi közzé a módszereket, amelyek jobban irányítható HTTP szinten ha esetleg szükség lenne rá. Most továbbíthatja a kérelmeket, és az új szereplő egyéni HTTP-fejlécekben `AzureOperationResponse<T>` visszatérési típusa történő közvetlen hozzáférést biztosít a `HttpRequestMessage` és `HttpResponseMessage` a művelethez. `AzureOperationResponse` a van definiálva a `Microsoft.Rest.Azure` névtér és a felváltja `Hyak.Common.OperationResponse`.

#### <a name="scoringparameters-changes"></a>ScoringParameters módosítások
Egy új osztályt `ScoringParameter` könnyebb paraméterek megadásához a profilok pontozási keresési lekérdezés az SDK legújabb hozzá lett adva. Korábban a `ScoringProfiles` tulajdonsága a `SearchParameters` osztály volt megadva, `IList<string>`; Most írta-e be `IList<ScoringParameter>`.

##### <a name="example"></a>Példa
Ha a kód így néz ki:

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

#### <a name="model-class-changes"></a>Modellmódosításokkal osztály
A leírt aláírás-módosítások miatt [művelet metódus módosítások](#OperationMethodChanges), sok osztály a a `Microsoft.Azure.Search.Models` névtér átnevezték vagy eltávolítani. Példa:

* `IndexDefinitionResponse` lett cserélve `AzureOperationResponse<Index>`
* A `DocumentSearchResponse` új nevet kapott: `DocumentSearchResult`
* A `IndexResult` új nevet kapott: `IndexingResult`
* `Documents.Count()` most adja vissza egy `long` a dokumentum számával, hanem egy `DocumentCountResponse`
* A `IndexGetStatisticsResponse` új nevet kapott: `IndexGetStatisticsResult`
* A `IndexListResponse` új nevet kapott: `IndexListResult`

Összefoglalva, `OperationResponse`-származtatott osztályok, hogy csak a burkolása el lettek távolítva a modellobjektumot. A fennmaradó osztályok rendelkezésére állt-e a változása utótag `Response` való `Result`.

##### <a name="example"></a>Példa
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

##### <a name="response-classes-and-ienumerable"></a>Válasz osztályok és IEnumerable
Egy további, a kód érintő módosítást a válasz osztályok gyűjtemények vonatkozik, amelyek már nem valósítja meg `IEnumerable<T>`. Ehelyett a gyűjteménytulajdonság közvetlenül is elérheti. Ha például a kód néz ki:

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

##### <a name="special-case-for-web-applications"></a>Webes alkalmazásokhoz, különleges esetben
Ha a webes alkalmazás az rendezi sorba `DocumentSearchResponse` közvetlenül a keresési eredmények kapjon a böngésző, módosítania kell a kódot kell, vagy az eredmények nem fogja szerializálni megfelelően. Ha például a kód néz ki:

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

Olvasson be módosíthatja a `.Results` javítsa ki a keresési eredmények megjelenítésének keresési válasz tulajdonsága:

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

Meg kell keresni ezekben az esetekben a kódot. **a fordító nem figyelmeztet,** mert `JsonResult.Data` típusú `object`.

#### <a name="cloudexception-changes"></a>CloudException módosítások
A `CloudException` osztály tért át a `Hyak.Common` névtér a `Microsoft.Rest.Azure` névtér. Emellett a `Error` tulajdonság lett átnevezve `Body`.

#### <a name="searchserviceclient-and-searchindexclient-changes"></a>SearchServiceClient és SearchIndexClient módosítása
Milyen típusú a `Credentials` tulajdonság megváltozott `SearchCredentials` annak alaposztályát, hogy `ServiceClientCredentials`. Ha el szeretné érni a `SearchCredentials` , egy `SearchIndexClient` vagy `SearchServiceClient`, használja az új `SearchCredentials` tulajdonság.

Az SDK-t és korábbi verzióiban `SearchServiceClient` és `SearchIndexClient` konstruktorokat, amelyek került volna egy `HttpClient` paraméter. Ezek konstruktorok használatát, amelyek helyett egy `HttpClientHandler` és tömbje `DelegatingHandler` objektumok. Így könnyebben előre feldolgozzák a HTTP-kérelmek szükség esetén az egyéni kezelők telepítéséhez.

Végezetül, amelyre szükség volt a konstruktorok egy `Uri` és `SearchCredentials` megváltoztak. Ha például rendelkezik kódot, amely a következőképpen néz ki:

    var client =
        new SearchServiceClient(
            new SearchCredentials("abc123"),
            new Uri("http://myservice.search.windows.net"));

Módosíthatja a build hibák elhárításához:

    var client =
        new SearchServiceClient(
            new Uri("http://myservice.search.windows.net"),
            new SearchCredentials("abc123"));

Is vegye figyelembe, hogy a hitelesítő adatok paraméter típusa módosult `ServiceClientCredentials`. Ez nem valószínű, hogy a kód óta hatással `SearchCredentials` származó `ServiceClientCredentials`.

#### <a name="passing-a-request-id"></a>A kérelem azonosító továbbításához
Az SDK régebbi verzióit, beállíthat egy Kérelemazonosító a a `SearchServiceClient` vagy `SearchIndexClient` és minden kérelemben REST API kellene szerepelnie. Ez akkor hasznos, kapcsolatos hibák elhárításához a keresőszolgáltatása Ha kell forduljon a támogatási szolgálathoz. Azonban célszerű több minden művelethez egyedi Kérelemazonosító beállítása helyett ugyanezzel az Azonosítóval használni minden műveletnél. Emiatt a `SetClientRequestId` módszerek `SearchServiceClient` és `SearchIndexClient` el lettek távolítva. Ehelyett átadhatók a Kérelemazonosító minden művelet metódus segítségével a választható `SearchRequestOptions` paraméter.

> [!NOTE]
> Egy későbbi kiadásban az SDK adunk hozzá egy új mechanizmus beállításához a Kérelemazonosító globálisan az ügyfélen lévő objektumok, amely megfelel az egyéb Azure SDK-k által használt módszer.
> 
> 

#### <a name="example"></a>Példa
Ha kódot, amely a következőképpen néz ki:

    client.SetClientRequestId(Guid.NewGuid());
    ...
    long count = client.Documents.Count();

Módosíthatja a build hibák elhárításához:

    long count = client.Documents.Count(new SearchRequestOptions(requestId: Guid.NewGuid()));

#### <a name="interface-name-changes"></a>Csatoló nevének módosítása
A művelet csoport illesztőneveket összhangban vannak a megfelelő tulajdonságneveket változásai:

* Milyen típusú `ISearchServiceClient.Indexes` át lett nevezve a `IIndexOperations` való `IIndexesOperations`.
* Milyen típusú `ISearchServiceClient.Indexers` át lett nevezve a `IIndexerOperations` való `IIndexersOperations`.
* Milyen típusú `ISearchServiceClient.DataSources` át lett nevezve a `IDataSourceOperations` való `IDataSourcesOperations`.
* Milyen típusú `ISearchIndexClient.Documents` át lett nevezve a `IDocumentOperations` való `IDocumentsOperations`.

Ez a változás nem valószínű, hogy a kód befolyásolja, hacsak mocks ezen felületek tesztelési célból létrehozott.

<a name="BugFixesV1"></a>

### <a name="bug-fixes-in-version-11"></a>Hibajavítások 1.1-es verzió
Hiba történt az Azure Search .NET SDK egyéni modell osztályok szerializálási régebbi verzióit. A hiba akkor fordulhat elő, ha létrehozott egy egyéni modellosztállyal egy nem nullázható értéktípus tulajdonsággal.

#### <a name="steps-to-reproduce"></a>Reprodukálásához szükséges lépések
Hozzon létre egy egyéni modellosztállyal egy tulajdonság nem nullázható értéktípus. Például hozzáadhat egy nyilvános `UnitCount` típusú tulajdonság `int` helyett `int?`.

Ha az alapértelmezett érték az adott típusú dokumentum indexeli (például 0 `int`), a mező null értékű, az Azure Search lesz. Ha ezt követően a felhasználó, a `Search` hívás kivételhibát `JsonSerializationException` panaszos, amely nem konvertálható `null` való `int`.

Szűrők is, nem működnek megfelelően, mivel null bejegyzésre kerültek-e az indexet a kívánt érték helyett.

#### <a name="fix-details"></a>Javítsa ki a részletei
A probléma az SDK 1.1-es verziójában megoldottuk azt. Most, ha van egy modell ehhez hasonló:

    public class Model
    {
        public string Key { get; set; }

        public int IntValue { get; set; }
    }

és a beállított `IntValue` 0, ezt az értéket, most már megfelelően szerializálni a 0 értéket a keresztülhaladnak a hálózaton és a rendszer a 0 értéket a index. Kerek esetén is megfelelően működik-e.

Érdemes figyelembe ezt a módszert egy lehetséges probléma: Ha a modell típusa egy nem nullázható tulajdonságot használja, hogy **garantálni** , hogy az indexben egyetlen dokumentum tartalmazza-e a megfelelő mező null értéket. Az SDK-t és az Azure Search REST API nem leírtakkal érvényesítik.

Ennek nem csupán elméleti jelentősége van: képzeljünk el például egy olyan alkalmazási helyzetet, ahol egy `Edm.Int32` típusú, meglévő indexhez új mezőt kell hozzáadnunk. Az indexdefiníció frissítését követően ehhez a mezőhöz minden dokumentumban null érték tartozik (mivel az Azure Search szolgáltatásban az összes értéktípus nullázható). Ha ezt követően egy modellosztályt úgy alkalmaz, hogy ehhez a mezőhöz nem nullázható `int` tulajdonságot ad meg, a dokumentumok lekérdezésének megkísérlésekor egy ehhez hasonló `JsonSerializationException` választ kap:

    Error converting value {null} to type 'System.Int32'. Path 'IntValue'.

Ezért továbbra is javasoljuk, hogy használjon nullázható típusok a modell osztályok az ajánlott eljárás.

Ezt a hibát, és a javítás további részletekért lásd: [a Githubon probléma](https://github.com/Azure/azure-sdk-for-net/issues/1063).

