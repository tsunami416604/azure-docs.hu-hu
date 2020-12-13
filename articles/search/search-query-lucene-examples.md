---
title: Teljes Lucene lekérdezési szintaxis használata
titleSuffix: Azure Cognitive Search
description: A Lucene lekérdezési szintaxisa a fuzzy kereséshez, a közelségi kereséshez, a kifejezés fokozásához, a reguláris kifejezéses kereséshez és a helyettesítő karakteres keresésekhez egy Azure Cognitive Search szolgáltatásban.
manager: nitinme
author: HeidiSteen
ms.author: heidist
tags: Lucene query analyzer syntax
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 10/05/2020
ms.openlocfilehash: 406233fd93ca76a683cf9f9a9e857de9099705ef
ms.sourcegitcommit: 1bdcaca5978c3a4929cccbc8dc42fc0c93ca7b30
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/13/2020
ms.locfileid: "97368545"
---
# <a name="use-the-full-lucene-search-syntax-advanced-queries-in-azure-cognitive-search"></a>A "teljes" Lucene keresési szintaxis használata (speciális lekérdezések az Azure Cognitive Searchban)

Az Azure Cognitive Search-lekérdezések létrehozásakor lecserélheti az alapértelmezett [egyszerű lekérdezés-elemzőt](query-simple-syntax.md) az Azure Cognitive Search nagyobb teljesítményű [Lucene-lekérdezési elemzője](query-lucene-syntax.md) segítségével, így speciális és speciális lekérdezési definíciókat hozhat létre. 

A Lucene-elemző támogatja az összetett lekérdezési szerkezeteket, például a mezők hatókörű lekérdezéseket, a zavaros keresést, a Infix és az utótag helyettesítő karakteres keresését, a közelségi keresést, a kifejezés növelését és a reguláris kifejezések keresését. További feldolgozási követelmények is elérhetők, ezért érdemes lehet valamivel több végrehajtási időt várnia. Ebben a cikkben áttekintheti a lekérdezési műveleteket bemutató példákat a teljes szintaxis alapján.

