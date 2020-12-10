---
title: Lekérdezéstípusok
titleSuffix: Azure Cognitive Search
description: Ismerje meg az Cognitive Search által támogatott lekérdezések típusait, beleértve az ingyenes szöveget, a szűrőt, az automatikus kiegészítést és a javaslatokat, a Geo-keresést, a rendszerlekérdezéseket és a dokumentumok keresését.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 12/09/2020
ms.openlocfilehash: d1ea2d0ba8ed5850e5d4cd9c06a0b016c4059ca7
ms.sourcegitcommit: 273c04022b0145aeab68eb6695b99944ac923465
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/10/2020
ms.locfileid: "97007857"
---
# <a name="query-types-in-azure-cognitive-search"></a>Lekérdezési típusok az Azure Cognitive Search

Az Azure Cognitive Searchban a lekérdezés egy kerekítési művelet teljes specifikációja, a lekérdezés-végrehajtást szabályozó paraméterekkel, valamint a válasznak megfelelő paraméterekkel.

## <a name="elements-of-a-request"></a>A kérelem elemei

Az alábbi példa egy, a [keresési dokumentumok REST API](/rest/api/searchservice/search-documents)használatával létrehozott reprezentatív lekérdezés. Ez a példa a [Hotels bemutató indexét](search-get-started-portal.md) célozza meg, és általános paramétereket tartalmaz, így megtalálhatja, hogy a lekérdezés hogyan néz ki.

```http
POST https://[service name].search.windows.net/indexes/[index name]/docs/search?api-version=[api-version]
{
    "queryType": "simple" 
    "search": "`New York` +restaurant",
    "searchFields": "Description, Address/City, Tags",
    "select": "HotelId, HotelName, Description, Rating, Address/City, Tags",
    "top": "10",
    "count": "true",
    "orderby": "Rating desc"
}
```

A lekérdezések mindig egyetlen index dokumentumainak gyűjteményére vannak irányítva. Az indexek nem csatlakoztathatók, és nem hozhatnak létre egyéni vagy ideiglenes adatstruktúrákat lekérdezési célként.

+ **`queryType`** Beállítja az elemzőt, amely az [alapértelmezett egyszerű lekérdezés-elemző](search-query-simple-examples.md) (optimális a teljes szöveges kereséshez), vagy a speciális lekérdezési felépítéshez használt [teljes Lucene lekérdezés-elemző](search-query-lucene-examples.md) , például a reguláris kifejezések, a közelségi keresés, a fuzzy és a helyettesítő karakterek keresése, hogy csak néhányat említsünk.

+ **`search`** Megadja az egyeztetési feltételeket, általában az egész kifejezést vagy kifejezéseket, de gyakran logikai operátorok kísérik. Az önálló különálló kifejezések *kifejezéses* lekérdezések. Az idézőjelek közé zárt, több részből álló lekérdezések *kifejezéses* lekérdezéseket tartalmaznak. A keresés nem definiálható, például a alkalmazásban, **`search=*`** de a nem megfelelő feltételek nélkül az eredményhalmaz önkényesen kiválasztott dokumentumokból áll.

+ **`searchFields`** a lekérdezés végrehajtásának korlátozása adott mezőkre. Az index sémában *kereshetőként* megadott bármely mező erre a paraméterre van jelölt.

A válaszokat a lekérdezésben foglalt paraméterek is megformázzák:

+ **`select`** meghatározza, hogy mely mezőket kell visszaadni a válaszban. Csak SELECT utasításban lehet használni az indexben *beolvasható* megjelölt mezőket.

+ **`top`** a megadott számú legmegfelelőbb dokumentumot adja vissza. Ebben a példában csak 10 találat lesz visszaadva. Az eredmények megjelenítéséhez a Top és a skip (nem látható) lehetőséget használhatja.

+ **`count`** azt jelzi, hogy a teljes indexben hány dokumentum felel meg összességében, ami a visszaadott értéknél nagyobb lehet. 

+ **`orderby`** akkor használatos, ha egy érték, például egy minősítés vagy egy hely alapján kívánja rendezni az eredményeket. Ellenkező esetben az alapértelmezett érték a relevancia pontszám használata az eredmények rangsorolása érdekében.

> [!Tip]
> A kód megírása előtt a lekérdezési eszközök segítségével megismerheti a szintaxist, és kísérletezhet a különböző paraméterekkel. A leggyorsabb módszer a beépített portál eszköz, a [Search Explorer](search-explorer.md).
>
> Ha követte ezt a rövid útmutatót a [Hotels bemutató index létrehozásához](search-get-started-portal.md), a lekérdezési karakterláncot beillesztheti az Intéző címsorába az első lekérdezés futtatásához: `search=+"New York" +restaurant&searchFields=Description, Address/City, Tags&$select=HotelId, HotelName, Description, Rating, Address/City, Tags&$top=10&$orderby=Rating desc&$count=true`

