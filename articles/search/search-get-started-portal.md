---
title: Keresési index létrehozása az Azure Portalon
titleSuffix: Azure Cognitive Search
description: Ebben az Azure Portalon gyorsindítás, az Adatok importálása varázsló segítségével hozza létre, töltse be és kérdezze le az első keresési index az Azure Cognitive Search.
author: tchristiani
manager: nitinme
ms.author: terrychr
ms.service: cognitive-search
ms.topic: quickstart
ms.date: 02/10/2020
ms.openlocfilehash: 8324ca0184c508591fa4568175bad0f606f952a8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "80369456"
---
# <a name="quickstart-create-an-azure-cognitive-search-index-in-the-azure-portal"></a>Rövid útmutató: Hozzon létre egy Azure Cognitive Search indexet az Azure Portalon
> [!div class="op_single_selector"]
> * [Portál](search-get-started-portal.md)
> * [C #](search-get-started-dotnet.md)
> * [Java](search-get-started-java.md)
> * [Node.js](search-get-started-nodejs.md)
> * [Powershell](search-get-started-powershell.md)
> * [Postman](search-get-started-postman.md)
> * [Python](search-get-started-python.md)

A portál **Adatok importálása** varázslóés **a Kereséskezelő** eszközeivel gyorsan felturbózhatja a fogalmakat, és perceken belül érdekes lekérdezéseket írhat egy indexhez.

Ha az eszközök túlságosan korlátozóak, fontolja meg az [Azure Cognitive Search programozásának kódalapú bevezetését a .NET-ben,](search-howto-dotnet-sdk.md) vagy használja [a Postmant REST API-hívások hoz.](search-get-started-postman.md) 

Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) mielőtt elkezdené. 

## <a name="prerequisites"></a>Előfeltételek

[Hozzon létre egy Azure Cognitive Search szolgáltatást,](search-create-service-portal.md) vagy [keressen egy meglévő szolgáltatást](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) a jelenlegi előfizetése alatt. A rövid útmutatóhoz ingyenes szolgáltatást használhat. 

### <a name="check-for-space"></a>Szabad terület ellenőrzése

Sok ügyfél az ingyenes szolgáltatással kezdi. Ez a verzió három indexre, három adatforrásra és három indexelőre korlátozódik. Mielőtt hozzákezdene, ellenőrizze, hogy elegendő hellyel rendelkezik-e további elemek számára. Az oktatóanyagban minden objektumból egyet hozhat majd létre.

A szolgáltatás irányítópultján található szakaszok azt mutatják, hogy hány indexelés, indexelő és adatforrás már rendelkezik. 

![Indexek, indexelők és adatforrások listája](media/search-get-started-portal/tiles-indexers-datasources.png)

## <a name="create-an-index-and-load-data"></a><a name="create-index"></a> Index létrehozása és az adatok betöltése

A keresési lekérdezések egy [*index*](search-what-is-an-index.md) alapján ismétlődnek, amely kereshető adatokat, metaadatokat és további szerkezeteket tartalmaz, amelyek bizonyos keresési viselkedések optimalizálásához használhatók.

Ebben az oktatóanyagban egy beépített mintaadatkészletet használunk, amely az [ **Adatok importálása** varázslón](search-import-data-portal.md)keresztül egy [*indexelő*](search-indexer-overview.md) vel bejárható. Az indexelők adatforrás-specifikus webbejárók, amelyek metaadatokat és tartalmakat képesek olvasni a támogatott Azure-adatforrásokból. Az indexelők et általában programozott módon használják, de a portálon az **Adatok importálása** varázslón keresztül érheti el őket. 

### <a name="step-1---start-the-import-data-wizard-and-create-a-data-source"></a>1. lépés – Az Adatok importálása varázsló indítása és adatforrás létrehozása

1. Az Azure Cognitive Search szolgáltatás irányítópultján kattintson az **Adatok importálása** a parancssávon egy keresési index létrehozásához és feltöltéséhez.

   ![Adatok importálása parancs](media/search-get-started-portal/import-data-cmd.png)

2. A varázslóban kattintson a **Csatlakozás az adatokhoz** > **Minta** > **hotelek-minta gombra.** Ez az adatforrás be van építve. Ha saját adatforrást hoz létre, meg kell adnia egy nevet, típust és kapcsolati információt. Létrehozását követően „meglévő adatforrássá” válik, amely más importálási műveletek során ismét felhasználható.

   ![Minta adatkészlet kiválasztása](media/search-get-started-portal/import-datasource-sample.png)