> [!Note]
> A teljes Lucene lekérdezési szintaxison keresztül engedélyezett speciális lekérdezési szerkezetek közül sok nem kerül [szöveg-elemzésre](search-lucene-query-architecture.md#stage-2-lexical-analysis), ami meglepő lehet, ha az eredmény vagy a morfológiai elemzéshez. A lexikális analízis csak teljes feltételekkel (lekérdezés vagy kifejezéses lekérdezés) hajtható végre. Hiányos kifejezésekkel rendelkező lekérdezések (előtag-lekérdezés, helyettesítő karakteres lekérdezés, regex lekérdezés, fuzzy lekérdezés) közvetlenül a lekérdezési fában lesznek hozzáadva, az elemzési szakasz megkerülésével. Az egyetlen, a részleges lekérdezési feltételeken végrehajtott átalakítás lowercasing. 
>

## <a name="nyc-jobs-examples"></a>NYC-feladatok – példák

Az alábbi példák a [New York-i OpenData kezdeményezés](https://nycopendata.socrata.com/)által biztosított adatkészletek alapján elérhető feladatokat tartalmazó [NYC-feladatok keresési indexét](https://azjobsdemo.azurewebsites.net/) használják. Ezek az adathalmazok nem tekintendők aktuálisnak vagy teljesnek. Az index a Microsoft által biztosított sandbox-szolgáltatáson alapul, ami azt jelenti, hogy a lekérdezések kipróbálásához nincs szükség Azure-előfizetésre vagy Azure-Cognitive Searchra.

Mi a teendő, ha a Poster vagy egy azzal egyenértékű eszköz a HTTP-kérelem közzétételéhez a GET vagy a POST szolgáltatásban. Ha nem ismeri ezeket az eszközöket, tekintse meg a gyors útmutató [: az Azure Cognitive Search REST API megismerése](search-get-started-rest.md)című témakört.

## <a name="set-up-the-request"></a>A kérelem beállítása

1. A kérelem fejlécének a következő értékekkel kell rendelkeznie:

   | Kulcs | Érték |
   |-----|-------|
   | Content-Type | `application/json`|
   | API-kulcs  | `252044BE3886FE4A8E3BAA4F595114BB` </br> (ez a tényleges lekérdezési API-kulcs a NYC-feladatok indexét üzemeltető sandbox keresési szolgáltatáshoz) |

1. A művelet beállítása a következőre: **`GET`** .

1. Az URL-cím beállítása **`https://azs-playground.search.windows.net/indexes/nycjobs/docs/search=*&api-version=2020-06-30&queryType=full`**

   + Az index dokumentumok gyűjteménye minden kereshető tartalmat tartalmaz. A kérelem fejlécében megadott lekérdezési API-kulcs csak olyan olvasási műveletekhez használható, amelyek a dokumentumok gyűjteményét célozzák meg.

   + **`$count=true`** a keresési feltételeknek megfelelő dokumentumok számát adja vissza. Üres keresési sztring esetén a szám az index összes dokumentuma lesz (körülbelül 2558 a NYC-feladatok esetében).

   + **`search=*`** egy meghatározatlan lekérdezés, amely null vagy üres kereséssel egyenértékű. Ez nem különösen hasznos, de ez a legegyszerűbb keresési lehetőség, amely az index összes lekérdezhető mezőjét megjeleníti az összes értékkel együtt.

   + **`queryType=full`** meghívja a teljes Lucene-elemzőt.

1. Ellenőrzési lépésként illessze be a következő kérelmet a GET mezőbe, és kattintson a **Küldés** gombra. Az eredményeket a rendszer részletes JSON-dokumentumként adja vissza.

   ```http
   https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2020-06-30&$count=true&search=*&queryType=full
   ```

### <a name="how-to-invoke-full-lucene-parsing"></a>A teljes Lucene-elemzés meghívása

A Hozzáadás **`queryType=full`** gombra kattintva meghívja a teljes lekérdezési szintaxist, felülbírálva az alapértelmezett egyszerű lekérdezési szintaxist. A cikkben szereplő összes példa megadja a **`queryType=full`** keresési paramétert, amely azt jelzi, hogy a teljes szintaxist a Lucene lekérdezés-elemző kezeli. 

```http
POST /indexes/nycjobs/docs/search?api-version=2020-06-30
{
    "count": true,
    "queryType": "full",
    "search": "*"
}
```

## <a name="example-1-query-scoped-to-a-list-of-fields"></a>1. példa: a lekérdezés hatóköre a mezők listájára terjed ki

Ez az első példa nem elemző-specifikus, de az első alapvető lekérdezési koncepció bevezetéséhez vezetünk: a tárolás. Ebben a példában a lekérdezés-végrehajtás és a válasz is csak néhány konkrét mezőre van korlátozva. Az olvasható JSON-válaszok szerkezetének ismerete fontos, ha az eszköz Poster vagy Search Explorer. 

Ez a lekérdezés csak a (z) *business_title* a-ben **`searchFields`** , a **`select`** válaszban a paraméterrel megegyező mezőben megadott értékkel.

```http
POST https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2020-06-30
{
    "count": true,
    "queryType": "full",
    "search": "*",
    "searchFields": "business_title",
    "select": "business_title"
}
```

A lekérdezésre adott válasznak az alábbi képernyőképhez hasonlóan kell kinéznie.

  ![Poster-minta válasza pontszámokkal](media/search-query-lucene-examples/postman-sample-results.png)

Lehetséges, hogy észrevette a keresési pontszámot a válaszban. **1** egységes pontszám akkor fordul elő, ha nincs rangsor, vagy mert a keresés nem teljes szöveges keresést tartalmaz, vagy ha nem adtak meg feltételeket. Üres keresés esetén a sorok tetszőleges sorrendben jönnek vissza. A tényleges feltételek belefoglalásakor a keresési pontszámok jelentős értékekre lesznek kialakítva.

## <a name="example-2-fielded-search"></a>2. példa: mező szerinti keresés

A teljes Lucene szintaxisa támogatja az egyes keresési kifejezések adott mezőhöz való hatókörét. Ez a példa olyan üzleti címeket keres, amelyek a legfelső szintűek, de nem a Junior kifejezéssel rendelkeznek. Több mezőt is megadhat a és a használatával.

```http
POST /indexes/nycjobs/docs?api-version=2020-06-30
{
    "count": true,
    "queryType": "full",
    "search": "business_title:(senior NOT junior) AND posting_type:external",
    "searchFields": "business_title, posting_type",
    "select": "business_title, posting_type"
}
```

A lekérdezésre adott válasznak a következő képernyőképhez hasonlóan kell kinéznie (posting_type nem látható).

  :::image type="content" source="media/search-query-lucene-examples/intrafieldfilter.png" alt-text="Poster-minta válaszának keresési kifejezése" border="false":::

A keresési kifejezés lehet egyetlen szó vagy kifejezés, vagy egy összetettebb kifejezés zárójelben, opcionálisan logikai operátorokkal. Néhány példa a következőkre:

+ `business_title:(senior NOT junior)`
+ `state:("New York" OR "New Jersey")`
+ `business_title:(senior NOT junior) AND posting_type:external`

Ügyeljen arra, hogy az idézőjelek között több karakterláncot is helyezzen el, ha azt szeretné, hogy mindkét sztring egyetlen entitásként legyen kiértékelve, mivel ebben az esetben két különböző helyet keres a `state` mezőben. Az eszköztől függően előfordulhat, hogy el kell menekülnie ( `\` ) az idézőjeleket. 

A **mezőnév: searchExpression** mezőben megadott mezőnek kereshető mezőnek kell lennie. További részletekért lásd: [create index (Azure Cognitive Search REST API)](/rest/api/searchservice/create-index) .

> [!NOTE]
> A fenti példában a paraméter ki **`searchFields`** van hagyva, mert a lekérdezés egyes részei explicit módon vannak megadva. Azonban továbbra is használhatja, **`searchFields`** Ha a lekérdezés több részből áll (using és utasítások). Például a lekérdezés `search=business_title:(senior NOT junior) AND external&searchFields=posting_type` `senior NOT junior` csak a `business_title` mezőhöz igazodik, míg a "külső" értékkel egyezik a `posting_type` mezővel. A mező megadott neve `fieldName:searchExpression` mindig elsőbbséget élvez **`searchFields`** , ezért ebben a példában a kihagyható `business_title` **`searchFields`** .

## <a name="example-3-fuzzy-search"></a>3. példa: fuzzy keresés

A teljes Lucene szintaxisa támogatja a zavaros keresést, és a hasonló szerkezettel rendelkező feltételek egyeztetését is. A zavaros kereséshez fűzze hozzá a tilde `~` szimbólumot egyetlen szó végén egy opcionális paraméterrel, egy 0 és 2 közötti értéket, amely megadja a szerkesztési távolságot. Például a `blue~` `blue~1` kék, a blues és a ragasztó is visszatérhet.

```http
POST /indexes/nycjobs/docs?api-version=2020-06-30
{
    "count": true,
    "queryType": "full",
    "search": "business_title:asosiate~",
    "searchFields": "business_title",
    "select": "business_title"
}
```

A kifejezések nem támogatottak közvetlenül, de a több részből álló kifejezések minden egyes időszakában megadható egy fuzzy egyezés, például: `search=business_title:asosiate~ AND comm~` .  Az alábbi képernyőképen a válasz tartalmazza a *közösségi munkatárs* egyezését.

  :::image type="content" source="media/search-query-lucene-examples/fuzzysearch.png" alt-text="Fuzzy keresési válasz" border="false":::

> [!Note]
> A zavaros lekérdezések [elemzése](search-lucene-query-architecture.md#stage-2-lexical-analysis)nem történik meg. Hiányos kifejezésekkel rendelkező lekérdezések (előtag-lekérdezés, helyettesítő karakteres lekérdezés, regex lekérdezés, fuzzy lekérdezés) közvetlenül a lekérdezési fában lesznek hozzáadva, az elemzési szakasz megkerülésével. Az egyetlen, a részleges lekérdezési feltételeken végrehajtott átalakítás lowercasing.
>

## <a name="example-4-proximity-search"></a>4. példa: a közelség keresése

A közelségi keresések megkeresik a dokumentumokban egymáshoz közeli kifejezéseket. Szúrjon be egy "~" szimbólumot egy kifejezés végén, majd a közelségi határt létrehozó szavak számát. Például a "Hotel Airport" ~ 5 megkeresi a (z) és a repülőtér használati feltételeit egy dokumentumban lévő, egymástól eltérő 5 szóból.

Ez a lekérdezés megkeresi a "vezető" és az "elemző" kifejezéseket, ahol az egyes kifejezéseket nem több szó választja el, és az idézőjelek megmaradnak ( `\"` ) a kifejezés megőrzése érdekében:

```http
POST /indexes/nycjobs/docs?api-version=2020-06-30
{
    "count": true,
    "queryType": "full",
    "search": "business_title:\"senior analyst\"~1",
    "searchFields": "business_title",
    "select": "business_title"
}
```

A lekérdezésre adott válasznak a következő képernyőképhez hasonlóan kell kinéznie 

  :::image type="content" source="media/search-query-lucene-examples/proximity-before.png" alt-text="Közelségi lekérdezés" border="false":::

Próbálja ki újra, kiküszöbölve a távolságot ( `~0` ) a "vezető elemző" kifejezés között. Figyelje meg, hogy a lekérdezés 8 dokumentumot ad vissza, az előző lekérdezés esetében pedig 10.

```http
POST /indexes/nycjobs/docs?api-version=2020-06-30
{
    "count": true,
    "queryType": "full",
    "search": "business_title:\"senior analyst\"~0",
    "searchFields": "business_title",
    "select": "business_title"
}
```

## <a name="example-5-term-boosting"></a>5. példa: a kifejezés fokozása

A kifejezés növelése arra utal, hogy a dokumentum rangsorolása magasabb, ha a megnövelt kifejezést tartalmazza, a kifejezést nem tartalmazó dokumentumokhoz képest. Egy kifejezés növeléséhez használja a kalapot, a `^` szimbólumot a keresett kifejezés végén lévő kiemelés faktor (a szám) karakterrel.

Ebben az "előtt" lekérdezésben keressen feladatokat a *számítógép-elemző* kifejezéssel, és figyelje meg, hogy a *számítógépek és az* *elemzők* között nincs találat, de a *számítógép* -feladatok még a találatok elején vannak.

```http
POST /indexes/nycjobs/docs?api-version=2020-06-30
{
    "count": true,
    "queryType": "full",
    "search": "business_title:computer analyst",
    "searchFields": "business_title",
    "select": "business_title"
}
```

Az "After" lekérdezésben ismételje meg a keresést, ez az idő fokozza az eredményeket az *elemző* kifejezéssel a *számítógép* kifejezése alatt, ha a két szó nem létezik. A lekérdezés egy olvasható verziója `search=business_title:computer analyst^2` . A Poster-on való működőképes lekérdezés a következőként `^2` van kódolva: `%5E2` .

```http
POST /indexes/nycjobs/docs?api-version=2020-06-30
{
    "count": true,
    "queryType": "full",
    "search": "business_title:computer analyst%5e2",
    "searchFields": "business_title",
    "select": "business_title"
}
```

A lekérdezésre adott válasznak az alábbi képernyőképhez hasonlóan kell kinéznie.

  :::image type="content" source="media/search-query-lucene-examples/termboostingafter.png" alt-text="Kiemelési időszak" border="false":::

A kifejezés fokozása különbözik az adott pontozási profilokban lévő pontozási profiloktól, és nem adott feltételekkel javítja bizonyos mezőket. A következő példa segít illusztrálni a különbségeket.

Vegyünk egy pontozási profilt, amely fellendíti a egyezéseket egy adott mezőben, például a **műfajt** a musicstoreindex példában. A kifejezés fokozása felhasználható a másoknál nagyobb keresési kifejezések további növelésére. Például a "Rock ^ 2 Electronic" olyan dokumentumokat fog növelni, amelyekben a kifejezés a **műfaj** mezőben magasabb, mint az index más kereshető mezőinél is szerepel. Továbbá a "rock" keresési kifejezést tartalmazó dokumentumok nagyobbak lesznek, mint a "elektronikus" keresési kifejezés, amely a "KRADEB" kifejezés eredményét eredményezi (2).

A faktor szintjének beállításakor minél magasabb a növelési tényező, annál nagyobb a kifejezés, mint a többi keresési kifejezéshez képest. Alapértelmezés szerint a Boost faktor 1. Bár a Boost faktornak pozitívnak kell lennie, kevesebb, mint 1 (például 0,2).

## <a name="example-6-regex"></a>6. példa: regex

A reguláris kifejezéses keresés a "/" perjelek közötti, a [regexp osztályban](https://lucene.apache.org/core/6_6_1/core/org/apache/lucene/util/automaton/RegExp.html)dokumentált tartalom alapján keres egyezést.

```http
POST /indexes/nycjobs/docs?api-version=2020-06-30
{
    "count": true,
    "queryType": "full",
    "search": "business_title:/(Sen|Jun)ior/",
    "searchFields": "business_title",
    "select": "business_title"
}
```

A lekérdezésre adott válasznak az alábbi képernyőképhez hasonlóan kell kinéznie.

  :::image type="content" source="media/search-query-lucene-examples/regex.png" alt-text="Regex-lekérdezés" border="false":::

> [!Note]
> A regex-lekérdezések [elemzése](./search-lucene-query-architecture.md#stage-2-lexical-analysis)nem történik meg. Az egyetlen, a részleges lekérdezési feltételeken végrehajtott átalakítás lowercasing.
>

## <a name="example-7-wildcard-search"></a>7. példa: helyettesítő karakteres keresés

Az általánosan felismerhető szintaxist használhatja több ( \* ) vagy szimpla (?) karakteres helyettesítő karakteres kereséshez. Vegye figyelembe, hogy a Lucene-lekérdezés elemzője egyetlen kifejezéssel támogatja a szimbólumok használatát, nem pedig egy kifejezést.

Ebben a lekérdezésben keressen olyan feladatokat, amelyek tartalmazzák a "PROG" előtagot, amely magában foglalja az üzleti címeket a programozási és programozói feltételekkel. A `*` Keresés első karaktere nem használható a vagy a `?` szimbólumként.

```http
POST /indexes/nycjobs/docs?api-version=2020-06-30
{
    "count": true,
    "queryType": "full",
    "search": "business_title:prog*",
    "searchFields": "business_title",
    "select": "business_title"
}
```

A lekérdezésre adott válasznak az alábbi képernyőképhez hasonlóan kell kinéznie.

  :::image type="content" source="media/search-query-lucene-examples/wildcard.png" alt-text="Helyettesítő karakteres lekérdezés" border="false":::

> [!Note]
> A helyettesítő karakteres lekérdezések [elemzése](./search-lucene-query-architecture.md#stage-2-lexical-analysis)nem történik meg. Az egyetlen, a részleges lekérdezési feltételeken végrehajtott átalakítás lowercasing.
>

## <a name="next-steps"></a>Következő lépések

Próbálja meg megadni a lekérdezéseket a kódban. Az alábbi hivatkozások azt ismertetik, hogyan állíthat be keresési lekérdezéseket az Azure SDK-k használatával.

+ [Az index lekérdezése a .NET SDK használatával](search-get-started-dotnet.md)
+ [Az index lekérdezése a Python SDK használatával](search-get-started-python.md)
+ [Az index lekérdezése a JavaScript SDK használatával](search-get-started-javascript.md)

A szintaxissal, a lekérdezési architektúrával és a példákkal kapcsolatban a következő hivatkozásokban találhat további tudnivalókat:

+ [Példák a speciális lekérdezések kiépítési Lucene](search-query-lucene-examples.md)
+ [A teljes szöveges keresés működése az Azure Cognitive Searchben](search-lucene-query-architecture.md)
+ [Egyszerű lekérdezési szintaxis](query-simple-syntax.md)
+ [Teljes Lucene lekérdezési szintaxis](query-lucene-syntax.md)
+ [Szűrési szintaxis](search-query-odata-filter.md)