---
title: Egyszerű Lucene lekérdezési szintaxis használata
titleSuffix: Azure Cognitive Search
description: Példa a lekérdezések futtatására a teljes szöveges keresés egyszerű szintaxisa, a szűrési keresés, a Geo-keresés és az Azure Cognitive Search indexek közötti részletes keresés alapján.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 12/12/2020
ms.openlocfilehash: ff9495e37a499b5502d8f8ced79b69608fa9552a
ms.sourcegitcommit: cc13f3fc9b8d309986409276b48ffb77953f4458
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/14/2020
ms.locfileid: "97401746"
---
# <a name="use-the-simple-search-syntax-in-azure-cognitive-search"></a>Az "egyszerű" keresési szintaxis használata az Azure-ban Cognitive Search

Az Azure Cognitive Searchban az [egyszerű lekérdezési szintaxis](query-simple-syntax.md) meghívja a teljes szöveges keresés alapértelmezett lekérdezés-elemzőjét. Az elemző gyorsan és gyakran kezeli a gyakori forgatókönyveket, például a teljes szöveges keresést, a szűrt és a sokoldalú keresést, valamint az előtag-keresést. Ez a cikk példákat mutat be a [keresési dokumentumok (REST API)](/rest/api/searchservice/search-documents) kérelem egyszerű szintaxisának megjelenítésére.

> [!NOTE]
> Egy alternatív lekérdezési szintaxis [teljes Lucene](query-lucene-syntax.md), amely összetettebb lekérdezési struktúrákat támogat, például a fuzzy és a helyettesítő karakterek keresését. További információt és példákat [a teljes Lucene szintaxis használata](search-query-lucene-examples.md)című témakörben talál.

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

1. Állítsa be az URL-címet a következőre: **`https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2020-06-30&search=*&$count=true`** . 

   + Az index dokumentumok gyűjteménye minden kereshető tartalmat tartalmaz. A kérelem fejlécében megadott lekérdezési API-kulcs csak olyan olvasási műveletekhez használható, amelyek a dokumentumok gyűjteményét célozzák meg.

   + **`$count=true`** a keresési feltételeknek megfelelő dokumentumok számát adja vissza. Üres keresési sztring esetén a szám az index összes dokumentuma lesz (körülbelül 2558 a NYC-feladatok esetében).

   + **`search=*`** egy meghatározatlan lekérdezés, amely null vagy üres kereséssel egyenértékű. Ez nem különösen hasznos, de ez a legegyszerűbb keresési lehetőség, amely az index összes lekérdezhető mezőjét megjeleníti az összes értékkel együtt.

1. Ellenőrzési lépésként illessze be a következő kérelmet a GET mezőbe, és kattintson a **Küldés** gombra. Az eredményeket a rendszer részletes JSON-dokumentumként adja vissza.

   ```http
   https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2020-06-30&$count=true&search=*&queryType=full
   ```

### <a name="how-to-invoke-simple-query-parsing"></a>Egyszerű lekérdezések elemzésének meghívása

Az interaktív lekérdezésekhez nem kell semmit megadnia: az egyszerű érték az alapértelmezett. Ha korábban meghívja a kódot, **`queryType=full`** alaphelyzetbe állíthatja az alapértelmezett értéket **`queryType=simple`** .

```http
POST /indexes/nycjobs/docs/search?api-version=2020-06-30
{
    "queryType": "simple"
}
```

## <a name="example-1-full-text-search-on-specific-fields"></a>1. példa: a teljes szöveges keresés adott mezőkön

Ez az első példa nem elemző-specifikus, de az első alapvető lekérdezési koncepció bevezetéséhez vezetünk: a tárolás. Ebben a példában a lekérdezés-végrehajtás és a válasz is csak néhány konkrét mezőre van korlátozva. Az olvasható JSON-válaszok szerkezetének ismerete fontos, ha az eszköz Poster vagy Search Explorer. 

Ez a lekérdezés csak a (z) *business_title* a-ben **`searchFields`** , a **`select`** válaszban a paraméterrel megegyező mezőben megadott értékkel.

```http
POST /indexes/nycjobs/docs/search?api-version=2020-06-30
{
    "count": true,
    "queryType": "simple",
    "search": "*",
    "searchFields": "business_title",
    "select": "business_title"
}
```

A lekérdezésre adott válasznak az alábbi képernyőképhez hasonlóan kell kinéznie.

  :::image type="content" source="media/search-query-lucene-examples/postman-sample-results.png" alt-text="Poster-minta válasz" border="false":::

