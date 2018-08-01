---
title: Az Azure Search – alapok lekérdezése |} A Microsoft Docs
description: Alapjait, amellyel egy keresési lekérdezést az Azure Search szolgáltatásban paraméterek használatával szeretne szűrni, jelölje ki, és rendezheti az eredményeket.
author: HeidiSteen
manager: cgronlun
ms.author: heidist
services: search
ms.service: search
ms.topic: conceptual
ms.date: 07/27/2018
ms.openlocfilehash: 4650ad89850f32ae5e83a7ac1cd5eac096b180ed
ms.sourcegitcommit: f86e5d5b6cb5157f7bde6f4308a332bfff73ca0f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/31/2018
ms.locfileid: "39366446"
---
# <a name="query-fundamentals-in-azure-search"></a>Az Azure Search lekérdezési alapjai

Az Azure Search szolgáltatásban a lekérdezés definíciója egy teljes megadását, egy kérelmet, amely a következő részekből áll: URL-végpontot, célindex, api-kulcsot a kérelem api-verziót és a lekérdezési karakterlánc hitelesítéséhez használandó szolgáltatást. 

Lekérdezési karakterlánc összeállítás áll paraméterek, ahogyan az az [Search API-dokumentumok](https://docs.microsoft.com/rest/api/searchservice/search-documents). Közül a legfontosabb a **keresési =** paraméter, amely nem definiált lehet (`search=*`), de valószínűbb áll a feltételeket, kifejezések és a kezelők az alábbi példához hasonló:

```
"search": "seattle townhouse +\"lake\""
```

Más paraméterek használhatók a közvetlen lekérdezés-feldolgozás, vagy növelje a választ. Hogyan történik a paraméterek használata például bizonyos mezők keresési hatókör, egy keresési mód, ha a pontosság-visszahívási eltérés beállítása és hozzáadása a száma, hogy Ön is nyomon követheti, eredményeket. 

```
{  
    "search": "seattle townhouse* +\"lake\"",  
    "searchFields": "description, city",  
    "searchMode": "all",
    "count": "true", 
    "queryType": "simple" 
 } 
```

Habár a lekérdezés definíciója alapvető, az indexsémában fontos egyaránt a hogyan azt adja meg az engedélyezett műveletek mező szerint történik. Index a fejlesztés során mezők attribútumai engedélyezett műveletek határozzák meg. Például ahhoz, hogy a teljes szöveges keresés és a belefoglalási a keresési eredmények között, egy mezőt kell megjelölni, mindkét *kereshető* és *lekérhető*.

Lekérdezéskor a végrehajtási, mindig egy index, az api-kulcsot a kérésben megadott való hitelesítése ellen. Csatlakozzon az indexek nem, vagy hozzon létre egyéni vagy ideiglenes datové struktury lekérdezés célként.  

Bár a .NET API-k használatakor szerializálási van-e építve a lekérdezés eredményeit a REST API-t a JSON-dokumentumok formájában átvitt. Átalakíthatja eredmények paraméterek beállításával a lekérdezésre, az eredmény az adott mezők kiválasztása

Összefoglalva, az anyag a lekérdezési kérelem megadja hatókörrel és a műveletek: mezőket a keresést, az eredmény szerepeltetendő mezőket a rendezés vagy szűrő beállítása, amely tartalmazza, és így tovább. Nincs megadva, a lekérdezés fut kereshető mező alapján egy teljes szöveges keresési művelet egy tetszőleges sorrendben pontozás nélküli eredményhalmazt visszaadása.

<a name="types-of-queries"></a>

## <a name="types-of-queries-search-and-filter"></a>A lekérdezések típusai: keresés és szűrés

Az Azure Search számos lehetőséget kínál rendkívül hatékony lekérdezések végrehajtására. Az itt használt két fő lekérdezési típus: `search` és `filter`. 

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

A szűrőkifejezések szintaxisa az [Odata szűrési nyelv](https://docs.microsoft.com/rest/api/searchservice/OData-Expression-Syntax-for-Azure-Search) alkészlete. A keresési lekérdezések esetében használható az [egyszerűsített szintaxis](https://docs.microsoft.com/rest/api/searchservice/Simple-query-syntax-in-Azure-Search) vagy az alább tárgyalt [Lucene lekérdezési szintaxis](https://docs.microsoft.com/rest/api/searchservice/Lucene-query-syntax-in-Azure-Search) is.


## <a name="choose-a-syntax-simple-or-full"></a>Válassza ki a szintaxis: egyszerű vagy teljes

Az Azure Search helyezkedik el, az Apache Lucene felett, és annak eldöntésére, hogy két lekérdezést elemzők az általános és speciális lekérdezések között. Általános keresési kérelmekhez dolgoznak ki az alapértelmezett [egyszerű lekérdezési szintaxis](https://docs.microsoft.com/rest/api/searchservice/Simple-query-syntax-in-Azure-Search). Ez a szintaxis számos gyakori keresési operátort, például az AND, OR, NOT, a kifejezés, utótag és prioritási operátorokat támogatja.

A [Lucene lekérdezési szintaxis](https://docs.microsoft.com/rest/api/searchservice/Lucene-query-syntax-in-Azure-Search#bkmk_syntax), engedélyezve van, amikor hozzáadja **queryType = full** a kérést, közzéteszi a részeként kifejlesztett, széles körben átvett, kifejező lekérdezési nyelv [Apache Lucene](https://lucene.apache.org/core/4_10_2/queryparser/org/apache/lucene/queryparser/classic/package-summary.html). Ezen lekérdezési szintaxis használatával lehetővé teszi, hogy a speciális lekérdezések:

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

Az Azure Search számára keresési kérések elküldésekor az alkalmazás keresőmezőjébe írt tényleges szavak mellett számos paraméter is megadható. A lekérdezési paraméterek segítségével jobban kezelhető a [teljes szöveges keresés](search-lucene-query-architecture.md).

A lekérdezési kérést a szükséges elemeket a következő összetevőket tartalmazza:

+ Végpont és az index dokumentumok gyűjtemény, ki, a szolgáltatás URL-ként `https://<your-service-name>.search.windows.net/indexes/<your-index-name>/docs`.
+ API-verzió (csak REST), kifejezett **api-version =**
+ lekérdezés vagy a felügyeleti api-kulcsot, a kifejezett **api-kulcs =**
+ lekérdezési karakterlánc kifejezett **keresési =**, amely lehet meghatározatlan Ha egy üres keresés végrehajtására vonatkozó szándékát. Csak egy kifejezést, is küldhet **$filter =**.
+ **queryType =**, egyszerű vagy teljes, amely kihagyható, ha azt szeretné, az alapértelmezett egyszerű szintaxis használatára.

Minden más keresési paraméterek nem kötelezők.

## <a name="apis-and-tools-for-testing"></a>API-k és tesztelési eszközök

A következő táblázat felsorolja az API-k és a lekérdezések elküldése az eszköz-alapú megközelítéseken.

| Módszer | Leírás |
|-------------|-------------|
| [A SearchIndexClient (.NET)](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.searchindexclient?view=azure-dotnet) | Az Azure Search-index lekérdezése használható ügyfél.  <br/>[Részletek](search-howto-dotnet-sdk.md#core-scenarios)  |
| [Dokumentumok keresése (REST API)](https://docs.microsoft.com/rest/api/searchservice/search-documents) | GET vagy POST metódus az indexen, további bemeneti lekérdezési paraméterek használatával.  |
| [A fiddler, Postman vagy más HTTP tesztelési eszköz](search-fiddler.md) | Azt ismerteti, hogyan állítható be egy kérés fejlécéhez és a szervezet az Azure Search-lekérdezések küldési.  |
| [Az Azure Portalon a keresési ablak](search-explorer.md) | Itt egy keresősáv és a lehetőségek a kiválasztott index és api-verziót. A eredmény JSON-dokumentumok formájában. <br/>[Részletek](search-get-started-portal.md#query-index) | 

## <a name="manage-search-results"></a>Keresési eredmények kezelése

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

## <a name="see-also"></a>Lásd még

+ [Teljes szöveges keresés működése az Azure Search (lekérdezéselemzési architektúra](search-lucene-query-architecture.md)
+ [A keresési ablak](search-explorer.md)
+ [Lekérdezése a .NET-ben](search-query-dotnet.md)
+ [Lekérdezése a REST-ben](search-query-rest-api.md)
