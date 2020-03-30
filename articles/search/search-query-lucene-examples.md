---
title: Teljes Lucene-lekérdezés szintaxisának használata
titleSuffix: Azure Cognitive Search
description: Lucene lekérdezés szintaxisa az intelligens kereséshez, a közelségkereséshez, a kifejezéskiemeléshez, a reguláris kifejezéskereséshez és a helyettesítő karakteres kereséshez az Azure Cognitive Search szolgáltatásban.
manager: nitinme
author: HeidiSteen
ms.author: heidist
tags: Lucene query analyzer syntax
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 745be21c2a7a09a09fdbbfd57a305d09a4fac3ed
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "72793434"
---
# <a name="use-the-full-lucene-search-syntax-advanced-queries-in-azure-cognitive-search"></a>Használja a "teljes" Lucene keresési szintaxist (speciális lekérdezések az Azure Cognitive Search-ben)

Amikor lekérdezéseket hoz létre az Azure Cognitive Search számára, lecserélheti az alapértelmezett [egyszerű lekérdezéselemzőt](query-simple-syntax.md) a szélesebb körű [Lucene-lekérdezés-elemzőre az Azure Cognitive Search-ben,](query-lucene-syntax.md) hogy speciális és speciális lekérdezésdefiníciókat fogalmazzon meg. 

A Lucene-elemző támogatja az összetett lekérdezési konstrukciókat, például a mezőhatókörrel végzett lekérdezéseket, az intelligens és előtaghelyettesítő keresést, a közelségi keresést, a kifejezéskiemelést és a reguláris kifejezéskeresést. A további teljesítmény további feldolgozási követelményekkel jár, így valamivel hosszabb végrehajtási időre számíthat. Ebben a cikkben a teljes szintaxis használata esetén elérhető lekérdezési műveleteket bemutató példákat is átléphet.

