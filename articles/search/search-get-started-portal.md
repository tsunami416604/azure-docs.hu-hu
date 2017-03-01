---
title: "Az első saját Azure Search-index létrehozása a portálon | Microsoft Docs"
description: "Az Azure Portalon előre meghatározott mintaadatok használatával indexet hozhat létre. Használhatja a teljes szöveges keresést, a szűrőket, az aspektusokat, az intelligens keresést, a geosearch funkciót és sok mást."
services: search
documentationcenter: 
author: HeidiSteen
manager: jhubbard
editor: 
tags: azure-portal
ms.assetid: 21adc351-69bb-4a39-bc59-598c60c8f958
ms.service: search
ms.devlang: na
ms.workload: search
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.date: 02/15/2017
ms.author: heidist
translationtype: Human Translation
ms.sourcegitcommit: cb0843ec739d11e997794a8217c95696c4e78d23
ms.openlocfilehash: 70999d615038e7a5a11a623a9eef3e08c09f5eb9


---
# <a name="build-and-query-your-first-azure-search-index-in-the-portal"></a>Az első saját Azure Search-index létrehozása és lekérdezése a portálon

Egy előre meghatározott minta adatkészlettel, valamint az **Adatok importálása** varázslóval gyorsan létrehozhat egy indexet az Azure Portalon. A **keresési ablakban** használhatja a teljes szöveges keresést, a szűrőket, az aspektusokat, az intelligens keresést és a geosearch funkciót.  

Így kódolás nélkül juthat előre meghatározott adatokhoz, és azonnal lehetősége van Önt érdeklő lekérdezések írására. A portál eszközei nem helyettesítik a kódolást, de a következő feladatokhoz hasznosak lehetnek:

+ Gyakorlati tanulás minimális bevezetés után
+ Index prototípusának elkészítése kód írása előtt az **Adatok importálása** területen
+ A lekérdezések és az elemzőszintaxis tesztelése a **keresési ablakban**
+ A szolgáltatásban már közzétett, meglévő index megtekintése és az index attribútumainak keresése

**Becsült időtartam:** Körülbelül 15 perc, de tovább is tarthat, ha szükség van a fiók vagy szolgáltatás regisztrálására. 