### <a name="how-field-attributes-in-an-index-determine-query-behaviors"></a>A lekérdezés viselkedését egy indexben definiáló mező attribútumai

Az index tervezési és lekérdezési kialakítása szorosan összekapcsolható az Azure Cognitive Searchban. Fontos, hogy tudd elölről, hogy az *index séma*, amely az egyes mezők attribútumait határozza meg, meghatározza, hogy milyen típusú lekérdezést hozhat létre. 

Egy mező indexelése az engedélyezett műveletekkel – azt határozza meg, hogy egy mező *kereshető* -e az indexben, lekérhető az *eredmények között,* *rendezhető*, *szűrhető* és így tovább. A példában a lekérdezési karakterláncban `"$orderby": "Rating"` csak a működik, mert a minősítés mező az index sémában *rendezhető* van megjelölve. 

![A szállodai minta index-definíciója](./media/search-query-overview/hotel-sample-index-definition.png "A szállodai minta index-definíciója")

A fenti képernyőkép a Hotels-minta index-attribútumainak részleges listája. A teljes index séma a portálon tekinthető meg. További információ az index attribútumairól: [create index REST API](/rest/api/searchservice/create-index).

> [!Note]
> Egyes lekérdezési funkciók esetében a teljes indexre van engedélyezve, nem pedig a mező alapján. Ezek a képességek többek között a következők: [szinonimák térképek](search-synonyms.md), [Egyéni elemzők](index-add-custom-analyzers.md), [javaslati szerkezetek (automatikus kiegészítés és javasolt lekérdezések esetén)](index-add-suggesters.md), [a rangsorolási eredmények pontozási logikája](index-add-scoring-profiles.md).

## <a name="choose-a-parser-simple--full"></a>Válasszon elemzőt: Simple | teljes

Az Azure Cognitive Search két lekérdezési elemző közül választhat a tipikus és a speciális lekérdezések kezeléséhez. Az egyszerű elemzőt használó kérelmeket az [egyszerű lekérdezési szintaxissal](query-simple-syntax.md)kell kijelölni, amely a sebességet és a hatékonyságot az ingyenes szöveges lekérdezésekben válassza. Ez a szintaxis számos gyakori keresési operátort támogat, többek között a következőt: és, vagy, nem, kifejezés, utótag és elsőbbségi operátor.

