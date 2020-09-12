---
title: Egyszerű lekérdezés létrehozása
titleSuffix: Azure Cognitive Search
description: Példa a lekérdezések futtatására a teljes szöveges keresés egyszerű szintaxisa, a szűrési keresés, a Geo-keresés és az Azure Cognitive Search indexek közötti részletes keresés alapján.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 3c469d7274bb90e194478af2464cb352efe7490c
ms.sourcegitcommit: 58d3b3314df4ba3cabd4d4a6016b22fa5264f05a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/02/2020
ms.locfileid: "89294866"
---
# <a name="create-a-simple-query-in-azure-cognitive-search"></a>Egyszerű lekérdezés létrehozása az Azure Cognitive Search

Az Azure Cognitive Searchban az [egyszerű lekérdezési szintaxis](query-simple-syntax.md) meghívja az alapértelmezett lekérdezési elemzőt a teljes szöveges keresési lekérdezések indexre való végrehajtásához. Ez az elemző gyors, és kezeli a gyakori forgatókönyveket, például a teljes szöveges keresést, a szűrt és a sokoldalú keresést, valamint a Geo-keresést. 

Ebben a cikkben példákat használunk az egyszerű szintaxis szemléltetésére, a `search=` [keresési dokumentumok](/rest/api/searchservice/search-documents) művelet paraméterének feltöltésére.

Egy alternatív lekérdezési szintaxis [teljes Lucene](query-lucene-syntax.md), amely összetettebb lekérdezési struktúrákat támogat, például a fuzzy és a helyettesítő karakterek keresését, ami további időt vehet igénybe. További információt és példákat a teljes szintaxissal kapcsolatban [a teljes Lucene szintaxis használata](search-query-lucene-examples.md)című témakörben talál.

## <a name="formulate-requests-in-postman"></a>Kérelmek összeállítása a Poster-ban