3. Folytassa a következő lappal.

### <a name="step-2---skip-the-enrich-content-page"></a>2. lépés - A "Tartalom gazdagítása" oldal kihagyása

A varázsló támogatja egy [AI-dúsítási folyamat](cognitive-search-concept-intro.md) létrehozását a Cognitive Services AI-algoritmusok indexelésbe való beépítéséhez. 

Egyelőre kihagyjuk ezt a lépést, és közvetlenül a **Célindex testreszabása**elemre lépünk.

   ![A kognitív képességek lépés kihagyása](media/search-get-started-portal/skip-cog-skill-step.png)

> [!TIP]
> Egy rövid útmutatóban vagy [oktatóanyagban](cognitive-search-tutorial-blob.md)végigléphet egy AI-indexelési [példán.](cognitive-search-quickstart-blob.md)

### <a name="step-3---configure-index"></a>3. lépés - Index konfigurálása

Az index létrehozása általában egy kódalapú gyakorlat, amely az adatok betöltése előtt fejeződött be. Azonban, ahogy ez az oktatóanyag is jelzi, a varázsló alapindexet hozhat létre bármely feltérképezhető adatforráshoz. Az indexhez szükség van legalább egy névre és egy mezőgyűjteményre, amely mezők közül az egyiket a dokumentum kulcsaként kell megjelölni. Ez az egyes dokumentumok egyedi azonosítására szolgál. Ezenkívül megadhatja a nyelvi elemzők et vagy javaslatajánlókat, ha automatikus kiegészítést vagy javasolt lekérdezéseket szeretne.

A mezők adattípusokkal és attribútumokkal rendelkeznek. A fent látható jelölőnégyzetek *indexattribútumok*, amelyek a mező használati módját szabályozzák.

* **Lekérhető**: azt jelenti, hogy a mező a keresési eredmények listájában jelenik meg. Az egyes mezőket a keresési eredmények tiltott területként jelölheti meg, ha törli ezt a jelölőnégyzetet, például a csak szűrőkifejezésekben használt mezők esetében.
* **A** kulcs az egyedi dokumentumazonosító. Ez mindig egy húr, és szükség van rá.
* **A szűrhető**, **a Rendezés és**a **Facetable** határozza meg, hogy a mezőkszűrőben, rendezésben vagy felületalapú navigációs struktúrában legyenek-e használatban.
* **Kereshető**: azt jelenti, hogy a mező szerepel a teljes szöveges keresésben. A sztringek kereshetők. A numerikus és logikai mezőket gyakran nem kereshetőként jelölik meg.

A tárolási követelmények nem változnak a választás következtében. Ha például több mezőben állítja be a **Visszakereshető** attribútumot, a tárolási követelmények nem lépnek fel.

Alapértelmezés szerint a varázsló átvizsgálja a adatforrást egyedi azonosítókat keresve, amelyeket felhasználhat a kulcsmező alapjaként. *A karakterláncok* **visszakereshetőek** és **kereshetőek.** *Az egész számok* **beolvasható**, **szűrhető**, **rendezhető**és **facetable hozzárendelésűek.**

1. Fogadja el az alapértelmezett beállításokat. 

   Ha a varázslót másodszor is újrafuttatja egy meglévő szállodai adatforrás használatával, az index nem lesz alapértelmezett attribútumokkal konfigurálva. A jövőbeli importálások attribútumait manuálisan kell kiválasztania. 

   ![Generált szállodák indexe](media/search-get-started-portal/hotelsindex.png)

2. Folytassa a következő lappal.


### <a name="step-4---configure-indexer"></a>4. lépés - Indexelő konfigurálása

Továbbra is az **Adatok importálása** varázslóban maradva kattintson az **Indexelő** > **Név** lehetőségre, és gépelje be az indexelő nevét.

Ez az objektum egy végrehajtható folyamatot határoz meg. Lehet, hogy azt az ismétlődő ütemezés, de most használja az alapértelmezett beállítást, hogy futtassa az indexelő egyszer, azonnal.

Kattintson **a Küldés** gombra az indexelő létrehozásához és egyidejű futtatásához.

  ![hotelindexelő](media/search-get-started-portal/hotels-indexer.png)

## <a name="monitor-progress"></a>A folyamat figyelése