A [teljes Lucene lekérdezési szintaxis](query-Lucene-syntax.md#bkmk_syntax), amely a kérelemhez való hozzáadáskor engedélyezve van `queryType=full` , az [Apache Lucene](https://lucene.apache.org/core/6_6_1/queryparser/org/apache/lucene/queryparser/classic/package-summary.html)részeként kifejlesztett, széles körben elfogadott és kifejező lekérdezési nyelvet jeleníti meg. A teljes szintaxis kibővíti az egyszerű szintaxist. Az egyszerű szintaxishoz írt összes lekérdezés a teljes Lucene-elemző alatt fut. 

Az alábbi példák szemléltetik a pontot: ugyanaz a lekérdezés, de különböző queryType-beállításokkal eltérő eredményeket eredményez. Az első lekérdezésben a `^3` rendszer a `historic` keresési kifejezés részeként kezeli a következőt:. A lekérdezés legfelső szintű eredménye a "Marquis Plaza & Suites", amely az *óceánt* is tartalmazta a leírásában.

```http
queryType=simple&search=ocean historic^3&searchFields=Description, Tags&$select=HotelId, HotelName, Tags, Description&$count=true
```

Ugyanaz a lekérdezés, amely a teljes Lucene elemzőt használja, az értelmezést `^3` a mezőre való emlékeztetőként. A váltási elemzők a rangot módosítják, és az olyan eredmények, amelyek a korábbi, a *régire* való áttérés kifejezését tartalmazzák.

```http
queryType=full&search=ocean historic^3&searchFields=Description, Tags&$select=HotelId, HotelName, Tags, Description&$count=true
```

<a name="types-of-queries"></a>

## <a name="types-of-queries"></a>A lekérdezések típusai

Az Azure Cognitive Search a lekérdezési típusok széles körét támogatja. 

| Lekérdezés típusa | Használat | Példák és további információk |
|------------|--------|-------------------------------|
| Szabad formátumú szöveges keresés | Keresési paraméter és vagy elemző| A teljes szöveges keresés egy vagy több kifejezést keres az index összes *kereshető* mezőjében, és úgy működik, ahogyan a keresőmotor, például a Google vagy a Bing működéséhez. A bevezetés példája a teljes szöveges keresés.<br/><br/>A teljes szöveges keresés a lexikális analízis használatával a standard Lucene Analyzer (alapértelmezés szerint) és az összes kifejezés, a leállítási szavak, például a "The". Felülbírálhatja az alapértelmezettet a [nem angol nyelvű elemzők](index-add-language-analyzers.md#language-analyzer-list) , illetve a lexikális analízist módosító [speciális nyelvtől független elemzők](index-add-custom-analyzers.md#AnalyzerTable) használatával. Példa egy [kulcsszóra](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/KeywordAnalyzer.html) , amely egy mező teljes tartalmát egyetlen tokenként kezeli. Ez hasznos lehet például a zip-kódok, az azonosítók és a termékek neveihez. | 
| Szűrt keresés | [OData szűrési kifejezés](query-odata-filter-orderby-syntax.md) és vagy elemző | A szűrési lekérdezések egy logikai kifejezést értékelnek ki az index összes *szűrhető* mezőjében. A kereséstől eltérően a szűrő lekérdezése egy mező pontos tartalmának felel meg, beleértve a kis-és nagybetűk megkülönböztetését is a karakterlánc mezőiben. Egy másik különbség, hogy a szűrési lekérdezések OData szintaxisban vannak kifejezve. <br/>[Példa szűrési kifejezésre](search-query-simple-examples.md#example-3-filter-queries) |
| Földrajzi keresés | [EDM. geographypoint adattípuson típus](/rest/api/searchservice/supported-data-types) a mezőn, szűrési kifejezés és vagy elemző | Egy EDM rendelkező mezőben tárolt koordinátákat a rendszer a "keresés a közelben" vagy a térképes keresési vezérlőkben használja. <br/>[Földrajzi keresés – példa](search-query-simple-examples.md#example-5-geo-search)|
| Tartomány keresése | szűrő kifejezés és egyszerű elemző | Az Azure Cognitive Searchban a Range lekérdezéseket a Filter paraméterrel kell felépíteni. <br/>[Tartomány szűrője – példa](search-query-simple-examples.md#example-4-range-filters) | 
| [Mező szerinti keresés](query-lucene-syntax.md#bkmk_fields) | Keresési paraméter és teljes elemző | Hozzon létre egy összetett lekérdezési kifejezést, amely egyetlen mezőt céloz meg. <br/>[Mező szerinti keresés – példa](search-query-lucene-examples.md#example-2-fielded-search) |
| [Automatikus kiegészítés vagy javasolt eredmények](search-autocomplete-tutorial.md) | Automatikus kiegészítés vagy javaslat paraméter | Egy alternatív lekérdezési űrlap, amely egy keresési típusban található részleges karakterláncok alapján hajtható végre. Az automatikus kiegészítés és a javaslatok együtt vagy külön is használhatók. |
| [fuzzy keresés](query-lucene-syntax.md#bkmk_fuzzy) | Keresési paraméter és teljes elemző | Megegyezik a hasonló szerkezettel vagy helyesírással kapcsolatos feltételekkel. <br/>[Fuzzy keresési példa](search-query-lucene-examples.md#example-3-fuzzy-search) |
| [közelség keresése](query-lucene-syntax.md#bkmk_proximity) | Keresési paraméter és teljes elemző | Megkeresi a dokumentumban egymáshoz közeli kifejezéseket. <br/>[Proximity keresési példa](search-query-lucene-examples.md#example-4-proximity-search) |
| [a kifejezés fokozása](query-lucene-syntax.md#bkmk_termboost) | Keresési paraméter és teljes elemző | Ha a megnövelt kifejezést tartalmazza, rangsorolja a dokumentumot, amely nem a többihez képest. <br/>[Példa a "Kiemelés" kifejezésre](search-query-lucene-examples.md#example-5-term-boosting) |
| [reguláris kifejezés keresése](query-lucene-syntax.md#bkmk_regex) | Keresési paraméter és teljes elemző | A reguláris kifejezés tartalma alapján illeszkedik. <br/>[Reguláris kifejezés – példa](search-query-lucene-examples.md#example-6-regex) |
|  [helyettesítő karakter vagy előtag keresése](query-lucene-syntax.md#bkmk_wildcard) | Keresési paraméter és teljes elemző | Egy előtag és egy tilde ( `~` ) vagy egy karakter () alapján illeszkedik `?` . <br/>[Helyettesítő karakteres keresés – példa](search-query-lucene-examples.md#example-7-wildcard-search) |

## <a name="next-steps"></a>Következő lépések

Használja a portált vagy más eszközt, például a Poster vagy a Visual Studio Code-ot, vagy az egyik SDK-t, hogy mélyebben tárja fel a lekérdezéseket. Az alábbi hivatkozásokkal kezdheti meg az első lépéseket.

+ [Keresési ablak](search-explorer.md)
+ [Lekérdezés a .NET-ben](./search-get-started-dotnet.md)
+ [A lekérdezés a REST-ben](./search-get-started-powershell.md)