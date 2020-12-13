---
title: Lekérdezéstípusok
titleSuffix: Azure Cognitive Search
description: Ismerje meg az Cognitive Search által támogatott lekérdezések típusait, beleértve az ingyenes szöveget, a szűrőt, az automatikus kiegészítést és a javaslatokat, a Geo-keresést, a rendszerlekérdezéseket és a dokumentumok keresését.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 12/11/2020
ms.openlocfilehash: 9cac0a0026a7007e227607e04e03a77e4df99ecd
ms.sourcegitcommit: 1bdcaca5978c3a4929cccbc8dc42fc0c93ca7b30
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/13/2020
ms.locfileid: "97368120"
---
# <a name="querying-in-azure-cognitive-search"></a>Lekérdezés az Azure Cognitive Searchban

Az Azure Cognitive Search széles körű lekérdezési nyelvet kínál a különböző forgatókönyvek, az ingyenes űrlapos keresés és a nagy mértékben megadott lekérdezési minták támogatásához. Ez a cikk a létrehozható lekérdezések típusait foglalja össze.

A Cognitive Searchban a lekérdezés egy kerekítési művelet teljes specifikációja **`search`** , amely a lekérdezés-végrehajtást és a válasz visszatérését is tájékoztatja. A paraméterek és az elemzők határozzák meg a lekérdezési kérelem típusát. A következő lekérdezési példa a [Search Documents (REST API) kifejezést](/rest/api/searchservice/search-documents)használja, amely a [Hotels bemutató indexét](search-get-started-portal.md)célozza meg.