A varázslónak az Indexelők listájához kell elvinnie, ahol figyelemmel kísérheti a folyamatot. Az önnavigációhoz lépjen az Áttekintés lapra, és kattintson az **Indexelők gombra.**

Eltarthat néhány percig, amíg a portál frissíti a lapot, de az újonnan létrehozott indexelőnek a listában kell lennie, az állapot "folyamatban" vagy sikeres, az indexelt dokumentumok számával együtt.

   ![Indexelő állapotüzenete](media/search-get-started-portal/indexers-inprogress.png)

## <a name="view-the-index"></a>Az index megtekintése

A fő szolgáltatáslap az Azure Cognitive Search szolgáltatásban létrehozott erőforrásokra mutató hivatkozásokat tartalmaz.  Az imént létrehozott index megtekintéséhez kattintson a hivatkozások listájában az **Indexek** elemre. 

Várja meg, amíg a portállap frissül. Néhány perc múlva meg kell jelennie az index a dokumentum száma és a tárolási méret.

   ![Indexek listája a szolgáltatás irányítópultján](media/search-get-started-portal/indexes-list.png)

Ebből a listából rákattinthat az imént létrehozott *szállodaminta* indexre, megtekintheti az indexsémát. és szükség esetén új mezőket is hozzáadhat. 

A **Mezők** lapon látható az indexséma. Görgessen a lista aljára, és írjon be egy új mezőt. A legtöbb esetben nem módosíthatja a meglévő mezőket. A meglévő mezők fizikai reprezentációval rendelkeznek az Azure Cognitive Search-ben, és így nem módosíthatók, még kódban sem. Meglévő mező alapvető módosításához hozzon létre egy új indexet, és dobja ki az eredetit.

   ![példa indexdefinícióra](media/search-get-started-portal/sample-index-def.png)

Egyéb szerkezetek, például pontozási profilok és CORS-paraméterek, bármikor hozzáadhatók.

Annak érdekében, hogy jól átlássa az indextervezés során szerkeszthető és nem szerkeszthető elemeket, szánjon egy percet az indexdefiníciós lehetőségek áttekintésére. A kiszürkített lehetőségek azt jelzik, hogy az érték nem módosítható vagy törölhető. 

## <a name="query-using-search-explorer"></a><a name="query-index"></a>Lekérdezés a Kereséskezelővel

Továbblépve most már rendelkezünk egy keresési indexszel, amely készen áll a lekérdezésre a beépített [**Keresési ablak**](search-explorer.md) lekérdezési lappal. Ez egy keresőmezőt biztosít, amellyel tesztelheti a tetszőleges lekérdezési sztringeket.

