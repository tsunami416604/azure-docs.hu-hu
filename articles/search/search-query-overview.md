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
ms.openlocfilehash: 098718293cda1699fb07e09fa81af94a95bbdeca
ms.sourcegitcommit: d16b7d22dddef6da8b6cfdf412b1a668ab436c1f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/08/2018
ms.locfileid: "39715158"
---
# <a name="query-types-and-composition-in-azure-search"></a>Lekérdezés típusa és létrehozása az Azure Search

Lekérdezés létrehozása az Azure Search egy teljes megadását, egy kérelem: felel meg a feltételeket, valamint irányítja a lekérdezés-végrehajtás és a válasz alakításra. Egy kérelem megadja a mezőket tartalmaz, mely mezők, a rendezés vagy szűrőt, és így tovább. Nincs megadva, a lekérdezés fut kereshető mező alapján egy teljes szöveges keresési művelet egy tetszőleges sorrendben pontozás nélküli eredményhalmazt visszaadása.

## <a name="introduction-by-example"></a>Példa alapján bemutatása

Példák lehetnek hasznosak ábrázoló alapfogalmakat. A következő példában a kérésfejléceket, használja a [Search dokumentumok REST API](https://docs.microsoft.com/rest/api/searchservice/search-documents), tájékoztatja a kérés és a válasz. Az Azure Search szolgáltatásban a lekérdezés végrehajtása nem mindig felel meg egy index, az api-kulcsot a kérésben megadott való hitelesítése. 

```
{  
    "queryType": "simple" 
    "search": "seattle townhouse* +\"lake\"", 
    "searchFields": "description, city",  
    "count": "true", 
    "select": "listingId, street, status, daysOnMarket, description",
    "top": "10",
    "orderby": "listingId"
 } 
```
Reprezentatív lekérdezésként Ez a példa bemutatja a lekérdezés definíciója, az elemző bemeneteit, az eredményhalmaz megfelelő átalakítását, számos fontos szempontja. A lekérdezés végrehajtása nem mindig felel meg egy index, az api-kulcsot a kérésben megadott való hitelesítése. 

A lekérdezés futtatására használja [keresés explorer és a ingatlan bemutató index](search-get-started-portal.md). Az explorer keresősávba is illessze be a lekérdezési karakterlánc: `search=seattle townhouse +lake&searchFields=description, city&$count=true&$select=listingId, street, status, daysOnMarket, description&$top=10&orderby=listingId`

**Az index keresése**

+ Lekérdezéselemző közül választhat, a beállítása keresztül `queryType`. A legtöbb fejlesztő használja az alapértelmezett [egyszerű elemző](search-query-simple-examples.md) teljes szöveges kereséshez, de [teljes Lucene](search-query-lucene-examples.md) elemzés szükség, például az intelligens keresést vagy reguláris kifejezésekkel speciális lekérdezési űrlapok.
+ Egyezés az indexben található dokumentumok feltételek keresztül van beállítva a `search` paraméter. Lehet, hogy a keresés nem definiált, mint a `search=*`, de további valószínűleg áll feltételek, kifejezések és hasonló operátorok mi jelenik meg a példa.
+ Hatókör lehet a teljes indexre, illetve specifikus mezők, ahogyan `searchFields`.

**A válasz rendszerezéséhez**

Más paramétereket, a példában a lekérdezés eredményeit vonatkoznak:

+ `count` a dokumentumok számát a lekérdezésnek megfelelő.
+ `select` a válaszban visszaadandó mezők korlátozza.
+ `top` a válaszban visszaadott dokumentumok vagy a sorok korlátozza. Az alapértelmezett érték 50. a példában, amely csökkenti a 10-re.
+ `orderby` az eredményeket egy mező alapján rendezi.

**Keresztül indexattribútumokat műveletek engedélyezése**

Index tervezési és kialakítási szorosan összekapcsolva az Azure Search lekérdezési. Itt nem látható, amíg a kritikus ponttá előre tudni, hogy a *indexsémát*, az egyes mezők attribútumai, határozza meg, milyen típusú lekérdezési hozhat létre. Egy mező állapítsa meg az index attribútumainak engedélyezett műveletek –, hogy az adott mező kitöltése *kereshető* az indexben *lekérhető* eredmények között, *rendezhető*, * szűrhető*, és így tovább. A példában `"orderby": "listingId"` csak akkor működik, ha a listingId mező van megjelölve *rendezhető* az indexsémában. Az index attribútumainak kapcsolatos további információkért lásd: [Index REST API létrehozása](https://docs.microsoft.com/rest/api/searchservice/create-index).

Engedélyezett műveletek egy mezőnként könnyen módon egy index definícióját, hogy tájékoztatja a lekérdezés végrehajtása. Egyéb képességek engedélyezve van az indexben a következők:

+ [A szinonimák](https://docs.microsoft.com/rest/api/searchservice/synonym-map-operations)
+ [(Nyelvi) szövegelemzés](https://docs.microsoft.com//rest/api/searchservice/language-support) és [egyéni elemző](https://docs.microsoft.com/rest/api/searchservice/custom-analyzers-in-azure-search)
+ [Javaslattevő szerkezeteket](https://docs.microsoft.com/rest/api/searchservice/suggesters) , amelyek lehetővé teszik az automatikus kiegészítés és auto-javaslat
+ [Pontozási profilok](https://docs.microsoft.com/rest/api/searchservice/add-scoring-profiles-to-a-search-index) logika hozzáadása, amely keresési eredmények rangsorolása

A fenti funkciók gyakorolja a lekérdezés végrehajtása során, de általában megvalósított a kódban a Mezőtulajdonságok helyett a lekérdezés a paramétereket.

<a name="types-of-queries"></a>

## <a name="types-of-queries-search-and-filter"></a>A lekérdezések típusai: keresés és szűrés

A bevezető a példában a keresési paramétert azonosított, az azt jelenti, hogy a keresési feltételek átadott a motor. A gyakorlatban, két fő lekérdezési típus: nincsenek: `search` és `filter`. 

+ `search` lekérdezések vizsgálata az összes egy vagy több feltételek *kereshető* az index, és a keresőmotor, például a Google vagy a Bing munka volna várt módon működik. A bevezetés használt példák a `search` paraméter.

+ `filter` lekérdezések egy logikai kifejezés kiértékelése összes *szűrhető* index mezőire. Ellentétben `search`, amely egy `filter` -lekérdezés megegyezik a mezőket, beleértve a kisbetű/nagybetű megkülönböztetése a karakterlánc-mezők pontos tartalmát.

A Keresés és szűrés együtt vagy külön-külön is. Önálló szűrőt, anélkül, hogy egy lekérdezési karakterláncot, akkor hasznos, ha a szűrőkifejezés viszont fontos dokumentumok teljes minősítéséhez. A lekérdezési karakterlánc nélkül nincs nincs lexikális vagy nyelvi elemzés, nincs pontozási és nincs ennek a területnek. Figyelje meg a keresési karakterlánc üres.

```
POST /indexes/nycjobs/docs/search?api-version=2017-11-11  
    {  
      "search": "",
      "filter": "salary_frequency eq 'Annual' and salary_range_from gt 90000",
      "count": "true"
    }
```

Együttes használatuk esetén a szűrő alkalmazása először a teljes indexre, és majd a Keresés a szűrő az eredmények alapján történik. A szűrők éppen ezért hasznosak a lekérdezés teljesítményének javítására, mivel általuk lecsökkenthető a keresési lekérdezés által feldolgozandó dokumentumok köre.

A szűrőkifejezések szintaxisa az [Odata szűrési nyelv](https://docs.microsoft.com/rest/api/searchservice/OData-Expression-Syntax-for-Azure-Search) alkészlete. Keresési lekérdezések esetén használhatja a [egyszerűsített szintaxis](https://docs.microsoft.com/rest/api/searchservice/Simple-query-syntax-in-Azure-Search) vagy a [Lucene lekérdezési szintaxis](https://docs.microsoft.com/rest/api/searchservice/Lucene-query-syntax-in-Azure-Search) alább tárgyalt.


## <a name="choose-a-syntax-simple-or-full"></a>Válassza ki a szintaxis: egyszerű vagy teljes

Az Azure Search helyezkedik el, az Apache Lucene felett, és annak eldöntésére, hogy két lekérdezést elemzők az általános és speciális lekérdezések között. Általános keresési kérelmekhez dolgoznak ki az alapértelmezett [egyszerű lekérdezési szintaxis](https://docs.microsoft.com/rest/api/searchservice/Simple-query-syntax-in-Azure-Search). Ez a szintaxis számos gyakori keresési operátort, például az AND, OR, NOT, a kifejezés, utótag és prioritási operátorokat támogatja.

A [Lucene lekérdezési szintaxis](https://docs.microsoft.com/rest/api/searchservice/Lucene-query-syntax-in-Azure-Search#bkmk_syntax), engedélyezve van, amikor hozzáadja `queryType=full` a kérést, közzéteszi a részeként kifejlesztett, széles körben elfogadott, kifejező lekérdezési nyelv [Apache Lucene](https://lucene.apache.org/core/4_10_2/queryparser/org/apache/lucene/queryparser/classic/package-summary.html). Ezen lekérdezési szintaxis használatával lehetővé teszi, hogy a speciális lekérdezések:

+ [A mező-hatáskörű lekérdezések](https://docs.microsoft.com/rest/api/searchservice/Lucene-query-syntax-in-Azure-Search#bkmk_fields)
+ [intelligens keresést](https://docs.microsoft.com/rest/api/searchservice/Lucene-query-syntax-in-Azure-Search#bkmk_fuzzy)
+ [közelségi keresésre](https://docs.microsoft.com/rest/api/searchservice/Lucene-query-syntax-in-Azure-Search#bkmk_proximity)
+ [kiemelési távú](https://docs.microsoft.com/rest/api/searchservice/Lucene-query-syntax-in-Azure-Search#bkmk_termboost)
+ [reguláris kifejezés keresése](https://docs.microsoft.com/rest/api/searchservice/Lucene-query-syntax-in-Azure-Search#bkmk_regex)
+ [helyettesítő karakteres keresés](https://docs.microsoft.com/rest/api/searchservice/Lucene-query-syntax-in-Azure-Search#bkmk_wildcard)

Logikai operátorok ugyanazok, főleg mindkét szintaxist követi, a teljes Lucene további formátumok:

+ [Az egyszerű szintaxis logikai operátorok](https://docs.microsoft.com/rest/api/searchservice/simple-query-syntax-in-azure-search#operators-in-simple-search)
+ [A teljes Lucene-szintaxisban logikai operátorok](https://docs.microsoft.com/rest/api/searchservice/Lucene-query-syntax-in-Azure-Search#bkmk_boolean)

## <a name="required-and-optional-elements"></a>Szükséges és választható elemek

Lekérdezések mindig egy egyetlen indextől irányítja. Csatlakozzon az indexek nem, vagy hozzon létre egyéni vagy ideiglenes datové struktury lekérdezés célként. 

Az Azure Search számára keresési kérések elküldésekor az alkalmazás keresőmezőjébe írt tényleges szavak mellett számos paraméter is megadható. A lekérdezési paraméterek segítségével jobban kezelhető a [teljes szöveges keresés](search-lucene-query-architecture.md).

A lekérdezési kérést a szükséges elemeket a következő összetevőket tartalmazza:

+ Végpont és az index dokumentumok gyűjtemény, ki, a szolgáltatás URL-ként `https://<your-service-name>.search.windows.net/indexes/<your-index-name>/docs`.
+ API-verzió (csak REST), kifejezve `api-version`
+ lekérdezés vagy a felügyeleti api-kulcsot, kifejezve `api-key`
+ lekérdezési karakterlánc kifejezett `search`, amely lehet meghatározatlan Ha egy üres keresés végrehajtására vonatkozó szándékát. Csak egy kifejezést, is küldhet `$filter`.
+ `queryType`, egyszerű vagy teljes, amely kihagyható, ha azt szeretné, az alapértelmezett egyszerű szintaxis használatára.

Minden más keresési paraméterek nem kötelezők.

## <a name="manage-search-results"></a>Keresési eredmények kezelése 

Bár a .NET API-k használatakor szerializálási van-e építve a lekérdezés eredményeit a REST API-t a JSON-dokumentumok formájában átvitt. Átalakíthatja eredmények paraméterek beállításával a lekérdezésre, az eredmény az adott mezők kiválasztása

A lekérdezési paraméterek segítségével a következőképpen állítsa be az eredmény szerkezete:

+ Korlátozása vagy a kötegelés a dokumentumok száma, az eredmények között (alapértelmezés szerint 50)
+ Az eredmények tartalmazzák a mezők kiválasztása
+ Rendezési sorrend megadása
+ Hozzáadásának nyomja le emeli ki, hogy felhívja a figyelmet a megfelelő feltételek törzsében a keresési eredmények

### <a name="tips-for-unexpected-results"></a>Tippek a nem várt eredmények

Néha előfordul az anyag nem eredmény szerkezete jsou váratlan. Ha a lekérdezés eredményét nem várt megtekintéséhez, próbálkozzon ezek lekérdezés a módosítások megtekintéséhez, ha az eredmények javítása:

+ Változás `searchMode=any` (alapértelmezett) `searchMode=all` kötelező helyett a kritériumok összes feltételeknek megfelelő találat. Ez akkor különösen igaz, ha a logikai operátorokkal részét képezik a lekérdezést.

+ Módosítsa a lekérdezési módszer, ha a szöveg- vagy lexikai elemzés szükséges, de a lekérdezés típusa kizárja nyelvi feldolgozás. A teljes szöveges keresésben szöveges vagy lexikai elemzés automatikusa kijavítja a helyesírási hibák, egyes számban-plural word űrlapokat, és akár szabálytalan műveletek vagy főneveket. Például bizonyos lekérdezések intelligens vagy helyettesítő karakteres keresés, szövegelemzés nem a lekérdezés elemzési folyamat részét. Bizonyos esetekben a reguláris kifejezések áthidaló használták fel. 

### <a name="paging-results"></a>Lapozás eredményei
A keresési eredmények lapozása az Azure Search segítségével könnyen megvalósítható. A `top` és `skip` paraméterek használatával zökkenőmentesen adhat ki olyan keresési kéréseket, amelyek segítségével a keresési eredmények teljes készletét kezelhető, rendezett alkészletek formájában fogadhatja, így lehetővé téve a helyes keresési felhasználói gyakorlat egyszerű kialakítását. Az eredmények kisebb alkészleteinek fogadásakor a keresési eredmények teljes készletében lévő dokumentumok darabszámát is megkaphatja.

A keresési eredmények lapozásáról további információkat a [Keresési eredmények lapozása az Azure Search szolgáltatásban](search-pagination-page-layout.md) című cikkben talál.

### <a name="ordering-results"></a>Az eredmények rendezése
A keresési lekérdezés eredményeinek fogadásakor kérheti, hogy az Azure Search az eredményeket egy adott mezőben lévő érték szerint rendezve szolgáltassa. Alapértelmezés szerint az Azure Search a keresési eredményeket az egyes dokumentumok keresési pontszáma szent rangsorolja, amelyet a [TF-IDF](https://en.wikipedia.org/wiki/Tf%E2%80%93idf) állományból származtat.

Ha azt szeretné, hogy az Azure Search az eredményeket a keresési pontszámtól eltérő érték szerint rendezve adja vissza, erre a célra az `orderby` keresési paramétert használhatja. Az `orderby` paraméter értékét megadhatja úgy, hogy az tartalmazza a mezőneveket és a [`geo.distance()` függvény](https://docs.microsoft.com/rest/api/searchservice/OData-Expression-Syntax-for-Azure-Search) meghívásait a térinformatikai értékek esetében. Az egyes kifejezések után írt `asc` jellel az eredményeket növekvő sorrendben, `desc` jellel pedig csökkenő sorrendben kérheti le. Alapértelmezés szerint a rangsorolás növekvő sorrendben történik.


### <a name="hit-highlighting"></a>Találatok kiemelése
Az Azure Search szolgáltatás `highlight`, `highlightPreTag` és `highlightPostTag` paramétereinek használatával egyszerűen kiemelhető a keresési lekérdezésnek megfelelő keresési eredmények pontos köre. Megadhatja, hogy mely *searchable* (kereshető) mezők esetében kívánja bekapcsolni az egyező szöveg kiemelését, valamint az Azure által visszaadott egyező szöveg elejére és végére hozzáfűzni kívánt sztringcímkéket.

## <a name="apis-and-tools-for-testing"></a>API-k és tesztelési eszközök

A következő táblázat felsorolja az API-k és a lekérdezések elküldése az eszköz-alapú megközelítéseken.

| Módszer | Leírás |
|-------------|-------------|
| [A SearchIndexClient (.NET)](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.searchindexclient?view=azure-dotnet) | Az Azure Search-index lekérdezése használható ügyfél.  <br/>[Részletek](search-howto-dotnet-sdk.md#core-scenarios)  |
| [Dokumentumok keresése (REST API)](https://docs.microsoft.com/rest/api/searchservice/search-documents) | GET vagy POST metódus az indexen, további bemeneti lekérdezési paraméterek használatával.  |
| [A fiddler, Postman vagy más HTTP tesztelési eszköz](search-fiddler.md) | Azt ismerteti, hogyan állítható be egy kérés fejlécéhez és a szervezet az Azure Search-lekérdezések küldési.  |
| [Az Azure Portalon a keresési ablak](search-explorer.md) | Itt egy keresősáv és a lehetőségek a kiválasztott index és api-verziót. A eredmény JSON-dokumentumok formájában. <br/>[Részletek](search-get-started-portal.md#query-index) | 

## <a name="see-also"></a>Lásd még

+ [Teljes szöveges keresés működése az Azure Search (lekérdezéselemzési architektúra](search-lucene-query-architecture.md)
+ [A keresési ablak](search-explorer.md)
+ [Lekérdezése a .NET-ben](search-query-dotnet.md)
+ [Lekérdezése a REST-ben](search-query-rest-api.md)