```http
POST https://[service name].search.windows.net/indexes/hotels-sample-index/docs/search?api-version=2020-06-30
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

A lekérdezés végrehajtása során használt paraméterek:

+ **`queryType`** Beállítja az elemzőt, amely az [alapértelmezett egyszerű lekérdezés-elemző](search-query-simple-examples.md) (optimális a teljes szöveges kereséshez), vagy a speciális lekérdezési felépítéshez használt [teljes Lucene lekérdezés-elemző](search-query-lucene-examples.md) , például a reguláris kifejezések, a közelségi keresés, a fuzzy és a helyettesítő karakterek keresése, hogy csak néhányat említsünk.

+ **`search`** az egyeztetési feltételeket, általában a teljes kifejezéseket és kifejezéseket biztosítja operátorok segítségével vagy anélkül. Az index sémában *kereshetőként* megadott bármely mező erre a paraméterre van jelölt. 

+ **`searchFields`** a lekérdezés végrehajtásának korlátozása adott kereshető mezőkre.

A válasz formálásához használt paraméterek:

+ **`select`** meghatározza, hogy mely mezőket kell visszaadni a válaszban. Csak SELECT utasításban lehet használni az indexben *beolvasható* megjelölt mezőket.

+ **`top`** a megadott számú legmegfelelőbb dokumentumot adja vissza. Ebben a példában csak 10 találat lesz visszaadva. Az eredmények megjelenítéséhez a Top és a skip (nem látható) lehetőséget használhatja.

+ **`count`** azt jelzi, hogy a teljes indexben hány dokumentum felel meg összességében, ami a visszaadott értéknél nagyobb lehet. 

+ **`orderby`** akkor használatos, ha egy érték, például egy minősítés vagy egy hely alapján kívánja rendezni az eredményeket. Ellenkező esetben az alapértelmezett érték a relevancia pontszám használata az eredmények rangsorolása érdekében. A mezőnek *csoportosítva* kell lennie, hogy a paraméter jelölt legyen.

A fenti lista reprezentatív, de nem teljes. A lekérdezési kérelmek paramétereinek teljes listájáért lásd: [dokumentumok keresése (REST API)](/rest/api/searchservice/search-documents).

<a name="types-of-queries"></a>

## <a name="types-of-queries"></a>A lekérdezések típusai

Néhány jelentős kivétel miatt a lekérdezési kérések a gyors vizsgálatokra strukturált, invertált indexekre mutatnak, ahol a találatok tetszőleges számú keresési dokumentumon belül megtalálhatók. Cognitive Search a találatok keresésének elsődleges módszere teljes szöveges keresés vagy szűrők, de más, jól ismert keresési funkciókat is megvalósíthat, például az automatikus kiegészítést vagy a földrajzi hely keresését. A cikk többi része összefoglalja a Cognitive Search lekérdezéseit, és hivatkozásokat tartalmaz a további információkhoz és példákhoz.

## <a name="full-text-search"></a>Teljes szöveges keresés

Ha a keresőalkalmazás olyan keresőmezőt tartalmaz, amely a lejárati bemeneteket gyűjti, akkor a teljes szöveges keresés valószínűleg az adott élményt támogató lekérdezési művelet. A teljes szöveges keresés az **`search`** index összes *kereshető* mezőjében egy paraméterben átadott kifejezést vagy kifejezést fogad el. A lekérdezési karakterláncban nem kötelező logikai operátorok adhatnak meg belefoglalási vagy kizárási feltételeket. Az egyszerű elemző és a teljes elemző támogatja a teljes szöveges keresést.

Cognitive Search a teljes szöveges keresés az Apache Lucene lekérdezési motorra épül. A teljes szöveges keresésekben a lekérdezési karakterláncok a lexikális elemzések révén hatékonyabbá teszik a vizsgálatokat. Az elemzés magában foglalja az összes feltételt, eltávolítja a leállítási szavakat, például a "The" szót, és csökkenti a kifejezéseket az egyszerű legfelső szintű űrlapokra Az alapértelmezett elemző a standard Lucene.

Ha egyezési feltételek találhatók, a lekérdezési motor visszaállítja a egyezést tartalmazó keresési dokumentumot, rangsorolja a dokumentumokat a relevancia sorrendjében, és a válaszban a legfontosabb 50 (alapértelmezés szerint) adja vissza.

Ha teljes szöveges keresést hajt végre, akkor a tartalom jogkivonatának megismerése segít a lekérdezési rendellenességek hibakeresésében. A leválasztott karakterláncok vagy speciális karakterek lekérdezése az alapértelmezett standard Lucene eltérő Analyzer használatát teszi lehetővé annak biztosítása érdekében, hogy az index tartalmazza a megfelelő jogkivonatokat. Felülbírálhatja az alapértelmezett [nyelvi elemzőket](index-add-language-analyzers.md#language-analyzer-list) vagy [speciális elemzőket](index-add-custom-analyzers.md#AnalyzerTable) , amelyek a lexikális analízist módosítják. Az egyik példa egy [kulcsszó](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/KeywordAnalyzer.html) , amely egy mező teljes tartalmát egyetlen tokenként kezeli. Ez hasznos lehet például a zip-kódok, az azonosítók és a termékek neveihez. További információ: [részleges kifejezéses keresés és minták speciális karakterekkel](search-query-partial-matching.md).

Ha a logikai operátorok nagy mennyiségű használatát tervezi, ami nagyobb valószínűséggel olyan indexekben, amelyek nagy méretű szöveges blokkokat tartalmaznak (egy tartalom vagy hosszú leírások), akkor ügyeljen arra, hogy az **`searchMode=Any|All`** adott beállítás hatásának kiértékeléséhez használja a paramétert a logikai keresésnél.

## <a name="autocomplete-and-suggested-queries"></a>Automatikus kiegészítés és javasolt lekérdezések

Az [automatikus kiegészítés vagy a javasolt eredmények](search-autocomplete-tutorial.md) olyan alternatívák, **`search`** amelyekkel egy adott keresési élményben a részleges karakterlánc-bemeneteken (az egyes karakterek után) alapuló, egymást követő lekérdezési kérések is elérhetők. A és a **`autocomplete`** **`suggestions`** paraméter együtt vagy külön is használható az [oktatóanyagban](tutorial-csharp-type-ahead-and-suggestions.md)leírtak szerint, de nem használhatja őket a következővel: **`search`** . A befejezett feltételek és a javasolt lekérdezések a tárgymutató tartalmából származnak. A motor soha nem ad vissza olyan karakterláncot vagy javaslatot, amely nem létezik az indexben. További információ: [automatikus kiegészítés (REST API)](/rest/api/searchservice/autocomplete) és [javaslatok (REST API)](/rest/api/searchservice/suggestions).

## <a name="filter-search"></a>Keresés szűrése

A szűrők széles körben használatosak a Cognitive Searcht tartalmazó alkalmazásokban. Az alkalmazás oldalain a szűrők gyakran a felhasználó által irányított szűréshez használt hivatkozás-navigációs struktúrákban láthatók. A szűrőket belsőleg is használják az indexelt tartalomhoz tartozó szeletek megjelenítéséhez. Például szűrheti a nyelvet, ha az index az angol és a francia nyelven is tartalmaz mezőket. 

A következő táblázatban leírtak szerint szűrőkre is szükség lehet egy speciális lekérdezési űrlap meghívásához. A szűrőket meghatározatlan kereséssel ( **`search=*`** ) vagy olyan lekérdezési karakterlánccal használhatja, amely kifejezéseket, kifejezéseket, operátorokat és mintákat tartalmaz.

| Szűrési forgatókönyv | Leírás |
|-----------------|-------------|
| Tartomány szűrőinek | Az Azure Cognitive Searchban a Range lekérdezéseket a Filter paraméterrel kell felépíteni. További információért és Példákért lásd: [tartomány szűrő példa](search-query-simple-examples.md#example-4-range-filters). |
| Földrajzi hely keresése | Ha egy kereshető mező [EDM. geographypoint adattípuson típusú](/rest/api/searchservice/supported-data-types), létrehozhat egy szűrési kifejezést a "keresés a közelben" vagy a térképes keresési vezérlőkben. A földrajzi keresést elvégező mezők koordinátáit tartalmaznak. További információ és példa: [földrajzi keresési példa](search-query-simple-examples.md#example-5-geo-search). |
| Jellemzőalapú navigáció | A felületi navigációs struktúra a felhasználó által irányított Navigálás során válik elérhetővé, amikor egy dimenzióban lévő eseményre adott válaszként meghívja a szűrőt `onclick` . Ennek megfelelően a dimenziók és a szűrők kézzel is elérhetők. Ha dimenziós navigálást ad hozzá, a felhasználói élmény elvégzéséhez szűrőkre lesz szüksége. További információ: [Face szűrő](search-filters-facets.md)létrehozása. |

> [!NOTE]
> A szűrési kifejezésekben használt szöveg elemzése nem történik meg a lekérdezés feldolgozásakor. A szövegbevitel a Verbatim kis-és nagybetűket megkülönböztető karakteres mintája, amely az egyeztetés sikerességét vagy meghibásodását feltételezi. A szűrési kifejezések [OData szintaxissal](query-odata-filter-orderby-syntax.md) vannak kialakítva, és az **`filter`** index összes *szűrhető* mezőjében egy paraméterrel lesznek átadva. További információ: [szűrők az Azure Cognitive Searchban](search-filters.md).

## <a name="document-look-up"></a>Dokumentum-keresés

A korábban leírt lekérdezési űrlapokkal ellentétben ez az [azonosító alapján egyetlen keresési dokumentumot](/rest/api/searchservice/lookup-document)kér le, amely nem rendelkezik megfelelő indexelési kereséssel vagy vizsgálattal. Csak az egyik dokumentum van kérelmezve és visszaadva. Amikor egy felhasználó kiválaszt egy elemet a keresési eredmények között, a dokumentum beolvasása és a Részletek lap a mezőkkel való feltöltése egy tipikus válasz, és egy dokumentum-keresési művelet az azt támogató művelet.

## <a name="advanced-search-fuzzy-wildcard-proximity-regex"></a>Speciális keresés: fuzzy, helyettesítő karakter, közelség, regex

A speciális lekérdezési űrlap a teljes Lucene-elemzőtől és az adott lekérdezési viselkedést kiváltó operátortól függ.

| Lekérdezés típusa | Használat | Példák és további információk |
|------------|--------|------------------------------|
| [Mező szerinti keresés](query-lucene-syntax.md#bkmk_fields) | **`search`**  paraméter **`queryType=full`**  | Hozzon létre egy összetett lekérdezési kifejezést, amely egyetlen mezőt céloz meg. <br/>[Mező szerinti keresés – példa](search-query-lucene-examples.md#example-2-fielded-search) |
| [fuzzy keresés](query-lucene-syntax.md#bkmk_fuzzy) | **`search`** paraméter **`queryType=full`** | Megegyezik a hasonló szerkezettel vagy helyesírással kapcsolatos feltételekkel. <br/>[Fuzzy keresési példa](search-query-lucene-examples.md#example-3-fuzzy-search) |
| [közelség keresése](query-lucene-syntax.md#bkmk_proximity) | **`search`** paraméter **`queryType=full`** | Megkeresi a dokumentumban egymáshoz közeli kifejezéseket. <br/>[Proximity keresési példa](search-query-lucene-examples.md#example-4-proximity-search) |
| [a kifejezés fokozása](query-lucene-syntax.md#bkmk_termboost) | **`search`** paraméter **`queryType=full`** | Ha a megnövelt kifejezést tartalmazza, rangsorolja a dokumentumot, amely nem a többihez képest. <br/>[Példa a "Kiemelés" kifejezésre](search-query-lucene-examples.md#example-5-term-boosting) |
| [reguláris kifejezés keresése](query-lucene-syntax.md#bkmk_regex) | **`search`** paraméter **`queryType=full`** | A reguláris kifejezés tartalma alapján illeszkedik. <br/>[Reguláris kifejezés – példa](search-query-lucene-examples.md#example-6-regex) |
|  [helyettesítő karakter vagy előtag keresése](query-lucene-syntax.md#bkmk_wildcard) | **`search`** paraméter * *_`~`_* vagy **`?`** , **`queryType=full`**| Egy előtag és egy tilde ( `~` ) vagy egy karakter () alapján illeszkedik `?` . <br/>[Helyettesítő karakteres keresés – példa](search-query-lucene-examples.md#example-7-wildcard-search) |

## <a name="next-steps"></a>Következő lépések

Használja a portált vagy más eszközt, például a Poster vagy a Visual Studio Code-ot, vagy az egyik SDK-t, hogy mélyebben tárja fel a lekérdezéseket. Az alábbi hivatkozásokkal kezdheti meg az első lépéseket.

+ [Keresési ablak](search-explorer.md)
+ [A lekérdezés a REST-ben](search-get-started-rest.md)
+ [Lekérdezés a .NET-ben](search-get-started-dotnet.md)
+ [Lekérdezés a Pythonban](search-get-started-python.md)
+ [Lekérdezés a JavaScriptben](search-get-started-javascript.md)