**A kereséskezelő** csak a [REST API-kérelmek](https://docs.microsoft.com/rest/api/searchservice/search-documents)kezelésére van felszerelve, de elfogadja az [egyszerű lekérdezésszintaxisés](https://docs.microsoft.com/rest/api/searchservice/simple-query-syntax-in-azure-search) a [teljes Lucene-lekérdezéselemző szintaxisát,](https://docs.microsoft.com/rest/api/searchservice/lucene-query-syntax-in-azure-search)valamint a [Search Document REST API-műveletekben](https://docs.microsoft.com/rest/api/searchservice/search-documents#bkmk_examples) elérhető összes keresési paramétert.

> [!TIP]
> A következő lépéseket 6m08s-on mutatja be az [Azure Cognitive Search Overview videóban.](https://channel9.msdn.com/Events/Connect/2016/138)
>

1. A parancssávon kattintson a **Keresési ablak** elemre.

   ![Keresési ablak parancs](media/search-get-started-portal/search-explorer-cmd.png)

2. Az **Index** legördülő menüben válassza a *hotels-sample-index*lehetőséget. Kattintson az **API-verzió** legördülő menüre, hogy megtekintse, mely REST API-k érhetők el. Az alábbi lekérdezések esetén használja az általánosan elérhető verziót (2019-05-06).

   ![Index és API-parancsok](media/search-get-started-portal/search-explorer-changeindex.png)

3. A keresősávba illessze be az alábbi lekérdezési karakterláncokat, és kattintson a **Keresés**gombra.

   ![Lekérdezési karakterlánc és keresés gomb](media/search-get-started-portal/search-explorer-query-string-example.png)

## <a name="example-queries"></a>Példa lekérdezésekre

A Bing- vagy Google-keresésben vagy a teljesen megadott lekérdezési kifejezésekben használt kifejezésekhez hasonlóan kifejezéseket és kifejezéseket is megadhat. Az eredmények részletes JSON-dokumentumokként kerülnek visszaadásra.

### <a name="simple-query-with-top-n-results"></a>Egyszerű lekérdezés az első N eredménnyel

#### <a name="example-string-query-searchspa"></a>Példa (karakterlánc-lekérdezés):`search=spa`

* A **keresési** paraméter segítségével a teljes szöveges kereséshez kulcsszókeresést, ebben az esetben a *spa-t* tartalmazók számára a dokumentum bármely kereshető mezőjében szereplő információkat adja vissza.

* A **Keresési ablak** JSON-formátumban adja vissza az eredményeket, amely részletes és nehezen olvasható lehet, ha a dokumentumok sűrű szerkezettel rendelkeznek. Ez szándékos; a teljes dokumentum ravaló láthatósága fontos a fejlesztési célok, különösen a tesztelés során. A jobb felhasználói élmény érdekében olyan kódot kell írnia, amely [a keresési eredmények kezelésével](search-pagination-page-layout.md) kiemeli a fontosabb elemeket.

* A dokumentumokban minden mező „lekérdezhetőként” van jelölve az indexben. A portál indexattribútumainak megtekintéséhez kattintson a *hotelek mintájára* az **Indexek** listában.

#### <a name="example-parameterized-query-searchspacounttruetop10"></a>Példa (paraméterezett lekérdezés):`search=spa&$count=true&$top=10`

* A **&** szimbólum a keresési paraméterek hozzáfűzésére szolgál, amelyek bármilyen sorrendben megadhatók.

* A **$count=true** paraméter az összes visszaadott dokumentum teljes számát adja vissza. Ez az érték a keresési eredmények elejénél található. A szűrőlekérdezések ellenőrzéséhez megfigyelheti a **$count=true** paraméter által jelentett módosításokat. A kisebb darabszámok azt jelzik, hogy a szűrő működik.

* A **$top=10** a legmagasabban rangsorolt 10 dokumentumot adja vissza az összes dokumentumközül. Alapértelmezés szerint az Azure Cognitive Search az első 50 legjobb egyezést adja vissza. A **$top** paraméter használatával növelheti vagy csökkentheti a mennyiséget.

### <a name="filter-the-query"></a><a name="filter-query"></a> A lekérdezés szűrése

A **$filter** paraméter hozzáfűzésekor a szűrők megjelennek a keresési kérésekben. 

#### <a name="example-filtered-searchbeachfilterrating-gt-4"></a>Példa (szűrt): `search=beach&$filter=Rating gt 4`

* A **$filter** paraméter olyan eredményeket ad vissza, amelyek megfelelnek a megadott feltételeknek. Ebben az esetben a 4-nél nagyobb minősítések.

* A szűrőszintaxis egy OData-konstrukció. További információk: [OData-szűrőszintaxis](https://docs.microsoft.com/rest/api/searchservice/odata-expression-syntax-for-azure-search).

### <a name="facet-the-query"></a><a name="facet-query"></a> A lekérdezés értékkorlátozása

Az értékkorlátozó szűrők megjelennek a keresési kérésekben. A facet paraméter adja vissza azon dokumentumok összegzett darabszámát, amelyek megfelelnek a megadott értékkorlátozási értéknek.

#### <a name="example-faceted-with-scope-reduction-searchfacetcategorytop2"></a>Példa (hatókörszűkítéssel korlátozva): `search=*&facet=Category&$top=2`

* A **search=*** egy üres keresés. Az üres keresések mindenben keresnek. Az üres lekérdezések elküldésének egyik oka a teljes dokumentumkészlet szűrése vagy értékkorlátozása lehet. Például azt szeretné, hogy egy faceting navigációs struktúra az indexben lévő összes szállodából álljon.
* A **facet** paraméter olyan navigációs szerkezetet ad vissza, amelyet továbbíthat egy felhasználói felületi vezérlőnek. Kategóriákat és egy számot ad vissza. Ebben az esetben a kategóriák a *Kategória*nevű mezőn alapulnak. Nincs összesítés az Azure Cognitive Search, de megközelítheti az `facet`összesítés keresztül, amely az egyes kategóriákban a dokumentumok számát adja.

* A **$top=2** paraméter két dokumentumot ad vissza, így bemutatja, hogy a `top` használatával csökkentheti és növelheti is az eredményeket.

#### <a name="example-facet-on-numeric-values-searchspafacetrating"></a>Példa (a numerikus értékekre korlátozva): `search=spa&facet=Rating`

* Ez a lekérdezés a minősítés aspektusa, a *spa*szöveges keresésére. Az *Értékelés* kifejezés megadható lapként, mert a mező visszakereshetőként, szűrhetőként és facetable-ként van megjelölve az indexben, és a benne lévő értékek (numerikus, 1-től 5-ig) alkalmasak a listák csoportokba sorolására.

* Csak a szűrhető mezők értéke korlátozható. Csak a lekérdezhető mezők adhatók vissza az eredményekben.

* A *Minősítés* mező dupla pontosságú lebegőpontos, és a csoportosítás pontos értékszerint történik. Az intervallumok közötti csoportosításról további információt (például "3 csillagos minősítés", "4 csillagos minősítések" stb.) talál: [Faceted navigation az Azure Cognitive Search](https://docs.microsoft.com/azure/search/search-faceted-navigation#filter-based-on-a-range)alkalmazásban.


### <a name="highlight-search-results"></a><a name="highlight-query"></a> Keresési eredmények kiemelése

A találatok kiemelése a kulcsszóval megegyező szöveg formázását jelenti, feltéve, hogy vannak egyezések a megadott mezőben. Ha a keresett kifejezés egy leírás mélyén rejlik, a találatok kiemelése funkcióval könnyebben észrevehetővé teheti.

#### <a name="example-highlighter-searchbeachhighlightdescription"></a>Példa (kiemelő): `search=beach&highlight=Description`

* Ebben a példában a formázott szó *strand* könnyebb észrevenni a leírás mezőben.

#### <a name="example-linguistic-analysis-searchbeacheshighlightdescription"></a>Példa (nyelvi elemzés): `search=beaches&highlight=Description`

* A teljes szöveges keresés felismeri a szóűrlapok alapvető változatait. Ebben az esetben a keresési eredmények a "strand" kiemelt szövegét tartalmazzák, azon szállodák esetében, amelyek kereshető mezőikben ezt a szót tartalmazzák, válaszul a "strandok" kulcsszókeresésére. A nyelvészeti elemzés következtében ugyanazon szó különböző alakjai is megjelenhetnek az eredmények között. 

* Az Azure Cognitive Search 56 elemzőt támogat a Lucene-től és a Microsofttól. Az Azure Cognitive Search által használt alapértelmezett a standard Lucene-elemző.

### <a name="try-fuzzy-search"></a><a name="fuzzy-search"></a> Az intelligens keresés kipróbálása

Alapértelmezés szerint a hibásan írt lekérdezési kifejezések, például a "Seattle" *leírása* nem ad vissza egyezéseket a szokásos keresésben. A következő példa nem ad vissza eredményt.

#### <a name="example-misspelled-term-unhandled-searchseatle"></a>Példa (elírt kifejezés, nem kezelve): `search=seatle`

A helytelenül leírt szavak kezelésére használhat intelligens keresést. Az intelligens keresés akkor érhető el, amikor a teljes Lucene lekérdezési szintaxis használja, ehhez pedig a következő két dolog szükséges: állítsa be a **queryType=full** paramétert a lekérdezésre, és fűzze hozzá a **~** utótagot a keresési sztringhez.

#### <a name="example-misspelled-term-handled-searchseatlequerytypefull"></a>Példa (elírt kifejezés, kezelve): `search=seatle~&queryType=full`

Ez a példa most visszaadja azokat a dokumentumokat, amelyek a "Seattle" egyezéseit tartalmazzák.

Amikor a **queryType** paraméter nincs meghatározva, a rendszer az alapértelmezett egyszerű lekérdezéselemzőt használja. Ez az egyszerű lekérdezéselemző gyorsabb, de ha intelligens keresésre, reguláris kifejezésekre, közelségi keresésre vagy egyéb speciális lekérdezéstípusokra van szüksége, a teljes szintaxisra szüksége lesz.

Az intelligens keresés és a helyettesítő karakteres keresések befolyásolják a keresési eredményeket. A rendszer az ilyen formátumú kereséseken nem végez nyelvi elemzést. Az intelligens és helyettesítő karakteres keresés használata előtt tekintse át a Teljes szöveges keresés működését az [Azure Cognitive Search alkalmazásban,](search-lucene-query-architecture.md#stage-2-lexical-analysis) és keresse meg a lexikális elemzés alóli kivételekről szóló szakaszt.

A teljes lekérdezéselemző által engedélyezett lekérdezési forgatókönyvekről további információt a [Lucene-lekérdezés szintaxisa az Azure Cognitive Search című témakörben talál.](https://docs.microsoft.com/rest/api/searchservice/lucene-query-syntax-in-azure-search)

### <a name="try-geospatial-search"></a><a name="geo-search"></a> A térinformatikai keresés kipróbálása

 A térinformatikai keresés az [edm.GeographyPoint adattípuson](https://docs.microsoft.com/rest/api/searchservice/supported-data-types) keresztül támogatott a koordinátákat tartalmazó mezők esetében. A geosearch egy szűrőtípus, amelynek meghatározása a [OData-szűrőszintaxis](https://docs.microsoft.com/rest/api/searchservice/odata-expression-syntax-for-azure-search) című témakörben olvasható.

#### <a name="example-geo-coordinate-filters-searchcounttruefiltergeodistancelocationgeographypoint-12212-4767-le-5"></a>Példa (geokoordináta-szűrők): `search=*&$count=true&$filter=geo.distance(Location,geography'POINT(-122.12 47.67)') le 5`

A példa lekérdezés minden eredményt szűr a helyzeti adatok alapján, és olyan eredményeket a vissza, amelyek kevesebb, mint 5 kilométerre találhatók a (szélességi és hosszúsági koordinátákként) megadott ponttól. A **$count** paraméter hozzáadásával láthatja, hány eredményt ad vissza a rendszer, amikor módosítja a távolságot vagy a koordinátákat.

A térinformatikai keresés hasznos lehet, ha a keresőalkalmazás rendelkezik „keresés a közelben” funkcióval vagy térképes navigációt használ. Ez azonban nem teljes szöveges keresés. Ha felhasználói követelményei vannak egy város vagy ország/régió név szerint történő kereséséhez, a koordináták mellett adja meg a város- vagy ország-/régióneveket tartalmazó mezőket is.

## <a name="takeaways"></a>Legfontosabb ismeretek

Ez az oktatóanyag rövid útmutatót nyújt az Azure Cognitive Search az Azure Portalhasználatával.

Megtudta, hogyan hozhat létre keresési indexet az **Adatok importálása** varázslóval. Megismerkedett az [indexelőkkel](search-indexer-overview.md), valamint az indextervezés alapvető munkafolyamatával, többek között [a közzétett indexek támogatott módosításaival](https://docs.microsoft.com/rest/api/searchservice/update-index) is.

Az Azure Portal **Keresési ablakával** megismert egyes alapvető lekérdezési szintaxisokat olyan gyakorlati példákon keresztül, amelyek kulcsfontosságú funkciókat, például a szűrést, a találatok kiemelését, az intelligens és a földrajzi keresést mutatták be.

Azt is megtanulta, hogyan kereshetindexeket, indexelőket és adatforrásokat a portálon. Később bármilyen új adatforrás definícióit és mezőgyűjteményeit gyorsan és egyszerűen ellenőrizheti a portálon.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha a saját előfizetésében dolgozik, érdemes az egyes projektek végén eldöntenie, hogy szüksége lesz-e még a létrehozott erőforrásokra. A továbbra is futó erőforrások költségekkel járhatnak. Az erőforrások egyesével is törölhetők, de az erőforráscsoport törlésével egyszerre eltávolítható az összes erőforrás is.

Az erőforrásokat a portálon keresheti meg és kezelheti a bal oldali navigációs ablak **Minden erőforrás** vagy **Erőforráscsoport** hivatkozásával.

Ha ingyenes szolgáltatást használ, ne feledje, hogy három indexelésre, indexelőre és adatforrásra van korlátozva. Törölheti az egyes elemeket a portálon, hogy a korlát alatt maradjon. 

## <a name="next-steps"></a>További lépések

Portálvarázsló használatával böngészőben futó, használatra kész webalkalmazást hozhat létre. Kipróbálhatja ezt a varázslót az imént létrehozott kis indexen, vagy használhatja a beépített mintaadatkészletek egyikét a gazdagabb keresési élményhez.

> [!div class="nextstepaction"]
> [Keresési alkalmazás létrehozása a portálon](search-create-app-portal.md)