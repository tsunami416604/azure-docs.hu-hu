---
title: Create a search index in the Azure portal
titleSuffix: Azure Cognitive Search
description: In this portal quickstart, learn how to use the Import Data wizard to create, load, and query your first search index in Azure Cognitive Search.
author: HeidiSteen
manager: nitinme
ms.author: heidist
ms.service: cognitive-search
ms.topic: quickstart
ms.date: 11/04/2019
ms.openlocfilehash: be2425d97573e7990ea7f0dfd4c2d999e85fe922
ms.sourcegitcommit: dd0304e3a17ab36e02cf9148d5fe22deaac18118
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/22/2019
ms.locfileid: "74406998"
---
# <a name="quickstart-create-an-azure-cognitive-search-index-in-the-azure-portal"></a>Quickstart: Create an Azure Cognitive Search index in the Azure portal
> [!div class="op_single_selector"]
> * [Portal](search-get-started-portal.md)
> * [C#](search-get-started-dotnet.md)
> * [Java](search-get-started-java.md)
> * [Node.js](search-get-started-nodejs.md)
> * [PowerShell](search-get-started-powershell.md)
> * [Postman](search-get-started-postman.md)
> * [Python](search-get-started-python.md)

Use the portal's **Import data** wizard and **Search explorer** tools to quickly ramp up on concepts, and write interesting queries against an index within minutes.

If the tools are too limiting, you can consider a [code-based introduction to programming Azure Cognitive Search in .NET](search-howto-dotnet-sdk.md) or use [Postman for making REST API calls](search-get-started-postman.md). 

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt. 

## <a name="prerequisites"></a>Előfeltételek

[Create an Azure Cognitive Search service](search-create-service-portal.md) or [find an existing service](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) under your current subscription. You can use a free service for this quickstart. 

### <a name="check-for-space"></a>Szabad terület ellenőrzése

Sok ügyfél az ingyenes szolgáltatással kezdi. Ez a verzió három indexre, három adatforrásra és három indexelőre korlátozódik. Mielőtt hozzákezdene, ellenőrizze, hogy elegendő hellyel rendelkezik-e további elemek számára. Az oktatóanyagban minden objektumból egyet hozhat majd létre.

Sections on the service dashboard show how many indexes, indexers, and data sources you already have. 

![Lists of indexes, indexers, and datasources](media/search-get-started-portal/tiles-indexers-datasources.png)

## <a name="create-index"></a> Index létrehozása és az adatok betöltése

A keresési lekérdezések egy [*index*](search-what-is-an-index.md) alapján ismétlődnek, amely kereshető adatokat, metaadatokat és további szerkezeteket tartalmaz, amelyek bizonyos keresési viselkedések optimalizálásához használhatók.

For this tutorial, we use a built-in sample dataset that can be crawled using an [*indexer*](search-indexer-overview.md) via the [**Import data** wizard](search-import-data-portal.md). Az indexelők adatforrás-specifikus webbejárók, amelyek metaadatokat és tartalmakat képesek olvasni a támogatott Azure-adatforrásokból. Normally, indexers are used programmatically, but in the portal, you can access them through the **Import data** wizard. 

### <a name="step-1---start-the-import-data-wizard-and-create-a-data-source"></a>Step 1 - Start the Import data wizard and create a data source

1. On the Azure Cognitive Search service dashboard, click **Import data** on the command bar to create and populate a search index.

   ![Adatok importálása parancs](media/search-get-started-portal/import-data-cmd.png)

2. In the wizard, click **Connect to your data** > **Samples** > **hotels-sample**. This data source is built-in. If you were creating your own data source, you would need to specify a name, type, and connection information. Létrehozását követően „meglévő adatforrássá” válik, amely más importálási műveletek során ismét felhasználható.

   ![Minta adatkészlet kiválasztása](media/search-get-started-portal/import-datasource-sample.png)

3. Continue to the next page.

### <a name="step-2---skip-the-enrich-content-page"></a>Step 2 - Skip the "Enrich content" page

The wizard supports the creation of an [AI enrichment pipeline](cognitive-search-concept-intro.md) for incorporating the Cognitive Services AI algorithms into indexing. 

We'll skip this step for now, and move directly on to **Customize target index**.

   ![A kognitív képességek lépés kihagyása](media/search-get-started-portal/skip-cog-skill-step.png)

> [!TIP]
> You can step through an AI-indexing example in a [quickstart](cognitive-search-quickstart-blob.md) or [tutorial](cognitive-search-tutorial-blob.md).

### <a name="step-3---configure-index"></a>Step 3 - Configure index

Typically, index creation is a code-based exercise, completed prior to loading data. However, as this tutorial indicates, the wizard can generate a basic index for any data source it can crawl. Az indexhez szükség van legalább egy névre és egy mezőgyűjteményre, amely mezők közül az egyiket a dokumentum kulcsaként kell megjelölni. Ez az egyes dokumentumok egyedi azonosítására szolgál. Additionally, you can specify language analyzers or suggesters if you want autocomplete or suggested queries.

A mezők adattípusokkal és attribútumokkal rendelkeznek. A fent látható jelölőnégyzetek *indexattribútumok*, amelyek a mező használati módját szabályozzák.

* **Lekérhető**: azt jelenti, hogy a mező a keresési eredmények listájában jelenik meg. You can mark individual fields as off limits for search results by clearing this checkbox, for example for fields used only in filter expressions.
* **Key** is the unique document identifier. It's always a string, and it is required.
* **Filterable**, **Sortable**, and **Facetable** determine whether fields are used in a filter, sort, or faceted navigation structure.
* **Kereshető**: azt jelenti, hogy a mező szerepel a teljes szöveges keresésben. A sztringek kereshetők. A numerikus és logikai mezőket gyakran nem kereshetőként jelölik meg.

Storage requirements do not vary as a result of your selection. For example, if you set the **Retrievable** attribute on multiple fields, storage requirements do not go up.

Alapértelmezés szerint a varázsló átvizsgálja a adatforrást egyedi azonosítókat keresve, amelyeket felhasználhat a kulcsmező alapjaként. *Strings* are attributed as **Retrievable** and **Searchable**. *Integers* are attributed as **Retrievable**, **Filterable**, **Sortable**, and **Facetable**.

1. Fogadja el az alapértelmezett beállításokat. 

   If you rerun the wizard a second time using an existing hotels data source, the index won't be configured with default attributes. You'll have to manually select attributes on future imports. 

   ![Generated hotels index](media/search-get-started-portal/hotelsindex.png)

2. Continue to the next page.


### <a name="step-4---configure-indexer"></a>Step 4 - Configure indexer

Továbbra is az **Adatok importálása** varázslóban maradva kattintson az **Indexelő** > **Név** lehetőségre, és gépelje be az indexelő nevét.

Ez az objektum egy végrehajtható folyamatot határoz meg. You could put it on recurring schedule, but for now use the default option to run the indexer once, immediately.

Click **Submit** to create and simultaneously run the indexer.

  ![hotels indexer](media/search-get-started-portal/hotels-indexer.png)

## <a name="monitor-progress"></a>Monitor progress

The wizard should take you to the Indexers list where you can monitor progress. For self-navigation, go to the Overview page and click **Indexers**.

It can take a few minutes for the portal to update the page, but you should see the newly created indexer in the list, with status indicating "in progress" or success, along with the number of documents indexed.

   ![Indexelő állapotüzenete](media/search-get-started-portal/indexers-inprogress.png)

## <a name="view-the-index"></a>Az index megtekintése

The main service page provides links to the resources created in your Azure Cognitive Search service.  To view the index you just created, click **Indexes** from the list of links. 

Wait for the portal page to refresh. After a few minutes, you should see the index with a document count and storage size.

   ![Indexes list on the service dashboard](media/search-get-started-portal/indexes-list.png)

From this list, you can click on the *hotels-sample* index that you just created, view the index schema. and optionally add new fields. 

The **Fields** tab shows the index schema. Scroll to the bottom of the list to enter a new field. In most cases, you cannot change existing fields. Existing fields have a physical representation in Azure Cognitive Search and are thus non-modifiable, not even in code. To fundamentally change an existing field, create a new index, dropping the original.

   ![példa indexdefinícióra](media/search-get-started-portal/sample-index-def.png)

Egyéb szerkezetek, például pontozási profilok és CORS-paraméterek, bármikor hozzáadhatók.

Annak érdekében, hogy jól átlássa az indextervezés során szerkeszthető és nem szerkeszthető elemeket, szánjon egy percet az indexdefiníciós lehetőségek áttekintésére. A kiszürkített lehetőségek azt jelzik, hogy az érték nem módosítható vagy törölhető. 

## <a name="query-index"></a> Query using Search explorer

Továbblépve most már rendelkezünk egy keresési indexszel, amely készen áll a lekérdezésre a beépített [**Keresési ablak**](search-explorer.md) lekérdezési lappal. Ez egy keresőmezőt biztosít, amellyel tesztelheti a tetszőleges lekérdezési sztringeket.

**Search explorer** is only equipped to handle [REST API requests](https://docs.microsoft.com/rest/api/searchservice/search-documents), but it accepts syntax for both [simple query syntax](https://docs.microsoft.com/rest/api/searchservice/simple-query-syntax-in-azure-search) and [full Lucene query parser](https://docs.microsoft.com/rest/api/searchservice/lucene-query-syntax-in-azure-search), plus all the search parameters available in [Search Document REST API](https://docs.microsoft.com/rest/api/searchservice/search-documents#bkmk_examples) operations.

> [!TIP]
> The following steps are demonstrated at 6m08s into the [Azure Cognitive Search Overview video](https://channel9.msdn.com/Events/Connect/2016/138).
>

1. A parancssávon kattintson a **Keresési ablak** elemre.

   ![Keresési ablak parancs](media/search-get-started-portal/search-explorer-cmd.png)

2. From the **Index** dropdown, choose  *hotels-sample-index*. Click the **API Version** dropdown, to see which REST APIs are available. For the queries below, use the generally available version (2019-05-06).

   ![Index és API-parancsok](media/search-get-started-portal/search-explorer-changeindex.png)

3. In the search bar, paste in the query strings below and click **Search**.

   ![Query string and search button](media/search-get-started-portal/search-explorer-query-string-example.png)

## <a name="example-queries"></a>Example queries

You can enter terms and phrases, similar to what you might do in a Bing or Google search, or fully-specified query expressions. Results are returned as verbose JSON documents.

### <a name="simple-query-with-top-n-results"></a>Egyszerű lekérdezés az első N eredménnyel

#### <a name="example-string-query-searchspa"></a>Example (string query): `search=spa`

* The **search** parameter is used to input a keyword search for full text search, in this case, returning hotel data for those containing *spa* in any searchable field in the document.

* A **Keresési ablak** JSON-formátumban adja vissza az eredményeket, amely részletes és nehezen olvasható lehet, ha a dokumentumok sűrű szerkezettel rendelkeznek. This is intentional; visibility into the entire document is important for development purposes, especially during testing. A jobb felhasználói élmény érdekében olyan kódot kell írnia, amely [a keresési eredmények kezelésével](search-pagination-page-layout.md) kiemeli a fontosabb elemeket.

* A dokumentumokban minden mező „lekérdezhetőként” van jelölve az indexben. To view index attributes in the portal, click *hotels-sample* in the **Indexes** list.

#### <a name="example-parameterized-query-searchspacounttruetop10"></a>Example (parameterized query): `search=spa&$count=true&$top=10`

* Az **&** szimbólum a keresési paraméterek összefűzésére használható, amelyek bármilyen sorrendben megadhatók.

* The **$count=true** parameter returns the total count of all documents returned. Ez az érték a keresési eredmények elejénél található. A szűrőlekérdezések ellenőrzéséhez megfigyelheti a **$count=true** paraméter által jelentett módosításokat. A kisebb darabszámok azt jelzik, hogy a szűrő működik.

* The **$top=10** returns the highest ranked 10 documents out of the total. By default, Azure Cognitive Search returns the first 50 best matches. A **$top** paraméter használatával növelheti vagy csökkentheti a mennyiséget.

### <a name="filter-query"></a> A lekérdezés szűrése

A **$filter** paraméter hozzáfűzésekor a szűrők megjelennek a keresési kérésekben. 

#### <a name="example-filtered-searchbeachfilterrating-gt-4"></a>Példa (szűrt): `search=beach&$filter=Rating gt 4`

* A **$filter** paraméter olyan eredményeket ad vissza, amelyek megfelelnek a megadott feltételeknek. In this case, ratings greater than 4.

* A szűrőszintaxis egy OData-konstrukció. További információk: [OData-szűrőszintaxis](https://docs.microsoft.com/rest/api/searchservice/odata-expression-syntax-for-azure-search).

### <a name="facet-query"></a> A lekérdezés értékkorlátozása

Az értékkorlátozó szűrők megjelennek a keresési kérésekben. A facet paraméter adja vissza azon dokumentumok összegzett darabszámát, amelyek megfelelnek a megadott értékkorlátozási értéknek.

#### <a name="example-faceted-with-scope-reduction-searchfacetcategorytop2"></a>Példa (hatókörszűkítéssel korlátozva): `search=*&facet=Category&$top=2`

* A **search=** * egy üres keresés. Az üres keresések mindenben keresnek. Az üres lekérdezések elküldésének egyik oka a teljes dokumentumkészlet szűrése vagy értékkorlátozása lehet. For example, you want a faceting navigation structure to consist of all hotels in the index.
* A **facet** paraméter olyan navigációs szerkezetet ad vissza, amelyet továbbíthat egy felhasználói felületi vezérlőnek. Kategóriákat és egy számot ad vissza. In this case, categories are based on a field conveniently called *Category*. There is no aggregation in Azure Cognitive Search, but you can approximate aggregation via `facet`, which gives a count of documents in each category.

* A **$top=2** paraméter két dokumentumot ad vissza, így bemutatja, hogy a `top` használatával csökkentheti és növelheti is az eredményeket.

#### <a name="example-facet-on-numeric-values-searchspafacetrating"></a>Example (facet on numeric values): `search=spa&facet=Rating`

* This query is facet for rating, on a text search for *spa*. The term *Rating* can be specified as a facet because the field is marked as retrievable, filterable, and facetable in the index, and the values it contains (numeric, 1 through 5), are suitable for categorizing listings into groups.

* Csak a szűrhető mezők értéke korlátozható. Csak a lekérdezhető mezők adhatók vissza az eredményekben.

* The *Rating* field is double-precision floating point and the grouping will be by precise value. For more information on grouping by interval (for instance, "3 star ratings," "4 star ratings," etc.), see [How to implement faceted navigation in Azure Cognitive Search](https://docs.microsoft.com/azure/search/search-faceted-navigation#filter-based-on-a-range).


### <a name="highlight-query"></a> Keresési eredmények kiemelése

A találatok kiemelése a kulcsszóval megegyező szöveg formázását jelenti, feltéve, hogy vannak egyezések a megadott mezőben. Ha a keresett kifejezés egy leírás mélyén rejlik, a találatok kiemelése funkcióval könnyebben észrevehetővé teheti.

#### <a name="example-highlighter-searchbeachhighlightdescription"></a>Példa (kiemelő): `search=beach&highlight=Description`

* In this example, the formatted word *beach* is easier to spot in the description field.

#### <a name="example-linguistic-analysis-searchbeacheshighlightdescription"></a>Példa (nyelvi elemzés): `search=beaches&highlight=Description`

* Full text search recognizes basic variations in word forms. In this case, search results contain highlighted text for "beach", for hotels that have that word in their searchable fields, in response to a keyword search on "beaches". A nyelvészeti elemzés következtében ugyanazon szó különböző alakjai is megjelenhetnek az eredmények között. 

* Azure Cognitive Search supports 56 analyzers from both Lucene and Microsoft. The default used by Azure Cognitive Search is the standard Lucene analyzer.

### <a name="fuzzy-search"></a> Az intelligens keresés kipróbálása

By default, misspelled query terms, like *seatle* for "Seattle", fail to return matches in typical search. A következő példa nem ad vissza eredményt.

#### <a name="example-misspelled-term-unhandled-searchseatle"></a>Példa (elírt kifejezés, nem kezelve): `search=seatle`

A helytelenül leírt szavak kezelésére használhat intelligens keresést. Az intelligens keresés akkor érhető el, amikor a teljes Lucene lekérdezési szintaxis használja, ehhez pedig a következő két dolog szükséges: állítsa be a **queryType=full** paramétert a lekérdezésre, és fűzze hozzá a **~** utótagot a keresési sztringhez.

#### <a name="example-misspelled-term-handled-searchseatlequerytypefull"></a>Példa (elírt kifejezés, kezelve): `search=seatle~&queryType=full`

This example now returns documents that include matches on "Seattle".

Amikor a **queryType** paraméter nincs meghatározva, a rendszer az alapértelmezett egyszerű lekérdezéselemzőt használja. Ez az egyszerű lekérdezéselemző gyorsabb, de ha intelligens keresésre, reguláris kifejezésekre, közelségi keresésre vagy egyéb speciális lekérdezéstípusokra van szüksége, a teljes szintaxisra szüksége lesz.

Az intelligens keresés és a helyettesítő karakteres keresések befolyásolják a keresési eredményeket. A rendszer az ilyen formátumú kereséseken nem végez nyelvi elemzést. Before using fuzzy and wildcard search, review [How full text search works in Azure Cognitive Search](search-lucene-query-architecture.md#stage-2-lexical-analysis) and look for the section about exceptions to lexical analysis.

For more information about query scenarios enabled by the full query parser, see [Lucene query syntax in Azure Cognitive Search](https://docs.microsoft.com/rest/api/searchservice/lucene-query-syntax-in-azure-search).

### <a name="geo-search"></a> A térinformatikai keresés kipróbálása

A térinformatikai keresés az [edm.GeographyPoint adattípuson](https://docs.microsoft.com/rest/api/searchservice/supported-data-types) keresztül támogatott a koordinátákat tartalmazó mezők esetében. A geosearch egy szűrőtípus, amelynek meghatározása a [OData-szűrőszintaxis](https://docs.microsoft.com/rest/api/searchservice/odata-expression-syntax-for-azure-search) című témakörben olvasható.

#### <a name="example-geo-coordinate-filters-searchcounttruefiltergeodistancelocationgeographypoint-12212-4767-le-5"></a>Példa (geokoordináta-szűrők): `search=*&$count=true&$filter=geo.distance(Location,geography'POINT(-122.12 47.67)') le 5`

A példa lekérdezés minden eredményt szűr a helyzeti adatok alapján, és olyan eredményeket a vissza, amelyek kevesebb, mint 5 kilométerre találhatók a (szélességi és hosszúsági koordinátákként) megadott ponttól. A **$count** paraméter hozzáadásával láthatja, hány eredményt ad vissza a rendszer, amikor módosítja a távolságot vagy a koordinátákat.

A térinformatikai keresés hasznos lehet, ha a keresőalkalmazás rendelkezik „keresés a közelben” funkcióval vagy térképes navigációt használ. Ez azonban nem teljes szöveges keresés. If you have user requirements for searching on a city or country/region by name, add fields containing city or country/region names, in addition to coordinates.

## <a name="takeaways"></a>Legfontosabb ismeretek

This tutorial provided a quick introduction to Azure Cognitive Search using the Azure portal.

Megtudta, hogyan hozhat létre keresési indexet az **Adatok importálása** varázslóval. Megismerkedett az [indexelőkkel](search-indexer-overview.md), valamint az indextervezés alapvető munkafolyamatával, többek között [a közzétett indexek támogatott módosításaival](https://docs.microsoft.com/rest/api/searchservice/update-index) is.

Az Azure Portal **Keresési ablakával** megismert egyes alapvető lekérdezési szintaxisokat olyan gyakorlati példákon keresztül, amelyek kulcsfontosságú funkciókat, például a szűrést, a találatok kiemelését, az intelligens és a földrajzi keresést mutatták be.

You also learned how to find indexes, indexers, and data sources in the portal. Később bármilyen új adatforrás definícióit és mezőgyűjteményeit gyorsan és egyszerűen ellenőrizheti a portálon.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

When you're working in your own subscription, it's a good idea at the end of a project to identify whether you still need the resources you created. Resources left running can cost you money. You can delete resources individually or delete the resource group to delete the entire set of resources.

You can find and manage resources in the portal, using the **All resources** or **Resource groups** link in the left-navigation pane.

If you are using a free service, remember that you are limited to three indexes, indexers, and data sources. You can delete individual items in the portal to stay under the limit. 

## <a name="next-steps"></a>Következő lépések

While the portal is useful for initial exploration and small tasks, reviewing the APIs early on will help you understand the concepts and workflow on a deeper level:

> [!div class="nextstepaction"]
> [Create an index using .NET SDK](https://docs.microsoft.com/azure/search/search-create-index-dotnet)