Lehetséges, hogy észrevette a keresési pontszámot a válaszban. **1** egységes pontszám akkor fordul elő, ha nincs rangsor, vagy mert a keresés nem teljes szöveges keresést tartalmaz, vagy ha nem adtak meg feltételeket. Üres keresés esetén a sorok tetszőleges sorrendben jönnek vissza. A tényleges feltételek belefoglalásakor a keresési pontszámok jelentős értékekre lesznek kialakítva.

## <a name="example-2-look-up-by-id"></a>2. példa: Keresés azonosító alapján

Ha a keresési eredményeket egy lekérdezésben adja vissza, az azt követő logikai lépés egy olyan részleteket tartalmazó oldal megadása, amely több mezőt tartalmaz a dokumentumból. Ebből a példából megtudhatja, hogyan adhat vissza egyetlen dokumentumot egy [keresési művelettel](/rest/api/searchservice/lookup-document) a dokumentum azonosítójának átadásához.

Minden dokumentum egyedi azonosítóval rendelkezik. Ha egy keresési lekérdezés szintaxisát szeretné kipróbálni, először a dokumentumok azonosítóinak listáját kell visszaadnia, hogy megtalálja az egyiket a használathoz. A NYC-feladatok esetében az azonosítók a mezőben vannak tárolva `id` .

```http
GET /indexes/nycjobs/docs?api-version=2020-06-30&search=*&$select=id&$count=true
```

Ezután lekérhet egy dokumentumot a gyűjteményből az `id` előző válaszban elsőként megjelenő "9E1E3AF9-0660-4E00-AF51-9B654925A2D5" alapján. A következő lekérdezés a teljes dokumentum összes lekérdezhető mezőjét adja vissza.

```http
GET /indexes/nycjobs/docs/9E1E3AF9-0660-4E00-AF51-9B654925A2D5?api-version=2020-06-30
```

## <a name="example-3-filter-queries"></a>3. példa: lekérdezések szűrése

A [szűrési szintaxis](./search-query-odata-filter.md) egy olyan OData kifejezés, amelyet saját maga vagy a is használhat **`search`** . A keresési paraméter nélküli önálló szűrő akkor hasznos, ha a szűrő kifejezés képes teljes mértékben minősíteni a dokumentumokat. A lekérdezési karakterláncok nélkül nincs lexikális vagy nyelvi elemzés, nincs pontozás (az összes pontszám 1), és nincs rangsor. Figyelje meg, hogy a keresési karakterlánc üres.

```http
POST /indexes/nycjobs/docs/search?api-version=2020-06-30
    {
      "count": true,
      "search": "",
      "filter": "salary_frequency eq 'Annual' and salary_range_from gt 90000",
      "select": "job_id, business_title, agency, salary_range_from"
    }
```

Együtt használva a szűrő először a teljes indexre lesz alkalmazva, majd a keresés a szűrő eredményein történik. A szűrők éppen ezért hasznosak a lekérdezés teljesítményének javítására, mivel általuk lecsökkenthető a keresési lekérdezés által feldolgozandó dokumentumok köre.

  :::image type="content" source="media/search-query-simple-examples/filtered-query.png" alt-text="Lekérdezési válasz szűrése" border="false":::

Egy másik hatékony módszer a szűrő és a keresés összekapcsolására **`search.ismatch*()`** egy szűrési kifejezésben, ahol keresési lekérdezést használhat a szűrőn belül. Ez a szűrési kifejezés egy helyettesítő karaktert használ a *tervben* a business_title kiválasztásához, beleértve a tervet, a Plannert, a tervezést és így tovább.

```http
POST /indexes/nycjobs/docs/search?api-version=2020-06-30
    {
      "count": true,
      "search": "",
      "filter": "search.ismatch('plan*', 'business_title', 'full', 'any')",
      "select": "job_id, business_title, agency, salary_range_from"
    }
```

