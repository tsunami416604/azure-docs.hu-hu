---
title: Lucene lekérdezési példa – Azure Search
description: Lucene lekérdezési szintaxis az intelligens keresést, közelségi keresésre, kifejezés kiemelése, reguláris kifejezés keresése és az Azure Search szolgáltatás helyettesítő karakteres kereséssel.
author: HeidiSteen
manager: cgronlun
tags: Lucene query analyzer syntax
services: search
ms.service: search
ms.topic: conceptual
ms.date: 08/09/2018
ms.author: heidist
ms.custom: seodec2018
ms.openlocfilehash: 8ec6a6a24629f72199d5f5afa86200acf53aba01
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/18/2019
ms.locfileid: "58136546"
---
# <a name="lucene-syntax-query-examples-for-building-advanced-queries-in-azure-search"></a>Példák Lucene szintaxisú lekérdezésekre, amellyel az Azure Search speciális lekérdezések
Lekérdezések az Azure Search létrehozásánál lecserélheti az alapértelmezett [egyszerű lekérdezéselemzőt](https://docs.microsoft.com/rest/api/searchservice/simple-query-syntax-in-azure-search) szélesebb körben alkalmazhatóvá vált a [Lucene lekérdezéselemző az Azure Search](https://docs.microsoft.com/rest/api/searchservice/lucene-query-syntax-in-azure-search) határozhatja meg a speciális és speciális lekérdezés definíciók. 

A Lucene lekérdezéselemző támogatja az összetett lekérdezési szerkezeteket, például a mező-hatáskörű lekérdezések, az intelligens és helyettesítő karakteres keresés előtag, közelségi keresésre, időszak kiemelési és reguláris kifejezés keresése. A csúcsterheléseket tartalmaz további feldolgozási követelmények így egy kiadva, hosszabb végrehajtási időt kell látnia. Ez a cikk elolvasásával példák elérhető lekérdezési műveletek bemutatásához, ha a teljes szintaxisát használja.

> [!Note]
> Speciális lekérdezési konstrukciók engedélyezését a teljes Lucene lekérdezési szintaxis számos nem [szöveg elemzése](https://docs.microsoft.com/azure/search/search-lucene-query-architecture#stage-2-lexical-analysis), amely knál, ha, amely szerint vagy morfológiai várt lehet. Lexikai elemzés csak hajtható végre teljes igényei szerint (lekérdezési kifejezés vagy kifejezés lekérdezést). Lekérdezési típusokra hiányos feltételeket (előtag lekérdezés, helyettesítő karaktert tartalmazó lekérdezés, regex lekérdezés, az intelligens lekérdezés) kerülnek közvetlenül a lekérdezés fában, az elemzési fázis kihagyásával. Az egyetlen átalakítás hiányos lekérdezési kifejezések végrehajtott lowercasing van. 
>

## <a name="formulate-requests-in-postman"></a>Állítson össze a Postmanben kérelmek

Az alábbi példák kihasználhatja az elérhető feladatok által biztosított adatkészlet alapján álló i állások keresési indexet a [város, New York OpenData](https://opendata.cityofnewyork.us/) kezdeményezésére. Ezek az adatok nem az aktuális vagy a teljes kell tekinteni. Az index van a Microsoft, ami azt jelenti, hogy nem kell egy Azure-előfizetést, vagy próbálja ki ezeket a lekérdezéseket az Azure Search által nyújtott védőfal szolgáltatásokat.

Amire szüksége a Postman vagy egy egyenértékű eszközt tud kiállítani a HTTP-kérelem a GET. További információkért lásd: [REST-ügyfelekkel Intéző](search-fiddler.md).

### <a name="set-the-request-header"></a>Állítsa be a kérelem fejléce

1. A kérelem fejlécében beállítása **Content-Type** való `application/json`.

2. Adjon hozzá egy **api-kulcs**, és állítsa be a következő karakterláncra: `252044BE3886FE4A8E3BAA4F595114BB`. Ez a lekérdezési kulcs állások index üzemeltető védőfal search szolgáltatás számára.

A kérelem fejlécében megadott, felhasználhatja azt az összes ebben a cikkben szereplő lekérdezések kicserélni a csak a **keresési =** karakterlánc. 

  ![Postman-kérelem fejléce](media/search-query-lucene-examples/postman-header.png)

### <a name="set-the-request-url"></a>A kérelem URL-Címének beállítása

Kérjen egy GET parancs párosítva van az Azure Search végpont és a keresési karakterláncot tartalmazó URL-címet.

  ![Postman-kérelem fejléce](media/search-query-lucene-examples/postman-basic-url-request-elements.png)

URL-Címének szerkezete a következő elemekből áll:

+ **`https://azs-playground.search.windows.net/`** az Azure Search fejlesztői csapat által karbantartott védőfal keresési szolgáltatás. 
+ **`indexes/nycjobs/`** az i állások index a indexek gyűjtemény adott van. A szolgáltatás nevét és a index van szükség a kérelem.
+ **`docs`** van a dokumentumok gyűjteményt összes kereshető tartalom tartalmazó. A lekérdezési api-kulcs a kérés fejlécében megadott csak a dokumentumok gyűjteményt célzó olvasási műveletek a működik.
+ **`api-version=2017-11-11`** állítja be az api-verziót, amely egy kötelező paraméter halasztása minden kérelemnél.
+ **`search=*`** a lekérdezési karakterlánc, amely a kezdeti lekérdezés null értékű, a (alapértelmezés szerint) az első 50 eredményt adnak vissza.

## <a name="send-your-first-query"></a>Az első lekérdezés küldése

Ellenőrzési lépésként, illessze be a következő kérelmet GET, majd kattintson a **küldése**. Eredmények a rendszer a részletes JSON-dokumentumok formájában adja vissza. Akkor is másolás és beillesztés az URL-cím első az alábbi példában.

  ```http
  https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2017-11-11&$count=true&search=*
  ```

A lekérdezési karakterlánc **`search=*`**, van egy nem meghatározott keresés egyenértékű, NULL értékű vagy üres keresés. Nem különösen akkor hasznos, de a legegyszerűbb keresést végezhet.

Ha szükséges, hozzáadhat **`$count=true`** az URL-címet a keresési feltételeknek megfelelő a dokumentumok számát adja vissza. Egy üres Keresés karakterlánc ez az index (körülbelül 2800-i állások esetén) szereplő összes dokumentumot.

## <a name="how-to-invoke-full-lucene-parsing"></a>Hogyan kell elindítani a teljes Lucene-elemzés

Adjon hozzá **queryType = full** kell elindítani a teljes lekérdezési szintaxis, származtatásakor az alapértelmezett egyszerű lekérdezési szintaxis. 

```GET
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2017-11-11&queryType=full&search=*
```

Minden ebben a cikkben szereplő példák adja meg a **queryType = full** keresési paramétert, amely azt jelzi, hogy a teljes szintaxis kezeli a Lucene lekérdezéselemző. 

## <a name="example-1-field-scoped-query"></a>1. példa: A mező-hatáskörű lekérdezések

Az első példa nem parser-specifikus, de azt vezethet, vezessen be az első lekérdezés alapvető fogalom: tartalmazottsági. Ebben a példában a lekérdezés-végrehajtás és a válasz csak néhány bizonyos mezők hatóköröket. Hogyan építse fel olvasható JSON-választ, hogy akkor fontos, ha az eszköz csak a Postman vagy a keresési explorer. 

Kihagytuk, a lekérdezés célozza meg, csak a *business_title* mezőben, majd adja meg a csak üzleti címe adja vissza. Szintaxisa a következő **searchFields** korlátozása csak a business_title mezőre, lekérdezés-végrehajtás és **kiválasztása** , adja meg, hogy mely mezők szerepelnek a válasz.

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2017-11-11&$count=true&searchFields=business_title&$select=business_title&search=*
```

Ez a lekérdezés válasza az alábbi képernyőfelvételhez hasonlóan kell kinéznie.

  ![Postman mintaválasz](media/search-query-lucene-examples/postman-sample-results.png)

Előfordulhat, hogy észrevette a keresési pontszámtól a válaszban. 1 egységes pontszámok fordulhat elő, esetén nincs rank, vagy mert a keresés nem volt a teljes szöveges keresés, vagy mert a feltétel nem lett alkalmazva. A feltétel nem null értékű Search sorok visszatérhet tetszőleges sorrendben. Is tartalmazó tényleges feltételek, látni fogja a keresési pontszámok jelentéssel bíró értékekké fejlesztheti tovább.

## <a name="example-2-intra-field-filtering"></a>2. példa Mező-on belüli szűrése

Teljes Lucene szintaxis támogatja a kifejezések mező belül. Ez a lekérdezés a őket, de nem kezdő kifejezés vezető üzleti az adatfeliratokat keresi:

```GET
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2017-11-11&$count=true&searchFields=business_title&$select=business_title&queryType=full&search=business_title:senior+NOT+junior
```

  ![Postman mintaválasz](media/search-query-lucene-examples/intrafieldfilter.png)

Adjon meg egy **fieldname:searchterm** konstrukció, megadhat egy fielded lekérdezési műveletet, ha a mező kitöltése egyetlen szó, és a keresési kifejezést is egy szót vagy kifejezést, igény szerint a logikai operátorokkal együtt. Néhány példa a következők:

* business_title:(senior NOT junior)
* állapot: ("Győr" és "Új Jersey")

Ügyeljen arra, hogy helyezzen több karakterlánc idézőjelek között, ha azt szeretné, hogy mindkét karakterlánc, kiértékelendő egyetlen egységként, ahogy ebben az esetben a két különböző városok a hely mezőben lévő keresése. Arra is ügyeljen az operátor van nagybetű, az nem látható módon és és.

A megadott mező **fieldname:searchterm** kell lennie egy kereshető mező. Lásd: [Index létrehozása (Azure Search szolgáltatás REST API)](https://docs.microsoft.com/rest/api/searchservice/create-index) indexattribútumokat használata a Meződefiníciók részleteiért.

## <a name="example-3-fuzzy-search"></a>3. példa: intelligens keresést

Teljes Lucene-szintaxis az intelligens keresés a feltételeket, amelyek hasonló konstrukció megfelelő is támogatja. Ehhez az intelligens keresést, fűzze hozzá a hullámos vonallal `~` szimbólum egy egyetlen szó, és a egy nem kötelező paraméter, a 0. és 2 közötti értéket, amely megadja a Szerkesztés végén. Ha például `blue~` vagy `blue~1` kék blues és integrációs adna vissza.

Ez a lekérdezés keresi a feladatok, az a kifejezés "társult" (szándékosan írva):

```GET
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2017-11-11&$count=true&searchFields=business_title&$select=business_title&queryType=full&search=business_title:asosiate~
```
  ![Intelligens keresést válasz](media/search-query-lucene-examples/fuzzysearch.png)

/ [Lucene dokumentáció](https://lucene.apache.org/core/4_10_2/queryparser/org/apache/lucene/queryparser/classic/package-summary.html), az intelligens keresés alapján [Damerau-Levenshtein távolság](https://en.wikipedia.org/wiki/Damerau%e2%80%93Levenshtein_distance).

> [!Note]
> Az intelligens-lekérdezések [elemzett](https://docs.microsoft.com/azure/search/search-lucene-query-architecture#stage-2-lexical-analysis). Lekérdezési típusokra hiányos feltételeket (előtag lekérdezés, helyettesítő karaktert tartalmazó lekérdezés, regex lekérdezés, az intelligens lekérdezés) kerülnek közvetlenül a lekérdezés fában, az elemzési fázis kihagyásával. Az egyetlen átalakítás hiányos lekérdezési kifejezések végrehajtott lowercasing van.
>

## <a name="example-4-proximity-search"></a>4. példa: közelségi keresésre
Közelségi keresés segítségével keresse meg a feltételeket, amelyek egymáshoz közel egy dokumentumban. Egy hullámos vonallal beszúrása "~" kifejezés végén jel követ szavak, amelyek a közelségi határ létrehozása száma. Például "Szálloda repülőtér" ~ 5 megtalálja a a feltételek Szálloda és repülőtér belül minden más 5 szó egy dokumentumban.

Ebben a lekérdezésben, feladatok, ahol azt nem lehet egynél több szóból elválasztott "vezető elemző" kifejezéssel:

```GET
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2017-11-11&$count=true&searchFields=business_title&$select=business_title&queryType=full&search=business_title:%22senior%20analyst%22~1
```
  ![Közelségi lekérdezések](media/search-query-lucene-examples/proximity-before.png)

Próbálja ki újra eltávolítása a szavak közötti "vezető elemző" kifejezés. Figyelje meg, hogy ez a lekérdezés, ellentétben az előző lekérdezés 10 visszaadja a 8 dokumentumokat.

```GET
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2017-11-11&$count=true&searchFields=business_title&$select=business_title&queryType=full&search=business_title:%22senior%20analyst%22~0
```

## <a name="example-5-term-boosting"></a>5. példa: kiemelési távú
Kifejezés kiemelési prioritása nagyobb, ha a gyorsított időszak alatt, dokumentum, amely nem tartalmazza a kisbetűs viszonyított tartalmazza a dokumentum hivatkozik. Jelentősen növelheti a kifejezés, használja a beszúrási jellel "^", a keresett kifejezés végén boost tényezővel (szám) szimbólum. 

A jelen "előtte" lekérdezések, feladatok kifejezéssel keressen *számítógép elemző* , és figyelje meg, hogy nem adott eredményeket mindkét szavakat *számítógép* és *elemző*, még  *számítógép* feladat van az eredmények tetején.

```GET
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2017-11-11&$count=true&searchFields=business_title&$select=business_title&queryType=full&search=business_title:computer%20analyst
```
  ![Távú növelése előtt](media/search-query-lucene-examples/termboostingbefore.png)

A "után" lekérdezés, ismételje meg a keresési eredmények a kifejezéssel kiemelési ezúttal *elemző* távon *számítógép* mindkét szavak nem léteznek. 

```GET
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2017-11-11&$count=true&searchFields=business_title&$select=business_title&queryType=full&search=business_title:computer%20analyst%5e2
```
A fenti lekérdezés robotokat olvasható verziója `search=business_title:computer analyst^2`. Egy közös üzemeltetése lekérdezés `^2` kódolása `%5E2`, amely jóval nehezebb megtekintéséhez.

  ![Kiemelési után távú](media/search-query-lucene-examples/termboostingafter.png)

Kifejezés kiemelési eltér a pontozási profilok, hogy miként növelhető a pontozási profilok specifikus használati helyett egyes mezők. Az alábbi példa segít a különbségeket mutatja be.

Fontolja meg a relevanciaprofil, amely serkenti az megfelel egy bizonyos mezőben, például **műfaj** musicstoreindex példában. Kifejezés kiemelési felhasználható bizonyos keresési feltételek magasabb, mint a többi további növelése érdekében. Például "rock ^ elektronikus 2" lesz, amely tartalmazza a keresési kifejezéseket, a dokumentumok növelése a **műfaj** magasabb, mint a többi az indexben kereshető mezőt a mező. A keresési kifejezés "rock" tartalmazó dokumentumok továbbá magasabb, mint a többi "elektronikus" a kifejezés boost érték (2) eredményeként keresési kifejezés besorolásra kerülő.

Authentication beállítását a, minél nagyobb a boost tényező, több megfelelő kifejezés lesznek más keresési feltételek viszonyítva. Alapértelmezés szerint a boost tényező: 1. Bár a boost tényező pozitívnak kell lennie, 1-nél kisebb (például 0.2-es) lehet.


## <a name="example-6-regex"></a>6. példa: Regex

Keresés reguláris kifejezés közötti útjaiban perjeleket a "/", a dokumentált tartalma alapján egyezést talál az [RegExp osztály](https://lucene.apache.org/core/4_10_2/core/org/apache/lucene/util/automaton/RegExp.html).

Ebben a lekérdezésben, keresse meg a kifejezés vezető vagy beosztott rendelkező feladatok: ' search = business_title:/(Sen| Június) ior / ".

```GET
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2017-11-11&$count=true&searchFields=business_title&$select=business_title&queryType=full&search=business_title:/(Sen|Jun)ior/
```

  ![Regex lekérdezés](media/search-query-lucene-examples/regex.png)

> [!Note]
> Regex-lekérdezések [elemzett](https://docs.microsoft.com/azure/search/search-lucene-query-architecture#stage-2-lexical-analysis). Az egyetlen átalakítás hiányos lekérdezési kifejezések végrehajtott lowercasing van.
>

## <a name="example-7-wildcard-search"></a>7. példa: helyettesítő karakteres keresés
Általában felismerhető szintaxist használhat több (\*) vagy egy (?) karakter helyettesítő karakteres kereséssel. Vegye figyelembe, hogy a Lucene lekérdezéselemző használatát a szimbólumok egyetlen kifejezés, és a egy kifejezés nem támogatja.

Ebben a lekérdezésben keressen rá a "programazonosítója", amelynél a programozási feltételeket és a benne programozói üzleti címek előtagot tartalmazó feladatok. Nem használhatja a * és? szimbólum a keresési első karaktert.

```GET
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2017-11-11&$count=true&searchFields=business_title&$select=business_title&queryType=full&search=business_title:prog*
```
  ![Helyettesítő karaktert tartalmazó lekérdezés](media/search-query-lucene-examples/wildcard.png)

> [!Note]
> Helyettesítő karaktert tartalmazó lekérdezések [elemzett](https://docs.microsoft.com/azure/search/search-lucene-query-architecture#stage-2-lexical-analysis). Az egyetlen átalakítás hiányos lekérdezési kifejezések végrehajtott lowercasing van.
>

## <a name="next-steps"></a>További lépések
Adja meg a Lucene lekérdezéselemző az a kód. Az alábbi hivatkozások azt ismertetik, hogyan állítható be a keresési lekérdezések a .NET-hez és a REST API-t. A hivatkozások az alapértelmezett egyszerű szintaxist használ, így a megismert információk adja meg, hogy ez a cikk a alkalmazni kell a **queryType**.

* [A .NET SDK használatával az Azure Search-Index lekérdezése](search-query-dotnet.md)
* [A REST API használatával az Azure Search-Index lekérdezése](search-create-index-rest-api.md)

Az alábbi hivatkozásokra kattintva további keresésiszintaxis-referencia, lekérdezés-architektúra és példák találhatók:

+ [Példák egyszerű szintaxisú lekérdezésekre](search-query-simple-examples.md)
+ [Teljes szöveges keresés működése az Azure Search szolgáltatásban](search-lucene-query-architecture.md)
+ [Egyszerű lekérdezési szintaxis](https://docs.microsoft.com/rest/api/searchservice/simple-query-syntax-in-azure-search)
+ [Teljes Lucene lekérdezési szintaxis](https://docs.microsoft.com/rest/api/searchservice/lucene-query-syntax-in-azure-search)