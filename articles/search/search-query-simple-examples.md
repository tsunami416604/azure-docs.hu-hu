---
title: Egyszerű lekérdezés létrehozása
titleSuffix: Azure Cognitive Search
description: Például a teljes szöveges keresés egyszerű szintaxisán alapuló lekérdezések futtatásához, a szűrőkeresés, a földrajzi keresés, a faceted keresés az Azure Cognitive Search indexe alapján.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 42ace2444c7d8e790c7575a7c1a7b3b1219d090a
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/03/2020
ms.locfileid: "80656418"
---
# <a name="create-a-simple-query-in-azure-cognitive-search"></a>Egyszerű lekérdezés létrehozása az Azure Cognitive Search szolgáltatásban

Az Azure Cognitive [Search, az egyszerű lekérdezés szintaxisa](query-simple-syntax.md) meghívja az alapértelmezett lekérdezéselemző teljes szöveges keresési lekérdezések egy indexen. Ez az elemző gyors, és kezeli a gyakori forgatókönyveket, beleértve a teljes szöveges keresést, a szűrt és a kifejezésre végzett keresést, valamint a földrajzi keresést. 

Ebben a cikkben példákat használunk az egyszerű szintaxis szemléltetésére.

Egy másik lekérdezés szintaxisa [a Full Lucene,](query-lucene-syntax.md)amely összetettebb lekérdezési struktúrákat támogat, például az intelligens és helyettesítő karakteres keresést, ami további időt vehet igénybe. További információt és a teljes szintaxist bemutató példákat [a Teljes Lucene szintaxis használata](search-query-lucene-examples.md)című témakörben talál.

## <a name="formulate-requests-in-postman"></a>A kérelmek megfogalmazása a Postmanben