A függvénnyel kapcsolatos további információkért tekintse [meg a Search. ismatch "példák szűrése"](./search-query-odata-full-text-search-functions.md#examples)című témakört.

## <a name="example-4-range-filters"></a>4. példa: tartomány szűrőinek

A tartomány szűrése **`$filter`** bármely adattípushoz kifejezéssel támogatott. A következő példák a numerikus és a sztring mezőkre mutatnak keresést. 

Az adattípusok fontosak a tartományhoz tartozó szűrőkben, és akkor működnek a legjobban, ha numerikus mezőkben numerikus adat szerepel, és a sztring mezőkben sztring szerepel. A karakterlánc mezőiben szereplő numerikus adat nem alkalmas tartományokhoz, mert a numerikus karakterláncok nem összehasonlíthatóak az Azure Cognitive Searchban.

A következő lekérdezés egy numerikus tartomány:

```http
POST /indexes/nycjobs/docs/search?api-version=2020-06-30
    {
      "count": true,
      "search": "",
      "filter": "num_of_positions ge 5 and num_of_positions lt 10",
      "select": "job_id, business_title, num_of_positions, agency",
      "orderby": "agency"
    }
```
A lekérdezésre adott válasznak az alábbi képernyőképhez hasonlóan kell kinéznie.

  :::image type="content" source="media/search-query-simple-examples/rangefilternumeric.png" alt-text="Numerikus tartományokhoz tartozó tartomány-szűrő" border="false":::

Ebben a lekérdezésben a tartomány egy sztring mező (business_title) fölé esik:

```http
POST /indexes/nycjobs/docs/search?api-version=2020-06-30
    {
      "count": true,
      "search": "",
      "filter": "business_title ge 'A*' and business_title lt 'C*'",
      "select": "job_id, business_title, agency",
      "orderby": "business_title"
    }
```

A lekérdezésre adott válasznak az alábbi képernyőképhez hasonlóan kell kinéznie.

  :::image type="content" source="media/search-query-simple-examples/rangefiltertext.png" alt-text="Tartomány-szűrő a szöveges tartományokhoz" border="false":::

> [!NOTE]
> Az értékek tartományán alapuló aspektus egy gyakori keresési alkalmazásra vonatkozó követelmény. További információkért és példákért tekintse [meg a dimenziós szűrő felépítését](search-filters-facets.md)ismertető témakört.

## <a name="example-5-geo-search"></a>5. példa: Geo-keresés

A minta index egy geo_location mezőt tartalmaz szélességi és hosszúsági koordinátákkal. Ez a példa a [Geo. Distance függvényt](search-query-odata-geo-spatial-functions.md#examples) használja, amely egy kiindulási pont kerületén belüli dokumentumok szűrésére szolgál, az Ön által megadott távolságra (kilométerben). A lekérdezésben szereplő utolsó értéket (4) módosíthatja a lekérdezés felületi területének csökkentése vagy nagyítása érdekében.

```http
POST /indexes/nycjobs/docs/search?api-version=2020-06-30
    {
      "count": true,
      "search": "",
      "filter": "geo.distance(geo_location, geography'POINT(-74.11734 40.634384)') le 4",
      "select": "business_title, work_location"
    }
```

További olvasható találatok esetén a keresési eredmények kivágása a beosztás és a munkahelyi hely között történik. A kezdeti koordinátákat a rendszer egy véletlenszerű dokumentumból szerezte be az indexben (ebben az esetben a Staten Island-beli munkahelyhez.

  :::image type="content" source="media/search-query-simple-examples/geo-search.png" alt-text="Staten Island Térkép" border="false":::

## <a name="example-6-search-precision"></a>6. példa: a keresés pontossága

A kifejezéses lekérdezések egyetlen kifejezésből állnak, amelyek közül sok közülük egymástól függetlenül kerül kiértékelésre. A kifejezéses lekérdezések idézőjelek közé vannak lefoglalva, és egy Verbatim sztringként vannak kiértékelve. A egyezés pontosságát a kezelők és a searchMode vezérlik.

1. példa: `search=fire`  a 140 eredményekre illeszkedik, ahol az összes egyezés tartalmazni kívánja a Word tüzet a dokumentumban.

```http
POST /indexes/nycjobs/docs/search?api-version=2020-06-30
    {
      "count": true,
      "search": "fire"
    }
```

2. példa: a `search=fire department` 2002 eredményt adja vissza. A rendszer a tüzet vagy a részleget tartalmazó dokumentumok egyezéseit adja vissza.

```http
POST /indexes/nycjobs/docs/search?api-version=2020-06-30
    {
      "count": true,
      "search": "fire department"
    }
```

3. példa: a `search="fire department"` 77 eredményt adja vissza. Ha a karakterláncot idézőjelek közé helyezi, a kifejezés mindkét kifejezésből álló kifejezést hoz létre, és a egyezések a tokenes kifejezésekben találhatók a kombinált kifejezéseket tartalmazó indexben. Ez a cikk azt ismerteti, hogy a hasonló keresés miért `search=+fire +department` nem egyenértékű. Mindkét feltételt kötelező megadni, de egymástól függetlenül vizsgáljuk. 

```http
POST /indexes/nycjobs/docs/search?api-version=2020-06-30
    {
    "count": true,
    "search": "\"fire department\""
    }
```

> [!Note]
> Mivel a kifejezés lekérdezés idézőjelek között van megadva, ez a példa Escape-karaktert ( `\` ) ad hozzá a szintaxis megőrzése érdekében.

## <a name="example-7-booleans-with-searchmode"></a>7. példa: logikai értékek searchMode

Az egyszerű szintaxis a logikai operátorokat karakter () formában támogatja `+, -, |` . A searchMode paraméter a pontosság és a visszahívás közötti kompromisszumokat adja meg, és a **`searchMode=any`** szívességet idézi elő (a feltételek egyeztetése az eredményhalmaz dokumentumára van beállítva), és a **`searchMode=all`** pontosságot (az összes feltételt egyeztetni kell). 

Az alapértelmezett érték az **`searchMode=any`** , ami zavaró lehet, ha több operátort tartalmazó lekérdezést halmoz fel, és a szűkebb eredmények helyett szélesebb körűen fog megjelenni. Ez különösen igaz, és nem, ahol az eredmények tartalmazzák az összes olyan dokumentumot, amely "nem tartalmaz" egy adott kifejezést.

Az alapértelmezett searchMode (any), a 2800-es dokumentumok visszaadása: a "tűzoltó részleg" kifejezést, valamint az összes olyan dokumentumot, amely nem tartalmazza a "Metrotech Center" kifejezést.

```http
POST /indexes/nycjobs/docs/search?api-version=2020-06-30
    {
      "count": true,
      "search": "\"fire department\"-\"Metrotech Center\"",
      "searchMode": "any"
    }
```

A lekérdezésre adott válasznak az alábbi képernyőképhez hasonlóan kell kinéznie.

  :::image type="content" source="media/search-query-simple-examples/searchmodeany.png" alt-text="tetszőleges keresési mód" border="false":::

Ha úgy módosítja, hogy a **`searchMode=all`** feltételek kumulatív hatással legyenek a feltételekre, és egy kisebb eredményhalmaz-21 dokumentumot ad vissza, amely a teljes "tűzoltó részleg" kifejezést tartalmazza, a feladatok pedig a Metrotech Center-címen.

```http
POST /indexes/nycjobs/docs/search?api-version=2020-06-30
    {
      "count": true,
      "search": "\"fire department\"-\"Metrotech Center\"",
      "searchMode": "all"
    }
```

  :::image type="content" source="media/search-query-simple-examples/searchmodeall.png" alt-text="összes keresési mód" border="false":::

## <a name="example-8-structuring-results"></a>8. példa: az eredmények strukturálása

Számos paraméter határozza meg, hogy mely mezők szerepelnek a keresési eredmények között, az egyes kötegekben visszaadott dokumentumok száma és a rendezési sorrend. Ez a példa az előző példák közül néhányat felhasznál, és az eredményeket adott mezőkre korlátozza az **`$select`** utasítás és a Verbatim keresési feltételek használatával, a 82 találatok visszaadásával.

```http
POST /indexes/nycjobs/docs/search?api-version=2020-06-30
    {
      "count": true,
      "search": "\"fire department\"",
      "searchMode": "any",
      "select": "job_id,agency,business_title,civil_service_title,work_location,job_description"
    }
```

Az előző példához hozzáfűzve megadhatja a title (cím) sorrendet. Ez a rendezés azért működik, mert a civil_service_title az indexben *rendezhető* .

```http
POST /indexes/nycjobs/docs/search?api-version=2020-06-30
    {
      "count": true,
      "search": "\"fire department\"",
      "searchMode": "any",
      "select": "job_id,agency,business_title,civil_service_title,work_location,job_description",
      "orderby": "civil_service_title"
    }
```

A lapozási eredmények a paraméter használatával valósulnak **`$top`** meg, ebben az esetben az első 5 dokumentumot adja vissza:

```http
POST /indexes/nycjobs/docs/search?api-version=2020-06-30
    {
      "count": true,
      "search": "\"fire department\"",
      "searchMode": "any",
      "select": "job_id,agency,business_title,civil_service_title,work_location,job_description",
      "orderby": "civil_service_title",
      "top": "5"
    }
```

A következő 5 beszerzéséhez hagyja ki az első köteget:

```http
POST /indexes/nycjobs/docs/search?api-version=2020-06-30
    {
      "count": true,
      "search": "\"fire department\"",
      "searchMode": "any",
      "select": "job_id,agency,business_title,civil_service_title,work_location,job_description",
      "orderby": "civil_service_title",
      "top": "5",
      "skip": "5"
    }
```

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