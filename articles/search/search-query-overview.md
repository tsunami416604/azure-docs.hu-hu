---
title: Típusok és létrehozása az Azure Search lekérdezési |} A Microsoft Docs
description: Alapjait, amellyel egy keresési lekérdezést az Azure Search szolgáltatásban paraméterek használatával szeretne szűrni, jelölje ki, és rendezheti az eredményeket.
author: HeidiSteen
manager: cgronlun
ms.author: heidist
services: search
ms.service: search
ms.topic: conceptual
ms.date: 08/03/2018
ms.openlocfilehash: a1dad30148da9f6b322c75fd40dc01098c4d6b63
ms.sourcegitcommit: a2ae233e20e670e2f9e6b75e83253bd301f5067c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/13/2018
ms.locfileid: "42060812"
---
# <a name="query-types-and-composition-in-azure-search"></a>Lekérdezés típusa és létrehozása az Azure Search

Az Azure Search szolgáltatásban a lekérdezés egy oda-vissza művelet teljes leírását. Paraméterek és az index, végrehajtási utasítások a motor és a válasz alakító irányelvek dokumentumok keresése az egyezési feltételek adja meg. Pontosabban megadhatja, melyik mezők a releváns, hogyan kereshet, melyik mezők adja vissza, hogy rendezésére és szűrésére, és így tovább. Nincs megadva, a lekérdezés fut kereshető mező alapján egy teljes szöveges keresési művelet egy tetszőleges sorrendben pontozás nélküli eredményhalmazt visszaadása.

## <a name="a-first-look-at-query-requests"></a>Először tekintse meg a lekérdezésekre vonatkozó kérelmek

