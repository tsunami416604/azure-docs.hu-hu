---
title: 'Gyors útmutató: Hozzon létre, betöltését és használata az Azure portal – Azure Search-index lekérdezése'
description: Az Azure Portalon az adatok importálása varázsló segítségével létrehozása, betöltése és lekérdezése az első Azure Search-index.
author: lobrien
manager: cgronlun
tags: azure-portal
services: search
ms.service: search
ms.topic: tutorial
ms.date: 07/01/2019
ms.author: laobri
ms.custom: seodec2018
ms.openlocfilehash: 2a4d7435383f740dc386a740062e66cd2d3585b0
ms.sourcegitcommit: 66237bcd9b08359a6cce8d671f846b0c93ee6a82
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2019
ms.locfileid: "67798895"
---
# <a name="quickstart-create-an-azure-search-index-using-the-azure-portal"></a>Gyors útmutató: Az Azure portal használatával egy Azure Search-index létrehozása
> [!div class="op_single_selector"]
> * [Portál](search-get-started-portal.md)
> * [PowerShell](search-get-started-powershell.md)
> * [Postman](search-get-started-postman.md)
> * [Python](search-get-started-python.md)
> * [C#](search-get-started-dotnet.md)

Egy gyors felkészülési Azure Search-fogalmakról próbálja meg a beépített eszközök az Azure Portalon. Varázslók és a szerkesztők nem kínál a .NET és REST API-k teljes paritás, de Ön is gyorsan használatba a kódmentes bevezető érdekes-lekérdezéseket futtassanak a mintaadatok írása percen belül.

> [!div class="checklist"]
> * Indítsa el az Azure-ban üzemeltetett ingyenes nyilvános minta adatkészlet
> * Futtassa a **adatimportálás** varázsló az Azure Search-indexet hozhat létre és az adatok betöltése
> * Az indexelő folyamatot a portálon
> * Meglévő index, és azt módosítására szolgáló beállítások megtekintése
> * Ismerje meg a teljes szöveges keresés, szűrőket, értékkorlátozással, intelligens keresés és a geosearch **keresési ablak**

Ha az eszközök túl vannak korlátozza, érdemes lehet egy [kódalapú bevezetését programozási Azure search .NET-keretrendszerben](search-howto-dotnet-sdk.md) vagy [REST API-hívások a Postman](search-get-started-postman.md). Vagy megtekinthet egy 6 perces bemutatót az oktatóanyag lépéseiről. A bemutató nagyjából az [Azure Search áttekintővideójának](https://channel9.msdn.com/Events/Connect/2016/138) harmadik percénél kezdődik.

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt. 

## <a name="prerequisites"></a>Előfeltételek

[Az Azure Search szolgáltatás létrehozása](search-create-service-portal.md) vagy [keresse meg a meglévő service](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) az aktuális előfizetésben. Ebben a rövid útmutatóban egy ingyenes szolgáltatás használhatja. 

### <a name="check-for-space"></a>Szabad terület ellenőrzése

Sok ügyfél az ingyenes szolgáltatással kezdi. Ez a verzió három indexre, három adatforrásra és három indexelőre korlátozódik. Mielőtt hozzákezdene, ellenőrizze, hogy elegendő hellyel rendelkezik-e további elemek számára. Az oktatóanyagban minden objektumból egyet hozhat majd létre.

A szolgáltatás irányítópultján szakaszok bemutatják, hogy hány indexek, indexelők és adatforrások már rendelkezik. 

![Indexek, indexelők és adatforrások listája](media/search-get-started-portal/tiles-indexers-datasources.png)

## <a name="create-index"></a> Index létrehozása és az adatok betöltése

A keresési lekérdezések egy [*index*](search-what-is-an-index.md) alapján ismétlődnek, amely kereshető adatokat, metaadatokat és további szerkezeteket tartalmaz, amelyek bizonyos keresési viselkedések optimalizálásához használhatók.

Ebben az oktatóanyagban egy beépített mintaadathalmazt használunk, amely könnyedén bejárható egy [*indexelővel*](search-indexer-overview.md) az **Adatok importálása** varázslón keresztül. Az indexelők adatforrás-specifikus webbejárók, amelyek metaadatokat és tartalmakat képesek olvasni a támogatott Azure-adatforrásokból. Általában az indexelők programozott módon használják, de a portálon hozzáférhetnek azokhoz a **adatimportálás** varázsló. 

### <a name="step-1---start-the-import-data-wizard-and-create-a-data-source"></a>1\. lépés – az adatok importálása varázsló elindításához, és hozzon létre egy adatforrást

1. Az Azure Search szolgáltatás irányítópultján kattintson **adatimportálás** a parancssávon hozhat létre, és a search-index feltöltéséhez.

   ![Adatok importálása parancs](media/search-get-started-portal/import-data-cmd.png)

2. A varázslóban kattintson **csatlakozás az adatokhoz** > **minták** > **hotels-sample**. Az ehhez az adatforráshoz. A saját adatforrás hozott létre, ha egy név, típus és kapcsolatadatok megadása kell. Létrehozását követően „meglévő adatforrássá” válik, amely más importálási műveletek során ismét felhasználható.

   ![Minta adatkészlet kiválasztása](media/search-get-started-portal/import-datasource-sample.png)

3. Folytassa a következő lapra.

   ![Cognitive Search következő oldal gomb](media/search-get-started-portal/next-button-add-cog-search.png)

### <a name="step-2---skip-cognitive-skills"></a>2\. lépés – Kihagyás kognitív képességek

A varázsló támogatja az egy [kognitív képességeket folyamat](cognitive-search-concept-intro.md) számára a Cognitive Services AI-algoritmusok beépítése az indexelés. 

Azt fogjuk ezt a lépést kihagyhatja a lépést, és helyezze közvetlenül a **célindex testreszabása**.

   ![A kognitív képességek lépés kihagyása](media/search-get-started-portal/skip-cog-skill-step.png)

> [!TIP]
> Az AI-indexelő például elolvasásával egy [rövid](cognitive-search-quickstart-blob.md) vagy [oktatóanyag](cognitive-search-tutorial-blob.md).

### <a name="step-3---configure-index"></a>3\. lépés – index konfigurálása

Az index létrehozása általában egy kódalapú gyakorlatban befejeződött az adatok betöltése előtt. Azonban ez az oktatóanyag azt mutatja, ahogy a varázsló hozhat létre egy alapszintű index fel tud térképezni semmilyen adatforrás esetében. Az indexhez szükség van legalább egy névre és egy mezőgyűjteményre, amely mezők közül az egyiket a dokumentum kulcsaként kell megjelölni. Ez az egyes dokumentumok egyedi azonosítására szolgál. Ezenkívül azt is megadhatja a nyelvi elemzőket vagy javaslattevőket Ha azt szeretné, hogy az automatikus kiegészítés vagy a javasolt lekérdezések.

A mezők adattípusokkal és attribútumokkal rendelkeznek. A fent látható jelölőnégyzetek *indexattribútumok*, amelyek a mező használati módját szabályozzák.

* **Lekérhető**: azt jelenti, hogy a mező a keresési eredmények listájában jelenik meg. Keresési eredmények ki, az egyes mezők, például a mezőket csak szűrőkifejezésekben használják ezt a jelölőnégyzetet törlésével jelölheti meg.
* **Kulcs** dokumentum egyedi azonosítója. A rendszer mindig egy karakterlánc, és szükség rá.
* **Szűrhető**, **Sortable**, és **kategorizálható** meghatározása mezők használja-e egy szűrési, rendezési vagy jellemzőalapú navigációs struktúrát.
* **Kereshető**: azt jelenti, hogy a mező szerepel a teljes szöveges keresésben. A sztringek kereshetők. A numerikus és logikai mezőket gyakran nem kereshetőként jelölik meg.

Tárolási követelmények változnak az eredményeként választását. Például, ha beállította a **lekérhető** attribútum több mező alapján tárolási követelmények lép fel.

Alapértelmezés szerint a varázsló átvizsgálja a adatforrást egyedi azonosítókat keresve, amelyeket felhasználhat a kulcsmező alapjaként. *Karakterláncok* vannak megjelölve **lekérhető** és **kereshető**. *Egész számok* vannak megjelölve **lekérhető**, **Filterable**, **Sortable**, és **kategorizálható**.

1. Fogadja el az alapértelmezett beállításokat. 

   Ha újra futtatja a varázsló használatával egy "Hotels" létező adatforrást másodszor, az index nem lesz konfigurálva, az alapértelmezett attribútumok. Manuálisan válassza ki a jövőbeli import attribútumok kell. 

   ![Generált "Hotels" index](media/search-get-started-portal/hotelsindex.png)

2. Folytassa a következő lapra.

   ![Következő oldal indexelő létrehozása](media/search-get-started-portal/next-button-create-indexer.png)

### <a name="step-4---configure-indexer"></a>4\. lépés – az indexelő konfigurálása

Továbbra is az **Adatok importálása** varázslóban maradva kattintson az **Indexelő** > **Név** lehetőségre, és gépelje be az indexelő nevét.

Ez az objektum egy végrehajtható folyamatot határoz meg. Sikerült feltölteni a ismétlődő ütemezés szerint, de most használja az alapértelmezett beállítás, futtassa az indexelőt egyszer, azonnal.

Kattintson a **küldés** hozhat létre, és egyszerre is futtathatja az indexelőt.

  !["Hotels" indexelő](media/search-get-started-portal/hotels-indexer.png)

## <a name="monitor-progress"></a>A figyelő folyamatban

A varázsló nagyjából az indexelők listájának, ahol figyelemmel kísérheti folyamatban van. Önkiszolgáló navigációs sávján keresse az Áttekintés lapon, majd kattintson **indexelők**.

A portál a lap frissítése néhány percet vesz igénybe, de kell megjelennie a listában, az állapot jelzi az újonnan létrehozott indexelő "folyamatban" vagy sikeres indexelt dokumentumok számával együtt.

   ![Indexelő állapotüzenete](media/search-get-started-portal/indexers-inprogress.png)

## <a name="view-the-index"></a>Az index megtekintése

A fő szolgáltatás oldalát az Azure Search szolgáltatáshoz létrehozott erőforrásokat mutató hivatkozásokat tartalmaz.  Az újonnan létrehozott indexben megtekintéséhez kattintson **indexek** hivatkozások listájában. 

   ![A szolgáltatás irányítópultján indexek listája](media/search-get-started-portal/indexes-list.png)

Ebből a listából, kattintson a a *hotels-sample* index imént hozta létre, megtekintheti az indexsémát. és ha szükséges, hozzáadhat új mezőket. 

A **mezők** lapon látható az indexsémát. Adjon meg egy új mezőt a lista alján görgessen. A legtöbb esetben a meglévő mezők nem módosítható. A meglévő mezők fizikailag vannak jelölve az Azure Searchben, és így nem módosíthatók, még a kódban sem. Alapvető változást existující Pole, hozzon létre egy új index, az eredeti elvetését.

   ![példa indexdefinícióra](media/search-get-started-portal/sample-index-def.png)

Egyéb szerkezetek, például pontozási profilok és CORS-paraméterek, bármikor hozzáadhatók.

Annak érdekében, hogy jól átlássa az indextervezés során szerkeszthető és nem szerkeszthető elemeket, szánjon egy percet az indexdefiníciós lehetőségek áttekintésére. A kiszürkített lehetőségek azt jelzik, hogy az érték nem módosítható vagy törölhető. 

## <a name="query-index"></a> Lekérdezés a keresési ablak használatával

Továbblépve most már rendelkezünk egy keresési indexszel, amely készen áll a lekérdezésre a beépített [**Keresési ablak**](search-explorer.md) lekérdezési lappal. Ez egy keresőmezőt biztosít, amellyel tesztelheti a tetszőleges lekérdezési sztringeket.

**A keresési ablak** csak rendelkezik a [REST API-kérelmek](https://docs.microsoft.com/rest/api/searchservice/search-documents), de szintaxist is fogad el [egyszerű lekérdezési szintaxis](https://docs.microsoft.com/rest/api/searchservice/simple-query-syntax-in-azure-search) és [teljes Lucene lekérdezéselemző](https://docs.microsoft.com/rest/api/searchservice/lucene-query-syntax-in-azure-search), Ezen kívül elérhető összes keresési paramétert [Search dokumentum REST API](https://docs.microsoft.com/rest/api/searchservice/search-documents#bkmk_examples) műveleteket.

> [!TIP]
> Az [Azure Search szolgáltatás áttekintő videója](https://channel9.msdn.com/Events/Connect/2016/138) a következő lépéseket mutatja be 6 perc 8 másodperctől kezdve.
>

1. A parancssávon kattintson a **Keresési ablak** elemre.

   ![Keresési ablak parancs](media/search-get-started-portal/search-explorer-cmd.png)

2. Az a **Index** legördülő menüben válassza a *hotels-sample*. Kattintson a **API-verzió** legördülő menüben láthatja, hogy melyik REST API-k érhetők el. Az alábbi lekérdezésekhez használja az általánosan elérhető verziót (a 2019-05-06).

   ![Index és API-parancsok](media/search-get-started-portal/search-explorer-changeindex.png)

3. A keresősávba, illessze be az alábbi lekérdezési karakterláncokat, és kattintson a **keresési**.

   ![Lekérdezési karakterlánc és a Keresés gomb](media/search-get-started-portal/search-explorer-query-string-example.png)

## <a name="example-queries"></a>Példa a lekérdezésekre

Megadhat egy Binget vagy Google keresési, vagy teljesen megadott lekérdezési kifejezések előfordulhat, hogy ehhez hasonló kifejezésekkel. Eredmények a rendszer a részletes JSON-dokumentumok formájában adja vissza.

### <a name="simple-query-with-top-n-results"></a>Egyszerű lekérdezés az első N eredménnyel

#### <a name="example-string-query-searchspa"></a>(Lekérdezési karakterlánc). például: `search=spa`

* A **keresési** paraméter használható teljes szöveges kereséshez, ebben az esetben kulcsszavas keresés bevitelére azok számára, amely tartalmazza a szállodai adatvisszaadás *spa* a dokumentum bármely kereshető mezőjében.

* A **Keresési ablak** JSON-formátumban adja vissza az eredményeket, amely részletes és nehezen olvasható lehet, ha a dokumentumok sűrű szerkezettel rendelkeznek. Ez a szándékos; a teljes dokumentum betekintést fontos fejlesztési célokra, különösen a tesztelés során. A jobb felhasználói élmény érdekében olyan kódot kell írnia, amely [a keresési eredmények kezelésével](search-pagination-page-layout.md) kiemeli a fontosabb elemeket.

* A dokumentumokban minden mező „lekérdezhetőként” van jelölve az indexben. A portálon az indexattribútumok megtekintéséhez, kattintson a *hotels-sample* a a **indexek** listája.

#### <a name="example-parameterized-query-searchspacounttruetop10"></a>(A paraméteres lekérdezés). például: `search=spa&$count=true&$top=10`

* Az **&** szimbólum a keresési paraméterek összefűzésére használható, amelyek bármilyen sorrendben megadhatók.

* A **$count = true** paraméter visszaadja az összes visszaadott dokumentum teljes száma. Ez az érték a keresési eredmények elejénél található. A szűrőlekérdezések ellenőrzéséhez megfigyelheti a **$count=true** paraméter által jelentett módosításokat. A kisebb darabszámok azt jelzik, hogy a szűrő működik.

* A **$top = 10** a legmagasabb első összesen 10 dokumentumot adja vissza. Alapértelmezés szerint az Azure Search az első 50 egyezést adja vissza. A **$top** paraméter használatával növelheti vagy csökkentheti a mennyiséget.

### <a name="filter-query"></a> A lekérdezés szűrése

A **$filter** paraméter hozzáfűzésekor a szűrők megjelennek a keresési kérésekben. 

#### <a name="example-filtered-searchbeachfilterrating-gt-4"></a>Példa (szűrt): `search=beach&$filter=Rating gt 4`

* A **$filter** paraméter olyan eredményeket ad vissza, amelyek megfelelnek a megadott feltételeknek. Ebben az esetben minősítése 4-nél nagyobb.

* A szűrőszintaxis egy OData-konstrukció. További információk: [OData-szűrőszintaxis](https://docs.microsoft.com/rest/api/searchservice/odata-expression-syntax-for-azure-search).

### <a name="facet-query"></a> A lekérdezés értékkorlátozása

Az értékkorlátozó szűrők megjelennek a keresési kérésekben. A facet paraméter adja vissza azon dokumentumok összegzett darabszámát, amelyek megfelelnek a megadott értékkorlátozási értéknek.

#### <a name="example-faceted-with-scope-reduction-searchfacetcategorytop2"></a>Példa (hatókörszűkítéssel korlátozva): `search=*&facet=Category&$top=2`

* A **search=** * egy üres keresés. Az üres keresések mindenben keresnek. Az üres lekérdezések elküldésének egyik oka a teljes dokumentumkészlet szűrése vagy értékkorlátozása lehet. Ha például azt szeretné, egy értékkorlátozó navigációs szerkezet az index összes hotels áll.
* A **facet** paraméter olyan navigációs szerkezetet ad vissza, amelyet továbbíthat egy felhasználói felületi vezérlőnek. Kategóriákat és egy számot ad vissza. Ebben az esetben kategóriák alapján mezője kényelmesen *kategória*. Az Azure Searchben nincs összesítés, de megbecsülheti az összesítést a `facet` használatával, amely az egyes kategóriákban lévő dokumentumok számát adja meg.

* A **$top=2** paraméter két dokumentumot ad vissza, így bemutatja, hogy a `top` használatával csökkentheti és növelheti is az eredményeket.

#### <a name="example-facet-on-numeric-values-searchspafacetrating"></a>(Értékkorlátozás a numerikus értékek). például: `search=spa&facet=Rating`

* Ez a lekérdezés az minősítést, szöveges kereséséhez *spa*. Az előfizetési időszak *minősítés* is megadható értékkorlátozásként, mert a mező lekérdezhetőként, szűrhetőként meg van jelölve, és kategorizálható az indexben, és az értékei (numerikus, 1 – 5) alkalmasak a csoportok ingatlanok.

* Csak a szűrhető mezők értéke korlátozható. Csak a lekérdezhető mezők adhatók vissza az eredményekben.

* A *minősítés* mező kétszeres pontosságú lebegőpontos és a csoportosítási pontos érték lesz. További információ a csoportosítási időköze (például "3 csillagokkal," "4 csillagosig," stb.), lásd: [jellemzőalapú navigáció megvalósítása az Azure Search](https://docs.microsoft.com/azure/search/search-faceted-navigation#filter-based-on-a-range).


### <a name="highlight-query"></a> Keresési eredmények kiemelése

A találatok kiemelése a kulcsszóval megegyező szöveg formázását jelenti, feltéve, hogy vannak egyezések a megadott mezőben. Ha a keresett kifejezés egy leírás mélyén rejlik, a találatok kiemelése funkcióval könnyebben észrevehetővé teheti.

#### <a name="example-highlighter-searchbeachhighlightdescription"></a>Példa (kiemelő): `search=beach&highlight=Description`

* Ebben a példában a formázott szó *beach* az könnyebben észrevehető a Leírás mezőben.

#### <a name="example-linguistic-analysis-searchbeacheshighlightdescription"></a>Példa (nyelvi elemzés): `search=beaches&highlight=Description`

* A teljes szöveges keresés felismeri a word-űrlapok alapszintű változásait. Ebben az esetben a keresési eredmények tartalmazzák a kiemelt szöveget az "beach", a "Hotels" a kereshető mezőket, a keresési kulcsszó "tengerpartok" választ, amelyek a word. A nyelvészeti elemzés következtében ugyanazon szó különböző alakjai is megjelenhetnek az eredmények között. 

* Az Azure Search szolgáltatás összesen 56, a Lucene-től és Microsoft-tól származó elemzőt támogat. A szolgáltatás alapértelmezés szerint a standard Lucene-elemzőt használja.

### <a name="fuzzy-search"></a> Az intelligens keresés kipróbálása

Alapértelmezés szerint hibás lekérdezési kifejezéseket, például *seatle* a "Seattle", nem adnak vissza egyezések átlagos keresések. A következő példa nem ad vissza eredményt.

#### <a name="example-misspelled-term-unhandled-searchseatle"></a>Példa (elírt kifejezés, nem kezelve): `search=seatle`

A helytelenül leírt szavak kezelésére használhat intelligens keresést. Az intelligens keresés akkor érhető el, amikor a teljes Lucene lekérdezési szintaxis használja, ehhez pedig a következő két dolog szükséges: állítsa be a **queryType=full** paramétert a lekérdezésre, és fűzze hozzá a **~** utótagot a keresési sztringhez.

#### <a name="example-misspelled-term-handled-searchseatlequerytypefull"></a>Példa (elírt kifejezés, kezelve): `search=seatle~&queryType=full`

Ebben a példában most már megfelel a "Seattle" tartalmazó dokumentumokat ad vissza.

Amikor a **queryType** paraméter nincs meghatározva, a rendszer az alapértelmezett egyszerű lekérdezéselemzőt használja. Ez az egyszerű lekérdezéselemző gyorsabb, de ha intelligens keresésre, reguláris kifejezésekre, közelségi keresésre vagy egyéb speciális lekérdezéstípusokra van szüksége, a teljes szintaxisra szüksége lesz.

Az intelligens keresés és a helyettesítő karakteres keresések befolyásolják a keresési eredményeket. A rendszer az ilyen formátumú kereséseken nem végez nyelvi elemzést. Az intelligens és a helyettesítő karakteres keresések használata előtt tekintse át [Az Azure Search teljes szöveges keresés funkciójának működését](search-lucene-query-architecture.md#stage-2-lexical-analysis) ismertető cikknek a nyelvi elemzés kivételeivel foglalkozó szakaszát.

A teljes lekérdezéselemző által lehetővé tett lekérdezési forgatókönyvekkel kapcsolatos további információk: [Lucene lekérdezési szintaxis az Azure Search szolgáltatásban](https://docs.microsoft.com/rest/api/searchservice/lucene-query-syntax-in-azure-search).

### <a name="geo-search"></a> A térinformatikai keresés kipróbálása

A térinformatikai keresés az [edm.GeographyPoint adattípuson](https://docs.microsoft.com/rest/api/searchservice/supported-data-types) keresztül támogatott a koordinátákat tartalmazó mezők esetében. A geosearch egy szűrőtípus, amelynek meghatározása a [OData-szűrőszintaxis](https://docs.microsoft.com/rest/api/searchservice/odata-expression-syntax-for-azure-search) című témakörben olvasható.

#### <a name="example-geo-coordinate-filters-searchcounttruefiltergeodistancelocationgeographypoint-12212-4767-le-5"></a>Példa (geokoordináta-szűrők): `search=*&$count=true&$filter=geo.distance(Location,geography'POINT(-122.12 47.67)') le 5`

A példa lekérdezés minden eredményt szűr a helyzeti adatok alapján, és olyan eredményeket a vissza, amelyek kevesebb, mint 5 kilométerre találhatók a (szélességi és hosszúsági koordinátákként) megadott ponttól. A **$count** paraméter hozzáadásával láthatja, hány eredményt ad vissza a rendszer, amikor módosítja a távolságot vagy a koordinátákat.

A térinformatikai keresés hasznos lehet, ha a keresőalkalmazás rendelkezik „keresés a közelben” funkcióval vagy térképes navigációt használ. Ez azonban nem teljes szöveges keresés. Ha felhasználói követelmények keresése név alapján, város és ország/régió, város és ország/régió nevét, mellett koordinátákat tartalmazó mezők hozzáadása

## <a name="takeaways"></a>Legfontosabb ismeretek

Ebben az oktatóanyagban biztosított az Azure Search röviden bemutatja az Azure portal használatával.

Megtudta, hogyan hozhat létre keresési indexet az **Adatok importálása** varázslóval. Megismerkedett az [indexelőkkel](search-indexer-overview.md), valamint az indextervezés alapvető munkafolyamatával, többek között [a közzétett indexek támogatott módosításaival](https://docs.microsoft.com/rest/api/searchservice/update-index) is.

Az Azure Portal **Keresési ablakával** megismert egyes alapvető lekérdezési szintaxisokat olyan gyakorlati példákon keresztül, amelyek kulcsfontosságú funkciókat, például a szűrést, a találatok kiemelését, az intelligens és a földrajzi keresést mutatták be.

Azt is megtanulta, hogyan találhatja meg az indexek, indexelők és adatforrások a portálon. Később bármilyen új adatforrás definícióit és mezőgyűjteményeit gyorsan és egyszerűen ellenőrizheti a portálon.

## <a name="clean-up"></a>A fölöslegessé vált elemek eltávolítása

Dolgozik, a saját előfizetése, esetén célszerű egy projektet a végén, hogy azonosítani, hogy az erőforrások továbbra is kell-e létrehozott. Erőforrások bal oldali futó is költséget takaríthat meg költséget. Külön-külön törölje az erőforrást, vagy törölje az erőforráscsoportot törli az erőforrások teljes készletében.

Megkeresheti és kezelheti az erőforrásokat a portál használatával a **összes erőforrás** vagy **erőforráscsoportok** hivatkozásra a bal oldali navigációs ablaktáblán.

Ha használ egy ingyenes szolgáltatás, ne feledje, hogy korlátozódnak három indexek, indexelők és adatforrások. A korlátja alatt maradjunk a portál egyes elemeire törölheti. 

## <a name="next-steps"></a>További lépések

Az Azure Searchöt behatóbban is megismerheti az alábbi programozási eszközökkel:

* [-Index létrehozása .NET SDK használatával](https://docs.microsoft.com/azure/search/search-create-index-dotnet)
* [-Index létrehozása REST API-k használatával](https://docs.microsoft.com/azure/search/search-create-index-rest-api)
* [-Index létrehozása Postman vagy a Fiddlerre, és az Azure Search REST API-k használatával](search-get-started-postman.md)