> [!Note]
> A teljes Lucene-lekérdezés szintaxisán keresztül engedélyezett speciális lekérdezéskonstrukciók közül sok nem [szöveg-elemzésalatt](search-lucene-query-architecture.md#stage-2-lexical-analysis)áll, ami meglepő lehet, ha a származtatásra vagy lemmatizálásra számít. A lexikális elemzés csak teljes feltételekkel (kifejezéslekérdezéssel vagy kifejezéslekérdezéssel) történik. A hiányos kifejezésekkel rendelkező lekérdezéstípusok (előtag lekérdezés, helyettesítő lekérdezés, regex-lekérdezés, intelligens lekérdezés) közvetlenül a lekérdezési fához kerülnek, megkerülve az elemzési szakaszt. A nem teljes lekérdezési kifejezéseken végrehajtott egyetlen átalakítás a csökkentés. 
>

## <a name="formulate-requests-in-postman"></a>A kérelmek megfogalmazása a Postmanben

A következő példák a [New York-i OpenData kezdeményezés](https://opendata.cityofnewyork.us/) által biztosított adatkészlet alapján elérhető állásokból álló NYC Jobs keresési indexet használják ki. Ezek az adatok nem tekinthetők aktuálisnak vagy teljesnek. Az index a Microsoft által biztosított sandbox szolgáltatás, ami azt jelenti, hogy nem kell egy Azure-előfizetés vagy az Azure Cognitive Search, hogy megpróbálja ezeket a lekérdezéseket.

Amire szüksége van a Postman vagy egy ezzel egyenértékű eszköz kiadó HTTP kérelmet GET. További információ: [Explore with REST clients](search-get-started-postman.md).

### <a name="set-the-request-header"></a>A kérelemfejléc beállítása

1. A kérelem fejlécében állítsa a `application/json` **Tartalomtípus** t .

2. Adjon hozzá egy **api-kulcsot**, és `252044BE3886FE4A8E3BAA4F595114BB`állítsa a következő karakterláncra: . Ez a nyc jobs indexet üzemeltető sandbox keresési szolgáltatás lekérdezési kulcsa.

Miután megadta a kérelem fejlécét, újra felhasználhatja azt a cikkben szereplő összes lekérdezéshez, és csak a **search=** karakterláncot cserélheti ki. 

  ![Postman-kérelem fejléce](media/search-query-lucene-examples/postman-header.png)

### <a name="set-the-request-url"></a>A kérelem URL-címének beállítása

A kérés egy GET parancs az Azure Cognitive Search végpontot és keresési karakterláncot tartalmazó URL-címmel párosítva.

  ![Postman-kérelem fejléce](media/search-query-lucene-examples/postman-basic-url-request-elements.png)

Az URL-összetétel a következő elemekkel rendelkezik:

+ **`https://azs-playground.search.windows.net/`** az Azure Cognitive Search fejlesztői csapata által fenntartott sandbox keresési szolgáltatás. 
+ **`indexes/nycjobs/`** a NYC-feladatok indexe az adott szolgáltatás indexgyűjteményében. A kérelemhez a szolgáltatás név és az index is szükséges.
+ **`docs`** az összes kereshető tartalmat tartalmazó dokumentumgyűjtemény. A kérelem fejlécében megadott lekérdezési API-kulcs csak a dokumentumgyűjteményt célzó olvasási műveleteken működik.
+ **`api-version=2019-05-06`** beállítja az api-verziót, amely minden kéréshez szükséges paraméter.
+ **`search=*`** a lekérdezési karakterlánc, amely a kezdeti lekérdezésben null értékű, és az első 50 eredményt adja vissza (alapértelmezés szerint).

## <a name="send-your-first-query"></a>Az első lekérdezés elküldése

Ellenőrzési lépésként illessze be a következő kérést a GET fájlba, és kattintson a **Küldés**gombra. Az eredmények részletes JSON-dokumentumokként kerülnek visszaadásra. A teljes dokumentumokat a rendszer visszaadja, így az összes mező és érték megtekintése.

Ezt az URL-címet érvényesítési lépésként illessze be egy REST-ügyfélbe, és tekintse meg a dokumentumszerkezetet.

  ```http
  https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2019-05-06&$count=true&search=*
  ```

A lekérdezési **`search=*`** karakterlánc , a null vagy üres keresés meghatározatlan keresési megfelelője. Ez a legegyszerűbb keresés, amit tehetünk.

Tetszés szerint **`$count=true`** hozzáadhatja az URL-címhez a keresési feltételeknek megfelelő dokumentumok számát. Egy üres keresési karakterlánc, ez az összes dokumentumot az indexben (kb. 2800 esetében NYC Jobs).

## <a name="how-to-invoke-full-lucene-parsing"></a>Hogyan lehet meghívni a teljes Lucene elemzés

Add **queryType=full** a teljes lekérdezés szintaxisának meghívásához, felülbírálva az alapértelmezett egyszerű lekérdezésszintaxist. 

```GET
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2019-05-06&queryType=full&search=*
```

A cikkben szereplő összes példa megadja a **queryType=full** search paramétert, jelezve, hogy a teljes szintaxist a Lucene Query Parser kezeli. 

## <a name="example-1-query-scoped-to-a-list-of-fields"></a>1. példa: A lekérdezés hatóköre mezők listájára

Ez az első példa nem Lucene-specifikus, de az első alapvető lekérdezési koncepció, a mezőhatókör bevezetéséhez vezetünk. Ez a példa a teljes lekérdezést és a választ csak néhány konkrét mezőre fedi le. Az olvasható JSON-válasz strukturálásának ismerete akkor fontos, ha az eszköz postás vagy kereső. 

Röviden, a lekérdezés csak a *business_title* mezőt célozza meg, és csak az üzleti címeket adja vissza. A **SearchFields** paraméter csak a business_title mezőre korlátozza a lekérdezésvégrehajtását, és **megadja,** hogy mely mezők szerepeljenek a válaszban.

### <a name="partial-query-string"></a>Részleges lekérdezési karakterlánc

```http
&search=*&searchFields=business_title&$select=business_title
```

Ugyanaz a lekérdezés, amely több mezőt tartalmazó, vesszővel tagolt listában.

```http
search=*&searchFields=business_title, posting_type&$select=business_title, posting_type
```

A vesszők utáni szóközök nem kötelezőek.

> [!Tip]
> Ha a REST API-t az alkalmazáskódjából használja, ne felejtse el url-kódolási paramétereket, például `$select` a és `searchFields`a .

### <a name="full-url"></a>Teljes URL-cím

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2019-05-06&queryType=full&$count=true&search=*&searchFields=business_title&$select=business_title
```

A lekérdezésre adott válasznak az alábbi képernyőképhez hasonlóan kell kinéznie.

  ![Postás minta válasz](media/search-query-lucene-examples/postman-sample-results.png)

Lehet, hogy észrevette a keresési pontszámot a válaszban. Az 1-es egységes pontszámok akkor fordulnak elő, ha nincs rang, vagy azért, mert a keresés nem volt teljes szöveges keresés, vagy mert nem alkalmaztak feltételt. A feltétel nélküli null kereséshez a sorok tetszőleges sorrendben térnek vissza. Ha tényleges keresési feltételeket ad meg, látni fogja, hogy a keresési pontszámok értelmes értékekké alakulnak.

## <a name="example-2-fielded-search"></a>2. példa: Mezőalapú keresés

A Teljes lucene szintaxis támogatja az egyes keresési kifejezések hatókörének egy adott mezőre történő hatókörét. Ez a példa olyan üzleti címeket keres, amelyekben a senior kifejezés szerepel, de nem a junior.

### <a name="partial-query-string"></a>Részleges lekérdezési karakterlánc

```http
$select=business_title&search=business_title:(senior NOT junior)
```

Itt van ugyanaz a lekérdezés több mezővel.

```http
$select=business_title, posting_type&search=business_title:(senior NOT junior) AND posting_type:external
```

### <a name="full-url"></a>Teljes URL-cím

```GET
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2019-05-06&queryType=full&$count=true&$select=business_title&search=business_title:(senior NOT junior)
```

  ![Postás minta válasz](media/search-query-lucene-examples/intrafieldfilter.png)

A **mezőnév:searchExpression** szintaxissal definiálhat mezőalapú keresési műveletet, ahol a keresési kifejezés lehet egyetlen szó vagy kifejezés, vagy zárójelben összetettebb kifejezés, tetszés szerint logikai operátorokkal. Néhány példa a következők:

- `business_title:(senior NOT junior)`
- `state:("New York" OR "New Jersey")`
- `business_title:(senior NOT junior) AND posting_type:external`

Ügyeljen arra, hogy több karakterláncot helyezzen idézőjelek közé, ha mindkét karakterláncot egyetlen entitásként szeretné `state` kiértékelni, mivel ebben az esetben két különböző helyet keres a mezőben. Is, győződjön meg arról, hogy az üzemeltető nagybetűs, mint látod a NEM és és.

A **fieldName:searchExpression** mezőben megadott mezőnek kereshető mezőnek kell lennie. [Index létrehozása (Azure Cognitive Search REST API)](https://docs.microsoft.com/rest/api/searchservice/create-index) a meződefiníciókban használt indexattribútumok használatával kapcsolatos részletekért.

> [!NOTE]
> A fenti példában nem kellett a `searchFields` paramétert használnunk, mert a lekérdezés minden egyes része kifejezetten megadott mezőnévvel rendelkezik. A `searchFields` paraméter azonban továbbra is használható, ha olyan lekérdezést szeretne futtatni, amelyben egyes részek hatóköre egy adott mezőre van hatázza, a többi pedig több mezőre is vonatkozhat. A `search=business_title:(senior NOT junior) AND external&searchFields=posting_type` lekérdezés például `senior NOT junior` csak a `business_title` mezőnek felel meg, míg `posting_type` a "külső" és a mező. A **fieldName:searchExpression** mezőnév mindig elsőbbséget élvez `searchFields` a paraméterrel szemben, ezért ebben a `business_title` példában `searchFields` nem kell szerepelnünk a paraméterben.

## <a name="example-3-fuzzy-search"></a>3. példa: Fuzzy keresés

A Teljes Lucene szintaxis támogatja az intelligens keresést is, amelyek hasonló szerkezetű kifejezésekhez illeszkednek. Fuzzy kereséshez fűzze hozzá `~` a tilde szimbólumot egyetlen szó végén egy nem kötelező paraméterrel, 0 és 2 közötti értékkel, amely meghatározza a szerkesztési távolságot. Például, `blue~` `blue~1` vagy visszatér a kék, kék, és ragasztó.

### <a name="partial-query-string"></a>Részleges lekérdezési karakterlánc

```http
searchFields=business_title&$select=business_title&search=business_title:asosiate~
```

A kifejezések közvetlenül nem támogatottak, de a kifejezés összetevőiben megadható kontinamot.

```http
searchFields=business_title&$select=business_title&search=business_title:asosiate~ AND comm~ 
```


### <a name="full-url"></a>Teljes URL-cím

Ez a lekérdezés a "társ" (szándékosan hibásan írt) kifejezéssel rendelkező feladatokat keresi:

```GET
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2019-05-06&queryType=full&$count=true&searchFields=business_title&$select=business_title&search=business_title:asosiate~
```
  ![Fuzzy keresési válasz](media/search-query-lucene-examples/fuzzysearch.png)


> [!Note]
> A fuzzy lekérdezéseket a rendszer nem [elemzi.](search-lucene-query-architecture.md#stage-2-lexical-analysis) A hiányos kifejezésekkel rendelkező lekérdezéstípusok (előtag lekérdezés, helyettesítő lekérdezés, regex-lekérdezés, intelligens lekérdezés) közvetlenül a lekérdezési fához kerülnek, megkerülve az elemzési szakaszt. A nem teljes lekérdezési kifejezéseken végrehajtott egyetlen átalakítás a csökkentés.
>

## <a name="example-4-proximity-search"></a>4. példa: Közelségi keresés
A közelségi keresések segítségével olyan kifejezéseket kereshet, amelyek közel vannak egymáshoz a dokumentumban. Szúrjon be egy tilde "~" szimbólumot a kifejezés végére, amelyet a közelséghatárot létrehozó szavak száma követ. Például a "hotel repülőtér" ~ 5 megtalálja a feltételeket hotel és repülőtér belül 5 szót egymástól egy dokumentumban.

### <a name="partial-query-string"></a>Részleges lekérdezési karakterlánc

```http
searchFields=business_title&$select=business_title&search=business_title:%22senior%20analyst%22~1
```

### <a name="full-url"></a>Teljes URL-cím

Ebben a lekérdezésben a "vezető elemző" kifejezéssel rendelkező feladatok esetében, ahol legfeljebb egy szó választja el egymástól:

```GET
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2019-05-06&queryType=full&$count=true&searchFields=business_title&$select=business_title&search=business_title:%22senior%20analyst%22~1
```
  ![Közelség lekérdezés](media/search-query-lucene-examples/proximity-before.png)

Próbálja meg újra eltávolítja a szavakat a "vezető elemző". Figyelje meg, hogy 8 dokumentumot ad vissza ehhez a lekérdezéshez, szemben az előző lekérdezés 10-ével.

```GET
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2019-05-06&queryType=full&$count=true&searchFields=business_title&$select=business_title&search=business_title:%22senior%20analyst%22~0
```

## <a name="example-5-term-boosting"></a>5. példa: Kifejezéskiemelés
A kifejezéskiemelés azt jelenti, hogy a dokumentum magasabbra van rangsorolva, ha az tartalmazza a kiemelt kifejezést, a kifejezést nem tartalmazó dokumentumokhoz képest. Kifejezés kiemeléséhez használja a keresett kifejezés végén található "^" kiemelési tényezővel (számmal) ellátott "^" szimbólumot. 

### <a name="full-urls"></a>Teljes URL-címek

Ebben a "előtte" lekérdezés, keressen munkát a kifejezés *számítógépes elemző* és észre nincsenek eredmények mind a szavak *számítógép* és *elemző*, de *a számítógépes* feladatok tetején az eredményeket.

```GET
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2019-05-06&queryType=full&$count=true&searchFields=business_title&$select=business_title&search=business_title:computer%20analyst
```
  ![Kifejezés kiemelése előtt](media/search-query-lucene-examples/termboostingbefore.png)

Az "után" lekérdezés, ismételje meg a keresést, ezúttal fellendítése eredmények kifejezés *elemző* kifejezés *számítógép,* ha mindkét szó nem létezik. 

```GET
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2019-05-06&queryType=full&$count=true&searchFields=business_title&$select=business_title&search=business_title:computer%20analyst%5e2
```
A fenti lekérdezés emberibb olvasható `search=business_title:computer analyst^2`változata . Egy működőképes lekérdezés `^2` esetén a kódolva `%5E2`a , amelyet nehezebb látni.

  ![Kifejezés kiemelése után](media/search-query-lucene-examples/termboostingafter.png)

A kifejezéskiemelés abban különbözik a pontozási profiloktól, hogy a pontozási profilok bizonyos mezőket emelnek ki, nem pedig konkrét kifejezéseket. A következő példa a különbségek szemléltetése.

Vegyünk egy pontozási profilt, amely növeli a mérkőzések egy bizonyos területen, például a **műfaj** a musicstoreindex példában. A kifejezéskiemelés sel tovább növelheti bizonyos keresési kifejezéseket, amelyek magasabbak, mint mások. A "rock^2 electronic" például magasabbra emeli azokat a dokumentumokat, amelyek a **műfajmezőben** lévő keresési kifejezéseket tartalmazzák, mint az index más kereshető mezői. Továbbá a "rock" keresőkifejezést tartalmazó dokumentumok a reklámérték (2) kifejezés eredményeként magasabb rangúak lesznek, mint a többi "elektronikus" keresőkifejezés.

A tényezőszint beállításakor minél magasabb a kiemelési tényező, annál relevánsabb lesz a kifejezés a többi keresési kifejezéshez képest. Alapértelmezés szerint a kiemelési tényező 1. Bár a lökéstényezőnek pozitívnak kell lennie, 1-nél kisebb is lehet (például 0,2).


## <a name="example-6-regex"></a>6. példa: Regex

A reguláris kifejezéskeresés a [RegExp osztályban](https://lucene.apache.org/core/6_6_1/core/org/apache/lucene/util/automaton/RegExp.html)dokumentált perjelek "/" tartalma alapján egyezést talál.

### <a name="partial-query-string"></a>Részleges lekérdezési karakterlánc

```http
searchFields=business_title&$select=business_title&search=business_title:/(Sen|Jun)ior/
```

### <a name="full-url"></a>Teljes URL-cím

Ebben a lekérdezésben az Senior vagy a `search=business_title:/(Sen|Jun)ior/`Junior kifejezéssel rendelkező feladatok keresése: .

```GET
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2019-05-06&queryType=full&$count=true&searchFields=business_title&$select=business_title&search=business_title:/(Sen|Jun)ior/
```

  ![Regex lekérdezés](media/search-query-lucene-examples/regex.png)

> [!Note]
> A Regex lekérdezéseket a rendszer nem [elemzi.](https://docs.microsoft.com/azure/search/search-lucene-query-architecture#stage-2-lexical-analysis) A nem teljes lekérdezési kifejezéseken végrehajtott egyetlen átalakítás a csökkentés.
>

## <a name="example-7-wildcard-search"></a>7. példa: Helyettesítő karakteres keresés
Az általánosan elismert szintaxist\*több ( ) vagy egy (?) karakteres helyettesítő karakteres kereséshez is használhatja. Megjegyzés: A Lucene lekérdezéselemző támogatja ezeknek a szimbólumoknak a használatát egyetlen kifejezéssel, nem pedig kifejezéssel.

### <a name="partial-query-string"></a>Részleges lekérdezési karakterlánc

```http
searchFields=business_title&$select=business_title&search=business_title:prog*
```

### <a name="full-url"></a>Teljes URL-cím

Ebben a lekérdezésben keressen olyan feladatokat, amelyek tartalmazzák a "prog" előtagot, amely magában foglalná az üzleti címeket a programozás és a programozó kifejezésekkel. Nem használhat * vagy ? szimbólumot a keresés első karaktereként.

```GET
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2019-05-06&queryType=full&$count=true&searchFields=business_title&$select=business_title&search=business_title:prog*
```
  ![Helyettesítő karakteres lekérdezés](media/search-query-lucene-examples/wildcard.png)

> [!Note]
> A helyettesítő karakteres lekérdezéseket a program nem [elemzi.](https://docs.microsoft.com/azure/search/search-lucene-query-architecture#stage-2-lexical-analysis) A nem teljes lekérdezési kifejezéseken végrehajtott egyetlen átalakítás a csökkentés.
>

## <a name="next-steps"></a>További lépések
Próbálja meg megadni a Lucene lekérdezéselemzőt a kódban. Az alábbi hivatkozások bemutatják, hogyan állíthat be keresési lekérdezéseket a .NET és a REST API-hoz. A hivatkozások az alapértelmezett egyszerű szintaxist használják, ezért a **queryType**megadásához alkalmaznia kell a cikkből tanultakat.

* [Az index lekérdezése a .NET SDK használatával](search-query-dotnet.md)
* [Az index lekérdezése a REST API használatával](search-create-index-rest-api.md)

További szintaxis-referencia, lekérdezési architektúra és példák az alábbi hivatkozásokon találhatók:

+ [Példák egyszerű szintaxislekérdezési példák](search-query-simple-examples.md)
+ [A teljes szöveges keresés működése az Azure Cognitive Searchben](search-lucene-query-architecture.md)
+ [Egyszerű lekérdezési szintaxis](https://docs.microsoft.com/rest/api/searchservice/simple-query-syntax-in-azure-search)
+ [Teljes Lucene lekérdezési szintaxis](https://docs.microsoft.com/rest/api/searchservice/lucene-query-syntax-in-azure-search)