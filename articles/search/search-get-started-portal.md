---
title: Oktatóanyag – Indexelés, lekérdezés és szűrés az Azure Searchben a portál használatával | Microsoft Docs
description: Ebben az oktatóanyagban az Azure Portal és előre meghatározott mintaadatok segítségével indexet hozhat létre az Azure Searchben. Használhatja a teljes szöveges keresést, a szűrőket, az aspektusokat, az intelligens keresést, a geosearch funkciót és sok mást.
author: HeidiSteen
manager: cgronlun
tags: azure-portal
services: search
ms.service: search
ms.topic: tutorial
ms.date: 07/10/2018
ms.author: heidist
ms.openlocfilehash: 0eb6701a7ea08c2dd63bd8b5d7d7c805e6eb1376
ms.sourcegitcommit: f86e5d5b6cb5157f7bde6f4308a332bfff73ca0f
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/31/2018
ms.locfileid: "39365085"
---
# <a name="tutorial-use-built-in-tools-for-azure-search-indexing-and-queries"></a>Oktatóanyag: Beépített eszközök használata indexeléshez és lekérdezésekhez az Azure Searchben

Az Azure Search szolgáltatás lapján az Azure Portalon a beépített eszközökkel tesztelheti az ötleteit, és gyakorlati tapasztalatokat szerezhet minimális felkészüléssel. A portál eszközei nem biztosítanak teljes kompatibilitást a .NET és REST API-kkal, azonban az ötletek gyors megvalósíthatósági teszteléséhez a varázslók és a szerkesztők gyors segítséget adnak. Ez a kódolás nélküli bevezetés egy kisebb közzétett adathalmazzal segíti az első lépésekben, hogy azonnal érdekes lekérdezéseket tudjon írni. 

> [!div class="checklist"]
> * Kezdje a nyilvános mintaadatokkal, és automatikusan hozzon létre egy Azure Search-indexet az **Adatok importálása** varázslóval. 
> * Az Azure Searchben közzétett bármely index indexsémáját és attribútumait megtekintheti.
> * A **keresési ablakban** használhatja a teljes szöveges keresést, a szűrőket, az aspektusokat, az intelligens keresést és a geosearch funkciót.  

