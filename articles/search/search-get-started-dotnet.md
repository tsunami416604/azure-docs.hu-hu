---
title: 'Gyors útmutató: keresési index létrehozása a .NET-ben'
titleSuffix: Azure Cognitive Search
description: Ebből a C# rövid útmutatóból megtudhatja, hogyan hozhat létre indexet, tölthet be és hogyan futtathat lekérdezéseket a Azure.Search.Documents ügyféloldali kódtár használatával.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 08/05/2020
ms.custom: devx-track-csharp
ms.openlocfilehash: e1a52a15012e367dc902992f7f7b905fc6c6a5eb
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/05/2020
ms.locfileid: "91541543"
---
# <a name="quickstart-create-a-search-index-using-the-azuresearchdocuments-client-library"></a>Gyors útmutató: keresési index létrehozása a Azure.Search.Documents ügyféloldali kódtár használatával

Az új [Azure.Search.Documents (11-es verzió) ügyféloldali kódtár](/dotnet/api/overview/azure/search.documents-readme) használatával létrehozhat egy .net Core Console-alkalmazást a C#-ban, amely egy keresési indexet hoz létre, tölt be és kérdez le.

[Töltse le a forráskódot](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/quickstart/v11) , hogy a kész projekttel kezdődjön, vagy kövesse a cikk lépéseit saját létrehozásához.

> [!NOTE]
> Korábbi verziót keres? Lásd: [keresési index létrehozása a Microsoft. Azure. Search v10 használatával](search-get-started-dotnet-v10.md) .

## <a name="prerequisites"></a>Előfeltételek

A Kezdés előtt a következő eszközökkel és szolgáltatásokkal rendelkezhet:

+ Aktív előfizetéssel rendelkező Azure-fiók. [Hozzon létre egy fiókot ingyenesen](https://azure.microsoft.com/free/).

+ Egy Azure Cognitive Search szolgáltatás. [Hozzon létre egy szolgáltatást](search-create-service-portal.md) , vagy [keressen egy meglévő szolgáltatást](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices). Ehhez a rövid útmutatóhoz ingyenes szolgáltatást is használhat. 

+ A [Visual Studio](https://visualstudio.microsoft.com/downloads/)bármely kiadása. A mintakód a Visual Studio 2019 ingyenes közösségi kiadásában lett tesztelve.

<a name="get-service-info"></a>

## <a name="get-a-key-and-endpoint"></a>Kulcs és végpont beolvasása

A szolgáltatás felé irányuló hívások URL-végpontot és hozzáférési kulcsot igényelnek minden kérelemben. A Search szolgáltatás mindkettővel jön létre, így ha az előfizetéshez hozzáadta az Azure Cognitive Searcht, kövesse az alábbi lépéseket a szükséges információk beszerzéséhez:

1. [Jelentkezzen be a Azure Portalba](https://portal.azure.com/), és a keresési szolgáltatás **Áttekintés** lapján töltse le az URL-címet. A végpontok például a következőképpen nézhetnek ki: `https://mydemo.search.windows.net`.

2. A **Beállítások**  >  **kulcsainál**kérjen meg egy rendszergazdai kulcsot a szolgáltatásra vonatkozó teljes jogosultságokról, ha objektumokat hoz létre vagy töröl. Két felcserélhető elsődleges és másodlagos kulcs van. Bármelyiket használhatja.

   ![HTTP-végpont és elérési kulcs beszerzése](media/search-get-started-postman/get-url-key.png "HTTP-végpont és elérési kulcs beszerzése")

Minden kérelemhez API-kulcs szükséges a szolgáltatásnak küldött összes kéréshez. Érvényes kulcs birtokában kérelmenként létesíthető megbízhatósági kapcsolat a kérést küldő alkalmazás és az azt kezelő szolgáltatás között.

## <a name="set-up-your-project"></a>A projekt beállítása

Indítsa el a Visual studiót, és hozzon létre egy új, a .NET Core-on futtatható konzol-alkalmazási projektet. 

### <a name="install-the-nuget-package"></a>A NuGet-csomag telepítése

A projekt létrehozása után adja hozzá az ügyféloldali kódtárat. A [Azure.Search.Documents csomag](https://www.nuget.org/packages/Azure.Search.Documents/) egy olyan ügyféloldali függvénytárból áll, amely biztosítja a .net-beli keresési szolgáltatással való együttműködéshez használt összes API-t.

1. Az **eszközök**  >  **NuGet csomagkezelő**területén válassza a **NuGet-csomagok kezelése megoldást..**. lehetőséget. 

1. Kattintson a **Browse** (Tallózás) gombra.

1. Keresse meg `Azure.Search.Documents` és válassza ki a 11.0.0 verziót.

1. A jobb oldalon kattintson a **telepítés** gombra a szerelvény projekthez és megoldáshoz való hozzáadásához.

### <a name="create-a-search-client"></a>Keresési ügyfél létrehozása

1. A **program.cs**módosítsa a névteret, `AzureSearch.SDK.Quickstart.v11` majd adja hozzá a következő `using` irányelveket.

   ```csharp
   using Azure;
   using Azure.Search.Documents;
   using Azure.Search.Documents.Indexes;
   using Azure.Search.Documents.Indexes.Models;
   using Azure.Search.Documents.Models;
   ```

1. Hozzon létre két ügyfelet: a [SearchIndexClient](/dotnet/api/azure.search.documents.indexes.searchindexclient) létrehozza az indexet, és a [SearchClient](/dotnet/api/azure.search.documents.searchclient) egy meglévő indextel működik. A létrehozási/törlési jogokkal való hitelesítéshez mindkettőnek szüksége van a szolgáltatási végpontra és egy rendszergazdai API-kulcsra.

   ```csharp
   static void Main(string[] args)
   {
       string serviceName = "<YOUR-SERVICE-NAME>";
       string indexName = "hotels-quickstart-v11";
       string apiKey = "<YOUR-ADMIN-API-KEY>";

       // Create a SearchIndexClient to send create/delete index commands
       Uri serviceEndpoint = new Uri($"https://{serviceName}.search.windows.net/");
       AzureKeyCredential credential = new AzureKeyCredential(apiKey);
       SearchIndexClient idxclient = new SearchIndexClient(serviceEndpoint, credential);

       // Create a SearchClient to load and query documents
       SearchClient qryclient = new SearchClient(serviceEndpoint, indexName, credential);
    ```

## <a name="1---create-an-index"></a>1 – Index létrehozása

Ez a rövid útmutató egy olyan szállodai indexet hoz létre, amelyet betölt a szállodai adatforrásokkal, és lekérdezéseket futtat. Ebben a lépésben definiálja a mezőket az indexben. Minden mező definíciója tartalmaz egy nevet, egy adattípust és egy attribútumot, amely meghatározza, hogy a rendszer hogyan használja a mezőt.

Ebben a példában a Azure.Search.Documents könyvtár szinkron metódusait használjuk az egyszerűség és az olvashatóság érdekében. Éles környezetekben azonban aszinkron metódusokat kell használnia, hogy az alkalmazás méretezhető és rugalmas legyen. A [CreateIndex](/dotnet/api/azure.search.documents.indexes.searchindexclient.createindex)helyett például a [CreateIndexAsync](/dotnet/api/azure.search.documents.indexes.searchindexclient.createindexasync) -et kellene használnia.

1. Adjon hozzá egy üres osztály-definíciót a projekthez: **Hotel.cs**

1. A **Hotel.cs**határozza meg a szállodai dokumentumok szerkezetét.

    ```csharp
    using System;
    using System.Text.Json.Serialization;

    namespace AzureSearch.SDK.Quickstart.v11
    {
        public class Hotel
        {
            [JsonPropertyName("hotelId")]
            public string Id { get; set; }

            [JsonPropertyName("hotelName")]
            public string Name { get; set; }

            [JsonPropertyName("hotelCategory")]
            public string Category { get; set; }

            [JsonPropertyName("baseRate")]
            public Int32 Rate { get; set; }

            [JsonPropertyName("lastRenovationDate")]
            public DateTime Updated { get; set; }
        }
    }
    ```

1. A **program.cs**területen határozza meg a mezőket és az attribútumokat. A [SearchIndex](/dotnet/api/azure.search.documents.indexes.models.searchindex) és a [CreateIndex](/dotnet/api/azure.search.documents.indexes.searchindexclient.createindex) index létrehozására szolgál.

   ```csharp
    // Define an index schema using SearchIndex
    // Create the index using SearchIndexClient
    SearchIndex index = new SearchIndex(indexName)
    {
        Fields =
            {
                new SimpleField("hotelId", SearchFieldDataType.String) { IsKey = true, IsFilterable = true, IsSortable = true },
                new SearchableField("hotelName") { IsFilterable = true, IsSortable = true },
                new SearchableField("hotelCategory") { IsFilterable = true, IsSortable = true },
                new SimpleField("baseRate", SearchFieldDataType.Int32) { IsFilterable = true, IsSortable = true },
                new SimpleField("lastRenovationDate", SearchFieldDataType.DateTimeOffset) { IsFilterable = true, IsSortable = true }
            }
    };

    Console.WriteLine("{0}", "Creating index...\n");
    idxclient.CreateIndex(index);
   ```

A mező attribútumai határozzák meg, hogyan használják az alkalmazásokban. Az `IsFilterable` attribútumot például minden olyan mezőhöz hozzá kell rendelni, amely támogatja a szűrési kifejezést.

A .NET SDK azon korábbi verzióival szemben, amelyek kereshető [IsSearchable](/dotnet/api/microsoft.azure.search.models.field.issearchable) igényelnek, a [SearchableField](/dotnet/api/azure.search.documents.indexes.models.searchablefield) és a [SimpleField](/dotnet/api/azure.search.documents.indexes.models.simplefield) használatával egyszerűsítheti a mezők definícióit.

A korábbi verziókhoz hasonlóan további attribútumok is szükségesek a definícióban. Például a [IsFilterable](/dotnet/api/azure.search.documents.indexes.models.searchfield.isfilterable), a [IsSortable](/dotnet/api/azure.search.documents.indexes.models.searchfield.issortable)és a [IsFacetable](/dotnet/api/azure.search.documents.indexes.models.searchfield.isfacetable) explicit módon kell szerepelnie a fenti mintában látható módon. 

<a name="load-documents"></a>

## <a name="2---load-documents"></a>2 – dokumentumok betöltése

Az Azure Cognitive Search megkeresi a szolgáltatásban tárolt tartalmakat. Ebben a lépésben az imént létrehozott szállodai indexnek megfelelő JSON-dokumentumokat fogja betölteni.

Az Azure Cognitive Searchban a dokumentumok olyan adatstruktúrák, amelyek mind a lekérdezések indexeléséhez, mind pedig a kimenetekhez tartoznak. Egy külső adatforrásból beszerzett módon a dokumentumok bemenetei egy adatbázisban, a blob Storage-ban vagy a lemezen található JSON-dokumentumok soraiban lehetnek. Ebben a példában egy, a kódban öt szálláshelyre mutató parancsikont és beágyazott JSON-dokumentumot használunk. 

Dokumentumok feltöltésekor [IndexDocumentsBatch](/dotnet/api/azure.search.documents.models.indexdocumentsbatch-1) objektumot kell használnia. A IndexDocumentsBatch olyan [műveletek](/dotnet/api/azure.search.documents.models.indexdocumentsbatch-1.actions)gyűjteményét tartalmazza, amelyek mindegyike tartalmaz egy dokumentumot és egy olyan tulajdonságot, amely az Azure Cognitive Search a végrehajtandó műveletet ([feltöltés, Egyesítés, törlés és mergeOrUpload](search-what-is-data-import.md#indexing-actions)).

1. A **program.cs**-ben hozzon létre egy tömböt a dokumentumok és az indexelési műveletek közül, majd adja át a tömböt `ndexDocumentsBatch` az alábbi dokumentumoknak, amelyek megfelelnek a Hotel osztály által definiált Hotels-Gyorsindítás-v11 indexnek.

    ```csharp
    // Load documents (using a subset of fields for brevity)
    IndexDocumentsBatch<Hotel> batch = IndexDocumentsBatch.Create(
        IndexDocumentsAction.Upload(new Hotel { Id = "78", Name = "Upload Inn", Category = "hotel", Rate = 279, Updated = new DateTime(2018, 3, 1, 7, 0, 0) }),
        IndexDocumentsAction.Upload(new Hotel { Id = "54", Name = "Breakpoint by the Sea", Category = "motel", Rate = 162, Updated = new DateTime(2015, 9, 12, 7, 0, 0) }),
        IndexDocumentsAction.Upload(new Hotel { Id = "39", Name = "Debug Motel", Category = "motel", Rate = 159, Updated = new DateTime(2016, 11, 11, 7, 0, 0) }),
        IndexDocumentsAction.Upload(new Hotel { Id = "48", Name = "NuGet Hotel", Category = "hotel", Rate = 238, Updated = new DateTime(2016, 5, 30, 7, 0, 0) }),
        IndexDocumentsAction.Upload(new Hotel { Id = "12", Name = "Renovated Ranch", Category = "motel", Rate = 149, Updated = new DateTime(2020, 1, 24, 7, 0, 0) }));

    IndexDocumentsOptions idxoptions = new IndexDocumentsOptions { ThrowOnAnyError = true };

    Console.WriteLine("{0}", "Loading index...\n");
    qryclient.IndexDocuments(batch, idxoptions);
    ```

    Miután elvégezte a [IndexDocumentsBatch](/dotnet/api/azure.search.documents.models.indexdocumentsbatch-1) objektum inicializálását, a [SearchClient](/dotnet/api/azure.search.documents.searchclient) objektum [IndexDocuments](/dotnet/api/azure.search.documents.searchclient.indexdocuments) meghívásával elküldheti az indexbe.

1. Mivel ez egy olyan konzolos alkalmazás, amely egymás után futtatja az összes parancsot, adjon meg egy 2 másodperces várakozási időt az indexelés és a lekérdezések között.

    ```csharp
    // Wait 2 seconds for indexing to complete before starting queries (for demo and console-app purposes only)
    Console.WriteLine("Waiting for indexing...\n");
    System.Threading.Thread.Sleep(2000);
    ```

    A 2 másodperces késleltetés kompenzálja az indexelést, amely aszinkron módon történik, így minden dokumentum indexelhető a lekérdezések végrehajtása előtt. A késések kódolása jellemzően csak demók, tesztek és példák esetében szükséges.

## <a name="3---search-an-index"></a>3 – Keresés az indexekben

A lekérdezési eredményeket az első dokumentum indexelése után azonnal lekérheti, de az index tényleges tesztelésének meg kell várnia, amíg az összes dokumentum indexelve van.

Ez a szakasz két funkciót tartalmaz: a lekérdezési logikát és az eredményeket. Lekérdezésekhez használja a [keresési](/dotnet/api/azure.search.documents.searchclient.search) módszert. Ez a metódus a keresési szöveget (a lekérdezési karakterláncot) és egyéb [beállításokat](/dotnet/api/azure.search.documents.searchoptions)is végrehajtja.

A [SearchResults](/dotnet/api/azure.search.documents.models.searchresults-1) osztály az eredményeket jelöli.

1. A **program.cs**-ben hozzon létre egy WriteDocuments metódust, amely a keresési eredményeket a konzolra nyomtatja.

    ```csharp
    private static void WriteDocuments(SearchResults<Hotel> searchResults)
    {
        foreach (SearchResult<Hotel> response in searchResults.GetResults())
        {
            Hotel doc = response.Document;
            var score = response.Score;
            Console.WriteLine($"Name: {doc.Name}, Type: {doc.Category}, Rate: {doc.Rate}, Last-update: {doc.Updated}, Score: {score}");
        }

        Console.WriteLine();
    }
    ```

1. Hozzon létre egy RunQueries metódust a lekérdezések végrehajtásához és az eredmények visszaadásához. Az eredmények a Hotel objektumai.

    ```csharp
    private static void RunQueries(SearchClient qryclient)
    {
        SearchOptions options;
        SearchResults<Hotel> response;

        Console.WriteLine("Query #1: Search on the term 'motel' and list the relevance score for each match...\n");

        options = new SearchOptions()
        {
            Filter = "",
            OrderBy = { "" }
        };

        response = qryclient.Search<Hotel>("motel", options);
        WriteDocuments(response);

        Console.WriteLine("Query #2: Find hotels where 'type' equals hotel...\n");

        options = new SearchOptions()
        {
            Filter = "hotelCategory eq 'hotel'",
        };

        response = qryclient.Search<Hotel>("*", options);
        WriteDocuments(response);

        Console.WriteLine("Query #3: Filter on rates less than $200 and sort by when the hotel was last updated...\n");

        options = new SearchOptions()
        {
            Filter = "baseRate lt 200",
            OrderBy = { "lastRenovationDate desc" }
        };

        response = qryclient.Search<Hotel>("*", options);
        WriteDocuments(response);
    }
    ```

Ez a példa a [kifejezések egyezésének két módját mutatja be egy lekérdezésben](search-query-overview.md#types-of-queries): teljes szöveges keresés és szűrők:

+ Egy vagy több kifejezés teljes szöveges keresési lekérdezése az index kereshető mezőiben. Az első lekérdezés a teljes szöveges keresés. A teljes szöveges keresés az eredmények rangsorolására szolgáló releváns pontszámokat eredményez.

+ A Filter egy logikai kifejezés, amely egy index [IsFilterable](/dotnet/api/azure.search.documents.indexes.models.searchfield.isfilterable) mezőin keresztül lesz kiértékelve. A lekérdezéseket belefoglalhatja vagy kizárhatja az értékek közül. Ennek megfelelően a szűrő lekérdezéséhez nincs releváns pontszám társítva. Az utolsó két lekérdezés szemlélteti a szűrők keresését.

A teljes szöveges keresést és a szűrőket együtt vagy külön is használhatja.

A keresések és a szűrők a [SearchClient. Search](/dotnet/api/azure.search.documents.searchclient.search) metódus használatával is elvégezhetők. Keresési lekérdezés adható át a `searchText` karakterláncban, míg a [SearchOptions](/dotnet/api/azure.search.documents.searchoptions) osztály [Filter](/dotnet/api/azure.search.documents.searchoptions.filter) tulajdonságában egy szűrési kifejezés adható át. A keresés nélküli szűréshez csak adja `"*"` meg a `searchText` [keresési](/dotnet/api/azure.search.documents.searchclient.search) metódus paraméterét. Szűrés nélküli kereséshez hagyja üresen a `Filter` tulajdonságot, vagy ne adjon meg egyetlen `SearchOptions` példányt sem.

## <a name="run-the-program"></a>A program futtatása

Nyomja le az F5 billentyűt az alkalmazás újraépítéséhez és a program teljes körű futtatásához. 

A kimenet magában foglalja a [Console. WriteLIne](/dotnet/api/system.console.writeline)és a lekérdezési adatok és eredmények hozzáadásával kapcsolatos üzeneteket.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha a saját előfizetésében dolgozik, érdemes az egyes projektek végén eldöntenie, hogy szüksége lesz-e még a létrehozott erőforrásokra. A továbbra is futó erőforrások költségekkel járhatnak. Az erőforrásokat törölheti egyesével, vagy az erőforráscsoport törlésével eltávolíthatja a benne lévő összes erőforrást is.

A bal oldali navigációs panelen a **minden erőforrás** vagy **erőforráscsoport** hivatkozás használatával megkeresheti és kezelheti az erőforrásokat a portálon.

Ha ingyenes szolgáltatást használ, ne feledje, hogy Ön legfeljebb három indexet, indexelő és adatforrást használhat. A portálon törölheti az egyes elemeket, hogy a korlát alatt maradjon. 

## <a name="next-steps"></a>További lépések

Ebben a C# rövid útmutatóban egy sor feladatot dolgozott ki egy index létrehozásához, a dokumentumok betöltéséhez és a lekérdezések futtatásához. Az olvashatóság és a megértés érdekében a kód leegyszerűsítése különböző fázisokban volt. Ha Ön az alapvető fogalmakkal is rendelkezik, javasoljuk, hogy a következő cikkből megtudhatja, milyen alternatív megközelítésekre és fogalmakra van szükség, amelyek elmélyítik az Ön ismereteit. 

> [!div class="nextstepaction"]
> [Fejlesztés a .NET-ben](search-howto-dotnet-sdk.md)

Szeretné optimalizálni és menteni a Felhőbeli kiadásait?

> [!div class="nextstepaction"]
> [A költségek elemzésének megkezdése a Cost Management](../cost-management-billing/costs/quick-acm-cost-analysis.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)