Példák lehetnek hasznosak új fogalmak ismertetése. Egy reprezentatív lekérdezés kialakítani, a [REST API](https://docs.microsoft.com/rest/api/searchservice/search-documents), ez például célok a [ingatlan bemutató index](search-get-started-portal.md) és a gyakori paramétereket.

```
{  
    "queryType": "simple" 
    "search": "seattle townhouse* +\"lake\"", 
    "searchFields": "description, city",  
    "count": "true", 
    "select": "listingId, street, status, daysOnMarket, description",
    "top": "10",
    "orderby": "daysOnMarket"
 } 
```

+ **`queryType`** az elemző, amely az Azure Search lehet beállítja a [alapértelmezett egyszerű lekérdezéselemzőt](search-query-simple-examples.md) (a teljes szöveges keresés optimális), vagy a [teljes Lucene lekérdezéselemző](search-query-lucene-examples.md) használt speciális lekérdezési szerkezeteket, például a reguláris kifejezések , közelségi keresésre, intelligens és helyettesítő karakteres keresés, hogy néhányat említsünk.

+ **`search`** a match feltételeinek, általában szöveget, de gyakran kíséri meg a logikai operátorokkal biztosít. Egyetlen önálló kifejezések *kifejezés* lekérdezéseket. Ajánlat idézőjelek között több részből álló lekérdezések vannak *kulcskifejezések* lekérdezéseket. Lehet, hogy a keresés nem definiált, mint a **`search=*`**, de valószínűbb feltételek, kifejezések és hasonló mi jelenik meg a példa operátorok áll.

+ **`searchFields`** nem kötelező, segítségével korlátozhatja az egyes mezők a lekérdezés végrehajtása.

Válaszok a szerepel a lekérdezési paraméterek által is alakítják ki. A példában az eredményhalmaz áll szereplő mezőket a **`select`** utasítást. Ebben a lekérdezésben visszaadott csak az első 10 találat, de **`count`** jelzi, hogy hány dokumentumok teljes felel meg. Ebben a lekérdezésben sorok daysOnMarket alapján vannak rendezve.

Az Azure Search szolgáltatásban a lekérdezés végrehajtása nem mindig felel meg egy index, az api-kulcsot a kérésben megadott való hitelesítése. A REST mindkettő kérelemfejlécek szerepelnek.

### <a name="how-to-run-this-query"></a>Ez a lekérdezés futtatása

A lekérdezés végrehajtásához használja [keresés explorer és a ingatlan bemutató index](search-get-started-portal.md). 

Az explorer keresősávba is illessze be a lekérdezési karakterlánc: `search=seattle townhouse +lake&searchFields=description, city&$count=true&$select=listingId, street, status, daysOnMarket, description&$top=10&$orderby=daysOnMarket`

### <a name="how-query-operations-are-enabled-by-the-index"></a>Hogyan lekérdezési műveletek az index által engedélyezettek

Index tervezési és kialakítási szorosan összekapcsolva az Azure Search lekérdezési. Az alapvető tény előre tudni, hogy a *indexsémát*, az egyes mezők attribútumai, határozza meg, milyen típusú lekérdezési hozhat létre. 

Egy mezőt az index attribútumainak az engedélyezett műveletek – állítsa be, hogy az adott mező kitöltése *kereshető* az indexben *lekérhető* eredmények között, *rendezhető*,  *szűrhető*, és így tovább. A példában a lekérdezési karakterláncban `"$orderby": "daysOnMarket"` csak akkor működik, mert a daysOnMarket mező van megjelölve *rendezhető* az indexsémában. 

![Index definíciója a ingatlan minta](./media/search-query-overview/realestate-sample-index-definition.png "Index definíciója a ingatlan-minta")

A fenti képernyőképen az index attribútumainak az ingatlan-mintához részleges listája. A teljes indexsémát a portálon is megtekintheti. Az index attribútumainak kapcsolatos további információkért lásd: [Index REST API létrehozása](https://docs.microsoft.com/rest/api/searchservice/create-index).

> [!Note]
> Néhány lekérdezés funkció engedélyezve van, index kiterjedő helyett egy mezőnként könnyen. Ezek közé tartozik: [szinonimát térképek](https://docs.microsoft.com/rest/api/searchservice/synonym-map-operations), [egyéni elemzőket](https://docs.microsoft.com/rest/api/searchservice/custom-analyzers-in-azure-search), [javaslattevő szerkezeteket (az automatikus kiegészítés és automatikus kiegészítés)](https://docs.microsoft.com/rest/api/searchservice/suggesters), [kiértékelési logika eredmények rangsorolása](https://docs.microsoft.com/rest/api/searchservice/add-scoring-profiles-to-a-search-index).

## <a name="elements-of-a-query-request"></a>A lekérdezési kérést elemei

Lekérdezések mindig egy egyetlen indextől irányítja. Csatlakozzon az indexek nem, vagy hozzon létre egyéni vagy ideiglenes datové struktury lekérdezés célként. 

A lekérdezési kérést a szükséges elemeket a következő összetevőket tartalmazza:

+ Szolgáltatási végpont és az index dokumentumok gyűjtemény, rögzített és a felhasználó által definiált összetevőket tartalmazó URL-ként kifejezett: **`https://<your-service-name>.search.windows.net/indexes/<your-index-name>/docs`**
+ **`api-version`** (Csak REST) szükség, mert mindig az API-t egynél több verziója érhető el. 
+ **`api-key`**, vagy egy lekérdezés, vagy a felügyeleti api-kulcsot, a kérést a szolgáltatás végzi a hitelesítést.
+ **`queryType`**, egyszerű vagy teljes, amely kihagyható, ha a beépített, alapértelmezett egyszerű szintaxist használja.
+ **`search`** vagy **`filter`** biztosít az egyezés követelményeket, amelyeknek nincs megadva is lehet, ha egy üres keresés végrehajtására vonatkozó szándékát. Lekérdezés mindkétféle ismertetik az egyszerű elemző tekintetében, de még az összetett lekérdezéseket a keresési paraméter szükséges összetett lekérdezési kifejezések átadása.

Minden más keresési paraméterek nem kötelezők. Attribútumok teljes listáját lásd: [Index létrehozása (REST)](https://docs.microsoft.com/rest/api/searchservice/create-index). Közelebbről paraméterek használata feldolgozása közben, lásd: [teljes szöveges keresés működése az Azure Search](search-lucene-query-architecture.md).

## <a name="choose-a-parser-simple--full"></a>Válasszon egy elemző: egyszerű |} teljes

Az Azure Search helyezkedik el, az Apache Lucene felett, és annak eldöntésére, hogy két lekérdezést elemzők az általános és speciális lekérdezések között. Az egyszerű elemző-kérések dolgoznak ki használatával a [egyszerű lekérdezési szintaxis](https://docs.microsoft.com/rest/api/searchservice/Simple-query-syntax-in-Azure-Search), a sebességet és a szabad formátumú szöveges lekérdezésekben hatékonyságának alapértelmezés szerint kijelölt. Ez a szintaxis számos gyakori keresési operátort, például az AND, OR, NOT, a kifejezés, utótag és prioritási operátorokat támogatja.

A [teljes Lucene lekérdezési szintaxis](https://docs.microsoft.com/rest/api/searchservice/Lucene-query-syntax-in-Azure-Search#bkmk_syntax), engedélyezve van, amikor hozzáadja `queryType=full` a kérést, közzéteszi a részeként kifejlesztett, széles körben elfogadott, kifejező lekérdezési nyelv [Apache Lucene](https://lucene.apache.org/core/4_10_2/queryparser/org/apache/lucene/queryparser/classic/package-summary.html). Teljes szintaxisra kiterjeszti az egyszerű szintaxis. Az egyszerű szintaxis írt minden lekérdezés a teljes Lucene-elemzőt alatt fut. 

A következő példák bemutatják a pont: azonos lekérdezése, de különböző queryType beállítások, különböző eredményt adnak. Az első lekérdezés a `^3` a keresési kifejezés részeként kezeli.

```
queryType=simple&search=mountain beach garden ranch^3&searchFields=description&$count=true&$select=listingId, street, status, daysOnMarket, description&$top=10&$orderby=daysOnMarket
```

A teljes Lucene-elemzőt használja ugyanazt a lekérdezést a "ranch", amely felgyorsíthatók a keresési besorolás az eredmények a keresett kifejezést tartalmazó mezőt a terhelésétől értelmezi.

```
queryType=simple&search=mountain beach garden ranch^3&searchFields=description&$count=true&$select=listingId, street, status, daysOnMarket, description&$top=10&$orderby=daysOnMarket
```

<a name="types-of-queries"></a>

## <a name="types-of-queries"></a>A lekérdezések típusai

Az Azure Search lekérdezési típusokra széles skáláját támogatja. 

| Lekérdezés típusa | Használat | Példák és további információ |
|------------|--------|-------------------------------|
| Szabad formátumú szöveges keresés | Keresési paramétert, és mindkét elemző| A teljes szöveges keresés megvizsgálja az összes egy vagy több feltételek *kereshető* az index, és a keresőmotor, például a Google vagy a Bing munka volna várt módon működik. A példában a bevezető a teljes szöveges keresés.<br/><br/>A teljes szöveges keresés épp szöveg elemzése, a standard Lucene-elemzőt használatával (alapértelmezés szerint) a kisbetűs összes feltételek, például az "a" remove-stopszavak. Felülbírálhatja az alapértelmezett [nem angol nyelvű elemzők](https://docs.microsoft.com/rest/api/searchservice/language-support#analyzer-list) vagy [elemzők specializált](https://docs.microsoft.com/rest/api/searchservice/custom-analyzers-in-azure-search#AnalyzerTable) szövegelemzés módosító. Például [kulcsszó](https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/core/KeywordAnalyzer.html) , amely egy mező teljes tartalmát az egyes kezeli. Ez akkor hasznos, adatok, például a zip-kódok, azonosítók és bizonyos termékek neveit. | 
| Szűrt keresése | [OData szűrési kifejezés](https://docs.microsoft.com/rest/api/searchservice/OData-Expression-Syntax-for-Azure-Search) és vagy elemző | Szűrő lekérdezés egy logikai kifejezés kiértékelése összes *szűrhető* index mezőire. Keres a szűrő-lekérdezés megegyezik a kisbetű/nagybetű megkülönböztetése a karakterlánc-mezők például mező pontos tartalmával kell. Egy másik különbség, hogy az OData-szűrőszintaxis ki, a szűrőlekérdezések. <br/>[Szűrési kifejezés példa](search-query-simple-examples.md#example-3-filter-queries) |
| Földrajzi keresés | [Edm.GeographyPoint típus](https://docs.microsoft.com/rest/api/searchservice/supported-data-types) a mezőt, a szűrőkifejezés és vagy elemző | Tároló-Edm.GeographyPoint kellene mezőt koordináták, a "Keresés a közelben" vagy a térképes keressen vezérlőket. <br/>[Földrajzi keresés példa](search-query-simple-examples.md#example-5-geo-search)|
| Tartomány keresése | szűrési kifejezés, és egyszerű elemző | Az Azure Search szolgáltatásban lekérdezések beépített a szűrő paraméter használatával. <br/>[Példa a tartomány szűrő](search-query-simple-examples.md#example-4-range-filters) | 
| [Mező-on belüli szűrése](https://docs.microsoft.com/rest/api/searchservice/Lucene-query-syntax-in-Azure-Search#bkmk_fields) | Keresési paramétert, és teljes elemző | Hozhat létre egy egyetlen mező célzó összetett lekérdezési kifejezésben. <br/>[Mező-on belüli szűrési példát](search-query-lucene-examples.md#example-2-intra-field-filtering) |
| [intelligens keresést](https://docs.microsoft.com/rest/api/searchservice/Lucene-query-syntax-in-Azure-Search#bkmk_fuzzy) | Keresési paramétert, és teljes elemző | Megegyezik a hasonló konstrukció kellene vagy helyesírási feltételeket. <br/>[Intelligens keresést](search-query-lucene-examples.md#example-3-fuzzy-search) |
| [közelségi keresésre](https://docs.microsoft.com/rest/api/searchservice/Lucene-query-syntax-in-Azure-Search#bkmk_proximity) | Keresési paramétert, és teljes elemző | A dokumentum egymás közelében lévő keresése feltételek. <br/>[Közelségi keresés példa](search-query-lucene-examples.md#example-4-proximity-search) |
| [kiemelési távú](https://docs.microsoft.com/rest/api/searchservice/Lucene-query-syntax-in-Azure-Search#bkmk_termboost) | Keresési paramétert, és teljes elemző | Ha a gyorsított időszak alatt, viszonyítva, míg mások nem tartalmaz, rangsorolja a magasabb dokumentumot. <br/>[Kifejezés-kiemelés példa](search-query-lucene-examples.md#example-5-term-boosting) |
| [reguláris kifejezés keresése](https://docs.microsoft.com/rest/api/searchservice/Lucene-query-syntax-in-Azure-Search#bkmk_regex) | Keresési paramétert, és teljes elemző | Egyezés reguláris kifejezés tartalma alapján. <br/>[Reguláris kifejezés példa](search-query-lucene-examples.md#example-6-regex) |
|  [helyettesítő karakteres vagy előtag keresése](https://docs.microsoft.com/rest/api/searchservice/Lucene-query-syntax-in-Azure-Search#bkmk_wildcard) | Keresési paramétert, és teljes elemző | Egyezés alapján előtag és hullámos vonallal (`~`) vagy önálló karakter (`?`). <br/>[Helyettesítő karakteres keresés példa](search-query-lucene-examples.md#example-7-wildcard-search) |

## <a name="manage-search-results"></a>Keresési eredmények kezelése 

Bár a .NET API-k használatakor szerializálási van-e építve a lekérdezés eredményeit a REST API-t a JSON-dokumentumok formájában átvitt. Átalakíthatja eredmények a Query paraméterek beállításával a válasz egyedi mezők kiválasztása.

A lekérdezési paraméterek segítségével a következőképpen állítsa be az eredmény szerkezete:

+ Korlátozása vagy a kötegelés a dokumentumok száma, az eredmények között (alapértelmezés szerint 50)
+ Az eredmények tartalmazzák a mezők kiválasztása
+ Rendezési sorrend megadása
+ Hozzáadásának nyomja le emeli ki, hogy felhívja a figyelmet a megfelelő feltételek törzsében a keresési eredmények

### <a name="tips-for-unexpected-results"></a>Tippek a nem várt eredmények

Néha előfordul az anyag nem eredmény szerkezete jsou váratlan. Ha a lekérdezés eredményét nem várt megtekintéséhez, próbálkozzon ezek lekérdezés a módosítások megtekintéséhez, ha az eredmények javítása:

+ Változás **`searchMode=any`** (alapértelmezett) **`searchMode=all`** kötelező helyett a kritériumok összes feltételeknek megfelelő találat. Ez akkor különösen igaz, ha a logikai operátorokkal részét képezik a lekérdezést.

+ Módosítsa a lekérdezési módszer, ha a szöveg- vagy lexikai elemzés szükséges, de a lekérdezés típusa kizárja nyelvi feldolgozás. A teljes szöveges keresésben szöveges vagy lexikai elemzés automatikusa kijavítja a helyesírási hibák, egyes számban-plural word űrlapokat, és akár szabálytalan műveletek vagy főneveket. Például bizonyos lekérdezések intelligens vagy helyettesítő karakteres keresés, szövegelemzés nem a lekérdezés elemzési folyamat részét. Bizonyos esetekben a reguláris kifejezések áthidaló használták fel. 

### <a name="paging-results"></a>Lapozás eredményei
A keresési eredmények lapozása az Azure Search segítségével könnyen megvalósítható. Használatával a **`top`** és **`skip`** paraméterek, zökkenőmentesen adhat ki olyan keresési kéréseket, amelyek a keresési eredmények teljes készletében fogadhatja a kezelhető, rendezett alkészletek, amely könnyen engedélyezheti a helyes keresési felhasználói gyakorlat. Az eredmények kisebb alkészleteinek fogadásakor a keresési eredmények teljes készletében lévő dokumentumok darabszámát is megkaphatja.

A keresési eredmények lapozásáról további információkat a [Keresési eredmények lapozása az Azure Search szolgáltatásban](search-pagination-page-layout.md) című cikkben talál.

### <a name="ordering-results"></a>Az eredmények rendezése
A keresési lekérdezés eredményeinek fogadásakor kérheti, hogy az Azure Search az eredményeket egy adott mezőben lévő érték szerint rendezve szolgáltassa. Alapértelmezés szerint az Azure Search a keresési eredményeket az egyes dokumentumok keresési pontszáma szent rangsorolja, amelyet a [TF-IDF](https://en.wikipedia.org/wiki/Tf%E2%80%93idf) állományból származtat.

Ha azt szeretné, hogy vissza a keresési pontszámtól eltérő érték szerint rendezve az eredményeket, használhatja az Azure Search a **`orderby`** keresési paramétert. Értékét megadhatja a **`orderby`** paraméter tartalmazza a mezőneveket és a hívások a [  **`geo.distance()` függvény** ](https://docs.microsoft.com/rest/api/searchservice/OData-Expression-Syntax-for-Azure-Search) térinformatikai értékek esetében. Minden egyes kifejezések után által `asc` jelzi, hogy eredményeket növekvő sorrendben az igényelt és **`desc`** jelzi, hogy az eredmények csökkenő sorrendben kérik. Alapértelmezés szerint a rangsorolás növekvő sorrendben történik.


### <a name="hit-highlighting"></a>Találatok kiemelése
Az Azure Search a keresési lekérdezésnek megfelelő keresési eredmények pontos részének tárgyalta megkönnyíti a használatával a **`highlight`**, **`highlightPreTag`**, és **`highlightPostTag`** paramétereket. Megadhatja, hogy mely *searchable* (kereshető) mezők esetében kívánja bekapcsolni az egyező szöveg kiemelését, valamint az Azure által visszaadott egyező szöveg elejére és végére hozzáfűzni kívánt sztringcímkéket.

## <a name="apis-and-tools-for-testing"></a>API-k és tesztelési eszközök

A következő táblázat felsorolja az API-k és a lekérdezések elküldése az eszköz-alapú megközelítéseken.

| Módszer | Leírás |
|-------------|-------------|
| [A SearchIndexClient (.NET)](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.searchindexclient?view=azure-dotnet) | Az Azure Search-index lekérdezése használható ügyfél.  <br/>[Részletek](search-howto-dotnet-sdk.md#core-scenarios)  |
| [Dokumentumok keresése (REST API)](https://docs.microsoft.com/rest/api/searchservice/search-documents) | GET vagy POST metódus az indexen, további bemeneti lekérdezési paraméterek használatával.  |
| [A fiddler, Postman vagy más HTTP tesztelési eszköz](search-fiddler.md) | Azt ismerteti, hogyan állítható be egy kérés fejlécéhez és a szervezet az Azure Search-lekérdezések küldési.  |
| [Az Azure Portalon a keresési ablak](search-explorer.md) | Itt egy keresősáv és a lehetőségek a kiválasztott index és api-verziót. A eredmény JSON-dokumentumok formájában. <br/>[Részletek](search-get-started-portal.md#query-index) | 

## <a name="see-also"></a>Lásd még

+ [Teljes szöveges keresés működése az Azure Search (lekérdezéselemzési architektúra)](search-lucene-query-architecture.md)
+ [A keresési ablak](search-explorer.md)
+ [Lekérdezése a .NET-ben](search-query-dotnet.md)
+ [Lekérdezése a REST-ben](search-query-rest-api.md)