Az alábbi példákban a [New York OpenData Initiative City](https://nycopendata.socrata.com/) által biztosított adatkészletek alapján elérhető feladatokat tartalmazó NYC-feladatok keresési indexét használjuk. Ezek az adathalmazok nem tekintendők aktuálisnak vagy teljesnek. Az index a Microsoft által biztosított sandbox-szolgáltatáson alapul, ami azt jelenti, hogy a lekérdezések kipróbálásához nincs szükség Azure-előfizetésre vagy Azure-Cognitive Searchra.

A GET-ben a HTTP-kérelem kiadásához szükséges Poster vagy azzal egyenértékű eszközre van szükség. További információ: gyors útmutató [: az Azure Cognitive Search REST API közzététele a Poster használatával](search-get-started-postman.md).

### <a name="set-the-request-header"></a>A kérelem fejlécének beállítása

1. A kérelem fejlécében állítsa be a **Content-Type értéket a következőre** : `application/json` .

2. Adjon hozzá egy **API-kulcsot**, és állítsa be a következő sztringre: `252044BE3886FE4A8E3BAA4F595114BB` . Ez egy lekérdezési kulcs a NYC-feladatok indexét futtató sandbox Search szolgáltatáshoz.

A kérelem fejlécének megadását követően újra felhasználhatja azt a jelen cikk összes lekérdezéséhez, csak a **Search =** sztringet felcserélve. 

  ![Poster-kérelem fejlécének paramétereinek beállítása](media/search-query-lucene-examples/postman-header.png)

### <a name="set-the-request-url"></a>A kérelem URL-címének beállítása

A kérelem egy GET parancs, amely az Azure Cognitive Search végpontot és a keresési karakterláncot tartalmazó URL-címmel párosítva van.

  ![Poster-kérelem fejlécének beolvasása](media/search-query-lucene-examples/postman-basic-url-request-elements.png)

Az URL-összeállítás a következő elemekből áll:

+ **`https://azs-playground.search.windows.net/`** Az Azure Cognitive Search fejlesztői csapata által karbantartott sandbox keresési szolgáltatás. 
+ **`indexes/nycjobs/`** a New York Jobs index a szolgáltatás indexek gyűjteményében. A kéréshez a szolgáltatás nevét és indexét is meg kell adni.
+ **`docs`** az összes kereshető tartalmat tartalmazó dokumentumok gyűjteménye. A kérelem fejlécében megadott lekérdezési API-kulcs csak olyan olvasási műveleteken működik, amelyek a dokumentumok gyűjteményét célozzák meg.
+ **`api-version=2020-06-30`** Beállítja az API-verziót, amely minden kérelem esetében kötelező paraméter.
+ **`search=*`** a lekérdezési karakterlánc, amely a kezdeti lekérdezésben null értékű, és az első 50 eredményt adja vissza (alapértelmezés szerint).

## <a name="send-your-first-query"></a>Az első lekérdezés elküldése

Ellenőrzési lépésként illessze be a következő kérelmet a GET mezőbe, és kattintson a **Küldés**gombra. Az eredményeket a rendszer részletes JSON-dokumentumként adja vissza. A rendszer a teljes dokumentumot adja vissza, ami lehetővé teszi az összes mező és az összes érték megtekintését.

Illessze be ezt az URL-címet egy REST-ügyfélbe érvényesítési lépésként, és tekintse meg a dokumentum szerkezetét.

  ```http
  https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2020-06-30&$count=true&search=*
  ```

A lekérdezési karakterlánc ( **`search=*`** ) nem meghatározott keresés, amely egyenértékű null vagy üres kereséssel. Ez nem különösen hasznos, de ez a legegyszerűbb keresési lehetőség.

Opcionálisan hozzáadhatja **`$count=true`** az URL-címet a keresési feltételeknek megfelelő dokumentumok számának visszaadásához. Üres keresési sztring esetén ez az indexben található összes dokumentum (körülbelül 2800 a NYC-feladatok esetében).

## <a name="how-to-invoke-simple-query-parsing"></a>Egyszerű lekérdezések elemzésének meghívása

Az interaktív lekérdezésekhez nem kell semmit megadnia: az egyszerű érték az alapértelmezett. Ha a Code (kód) beállításnál korábban a **queryType = Full** értéket választotta a teljes lekérdezési szintaxishoz, alaphelyzetbe állíthatja az alapértelmezettet a **queryType = Simple**értékkel.

## <a name="example-1-field-scoped-query"></a>1. példa: mező hatókörű lekérdezés

Ez az első példa nem elemző-specifikus, de az első alapvető lekérdezési koncepció bevezetéséhez vezetünk: a tárolás. Ez a példa hatókörök lekérdezési végrehajtását és a válaszát csak néhány konkrét mezőre szűkíti. Az olvasható JSON-válaszok szerkezetének ismerete fontos, ha az eszköz Poster vagy Search Explorer. 

A rövidség kedvéért a lekérdezés csak a *business_title* mezőt célozza meg, és csak az üzleti címeket adja vissza. A szintaxis **searchFields** a lekérdezés végrehajtásának korlátozása csak a business_title mezőre, és **válassza ki** , hogy mely mezők szerepeljenek a válaszban.

### <a name="partial-query-string"></a>Részleges lekérdezési karakterlánc

```http
searchFields=business_title&$select=business_title&search=*
```

Itt ugyanaz a lekérdezés, amelyben több mező található a vesszővel tagolt listában.

```http
search=*&searchFields=business_title, posting_type&$select=business_title, posting_type
```

### <a name="full-url"></a>Teljes URL-cím

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2020-06-30&$count=true&searchFields=business_title&$select=business_title&search=*
```

A lekérdezésre adott válasznak az alábbi képernyőképhez hasonlóan kell kinéznie.

  ![Poster-minta válasz](media/search-query-lucene-examples/postman-sample-results.png)

Lehetséges, hogy észrevette a keresési pontszámot a válaszban. 1 egységes pontszám akkor fordul elő, ha nincs rangsor, vagy mert a keresés nem teljes szöveges keresés, vagy nem lett alkalmazva. A feltétel nélküli null kereséshez a sorok tetszőleges sorrendben jönnek vissza. A tényleges feltételek belefoglalásakor a keresési pontszámok jelentős értékekre lesznek kialakítva.

## <a name="example-2-look-up-by-id"></a>2. példa: Keresés azonosító alapján

Ez a példa egy kicsit atipikus, de a keresési viselkedés kiértékelése során érdemes megvizsgálni egy adott dokumentum teljes tartalmát, hogy megtudja, miért került bele vagy kizárt az eredményekből. Egyetlen dokumentum teljes egészében történő visszaadásához használjon [keresési műveletet](/rest/api/searchservice/lookup-document) a dokumentum azonosítójának továbbításához.

Minden dokumentum egyedi azonosítóval rendelkezik. Ha egy keresési lekérdezés szintaxisát szeretné kipróbálni, először a dokumentumok azonosítóinak listáját kell visszaadnia, hogy megtalálja az egyiket a használathoz. A NYC-feladatok esetében az azonosítók a mezőben vannak tárolva `id` .

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2020-06-30&$count=true&searchFields=id&$select=id&search=*
```

A következő példa egy olyan keresési lekérdezés `id` , amely az előző válaszban elsőként megjelenő "9E1E3AF9-0660-4E00-AF51-9B654925A2D5" alapján adott dokumentumot ad vissza. A következő lekérdezés a teljes dokumentumot adja vissza, nem csak a kijelölt mezőket. 

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs/9E1E3AF9-0660-4E00-AF51-9B654925A2D5?api-version=2020-06-30&$count=true&search=*
```

## <a name="example-3-filter-queries"></a>3. példa: lekérdezések szűrése

A [szűrési szintaxis](./search-query-odata-filter.md) egy OData kifejezés, amelyet a **kereséssel** vagy önmagában is használhat. A keresési paraméter nélküli önálló szűrő akkor hasznos, ha a szűrő kifejezés képes teljes mértékben minősíteni a dokumentumokat. A lekérdezési karakterláncok nélkül nincs lexikális vagy nyelvi elemzés, nincs pontozás (az összes pontszám 1), és nincs rangsor. Figyelje meg, hogy a keresési karakterlánc üres.

```http
POST /indexes/nycjobs/docs/search?api-version=2020-06-30
    {
      "search": "",
      "filter": "salary_frequency eq 'Annual' and salary_range_from gt 90000",
      "select": "job_id, business_title, agency, salary_range_from",
      "count": "true"
    }
```

Együtt használva a szűrő először a teljes indexre lesz alkalmazva, majd a keresés a szűrő eredményein történik. A szűrők éppen ezért hasznosak a lekérdezés teljesítményének javítására, mivel általuk lecsökkenthető a keresési lekérdezés által feldolgozandó dokumentumok köre.

  ![Lekérdezési válasz szűrése](media/search-query-simple-examples/filtered-query.png)

Ha szeretné kipróbálni a Poster használatával a GET paranccsal, illessze be a következő karakterláncot:

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2020-06-30&$count=true&$select=job_id,business_title,agency,salary_range_from&search=&$filter=salary_frequency eq 'Annual' and salary_range_from gt 90000
```

Egy másik hatékony módszer a szűrő és a keresés összekapcsolására **`search.ismatch*()`** egy szűrési kifejezésben, ahol keresési lekérdezést használhat a szűrőn belül. Ez a szűrési kifejezés egy helyettesítő karaktert használ a *tervben* a business_title kiválasztásához, beleértve a tervet, a Plannert, a tervezést és így tovább.

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2020-06-30&$count=true&$select=job_id,business_title,agency&search=&$filter=search.ismatch('plan*', 'business_title', 'full', 'any')
```

A függvénnyel kapcsolatos további információkért tekintse [meg a Search. ismatch "példák szűrése"](./search-query-odata-full-text-search-functions.md#examples)című témakört.

## <a name="example-4-range-filters"></a>4. példa: tartomány szűrőinek

A tartomány szűrése **`$filter`** bármely adattípushoz kifejezéssel támogatott. A következő példák a numerikus és a sztring mezőkre mutatnak keresést. 

Az adattípusok fontosak a tartományhoz tartozó szűrőkben, és akkor működnek a legjobban, ha numerikus mezőkben numerikus adat szerepel, és a sztring mezőkben sztring szerepel. A karakterlánc mezőiben szereplő numerikus adat nem alkalmas tartományokhoz, mert a numerikus karakterláncok nem összehasonlíthatóak az Azure Cognitive Searchban. 

Az alábbi példák formátuma az olvashatóság (numerikus tartomány, majd a szöveges tartomány):

```http
POST /indexes/nycjobs/docs/search?api-version=2020-06-30
    {
      "search": "",
      "filter": "num_of_positions ge 5 and num_of_positions lt 10",
      "select": "job_id, business_title, num_of_positions, agency",
      "orderby": "agency",
      "count": "true"
    }
```
  ![Numerikus tartományokhoz tartozó tartomány-szűrő](media/search-query-simple-examples/rangefilternumeric.png)


```http
POST /indexes/nycjobs/docs/search?api-version=2020-06-30
    {
      "search": "",
      "filter": "business_title ge 'A*' and business_title lt 'C*'",
      "select": "job_id, business_title, agency",
      "orderby": "business_title",
      "count": "true"
    }
```

  ![Tartomány-szűrő a szöveges tartományokhoz](media/search-query-simple-examples/rangefiltertext.png)

A GET paranccsal is kipróbálhatja ezeket a Poster használatával:

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2020-06-30&search=&$filter=num_of_positions ge 5 and num_of_positions lt 10&$select=job_id, business_title, num_of_positions, agency&$orderby=agency&$count=true
```

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2020-06-30&search=&$filter=business_title ge 'A*' and business_title lt 'C*'&$select=job_id, business_title, agency&$orderby=business_title&$count=true
```

> [!NOTE]
> Az értékek tartományán alapuló aspektus egy gyakori keresési alkalmazásra vonatkozó követelmény. További információt és példákat a szűrők kiépítéséhez a dimenziós navigációs struktúrákhoz a részletes [ *navigáció megvalósítása*című témakör "szűrés tartomány alapján"](search-faceted-navigation.md#filter-based-on-a-range)című szakaszában talál.

## <a name="example-5-geo-search"></a>5. példa: Geo-keresés

A minta index egy geo_location mezőt tartalmaz szélességi és hosszúsági koordinátákkal. Ez a példa a [Geo. Distance függvényt](./search-query-odata-geo-spatial-functions.md#examples) használja, amely egy kiindulási pont kerületén belüli dokumentumok szűrésére szolgál, az Ön által megadott távolságra (kilométerben). A lekérdezésben szereplő utolsó értéket (4) módosíthatja a lekérdezés felületi területének csökkentése vagy nagyítása érdekében.

A következő példa formátuma az olvashatóság érdekében:

```http
POST /indexes/nycjobs/docs/search?api-version=2020-06-30
    {
      "search": "",
      "filter": "geo.distance(geo_location, geography'POINT(-74.11734 40.634384)') le 4",
      "select": "job_id, business_title, work_location",
      "count": "true"
    }
```
További olvasható találatok esetén a keresési eredmények a feladatok AZONOSÍTÓjának, a beosztás és a munkahelyi hely belefoglalására vannak kimetszve. A kezdeti koordinátákat a rendszer egy véletlenszerű dokumentumból szerezte be az indexben (ebben az esetben a Staten Island-beli munkahelyhez.

Ezt a GET paranccsal is kipróbálhatja a Poster használatával:

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2020-06-30&$count=true&search=&$select=job_id, business_title, work_location&$filter=geo.distance(geo_location, geography'POINT(-74.11734 40.634384)') le 4
```

## <a name="example-6-search-precision"></a>6. példa: a keresés pontossága

A kifejezéses lekérdezések egyetlen kifejezésből állnak, amelyek közül sok közülük egymástól függetlenül kerül kiértékelésre. A kifejezéses lekérdezések idézőjelek közé vannak lefoglalva, és egy Verbatim sztringként vannak kiértékelve. A egyezés pontosságát a kezelők és a searchMode vezérlik.

1. példa: **`&search=fire`**  a 150 eredményt adja vissza, ahol az összes egyezés tartalmazza a Word tüzet a dokumentumban.

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2020-06-30&$count=true&search=fire
```

2. példa: a **`&search=fire department`** 2002 eredményt adja vissza. A rendszer a tüzet vagy a részleget tartalmazó dokumentumok egyezéseit adja vissza.

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2020-06-30&$count=true&search=fire department
```

3. példa: a **`&search="fire department"`** 82 eredményt adja vissza. A karakterlánc idézőjelek közé történő befoglalása mindkét kifejezésben egy Verbatim-keresés, a egyezések pedig a kombinált kifejezésekből álló indexben található, jogkivonatokban lévő kifejezésekben találhatók. Ez a cikk azt ismerteti, hogy a hasonló keresés miért **`search=+fire +department`** nem egyenértékű. Mindkét feltételt kötelező megadni, de egymástól függetlenül vizsgáljuk. 

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2020-06-30&$count=true&search="fire department"
```

## <a name="example-7-booleans-with-searchmode"></a>7. példa: logikai értékek searchMode

Az egyszerű szintaxis a logikai operátorokat karakter () formában támogatja `+, -, |` . A searchMode paraméter a pontosság és a visszahívás közötti kompromisszumokat adja meg, és a `searchMode=any` szívességet idézi elő (a feltételek egyeztetése az eredményhalmaz dokumentumára van beállítva), és a `searchMode=all` pontosságot (az összes feltételt egyeztetni kell). Az alapértelmezett érték az `searchMode=any` , ami zavaró lehet, ha több operátort tartalmazó lekérdezést halmoz fel, és a szűkebb eredmények helyett szélesebb körűen fog megjelenni. Ez különösen igaz, és nem, ahol az eredmények tartalmazzák az összes olyan dokumentumot, amely "nem tartalmaz" egy adott kifejezést.

Az alapértelmezett searchMode (any), a 2800-es dokumentumok visszaadása: a több részből álló "tűzoltó részleg" kifejezést tartalmazó, valamint az "Metrotech Center" kifejezéssel nem rendelkező dokumentumok.

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2020-06-30&$count=true&searchMode=any&search="fire department"  -"Metrotech Center"
```

  ![tetszőleges keresési mód](media/search-query-simple-examples/searchmodeany.png)

A searchMode módosítása a `all` feltételek kumulatív hatásának érvényesítésére és egy kisebb eredményhalmaz-21 dokumentum visszaadására, amely a teljes "tűzoltó részleg" kifejezést tartalmazza, és levonva a feladatokat a Metrotech Center-címen.

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2020-06-30&$count=true&searchMode=all&search="fire department"  -"Metrotech Center"
```
  ![összes keresési mód](media/search-query-simple-examples/searchmodeall.png)

## <a name="example-8-structuring-results"></a>8. példa: az eredmények strukturálása

Számos paraméter határozza meg, hogy mely mezők szerepelnek a keresési eredmények között, az egyes kötegekben visszaadott dokumentumok száma és a rendezési sorrend. Ebben a példában az előző példák közül néhányat felhasználunk, a **$Select** utasítás és a Verbatim keresési feltételek alapján adott mezőkre korlátozza az eredményeket, és visszaadja a 82 egyezéseket 

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2020-06-30&$count=true&$select=job_id,agency,business_title,civil_service_title,work_location,job_description&search="fire department"
```
Az előző példához hozzáfűzve megadhatja a title (cím) sorrendet. Ez a rendezés azért működik, mert a civil_service_title az indexben *rendezhető* .

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2020-06-30&$count=true&$select=job_id,agency,business_title,civil_service_title,work_location,job_description&search="fire department"&$orderby=civil_service_title
```

A lapozási eredmények a **$Top** paraméter használatával valósulnak meg, ebben az esetben az első 5 dokumentumot adja vissza:

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2020-06-30&$count=true&$select=job_id,agency,business_title,civil_service_title,work_location,job_description&search="fire department"&$orderby=civil_service_title&$top=5&$skip=0
```

A következő 5 beszerzéséhez hagyja ki az első köteget:

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2020-06-30&$count=true&$select=job_id,agency,business_title,civil_service_title,work_location,job_description&search="fire department"&$orderby=civil_service_title&$top=5&$skip=5
```

## <a name="next-steps"></a>Következő lépések
Próbálkozzon a kódban szereplő lekérdezések megadásával. Az alábbi hivatkozások azt ismertetik, hogyan állíthat be keresési lekérdezéseket a .NET-hez és a REST APIhoz az alapértelmezett egyszerű szintaxis használatával.

* [Az index lekérdezése a .NET SDK használatával](./search-get-started-dotnet.md)
* [Az index lekérdezése a REST API használatával](./search-get-started-powershell.md)

A szintaxissal, a lekérdezési architektúrával és a példákkal kapcsolatban a következő hivatkozásokban találhat további tudnivalókat:

+ [Példák a speciális lekérdezések kiépítési Lucene](search-query-lucene-examples.md)
+ [A teljes szöveges keresés működése az Azure Cognitive Searchben](search-lucene-query-architecture.md)
+ [Egyszerű lekérdezési szintaxis](/rest/api/searchservice/simple-query-syntax-in-azure-search)
+ [Teljes Lucene-lekérdezés](/rest/api/searchservice/lucene-query-syntax-in-azure-search)
+ [Szűrő-és OrderBy szintaxis](/rest/api/searchservice/odata-expression-syntax-for-azure-search)