A portál eszközei nem támogatják az Azure Search összes képességét. Ha az eszközök túl korlátozottnak bizonyulnak, érdemes lehet áttekintenie [az Azure Search .NET-ben való programozásának kódalapú bevezetését](search-howto-dotnet-sdk.md) vagy [a REST API-hívások indításához készült webes teszteszközöket](search-fiddler.md).

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt. Vagy megtekinthet egy 6 perces bemutatót az oktatóanyag lépéseiről. A bemutató nagyjából az [Azure Search áttekintővideójának](https://channel9.msdn.com/Events/Connect/2016/138) harmadik percénél kezdődik.

## <a name="prerequisites"></a>Előfeltételek

[Hozzon létre egy Azure Search szolgáltatást](search-create-service-portal.md), vagy keressen egy meglévő szolgáltatást az aktuális előfizetés alatt. 

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. Nyissa meg az Azure Search szolgáltatás irányítópultját. Ha nem rögzítette a szolgáltatás csempéjét az irányítópulton, az alábbi módon találhatja meg azt: 
   
   * Az ugrósávon kattintson a bal oldali navigációs panelen található **Minden szolgáltatás** elemre.
   * Az előfizetéséhez elérhető kereséssel kapcsolatos szolgáltatások listájának lekéréséhez írja be a keresőmezőbe a *search* kifejezést. Kattintson a **Keresési szolgáltatások** elemre. A keresett szolgáltatásnak meg kell jelennie a listában. 

### <a name="check-for-space"></a>Szabad terület ellenőrzése
Sok ügyfél az ingyenes szolgáltatással kezdi. Ez a verzió három indexre, három adatforrásra és három indexelőre korlátozódik. Mielőtt hozzákezdene, ellenőrizze, hogy elegendő hellyel rendelkezik-e további elemek számára. Az oktatóanyagban minden objektumból egyet hozhat majd létre. 

> [!TIP] 
> A szolgáltatás irányítópultján megtekintheti, hány index, indexelő és adatforrás áll rendelkezésére. Az Indexelő csempe a sikeresség és a sikertelenség jelölőit jeleníti meg. Kattintson a csempére az indexelők számának megtekintéséhez. 
>
> ![Indexelők és adatforrások csempéje][1]
>

## <a name="create-index"></a> Index létrehozása és az adatok betöltése
A keresési lekérdezések egy [*index*](search-what-is-an-index.md) alapján ismétlődnek, amely kereshető adatokat, metaadatokat és bizonyos keresési viselkedések optimalizálásához használt szerkezeteket tartalmaz.

Annak érdekében, hogy a feladat portálalapú maradjon, egy beépített mintaadathalmazt fogunk használni, amely könnyedén bejárható egy [*indexelővel*](search-indexer-overview.md) az **Adatok importálása** varázslón keresztül. Az indexelők adatforrás-specifikus webbejárók, amelyek metaadatokat és tartalmakat képesek olvasni a támogatott Azure-adatforrásokból. A kódban az indexelőket független erőforrásként hozhatja létre és kezelheti. A portálon az indexelők az **Adatok importálása** varázslóban érhetők el. 

#### <a name="step-1-start-the-import-data-wizard"></a>1. lépés: Az Adatok importálása varázsló elindítása
1. Az Azure Search szolgáltatás irányítópultján kattintson az **Adatok importálása** parancsra a parancssávon az indexet létrehozó és feltöltő varázsló elindításához.
   
    ![Adatok importálása parancs][2]

2. A varázslóban kattintson a **Csatlakozás az adatokhoz** > **Minták** > **realestate-us-sample** elemre. Az adatforrás neve, típusa és kapcsolódási adatai előre konfigurálva vannak. Létrehozását követően „meglévő adatforrássá” válik, amely más importálási műveletek során ismét felhasználható.

    ![Minta adatkészlet kiválasztása][9]

3. Az adatkészlet használatához kattintson az **OK** gombra.

#### <a name="skip-cognitive-skills"></a>Kognitív képességek kihagyása

Az **Adatok importálása** egy, a kognitív képességekre vonatkozó opcionális lépést is tartalmaz, amely MI-algoritmusokkal egészíti ki az indexelést. Ez az oktatóanyag erre a funkcióra nem terjed ki, ezért ugorjon a **Célindex testreszabása** lépésre. Ha kíváncsi az Azure Search előzetes verziójú szolgáltatásként elérhető új kognitív keresési szolgáltatásaira, tekintse meg a [kognitív keresés rövid útmutatóját](cognitive-search-quickstart-blob.md) vagy [oktatóanyagát](cognitive-search-tutorial-blob.md).

   ![A kognitív képességek lépés kihagyása][11]

#### <a name="step-2-define-the-index"></a>2. lépés: Az index meghatározása
Az indexek létrehozása általában manuális és kódalapú feladat, de a varázsló bármilyen bejárható adatforráshoz képes indexet létrehozni. Az indexhez szükség van legalább egy névre és egy mezőgyűjteményre, amely mezők közül az egyiket a dokumentum kulcsaként kell megjelölni. Ez az egyes dokumentumok egyedi azonosítására szolgál.

A mezők adattípusokkal és attribútumokkal rendelkeznek. A fent látható jelölőnégyzetek *indexattribútumok*, amelyek a mező használati módját szabályozzák. 

* **Lekérhető**: azt jelenti, hogy a mező a keresési eredmények listájában jelenik meg. A jelölőnégyzet törlésével az egyes mezőket a keresési eredmények korlátjain kívül esőként jelölheti meg, például amikor a mezőket csak szűrőkifejezésekben használják. 
* **Szűrhető**, **Rendezhető** és **Kategorizálható**: azt határozzák meg, hogy egy mező használható-e szűrésben, rendezésben vagy jellemzőalapú navigációs szerkezetben. 
* **Kereshető**: azt jelenti, hogy a mező szerepel a teljes szöveges keresésben. A sztringek kereshetők. A numerikus és logikai mezőket gyakran nem kereshetőként jelölik meg. 

Alapértelmezés szerint a varázsló átvizsgálja a adatforrást egyedi azonosítókat keresve, amelyeket felhasználhat a kulcsmező alapjaként. A sztringek lekérhetőként és kereshetőként vannak megjelölve. Az egész számok lekérhetőként, szűrhetőként, rendezhetőként és kategorizálhatóként vannak megjelölve.

  ![Létrehozott ingatlanindex][3]

Az index létrehozásához kattintson az **OK** gombra.

#### <a name="step-3-define-the-indexer"></a>3. lépés: Az indexelő meghatározása
Továbbra is az **Adatok importálása** varázslóban maradva kattintson az **Indexelő** > **Név** lehetőségre, és gépelje be az indexelő nevét. 

Ez az objektum egy végrehajtható folyamatot határoz meg. Ismétlődő ütemezés is beállítható, de most használja az alapértelmezett beállítást, és futtassa az indexelőt egyszer, közvetlenül az **OK** gombra való kattintás után.  

  ![ingatlanindexelő][8]

## <a name="check-progress"></a>Folyamat állapotának ellenőrzése
Az adatimportálás felügyeletéhez térjen vissza a szolgáltatás irányítópultjához, görgessen le, és kattintson duplán az **Indexelők** csempére az indexelők listájának megnyitásához. Az újonnan létrehozott indexelőnek szerepelnie kell a listában. Az index állapotának megjelölése „in progress” (folyamatban) vagy sikeres lehet. Emellett az indexelt dokumentumok száma is megjelenik.

   ![Indexelő állapotüzenete][4]

## <a name="view-the-index"></a>Az index megtekintése

A szolgáltatás irányítópultjának csempéin összegző adatok tekinthetők meg, valamint innen elérhetők a részletes információk is. Például az **Indexek** csempén a meglévő indexek listáját láthatja, beleértve az előző lépésben létrehozott *realestate-us-sample* indexet is.

Kattintson a *realestate-us-sample* csempére az indexdefiníció a portálon elérhető beállításainak megtekintéséhez. A **Mezők hozzáadása/szerkesztése** beállítással mezőket hozhat létre és láthat el attribútumokkal. A meglévő mezők fizikailag vannak jelölve az Azure Searchben, és így nem módosíthatók, még a kódban sem. Egy meglévő mező alapvető módosításához hozzon létre egy új mezőt, és vesse el a régit. 

   ![példa indexdefinícióra][10]

Egyéb szerkezetek, például pontozási profilok és CORS-paraméterek, bármikor hozzáadhatók. 

Annak érdekében, hogy jól átlássa az indextervezés során szerkeszthető és nem szerkeszthető elemeket, szánjon egy percet az indexdefiníciós lehetőségek áttekintésére. A kiszürkített lehetőségek azt jelzik, hogy az érték nem módosítható vagy törölhető.

## <a name="query-index"></a> Az index lekérdezése
Továbblépve most már rendelkezünk egy keresési indexszel, amely készen áll a lekérdezésre a beépített [**Keresési ablak**](search-explorer.md) lekérdezési lappal. Ez egy keresőmezőt biztosít, amellyel tesztelheti a tetszőleges lekérdezési sztringeket. 

> [!TIP]
> Az [Azure Search szolgáltatás áttekintő videója](https://channel9.msdn.com/Events/Connect/2016/138) a következő lépéseket mutatja be 6 perc 8 másodperctől kezdve.
>

1. A parancssávon kattintson a **Keresési ablak** elemre.

   ![Keresési ablak parancs][5]

2. Kattintson a parancssáv **Index módosítása** elemére a *realestate-us-sample* indexre való váltáshoz. Kattintson a parancssáv **API-verzió beállítása** elemére az elérhető REST API-k megtekintéséhez. Az alábbi lekérdezésekhez használja az általánosan elérhető verziót (2017-11-11). 

   ![Index és API-parancsok][6]

3. A keresősávba írja be az alábbi lekérdezési sztringeket, majd kattintson a **Keresés** gombra.

    > [!NOTE]
    > A **Keresési ablak** csak a [REST API-kérések](https://docs.microsoft.com/rest/api/searchservice/search-documents) kezelésre alkalmas. Elfogadja az [egyszerű lekérdezési szintaxis](https://docs.microsoft.com/rest/api/searchservice/simple-query-syntax-in-azure-search) és a [teljes Lucene lekérdezéselemző](https://docs.microsoft.com/rest/api/searchservice/lucene-query-syntax-in-azure-search) szintaxisát is, továbbá a [Dokumentum keresése](https://docs.microsoft.com/rest/api/searchservice/search-documents) műveletekben elérhető összes keresési paramétert.
    > 


#### <a name="example-string-searchseattle"></a>Példa (sztring): `search=seattle`

+ A **search** paraméter kulcsszavas keresés bevitelére használható teljes szöveges kereséshez. Jelen esetben olyan hirdetéseket kapunk vissza a Washington állambeli King megyéből, amelyek tartalmazzák a *Seattle* kifejezést a dokumentum bármely kereshető mezőjében. 

+ A **Keresési ablak** JSON-formátumban adja vissza az eredményeket, amely részletes és nehezen olvasható lehet, ha a dokumentumok sűrű szerkezettel rendelkeznek. Ez szándékos – fejlesztési célokból fontos a teljes dokumentum láthatósága, különösen a tesztelés során. A jobb felhasználói élmény érdekében olyan kódot kell írnia, amely [a keresési eredmények kezelésével](search-pagination-page-layout.md) kiemeli a fontosabb elemeket.

+ A dokumentumokban minden mező „lekérdezhetőként” van jelölve az indexben. A portálon az indexattribútumok megtekintéséhez kattintson a *realestate-us-sample* elemre az **Indexek** csempén.

#### <a name="example-parameterized-searchseattlecounttruetop100"></a>Példa (paraméteres): `search=seattle&$count=true&$top=100`

+ Az **&** szimbólum a keresési paraméterek összefűzésére használható, amelyek bármilyen sorrendben megadhatók. 

+  A **$count=true** paraméter visszaadja az összes visszaadott dokumentum összegét. Ez az érték a keresési eredmények elejénél található. A szűrőlekérdezések ellenőrzéséhez megfigyelheti a **$count=true** paraméter által jelentett módosításokat. A kisebb darabszámok azt jelzik, hogy a szűrő működik.

+ A **$top=100** paraméter az összes közül az első 100 dokumentumot adja vissza. Alapértelmezés szerint az Azure Search az első 50 egyezést adja vissza. A **$top** paraméter használatával növelheti vagy csökkentheti a mennyiséget.

## <a name="filter-query"></a> A lekérdezés szűrése

A **$filter** paraméter hozzáfűzésekor a szűrők megjelennek a keresési kérésekben. 

#### <a name="example-filtered-searchseattlefilterbeds-gt-3"></a>Példa (szűrt): `search=seattle&$filter=beds gt 3`

+ A **$filter** paraméter olyan eredményeket ad vissza, amelyek megfelelnek a megadott feltételeknek. Ebben az esetben: 3-nál több hálószoba. 

+ A szűrőszintaxis egy OData-konstrukció. További információk: [OData-szűrőszintaxis](https://docs.microsoft.com/rest/api/searchservice/odata-expression-syntax-for-azure-search).

## <a name="facet-query"></a> A lekérdezés értékkorlátozása

Az értékkorlátozó szűrők megjelennek a keresési kérésekben. A facet paraméter adja vissza azon dokumentumok összegzett darabszámát, amelyek megfelelnek a megadott értékkorlátozási értéknek. 

#### <a name="example-faceted-with-scope-reduction-searchfacetcitytop2"></a>Példa (hatókörszűkítéssel korlátozva): `search=*&facet=city&$top=2`

+ A **search=*** egy üres keresés. Az üres keresések mindenben keresnek. Az üres lekérdezések elküldésének egyik oka a teljes dokumentumkészlet szűrése vagy értékkorlátozása lehet. Például akkor, ha azt szeretné, hogy egy értékkorlátozó navigációs szerkezet az index összes városából álljon.

+  A **facet** paraméter olyan navigációs szerkezetet ad vissza, amelyet továbbíthat egy felhasználói felületi vezérlőnek. Kategóriákat és egy számot ad vissza. Jelen esetben a kategóriák alapját a városok száma jelenti. Az Azure Searchben nincs összesítés, de megbecsülheti az összesítést a `facet` használatával, amely az egyes kategóriákban lévő dokumentumok számát adja meg.

+ A **$top=2** paraméter két dokumentumot ad vissza, így bemutatja, hogy a `top` használatával csökkentheti és növelheti is az eredményeket.

#### <a name="example-facet-on-numeric-values-searchseattlefacetbeds"></a>Példa (a numerikus értékekre korlátozva): `search=seattle&facet=beds`**

+ Ez a lekérdezés az ágyak értékkorlátozását jelenti a *Seattle* szöveges kereséséhez. A *ágyak* kifejezés megadható értékkorlátozásként, mert a mező lekérdezhetőként, szűrhetőként és kategorizálhatóként van megjelölve az indexben, és a tartalmazott értékei (numerikus, 1–5) alkalmasak a hirdetések csoportokba való rendezésére (3 hálószobás, 4 hálószobás ingatlanok hirdetései). 

+ Csak a szűrhető mezők értéke korlátozható. Csak a lekérdezhető mezők adhatók vissza az eredményekben.

## <a name="highlight-query"></a> Kiemelés hozzáadása

A találatok kiemelése a kulcsszóval megegyező szöveg formázását jelenti, feltéve, hogy vannak egyezések a megadott mezőben. Ha a keresett kifejezés egy leírás mélyén rejlik, a találatok kiemelése funkcióval könnyebben észrevehetővé teheti. 

#### <a name="example-highlighter-searchgranite-countertopshighlightdescription"></a>Példa (kiemelő): `search=granite countertops&highlight=description`

+ Ebben a példában a *gránit munkalapok* formázott kifejezés könnyebben észrevehető a leírás mezőben.

#### <a name="example-linguistic-analysis-searchmicehighlightdescription"></a>Példa (nyelvi elemzés): `search=mice&highlight=description`

+ A teljes szöveges keresés hasonló szemantikával rendelkező szóalakok keresésére használható. Jelen esetben a keresési eredmények tartalmazzák a „mouse” (egér) kiemelt szöveget az egérlakta házak esetében, pedig a keresési kulcsszó „mice” (egerek) volt. A nyelvészeti elemzés következtében ugyanazon szó különböző alakjai is megjelenhetnek az eredmények között. 

+ Az Azure Search szolgáltatás összesen 56, a Lucene-től és Microsoft-tól származó elemzőt támogat. A szolgáltatás alapértelmezés szerint a standard Lucene-elemzőt használja. 

## <a name="fuzzy-search"></a> Az intelligens keresés kipróbálása

Alapértelmezés szerint a helytelenül leírt szavak (például a Seattle környékén található Sammamish-fennsíkra a *samamish* sztring használata) nem adnak vissza találatokat az átlagos keresések során. A következő példa nem ad vissza eredményt.

#### <a name="example-misspelled-term-unhandled-searchsamamish"></a>Példa (elírt kifejezés, nem kezelve): `search=samamish`

A helytelenül leírt szavak kezelésére használhat intelligens keresést. Az intelligens keresés akkor érhető el, amikor a teljes Lucene lekérdezési szintaxis használja, ehhez pedig a következő két dolog szükséges: állítsa be a **queryType=full** paramétert a lekérdezésre, és fűzze hozzá a **~** utótagot a keresési sztringhez. 

#### <a name="example-misspelled-term-handled-searchsamamishquerytypefull"></a>Példa (elírt kifejezés, kezelve): `search=samamish~&queryType=full`

Ez a példa már visszaadja a „Sammamish” kifejezést tartalmazó dokumentumokat.

Amikor a **queryType** paraméter nincs meghatározva, a rendszer az alapértelmezett egyszerű lekérdezéselemzőt használja. Ez az egyszerű lekérdezéselemző gyorsabb, de ha intelligens keresésre, reguláris kifejezésekre, közelségi keresésre vagy egyéb speciális lekérdezéstípusokra van szüksége, a teljes szintaxisra szüksége lesz. 

Az intelligens keresés és a helyettesítő karakteres keresések befolyásolják a keresési eredményeket. A rendszer az ilyen formátumú kereséseken nem végez nyelvi elemzést. Az intelligens és a helyettesítő karakteres keresések használata előtt tekintse át [Az Azure Search teljes szöveges keresés funkciójának működését](search-lucene-query-architecture.md#stage-2-lexical-analysis) ismertető cikknek a nyelvi elemzés kivételeivel foglalkozó szakaszát.

A teljes lekérdezéselemző által lehetővé tett lekérdezési forgatókönyvekkel kapcsolatos további információk: [Lucene lekérdezési szintaxis az Azure Search szolgáltatásban](https://docs.microsoft.com/rest/api/searchservice/lucene-query-syntax-in-azure-search).

## <a name="geo-search"></a> A térinformatikai keresés kipróbálása

A térinformatikai keresés az [edm.GeographyPoint adattípuson](https://docs.microsoft.com/rest/api/searchservice/supported-data-types) keresztül támogatott a koordinátákat tartalmazó mezők esetében. A geosearch egy szűrőtípus, amelynek meghatározása a [OData-szűrőszintaxis](https://docs.microsoft.com/rest/api/searchservice/odata-expression-syntax-for-azure-search) című témakörben olvasható. 

#### <a name="example-geo-coordinate-filters-searchcounttruefiltergeodistancelocationgeographypoint-122121513-47673988-le-5"></a>Példa (geokoordináta-szűrők): `search=*&$count=true&$filter=geo.distance(location,geography'POINT(-122.121513 47.673988)') le 5`

A példa lekérdezés minden eredményt szűr a helyzeti adatok alapján, és olyan eredményeket a vissza, amelyek kevesebb, mint 5 kilométerre találhatók a (szélességi és hosszúsági koordinátákként) megadott ponttól. A **$count** paraméter hozzáadásával láthatja, hány eredményt ad vissza a rendszer, amikor módosítja a távolságot vagy a koordinátákat. 

A térinformatikai keresés hasznos lehet, ha a keresőalkalmazás rendelkezik „keresés a közelben” funkcióval vagy térképes navigációt használ. Ez azonban nem teljes szöveges keresés. Ha a városok vagy országok nevére való keresés felhasználói követelmény, akkor a koordináták mellett adjon meg városok vagy országok nevét tartalmazó mezőket.

## <a name="takeaways"></a>Legfontosabb ismeretek

Ez az oktatóanyag az **Adatok importálása** varázsló és **Keresési ablak** használatának alapvető lépéseit mutatja be az Azure Portalon.

Megismerkedett az Adatok importálása varázsló fő összetevőivel, az [indexelőkkel](search-indexer-overview.md), valamint az indextervezés alapvető munkafolyamatával, többek között azzal is, hogy [a közzétett indexek mely módosításai támogatottak](ttps://docs.microsoft.com/rest/api/searchservice/update-index). 

A kulcsfontosságú funkciókat, úgymint a szűrést, a találatok kiemelését, az intelligens és a földrajzi keresést bemutató gyakorlati példákon keresztül megismerte a lekérdezési szintaxist.

Végül megtanulta, hogyan kérheti le az előfizetésében létrehozott bármely index, indexelő vagy adatforrás adatait az irányítópult csempéire kattintva. Később, amikor majd saját vagy a munkatársai által létrehozott indexekkel dolgozik, a portál használatával gyorsan ellenőrizheti az adatforrás-definíciókat vagy a mezőgyűjtemények szerkezetét, anélkül, hogy ismeretlen kódban kellene keresgélnie.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Az oktatóanyagok után feleslegessé vált elemek az Azure Search szolgáltatást tartalmazó erőforráscsoport törlésével távolíthatók el a leggyorsabban. Most törölheti az erőforráscsoportot, amivel véglegesen eltávolíthatja a teljes tartalmát. A portálon az erőforráscsoport neve az Azure Search szolgáltatás Áttekintés lapján szerepel.

## <a name="next-steps"></a>További lépések

Az Azure Search további eszközökkel való felderítéséhez érdemes valamelyik REST-alapú teszteszközt, például a Postmant vagy a Fiddlert használnia:

> [!div class="nextstepaction"]
> [Az Azure Search REST API-jait hívó webes teszteszközök](search-fiddler.md)


<!--Image references-->
[1]: ./media/search-get-started-portal/tiles-indexers-datasources2.png
[2]: ./media/search-get-started-portal/import-data-cmd2.png
[3]: ./media/search-get-started-portal/realestateindex2.png
[4]: ./media/search-get-started-portal/indexers-inprogress2.png
[5]: ./media/search-get-started-portal/search-explorer-cmd2.png
[6]: ./media/search-get-started-portal/search-explorer-changeindex-se2.png
[7]: ./media/search-get-started-portal/search-explorer-query2.png
[8]: ./media/search-get-started-portal/realestate-indexer2.png
[9]: ./media/search-get-started-portal/import-datasource-sample2.png
[10]: ./media/search-get-started-portal/sample-index-def.png
[11]: ./media/search-get-started-portal/skip-cog-skill-step.png