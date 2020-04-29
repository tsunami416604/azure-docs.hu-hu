---
title: Lekérdezéstípusok és összeállítás
titleSuffix: Azure Cognitive Search
description: Alapvető tudnivalók keresési lekérdezés létrehozásához az Azure Cognitive Searchban, paraméterek használatával szűrheti, kiválaszthatja és rendezheti az eredményeket.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 902f3628235cc8a4524ddc4dd8a5327592fe47e7
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "79282821"
---
# <a name="query-types-and-composition-in-azure-cognitive-search"></a>Az Azure Cognitive Search lekérdezési típusai és összetétele

Az Azure Cognitive Search-ban a lekérdezés egy kerekítési művelet teljes leírása. A kérelemben szereplő paraméterek egyeztetési feltételeket biztosítanak a dokumentumok indexben való kereséséhez, a belefoglalni vagy kizárni kívánt mezőket, a motornak átadott végrehajtási utasításokat, valamint a válasz alakításához szükséges irányelveket. Meghatározatlan (`search=*`), a lekérdezés teljes szöveges keresési műveletként futtatja az összes kereshető mezőt, és tetszőleges sorrendben adja vissza a pontszám nélküli eredményt.

Az alábbi példa egy, a [Rest APIban](https://docs.microsoft.com/rest/api/searchservice/search-documents)létrehozott reprezentatív lekérdezés. Ez a példa a [Hotels bemutató indexét](search-get-started-portal.md) célozza meg, és általános paramétereket tartalmaz.

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

+ **`queryType`** Beállítja az elemzőt, amely az [alapértelmezett egyszerű lekérdezés-elemző](search-query-simple-examples.md) (optimális a teljes szöveges kereséshez), vagy a speciális lekérdezési felépítéshez használt [teljes Lucene lekérdezés-elemző](search-query-lucene-examples.md) , például a reguláris kifejezések, a közelségi keresés, a fuzzy és a helyettesítő karakterek keresése, hogy csak néhányat említsünk.

+ **`search`** Megadja az egyeztetési feltételeket, általában szöveget, de gyakran logikai operátorok kísérik. Az önálló különálló kifejezések *kifejezéses* lekérdezések. Az idézőjelek közé zárt, több részből álló lekérdezések *kulcsfontosságú kifejezéssel* rendelkező lekérdezések. A keresés nem definiálható, például a alkalmazásban **`search=*`**, de valószínűbb kifejezéseket, kifejezéseket és operátorokat, amelyek a példában láthatóhoz hasonlóak.

+ **`searchFields`** a lekérdezés végrehajtásának korlátozása adott mezőkre. Az index sémában *kereshetőként* megadott bármely mező erre a paraméterre van jelölt.

A válaszokat a lekérdezésben foglalt paraméterek is megformázzák. A példában az eredményhalmaz az **`select`** utasításban felsorolt mezőkből áll. Egy $select utasításban csak lekérhető *mezők használhatók* . Ezen felül csak a **`top`** 10 találatot adja vissza a rendszer, miközben **`count`** azt jelzi, hogy hány dokumentum felel meg a teljesnek, ami a visszaadott értéknél több is lehet. Ebben a lekérdezésben a sorok rendezése csökkenő sorrendben történik.

Az Azure Cognitive Search-ban a lekérdezés-végrehajtás mindig egy indexen alapul, a kérelemben megadott API-kulccsal hitelesítve. A REST szolgáltatásban mindkettő a kérelem fejlécében van megadva.

### <a name="how-to-run-this-query"></a>A lekérdezés futtatása

A lekérdezés végrehajtásához használja [a keresési Explorert és a Hotels bemutató indexét](search-get-started-portal.md). 

Ezt a lekérdezési karakterláncot beillesztheti az Explorer keresősávba:`search=+"New York" +restaurant&searchFields=Description, Address/City, Tags&$select=HotelId, HotelName, Description, Rating, Address/City, Tags&$top=10&$orderby=Rating desc&$count=true`

## <a name="how-query-operations-are-enabled-by-the-index"></a>A lekérdezési műveletek engedélyezése az index alapján

Az index tervezési és lekérdezési kialakítása szorosan összekapcsolható az Azure Cognitive Searchban. Fontos, hogy tudd elölről, hogy az *index séma*, amely az egyes mezők attribútumait határozza meg, meghatározza, hogy milyen típusú lekérdezést hozhat létre. 

Egy mező indexelése az engedélyezett műveletekkel – azt határozza meg, hogy egy mező *kereshető* -e az indexben, lekérhető az *eredmények között,* *rendezhető*, *szűrhető*és így tovább. A példában a lekérdezési karakterláncban csak a működik, `"$orderby": "Rating"` mert a minősítés mező az index sémában *rendezhető* van megjelölve. 

![A szállodai minta index-definíciója](./media/search-query-overview/hotel-sample-index-definition.png "A szállodai minta index-definíciója")

A fenti képernyőkép a Hotels-minta index-attribútumainak részleges listája. A teljes index séma a portálon tekinthető meg. További információ az index attribútumairól: [create index REST API](https://docs.microsoft.com/rest/api/searchservice/create-index).

> [!Note]
> Egyes lekérdezési funkciók esetében a teljes indexre van engedélyezve, nem pedig a mező alapján. Ezek a képességek többek között a következők: [szinonimák térképek](search-synonyms.md), [Egyéni elemzők](index-add-custom-analyzers.md), [javaslati szerkezetek (automatikus kiegészítés és javasolt lekérdezések esetén)](index-add-suggesters.md), [a rangsorolási eredmények pontozási logikája](index-add-scoring-profiles.md).

## <a name="elements-of-a-query-request"></a>A lekérdezési kérelem elemei

A lekérdezések mindig egyetlen indexre vannak irányítva. Az indexek nem csatlakoztathatók, és nem hozhatnak létre egyéni vagy ideiglenes adatstruktúrákat lekérdezési célként. 

A lekérdezési kérelemhez szükséges elemek a következő összetevőket tartalmazzák:

+ A szolgáltatási végpont és az indexelési dokumentumok gyűjteménye rögzített és felhasználó által definiált összetevőket tartalmazó URL-címként kifejezve:**`https://<your-service-name>.search.windows.net/indexes/<your-index-name>/docs`**
+ **`api-version`**(Csak REST) szükséges, mert az API több verziója mindig elérhető. 
+ **`api-key`**, vagy egy lekérdezési vagy felügyeleti API-kulcs, hitelesíti a kérést a szolgáltatásnak.
+ **`queryType`**(egyszerű vagy teljes), amely kihagyható, ha a beépített alapértelmezett egyszerű szintaxist használja.
+ **`search`** vagy **`filter`** adja meg az egyeztetési feltételeket, amelyek nem adhatók meg, ha üres keresést szeretne végezni. Mindkét lekérdezési típust az egyszerű elemző is tárgyalja, de a speciális lekérdezésekhez a keresési paraméter szükséges a komplex lekérdezési kifejezések átadásához.

Az összes többi keresési paraméter megadása nem kötelező. Az attribútumok teljes listájáért lásd: [index létrehozása (REST)](https://docs.microsoft.com/rest/api/searchservice/create-index). A paraméterek a feldolgozás során való használatának alaposabb megismeréséhez lásd: [Hogyan működik a teljes szöveges keresés az Azure Cognitive Searchban](search-lucene-query-architecture.md).

## <a name="choose-apis-and-tools"></a>API-k és eszközök kiválasztása

A következő táblázat a lekérdezések elküldéséhez szükséges API-kat és eszköz-alapú megközelítéseket sorolja fel.

| Módszertan | Leírás |
|-------------|-------------|
| [Keresési ablak (portál)](search-explorer.md) | Az index és az API-Version beállításokhoz biztosít keresési sávot. Az eredményeket JSON-dokumentumként adja vissza a rendszer. Feltárásra, tesztelésre és érvényesítésre ajánlott. <br/>[További információ.](search-get-started-portal.md#query-index) | 
| [Poster vagy más REST-eszközök](search-get-started-postman.md) | A webes tesztelési eszközök kiváló választás a REST-hívások kialakításához. A REST API az Azure Cognitive Search minden lehetséges műveletét támogatja. Ebből a cikkből megtudhatja, hogyan állíthatja be a HTTP-kérések fejlécét és törzsét a kérelmek Azure Cognitive Searchba való küldéséhez.  |
| [SearchIndexClient (.NET)](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.searchindexclient?view=azure-dotnet) | Az Azure Cognitive Search index lekérdezéséhez használható ügyfél.  <br/>[További információ.](search-howto-dotnet-sdk.md#core-scenarios)  |
| [Dokumentumok keresése (REST API)](https://docs.microsoft.com/rest/api/searchservice/search-documents) | Metódusok beolvasása vagy közzététele indexeken a lekérdezési paraméterek használatával további bevitelhez.  |

## <a name="choose-a-parser-simple--full"></a>Válasszon elemzőt: Simple | teljes

Az Azure Cognitive Search az Apache Lucene, és két lekérdezési elemző közül választhat a tipikus és speciális lekérdezések kezeléséhez. Az egyszerű elemzőt használó kérelmeket az [egyszerű lekérdezési szintaxissal](query-simple-syntax.md)kell kijelölni, amely a sebességet és a hatékonyságot az ingyenes szöveges lekérdezésekben válassza. Ez a szintaxis számos gyakori keresési operátort támogat, többek között a következőt: és, vagy, nem, kifejezés, utótag és elsőbbségi operátor.

A [teljes Lucene lekérdezési szintaxis](query-Lucene-syntax.md#bkmk_syntax), amely a kérelemhez való hozzáadáskor `queryType=full` engedélyezve van, az [Apache Lucene](https://lucene.apache.org/core/6_6_1/queryparser/org/apache/lucene/queryparser/classic/package-summary.html)részeként kifejlesztett, széles körben elfogadott és kifejező lekérdezési nyelvet jeleníti meg. A teljes szintaxis kibővíti az egyszerű szintaxist. Az egyszerű szintaxishoz írt összes lekérdezés a teljes Lucene-elemző alatt fut. 

Az alábbi példák szemléltetik a pontot: ugyanaz a lekérdezés, de különböző queryType-beállításokkal eltérő eredményeket eredményez. Az első lekérdezésben a `^3` `historic` rendszer a keresési kifejezés részeként kezeli a következőt:. A lekérdezés legfelső szintű eredménye a "Marquis Plaza & Suites", amelynek a leírása a következő: *Ocean* .

```
queryType=simple&search=ocean historic^3&searchFields=Description, Tags&$select=HotelId, HotelName, Tags, Description&$count=true
```

Ugyanaz a lekérdezés, amely a teljes Lucene elemzőt használja `^3` , az értelmezést a mezőre való emlékeztetőként. A váltási elemzők a rangot módosítják, és az olyan eredmények, amelyek a korábbi, a *régire* való áttérés kifejezését tartalmazzák.

```
queryType=full&search=ocean historic^3&searchFields=Description, Tags&$select=HotelId, HotelName, Tags, Description&$count=true
```

<a name="types-of-queries"></a>

## <a name="types-of-queries"></a>A lekérdezések típusai

Az Azure Cognitive Search a lekérdezési típusok széles körét támogatja. 

| Lekérdezés típusa | Használat | Példák és további információk |
|------------|--------|-------------------------------|
| Szabad formátumú szöveges keresés | Keresési paraméter és vagy elemző| A teljes szöveges keresés egy vagy több kifejezést keres az index összes *kereshető* mezőjében, és úgy működik, ahogyan a keresőmotor, például a Google vagy a Bing működéséhez. A bevezetés példája a teljes szöveges keresés.<br/><br/>A teljes szöveges keresés a standard Lucene Analyzer használatával (alapértelmezés szerint) az összes kifejezésre kiterjedően, a leállítási szavakat (például "The") távolítja el. Felülírhatja az alapértelmezettet a [nem angol nyelvű elemzők](index-add-language-analyzers.md#language-analyzer-list) vagy a [speciális nyelvtől független elemzők](index-add-custom-analyzers.md#AnalyzerTable) használatával, amelyek a szöveges elemzést módosítják. Példa egy [kulcsszóra](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/KeywordAnalyzer.html) , amely egy mező teljes tartalmát egyetlen tokenként kezeli. Ez hasznos lehet például a zip-kódok, az azonosítók és a termékek neveihez. | 
| Szűrt keresés | [OData szűrési kifejezés](query-odata-filter-orderby-syntax.md) és vagy elemző | A szűrési lekérdezések egy logikai kifejezést értékelnek ki az index összes *szűrhető* mezőjében. A kereséstől eltérően a szűrő lekérdezése egy mező pontos tartalmának felel meg, beleértve a kis-és nagybetűk megkülönböztetését is a karakterlánc mezőiben. Egy másik különbség, hogy a szűrési lekérdezések OData szintaxisban vannak kifejezve. <br/>[Példa szűrési kifejezésre](search-query-simple-examples.md#example-3-filter-queries) |
| Földrajzi keresés | [EDM. geographypoint adattípuson típus](https://docs.microsoft.com/rest/api/searchservice/supported-data-types) a mezőn, szűrési kifejezés és vagy elemző | Egy EDM rendelkező mezőben tárolt koordinátákat a rendszer a "keresés a közelben" vagy a térképes keresési vezérlőkben használja. <br/>[Földrajzi keresés – példa](search-query-simple-examples.md#example-5-geo-search)|
| Tartomány keresése | szűrő kifejezés és egyszerű elemző | Az Azure Cognitive Searchban a Range lekérdezéseket a Filter paraméterrel kell felépíteni. <br/>[Tartomány szűrője – példa](search-query-simple-examples.md#example-4-range-filters) | 
| [Mező szerinti keresés](query-lucene-syntax.md#bkmk_fields) | Keresési paraméter és teljes elemző | Hozzon létre egy összetett lekérdezési kifejezést, amely egyetlen mezőt céloz meg. <br/>[Mező szerinti keresés – példa](search-query-lucene-examples.md#example-2-fielded-search) |
| [fuzzy keresés](query-lucene-syntax.md#bkmk_fuzzy) | Keresési paraméter és teljes elemző | Megegyezik a hasonló szerkezettel vagy helyesírással kapcsolatos feltételekkel. <br/>[Fuzzy keresési példa](search-query-lucene-examples.md#example-3-fuzzy-search) |
| [közelség keresése](query-lucene-syntax.md#bkmk_proximity) | Keresési paraméter és teljes elemző | Megkeresi a dokumentumban egymáshoz közeli kifejezéseket. <br/>[Proximity keresési példa](search-query-lucene-examples.md#example-4-proximity-search) |
| [a kifejezés fokozása](query-lucene-syntax.md#bkmk_termboost) | Keresési paraméter és teljes elemző | Ha a megnövelt kifejezést tartalmazza, rangsorolja a dokumentumot, amely nem a többihez képest. <br/>[Példa a "Kiemelés" kifejezésre](search-query-lucene-examples.md#example-5-term-boosting) |
| [reguláris kifejezés keresése](query-lucene-syntax.md#bkmk_regex) | Keresési paraméter és teljes elemző | A reguláris kifejezés tartalma alapján illeszkedik. <br/>[Reguláris kifejezés – példa](search-query-lucene-examples.md#example-6-regex) |
|  [helyettesítő karakter vagy előtag keresése](query-lucene-syntax.md#bkmk_wildcard) | Keresési paraméter és teljes elemző | Egy előtag és egy tilde (`~`) vagy egy karakter (`?`) alapján illeszkedik. <br/>[Helyettesítő karakteres keresés – példa](search-query-lucene-examples.md#example-7-wildcard-search) |

## <a name="manage-search-results"></a>Keresési eredmények kezelése 

A lekérdezési eredményeket a rendszer JSON-dokumentumként továbbítja a REST APIban, bár ha .NET API-kat használ, a szerializálás a beépített. Az eredményeket úgy alakíthatja át, hogy paramétereket állít be a lekérdezésben, és kiválasztja a válasz konkrét mezőit.

A lekérdezés paramétereinek használatával az alábbi módokon strukturálható az eredményhalmaz:

+ Az eredményekben szereplő dokumentumok számának korlátozása vagy feldolgozása (alapértelmezés szerint 50)
+ Az eredményekbe foglalandó mezők kiválasztása
+ Rendezési sorrend beállítása
+ A találatok hozzáadásával a keresési eredmények törzsében megfigyelheti a megfelelő kifejezéseket

### <a name="tips-for-unexpected-results"></a>Váratlan eredményekhez kapcsolódó tippek

Alkalmanként az anyag és az eredmények szerkezete nem várt. Ha a lekérdezés eredménye nem az, amit várhatóan látni fog, kipróbálhatja a lekérdezés módosításait, hogy megtudja, az eredmények javulnak-e:

+ Módosítsa **`searchMode=any`** az (alapértelmezett) **`searchMode=all`** beállítást, hogy az összes feltétel esetében kötelező legyen az egyezések megadása a feltételek helyett. Ez különösen akkor igaz, ha a lekérdezésben logikai operátorok szerepelnek.

+ Módosítsa a lekérdezési módszert, ha szöveges vagy lexikális elemzésre van szükség, de a lekérdezés típusa kizárja a nyelvi feldolgozást. A teljes szöveges keresés, a szöveges vagy a lexikális elemzés automatikus kijavítása a helyesírási hibák, a többes számú Word-űrlap, sőt a szabálytalan igék vagy főnevek esetében is. Bizonyos lekérdezések, például a fuzzy vagy a helyettesítő karakterek keresésekor a szöveges elemzés nem része a lekérdezés-elemzési folyamatnak. Bizonyos esetekben a reguláris kifejezések megkerülő megoldásként használhatók. 

### <a name="paging-results"></a>Lapozás eredményei
Az Azure Cognitive Search megkönnyíti a keresési eredmények lapozásának megvalósítását. A és **`skip`** a **`top`** paraméterek segítségével zökkenőmentesen kipróbálhatja azokat a keresési kéréseket, amelyek lehetővé teszik a keresési eredmények teljes készletének fogadását a felügyelhető, rendezett részhalmazokban, amelyek könnyen lehetővé teszik a jó keresési felhasználói felületi gyakorlatok használatát. Az eredmények kisebb alkészleteinek fogadásakor a keresési eredmények teljes készletében lévő dokumentumok darabszámát is megkaphatja.

A lapozási keresési eredményekről a cikk a [keresési eredmények az Azure Cognitive Searchban](search-pagination-page-layout.md)című cikkben talál további információt.

### <a name="ordering-results"></a>Az eredmények rendezése
Ha keresési lekérdezés eredményeit fogadja, kérheti, hogy az Azure Cognitive Search egy adott mezőben lévő értékek alapján rendezve jelenítse meg az eredményeket. Alapértelmezés szerint az Azure Cognitive Search megrendeli a keresési eredményeket az egyes dokumentumok keresési pontszáma alapján, amely a [TF-IDF-](https://en.wikipedia.org/wiki/Tf%E2%80%93idf)ből származik.

Ha azt szeretné, hogy az Azure Cognitive Search az eredményeket a keresési pontszámtól eltérő értékkel adja vissza, akkor használhatja a **`orderby`** keresési paramétert. Megadhatja a **`orderby`** paraméter értékét, hogy a mezőneveket és a [** `geo.distance()` függvény**](query-odata-filter-orderby-syntax.md) hívásait is tartalmazza a térinformatikai értékek számára. Az egyes kifejezéseket követve `asc` jelezheti, hogy a rendszer növekvő sorrendben kéri az eredményeket, **`desc`** és jelzi, hogy az eredményeket csökkenő sorrendben kell megadni. Alapértelmezés szerint a rangsorolás növekvő sorrendben történik.


### <a name="hit-highlighting"></a>Találatok kiemelése
Az Azure Cognitive Searchban a keresési lekérdezésnek megfelelő keresési eredmények pontos részét kiemelve a, **`highlight`** **`highlightPreTag`** a és **`highlightPostTag`** a paraméterek használatával egyszerűen elvégezhető. Megadhatja, hogy mely *kereshető* mezőknél legyen kiemelve a párosított szöveg, valamint az Azure Cognitive Search által visszaadott egyező szöveg kezdetéhez és végéhez hozzáfűzni kívánt karakterlánc-címkék pontos megadását.

## <a name="see-also"></a>Lásd még

+ [A teljes szöveges keresés működése az Azure Cognitive Searchban (lekérdezési elemzési architektúra)](search-lucene-query-architecture.md)
+ [Keresési ablak](search-explorer.md)
+ [Lekérdezés a .NET-ben](search-query-dotnet.md)
+ [A lekérdezés a REST-ben](search-create-index-rest-api.md)