Vagy megtekinthet egy 6 perces bemutatót az oktatóanyag lépéseiről. A bemutató nagyjából az [Azure Search áttekintővideójának](https://channel9.msdn.com/Events/Connect/2016/138) harmadik percénél kezdődik.

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag azt feltételezi, hogy rendelkezik [Azure-előfizetéssel](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F) és az [Azure Search szolgáltatással](search-create-service-portal.md). 

## <a name="find-your-service"></a>A szolgáltatása megkeresése
1. Jelentkezzen be az [Azure portálra](https://portal.azure.com).
2. Nyissa meg az Azure Search szolgáltatás irányítópultját. Ha nem rögzítette a szolgáltatás csempéjét az irányítópulton, az alábbi módon találhatja meg azt: 
   
   * Az ugrósávon kattintson a bal oldali navigációs ablaktábla alján található **További szolgáltatások** lehetőségre.
   * A keresőmezőbe gépelje a *search* kifejezést az előfizetéséhez elérhető keresési szolgáltatások listájának lekéréséhez. A keresett szolgáltatásnak meg kell jelennie a listában. 

## <a name="check-for-space"></a>Szabad terület ellenőrzése
Sok ügyfél az ingyenes szolgáltatással kezdi. Ez a verzió három indexre, három adatforrásra és három indexelőre korlátozódik. Mielőtt hozzákezdene, ellenőrizze, hogy elegendő hellyel rendelkezik-e további elemek számára. Az oktatóanyagban minden objektumból egyet hozhat majd létre. 

> [!TIP] 
> A szolgáltatás irányítópultján megtekintheti, hány index, indexelő és adatforrás áll rendelkezésére. Az Indexelő csempe a sikeresség és a sikertelenség jelölőit jeleníti meg. Kattintson a csempére az indexelők számának megtekintéséhez. 
>
> ![Indexelők és adatforrások csempéje][1]
>

## <a name="a-namecreate-indexa-create-an-index-and-load-data"></a><a name="create-index"></a> Index létrehozása és az adatok betöltése
A keresési lekérdezések egy *index* segítségével ismétlődnek, amely kereshető adatokat, metaadatokat és bizonyos keresési viselkedések optimalizálásához használt szerkezeteket tartalmaz.

Annak érdekében, hogy a feladat portálalapú maradjon, egy beépített minta adatkészletet fogunk használni, amely könnyedén bejárható egy indexelő segítségével az **Adatok importálása** varázslón keresztül. 

#### <a name="step-1-start-the-import-data-wizard"></a>1. lépés: Az Adatok importálása varázsló elindítása
1. Az Azure Search szolgáltatás irányítópultján kattintson az **Adatok importálása** parancsra a parancssávon az indexet létrehozó és feltöltő varázsló elindításához.
   
    ![Adatok importálása parancs][2]

2. A varázslóban kattintson az **Adatforrás** > **Minták** > **realestate-us-sample** elemre. Az adatforrás neve, típusa és kapcsolódási adatai előre konfigurálva vannak. Létrehozását követően „meglévő adatforrássá” válik, amely más importálási műveletek során ismét felhasználható.

    ![Minta adatkészlet kiválasztása][9]

3. Az adatkészlet használatához kattintson az **OK** gombra.

#### <a name="step-2-define-the-index"></a>2. lépés: Az index meghatározása
Az indexek létrehozása általában manuális és kódalapú feladat, de a varázsló bármilyen bejárható adatforráshoz képes indexet létrehozni. Az indexhez szükség van legalább egy névre és egy mezőgyűjteményre, amely mezők közül az egyiket a dokumentum kulcsaként kell megjelölni. Ez az egyes dokumentumok egyedi azonosítására szolgál.

A mezők adattípusokkal és attribútumokkal rendelkeznek. A fent látható jelölőnégyzetek *indexattribútumok*, amelyek a mező használati módját szabályozzák. 

* **Lekérhető**: azt jelenti, hogy a mező a keresési eredmények listájában jelenik meg. A jelölőnégyzet törlésével az egyes mezőket a keresési eredmények korlátjain kívül esőként jelölheti meg, például amikor a mezőket csak szűrőkifejezésekben használják. 
* **Szűrhető**, **Rendezhető** és **Kategorizálható**: azt határozzák meg, hogy egy mező használható-e szűrésben, rendezésben vagy jellemzőalapú navigációs szerkezetben. 
* **Kereshető**: azt jelenti, hogy a mező szerepel a teljes szöveges keresésben. A karakterláncok kereshetők. A numerikus és logikai mezőket gyakran nem kereshetőként jelölik meg. 

Alapértelmezés szerint a varázsló átvizsgálja a adatforrást egyedi azonosítókat keresve, amelyeket felhasználhat a kulcsmező alapjaként. A karakterláncok lekérhetőként és kereshetőként vannak megjelölve. Az egész számok lekérhetőként, szűrhetőként, rendezhetőként és kategorizálhatóként vannak megjelölve.

  ![Létrehozott ingatlanindex][3]

Az index létrehozásához kattintson az **OK** gombra.

#### <a name="step-3-define-the-indexer"></a>3. lépés: Az indexelő meghatározása
Továbbra is az **Adatok importálása** varázslóban maradva kattintson az **Indexelő** > **Név** lehetőségre, és gépelje be az indexelő nevét. 

Ez az objektum egy végrehajtható folyamatot határoz meg. Ismétlődő ütemezés is beállítható, de most használja az alapértelmezett beállítást, és futtassa az indexelőt egyszer, közvetlenül az **OK** gombra való kattintás után.  

  ![ingatlanindexelő][8]

## <a name="check-progress"></a>Folyamat állapotának ellenőrzése
Az adatimportálás felügyeletéhez térjen vissza a szolgáltatás irányítópultjához, görgessen le, és kattintson duplán az **Indexelők** csempére az indexelők listájának megnyitásához. Az újonnan létrehozott indexelőnek szerepelnie kell a listában. Az index állapotának megjelölése „in progress” (folyamatban) vagy sikeres lehet. Emellett az indexelt dokumentumok száma is megjelenik.

   ![Indexelő állapotüzenete][4]

## <a name="a-namequery-indexa-query-the-index"></a><a name="query-index"></a> Az index lekérdezése
Most már rendelkezik egy keresési indexszel, amely készen áll a lekérdezésre. A **Keresési ablak** a portálba épített lekérdezési eszköz. Biztosít egy keresőmezőt, amellyel ellenőrizheti, hogy a keresési eredmények megfelelnek-e a vártaknak. 

   ![Keresési ablak parancs][5]

> [!TIP]
> Az [Azure Search szolgáltatás áttekintő videója](https://channel9.msdn.com/Events/Connect/2016/138) a következő lépéseket mutatja be 6 perc 8 másodperctől kezdve.
>

1. A parancssávon kattintson a **Keresési ablak** elemre.

2. Kattintson a parancssáv **Index módosítása** elemére a *realestate-us-sample* indexre való váltáshoz.

   ![Index és API-parancsok][6]

3. Kattintson a parancssáv **API-verzió beállítása** elemére az elérhető REST API-k megtekintéséhez. Az előzetes verziójú API-k új, általánosan még ki nem adott szolgáltatásokhoz biztosítanak hozzáférést. További utasításig használja az általánosan elérhető verziót (2016-09-01). 

    > [!NOTE]
    > Az [Azure Search REST API](https://docs.microsoft.com/rest/api/searchservice/search-documents) és a [.NET-kódtár](search-howto-dotnet-sdk.md#core-scenarios) teljesen egyenértékűek, de a **Keresési ablak** csak REST-hívások kezelésre alkalmas. Elfogadja az [egyszerű lekérdezési szintaxis](https://docs.microsoft.com/rest/api/searchservice/simple-query-syntax-in-azure-search) és a [teljes Lucene lekérdezéselemző](https://docs.microsoft.com/rest/api/searchservice/lucene-query-syntax-in-azure-search) szintaxisát is, továbbá a [Dokumentum keresése](https://docs.microsoft.com/rest/api/searchservice/search-documents) műveletekben elérhető összes keresési paramétert.
    > 
    > A **Keresési ablak** JSON-formátumban adja vissza az eredményeket, amely részletes és nehezen olvasható lehet, ha a dokumentumok sűrű szerkezettel rendelkeznek. A dokumentumoktól függően előfordulhat, hogy a fontos elemek kinyeréséhez olyan kódot kell írnia, amely képes kezelni a keresési eredményeket.

4. A keresősávba írja be az alábbi lekérdezési karakterláncokat, majd kattintson a **Keresés** gombra.

  ![Példa keresési lekérdezésre][7]

**`search=seattle`** A `search` paraméter kulcsszavas keresés bevitelére használható. Jelen esetben olyan hirdetéseket kapunk vissza a Washington állambeli King megyéből, amelyek tartalmazzák a Seattle kifejezést a dokumentum bármely kereshető mezőjében.

**`search=seattle&facet=beds`** A `facet` paraméter olyan navigációs szerkezetet ad vissza, amelyet továbbíthat egy felhasználói felületi vezérlőnek. Kategóriákat és egy számot ad vissza. Jelen esetben a kategóriák alapját a hálószobák száma jelenti, az egyes kategóriákhoz tartozó dokumentumok vagy egyezések számával együtt. A `"beds"` megadható aspektusként, mert szűrhető és kategorizálható mezőként van megjelölve az indexben, és a tartalmazott értékei (numerikus, 1–5) alkalmasak a hirdetések csoportokba való rendezésére (3 hálószobás, 4 hálószobás ingatlanok hirdetései).  A `&` szimbólum a keresési paraméterek összefűzésére használható.

**`search=seattle&filter=bed gt 3`** A `filter` paraméter olyan eredményeket ad vissza, amelyek megfelelnek a megadott feltételeknek. Ebben az esetben: 3-nál több hálószoba. A szűrőszintaxis egy OData-konstrukció. További információk: [OData-szűrőszintaxis](https://docs.microsoft.com/rest/api/searchservice/odata-expression-syntax-for-azure-search).

**`search=granite countertops&highlight=description`** A találatok kiemelése funkció formázással látja el a kulcsszóval megegyező szöveget, feltéve, hogy vannak egyezések a megadott mezőben. Ha a keresett kifejezés egy leírás mélyén rejlik, a találatok kiemelése funkcióval könnyebben észrevehetővé teheti. Ebben az esetben a `"granite countertops"` formázott kifejezés könnyebben észrevehető a leírás mezőben.

**`search=mice&highlight=description`** A teljes szöveges keresés hasonló szemantikával rendelkező szóalakok keresésére használható. Jelen esetben a keresési eredmények tartalmazzák a „mouse” (egér) kiemelt szöveget az egérlakta házak esetében, pedig a keresési kulcsszó „mice” (egerek) volt. A nyelvészeti elemzés következtében ugyanazon szó különböző alakjai is megjelenhetnek az eredmények között. Az Azure Search szolgáltatás összesen 56, a Lucene-től és Microsoft-tól származó elemzőt támogat. A szolgáltatás alapértelmezés szerint a standard Lucene-elemzőt használja. 

**`search=samamish`** A helytelenül leírt szavak (például a Seattle környékén található Sammamish-fennsíkra utaló „samamish” kifejezés) nem adnak vissza találatokat az átlagos keresések során. A helytelenül leírt szavak kezelésére használhat intelligens keresést. Ennek leírását a következő példában olvashatja.

**`search=samamish~&queryType=full`** Az intelligens keresés a `~` szimbólum megadásával, valamint a teljes lekérdezéselemző használatával engedélyezhető, amely értelmezi és megfelelően elemzi a `~` szintaxist. Alapértelmezés szerint a rendszer az egyszerű lekérdezéselemzőt használja, mivel az gyorsabb, de kérheti a teljes lekérdezéselemző használatát, ha intelligens keresésre, reguláris kifejezésekre, közelségi keresésre vagy egyéb speciális lekérdezési típusokra van szüksége. A teljes lekérdezéselemző által lehetővé tett lekérdezési forgatókönyvekkel kapcsolatos további információk: [Lucene lekérdezési szintaxis az Azure Search szolgáltatásban](https://docs.microsoft.com/rest/api/searchservice/lucene-query-syntax-in-azure-search).

**`search=*`** Az üres keresések mindent visszaadnak. Az üres lekérdezés használatával lekérdezheti az indexben található dokumentumok teljes számát, vagy igény szerint használhatja a teljes dokumentumkészletet szűrésére vagy kategorizálására is. Erről a következő szakaszban olvashat.

**`search=*&filter=geo.distance(location,geography'POINT(-122.13+47.64)')+le+10`** A térinformatikai keresés az [edm.GeographyPoint adattípuson](https://docs.microsoft.com/rest/api/searchservice/supported-data-types) keresztül támogatott a koordinátákat tartalmazó mezők esetében. Ez a lekérdezés minden eredményt szűr a helyzeti adatok alapján, és olyan eredményeket a vissza, amelyek kevesebb, mint 10 kilométerre találhatók a (szélességi és hosszúsági koordinátákként) megadott ponttól. A geosearch egy szűrőtípus, amelynek meghatározása a [OData-szűrőszintaxis](https://docs.microsoft.com/rest/api/searchservice/odata-expression-syntax-for-azure-search) című témakörben olvasható. 

A szűrőkifejezéseket általában $filter paraméterként vannak megadva a `$` karakter használatával. A Keresési ablakban hagyja el a `$` karaktert.

A térinformatikai keresés hasznos lehet, ha a keresőalkalmazás rendelkezik „keresés a közelben” funkcióval vagy térképes navigációt használ. Ez azonban nem teljes szöveges keresés. Ha a városok vagy országok nevére való keresés felhasználói követelmény, akkor a koordináták mellett adjon meg városok vagy országok nevét tartalmazó mezőket.

## <a name="next-steps"></a>Következő lépések

+ Módosítsa az imént létrehozott objektumok bármelyikét. Miután egyszer lefuttatta a varázslót, visszatérhet és megtekintheti vagy módosíthatja az egyes összetevőket: az indexet, az indexelőt és az adatforrást. Bizonyos szerkesztések, például a mező adattípusának módosítása nem engedélyezett az indexben, de a legtöbb tulajdonságok és beállítás módosítható.

  Az egyes összetevők megtekintéséhez kattintson az **Index**, **Indexelő** vagy **Adatforrások** csempére az irányítópulton a meglévő objektumok listájának megjelenítéséhez. Az újjáépítést nem igénylő indexszerkesztési műveletekkel kapcsolatos további információk: [Index frissítése (Azure Search REST API)](https://docs.microsoft.com/rest/api/searchservice/update-index).

+ Próbálja ki az eszközöket és lépéseket más adatforrásokkal is. A `realestate-us-sample` minta-adatkészlet olyan Azure SQL Database-adatbázisból származik, amelyet az Azure Search szolgáltatás be tud járni. Az Azure SQL Database szolgáltatáson kívül az Azure Search az alábbiakat tudja bejárni: Azure Table Storage, Blob Storage, Azure virtuális gépen futtatott SQL Server, illetve DocumentDB. A varázsló az összes fent említett adatforrást támogatja. *Indexelő* használatával könnyedén hozhat létre és tölthet fel indexeket a kódban.

+ Minden más adatforrás leküldéses modellen keresztül támogatott, amely során a kód az új és módosított sorhalmazokat JSON-formátumban küldi le az index számára. További információk: [Dokumentumok hozzáadása, frissítése vagy törlése az Azure Search szolgáltatásban](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents).

A jelen cikkben említett egyéb szolgáltatásokról az alábbi hivatkozások követésével tudhat meg többet:

* [Az indexelők áttekintése](search-indexer-overview.md)
* [Index létrehozása (az index attribútumainak részletes leírását tartalmazza)](https://docs.microsoft.com/rest/api/searchservice/create-index)
* [Keresési ablak](search-explorer.md)
* [Dokumentumok keresése (a lekérdezési szintaxisra vonatkozó példákat tartalmaz)](https://docs.microsoft.com/rest/api/searchservice/search-documents)


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


<!--HONumber=Feb17_HO3-->


