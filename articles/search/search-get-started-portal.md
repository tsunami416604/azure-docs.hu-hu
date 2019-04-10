---
title: 'Gyors útmutató: Hozzon létre, betöltését és az Azure portal – Azure Search-index lekérdezése'
description: Hozhat létre, és az első beépített mintaadatok és az adatok importálása varázsló használata az Azure Portalon az Azure Search-index lekérdezése.
author: HeidiSteen
manager: cgronlun
tags: azure-portal
services: search
ms.service: search
ms.topic: tutorial
ms.date: 04/08/2019
ms.author: heidist
ms.custom: seodec2018
ms.openlocfilehash: a1cf72d9e3f5c2c6e919304d4d886a607c54f359
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/08/2019
ms.locfileid: "59282661"
---
# <a name="quickstart-use-built-in-portal-tools-for-azure-search-import-indexing-and-queries"></a>Gyors útmutató: Az Azure Search-importálási, az indexelés és a lekérdezések beépített portal-eszközök használata
> [!div class="op_single_selector"]
> * [Portál](search-get-started-portal.md)
> * [PowerShell](search-howto-dotnet-sdk.md)
> * [Postman](search-fiddler.md)
> * [C#](search-create-index-dotnet.md)
>*

Egy gyors felkészülési Azure Search-fogalmakról próbálja meg a beépített eszközök az Azure Portalon. Varázslók és a szerkesztők nem kínál a .NET és REST API-k teljes paritás, de Ön is gyorsan használatba a kódmentes bevezető érdekes-lekérdezéseket futtassanak a mintaadatok írása percen belül.

> [!div class="checklist"]
> * Indítsa el az Azure-ban üzemeltetett ingyenes nyilvános minta adatkészlet
> * Futtassa a **adatimportálás** varázsló az Azure Search-indexet hozhat létre és az adatok betöltése
> * Az indexelő folyamatot a portálon
> * Meglévő index, és azt módosítására szolgáló beállítások megtekintése
> * Ismerje meg a teljes szöveges keresés, szűrőket, értékkorlátozással, intelligens keresés és a geosearch **keresési ablak**

Ha az eszközök túl vannak korlátozza, érdemes lehet egy [kódalapú bevezetését programozási Azure search .NET-keretrendszerben](search-howto-dotnet-sdk.md) vagy [Postman vagy a REST API-hívások Fiddler](search-fiddler.md).

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt. Vagy megtekinthet egy 6 perces bemutatót az oktatóanyag lépéseiről. A bemutató nagyjából az [Azure Search áttekintővideójának](https://channel9.msdn.com/Events/Connect/2016/138) harmadik percénél kezdődik.

## <a name="prerequisites"></a>Előfeltételek

[Az Azure Search szolgáltatás létrehozása](search-create-service-portal.md) vagy [keresse meg a meglévő service](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) az aktuális előfizetésben. Ebben a rövid útmutatóban egy ingyenes szolgáltatás használhatja. 

### <a name="check-for-space"></a>Szabad terület ellenőrzése

Sok ügyfél az ingyenes szolgáltatással kezdi. Ez a verzió három indexre, három adatforrásra és három indexelőre korlátozódik. Mielőtt hozzákezdene, ellenőrizze, hogy elegendő hellyel rendelkezik-e további elemek számára. Az oktatóanyagban minden objektumból egyet hozhat majd létre.

A szolgáltatás irányítópultján szakaszok bemutatják, hogy hány indexek, indexelők és adatforrások már rendelkezik. 

![Indexek, indexelők és adatforrások listája](media/search-get-started-portal/tiles-indexers-datasources2.png)

## <a name="create-index"></a> Index létrehozása és az adatok betöltése

A keresési lekérdezések egy [*index*](search-what-is-an-index.md) alapján ismétlődnek, amely kereshető adatokat, metaadatokat és további szerkezeteket tartalmaz, amelyek bizonyos keresési viselkedések optimalizálásához használhatók.

Ebben az oktatóanyagban egy beépített mintaadathalmazt használunk, amely könnyedén bejárható egy [*indexelővel*](search-indexer-overview.md) az **Adatok importálása** varázslón keresztül. Az indexelők adatforrás-specifikus webbejárók, amelyek metaadatokat és tartalmakat képesek olvasni a támogatott Azure-adatforrásokból. Általában az indexelők programozott módon használják, de a portálon hozzáférhetnek azokhoz a **adatimportálás** varázsló. 

### <a name="step-1---start-the-import-data-wizard-and-create-a-data-source"></a>1. lépés – az adatok importálása varázsló elindításához, és hozzon létre egy adatforrást

1. Az Azure Search szolgáltatás irányítópultján kattintson **adatimportálás** a parancssávon hozhat létre, és a search-index feltöltéséhez.

   ![Adatok importálása parancs](media/search-get-started-portal/import-data-cmd2.png)

2. A varázslóban kattintson a **Csatlakozás az adatokhoz** > **Minták** > **realestate-us-sample** elemre. Az ehhez az adatforráshoz. A saját adatforrás hozott létre, ha egy név, típus és kapcsolatadatok megadása kell. Létrehozását követően „meglévő adatforrássá” válik, amely más importálási műveletek során ismét felhasználható.

   ![Minta adatkészlet kiválasztása](media/search-get-started-portal/import-datasource-sample2.png)

3. Folytassa a következő lapra.

   ![Cognitive Search következő oldal gomb](media/search-get-started-portal/next-button-add-cog-search.png)

### <a name="step-2---skip-cognitive-skills"></a>2. lépés – Kihagyás kognitív képességek

A varázsló támogatja az egy [kognitív képességeket folyamat](cognitive-search-concept-intro.md) számára a Cognitive Services AI-algoritmusok beépítése az indexelés. 

Azt fogjuk ezt a lépést kihagyhatja a lépést, és helyezze közvetlenül a **célindex testreszabása**.

   ![A kognitív képességek lépés kihagyása](media/search-get-started-portal/skip-cog-skill-step.png)

> [!TIP]
> Kipróbálhatja az Azure Search előzetes verzióban elérhető új kognitív keresési szolgáltatását is a [kognitív keresés rövid útmutatójában](cognitive-search-quickstart-blob.md) vagy az [oktatóanyagban](cognitive-search-tutorial-blob.md).

### <a name="step-3---configure-index"></a>3. lépés – index konfigurálása

Az index létrehozása általában egy kódalapú gyakorlatban befejeződött az adatok betöltése előtt. Azonban ez az oktatóanyag azt mutatja, ahogy a varázsló hozhat létre egy alapszintű index fel tud térképezni semmilyen adatforrás esetében. Az indexhez szükség van legalább egy névre és egy mezőgyűjteményre, amely mezők közül az egyiket a dokumentum kulcsaként kell megjelölni. Ez az egyes dokumentumok egyedi azonosítására szolgál. Ezenkívül azt is megadhatja a nyelvi elemzőket vagy javaslattevőket Ha azt szeretné, hogy az automatikus kiegészítés vagy a javasolt lekérdezések.

A mezők adattípusokkal és attribútumokkal rendelkeznek. A fent látható jelölőnégyzetek *indexattribútumok*, amelyek a mező használati módját szabályozzák.

* **Lekérhető**: azt jelenti, hogy a mező a keresési eredmények listájában jelenik meg. A jelölőnégyzet törlésével az egyes mezőket a keresési eredmények korlátjain kívül esőként jelölheti meg, például amikor a mezőket csak szűrőkifejezésekben használják.
* **Kulcs** dokumentum egyedi azonosítója. A rendszer mindig egy karakterlánc, és szükség rá.
* **Szűrhető**, **Sortable**, és **kategorizálható** meghatározása mezők használja-e egy szűrési, rendezési vagy jellemzőalapú navigációs struktúrát.
* **Kereshető**: azt jelenti, hogy a mező szerepel a teljes szöveges keresésben. A sztringek kereshetők. A numerikus és logikai mezőket gyakran nem kereshetőként jelölik meg.

Tárolási követelmények változnak az eredményeként választását. Például, ha beállította a **lekérhető** attribútum több mező alapján tárolási követelmények lép fel.

Alapértelmezés szerint a varázsló átvizsgálja a adatforrást egyedi azonosítókat keresve, amelyeket felhasználhat a kulcsmező alapjaként. *Karakterláncok* vannak megjelölve **lekérhető** és **kereshető**. *Egész számok* vannak megjelölve **lekérhető**, **Filterable**, **Sortable**, és **kategorizálható**.

1. Fogadja el az alapértelmezett beállításokat. 

   Ha újra futtatja a varázsló használatával realestate meglévő adatforrás másodszor, az index nem lesz konfigurálva, az alapértelmezett attribútumok. Manuálisan válassza ki a jövőbeli import attribútumok kell.

   ![Létrehozott ingatlanindex](media/search-get-started-portal/realestateindex2.png)

2. Folytassa a következő lapra.

   ![Következő oldal indexelő létrehozása](media/search-get-started-portal/next-button-create-indexer.png)

### <a name="step-4---configure-indexer"></a>4. lépés – az indexelő konfigurálása

Továbbra is az **Adatok importálása** varázslóban maradva kattintson az **Indexelő** > **Név** lehetőségre, és gépelje be az indexelő nevét.

Ez az objektum egy végrehajtható folyamatot határoz meg. Sikerült feltölteni a ismétlődő ütemezés szerint, de most használja az alapértelmezett beállítás, futtassa az indexelőt egyszer, azonnal.

Kattintson a **küldés** hozhat létre, és egyszerre is futtathatja az indexelőt.

  ![ingatlanindexelő](media/search-get-started-portal/realestate-indexer2.png)

## <a name="monitor-progress"></a>A figyelő folyamatban

A varázsló nagyjából az indexelők listájának, ahol figyelemmel kísérheti folyamatban van. Önkiszolgáló navigációs sávján keresse az Áttekintés lapon, majd kattintson **indexelők**.

A portál a lap frissítése néhány percet vesz igénybe, de kell megjelennie a listában, az állapot jelzi az újonnan létrehozott indexelő "folyamatban" vagy sikeres indexelt dokumentumok számával együtt.

   ![Indexelő állapotüzenete](media/search-get-started-portal/indexers-inprogress2.png)

## <a name="view-the-index"></a>Az index megtekintése

A fő szolgáltatás oldalát az Azure Search szolgáltatáshoz létrehozott erőforrásokat mutató hivatkozásokat tartalmaz.  Az újonnan létrehozott indexben megtekintéséhez kattintson **indexek** hivatkozások listájában. 

   ![A szolgáltatás irányítópultján indexek listája](media/search-get-started-portal/indexes-list.png)

Ebből a listából, kattintson a a *realestate-us-sample* index imént hozta létre, megtekintheti az indexsémát. és ha szükséges, hozzáadhat új mezőket. 

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

   ![Keresési ablak parancs](media/search-get-started-portal/search-explorer-cmd2.png)

2. Kattintson a parancssáv **Index módosítása** elemére a *realestate-us-sample* indexre való váltáshoz. Kattintson a parancssáv **API-verzió beállítása** elemére az elérhető REST API-k megtekintéséhez. Az alábbi lekérdezésekhez használja az általánosan elérhető verziót (2017-11-11).

   ![Index és API-parancsok](media/search-get-started-portal/search-explorer-changeindex-se2.png)

3. A keresősávba, illessze be az alábbi lekérdezési karakterláncokat, és kattintson a **keresési**.

   ![Lekérdezési karakterlánc és a Keresés gomb](media/search-get-started-portal/search-explorer-query-string-example.png)

## <a name="example-queries"></a>Példa a lekérdezésekre

Megadhat egy Binget vagy Google keresési, vagy teljesen megadott lekérdezési kifejezések előfordulhat, hogy ehhez hasonló kifejezésekkel. Eredmények a rendszer a részletes JSON-dokumentumok formájában adja vissza.

### <a name="simple-query-with-top-n-results"></a>Egyszerű lekérdezés az első N eredménnyel

#### <a name="example-string-query-searchseattle"></a>(Lekérdezési karakterlánc). például: `search=seattle`

* A **search** paraméter kulcsszavas keresés bevitelére használható teljes szöveges kereséshez. Jelen esetben olyan hirdetéseket kapunk vissza a Washington állambeli King megyéből, amelyek tartalmazzák a *Seattle* kifejezést a dokumentum bármely kereshető mezőjében.

* A **Keresési ablak** JSON-formátumban adja vissza az eredményeket, amely részletes és nehezen olvasható lehet, ha a dokumentumok sűrű szerkezettel rendelkeznek. Ez a szándékos; a teljes dokumentum betekintést fontos fejlesztési célokra, különösen a tesztelés során. A jobb felhasználói élmény érdekében olyan kódot kell írnia, amely [a keresési eredmények kezelésével](search-pagination-page-layout.md) kiemeli a fontosabb elemeket.

* A dokumentumokban minden mező „lekérdezhetőként” van jelölve az indexben. A portálon az indexattribútumok megtekintéséhez, kattintson a *realestate-us-sample* a a **indexek** listája.

#### <a name="example-parameterized-query-searchseattlecounttruetop100"></a>(A paraméteres lekérdezés). például: `search=seattle&$count=true&$top=100`

* Az **&** szimbólum a keresési paraméterek összefűzésére használható, amelyek bármilyen sorrendben megadhatók.

* A **$count=true** paraméter az összes visszaadott dokumentum teljes darabszámát adja vissza. Ez az érték a keresési eredmények elejénél található. A szűrőlekérdezések ellenőrzéséhez megfigyelheti a **$count=true** paraméter által jelentett módosításokat. A kisebb darabszámok azt jelzik, hogy a szűrő működik.

* A **$top=100** paraméter az összes közül az első 100 dokumentumot adja vissza. Alapértelmezés szerint az Azure Search az első 50 egyezést adja vissza. A **$top** paraméter használatával növelheti vagy csökkentheti a mennyiséget.

### <a name="filter-query"></a> A lekérdezés szűrése

A **$filter** paraméter hozzáfűzésekor a szűrők megjelennek a keresési kérésekben. 

#### <a name="example-filtered-searchseattlefilterbeds-gt-3"></a>Példa (szűrt): `search=seattle&$filter=beds gt 3`

* A **$filter** paraméter olyan eredményeket ad vissza, amelyek megfelelnek a megadott feltételeknek. Ebben az esetben: 3-nál több hálószoba.

* A szűrőszintaxis egy OData-konstrukció. További információk: [OData-szűrőszintaxis](https://docs.microsoft.com/rest/api/searchservice/odata-expression-syntax-for-azure-search).

### <a name="facet-query"></a> A lekérdezés értékkorlátozása

Az értékkorlátozó szűrők megjelennek a keresési kérésekben. A facet paraméter adja vissza azon dokumentumok összegzett darabszámát, amelyek megfelelnek a megadott értékkorlátozási értéknek.

#### <a name="example-faceted-with-scope-reduction-searchfacetcitytop2"></a>Példa (hatókörszűkítéssel korlátozva): `search=*&facet=city&$top=2`

* A **search=*** egy üres keresés. Az üres keresések mindenben keresnek. Az üres lekérdezések elküldésének egyik oka a teljes dokumentumkészlet szűrése vagy értékkorlátozása lehet. Például akkor, ha azt szeretné, hogy egy értékkorlátozó navigációs szerkezet az index összes városából álljon.

* A **facet** paraméter olyan navigációs szerkezetet ad vissza, amelyet továbbíthat egy felhasználói felületi vezérlőnek. Kategóriákat és egy számot ad vissza. Jelen esetben a kategóriák alapját a városok száma jelenti. Az Azure Searchben nincs összesítés, de megbecsülheti az összesítést a `facet` használatával, amely az egyes kategóriákban lévő dokumentumok számát adja meg.

* A **$top=2** paraméter két dokumentumot ad vissza, így bemutatja, hogy a `top` használatával csökkentheti és növelheti is az eredményeket.

#### <a name="example-facet-on-numeric-values-searchseattlefacetbeds"></a>Példa (a numerikus értékekre korlátozva): `search=seattle&facet=beds`**

* Ez a lekérdezés az ágyak értékkorlátozását jelenti a *Seattle* szöveges kereséséhez. A *ágyak* kifejezés megadható értékkorlátozásként, mert a mező lekérdezhetőként, szűrhetőként és kategorizálhatóként van megjelölve az indexben, és a tartalmazott értékei (numerikus, 1–5) alkalmasak a hirdetések csoportokba való rendezésére (3 hálószobás, 4 hálószobás ingatlanok hirdetései).

* Csak a szűrhető mezők értéke korlátozható. Csak a lekérdezhető mezők adhatók vissza az eredményekben.

### <a name="highlight-query"></a> Keresési eredmények kiemelése

A találatok kiemelése a kulcsszóval megegyező szöveg formázását jelenti, feltéve, hogy vannak egyezések a megadott mezőben. Ha a keresett kifejezés egy leírás mélyén rejlik, a találatok kiemelése funkcióval könnyebben észrevehetővé teheti.

#### <a name="example-highlighter-searchgranite-countertopshighlightdescription"></a>Példa (kiemelő): `search=granite countertops&highlight=description`

* Ebben a példában a *gránit munkalapok* formázott kifejezés könnyebben észrevehető a leírás mezőben.

#### <a name="example-linguistic-analysis-searchmicehighlightdescription"></a>Példa (nyelvi elemzés): `search=mice&highlight=description`

* A teljes szöveges keresés hasonló szemantikával rendelkező szóalakok keresésére használható. Jelen esetben a keresési eredmények tartalmazzák a „mouse” (egér) kiemelt szöveget az egérlakta házak esetében, pedig a keresési kulcsszó „mice” (egerek) volt. A nyelvészeti elemzés következtében ugyanazon szó különböző alakjai is megjelenhetnek az eredmények között.

* Az Azure Search szolgáltatás összesen 56, a Lucene-től és Microsoft-tól származó elemzőt támogat. A szolgáltatás alapértelmezés szerint a standard Lucene-elemzőt használja.

### <a name="fuzzy-search"></a> Az intelligens keresés kipróbálása

Alapértelmezés szerint hibás lekérdezési kifejezéseket, például *samamish* a Seattle környékén található Sammamish plafont, az nem adnak vissza egyezések átlagos keresések. A következő példa nem ad vissza eredményt.

#### <a name="example-misspelled-term-unhandled-searchsamamish"></a>Példa (elírt kifejezés, nem kezelve): `search=samamish`

A helytelenül leírt szavak kezelésére használhat intelligens keresést. Az intelligens keresés akkor érhető el, amikor a teljes Lucene lekérdezési szintaxis használja, ehhez pedig a következő két dolog szükséges: állítsa be a **queryType=full** paramétert a lekérdezésre, és fűzze hozzá a **~** utótagot a keresési sztringhez.

#### <a name="example-misspelled-term-handled-searchsamamishquerytypefull"></a>Példa (elírt kifejezés, kezelve): `search=samamish~&queryType=full`

Ez a példa már visszaadja a „Sammamish” kifejezést tartalmazó dokumentumokat.

Amikor a **queryType** paraméter nincs meghatározva, a rendszer az alapértelmezett egyszerű lekérdezéselemzőt használja. Ez az egyszerű lekérdezéselemző gyorsabb, de ha intelligens keresésre, reguláris kifejezésekre, közelségi keresésre vagy egyéb speciális lekérdezéstípusokra van szüksége, a teljes szintaxisra szüksége lesz.

Az intelligens keresés és a helyettesítő karakteres keresések befolyásolják a keresési eredményeket. A rendszer az ilyen formátumú kereséseken nem végez nyelvi elemzést. Az intelligens és a helyettesítő karakteres keresések használata előtt tekintse át [Az Azure Search teljes szöveges keresés funkciójának működését](search-lucene-query-architecture.md#stage-2-lexical-analysis) ismertető cikknek a nyelvi elemzés kivételeivel foglalkozó szakaszát.

A teljes lekérdezéselemző által lehetővé tett lekérdezési forgatókönyvekkel kapcsolatos további információk: [Lucene lekérdezési szintaxis az Azure Search szolgáltatásban](https://docs.microsoft.com/rest/api/searchservice/lucene-query-syntax-in-azure-search).

### <a name="geo-search"></a> A térinformatikai keresés kipróbálása

A térinformatikai keresés az [edm.GeographyPoint adattípuson](https://docs.microsoft.com/rest/api/searchservice/supported-data-types) keresztül támogatott a koordinátákat tartalmazó mezők esetében. A geosearch egy szűrőtípus, amelynek meghatározása a [OData-szűrőszintaxis](https://docs.microsoft.com/rest/api/searchservice/odata-expression-syntax-for-azure-search) című témakörben olvasható.

#### <a name="example-geo-coordinate-filters-searchcounttruefiltergeodistancelocationgeographypoint-122121513-47673988-le-5"></a>Példa (geokoordináta-szűrők): `search=*&$count=true&$filter=geo.distance(location,geography'POINT(-122.121513 47.673988)') le 5`

A példa lekérdezés minden eredményt szűr a helyzeti adatok alapján, és olyan eredményeket a vissza, amelyek kevesebb, mint 5 kilométerre találhatók a (szélességi és hosszúsági koordinátákként) megadott ponttól. A **$count** paraméter hozzáadásával láthatja, hány eredményt ad vissza a rendszer, amikor módosítja a távolságot vagy a koordinátákat.

A térinformatikai keresés hasznos lehet, ha a keresőalkalmazás rendelkezik „keresés a közelben” funkcióval vagy térképes navigációt használ. Ez azonban nem teljes szöveges keresés. Ha a városok vagy országok/régiók nevére való keresés felhasználói követelmény, akkor a koordináták mellett adjon meg városok vagy országok nevét tartalmazó mezőket.

## <a name="takeaways"></a>Legfontosabb ismeretek

Ebben az oktatóanyagban biztosított az Azure Search röviden bemutatja az Azure portal használatával.

Megtudta, hogyan hozhat létre keresési indexet az **Adatok importálása** varázslóval. Megismerkedett az [indexelőkkel](search-indexer-overview.md), valamint az indextervezés alapvető munkafolyamatával, többek között [a közzétett indexek támogatott módosításaival](https://docs.microsoft.com/rest/api/searchservice/update-index) is.

Az Azure Portal **Keresési ablakával** megismert egyes alapvető lekérdezési szintaxisokat olyan gyakorlati példákon keresztül, amelyek kulcsfontosságú funkciókat, például a szűrést, a találatok kiemelését, az intelligens és a földrajzi keresést mutatták be.

Azt is megtanulta, hogyan találhatja meg az indexek, indexelők és adatforrások a portálon. Később bármilyen új adatforrás definícióit és mezőgyűjteményeit gyorsan és egyszerűen ellenőrizheti a portálon.

## <a name="clean-up"></a>A fölöslegessé vált elemek eltávolítása

Ha ez az oktatóanyag az Azure Search szolgáltatás első használatát, törölje az erőforráscsoportot, amely tartalmazza az Azure Search szolgáltatás. Ha nem, keresse ki a megfelelő erőforráscsoport nevét a szolgáltatások listájában, és törölje.

## <a name="next-steps"></a>További lépések

Az Azure Searchöt behatóbban is megismerheti az alábbi programozási eszközökkel:

* [-Index létrehozása .NET SDK használatával](https://docs.microsoft.com/azure/search/search-create-index-dotnet)
* [-Index létrehozása REST API-k használatával](https://docs.microsoft.com/azure/search/search-create-index-rest-api)
* [-Index létrehozása Postman vagy a Fiddlerre, és az Azure Search REST API-k használatával](search-fiddler.md)
