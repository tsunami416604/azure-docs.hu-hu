---
title: Lekérdezéstípusok és összeállítás
titleSuffix: Azure Cognitive Search
description: Alapjai a keresési lekérdezés az Azure Cognitive Search, paraméterek használatával az eredmények szűréséhez, kiválasztásához és rendezéséhez.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 902f3628235cc8a4524ddc4dd8a5327592fe47e7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79282821"
---
# <a name="query-types-and-composition-in-azure-cognitive-search"></a>Lekérdezéstípusok és -összetétel az Azure Cognitive Search szolgáltatásban

Az Azure Cognitive Search, a lekérdezés egy teljes specifikációja egy oda-vissza művelet. A kérelem paraméterei egyezési feltételeket biztosítanak az indexben lévő dokumentumok kereséséhez, amelyek mezőket tartalmaznak vagy zárnak ki, a motornak átadott végrehajtási utasításokat, valamint a válasz formázására vonatkozó utasításokat. Meghatározatlan`search=*`( ), a lekérdezés teljes szöveges keresési műveletként fut az összes kereshető mezőn, és tetszőleges sorrendben ad vissza egy nem pontozott eredményt.

A következő példa a [REST API-ban](https://docs.microsoft.com/rest/api/searchservice/search-documents)készült reprezentatív lekérdezés. Ez a példa a [hotelek bemutatóindexét](search-get-started-portal.md) célozza meg, és közös paramétereket tartalmaz.

```
{
    "queryType": "simple" 
    "search": "+New York +restaurant",
    "searchFields": "Description, Address/City, Tags",
    "select": "HotelId, HotelName, Description, Rating, Address/City, Tags",
    "top": "10",
    "count": "true",
    "orderby": "Rating desc"
}
```

+ **`queryType`** Beállítja az elemzőt, amely vagy az [alapértelmezett egyszerű lekérdezéselemző](search-query-simple-examples.md) (optimális a teljes szöveges kereséshez), vagy a [teljes Lucene lekérdezéselemzőt,](search-query-lucene-examples.md) amelyet speciális lekérdezésszerkezetekhez , például reguláris kifejezésekhez, közelségkereséshez, fuzzy és helyettesítő karakteres kereséshez használnak, hogy csak néhányat említsünk.

+ **`search`** megadja az egyezési feltételeket, általában szöveget, de gyakran logikai operátorokkal együtt. Az önálló kifejezések *term* kifejezéslekérdezések. Az idéződő többrészes lekérdezések *kulcskifejezés-lekérdezések.* A keresés meghatározatlan lehet, **`search=*`** mint a ban, de valószínűbb, hogy a példában szereplőhöz hasonló kifejezésekből, kifejezésekből és operátorokból áll.

+ **`searchFields`** lekérdezés-végrehajtást meghatározott mezőkre korlátozza. Az indexsémában *kereshetőként* hozzárendelt bármely mező erre a paraméterre jelölt.

A válaszokat a lekérdezésben megadott paraméterek is alakítják. A példában az eredményhalmaz az **`select`** utasításban felsorolt mezőkből áll. Csak a *visszakereshetőként* megjelölt mezők használhatók $select utasításban. Emellett csak a **`top`** 10 lekérés tért vissza **`count`** ebben a lekérdezésben, miközben megmondja, hogy hány dokumentum egyezik összességében, ami több lehet, mint a visszaadott. Ebben a lekérdezésben a sorok minősítés szerint vannak rendezve csökkenő sorrendben.

Az Azure Cognitive Search, lekérdezés végrehajtása mindig egy index, hitelesítve egy api-kulcs a kérelemben biztosított. A REST-ben mindkettő a kérelemfejlécekben található.

### <a name="how-to-run-this-query"></a>A lekérdezés futtatása

A lekérdezés végrehajtásához használja [a Kereséskezelőt és a hotelek bemutatóindexét.](search-get-started-portal.md) 

Ezt a lekérdezési karakterláncot beillesztheti a felfedező keresősávjába:`search=+"New York" +restaurant&searchFields=Description, Address/City, Tags&$select=HotelId, HotelName, Description, Rating, Address/City, Tags&$top=10&$orderby=Rating desc&$count=true`

## <a name="how-query-operations-are-enabled-by-the-index"></a>A lekérdezési műveletek index általi engedélyezésének

Az Index-tervezés és a lekérdezéstervezés szorosan kapcsolódnak az Azure Cognitive Search szolgáltatáshoz. Fontos tény, hogy előre tudni, hogy az *index séma*, attribútumokkal az egyes mezőkben, határozza meg, hogy milyen típusú lekérdezést lehet építeni. 

A mező indexattribútumainak indexe határozza meg az engedélyezett műveleteket – függetlenül attól, hogy egy mező *kereshető-e* az indexben, *visszakereshető-e* az eredményekközött, *rendezhető,* *szűrhető*stb. A példa lekérdezési `"$orderby": "Rating"` karakterláncban csak akkor működik, mert a Minősítés mező *rendezhetőként* van megjelölve az indexsémában. 

![A szállodai minta indexmeghatározása](./media/search-query-overview/hotel-sample-index-definition.png "A szállodai minta indexmeghatározása")

A fenti képernyőkép a szállodai minta indexattribútumainak részleges listája. Megtekintheti a teljes indexsémát a portálon. Az indexattribútumokról további információt az [Index REST API létrehozása című témakörben](https://docs.microsoft.com/rest/api/searchservice/create-index)talál.

> [!Note]
> Néhány lekérdezési funkció indexszintű, nem pedig mezőalapú alapon engedélyezett. Ezek a képességek a következők: [szinonimatérképek](search-synonyms.md), [egyéni elemzők](index-add-custom-analyzers.md), [javaslatajánlói konstrukciók (automatikus kiegészítéshez és javasolt lekérdezésekhez),](index-add-suggesters.md) [pontozási logika az eredmények rangsorolásához.](index-add-scoring-profiles.md)

## <a name="elements-of-a-query-request"></a>Lekérdezési kérelem elemei

A lekérdezések mindig egyetlen indexre irányulnak. Nem illeszthet indexeket, és nem hozhat létre egyéni vagy ideiglenes adatstruktúrákat lekérdezési célként. 

A lekérdezési kérelem kötelező elemei a következő összetevőket tartalmazzák:

+ Szolgáltatásvégpont- és indexbizonylat-gyűjtemény, rögzített és felhasználó által definiált összetevőket tartalmazó URL-címben kifejezve:**`https://<your-service-name>.search.windows.net/indexes/<your-index-name>/docs`**
+ **`api-version`**(CSAK REST) azért szükséges, mert az API-nak egynél több verziója érhető el mindenkor. 
+ **`api-key`**, vagy egy lekérdezés vagy felügyeleti api-kulcs, hitelesíti a kérelmet a szolgáltatás.
+ **`queryType`**, legyen egyszerű vagy teljes, amely elhagyható, ha a beépített alapértelmezett egyszerű szintaxist használja.
+ **`search`** vagy **`filter`** megadja az egyezési feltételeket, amelyek meghatározatlanok, ha üres keresést szeretne végrehajtani. Mindkét lekérdezéstípust az egyszerű elemző vel tárgyaljuk, de még a speciális lekérdezések is megkövetelik a keresési paramétert az összetett lekérdezési kifejezések átadásához.

Az összes többi keresési paraméter megadása nem kötelező. Az attribútumok teljes listáját az [Index (REST) létrehozása című](https://docs.microsoft.com/rest/api/searchservice/create-index)témakörben találja. A paraméterek feldolgozás közbeni használatával a [Teljes szöveges keresés működése](search-lucene-query-architecture.md)az Azure Cognitive Search alkalmazásban című témakörben olvashat közelebbről.

## <a name="choose-apis-and-tools"></a>API-k és eszközök kiválasztása

Az alábbi táblázat a lekérdezések elküldésével kapcsolatos API-kat és eszközalapú megközelítéseket sorolja fel.

| Módszertan | Leírás |
|-------------|-------------|
| [Keresési ablak (portál)](search-explorer.md) | Keresési sávot, valamint index- és api-verziók kiválasztásának beállításait tartalmazza. Az eredmények JSON-dokumentumként kerülnek visszaadásra. Feltáráshoz, teszteléshez és érvényesítéshez ajánlott. <br/>[További információ.](search-get-started-portal.md#query-index) | 
| [Postás vagy más REST-eszközök](search-get-started-postman.md) | A webes tesztelési eszközök kiváló választást jelentenek a REST-hívások megfogalmazásához. A REST API támogatja az Azure Cognitive Search minden lehetséges műveletét. Ebből a cikkből megtudhatja, hogyan állíthat be egy HTTP-kérelem fejlécét és törzsét az Azure Cognitive Search kéréseinek küldéséhez.  |
| [SearchIndexClient (.NET)](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.searchindexclient?view=azure-dotnet) | Az Azure Cognitive Search-index lekérdezésére használható ügyfél.  <br/>[További információ.](search-howto-dotnet-sdk.md#core-scenarios)  |
| [Keresési dokumentumok (REST API)](https://docs.microsoft.com/rest/api/searchservice/search-documents) | Get vagy POST metódusok egy index, lekérdezési paraméterek használatával további bemenet.  |

## <a name="choose-a-parser-simple--full"></a>Válasszon egy elemző: egyszerű | Teljes

Az Azure Cognitive Search az Apache Lucene tetején helyezkedik el, és két lekérdezéselemző közül választhat a tipikus és speciális lekérdezések kezeléséhez. Az egyszerű elemzőt használó kérelmek megfogalmazása az [egyszerű lekérdezésszintaxissal](query-simple-syntax.md)történik, amely et a szabad formátumú szöveges lekérdezések gyorsasága és hatékonysága alapértelmezettként jelöli meg. Ez a szintaxis számos gyakori keresési operátort támogat, beleértve az AND, OR, NOT, phrase, utótagot és elsőbbségi operátorokat.

A [teljes Lucene lekérdezés szintaxisa](query-Lucene-syntax.md#bkmk_syntax), amely engedélyezve van, amikor hozzáad `queryType=full` a kérelemhez, megjeleníti az Apache [Lucene](https://lucene.apache.org/core/6_6_1/queryparser/org/apache/lucene/queryparser/classic/package-summary.html)részeként kifejlesztett széles körben elfogadott és kifejező lekérdezési nyelvet. A teljes szintaxis kiterjeszti az egyszerű szintaxist. Az egyszerű szintaxishoz írt lekérdezések a teljes Lucene-elemző alatt futnak. 

A következő példák a pontot mutatják be: ugyanaz a lekérdezés, de különböző queryType-beállításokkal eltérő eredményeket eredményeznek. Az első lekérdezésben `^3` `historic` az utánt a rendszer a keresési kifejezés részeként kezeli. A lekérdezés legmagasabb anamtalánya a "Marquis Plaza & Suites", amelynek leírása *az óceán*

```
queryType=simple&search=ocean historic^3&searchFields=Description, Tags&$select=HotelId, HotelName, Tags, Description&$count=true
```

Ugyanaz a lekérdezés a teljes Lucene-elemzőhasználatával helyszíni `^3` kifejezésemlékeztetőként értelmezi. Az elemzők közötti váltás megváltoztatja a rangsort, és a *történelmi* kifejezést tartalmazó eredmények a csúcsra lépnek.

```
queryType=full&search=ocean historic^3&searchFields=Description, Tags&$select=HotelId, HotelName, Tags, Description&$count=true
```

<a name="types-of-queries"></a>

## <a name="types-of-queries"></a>A lekérdezések típusai

Az Azure Cognitive Search a lekérdezéstípusok széles körét támogatja. 

| Lekérdezés típusa | Használat | Példák és további információk |
|------------|--------|-------------------------------|
| Szabad formátumú szövegkeresés | Keresési paraméter és vagy elemző| A teljes szöveges keresés egy vagy több kifejezést keres az index összes *kereshető* mezőjében, és úgy működik, ahogy azt egy olyan keresőmotortól elvárná, mint a Google vagy a Bing. A bevezetőben látható példa a teljes szöveges keresés.<br/><br/>A teljes szöveges keresés szövegelemzésen megy keresztül a szabványos Lucene analizátor segítségével (alapértelmezés szerint) az összes kifejezés kisbetűsé, távolítsa el a stop szavakat, mint például a "a". Az alapértelmezett értékeket felülbírálhatja a [nem angol nyelvű elemzőkkel](index-add-language-analyzers.md#language-analyzer-list) vagy a szövegelemzést [módosító speciális nyelvfüggetlen elemzőkkel.](index-add-custom-analyzers.md#AnalyzerTable) Ilyen [kulcsszó,](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/KeywordAnalyzer.html) amely a mező teljes tartalmát egyetlen tokenként kezeli. Ez olyan adatok esetén hasznos, mint az irányítószámok, azonosítók és néhány terméknév. | 
| Szűrt keresés | [OData szűrőkifejezés](query-odata-filter-orderby-syntax.md) és vagy elemző | A szűrőlekérdezések logikai kifejezést értékelnek az index összes *szűrhető* mezőjére. A kereséssel ellentétben a szűrőlekérdezés megfelel a mező pontos tartalmának, beleértve a kis- és nagybetűk megkülönböztetését a karakterláncmezőkön. Egy másik különbség az, hogy a szűrőlekérdezések OData szintaxisban vannak kifejezve. <br/>[Példa kifejezés szűrésére](search-query-simple-examples.md#example-3-filter-queries) |
| Földrajzi keresés | [Edm.GeographyPoint típus](https://docs.microsoft.com/rest/api/searchservice/supported-data-types) a mezőben, szűrőkifejezés és elemző | Az Edm.GeographyPoint mezőben tárolt koordinátákat a "keresés a közelemben" vagy a térképalapú keresési vezérlőkhasználják. <br/>[Példa földrajzi keresésre](search-query-simple-examples.md#example-5-geo-search)|
| Tartománykeresés | szűrőkifejezés és egyszerű elemző | Az Azure Cognitive Search, tartomány lekérdezések a szűrő paraméter használatával épülnek fel. <br/>[Példa tartományszűrőre](search-query-simple-examples.md#example-4-range-filters) | 
| [Mezőalapú keresés](query-lucene-syntax.md#bkmk_fields) | Keresési paraméter és Teljes elemző | Összetett lekérdezési kifejezés létrehozása, amely egyetlen mezőt céloz meg. <br/>[Példa mezőalapú keresési](search-query-lucene-examples.md#example-2-fielded-search) |
| [intelligens keresés](query-lucene-syntax.md#bkmk_fuzzy) | Keresési paraméter és Teljes elemző | Hasonló konstrukciójú vagy helyesírású kifejezésekkel egyezik. <br/>[Homályos keresési példa](search-query-lucene-examples.md#example-3-fuzzy-search) |
| [közelségkeresés](query-lucene-syntax.md#bkmk_proximity) | Keresési paraméter és Teljes elemző | Megkeresi azokat a kifejezéseket, amelyek közel vannak egymáshoz a dokumentumban. <br/>[Példa közelségkeresési](search-query-lucene-examples.md#example-4-proximity-search) |
| [kifejezés növelése](query-lucene-syntax.md#bkmk_termboost) | Keresési paraméter és Teljes elemző | A dokumentumot magasabbra rangsorolja, ha az tartalmazza a kiemelt kifejezést, azokhoz képest, akik nem. <br/>[Példa a kifejezéskiemelésre](search-query-lucene-examples.md#example-5-term-boosting) |
| [reguláris kifejezés keresése](query-lucene-syntax.md#bkmk_regex) | Keresési paraméter és Teljes elemző | Egyezések a reguláris kifejezés tartalma alapján. <br/>[Példa reguláris kifejezésre](search-query-lucene-examples.md#example-6-regex) |
|  [helyettesítő karakter vagy előtag keresése](query-lucene-syntax.md#bkmk_wildcard) | Keresési paraméter és Teljes elemző | Egyezések előtag és`~`tilde (`?`) vagy egyetlen karakter ( alapján. <br/>[Példa helyettesítő karakteres keresésre](search-query-lucene-examples.md#example-7-wildcard-search) |

## <a name="manage-search-results"></a>Keresési eredmények kezelése 

A lekérdezési eredmények a REST API-ban JSON-dokumentumokként kerülnek streamelésre, bár ha .NET API-kat használ, a szerializálás be van építve. Az eredményeket úgy alakíthatja ki, hogy paramétereket állít be a lekérdezésre, és kiválasztja a válasz adott mezőit.

A lekérdezés paraméterei az eredményhalmaz szerkezetére használhatók a következő módokon:

+ Az eredményekben lévő dokumentumok számának korlátozása vagy kötegelése (alapértelmezés szerint 50)
+ Az eredményekbe felvenni kívánt mezők kiválasztása
+ Rendezési sorrend beállítása
+ Találati kiemelések hozzáadása, hogy felhívja a figyelmet a keresési eredmények törzsében lévő egyezési kifejezésekre

### <a name="tips-for-unexpected-results"></a>Tippek a váratlan eredményekhez

Alkalmanként az anyag és nem az eredmények szerkezete váratlan. Ha a lekérdezési eredmények nem az, amit elvár, megpróbálhatja ezeket a lekérdezési módosításokat, hogy lássa, javulnak-e az eredmények:

+ Módosítsa **`searchMode=any`** (alapértelmezett) **`searchMode=all`** ha a feltételek bármelyike helyett az összes feltételre vonatkozó egyezéseket szeretne megkövetelni. Ez különösen akkor igaz, ha logikai operátorok szerepelnek a lekérdezésben.

+ Módosítsa a lekérdezési technikát, ha szöveg vagy lexikai elemzés szükséges, de a lekérdezés típusa kizárja a nyelvi feldolgozást. A teljes szöveges keresésben a szöveg- vagy lexikai elemzés automatikusan kijavítja a helyesírási hibákat, az egyes számú többes számú szóformákat, sőt a szabálytalan igéket vagy főneveket is. Egyes lekérdezések, például az intelligens vagy helyettesítő karakteres keresés esetén a szövegelemzés nem része a lekérdezéselemzési folyamatnak. Bizonyos esetekben a reguláris kifejezések et használták kerülő megoldásként. 

### <a name="paging-results"></a>Lapozás eredményei
Az Azure Cognitive Search megkönnyíti a keresési eredmények lapozásának megvalósítását. A és **`top`** **`skip`** a paraméterek használatával zökkenőmentesen kiállíthat olyan keresési kérelmeket, amelyek lehetővé teszik a keresési eredmények teljes készletének kezelhető, rendezett részhalmazokban történő fogadását, amelyek könnyen lehetővé teszik a helyes keresési felhasználói felület gyakorlatát. Az eredmények kisebb alkészleteinek fogadásakor a keresési eredmények teljes készletében lévő dokumentumok darabszámát is megkaphatja.

A keresési eredmények lapozásáról a Keresési eredmények lapozása című cikkben olvashat [bővebben: Keresési eredmények lapozása az Azure Cognitive Search szolgáltatásban.](search-pagination-page-layout.md)

### <a name="ordering-results"></a>Az eredmények rendezése
Amikor egy keresési lekérdezés eredményeit kapja, kérheti, hogy az Azure Cognitive Search egy adott mező ben lévő értékek szerint rendezett eredményeket szolgálki. Alapértelmezés szerint az Azure Cognitive Search a [TF-IDF-ből](https://en.wikipedia.org/wiki/Tf%E2%80%93idf)származó keresési pontszám alapján rendeli a keresési eredményeket.

Ha azt szeretné, hogy az Azure Cognitive Search a keresési pontszámtól **`orderby`** eltérő érték alapján adja vissza az eredményeket, használhatja a keresési paramétert. Megadhatja a paraméter **`orderby`** értékét, hogy tartalmazza a mezőneveket és a [** `geo.distance()` függvény**](query-odata-filter-orderby-syntax.md) hívásait a térinformatikai értékekhez. Minden kifejezés követheti `asc` azt jelzi, hogy az eredmények **`desc`** et növekvő sorrendben kérik, és azt jelzi, hogy az eredményeket csökkenő sorrendben kérik. Alapértelmezés szerint a rangsorolás növekvő sorrendben történik.


### <a name="hit-highlighting"></a>Találatok kiemelése
Az Azure Cognitive Search, hangsúlyozva a keresési eredmények pontos, amely megfelel a **`highlight`** **`highlightPreTag`** keresési **`highlightPostTag`** lekérdezés egyszerűen használja a , és a paraméterek használatával. Megadhatja, hogy mely *kereshető* mezőket kell az egyező szöveg et, valamint adja meg a pontos karakterlánc-címkék hozzáfűzése a kezdete és vége az Azure Cognitive Search visszaad.

## <a name="see-also"></a>Lásd még

+ [A teljes szöveges keresés működése az Azure Cognitive Search szolgáltatásban (lekérdezéselemzési architektúra)](search-lucene-query-architecture.md)
+ [Kereséskezelő](search-explorer.md)
+ [Lekérdezés a .NET-ben](search-query-dotnet.md)
+ [Lekérdezés a REST-ben](search-create-index-rest-api.md)
