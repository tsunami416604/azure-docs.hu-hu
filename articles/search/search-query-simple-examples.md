---
title: Az Azure Search egyszerű lekérdezés példák |} A Microsoft Docs
description: A teljes szöveges keresés, filter, search, földrajzi keresés, többszempontú keresés és más lekérdezési karakterláncok az Azure Search-index lekérdezése használt egyszerű lekérdezés példákat.
author: HeidiSteen
manager: cgronlun
tags: Simple query analyzer syntax
services: search
ms.service: search
ms.topic: conceptual
ms.date: 07/16/2018
ms.author: heidist
ms.openlocfilehash: e4bb72eb8ad6f15b0e5bc14e0e07556e76d0477b
ms.sourcegitcommit: f86e5d5b6cb5157f7bde6f4308a332bfff73ca0f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/31/2018
ms.locfileid: "39369120"
---
# <a name="simple-syntax-query-examples-for-building-queries-in-azure-search"></a>Az Azure Searchben a lekérdezések létrehozásához Példák egyszerű szintaxisú lekérdezésekre

[Egyszerű lekérdezési szintaxis](https://docs.microsoft.com/rest/api/searchservice/simple-query-syntax-in-azure-search) hívja meg az alapértelmezett lekérdezéselemzőt végre teljes szöveges keresési lekérdezéseket az Azure Search-index. Az egyszerű lekérdezéselemző gyors, és kezeli az Azure Search, beleértve a teljes szöveges keresés, szűrt, és a jellemzőalapú keresés és földrajzi kereséssel gyakori forgatókönyvek. Ez a cikk lépésben elérhető lekérdezési műveletek bemutatásához, az egyszerű szintaxis használatakor példán keresztül.

Az alternatív lekérdezés szintaxisa [teljes Lucene](https://docs.microsoft.com/rest/api/searchservice/lucene-query-syntax-in-azure-search), összetettebb lekérdezés struktúrák, mint például támogató intelligens és helyettesítő karakteres keresés, amely feldolgozni további időt vehet igénybe. További információk és példák teljes szintaxis bemutatására: [példák Lucene szintaxisú lekérdezésekre](search-query-lucene-examples.md).

## <a name="formulate-requests-in-postman"></a>Állítson össze a Postmanben kérelmek

Az alábbi példák kihasználhatja az elérhető feladatok által biztosított adatkészlet alapján álló i állások keresési indexet a [város, New York OpenData](https://nycopendata.socrata.com/) kezdeményezésére. Ezek az adatok nem az aktuális vagy a teljes kell tekinteni. Az index van a Microsoft, ami azt jelenti, hogy nem kell egy Azure-előfizetést, vagy próbálja ki ezeket a lekérdezéseket az Azure Search által nyújtott védőfal szolgáltatásokat.

Amire szüksége a Postman vagy egy egyenértékű eszközt tud kiállítani a HTTP-kérelem a GET. További információkért lásd: [tesztelés REST-ügyfelekkel](search-fiddler.md).

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
  https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2017-11-11&search=*
  ```

A lekérdezési karakterlánc **`search=*`**, van egy nem meghatározott keresés egyenértékű, NULL értékű vagy üres keresés. Nem különösen akkor hasznos, de a legegyszerűbb keresést végezhet.

Ha szükséges, hozzáadhat **`$count=true`** az URL-címet a keresési feltételeknek megfelelő a dokumentumok számát adja vissza. Egy üres Keresés karakterlánc ez az index (2802 állások esetén) szereplő összes dokumentumot.

## <a name="how-to-invoke-simple-query-parsing"></a>Való meghívásának egyszerű lekérdezés elemzése

Interaktív lekérdezések esetén nem kell megadnia semmit: egyszerű az alapértelmezett érték. A kódban, ha korábban elindított **queryType = full** teljes lekérdezési szintaxis, sikerült alaphelyzetbe állítani a vissza az alapértelmezett **queryType = egyszerű**.

## <a name="example-1-field-scoped-query"></a>1. példa: A mező-hatáskörű lekérdezések

Az első lekérdezés nincs szintaxis-specifikus (a lekérdezés egyszerű és a teljes szintaxis működik), de hogy vezethet ebben a példában a vezessen be egy alapkonfiguráció lekérdezés fogalom, amely egy ésszerű olvasható JSON-választ. Kihagytuk, a lekérdezés célozza meg, csak a *business_title* mezőben, majd adja meg a csak üzleti címe adja vissza. 

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2017-11-11&$count=true&searchFields=business_title&$select=business_title&search=*
```

A **searchFields** paraméter csak az üzleti cím mező korlátozza a keresést. A **kiválasztása** paraméter meghatározza, hogy melyik mezőket tartalmazza az eredményhalmaz.

Ez a lekérdezés válasza az alábbi képernyőfelvételhez hasonlóan kell kinéznie.

  ![Postman mintaválasz](media/search-query-lucene-examples/postman-sample-results.png)

Akkor talán észrevette, hogy minden dokumentum is visszaadott a keresési pontszámtól annak ellenére, hogy a keresési pontszámtól nincs megadva. Ez azért, mert keresési pontszámtól metaadatait, az eredmények sorrend jelző érték beolvasása. Egységes pontszámok 1 esetén nincs rank, vagy mert a keresés nem volt a teljes szöveges keresés, vagy mert alkalmazására a feltétel nem fordulhat elő. NULL értékű keresési feltételt nem van, és a sorok visszaérkező tetszőleges sorrendben vannak. A keresési feltételeknek-definícióban több időt vesz igénybe, mint látni fogja a keresési pontszámok jelentéssel bíró értékekké fejlesztheti tovább.

## <a name="example-2-look-up-by-id"></a>2. példa: Keresési azonosító alapján

Ebben a példában egy kicsit szokatlan, de keresési viselkedések kiértékelésekor érdemes tudni, miért lett foglalt vagy zárva az eredményeket a dokumentum teljes tartalmának vizsgálata. A teljes dokumentum használja egy [Fiókkeresési műveletben](https://docs.microsoft.com/rest/api/searchservice/lookup-document) megadni a dokumentum azonosítója.

Minden dokumentum rendelkezik egy egyedi azonosítója. Próbálja ki a keresési lekérdezés szintaxisát, először adja vissza a dokumentum azonosítók listáját, hogy talál egy használni. I állások, azonosítók vannak tárolva a `id` mező.

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2017-11-11&$count=true&searchFields=id&$select=id&search=*
 ```

A következő példában egy keresési lekérdezést egy adott dokumentum alapján visszaadó `id` "9E1E3AF9-0660-4E00-AF51-9B654925A2D5", amely jelent meg először az előző válaszban. A következő lekérdezés visszaadja a teljes dokumentumot, nem csak a kijelölt mezők. 

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs/9E1E3AF9-0660-4E00-AF51-9B654925A2D5?api-version=2017-11-11&$count=true&search=*
 ```

## <a name="example-3-search-precision"></a>3. példa: Keresési pontosság

Kifejezés lekérdezések ugyanazon használati feltételek, például számos őket, egymástól függetlenül kiértékelt. Lekérdezések kifejezést idézőjelek és tömbként lesz szó karakterlánc. Egyezés pontossága operátorok és searchMode vezérlik majd.

1. példa: **`&search=fire`** Ha minden megfelelő elemet a word fire valahol a dokumentum tartalmazza: 150 eredményeket adja vissza.

```
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2017-11-11&$count=true&search=fire
```

2. példa: **`&search=fire department`** 2002 eredményeket ad vissza. Egyezések fire vagy a részleg tartalmazó dokumentumok adja vissza.

```
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2017-11-11&$count=true&search=fire department
```

3. példa: **`&search="fire department"`** 82 eredményeket ad vissza. Mellékelve a karakterláncot az idézőjelek szó keresést a nagy valószínűséggel mindkét kifejezéssel, és egyezés található az indexben, az összesített használati álló tokenekre igényei szerint. Ez megmagyarázza, miért például keresés **`search=+fire +department`** nem megfelelő. Nagy valószínűséggel mindkét kifejezéssel szükség, de tartalomvizsgálatnak egymástól függetlenül. 

```
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2017-11-11&$count=true&search="fire department"
```

## <a name="example-4-booleans-with-searchmode"></a>4. példa: Logikai értékek a searchMode

Egyszerű szintaxis karakterek formájában támogatja a logikai operátorokkal (`+, -, |`). A searchMode paraméter tájékoztatja között pontosság és a már ismert, és kompromisszumot kínál a `searchMode=any` beállítva visszahívási (a dokumentum egy az eredményhalmaz semmilyen feltételt az egyező címtársémának megfelelően), és `searchMode=all` (az összes feltétel egyeztetni) pontosság beállítva. Az alapértelmezett érték `searchMode=any`, amely zavaró, ha meg vannak rétegezést lekérdezést több operátorokkal és szélesebb körű helyett szűkebb eredmények beolvasása. Ez különösen igaz a NOT, ahol eredmények tartalmazzák a "nem tartalmazó" dokumentumokat egy keresett kifejezést.

Az alapértelmezett searchMode (tetszőleges) használ, visszaadott 2800 dokumentumok: a több rész tartalmazó távú "fire részleg", valamint az összes dokumentumot, amelyek nem rendelkeznek az előfizetési időszak "Metrotech Center".

```
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2017-11-11&$count=true&searchMode=any&search="fire department"  -"Metrotech Center"
```
A searchMode módosítása `all` feltételek halmozott hatása kikényszeríti, és a egy kisebb eredményhalmaz – 21 dokumentumok – "fire részleg", csökkentve a Metrotech Center címen feladatok teljes kifejezést tartalmazó dokumentumok álló adja vissza.

```
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2017-11-11&$count=true&searchMode=all&search="fire department"  -"Metrotech Center"
```


## <a name="example-5-structuring-results"></a>5. példa: Strukturálja eredmények

Számos olyan paramétereket szabályozhatja, hogy mely mezők szerepelnek a keresési eredmények, az egyes batch és a rendezési sorrend visszaadott dokumentumok számát. Ebben a példában az eredmények használatával meghatározott mezőkre korlátozza az előző példák néhány resurfaces a **$select** utasítás és szó keresési feltételeknek, 82 egyezések visszaadása 

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2017-11-11&$count=true&$select=job_id,agency,business_title,civil_service_title,work_location,job_description&search="fire department"
```
Hozzáfűzi az előző példában az alakzatot, rendezheti címe. Ez a rendezés működik, mert civil_service_title *rendezhető* az indexben.

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2017-11-11&$count=true&$select=job_id,agency,business_title,civil_service_title,work_location,job_description&search="fire department"&$orderby=civil_service_title
```

Lapozás eredményei használatával lett megvalósítva a **$top** paramétert, ebben az esetben vissza a 5 leggyakoribb dokumentumokat:

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2017-11-11&$count=true&$select=job_id,agency,business_title,civil_service_title,work_location,job_description&search="fire department"&$orderby=civil_service_title&$top=5&$skip=0
```

A következő 5 kapni, hagyja ki az első köteg:

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2017-11-11&$count=true&$select=job_id,agency,business_title,civil_service_title,work_location,job_description&search="fire department"&$orderby=civil_service_title&$top=5&$skip=5
```

## <a name="next-steps"></a>További lépések
Próbálja meg a lekérdezések a kódot. Az alábbi hivatkozások azt ismertetik, hogyan állítható be a keresési lekérdezések a .NET-hez és a REST API az alapértelmezett egyszerű szintaxis használatával.

* [A .NET SDK használatával az Azure Search-Index lekérdezése](search-query-dotnet.md)
* [A REST API használatával az Azure Search-Index lekérdezése](search-query-rest-api.md)

Az alábbi hivatkozásokra kattintva további keresésiszintaxis-referencia, lekérdezés-architektúra és példák találhatók:

+ [Példák Lucene szintaxisú lekérdezésekre létrehozásához speciális lekérdezések](search-query-lucene-examples.md)
+ [Teljes szöveges keresés működése az Azure Search szolgáltatásban](search-lucene-query-architecture.md)
+ [Egyszerű lekérdezési szintaxis](https://docs.microsoft.com/rest/api/searchservice/simple-query-syntax-in-azure-search)
+ [Teljes Lucene lekérdezési](https://docs.microsoft.com/rest/api/searchservice/lucene-query-syntax-in-azure-search)