A következő példák a [New York-i OpenData kezdeményezés](https://nycopendata.socrata.com/) által biztosított adatkészlet alapján elérhető állásokból álló NYC Jobs keresési indexet használják ki. Ezek az adatok nem tekinthetők aktuálisnak vagy teljesnek. Az index a Microsoft által biztosított sandbox szolgáltatás, ami azt jelenti, hogy nem kell egy Azure-előfizetés vagy az Azure Cognitive Search, hogy megpróbálja ezeket a lekérdezéseket.

Amire szüksége van a Postman vagy egy ezzel egyenértékű eszköz kiadó HTTP kérelmet GET. További információ: [Rövid útmutató: Az Azure Cognitive Search REST API felfedezése a Postman használatával](search-get-started-postman.md)című témakörben olvashat.

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

A lekérdezési **`search=*`** karakterlánc , a null vagy üres keresés meghatározatlan keresési megfelelője. Ez nem különösebben hasznos, de ez a legegyszerűbb keresés, amit tehetünk.

Tetszés szerint **`$count=true`** hozzáadhatja az URL-címhez a keresési feltételeknek megfelelő dokumentumok számát. Egy üres keresési karakterlánc, ez az összes dokumentumot az indexben (kb. 2800 esetében NYC Jobs).

## <a name="how-to-invoke-simple-query-parsing"></a>Egyszerű lekérdezéselemzés megmeghívása

Az interaktív lekérdezések esetében nem kell megadnia semmit: az alapértelmezett egyszerű. A kódban, ha korábban meglett meghívva **queryType=full** a teljes lekérdezés szintaxisához, alaphelyzetbe állíthatja az alapértelmezett et a **queryType=simple**.

## <a name="example-1-field-scoped-query"></a>1. példa: Mezőhatókörrel kapcsolatos lekérdezés

Ez az első példa nem elemző-specifikus, de az első alapvető lekérdezési koncepció, az elszigetelés bevezetéséhez vezetünk. Ez a példa a lekérdezés végrehajtását és a választ csak néhány konkrét mezőre. Az olvasható JSON-válasz strukturálásának ismerete akkor fontos, ha az eszköz postás vagy kereső. 

Röviden, a lekérdezés csak a *business_title* mezőt célozza meg, és csak az üzleti címeket adja vissza. A szintaxis a **searchFields ,** hogy korlátozza a lekérdezés végrehajtását, hogy csak a business_title mezőt, és **válassza ki,** hogy mely mezők szerepelnek a válaszban.

### <a name="partial-query-string"></a>Részleges lekérdezési karakterlánc

```http
searchFields=business_title&$select=business_title&search=*
```

Ugyanaz a lekérdezés, amely több mezőt tartalmazó, vesszővel tagolt listában.

```http
search=*&searchFields=business_title, posting_type&$select=business_title, posting_type
```

### <a name="full-url"></a>Teljes URL-cím

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2019-05-06&$count=true&searchFields=business_title&$select=business_title&search=*
```

A lekérdezésre adott válasznak az alábbi képernyőképhez hasonlóan kell kinéznie.

  ![Postás minta válasz](media/search-query-lucene-examples/postman-sample-results.png)

Lehet, hogy észrevette a keresési pontszámot a válaszban. Az 1-es egységes pontszámok akkor fordulnak elő, ha nincs rang, vagy azért, mert a keresés nem volt teljes szöveges keresés, vagy mert nem alkalmaztak feltételt. A feltétel nélküli null kereséshez a sorok tetszőleges sorrendben térnek vissza. Ha tényleges feltételeket ad meg, látni fogja, hogy a keresési pontszámok értelmes értékekké alakulnak.

## <a name="example-2-look-up-by-id"></a>2. példa: Felnézés azonosító szerint

Ez a példa egy kicsit atipikus, de a keresési viselkedések kiértékelésekor érdemes megvizsgálni egy adott dokumentum teljes tartalmát, hogy megértse, miért került bele vagy nem került ki az eredményekből. Ha egy dokumentumot teljes egészében vissza szeretne adni, a dokumentumazonosítóban átadhatja a [lookup műveletet.](https://docs.microsoft.com/rest/api/searchservice/lookup-document)

Minden dokumentum egyedi azonosítóval rendelkezik. A lekérdezés szintaxisának kipróbálásához először adja vissza a dokumentumazonosítók listáját, hogy megtalálja a használhatót. Nyc-feladatok esetén az azonosítók a `id` mezőben tárolódnak.

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2019-05-06&$count=true&searchFields=id&$select=id&search=*
```

A következő példa egy "9E1E3AF9-0660-4E00-AF51-9B654925A2D5" (9E1E3AF9-0660-4E00-AF51-9B654925A2D5) alapján `id` egy adott dokumentumot visszaad, amely először az előző válaszban jelent meg. A következő lekérdezés a teljes dokumentumot adja vissza, nem csak a kijelölt mezőket. 

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs/9E1E3AF9-0660-4E00-AF51-9B654925A2D5?api-version=2019-05-06&$count=true&search=*
```

## <a name="example-3-filter-queries"></a>3. példa: Lekérdezések szűrése

[A szűrő szintaxisa](https://docs.microsoft.com/azure/search/search-query-odata-filter) olyan OData-kifejezés, amelyet **kereséssel** vagy önmagában is használhat. Egy keresési paraméter nélküli önálló szűrő akkor hasznos, ha a szűrőkifejezés képes az érdeklődésre számot tartó dokumentumok teljes minősítésére. Lekérdezési karakterlánc nélkül nincs lexikális vagy nyelvi elemzés, nincs pontozás (az összes pontszám 1), és nincs rangsorolás. Figyelje meg, hogy a keresési karakterlánc üres.

```http
POST /indexes/nycjobs/docs/search?api-version=2019-05-06
    {
      "search": "",
      "filter": "salary_frequency eq 'Annual' and salary_range_from gt 90000",
      "select": "job_id, business_title, agency, salary_range_from",
      "count": "true"
    }
```

Együtt használva a szűrő először a teljes indexre lesz alkalmazva, majd a keresés a szűrő eredményein történik. A szűrők éppen ezért hasznosak a lekérdezés teljesítményének javítására, mivel általuk lecsökkenthető a keresési lekérdezés által feldolgozandó dokumentumok köre.

  ![Lekérdezési válasz szűrése](media/search-query-simple-examples/filtered-query.png)

Ha szeretné kipróbálni ezt a Postman segítségével GET, akkor illessze be ezt a karakterláncot:

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2019-05-06&$count=true&$select=job_id,business_title,agency,salary_range_from&search=&$filter=salary_frequency eq 'Annual' and salary_range_from gt 90000
```

A szűrő és a keresés **`search.ismatch*()`** kombinálásának másik hatékony módja a szűrőkifejezés, ahol a szűrőn belül keresési lekérdezést használhat. Ez a szűrőkifejezés helyettesítő karaktert használ a *tervben* a business_title kiválasztásához, beleértve a terv, a tervező, a tervezés stb.

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2019-05-06&$count=true&$select=job_id,business_title,agency&search=&$filter=search.ismatch('plan*', 'business_title', 'full', 'any')
```

A függvényről további információt a [search.ismatch című témakörben talál a "Példák szűrése" című témakörben.](https://docs.microsoft.com/azure/search/search-query-odata-full-text-search-functions#examples)

## <a name="example-4-range-filters"></a>4. példa: Tartományszűrők

A tartományszűrést **`$filter`** bármilyen adattípus kifejezései támogatják. A következő példák numerikus és karakterláncmezőkön keresnek. 

Az adattípusok fontosak a tartományszűrőkben, és akkor működnek a legjobban, ha a numerikus adatok numerikus mezőkben, a karakterlánc-adatok pedig karakterláncmezőkben találhatók. Numerikus adatok karakterláncmezőkben nem alkalmas tartományok, mert numerikus karakterláncok nem hasonlíthatók össze az Azure Cognitive Search. 

A következő példák POST formátumúak az olvashatóság érdekében (numerikus tartomány, majd szövegtartomány):

```http
POST /indexes/nycjobs/docs/search?api-version=2019-05-06
    {
      "search": "",
      "filter": "num_of_positions ge 5 and num_of_positions lt 10",
      "select": "job_id, business_title, num_of_positions, agency",
      "orderby": "agency",
      "count": "true"
    }
```
  ![Tartományszűrő numerikus tartományokhoz](media/search-query-simple-examples/rangefilternumeric.png)


```http
POST /indexes/nycjobs/docs/search?api-version=2019-05-06
    {
      "search": "",
      "filter": "business_title ge 'A*' and business_title lt 'C*'",
      "select": "job_id, business_title, agency",
      "orderby": "business_title",
      "count": "true"
    }
```

  ![Tartományszűrő szövegtartományokhoz](media/search-query-simple-examples/rangefiltertext.png)

Ezeket a Postman-ben is kipróbálhatja a GET használatával:

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2019-05-06&search=&$filter=num_of_positions ge 5 and num_of_positions lt 10&$select=job_id, business_title, num_of_positions, agency&$orderby=agency&$count=true
```

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2019-05-06&search=&$filter=business_title ge 'A*' and business_title lt 'C*'&$select=job_id, business_title, agency&$orderby=business_title&$count=true
```

> [!NOTE]
> Értéktartományok on-val való faceting gyakori keresési alkalmazás követelmény. A színtartomány-navigációs struktúrák szűrőinek létrehozásáról további információt és példákat a ["Szűrés tartomány alapján" című témakörben talál: *"Szűrés*tartomány alapján" című témakörben.](search-faceted-navigation.md#filter-based-on-a-range)

## <a name="example-5-geo-search"></a>5. példa: Földrajzi keresés

A mintaindex tartalmaz egy geo_location mezőt szélességi és hosszúsági koordinátákkal. Ez a példa a [geo.distance függvényt](https://docs.microsoft.com/azure/search/search-query-odata-geo-spatial-functions#examples) használja, amely egy kiindulási pont kerületén belül szűri a dokumentumokat, tetszőleges távolságra (kilométerben) ki. A lekérdezés (4) utolsó értékének módosításával csökkentheti vagy nagyíthatja a lekérdezés felületét.

A következő példa POST formátumban olvasható:

```http
POST /indexes/nycjobs/docs/search?api-version=2019-05-06
    {
      "search": "",
      "filter": "geo.distance(geo_location, geography'POINT(-74.11734 40.634384)') le 4",
      "select": "job_id, business_title, work_location",
      "count": "true"
    }
```
Az olvashatóbb eredmények érdekében a keresési eredmények et úgy vágja le a program, hogy tartalmazzák a beosztásazonosítót, a beosztást és a munkahelyi helyet. A kezdő koordinátákat egy véletlenszerű dokumentumból szerezték be az indexben (ebben az esetben egy Staten Island-i munkahelyhez.

Azt is próbálja ki ezt a Postman segítségével GET:

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2019-05-06&$count=true&search=&$select=job_id, business_title, work_location&$filter=geo.distance(geo_location, geography'POINT(-74.11734 40.634384)') le 4
```

## <a name="example-6-search-precision"></a>6. példa: Keresés pontossága

A kifejezéslekérdezések egyetlen kifejezések, amelyek közül talán sok is, amelyek et egymástól függetlenül értékelik ki. A kifejezéslekérdezéseket idézőjelek közé kell tenni, és szó szerinti karakterláncként kell kiértékelni. A mérkőzés pontosságát operátorok és searchMode szabályozzák.

1. **`&search=fire`** példa: 150 eredményt ad vissza, ahol az összes egyezés a dokumentumban valahol a tűz szót tartalmazza.

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2019-05-06&$count=true&search=fire
```

2. **`&search=fire department`** példa: 2002-es eredményeket ad vissza. A rendszer visszaküldi az egyezéseket a tűzoltóságot vagy a részleget tartalmazó dokumentumokhoz.

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2019-05-06&$count=true&search=fire department
```

3. **`&search="fire department"`** példa: 82 eredményt ad vissza. A karakterlánc idézőjelek közé foglalása mindkét kifejezésre szó szerinti keresés, és az egyezések tokenizált kifejezéseken találhatók az egyesített kifejezésekből álló indexben. Ez megmagyarázza, hogy **`search=+fire +department`** egy ilyen keresés miért nem egyenértékű. Mindkét feltétel szükséges, de a beolvasott önállóan. 

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2019-05-06&$count=true&search="fire department"
```

## <a name="example-7-booleans-with-searchmode"></a>7. példa: Logikai értékek a searchMode segítségével

Az egyszerű szintaxis karakter ( karakter formájában`+, -, |`támogatja a logikai operátorokat . . A searchMode paraméter tájékoztatja a pontosság és `searchMode=any` a visszahívás közötti kompromisszumokat, előnyben részesítve a visszahívást (a feltételek egyeztetése megfelel az eredményhalmaz dokumentumának), és `searchMode=all` előnyben részesíti a pontosságot (minden feltételnek egyeztethetőnek kell lennie). Az alapértelmezett `searchMode=any`beállítás a , amely zavaró lehet, ha egy lekérdezést több operátorral halmoz fel, és a szűkebb eredmények helyett szélesebb lesz. Ez különösen igaz a NEM, ahol az eredmények tartalmazzák az összes dokumentumot "nem tartalmaz" egy adott kifejezést.

Az alapértelmezett searchMode (bármely) használatával 2800 dokumentumot ad vissza: azokat, amelyek a többrészes "tűzoltóság" kifejezést tartalmazzák, valamint minden olyan dokumentumot, amely nem rendelkezik a "Metrotech Center" kifejezéssel.

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2019-05-06&$count=true&searchMode=any&search="fire department"  -"Metrotech Center"
```

  ![keresési mód bármely](media/search-query-simple-examples/searchmodeany.png)

A searchMode `all` módosítása a feltételekre gyakorolt kumulatív hatás érvényesítésére, és egy kisebb eredményhalmazt - 21 dokumentumot - ad vissza, amely a teljes "tűzoltóság" kifejezést tartalmazó dokumentumokat tartalmaz, mínusz a Metrotech Center címén végzett feladatok.

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2019-05-06&$count=true&searchMode=all&search="fire department"  -"Metrotech Center"
```
  ![keresési mód minden](media/search-query-simple-examples/searchmodeall.png)

## <a name="example-8-structuring-results"></a>8. példa: Az eredmények strukturálása

Számos paraméter szabályozza, hogy mely mezők szerepeljenek a keresési eredményekközött, az egyes kötegekben visszaadott dokumentumok száma és a rendezési sorrend. Ez a példa az előző példák közül néhányat újra előad, az eredményeket a **$select** utasítás és a szó szerinti keresési feltételek használatával bizonyos mezőkre korlátozza, és 82 egyezést ad vissza. 

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2019-05-06&$count=true&$select=job_id,agency,business_title,civil_service_title,work_location,job_description&search="fire department"
```
Az előző példához fűzve cím szerint rendezhet. Ez a rendezés azért működik, mert civil_service_title *rendezhető* az indexben.

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2019-05-06&$count=true&$select=job_id,agency,business_title,civil_service_title,work_location,job_description&search="fire department"&$orderby=civil_service_title
```

A lapozási eredmények a **$top** paraméter használatával valósulnak meg, ebben az esetben az első 5 dokumentum visszaadása:

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2019-05-06&$count=true&$select=job_id,agency,business_title,civil_service_title,work_location,job_description&search="fire department"&$orderby=civil_service_title&$top=5&$skip=0
```

A következő 5-ös beszerezéséhez hagyja ki az első adagot:

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2019-05-06&$count=true&$select=job_id,agency,business_title,civil_service_title,work_location,job_description&search="fire department"&$orderby=civil_service_title&$top=5&$skip=5
```

## <a name="next-steps"></a>További lépések
Próbálja meg megadni a lekérdezéseket a kódban. Az alábbi hivatkozások bemutatják, hogyan állíthatók be keresési lekérdezések a .NET és a REST API számára az alapértelmezett egyszerű szintaxis használatával.

* [Az index lekérdezése a .NET SDK használatával](search-query-dotnet.md)
* [Az index lekérdezése a REST API használatával](search-create-index-rest-api.md)

További szintaxis-referencia, lekérdezési architektúra és példák az alábbi hivatkozásokon találhatók:

+ [Lucene szintaxislekérdezési példák speciális lekérdezések létrehozásához](search-query-lucene-examples.md)
+ [A teljes szöveges keresés működése az Azure Cognitive Searchben](search-lucene-query-architecture.md)
+ [Egyszerű lekérdezési szintaxis](https://docs.microsoft.com/rest/api/searchservice/simple-query-syntax-in-azure-search)
+ [Teljes Lucene-lekérdezés](https://docs.microsoft.com/rest/api/searchservice/lucene-query-syntax-in-azure-search)
+ [Szűrés és rendelési szintaxis](https://docs.microsoft.com/rest/api/searchservice/odata-expression-syntax-for-azure-search)
