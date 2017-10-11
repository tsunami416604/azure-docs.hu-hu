---
title: "Az Azure Search szolgáltatás REST API-verzió 2015-02-28 – előzetes verzió |} Microsoft Docs"
description: "Az Azure Search szolgáltatás REST API-verzió 2015-02-28-előzetes verzió például a természetes nyelvi elemzőkkel és moreLikeThis keresések kísérleti funkciót tartalmaz."
services: search
documentationcenter: na
author: brjohnstmsft
manager: pablocas
editor: 
ms.assetid: 3dba3bf8-9c83-42f6-82bc-04727bd11037
ms.service: search
ms.devlang: rest-api
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: search
ms.date: 05/01/2017
ms.author: brjohnst
ms.openlocfilehash: e6ad5c964bfa8421be2706cb4015980e01a271b7
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2017
---
# <a name="azure-search-service-rest-api-version-2015-02-28-preview"></a>Az Azure Search szolgáltatás REST API-ja: Verzió 2015-02-28 – előzetes
Ez a cikk a referenciadokumentációt tartalmaz `api-version=2015-02-28-Preview`. Ez az előnézet kibővíti az aktuális általánosan elérhető verzió [api-version = 2015-02-28](https://msdn.microsoft.com/library/dn798935.aspx), a következő kísérleti funkciók megadásával:

* `moreLikeThis`a lekérdezésparaméter a [dokumentumok keresése](#SearchDocs) API. Más-más adott dokumentumra vonatkozó dokumentumok találja meg.

Néhány további részei a `2015-02-28-Preview` REST API-t a külön-külön szerepelnek. Ezek a következők:

* [A pontozási profil](search-api-scoring-profiles-2015-02-28-preview.md)
* [Indexelők](search-api-indexers-2015-02-28-preview.md)

Az Azure Search szolgáltatás több verzióban érhető el. Tekintse meg [keresési Service Versioning](http://msdn.microsoft.com/library/azure/dn864560.aspx) részleteiről.

## <a name="apis-in-this-document"></a>Ez a dokumentum API-k
Az Azure Search szolgáltatás API API-műveleteket támogatja a két URL-cím szintaxissal: egyszerű és OData (lásd: [OData (Azure Search API) támogatása](http://msdn.microsoft.com/library/azure/dn798932.aspx) részletekért). Az alábbi lista egyszerű szintaxisát jeleníti meg.

[Index létrehozása](#CreateIndex)

    POST /indexes?api-version=2015-02-28-Preview

[Index frissítése](#UpdateIndex)

    PUT /indexes/[index name]?api-version=2015-02-28-Preview

[Index beolvasása](#GetIndex)

    GET /indexes/[index name]?api-version=2015-02-28-Preview

[Indexek listázása](#ListIndexes)

    GET /indexes?api-version=2015-02-28-Preview

[Megtekintheti a statisztikákat Index](#GetIndexStats)

    GET /indexes/[index name]/stats?api-version=2015-02-28-Preview

[Teszt elemző eszköz](#TestAnalyzer)

    POST /indexes/[index name]/analyze?api-version=2015-02-28-Preview

[Index törlése](#DeleteIndex)

    DELETE /indexes/[index name]?api-version=2015-02-28-Preview

[Hozzáadása, törlése, és az Index adatainak frissítése](#AddOrUpdateDocuments)

    POST /indexes/[index name]/docs/index?api-version=2015-02-28-Preview

[Dokumentumok keresése](#SearchDocs)

    GET /indexes/[index name]/docs?[query parameters]
    POST /indexes/[index name]/docs/search?api-version=2015-02-28-Preview

[Keresés a dokumentum](#LookupAPI)

     GET /indexes/[index name]/docs/[key]?[query parameters]

[A dokumentumok száma](#CountDocs)

    GET /indexes/[index name]/docs/$count?api-version=2015-02-28-Preview

[Javaslatok](#Suggestions)

    GET /indexes/[index name]/docs/suggest?[query parameters]
    POST /indexes/[index name]/docs/suggest?api-version=2015-02-28-Preview

- - -
<a name="IndexOps"></a>

## <a name="index-operations"></a>Indexművelet
Hozzon létre, és az Azure Search szolgáltatás egyszerű HTTP-kérelmek (POST, GET, PUT, DELETE) egy adott indexű erőforrás elleni keresztül Indexek kezelése. Index létrehozása, akkor először ÍRJON egy JSON-dokumentumában, amely leírja a sémát indexeli. A séma definiálja az index, az adattípusokat és hogyan is használhatók (például a teljes szöveges keresést, szűrők, rendezés vagy értékkorlátozás). A pontozási profil, javaslattevők és egyéb attribútumai konfigurálhatja a viselkedését az index is meghatároz.

A következő példa illusztrálja a Leírás mezőben definiált két nyelvű Szálloda adatai a keresést használt séma. Figyelje meg, hogyan attribútumok szabályozza, hogyan használja a mezőt. Például a `hotelId` a dokumentum kulcsként (`"key": true`) és a teljes szöveges keresés nem tartozik (`"searchable": false`).

    {
    "name": "hotels",  
    "fields": [
      {"name": "hotelId", "type": "Edm.String", "key": true, "searchable": false},
      {"name": "baseRate", "type": "Edm.Double"},
      {"name": "description", "type": "Edm.String", "filterable": false, "sortable": false, "facetable": false},
      {"name": "description_fr", "type": "Edm.String", "filterable": false, "sortable": false, "facetable": false, "analyzer": "fr.lucene"},
      {"name": "hotelName", "type": "Edm.String"},
      {"name": "category", "type": "Edm.String"},
      {"name": "tags", "type": "Collection(Edm.String)"},
      {"name": "parkingIncluded", "type": "Edm.Boolean"},
      {"name": "smokingAllowed", "type": "Edm.Boolean"},
      {"name": "lastRenovationDate", "type": "Edm.DateTimeOffset"},
      {"name": "rating", "type": "Edm.Int32"},
      {"name": "location", "type": "Edm.GeographyPoint"}
     ],
     "suggesters": [
      {
       "name": "sg",
       "searchMode": "analyzingInfixMatching",
       "sourceFields": ["hotelName"]
      }
     ]
    }

Az index létrehozása után lesz feltöltött dokumentumok, amely feltölti az indexet. Lásd: [hozzáadása vagy frissítés dokumentumok](#AddOrUpdateDocuments) a következő lépéshez.

Videó az Azure Search az indexelő, tekintse meg a [epizód Channel 9 felhő fedik le az Azure Search](http://go.microsoft.com/fwlink/p/?LinkId=511509).

<a name="CreateIndex"></a>

## <a name="create-index"></a>Index létrehozása
Az index az rendszerezése és dokumentumok keresése az Azure Search hasonló miként tábla rendezi egy adatbázis elsődleges eszköze. Minden egyes index összes felelnek meg a sémát indexeli (mezőnevek, az adattípusokat és tulajdonságait), de indexek is adja meg a további szerkezetek (javaslattevők, pontozási profilokat és a CORS-beállítások), amely más keresési tulajdonságainak megadásához dokumentumok gyűjteményével rendelkezik.

Létrehozhat egy új index használ egy HTTP POST vagy a PUT kérelmek Azure Search szolgáltatás belül. A kérelem törzse egy JSON-séma, amely az index és konfigurációs információt.

    POST https://[service name].search.windows.net/indexes?api-version=[api-version]
    Content-Type: application/json
    api-key: [admin key]

Azt is megteheti PUT használja, és adja meg az URI az index nevét. Ha az index nem létezik, a rendszer létrehozza.

    PUT https://[search service url]/indexes/[index name]?api-version=[api-version]

Az index létrehozása meghatározza, hogy a struktúra a dokumentumok és használja a keresési műveleteket. Az index feltöltése egy olyan különálló művelet. Az ebben a lépésben használhatja egy [indexelő](https://msdn.microsoft.com/library/azure/mt183328.aspx) (támogatott adatforrások érhető el) vagy egy [hozzáadása, frissítése vagy törlése dokumentumok](https://msdn.microsoft.com/library/azure/dn798930.aspx) műveletet. A fordított index jön létre, amikor a dokumentumok van közzétéve.

**Megjegyzés:**: tarifacsomag függ a indexek engedélyezett maximális száma. Az ingyenes szolgáltatás lehetővé teszi, hogy a 3 indexeket. Standard szolgáltatás lehetővé teszi, hogy 50 indexek érhető el keresési szolgáltatásonként. Lásd: [korlátozások és megkötések](http://msdn.microsoft.com/library/azure/dn798934.aspx) részleteiről.

**Kérés**

HTTPS-kapcsolat szükséges az összes szolgáltatási kérelmek. A **a Create Index** kérelem egy POST vagy a PUT metódust használó lehet létrehozni. POST használatakor meg kell adnia egy index neve a kérés törzsében séma Indexdefiníció együtt. A PUT érvénytelen az URL-cím. Ha az index nem létezik, akkor jön létre. Ha már létezik, az új definition frissül.

Az index neve kell kell kisbetű, betűvel vagy számmal kezdődhet, nincs perjeleket vagy pontokból és legfeljebb 128 karakter lehet. Után az index nevének betűvel vagy számmal verziótól kezdődően a nevét a többi tartalmazhatnak bármely betű, szám és kötőjeleket, mindaddig, amíg a kötőjelek nincsenek egymást követő.

A `api-version` szükséges. Lásd: [keresési Service Versioning](http://msdn.microsoft.com/library/azure/dn864560.aspx) elérhető verzió listáját.

**Kérelem fejlécei**

Az alábbi lista ismerteti a szükséges és választható kérelemfejlécekben.

* `Content-Type`: Kötelező. Állítsa ezt a beállítást`application/json`
* `api-key`: Kötelező. A `api-key` szolgál
* a keresőszolgáltatása kérés hitelesítéséhez. Egy karakterláncértéket, a szolgáltatás egyedi. A **a Create Index** kérelem tartalmaznia kell egy `api-key` fejlécben az adminisztrációs kulcsot (nem egy lekérdezési kulcsot).

Konfigurálnia kell a szolgáltatás nevét, a kérelem URL-címe összeállításához. A szolgáltatás nevet is kaphat és `api-key` a szolgáltatás irányítópultján, az Azure portálon. Lásd: [Azure Search szolgáltatás létrehozása a portálon](search-create-service-portal.md) navigációs segítségét.

<a name="RequestData"></a>
**Törzs szintaxis**

A kérelem törzse tartalmaz sémadefiníciót, amely tartalmazza az adatmezők belül, amely ezt az indexet, adattípusok, attribútumok, valamint megfelelő dokumentumok pontozása lekérdezések során használt profilok pontozás szünnap fog kell táplált dokumentumok listáját .

Vegye figyelembe, hogy az egy POST kérést, meg kell adnia az index neve a kérés törzsében.

Csak lehet egy kulcsmezőt az index. Kell lennie a mezőnek van. Ez a mező képviseli az tárolja az index egyes dokumentumok egyedi azonosítóját.

Az index fő részeket közé tartoznak a következők:

* `name`
* `fields`amely az indexbe, például név, adattípus és ezt a mezőt az engedélyezett műveletek meghatározó tulajdonságok biztosítása táplált lesz.
* `suggesters`Automatikus kitöltés vagy begépelt lekérdezések használatos.
* `scoringProfiles`Egyéni keresési pontszám prioritás használatos. Lásd: [pontozási profilok hozzáadása](https://msdn.microsoft.com/library/azure/dn798928.aspx) részleteiről.
* `analyzers`, `charFilters`, `tokenizers`, `tokenFilters` meghatározásához a dokumentumok lekérdezések példánycímkének/kereshető jogkivonatokba bontottuk hogyan használják. Lásd: [elemzése az Azure Search](https://aka.ms//azsanalysis) részleteiről.
* `defaultScoringProfile`használja az alapértelmezett viselkedés pontozási felülírásához.
* `corsOptions`engedélyezi eltérő eredetű lekérdezések írásában, az index.

A szerkezetének kialakítása a kérelem hasznos szintaxisa a következő. Egy minta kérelem biztosítja az ebben a témakörben további.

    {
      "name": (optional on PUT; required on POST) "name_of_index",
      "fields": [
        {
          "name": "name_of_field",
          "type": "Edm.String | Collection(Edm.String) | Edm.Int32 | Edm.Int64 | Edm.Double | Edm.Boolean | Edm.DateTimeOffset | Edm.GeographyPoint",
          "searchable": true (default where applicable) | false (only Edm.String and Collection(Edm.String) fields can be searchable),
          "filterable": true (default) | false,
          "sortable": true (default where applicable) | false (Collection(Edm.String) fields cannot be sortable),
          "facetable": true (default where applicable) | false (Edm.GeographyPoint fields cannot be facetable),
          "key": true | false (default, only Edm.String fields can be keys),
          "retrievable": true (default) | false,              
          "analyzer": "name of the analyzer used for search and indexing", (only if 'searchAnalyzer' and 'indexAnalyzer' are not set)
          "searchAnalyzer": "name of the search analyzer", (only if 'indexAnalyzer' is set and 'analyzer' is not set)
          "indexAnalyzer": "name of the indexing analyzer" (only if 'searchAnalyzer' is set and 'analyzer' is not set)
        }
      ],
      "suggesters": [
        {
          "name": "name of suggester",
          "searchMode": "analyzingInfixMatching" (other modes may be added in the future),
          "sourceFields": ["field1", "field2", ...]
        }
      ],
      "scoringProfiles": [
        {
          "name": "name of scoring profile",
          "text": (optional, only applies to searchable fields) {
            "weights": {
              "searchable_field_name": relative_weight_value (positive numbers),
              ...
            }
          },
          "functions": (optional) [
            {
              "type": "magnitude | freshness | distance | tag",
              "boost": # (positive number used as multiplier for raw score != 1),
              "fieldName": "...",
              "interpolation": "constant | linear (default) | quadratic | logarithmic",
              "magnitude": {
                "boostingRangeStart": #,
                "boostingRangeEnd": #,
                "constantBoostBeyondRange": true | false (default)
              },
              "freshness": {
                "boostingDuration": "..." (value representing timespan leading to now over which boosting occurs)
              },
              "distance": {
                "referencePointParameter": "...", (parameter to be passed in queries to use as reference location, see "scoringParameter" for syntax details)
                "boostingDistance": # (the distance in kilometers from the reference location where the boosting range ends)
              },
              "tag": {
                "tagsParameter": "..." (parameter to be passed in queries to specify list of tags to compare against target field, see "scoringParameter" for syntax details)
              }
            }
          ],
          "functionAggregation": (optional, applies only when functions are specified)
            "sum (default) | average | minimum | maximum | firstMatching"
        }
      ],
      "analyzers":(optional)[ ... ],
      "charFilters":(optional)[ ... ],
      "tokenizers":(optional)[ ... ],
      "tokenFilters":(optional)[ ... ],
      "defaultScoringProfile": (optional) "...",
      "corsOptions": (optional) {
        "allowedOrigins": ["*"] | ["origin_1", "origin_2", ...],
        "maxAgeInSeconds": (optional) max_age_in_seconds (non-negative integer)
      }
    }

**Indexattribútumok**

A következő attribútumok egy index létrehozásakor állítható be. További információk a pontozási és profilok pontozási: [hozzáadása a pontozási profil](https://msdn.microsoft.com/library/azure/dn798928.aspx).

`name`-Beállítja a mező nevét.

`type`– Beállítja a mező adattípusát.

`searchable`-Állapotúként jelöli meg a mező teljes szöveges keresés képes. Ez azt jelenti, hogy az elemzés során indexelő szóhatároló például is változni fog. Ha egy `searchable` mező "napfényes", belső, például értékre lesznek osztva az egyes jogkivonatokba "moziba" és "day". Ez lehetővé teszi, hogy ezek a feltételek a teljes szöveges keresés. Típusú mezők `Edm.String` vagy `Collection(Edm.String)` vannak `searchable` alapértelmezés szerint. Nem lehet a más típusú mezők `searchable`.

* **Megjegyzés:**: `searchable` mezők az indexben területnek felhasználását, mivel az Azure Search fogja tárolni egy további tokenekre verzióját a mező értékét a teljes szöveges keresést. Menti a terület az indexben, és nem kell szerepeltetni keresések mező, akkor `searchable` való `false`.

`filterable`-Lehetővé teszi a mutató hivatkozás fog megjelenni a mező `$filter` lekérdezések. `filterable`eltér az `searchable` a karakterláncok kezelésének módját. Típusú mezők `Edm.String` vagy `Collection(Edm.String)` , amelyek `filterable` nem kerülnek szóhatároló, így csak pontosan megegyezik az összehasonlítást. Például, ha a mező `f` "moziba Day", `$filter=f eq 'sunny'` megkeresi, nincs találat, de `$filter=f eq 'sunny day'` lesz. A mezők egyike sem `filterable` alapértelmezés szerint.

`sortable`-Alapértelmezés szerint a rendszer rendezése az eredmények pontszám, de sok elhatározásuk felhasználók érdemes a dokumentumok mezők szerint rendezhető. Típusú mezők `Collection(Edm.String)` nem lehet `sortable`. A többi mező kitöltése `sortable` alapértelmezés szerint.

`facetable`-A keresési eredmények találati száma (a példában, keresse meg a digitális fényképezőgépek, és tekintse meg a találatok márka, Megapixel, ár, stb.) kategória szerint tartalmazó bemutató jellemzően használt. Ez a beállítás nem használható típusú mezők `Edm.GeographyPoint`. A többi mező kitöltése `facetable` alapértelmezés szerint.

* **Megjegyzés:**: típusú mezők `Edm.String` , amelyek `filterable`, `sortable`, vagy `facetable` legfeljebb 32 KB-os hossza. Ennek az az oka egy egyetlen keresési kifejezés számít ilyen mezők, és az Azure Search a kifejezés hossza legfeljebb 32 KB lehet. Ha a szöveg hosszabb, mint a tárolása egy mezőnek van szüksége, szüksége lesz explicit módon meghatározhatja az `filterable`, `sortable`, és `facetable` való `false` az index definícióját a.
* **Megjegyzés:**: a mező rendelkezik-e beállítva a fenti attribútumok egyike `true` (`searchable`, `filterable`, `sortable`, vagy`facetable`) mező hatékonyan ki van zárva a fordított indexből. Ez akkor hasznos, amelyek nem szerepelnek a lekérdezések, amelyekre szükség van a találatok között mezők. Ilyen mezők kivételével az indexből javítja a teljesítményt.

`key`-A-dokumentumokat tárolhat a index egyedi azonosítót tartalmazó mezőt jelöli meg. Pontosan egy mezőt ki kell választani a `key` típusúnak kell lennie, és a mező `Edm.String`. Kulcsmezők segítségével kereshet a dokumentumok keresztül közvetlenül a [keresési API](#LookupAPI).

`retrievable`-Állítja be, hogy a mező a keresési eredmény adhatók vissza.  Ez akkor hasznos, ha szeretné (például margó) mező használható szűrőként, rendezés, vagy pontozási mechanizmus, de nem szeretné, hogy a végfelhasználók számára megjelenített a mező. Ennek az attribútumnak kell lennie `true` a `key` mezőket.

`analyzer`-Beállítja a analyzer keresési és indexelési idő használatának a mező nevét. Tekintse meg a megengedett értékhalmazt [elemzőkkel](https://msdn.microsoft.com/library/mt605304.aspx). Ez a beállítás használható csak `searchable` mezőket, és nem állítható be, vagy együtt `searchAnalyzer` vagy `indexAnalyzer`.  Miután a analyzer választja, azt a mező nem módosítható.

`searchAnalyzer`– Beállítja a mező a keresés közben használatos analyzer nevét. Tekintse meg a megengedett értékhalmazt [elemzőkkel](https://msdn.microsoft.com/library/mt605304.aspx). Ez a beállítás használható csak `searchable` mezőket. Együtt kell beállítani `indexAnalyzer` és együtt nem állítható be a `analyzer` lehetőséget. Az elemző meglévő mezőre lehet frissíteni.

`indexAnalyzer`– Beállítja a mező az indexelő közben használatos analyzer nevét. Tekintse meg a megengedett értékhalmazt [elemzőkkel](https://msdn.microsoft.com/library/mt605304.aspx). Ez a beállítás használható csak `searchable` mezőket. Együtt kell beállítani `searchAnalyzer` és együtt nem állítható be a `analyzer` lehetőséget. Miután a analyzer választja, azt a mező nem módosítható.

`suggesters`-A keresési mód és a javaslatok a tartalom forrásának mezők beállítása. Lásd: [Javaslattevők](#Suggesters) részleteiről.

`scoringProfiles`-Határozza meg az egyéni pontozási viselkedéseket, amelyek lehetővé teszik, hogy befolyásolják mely elemek jelennek meg a keresési eredmények között magasabb. A pontozási profil mező súlyok és funkciók épülnek fel. Lásd: [hozzáadása a pontozási profil](https://msdn.microsoft.com/library/azure/dn798928.aspx) további információt a pontozási profil használt attribútumoknál.

<!-- This is a standalone topic in MSDN -->
<a name="LanguageSupport"></a>
**Nyelvi támogatás**

Kereshető mezők változni elemzés, hogy a legtöbb gyakran magában foglalja a szóhatároló, szöveges normalizálási és szűri ki a feltételeket. Alapértelmezés szerint az Azure Search kereshető mezőt és elemzése a [Apache Lucene szabványos analyzer](http://lucene.apache.org/core/4_9_0/analyzers-common/index.html) szöveg amely bontja a következő elemek a["Unicode szöveg Szegmentálás"](http://unicode.org/reports/tr29/) szabályok. Emellett a szabványos analyzer kisbetű formájukban alakítja az összes karaktert. Indexelt dokumentumok és a keresési feltételek halad át az elemzés indexelő és a lekérdezés feldolgozása során.

Az Azure Search támogatja a különböző nyelveken. Minden egyes nyelvi egy nem szabványos szöveg analyzer, amelyek jellemzői egy adott nyelvhez tartozó fiókok igényel. Az Azure Search kétféle típusú elemzőkkel:

* 35 elemzőkkel Lucene által támogatott.
* 50 elemzőkkel által védett Microsoft természetes nyelvű Office-és Bing használt technológia feldolgozása támogatott.

Néhány fejlesztők Lucene több ismerős, egyszerű, nyílt forráskódú megoldás lehet, hogy inkább. Lucene elemzőkkel gyorsabb, de a Microsoft elemzőkkel képességeit, például Lemmatizálás, a word decompounding (például német, dán, holland, svéd, norvég, észt, Befejezés, magyar, Szlovák nyelven) és az entitás felismerési (URL-címek, speciális e-mailek, dátumok, számok). Ha lehetséges futtassa a Microsoft és a Lucene elemzőkkel eldönteni, melyik felel meg leginkább az összehasonlítása.

***Annak összevetése***

Az angol nyelvű tájékoztatáshoz a Lucene analyzer bővíti a szabványos analyzer. Az (záró tartozó) possessives eltávolítja a szavakat, vonatkozik, amelyek megfelelően [Porter származó algoritmus](http://tartarus.org/~martin/PorterStemmer/), és eltávolítja az angol [szavak leállítása](http://en.wikipedia.org/wiki/Stop_words).

Összehasonlításképpen a Microsoft analyzer helyett származó Lemmatizálás hajt végre. Az azt jelenti, hogy is képes kezelni ragozott és szabálytalan word űrlapok javulás mi több megfelelő találatok eredményez (7 modulja figyelési [Azure keresési MVA bemutató](http://www.microsoftvirtualacademy.com/training-courses/adding-microsoft-azure-search-to-your-websites-and-apps) további részletekért).

A Microsoft elemzőkkel indexelő van átlagos Lucene megfelelő, attól függően, hogy a nyelvi lassabb a két-három alkalommal. Keresés teljesítménye nem átlagos mérete lekérdezések jelentős hatással lehet.

***Konfigurálás***

Az egyes mezők az index definícióját, beállíthatja a `analyzer` tulajdonság, amely meghatározza, mely nyelvi és szállítói analyzer névre. Az azonos analyzer fogja alkalmazni, amikor az indexelő, és ezt a mezőt keresésekor.
Lehet például az angol, francia és spanyol-párhuzamos ugyanazt az indexet a létező Szálloda leírások külön mezők. Használja a ["searchFields" lekérdezésparaméter](#SearchQueryParameters) adhatja meg, melyik nyelvspecifikus mezővel rákereshet szemben a lekérdezésekben. Áttekintheti a lekérdezés példák, amelyek tartalmazzák a `analyzer` tulajdonság [dokumentumok keresése](#SearchDocs). 

***Analyzer listája***

Az alábbiakban van együtt Lucene és a Microsoft analyzer nevek támogatott nyelvek listáját.

<table style="font-size:12">
    <tr>
        <th>Nyelv</th>
        <th>Microsoft-elemző eszköz neve</th>
        <th>Lucene analyzer neve</th>
    </tr>
    <tr>
        <td>arab</td>
        <td>ar.Microsoft</td>
        <td>ar.lucene</td>        
    </tr>
    <tr>
        <td>örmény</td>
        <td></td>
        <td>HY.lucene</td>
      </tr>
    <tr>
        <td>Bangla</td>
        <td>BN.Microsoft</td>
        <td></td>
    </tr>
      <tr>
        <td>baszk</td>
        <td></td>
        <td>EU.lucene</td>
    </tr>
      <tr>
         <td>bolgár</td>
        <td>BG.Microsoft</td>
        <td>BG.lucene</td>
      </tr>
      <tr>
        <td>katalán</td>
        <td>CA.Microsoft</td>
        <td>CA.lucene</td>          
      </tr>
    <tr>
        <td>kínai (egyszerűsített)</td>
        <td>zh-Hans.microsoft</td>
        <td>zh-Hans.lucene</td>        
    </tr>
    <tr>
        <td>kínai (hagyományos)</td>
        <td>zh-Hant.microsoft</td>
        <td>zh-Hant.lucene</td>        
    <tr>
    <tr>
        <td>horvát</td>
        <td>hr.Microsoft</td>
        <td/></td>
    </tr>
    <tr>
        <td>cseh</td>
        <td>cs.Microsoft</td>
        <td>cs.lucene</td>        
    </tr>    
    <tr>
        <td>dán</td>
        <td>da.Microsoft</td>
        <td>da.lucene</td>        
    </tr>    
    <tr>
        <td>holland</td>
        <td>NL.Microsoft</td>
        <td>NL.lucene</td>    
    </tr>    
    <tr>
        <td>Angol</td>        
        <td>en.Microsoft</td>
        <td>en.lucene</td>        
    </tr>
    <tr>
        <td>észt</td>
        <td>et.Microsoft</td>
        <td></td>
    </tr>
    <tr>
        <td>finn</td>
        <td>Fi.Microsoft</td>
        <td>Fi.lucene</td>        
    </tr>    
    <tr>
        <td>francia</td>
        <td>FR.Microsoft</td>
        <td>FR.lucene</td>        
    </tr>
    <tr>
        <td>galíciai</td>
        <td></td>
        <td>GL.lucene</td>        
      </tr>
    <tr>
        <td>német</td>
        <td>de.Microsoft</td>
        <td>de.lucene</td>        
    </tr>
    <tr>
        <td>görög</td>
        <td>el.Microsoft</td>
        <td>el.lucene</td>        
    </tr>
    <tr>
        <td>gudzsaráti</td>
        <td>Gu.Microsoft</td>
        <td></td>
    </tr>
    <tr>
        <td>héber</td>
        <td>He.Microsoft</td>
        <td></td>
    </tr>
    <tr>
        <td>hindi</td>
        <td>Hi.Microsoft</td>
        <td>Hi.lucene</td>        
    </tr>
    <tr>
        <td>magyar</td>        
        <td>hu.Microsoft</td>
        <td>hu.lucene</td>
    </tr>
    <tr>
        <td>izlandi</td>
        <td>is.Microsoft</td>
        <td></td>
    </tr>
    <tr>
        <td>Indonéziai (Bahasa)</td>
        <td>ID.Microsoft</td>
        <td>ID.lucene</td>        
    </tr>
    <tr>
        <td>ír</td>
        <td></td>
          <td>GA.lucene</td>
    </tr>
    <tr>
        <td>olasz</td>
        <td>IT.Microsoft</td>
        <td>IT.lucene</td>        
    </tr>
    <tr>
        <td>japán</td>
        <td>ja.Microsoft</td>
        <td>ja.lucene</td>

    </tr>
    <tr>
        <td>kannada</td>
        <td>ka.Microsoft</td>
        <td></td>
    </tr>
    <tr>
        <td>koreai</td>
        <td>ko.Microsoft</td>
        <td>ko.lucene</td>
    </tr>
    <tr>
        <td>lett</td>        
        <td>LV.Microsoft</td>
        <td>LV.lucene</td>    
    </tr>
    <tr>
        <td>litván</td>
        <td>lt.Microsoft</td>
        <td></td>
    </tr>
    <tr>
        <td>malajálam</td>
        <td>ml.Microsoft</td>
        <td></td>
    </tr>
    <tr>
        <td>Maláj (latin betűs)</td>
        <td>MS.Microsoft</td>
        <td></td>
    </tr>
    <tr>
        <td>marathi</td>
        <td>MR.Microsoft</td>
        <td></td>
    </tr>
    <tr>
        <td>norvég</td>
        <td>NB.Microsoft</td>
        <td>No.lucene</td>        
    </tr>
      <tr>
        <td>perzsa</td>
        <td></td>
        <td>fa.lucene</td>        
      </tr>
    <tr>
        <td>lengyel</td>
        <td>pl.Microsoft</td>
        <td>pl.lucene</td>        
    </tr>
    <tr>
        <td>Portugál (brazíliai)</td>
        <td>PT-Br.microsoft</td>
        <td>PT-Br.lucene</td>        
    </tr>
    <tr>
        <td>Portugál (Portugália)</td>
        <td>PT-Pt.microsoft</td>        
        <td>PT-Pt.lucene</td>
    </tr>
    <tr>
        <td>pandzsábi</td>
        <td>Pa.Microsoft</td>
        <td></td>
    </tr>
    <tr>
        <td>román</td>
        <td>ro.Microsoft</td>
        <td>ro.lucene</td>
    </tr>
    <tr>
        <td>orosz</td>
        <td>RU.Microsoft</td>
        <td>RU.lucene</td>    
    </tr>
    <tr>
        <td>szerb (cirill betűs)</td>
        <td>az SR-cyrillic.microsoft</td>
        <td></td>
    </tr>
    <tr>
        <td>Szerb (latin betűs)</td>
        <td>az SR-latin.microsoft</td>
        <td></td>
    </tr>
    <tr>
        <td>szlovák</td>
        <td>SK.Microsoft</td>
        <td></td>
    </tr>
    <tr>
        <td>szlovén</td>
        <td>SL.Microsoft</td>
        <td></td>
    </tr>
    <tr>
        <td>spanyol</td>
        <td>es.Microsoft</td>
        <td>es.lucene</td>
    </tr>
    <tr>
        <td>svéd</td>
        <td>SV.Microsoft</td>
        <td>SV.lucene</td>
    </tr>

    <tr>
        <td>tamil</td>
        <td>TA.Microsoft</td>
        <td></td>
    </tr>
    <tr>
        <td>telugu</td>
        <td>Te.Microsoft</td>
        <td></td>
    </tr>
    <tr>
        <td>thai</td>
        <td>TH.Microsoft</td>
        <td>TH.lucene</td>
    </tr>
    <tr>
        <td>török</td>
        <td>tr.Microsoft</td>
        <td>tr.lucene</td>        
    </tr>
    <tr>
        <td>ukrán</td>
        <td>UK.Microsoft</td>
        <td></td>
    </tr>
    <tr>
        <td>urdu</td>
        <td>Your.Microsoft</td>
        <td></td>
    </tr>
    <tr>
        <td>vietnami</td>
        <td>VI.Microsoft</td>
        <td></td>
    </tr>
    <td colspan="3">Emellett az Azure Search biztosít nyelvtől független analyzer konfigurációk</td>
    <tr>
        <td>A szabványos ASCII-részekre bontás</td>
        <td>standardasciifolding.lucene</td>
        <td>
        <ul>
            <li>Unicode szöveg Szegmentálás (szabványos jogkivonatokat létrehozó)</li>
            <li>ASCII modellrész-létrehozási szűrő - Unicode-karaktereket, amelyek nem tartoznak a először 127 ASCII-karakterekből álló az ASCII megfelelő konvertálja. Ez akkor hasznos, a ékezetek eltávolításához.</li>
        </ul>
        </td>
    </tr>
</table>

Nevek attribútummal rendelkező összes elemzőkkel <i>lucene</i> szerint vannak kapcsolva [Apache Lucene nyelvi elemzőkkel](http://lucene.apache.org/core/4_9_0/analyzers-common/overview-summary.html). További információ az ASCII-részekre bontás szűrő található [Itt](http://lucene.apache.org/core/4_9_0/analyzers-common/org/apache/lucene/analysis/miscellaneous/ASCIIFoldingFilter.html).

**Javaslattevők**

A `suggester` határozza meg, melyik index mező használható támogatja a keresést automatikusan hajthat végre. Részleges keresőkifejezések küldött általában a [javaslatok API](#Suggestions) közben a felhasználó éppen gépel egy keresési lekérdezés, és az API-t adja vissza, amely javasolt kifejezések. A javaslattevő az indexet meghatározó meghatározza, hogy mely mezők alapján állítja össze a begépelt keresési feltételeket. Lásd: [Javaslattevők](#Suggesters) konfigurációs részletek.

**Pontozási profilok**

A `scoringProfile` egyéni pontozási viselkedéseket, amelyek lehetővé teszik, hogy befolyásolják mely elemek jelennek meg a keresési eredmények magasabb határozza meg. A pontozási profil mező súlyok és funkciók épülnek fel. Használja őket, meg kell adnia egy profilt a lekérdezési karakterlánc neve.

A pontozási profil alapértelmezett működik, a háttérben minden eleme egy eredményhalmazban szerepel egy keresési pontszám kiszámításához. Használhatja a belső relevanciaprofil névtelen. Másik lehetőségként állítsa `defaultScoringProfile` egy egyéni-profilt kell használnia az alapértelmezett, meghívni, amikor egyéni profil nincs megadva a lekérdezési karakterlánc.

Lásd: [pontozási profil hozzáadása egy keresési indexszel (Azure Search szolgáltatás REST API)](search-api-scoring-profiles-2015-02-28-preview.md) részleteiről.

**A CORS-beállítások**

Ügyféloldali Javascript nem hívható bármely API-k alapértelmezés szerint, mert a böngésző megakadályozza, hogy minden eltérő eredetű kérések. A CORS (eltérő eredetű erőforrások megosztása) módosításával engedélyezheti a `corsOptions` engedélyezi eltérő eredetű lekérdezéseket, amelyekkel az index attribútum. Vegye figyelembe, hogy egyetlen lekérdezés biztonsági okokból a CORS API-k támogatása. A CORS állíthat be a következő beállításokat:

* `allowedOrigins`(kötelező): a lista tartalmazza, amelyek az indexe hozzáférési engedélyt. Ez azt jelenti, hogy az adott források kiszolgált bármelyik Javascript-kód lekérdezheti az indexét (feltéve, hogy a megfelelő API-kulcsot biztosít) engedélyezett lesz. Eredetek formátuma általában az űrlap `protocol://fully-qualified-domain-name:port` bár gyakran nincs megadva a port. Lásd: [Ez a cikk](http://go.microsoft.com/fwlink/?LinkId=330822) további részleteket.
  * Ha azt szeretné, hogy férjen hozzá minden eredet, `*` egyetlen elemként a `allowedOrigins` tömb. Vegye figyelembe, hogy **ez nem ajánlott eljárás a termelési keresési szolgáltatások.** Azonban hasznos lehet a fejlesztési vagy a hibakeresési célra.
* `maxAgeInSeconds`(nem kötelező): a böngészők arra használják ezt az értéket időtartama (másodpercben) meghatározásához a gyorsítótár CORS-alapú elővizsgálati válaszok. Ez nem negatív egész számnak kell lennie. Minél nagyobb ez az érték, a jobb teljesítmény, de annál tovább fog tartani, amíg a CORS-házirend változásai érvénybe léptetéséhez. Ha nincs megadva, a alapértelmezett 5 perces időtartamot használható.

<a name="CreateUpdateIndexExample"></a>
**Kérelem törzse – példa**

    {
      "name": "hotels",  
      "fields": [
        {"name": "hotelId", "type": "Edm.String", "key": true, "searchable": false},
        {"name": "baseRate", "type": "Edm.Double"},
        {"name": "description", "type": "Edm.String", "filterable": false, "sortable": false, "facetable": false},
        {"name": "description_fr", "type": "Edm.String", "filterable": false, "sortable": false, "facetable": false, "analyzer": "fr.lucene"},
        {"name": "hotelName", "type": "Edm.String"},
        {"name": "category", "type": "Edm.String"},
        {"name": "tags", "type": "Collection(Edm.String)"},
        {"name": "parkingIncluded", "type": "Edm.Boolean"},
        {"name": "smokingAllowed", "type": "Edm.Boolean"},
        {"name": "lastRenovationDate", "type": "Edm.DateTimeOffset"},
        {"name": "rating", "type": "Edm.Int32"},
        {"name": "location", "type": "Edm.GeographyPoint"}
      ],
      "suggesters": [
        {
          "name": "sg",
          "searchMode": "analyzingInfixMatching",
          "sourceFields": ["hotelName"]
        }
      ]
    }

**Válasz**

A kérelem sikeres: "201 Created".

Alapértelmezés szerint az adott válasz törzsének meg az index létrehozásának JSON fogja tartalmazni. Ha a `Prefer` fejléc értéke `return=minimal`, az adott válasz törzse üres lesz, és a sikeres állapotkód lesz "204 Nincs tartalom" helyett "201 Created". Ez érvényét veszti, függetlenül attól, hogy PUT vagy POST használatával hozza létre az indexet.

**Megjegyzések**

Jelenleg korlátozott támogatása az index sémafrissítések. Bármely sémafrissítések újraindexelés, például a mezőtípusok módosítása újraindexelést igénylő jelenleg nem támogatottak. Bár a már meglévő mezők nem módosítható vagy törölhető, új mezők felveheti létező indexek bármikor. Amikor új mező ad hozzá, az indexben szereplő összes meglévő dokumentumok automatikusan fog rendelkezni a mező null értékű. További tárhely az új dokumentumok az indexhez felvételének befejezéséig fognak használni.

<a name="Suggesters"></a>

## <a name="suggesters"></a>Javaslattevők
A javaslatok az Azure Search szolgáltatása begépelt vagy automatikusan hajthat végre a lekérdezés egy olyan képességet, jegyez be a keresőmezőbe részleges karakterlánc-bemenetek válaszul lehetséges keresési feltételeinek megadása. Bizonyára észrevette konstrukciókat kereskedelmi keresőmotorok használatakor: írja be a ".NET" Bing előállító feltételei listájának ".NET 4.5-ös", ".NET-keretrendszer 3.5-ös", és így tovább. A keresési szolgáltatás REST API használata esetén a következők javaslatok valósít meg egyéni Azure Search alkalmazás szükségesek:

* Engedélyezze a javaslatok hozzáadása egy **javaslattevő** meghívták az indexben, amely a neve, a keresési mód és a mezőket sorolja fel, amelynek begépelt konstrukció. Például ha "város" mező, írja be a "Tenger" részleges keresési karakterlánc jár a "Seattle", "Tengerpart" és "Seatac" (mind a hármat olyan tényleges város nevek) lekérdezés javaslatként a felhasználó számára biztosított.
* Javaslatok meghívásához hívja a [javaslatok API](#Suggestions) az alkalmazás kódjában. Általában részleges keresőkifejezések vannak a szolgáltatásnak küldött, miközben a felhasználó éppen gépel egy keresési lekérdezés, és ez az API javasolt kifejezések készletét adja vissza.

Ez a cikk azt ismerteti, hogyan konfigurálhatja a **javaslattevő**. Tekintse meg a [javaslatok API](#Suggestions) talál részletes információt a javaslattevő használatáról.

**Használat**

`Suggesters`jönnek létre az index és a munka legjobban, ha annak alapján hozza létre az adott dokumentumok helyett laza szót vagy kifejezést. A legjobb jelölt mezők kitöltése, címek, nevek és egyéb viszonylag rövid kifejezés elem azonosításra alkalmas. Kevésbé hatékony olyan ismétlődő, például a kategóriák és a címkék, és nagyon hosszú mezők például leírásokat és megjegyzéseket mezőket.

Az index definícióját részeként is hozzáadhat egy egyetlen javaslattevő, hogy a `suggesters` gyűjtemény. A javaslattevő meghatározó tulajdonságok közé tartoznak a következők:

* `name`: Az a javaslattevő neve. A javaslattevő nevét használja, meghívásakor a `suggest` API.
* `searchMode`: A jelölt kifejezések keresésére használt stratégia. A jelenleg egyetlen támogatott mód van `analyzingInfixMatching`, melyik teljesít rugalmas megfelelő mondat eleji vagy mondat közepén.
* `sourceFields`: Egy vagy több mezőinek listáját, amelyek a forrás javaslatok tartalmának. Csak típusú mezők `Edm.String` és `Collection(Edm.String)` lehet, hogy javaslatokat adatforrásait. Csak olyan mezőket, amelyekre nincs beállítva egyéni nyelvi elemzőt is használható.

**A javaslattevő – példa**

A javaslattevő az index részét képezi. Csak egy javaslattevő is szerepel a `suggesters` mellett a mezők gyűjteményében a jelenlegi verzióban gyűjtemény és `scoringProfiles`.

        {
          "name": "hotels",
          "fields": [
             . . .
           ],
          "suggesters": [
            {
            "name": "sg",
            "searchMode": "analyzingInfixMatching",
            "sourceFields: ["hotelName", "category"]
            }
          ],
          "scoringProfiles": [
             . . .
          ]
        }

> [!NOTE]
> Ha használta az Azure Search nyilvános előzetes verziójának `suggesters` a felváltja egy régebbi logikai tulajdonság (`"suggestions": false`), amely csak a támogatott előtag javaslatok rövid karakterláncok (a 3-25 karakterből álló). A csere `suggesters`, támogatja a infix megfelelő megtalált jobb tűréssel tévesztések a keresési karakterláncot az egyező kifejezések elején vagy közepén mező tartalmát. Az általánosan elérhető kiadástól kezdve ez már a javaslatok API csak végrehajtására. A régebbi `suggestions` bevezetett tulajdonság `api-version=2014-07-31-Preview` ebben a verzióban használható folyamatosan, de nincs operatív a `2015-02-28` vagy az Azure Search újabb verzióit.
> 
> 

<a name="UpdateIndex"></a>

## <a name="update-index"></a>Index frissítése
Frissítheti a meglévő index belül Azure Search használatával egy HTTP PUT-kérelmet. Frissítések lehetnek új mező hozzáadása a meglévő séma, a CORS-beállítások módosítása és a pontozási profil módosítása. Lásd: [hozzáadása a pontozási profil](https://msdn.microsoft.com/library/azure/dn798928.aspx) további információt. A kérelem URI-azonosítója frissítéséhez index nevét kell megadni:

    PUT https://[search service url]/indexes/[index name]?api-version=[api-version]
    Content-Type: application/json
    api-key: [admin key]

**Fontos:** index sémafrissítések támogatása korlátozódik, amelyek nem igényelnek, a search-index újraépítése műveletek. Bármely sémafrissítések műveletek, például a mezőtípusok módosítása újraindexelést igénylő jelenleg nem támogatottak. Új mezők hozzáadása bármikor, noha a már meglévő mezők nem módosítható vagy törölhető. Ugyanez érvényes `suggesters`. Új mezők adhatók hozzá, a javaslattevő időpontjában mezőket ad hozzá, de a mezők nem távolítható el `suggesters` és a már meglévő mezők nem vehető fel `suggesters`.

Új mező hozzáadása az index, amikor az indexben szereplő összes meglévő dokumentumok automatikusan fog rendelkezni a mező null értékű. További tárhely az új dokumentumok az indexhez felvételének befejezéséig fognak használni.

**Kérés**

HTTPS-kapcsolat szükséges az összes szolgáltatási kérelmek. A **Index frissítése** segítségével a HTTP PUT kérés jön létre. A PUT érvénytelen az URL-cím. Ha az index nem létezik, akkor jön létre. Ha az index már létezik, az új definition frissül.

Az index neve kell kell kisbetű, betűvel vagy számmal kezdődhet, nincs perjeleket vagy pontokból és legfeljebb 128 karakter lehet. Után az index nevének betűvel vagy számmal verziótól kezdődően a nevét a többi tartalmazhatnak bármely betű, szám és kötőjeleket, mindaddig, amíg a kötőjelek nincsenek egymást követő.

`api-version=[string]`(kötelező). Az előzetes verzió `api-version=2015-02-28-Preview`. Lásd: [keresési Service Versioning](http://msdn.microsoft.com/library/azure/dn864560.aspx) részletek és alternatív verzióit.

**Kérelem fejlécei**

Az alábbi lista ismerteti a szükséges és választható kérelemfejlécekben.

* `Content-Type`: Kötelező. Állítsa ezt a beállítást`application/json`
* `api-key`: Kötelező. A `api-key` hitelesíteni a kérelmet a keresőszolgáltatása szolgál. Egy karakterláncértéket, a szolgáltatás egyedi. A **Index frissítése** kérelem tartalmaznia kell egy `api-key` fejlécben az adminisztrációs kulcsot (nem egy lekérdezési kulcsot).

Konfigurálnia kell a szolgáltatás nevét, a kérelem URL-címe összeállításához. Beszerezheti a szolgáltatás nevét és `api-key` a szolgáltatás irányítópultján, az Azure portálon. Lásd: [Azure Search szolgáltatás létrehozása a portálon](search-create-service-portal.md) navigációs segítségét.

**Törzs szintaxis**

Amikor frissíti a meglévő index, a szervezet tartalmaznia kell az eredeti sémadefiníciót és az új mezőket ad hozzá, valamint a módosított pontozási profilok javaslattevők és a CORS-beállítások, ha van ilyen. Ha nem módosítja a pontozási profil és a CORS-beállítások, meg kell adnia az eredeti származó az index létrehozásakor. Általában a legjobb minta frissítések kéri le az index definícióját a GET, módosítsa ezt, akkor PUT frissíti.

Az index létrehozásához használt sémaszintaxis itt jelenik meg kényelmét szolgálja. Lásd: [a Create Index](#CreateIndex) további részleteket.

    {
      "name": (optional) "name_of_index",
      "fields": [
        {
          "name": "name_of_field",
          "type": "Edm.String | Collection(Edm.String) | Edm.Int32 | Edm.Int64 | Edm.Double | Edm.Boolean | Edm.DateTimeOffset | Edm.GeographyPoint",
          "searchable": true (default where applicable) | false (only Edm.String and Collection(Edm.String) fields can be searchable),
          "filterable": true (default) | false,
          "sortable": true (default where applicable) | false (Collection(Edm.String) fields cannot be sortable),
          "facetable": true (default where applicable) | false (Edm.GeographyPoint fields cannot be facetable),
          "key": true | false (default, only Edm.String fields can be keys),
          "retrievable": true (default) | false, 
          "analyzer": "name of the analyzer used for search and indexing", (only if 'searchAnalyzer' and 'indexAnalyzer' are not set)
          "searchAnalyzer": "name of the search analyzer", (only if 'indexAnalyzer' is set and 'analyzer' is not set)
          "indexAnalyzer": "name of the indexing analyzer" (only if 'searchAnalyzer' is set and 'analyzer' is not set)
        }
      ],
      "suggesters": [
        {
          "name": "name of suggester",
          "searchMode": "analyzingInfixMatching" (other modes may be added in the future),
          "sourceFields": ["field1", "field2", ...]
        }
      ],
      "scoringProfiles": [
        {
          "name": "name of scoring profile",
          "text": (optional, only applies to searchable fields) {
            "weights": {
              "searchable_field_name": relative_weight_value (positive numbers),
              ...
            }
          },
          "functions": (optional) [
            {
              "type": "magnitude | freshness | distance | tag",
              "boost": # (positive number used as multiplier for raw score != 1),
              "fieldName": "...",
              "interpolation": "constant | linear (default) | quadratic | logarithmic",
              "magnitude": {
                "boostingRangeStart": #,
                "boostingRangeEnd": #,
                "constantBoostBeyondRange": true | false (default)
              },
              "freshness": {
                "boostingDuration": "..." (value representing timespan leading to now over which boosting occurs)
              },
              "distance": {
                "referencePointParameter": "...", (parameter to be passed in queries to use as reference location, see "scoringParameter" for syntax details)
                "boostingDistance": # (the distance in kilometers from the reference location where the boosting range ends)
              },
              "tag": {
                "tagsParameter": "..." (parameter to be passed in queries to specify list of tags to compare against target field, see "scoringParameter" for syntax details)
              }
            }
          ],
          "functionAggregation": (optional, applies only when functions are specified)
            "sum (default) | average | minimum | maximum | firstMatching"
        }
      ],
      "analyzers":(optional)[ ... ],
      "charFilters":(optional)[ ... ],
      "tokenizers":(optional)[ ... ],
      "tokenFilters":(optional)[ ... ],
      "defaultScoringProfile": (optional) "...",
      "corsOptions": (optional) {
        "allowedOrigins": ["*"] | ["origin_1", "origin_2", ...],
        "maxAgeInSeconds": (optional) max_age_in_seconds (non-negative integer)
      }
    }


**Válasz**

A kérelem sikeres: "204 Nincs tartalom".

Alapértelmezés szerint az adott válasz törzse üres lesz. Azonban ha a `Prefer` fejléc értéke `return=representation`, az adott válasz törzse fogja tartalmazni az index definícióját, hogy frissült a JSON. Ebben az esetben a sikeres állapotkód lesz "200 OK".

**Indexdefiníció egyéni lekérdezések frissítésekor**

Miután egy elemző eszköz, a jogkivonatokat létrehozó, egy token szűrőt, vagy egy karakter szűrő be van állítva, nem lehet módosítani. Új címkék adhatók hozzá a létező indexek csak akkor, ha a `allowIndexDowntime` jelző értéke igaz, a index frissítési kérelem: 

`PUT https://[search service name].search.windows.net/indexes/[index name]?api-version=[api-version]&allowIndexDowntime=true`

Vegye figyelembe, hogy ez a művelet kerül az index offline legalább néhány másodpercen belül, amely az indexelést és a lekérdezés-kérelem sikertelen lesz. Az index teljesítmény- és írási rendelkezésre állását korlátozott az index frissítése után több percig vagy nagyon nagy indexek hosszabb lehet.

<a name="ListIndexes"></a>

## <a name="list-indexes"></a>Lista indexek
A **lista indexek** művelet listáját adja vissza az indexek jelenleg az Azure Search szolgáltatásban.

    GET https://[service name].search.windows.net/indexes?api-version=[api-version]
    api-key: [admin key]

**Kérés**

HTTPS-kapcsolat szükséges az összes szolgáltatási kérelmek. A **lista indexek** kérelem a GET metódussal lehet létrehozni.

`api-version=[string]`(kötelező). Az előzetes verzió `api-version=2015-02-28-Preview`. Lásd: [keresési Service Versioning](http://msdn.microsoft.com/library/azure/dn864560.aspx) részletek és alternatív verzióit.

**Kérelem fejlécei**

Az alábbi lista ismerteti a szükséges és választható kérelemfejlécekben.

* `api-key`: Kötelező. A `api-key` hitelesíteni a kérelmet a keresőszolgáltatása szolgál. Egy karakterláncértéket, a szolgáltatás egyedi. A **lista indexek** kérelem tartalmaznia kell egy `api-key` egy adminisztrációs kulcsot (szemben a lekérdezési kulcs) értékre.

Konfigurálnia kell a szolgáltatás nevét, a kérelem URL-címe összeállításához. Beszerezheti a szolgáltatás nevét és `api-key` a szolgáltatás irányítópultján, az Azure portálon. Lásd: [Azure Search szolgáltatás létrehozása a portálon](search-create-service-portal.md) navigációs segítségét.

**Kérés törzsében**

nincs.

**Válasz**

Állapotkód: 200 OK visszaküldött a sikeres válasz.

Íme egy példa adott válasz törzse:

    {
      "value": [
        {
          "name": "Books",
          "fields": [
            {"name": "ISBN", ...},
            ...
          ]
        },
        {
          "name": "Games",
          ...
        },
        ...
      ]
    }

Vegye figyelembe, hogy a válasz le kíváncsiak vagyunk tulajdonságainak szűrheti. Például, ha azt szeretné, hogy csak index neveinek listáját, használja az OData `$select` lekérdezési lehetőség:

    GET /indexes?api-version=2015-02-28-Preview&$select=name

Ebben az esetben a fenti példa válaszát távolságban jelenjen meg az alábbiak szerint:

    {
      "value": [
        {"name": "Books"},
        {"name": "Games"},
        ...
      ]
    }

Ez a sávszélesség menteni, ha szerepel a keresőszolgáltatása indexek számos hasznos technika.

<a name="GetIndex"></a>

## <a name="get-index"></a>Index beolvasása
A **beolvasása Index** művelet lekérdezi az index definícióját az Azure Search.

    GET https://[service name].search.windows.net/indexes/[index name]?api-version=[api-version]
    api-key: [admin key]

**Kérés**

HTTPS szolgáltatáskérések szükség. A **beolvasása Index** kérelem a GET metódussal lehet létrehozni.

A kérelem URI azonosítója [index neve] határozza meg, hogy melyik index vissza az indexek gyűjteményből.

`api-version=[string]`(kötelező). Az előzetes verzió `api-version=2015-02-28-Preview`. Lásd: [keresési Service Versioning](http://msdn.microsoft.com/library/azure/dn864560.aspx) részletek és alternatív verzióit.

**Kérelem fejlécei**

Az alábbi lista ismerteti a szükséges és választható kérelemfejlécekben.

* `api-key`: A `api-key` hitelesíteni a kérelmet a keresőszolgáltatása szolgál. Egy karakterláncértéket, a szolgáltatás egyedi. A **beolvasása Index** kérelem tartalmaznia kell egy `api-key` egy adminisztrációs kulcsot (szemben a lekérdezési kulcs) értékre.

Konfigurálnia kell a szolgáltatás nevét, a kérelem URL-címe összeállításához. Beszerezheti a szolgáltatás nevét és `api-key` a szolgáltatás irányítópultján, az Azure portálon. Lásd: [Azure Search szolgáltatás létrehozása a portálon](search-create-service-portal.md) navigációs segítségét.

**Kérés törzsében**

nincs.

**Válasz**

Állapotkód: 200 OK visszaküldött a sikeres válasz.

A következő példa JSON a [létrehozása és frissítése Index](#CreateUpdateIndexExample) a válasz forgalma példát.

<a name="DeleteIndex"></a>

## <a name="delete-index"></a>Index törlése
A **Index törlése** művelet eltávolítja az index és a kapcsolódó dokumentumok az Azure Search szolgáltatás. Az index neve kaphat a szolgáltatás irányítópultján, az Azure portálon, vagy az API-t. Lásd: [lista indexek](#ListIndexes) részleteiről.

    DELETE https://[service name].search.windows.net/indexes/[index name]?api-version=[api-version]
    api-key: [admin key]

**Kérés**

HTTPS szolgáltatáskérések szükség. A **Index törlése** kérelmet a DELETE metódussal lehet létrehozni.

A kérelem URI azonosítója [index neve] határozza meg, hogy melyik index a indexgyűjteményhez törölheti.

`api-version=[string]`(kötelező). Az előzetes verzió `api-version=2015-02-28-Preview`. Lásd: [keresési Service Versioning](http://msdn.microsoft.com/library/azure/dn864560.aspx) részletek és alternatív verzióit.

**Kérelem fejlécei**

Az alábbi lista ismerteti a szükséges és választható kérelemfejlécekben.

* `api-key`: Kötelező. A `api-key` hitelesíteni a kérelmet a keresőszolgáltatása szolgál. Egy karakterláncértéket egyedi, a szolgáltatás URL-címre. A **Index törlése** kérelem tartalmaznia kell egy `api-key` fejlécben az adminisztrációs kulcsot (nem egy lekérdezési kulcsot).

Konfigurálnia kell a szolgáltatás nevét, a kérelem URL-címe összeállításához. Beszerezheti a szolgáltatás nevét és `api-key` a szolgáltatás irányítópultján, az Azure portálon. Lásd: [Azure Search szolgáltatás létrehozása a portálon](search-create-service-portal.md) navigációs segítségét.

**Kérés törzsében**

nincs.

**Válasz**

Állapotkód: 204 nem tartalom visszaküldött a sikeres válasz.

<a name="GetIndexStats"></a>

## <a name="get-index-statistics"></a>Megtekintheti a statisztikákat Index
A **beolvasása Index statisztika** művelet számát adja vissza az Azure Search dokumentum az aktuális index, valamint a tárhely kihasználtsága.

    GET https://[service name].search.windows.net/indexes/[index name]/stats?api-version=[api-version]
    api-key: [admin key]

> [!NOTE]
> A dokumentumok száma és a tárhely mérete statisztika nem a valós idejű néhány percenként összegyűjtése. Ezért ez az API által visszaadott statisztikák nem tükrözik legutóbbi indexelési műveletek okozta módosítások.
> 
> 

**Kérés**

HTTPS-kapcsolat szükséges szolgáltatások minden olyan kérelem esetében. A **beolvasása Index statisztika** kérelem a GET metódussal lehet létrehozni.

A kérelem URI azonosítója [index neve] közli az index statisztikáit. a megadott index vissza a szolgáltatás.

`api-version=[string]`(kötelező). Az előzetes verzió `api-version=2015-02-28-Preview`. Lásd: [keresési Service Versioning](http://msdn.microsoft.com/library/azure/dn864560.aspx) részletek és alternatív verzióit.

**Kérelem fejlécei**

Az alábbi lista ismerteti a szükséges és választható kérelemfejlécekben.

* `api-key`: A `api-key` hitelesíteni a kérelmet a keresőszolgáltatása szolgál. Egy karakterláncértéket, a szolgáltatás egyedi. A **beolvasása Index statisztika** kérelem tartalmaznia kell egy `api-key` egy adminisztrációs kulcsot (szemben a lekérdezési kulcs) értékre.

Konfigurálnia kell a szolgáltatás nevét, a kérelem URL-címe összeállításához. Beszerezheti a szolgáltatás nevét és `api-key` a szolgáltatás irányítópultján, az Azure portálon. Lásd: [Azure Search szolgáltatás létrehozása a portálon](search-create-service-portal.md) navigációs segítségét.

**Kérés törzsében**

nincs.

**Válasz**

Állapotkód: 200 OK visszaküldött a sikeres válasz.

A választörzs mérete a következő formátumban:

    {
      "documentCount": number,
      "storageSize": number (size of the index in bytes)
    }

<a name="TestAnalyzer"></a>

## <a name="test-analyzer"></a>Teszt elemző eszköz
A **elemzése API** bemutatja, hogyan egy analyzer bontja szöveg jogkivonatokat.

    POST https://[service name].search.windows.net/indexes/[index name]/analyze?api-version=[api-version]
    Content-Type: application/json
    api-key: [admin key]

**Kérés**

HTTPS-kapcsolat szükséges szolgáltatások minden olyan kérelem esetében. A **elemzése API** kérelmek POST metódussal történő lehet létrehozni.

`api-version=[string]`(kötelező). Az előzetes verzió `api-version=2015-02-28-Preview`. Lásd: [keresési Service Versioning](http://msdn.microsoft.com/library/azure/dn864560.aspx) részletek és alternatív verzióit.

**Kérelem fejlécei**

Az alábbi lista ismerteti a szükséges és választható kérelemfejlécekben.

* `api-key`: A `api-key` hitelesíteni a kérelmet a keresőszolgáltatása szolgál. Egy karakterláncértéket, a szolgáltatás egyedi. A **elemzése API** kérelem tartalmaznia kell egy `api-key` egy adminisztrációs kulcsot (szemben a lekérdezési kulcs) értékre.

Konfigurálnia kell az index neve és a szolgáltatás nevét, a kérelem URL-címe összeállításához. Beszerezheti a szolgáltatás nevét és `api-key` a szolgáltatás irányítópultján, az Azure portálon. Lásd: [Azure Search szolgáltatás létrehozása a portálon](search-create-service-portal.md) navigációs segítségét.

**Kérés törzsében**

    {
      "text": "Text to analyze",
      "analyzer": "analyzer_name"
    }

vagy

    {
      "text": "Text to analyze",
      "tokenizer": "tokenizer_name",
      "tokenFilters": (optional) [ "token_filter_name" ],
      "charFilters": (optional) [ "char_filter_name" ]
    }

A `analyzer_name`, `tokenizer_name`, `token_filter_name` és `char_filter_name` kell lenniük az előre definiált vagy egyéni lekérdezések, tokenizers, lexikális elem és az index char szűrőket érvényes nevét. Ismerje meg, a folyamat lexikális elemzési kapcsolatos információkért tekintse meg a [elemzése az Azure Search](https://aka.ms/azsanalysis).

**Válasz**

Állapotkód: 200 OK visszaküldött a sikeres válasz.

A választörzs mérete a következő formátumban:

    {
      "tokens": [
        {
          "token": string (token),
          "startOffset": number (index of the first character of the token),
          "endOffset": number (index of the last character of the token),
          "position": number (position of the token in the input text)
        },
        ...
      ]
    }

**Elemezze API – példa**

**Kérés**

    {
      "text": "Text to analyze",
      "analyzer": "standard"
    }

**Válasz**

    {
      "tokens": [
        {
          "token": "text",
          "startOffset": 0,
          "endOffset": 4,
          "position": 0
        },
        {
          "token": "to",
          "startOffset": 5,
          "endOffset": 7,
          "position": 1
        },
        {
          "token": "analyze",
          "startOffset": 8,
          "endOffset": 15,
          "position": 2
        }
      ]
    }

- - -
<a name="DocOps"></a>

## <a name="document-operations"></a>A dokumentum műveletek
Az Azure Search index a felhőben tárolt, és fel a szolgáltatás feltöltött JSON-dokumentumok segítségével. Feltöltött összes dokumentumot a corpus keresési adatait tartalmazza. Dokumentumok, amelyeket a keresési feltételek vannak tokenekre, feltölti, a mezők tartalmaznak. A `/docs` URL-szegmenseket, az Azure Search API a dokumentumok index gyűjteményét képviseli. Összes művelet végrehajtását, például feltöltése a gyűjteményt, az egyesítés, törlését és lekérdezését dokumentumok hajtsa végre a megfelelő helyezze keretén belül egyetlen index, ezért az URL-címek az e műveletek mindig indul rendelkező `/indexes/[index name]/docs` adott index neve.

Az alkalmazás kódjában vagy létre kell hoznia JSON-dokumentumokat az Azure Search feltölteni, vagy használhat egy [indexelő](https://msdn.microsoft.com/library/dn946891.aspx) dokumentumok betölteni, ha az adatforrás vagy az Azure SQL Database, vagy az Azure Cosmos DB. Indexek általában fel van töltve, egy Ön által biztosított egyetlen adatkészletből.

Meg kell terveznie, amely egy dokumentumot, amelyet meg szeretne keresni minden elemhez. Egy movie bérleti alkalmazás rendelkezhet movie egy dokumentumot, egy kirakat alkalmazás rendelkezhet SKU egy dokumentumot, online anyagok alkalmazás rendelkezhet működés során egy dokumentumot, a kutatási vállalkozás előfordulhat, hogy az egyes academic papír egy dokumentumot a tárház, és így tovább.

Dokumentumok egy vagy több mező áll. Mezők szöveg, amelyet az Azure Search által a keresési feltételek tokenekre bontott, valamint nem tokenekre vagy szűrők, vagy a pontozási profil használható nem szöveges értékeket tartalmazhat. A név, az adattípusokat és az egyes mezők támogatott keresési funkciók a sémát indexeli határozza meg. Egy azonosító kijelölt minden a sémát indexeli a mezők egyike, és minden egyes dokumentum esetében az Azonosítót tartalmazó mezőt, amely egyedileg azonosítja az indexben a dokumentum értékűnek kell lennie. A dokumentum többi mező megadása nem kötelező, és a rendszer alapértelmezés szerint null értékű, ha nincs megadva. Vegye figyelembe, hogy null értékek nem helyet foglalnak a keresési index.

Feltöltheti a dokumentumok, mielőtt kell már létrehozott az index a szolgáltatásban. Lásd: [a Create Index](#CreateIndex) az első lépéshez vonatkozó további információért.

<a name="AddOrUpdateDocuments"></a>

## <a name="add-update-or-delete-documents"></a>Adja hozzá, frissítés, vagy dokumentumok törlése
Feltöltheti, egyesítési, az egyesítés vagy feltöltés és a delete dokumentumok egy megadott indextől HTTP POST használatával. Nagyszámú a frissítések kötegelése (1000 dokumentumok kötegenként) vagy kötegenként körülbelül 16 MB dokumentumait ajánlott.

    POST https://[service name].search.windows.net/indexes/[index name]/docs/index?api-version=[api-version]
    Content-Type: application/json
    api-key: [admin key]

**Kérés**

HTTPS-kapcsolat szükséges az összes szolgáltatási kérelmek. Feltöltheti, egyesítési, az egyesítés vagy feltöltés és a delete dokumentumok egy megadott indextől HTTP POST használatával.

A kérelem URI-azonosítója tartalmaz [index neve], melyik index dokumentumok küldésére szolgáló megadása. Egyszerre csak egy index a dokumentumokat is közzétesz.

`api-version=[string]`(kötelező). Az előzetes verzió `api-version=2015-02-28-Preview`. Lásd: [keresési Service Versioning](http://msdn.microsoft.com/library/azure/dn864560.aspx) részletek és alternatív verzióit.

**Kérelem fejlécei**

Az alábbi lista ismerteti a szükséges és választható kérelemfejlécekben.

* `Content-Type`: Kötelező. Állítsa ezt a beállítást`application/json`
* `api-key`: Kötelező. A `api-key` hitelesíteni a kérelmet a keresőszolgáltatása szolgál. Egy karakterláncértéket, a szolgáltatás egyedi. A **hozzáadása dokumentumok** kérelem tartalmaznia kell egy `api-key` fejlécben az adminisztrációs kulcsot (nem egy lekérdezési kulcsot).

Konfigurálnia kell a szolgáltatás nevét, a kérelem URL-címe összeállításához. Beszerezheti a szolgáltatás nevét és `api-key` a szolgáltatás irányítópultján, az Azure portálon. Lásd: [Azure Search szolgáltatás létrehozása a portálon](search-create-service-portal.md) navigációs segítségét.

**Kérés törzsében**

A kérelem törzsében indexelése egy vagy több dokumentumot. Dokumentumok egyedi kulcs azonosítják. Minden egyes dokumentum rendelve egy műveletet: feltöltés, egyesítési, mergeOrUpload vagy törlése. Feltöltési kérést kulcs/érték párok készleteként tartalmaznia kell a dokumentum adatokat.

    {
      "value": [
        {
          "@search.action": "upload (default) | merge | mergeOrUpload | delete",
          "key_field_name": "unique_key_of_document", (key/value pair for key field from index schema)
          "field_name": field_value (key/value pairs matching index schema)
            ...
        },
        ...
      ]
    }

> [!NOTE]
> A dokumentum kulcsok csak betűket, számokat, kötőjeleket tartalmazhat ("-"), aláhúzásjelet ("_"), és egyenlőségjelet ("="). További részletekért lásd: [elnevezési szabályait](https://msdn.microsoft.com/library/azure/dn857353.aspx).
> 
> 

**A dokumentum műveletek**

* `upload`: Egy feltöltési művelet hasonlít "upsert" Ha a dokumentum program beszúrja, ha új és frissített/lecserélik akkor, ha van ilyen. Vegye figyelembe, hogy a frissítés esetben minden mező helyett.
* `merge`: Egyesítés egy meglévő dokumentum frissíti a megadott mezőket. Ha a dokumentum nem létezik, az egyesítés sikertelen lesz. A rendszer az egyesítési művelet során megadott mezőkre cseréli a dokumentum meglévő mezőit. Ez `Collection(Edm.String)` típusú mezőket tartalmaz. Például, ha a dokumentum tartalmaz a mező "címkék" értékű `["budget"]` és értékkel egyesítésével végrehajtása `["economy", "pool"]` "címkék", a végső "címkék" mező értéke lesz `["economy", "pool"]`. A következőket hajtja végre **nem** kell `["budget", "economy", "pool"]`.
* `mergeOrUpload`: viselkedik `merge` Ha egy dokumentumot a megadott kulcs már létezik az index. Ha a dokumentum nem létezik, hasonlóan viselkedik `upload` egy új dokumentumot.
* `delete`: Törlés a megadott dokumentum eltávolítása az index. Vegye figyelembe, hogy minden mezőt megadja egy `delete` művelet a következő kulcsmező nem lesz figyelembe véve. Ha szeretne egy egyéni mező eltávolítása egy dokumentumot, `merge` helyette és egyszerűen állítsa be az a mező explicit módon a `null`.

**Válasz**

Állapotkód 200 (OK) visszaküldött a sikeres válasz, ami azt jelenti, hogy minden elem sikeresen indexelt. Ezt a `status` tulajdonság beállítása true, az összes, valamint a, a `statusCode` tulajdonság beállítása (az újonnan feltöltött dokumentumok) 201-et vagy a 200-as (az egyesített vagy törölt dokumentumok):

    {
      "value": [
        {
          "key": "unique_key_of_new_document",
          "status": true,
          "errorMessage": null,
          "statusCode": 201
        },
        {
          "key": "unique_key_of_merged_document",
          "status": true,
          "errorMessage": null,
          "statusCode": 200
        },
        {
          "key": "unique_key_of_deleted_document",
          "status": true,
          "errorMessage": null,
          "statusCode": 200
        }
      ]
    }  

Legalább egy tételt indexelése nem sikerült állapotkód 207 (több állapot) érték érkezett vissza. Elemek nem indexelt rendelkezik a `status` mező értéke hamis. A `errorMessage` és `statusCode` tulajdonságok jelzi az indexelési hiba oka:

    {
      "value": [
        {
          "key": "unique_key_of_document_1",
          "status": false,
          "errorMessage": "The search service is too busy to process this document. Please try again later.",
          "statusCode": 503
        },
        {
          "key": "unique_key_of_document_2",
          "status": false,
          "errorMessage": "Document not found.",
          "statusCode": 404
        },
        {
          "key": "unique_key_of_document_3",
          "status": false,
          "errorMessage": "Index is temporarily unavailable because it was updated with the 'allowIndexDowntime' flag set to 'true'. Please try again later.",
          "statusCode": 422
        }
      ]
    }  

Az alábbi táblázat ismerteti a különböző dokumentumra állapotkódokat adhatók vissza a válaszban. Vegye figyelembe, hogy néhány utalnak a kérelmet, míg mások jelzi, hogy ideiglenes hibaállapotok. Az utóbbi várakozás után érdemes újra megpróbálnia.

<table style="font-size:12">
    <tr>
        <th>Állapotkód</th>
        <th>Jelentése</th>
        <th>Újrapróbálkozást lehetővé tevő</th>
        <th>Megjegyzések</th>
    </tr>
    <tr>
        <td>200</td>
        <td>A dokumentum sikeresen módosították vagy törölték.</td>
        <td>n/a</td>
        <td>Törlési műveletek vannak <a href="https://en.wikipedia.org/wiki/Idempotence">idempotent</a>. Ez azt jelenti, hogy akkor is, ha egy dokumentum kulcs nem létezik az indexben, ezzel a kulccsal a delete művelet végrehajtásának kísérlete eredményez 200 állapotkódot.</td>
    </tr>
    <tr>
        <td>201</td>
        <td>A dokumentum létrehozása sikeresen megtörtént.</td>
        <td>n/a</td>
        <td></td>
    </tr>
    <tr>
        <td>400</td>
        <td>Hiba történt a dokumentum, amely megakadályozta a indexelt.</td>
        <td>Nem</td>
        <td>A hibaüzenet a válaszban szereplő jelenik meg, miért nem megfelelő, a dokumentumon.</td>
    </tr>
    <tr>
        <td>404</td>
        <td>A dokumentum nem egyesíthetők, mert a megadott kulcs nem létezik az index.</td>
        <td>Nem</td>
        <td>Ez a hiba nem történik meg a feltöltések óta hoznak létre új dokumentumokat, és ez nem történik meg a törlések ezek ugyanis <a href="https://en.wikipedia.org/wiki/Idempotence">idempotent</a>.</td>
    </tr>
    <tr>
        <td>409</td>
        <td>Verzióütközés volt észlelhető, a dokumentum index.</td>
        <td>Igen</td>
        <td>Ez akkor fordulhat elő, index egyszerre egynél többször egyazon dokumentumban való regisztráláskor.</td>
    </tr>
    <tr>
        <td>422</td>
        <td>Az index átmenetileg nem érhető el, mert a "allowIndexDowntime" jelzőjének beállítása "igaz" a frissítés.</td>
        <td>Igen</td>
        <td></td>
    </tr>
    <tr>
        <td>503</td>
        <td>A keresési szolgáltatás átmenetileg nem érhető el, valószínűleg a nagy terhelésnek.</td>
        <td>Igen</td>
        <td>A kód várnia kell, mielőtt megpróbálná ebben az esetben, vagy azzal kockáztatja a szolgáltatás elérhetetlensége meghosszabbítva.</td>
    </tr>
</table> 

**Megjegyzés:**: az Ügyfélkód gyakran észlel a 207 választ, ha egy lehetséges oka, hogy a rendszer terhelésnek van kitéve. Ez alapján ellenőrizheti a `statusCode` 503-as tulajdonsága. Ha ez a helyzet, ajánlott ***szabályozás indexelési***. Ellenkező esetben forgalom indexelő nem subside, ha a rendszer elindítása volt elutasító összes kérelmek 503-as hiba.

429. állapotkód: azt jelzi, hogy túllépte a kvótát a dokumentumok index másodpercenkénti száma. Hozzon létre egy új indexet vagy magasabb kapacitáskorlátait frissítése.

**Példa**

    {
      "value": [
        {
          "@search.action": "upload",
          "hotelId": "1",
          "baseRate": 199.0,
          "description": "Best hotel in town",
          "description_fr": "Meilleur hôtel en ville",
          "hotelName": "Fancy Stay",
          "category": "Luxury",
          "tags": ["pool", "view", "wifi", "concierge"],
          "parkingIncluded": false,
          "smokingAllowed": false,
          "lastRenovationDate": "2010-06-27T00:00:00Z",
          "rating": 5,
          "location": { "type": "Point", "coordinates": [-122.131577, 47.678581] }
        },
        {
          "@search.action": "upload",
          "hotelId": "2",
          "baseRate": 79.99,
          "description": "Cheapest hotel in town",
          "description_fr": "Hôtel le moins cher en ville",
          "hotelName": "Roach Motel",
          "category": "Budget",
          "tags": ["motel", "budget"],
          "parkingIncluded": true,
          "smokingAllowed": true,
          "lastRenovationDate": "1982-04-28T00:00:00Z",
          "rating": 1,
          "location": { "type": "Point", "coordinates": [-122.131577, 49.678581] }
        },
        {
          "@search.action": "merge",
          "hotelId": "3",
          "baseRate": 279.99,
          "description": "Surprisingly expensive",
          "lastRenovationDate": null
        },
        {
          "@search.action": "delete",
          "hotelId": "4"
        }
      ]
    }
- - -
<a name="SearchDocs"></a>

## <a name="search-documents"></a>Dokumentumok keresése
A **keresési** művelet GET vagy POST-kérelmet ad ki, és meghatározza, hogy adjon a feltételeknek megfelelő dokumentumok kiválasztásának paramétereket.

    GET https://[service name].search.windows.net/indexes/[index name]/docs?[query parameters]
    api-key: [admin or query key]

    POST https://[service name].search.windows.net/indexes/[index name]/docs/search?api-version=[api-version]
    Content-Type: application/json
    api-key: [admin or query key]

**Mikor érdemes használni a FELADÁS egy vagy több GET helyett**

Ha használ HTTP GET hívja a **keresési** API-t kell vegye figyelembe, hogy a kérelem URL-címe nem lehet hosszabb 8 KB-os. Ez elég általában a legtöbb alkalmazás esetén. Egyes alkalmazások azonban nagyon nagy lekérdezések vagy OData szűrőkifejezéseket eredményez. Ezek az alkalmazások a HTTP POST jobb választás, mivel használata esetén lehetővé teszi a nagyobb szűrőket és lekérdezéseket GET-nál. A FELADÁS egy vagy több a feltételek vagy a lekérdezésben záradékok a korlátozás, nem a nyers lekérdezéssel, mivel a POST kérelem méretkorlátja nagyjából 16 MB méretét.

> [!NOTE]
> Annak ellenére, hogy a POST kérelem méretkorlátot nagyon nagy, keresési lekérdezések és szűrőkifejezéseket önkényesen összetett nem lehet. Lásd: [Lucene lekérdezés szintaxisát](https://msdn.microsoft.com/library/mt589323.aspx) és [OData-kifejezésszintaxist](https://msdn.microsoft.com/library/dn798921.aspx) keresési lekérdezés és a szűrő összetettsége korlátozásaival kapcsolatos további információt.
> 
> 

**Kérés**

HTTPS szolgáltatáskérések szükség. A **keresési** kérelem a GET vagy POST metódusok használatával lehet létrehozni.

A kérelem URI-azonosítója a lekérdezéshez, minden dokumentumhoz, amelyek megfelelnek a paraméterek melyik index határozza meg. Paraméterek vannak megadva, a GET-kérések esetén a lekérdezési karakterláncot, és a kérés törzsében POST esetén kéri.

Ajánlott eljárásként GET kérelmek létrehozásakor, ne felejtse el [URL-encode](https://msdn.microsoft.com/library/system.uri.escapedatastring.aspx) meghatározott lekérdezési paraméterek közvetlenül a REST API hívásakor. A **keresési** műveleteket, ilyenek:

* `$filter`
* `facet`
* `highlightPreTag`
* `highlightPostTag`
* `search`
* `moreLikeThis`

A fenti lekérdezési paraméterek csak javasolt, URL-kódolást. Ha véletlenül URL-kódolására, a teljes lekérdezési karakterlánc (mindent után a?), kérelmek megszakad.

Emellett URL-kódolást csak akkor szükség a REST API használatával közvetlenül a GET hívásakor. Kódolás nélkül URL-cím szükség, ha hívása **keresési** használatával a FELADÁS egy vagy több, vagy használatakor a [.NET ügyféloldali kódtár](https://msdn.microsoft.com/library/dn951165.aspx), amely alkalmazás URL-címet meg.

<a name="SearchQueryParameters"></a>
**Lekérdezés-paraméterek**

**Keresési** lekérdezés feltételeket, és megadhatja a keresés viselkedését több paramétert fogad. Megadja, hogy ezek a paraméterek az URL-cím lekérdezési karakterlánc meghívásakor **keresési** GET keresztül, és a kérés törzsében meghívásakor JSON tulajdonságként **keresési** POST keresztül. Egyes paraméterek szintaxisa a következő némileg eltérő GET és POST között. Ezek a különbségek jeleztük megfelelő alatt:

`search=[string]`(nem kötelező) – a keresendő szöveget. Minden `searchable` mezőben keresni alapértelmezés szerint, kivéve, ha `searchFields` van megadva. Ha a Keresés `searchable` mezők, a keresési szöveget magát tokenekre, így több feltételek szóközt kell elválasztani (például: `search=hello world`). Bármely feltételek egyeznek, használja a `*` (Ez lehet hasznos, ha logikai szűrőlekérdezések). A paraméter kihagyása esetében ugyanaz az eredmény értékre állítaná, `*`. Lásd: [egyszerű lekérdezés szintaxisát](https://msdn.microsoft.com/library/dn798920.aspx) a a keresési szintaxist a részletekért.

* **Megjegyzés:**: az eredmények néha lehet a meglepő lekérdezését `searchable` mezőket. A jogkivonatokat létrehozó angol szöveg, például az aposztrófot, vesszővel válassza el egymástól a számok stb közös esetek kezelésére logikát tartalmaz. Például `search=123,456` fog egyezni a 123 és 456, az egyedi feltételeket, hanem egy egyetlen kifejezés 123,456 óta angolul sok ezer elválasztóként használhatók vesszővel válassza el egymástól. Ezért azt javasoljuk, központozási helyett szóköz külön feltételeit a `search` paraméter.

`searchMode=any|all`(nem kötelező, az alapértelmezett `any`) – akár a keresési kifejezés részének vagy egészének egyeztetni ahhoz, hogy a dokumentumot, amely száma.

`searchFields=[string]`(nem kötelező) – a megadott szöveg keresése vesszővel elválasztott mezők nevei a listában. Célmező jelölésűnek kell lennie `searchable`.

`queryType=simple|full`(nem kötelező, az alapértelmezett `simple`) – Ha egy egyszerű lekérdező nyelv, amely lehetővé teszi, hogy szimbólumok, mint "egyszerű" keresési szöveg beállítása értelmezi +, * és "". Lekérdezések kiértékelése az összes kereshető mezőt között (vagy mezők jelzett `searchFields`) alapértelmezés szerint minden a dokumentumban. Ha a lekérdezés típusa beállítása `full` a Lucene lekérdező nyelv, amely lehetővé teszi, hogy a mező-specifikus és súlyozott keresések keresett szöveg értelmezi. Lásd: [egyszerű lekérdezés szintaxisát](https://msdn.microsoft.com/library/dn798920.aspx) és [Lucene lekérdezés szintaxisát](https://msdn.microsoft.com/library/mt589323.aspx) a a keresési szintaxis a részletekért. 

> [!NOTE]
> Keresés a Lucene lekérdezési nyelv nem támogatott helyett $filter hasonló funkciókat kínál, amelyek a tartományba.
> 
> 

`moreLikeThis=[key]`(választható) **Fontos:** a szolgáltatás csak érhető el a `2015-02-28-Preview`. Ez a beállítás nem használható a szöveges keresési paramétert tartalmazó lekérdezés `search=[string]`. A `moreLikeThis` paraméter megkeresi a dokumentum kulcs által megadott dokumentum hasonló dokumentumokat. Ha a keresési kérelem rendelkező `moreLikeThis`, keresési feltételeinek jön létre, a gyakoriság és a dokumentum kifejezések ritkasága alapján. A feltételeket szolgálnak majd a kérést. Alapértelmezés szerint az összes `searchable` mezők minősülnek, kivéve, ha `searchFields` korlátozása, hogy melyik mezőkre történik a keresés segítségével.  

`$skip=#`(nem kötelező) – a keresési eredmények kihagyását; száma Nem lehet nagyobb, mint 100 000. Ha megvizsgálja a dokumentumok sorrendben kell, de nem használható `$skip` Ez a korlátozás miatt érdemes `$orderby` teljesen rendezett kulcshoz és `$filter` tartománnyal lekérdezés helyett.

> [!NOTE]
> Meghívásakor **keresési** POST használ, ez a paraméter neve `skip` helyett `$skip`.
> 
> 

`$top=#`(választható) - lekérdezni a keresési eredmények számát. Használható együtt `$skip` a keresési eredmények ügyféloldali lapozás megvalósításához.

> [!NOTE]
> Meghívásakor **keresési** POST használ, ez a paraméter neve `top` helyett `$top`.
> 
> 

`$count=true|false`(nem kötelező, az alapértelmezett `false`) – megadja, hogy a számuk eredményeinek beolvasása. Ez az a szám, amelyek megfelelnek az összes dokumentum a `search` és `$filter` paraméterek, a rendszer figyelmen kívül hagyja `$top` és `$skip`. Az érték `true` teljesítmény hatással lehet. Vegye figyelembe, hogy visszaadott számának közelítő.

> [!NOTE]
> Meghívásakor **keresési** POST használ, ez a paraméter neve `count` helyett `$count`.
> 
> 

`$orderby=[string]`(választható) - szűrje az eredményeket kifejezések vesszővel tagolt listája. Minden egyes kifejezés lehet, vagy egy mező nevét, vagy a hívás a `geo.distance()` függvény. Minden egyes kifejezés követhetnek `asc` jelezni a növekvő, és `desc` csökkenő jelzi. Az alapértelmezett növekvő sorrend megadásához. Ki fogja bontásban a dokumentumok egyezés eredményét. Ha nincs `$orderby` van megadva, az alapértelmezett rendezési sorrend szerint dokumentum egyezés pontszám van csökkenő. Egy legfeljebb 32 záradékok `$orderby`.

> [!NOTE]
> Meghívásakor **keresési** POST használ, ez a paraméter neve `orderby` helyett `$orderby`.
> 
> 

`$select=[string]`(választható) - vesszővel tagolt mezők beolvasásához listáját. Ha nincs megadva, az összes mezők megjelölve a séma szerint lekérhető szerepelnek. Ez a paraméter értékre állításával közvetlenül is kérhet minden mező `*`.

> [!NOTE]
> Meghívásakor **keresési** POST használ, ez a paraméter neve `select` helyett `$select`.
> 
> 

`facet=[string]`(nulla vagy több) - dimenzió által egy mezőt. Opcionálisan a karakterlánc tartalmazhat paramétereit, és testre szabhatja a vesszővel tagolt kifejezett értékkorlátozás `name:value` párokat. Érvényes paraméterek a következők:

* `count`(dimenzió feltételek; száma legfeljebb alapértelmezett érték 10). Nincs maximum van, de magasabb értékkel fel Önnek megfelelő teljesítményét, különösen akkor, ha a jellemzőalapú mező számos egyedi feltételeket tartalmaz.
  * Például: `facet=category,count:5` a legjobb öt kategóriába lekérdezi a dimenzió eredményei között.  
  * **Megjegyzés:**: Ha a `count` paraméter értéke kisebb, mint az egyedi feltételeket, az eredmények pontatlan lehet. A szilánkok értékkorlátozás lekérdezések elosztott módját okozza. Növekvő `count` általában növeli a feltételek száma, de teljesítmény költségekkel pontosságára.
* `sort`(egyik `count` rendezéséhez *csökkenő* szerinti `-count` rendezéséhez *növekvő* szerinti `value` rendezéséhez *növekvő* érték, vagy a `-value` rendezéséhez *csökkenő* érték)
  * Például: `facet=category,count:3,sort:count` lekérdezi a három legfontosabb kategóriák értékkorlátozás eredmények minden Város nevű dokumentumok száma szerint csökkenő sorrendben. Például ha a felső három kategóriába költségvetést, Motel és engedélyezhető, és költségvetési rendelkezik 5 találatok Motel 6 van, és engedélyezhető 4 rendelkezik, akkor a gyűjtők lesz sorrendben Motel, a költségvetési, engedélyezhető.
  * Például: `facet=rating,sort:-value` hozza létre a gyűjtőbe az összes lehetséges értékelések érték szerint csökkenő sorrendben. Például ha az összes minősítés 1-5, a gyűjtők lesz sorba rendezve 5, 4, 3, 2, 1, hány dokumentumok felel meg az összes minősítés függetlenül.
* `values`(cső tagolt numerikus vagy `Edm.DateTimeOffset` dimenzióértéknek bejegyzés az dinamikus csoportja értékek)
  * Például: `facet=baseRate,values:10|20` három gyűjtők eredményez: egy alap arányának 0, de nem például legfeljebb 10, 10 egyet, de nem tartoznak a 20 a, és egy a 20 vagy újabb verzióját.
  * Például: `facet=lastRenovationDate,values:2010-02-01T00:00:00Z` hoz létre két gyűjtők: egy a szállodák felújított 2010. február előtt, egy pedig a szállodákat felújított. február 1., 2010-es vagy újabb.
* `interval`(a számok, 0-nál nagyobb egész szám időköz vagy `minute`, `hour`, `day`, `week`, `month`, `quarter`, `year` idő értékek dátum)
  * Például: `facet=baseRate,interval:100` gyűjtők mérete 100 alap-címtartományok alapján hoz létre. Például ha base díjszabás összes $60 és a $600 közötti, lesz a 0 – 100, 100-200-as, 200 – 300, 300-400, 400-500 és 500-600 gyűjtők.
  * Például: `facet=lastRenovationDate,interval:year` hoz létre egy gyűjtő amikor szállodák volt felújított évente.
* `timeoffset`([+-] óó: pp, [+-] hhmm, vagy [+-] hh) `timeoffset` nem kötelező megadni. Csak kombinálható az a `interval` lehetőséget, és csak akkor, ha alkalmazva típusú mező `Edm.DateTimeOffset`. Az érték az UTC idő eltolódását fiókjára idő határok beállítása határozza meg.
  * Például: `facet=lastRenovationDate,interval:day,timeoffset:-01:00` használja, amely elindítja a nap határ 01:00:00 UTC (a cél időzónában éjfél)
* **Megjegyzés:**: `count` és `sort` kombinálható is a ugyanazon dimenzió megadását, de nem használható együtt `interval` vagy `values`, és `interval` és `values` együtt nem használható együtt.
* **Megjegyzés:**: dátum idő időköz értékkorlátozás arra az esetre vonatkoznak UTC idő alapján, ha `timeoffset` nincs megadva. Például: a `facet=lastRenovationDate,interval:day`, a nap határ 00:00:00 UTC kezdődik. 

> [!NOTE]
> Meghívásakor **keresési** POST használ, ez a paraméter neve `facets` helyett `facet`. Is akkor adja meg azt a JSON-tömb, ahol minden karakterlánca egy külön értékkorlátozás kifejezés karakterláncok.
> 
> 

`$filter=[string]`(nem kötelező) – standard OData szintaxisban strukturált keresőkifejezést.

> [!NOTE]
> Meghívásakor **keresési** POST használ, ez a paraméter neve `filter` helyett `$filter`.
> 
> 

`highlight=[string]`(választható) - készletként használt találat vesszővel elválasztott mezők nevei mutatja be. Csak `searchable` mezők találatok kiemelése is használható.

`highlightPreTag=[string]`(nem kötelező, az alapértelmezett `<em>`) – egy karakterlánc-címke, amely lefoglalja érni emeli ki. Meg kell a `highlightPostTag`.

> [!NOTE]
> Meghívásakor **keresési** GET használ, az URL-cím fenntartott karakterek százalék-kódolással kell (például % &#23; helyett).
> 
> 

`highlightPostTag=[string]`(nem kötelező, az alapértelmezett `</em>`)-karakterlánc címke, amely hozzáfűzi a találati emeli ki. Meg kell a `highlightPreTag`.

> [!NOTE]
> Meghívásakor **keresési** GET használ, az URL-cím fenntartott karakterek százalék-kódolással kell (például % &#23; helyett).
> 
> 

`scoringProfile=[string]`(választható) - kiértékelése a pontozási profil nevének egyeznie vonatkozó eredmények dokumentumok megfelelő ahhoz, hogy az eredmények rendezéséhez.

`scoringParameter=[string]`(nulla vagy több) – azt jelzi, az értékek pontozó függvények megadott mindegyik paraméterhez (például `referencePointParameter`) a következő formátumban `name-value1,value2,...`.

* Például, ha a pontozási profil határozza meg a függvény "mylocation" nevű paraméterrel a lekérdezési karakterláncának beállítására lenne `&scoringParameter=mylocation--122.2,44.8`. Az első dash neve elválasztja az listában, míg a második dash része az első értéket (a földrajzi hosszúság értéke ebben a példában).
* Pontozási paraméterek például címke kiemelése, vesszővel válassza el egymástól, tartalmazhat szimpla idézőjelben használatával lista bármely ilyen értéke karaktert. Az értékek maguk tartalmazhat félidézőjelet, is escape így dupla által.
  * Például ha egy címke "mytag" nevű paramétert kiemelése rendelkezik, és a címke növelése kívánt értékek "Hello, O'Brien" és "Smith", a lekérdezési karakterláncának beállítására lenne `&scoringParameter=mytag-'Hello, O''Brien',Smith`. Ne feledje, hogy ajánlatok csak szükséges értékeket tartalmazó, vesszővel válassza el egymástól.

> [!NOTE]
> Meghívásakor **keresési** POST használ, ez a paraméter neve `scoringParameters` helyett `scoringParameter`. Is, akkor adja meg azt a JSON-tömb, ahol minden karakterlánca külön karakterláncok `name-values` pár.
> 
> 

`minimumCoverage`(nem kötelező, az alapértelmezett érték 100) – 0 és 100 százalékos aránya az indexet, át kell gondolni ahhoz, hogy a lekérdezés kell jelenteni sikeres, a keresési lekérdezés jelző közötti szám. Alapértelmezés szerint a teljes index elérhetőnek kell lennie, vagy `Search` 503-as HTTP-állapotkód: ad vissza. Ha `minimumCoverage` és `Search` sikeres, térjen vissza a 200-as HTTP, és tartalmazzák a `@search.coverage` a válaszban az index a lekérdezésben szereplő százalékos értékét.

> [!NOTE]
> A paraméter értéke 100-nál kisebb search rendelkezésre állását, még a szolgáltatások csak egy replika azért hasznos lehet. Azonban nem minden egyező dokumentumok garantáltan a keresési eredmények között szerepel. Ha keresési visszaírási fontosabb az alkalmazáshoz, mint a rendelkezésre állási, akkor a legcélszerűbb hagyja `minimumCoverage` az alapértelmezett érték 100.
> 
> 

`api-version=[string]`(kötelező). Az előzetes verzió `api-version=2015-02-28-Preview`. Lásd: [keresési Service Versioning](http://msdn.microsoft.com/library/azure/dn864560.aspx) részletek és alternatív verzióit.

Megjegyzés: A művelet a `api-version` egy lekérdezési paraméter, függetlenül attól, hogy meghívja az URL-címben megadott **keresési** GET vagy POST.

**Kérelem fejlécei**

Az alábbi lista ismerteti a szükséges és választható kérelemfejlécekben.

* `api-key`: A `api-key` hitelesíteni a kérelmet a keresőszolgáltatása szolgál. Egy karakterláncértéket egyedi, a szolgáltatás URL-címre. A **keresési** kérelem adhat meg, egy adminisztrációs kulcsot vagy egy lekérdezés kulcs `api-key`.

Konfigurálnia kell a szolgáltatás nevét, a kérelem URL-címe összeállításához. Beszerezheti a szolgáltatás nevét és `api-key` a szolgáltatás irányítópultján, az Azure portálon. Lásd: [Azure Search szolgáltatás létrehozása a portálon](search-create-service-portal.md) navigációs segítségét.

**Kérés törzsében**

A GET: nincs.

A FELADÁS egy vagy több:

    {
      "count": true | false (default),
      "facets": [ "facet_expression_1", "facet_expression_2", ... ],
      "filter": "odata_filter_expression",
      "highlight": "highlight_field_1, highlight_field_2, ...",
      "highlightPreTag": "pre_tag",
      "highlightPostTag": "post_tag",
      "minimumCoverage": # (% of index that must be covered to declare query successful; default 100),
      "moreLikeThis": "document_key" (mutually exclusive with "search" parameter),
      "orderby": "orderby_expression",
      "scoringParameters": [ "scoring_parameter_1", "scoring_parameter_2", ... ],
      "scoringProfile": "scoring_profile_name",
      "search": "simple_query_expression",
      "searchFields": "field_name_1, field_name_2, ...",
      "searchMode": "any" (default) | "all",
      "select": "field_name_1, field_name_2, ...",
      "skip": # (default 0),
      "top": #
    }

**A részleges kereséshez válaszok folytatása**

Egyes esetekben Azure Search nem adhat vissza a kért eredmények egyetlen keresési válasz. Ez akkor fordulhat elő, például ha a lekérdezés kísérel meg túl sok dokumentumok nem ad meg a különböző okokból `$top` vagy értéket `$top` túl nagy. Ilyen esetben az Azure Search kiterjed a `@odata.nextLink` adott válasz törzsének megjegyzése és is `@search.nextPageParameters` Ha POST kérelem volt. Ezek a jegyzeteket értékek segítségével állítson össze egy másik keresési kérelem lekérni a keresési válasz a következő része. Ez a lehetőség egy ***folytatási*** az eredeti keresés kérelmet, és a jegyzetek általában nevezzük ***folytatási jogkivonatok***. Lásd: [az alábbi példában](#SearchResponse) talál részletes információt a szintaxist, ezek a jegyzeteket és hol szerepelnek a válasz törzsében. 

Miért Azure Search előfordulhat, hogy vissza a folytatási jogkivonatok okai függő és bármikor megváltozhat. Robusztus ügyfelek mindig esetekben, amikor a vártnál kevesebb dokumentumot ad vissza, és a folytatási kód megtalálható folytatni a dokumentumok kezeléséhez készen áll. Ne feledje, hogy ugyanazt a HTTP-metódus karakterként használjon az eredeti kérést folytatásához. Például ha egy GET kérelmet küldött, minden folytatási kérést küld kell használnia az GET (és hasonlóképpen POST).

<a name="SearchResponse"></a>
**Válasz**

Állapotkód: 200 OK visszaküldött a sikeres válasz.

    {
      "@odata.count": # (if $count=true was provided in the query),
      "@search.coverage": # (if minimumCoverage was provided in the query),
      "@search.facets": { (if faceting was specified in the query)
        "facet_field": [
          {
            "value": facet_entry_value (for non-range facets),
            "from": facet_entry_value (for range facets),
            "to": facet_entry_value (for range facets),
            "count": number_of_documents
          }
        ],
        ...
      },
      "@search.nextPageParameters": { (request body to fetch the next page of results if not all results could be returned in this response and Search was called with POST)
        "count": ... (value from request body if present),
        "facets": ... (value from request body if present),
        "filter": ... (value from request body if present),
        "highlight": ... (value from request body if present),
        "highlightPreTag": ... (value from request body if present),
        "highlightPostTag": ... (value from request body if present),
        "minimumCoverage": ... (value from request body if present),
        "moreLikeThis": ... (value from request body if present),
        "orderby": ... (value from request body if present),
        "scoringParameters": ... (value from request body if present),
        "scoringProfile": ... (value from request body if present),
        "search": ... (value from request body if present),
        "searchFields": ... (value from request body if present),
        "searchMode": ... (value from request body if present),
        "select": ... (value from request body if present),
        "skip": ... (page size plus value from request body if present),
        "top": ... (value from request body if present minus page size),
      },
      "value": [
        {
          "@search.score": document_score (if a text query was provided),
          "@search.highlights": {
            field_name: [ subset of text, ... ],
            ...
          },
          key_field_name: document_key,
          field_name: field_value (retrievable fields or specified projection),
          ...
        },
        ...
      ],
      "@odata.nextLink": (URL to fetch the next page of results if not all results could be returned in this response; Applies to both GET and POST)
    }

**Példák:**

A további példákat talál a [OData kifejezési szintaxist az Azure Search](https://msdn.microsoft.com/library/azure/dn798921.aspx) lap.

1)    Keresse meg a rendezve csökkenő dátum szerint.

    GET /indexes/hotels/docs? keresési = * & $orderby = lastRenovationDate desc & api-version = 2015-02-28 – előzetes verzió

    POST /indexes/hotels/docs/search? api-version = 2015-02-28-Preview {"search": "*", "orderby": "lastRenovationDate desc"}

2)    Keresse meg a jellemzőalapú keresés, és értékkorlátozás a kategóriák, minősítés, címkéket, valamint az adott címtartományok baseRate elemek beolvasása:

    GET /indexes/hotels/docs? keresési = test & értékkorlátozás = kategória & értékkorlátozás = minősítés & értékkorlátozás = címkék & értékkorlátozás baseRate, érték: 80 = |} 150 |} 220 & api-version = 2015-02-28 – előzetes verzió

    POST /indexes/hotels/docs/search? api-version = 2015-02-28-Preview {"search": "teszt", "értékkorlátozással": ["kategória", "minősítés", "címkék", "baseRate, érték: 80-as |} 150 |} 220"]}

3)    Az előző jellemzőalapú lekérdezés eredményei egy szűrő használatával szűkítéséhez, miután a felhasználó a hivatkozásra kattint 3. és a "Motel" kategóriát rangsorolási:

    GET /indexes/hotels/docs? keresési = test & értékkorlátozás = címkék & értékkorlátozás baseRate, érték: 80 = |} 150 |} 220 & $filter minősítés eq 3 és kategória-eq "Motel" & api-version = 2015-02-28 – előzetes verzió =

    POST /indexes/hotels/docs/search? api-version = 2015-02-28-Preview {"search": "teszt", "értékkorlátozással": ["címkék", "baseRate, érték: 80-as |} 150 |} 220"], "szűrő": "minősítés eq 3 és kategória-eq"Motel""}

4) Jellemzőalapú keresés – az egyedi feltételeket a lekérdezés által visszaadott állítson be egy felső korlátot. Az alapértelmezett érték 10, de növelheti vagy csökkentheti a érték használata a `count` paraméter a `facet` attribútum:

    GET /indexes/hotels/docs? keresési = test & értékkorlátozás = város, számláló: 5 & api-version = 2015-02-28 – előzetes verzió

    POST /indexes/hotels/docs/search? api-version = 2015-02-28-Preview {"search": "teszt", "értékkorlátozással": ["város, számláló: 5"]}

5)    Keresse meg a belül adott mezők; Ha például egy nyelvspecifikus mező:

    GET /indexes/hotels/docs? keresési = hôtel & searchFields = description_fr & api-version = 2015-02-28 – előzetes verzió

    POST /indexes/hotels/docs/search? api-version = 2015-02-28-Preview {"search": "hôtel", "searchFields": "description_fr"}

6) Keresse meg a több mező között. Például tárolja, és a lekérdezés több nyelven, ugyanazt az indexet belül az összes kereshető mezőt.  Ha angol és francia leírások üzemel, a dokumentumon, vagy azok a lekérdezés eredményében térhet vissza:

    GET /indexes/hotels/docs? keresési = Szálloda & searchFields = leírás, description_fr & api-version = 2015-02-28 – előzetes verzió

    POST /indexes/hotels/docs/search? api-version = 2015-02-28-Preview {"search": "Szálloda", "searchFields": "leírás, description_fr"}

Vegye figyelembe, hogy csak lekérdezhető egy index egyszerre. Ne hozzon létre az egyes nyelvekhez több index, kivéve, ha azt tervezi, hogy egyszerre csak egy lekérdezést.

7)    Lapozófájl - Get-elemek 1. oldal (oldal mérete 10):

    GET /indexes/hotels/docs? keresési = * & $skip = 0 & $top = 10 & api-version = 2015-02-28 – előzetes verzió

    POST /indexes/hotels/docs/search? api-version = 2015-02-28-Preview {"search": "*", "kihagyása": 0, a "top": 10}

8)    Lapozófájl - Get-elemek a 2. lap (oldal mérete 10):

    GET /indexes/hotels/docs? keresési = * & $skip = 10 & $top = 10 & api-version = 2015-02-28 – előzetes verzió

    POST /indexes/hotels/docs/search? api-version = 2015-02-28-Preview {"search": "*", "kihagyása": "felső" 10: 10}

9)    Lekéri egy adott készletét mezők:

    GET /indexes/hotels/docs? keresési = * & $select = hotelName, leírása és api-version = 2015-02-28 – előzetes verzió

    POST /indexes/hotels/docs/search? api-version = 2015-02-28-Preview {"search": "*", "select": "hotelName, leírás"}

10)  A megadott szűrő kifejezésnek megfelelő dokumentumok beolvasása:

    GET /indexes/hotels/docs? $filter = (baseRate ge 60 és baseRate lt 300) vagy a hotelName eq divatos maradnak, és az api-version = 2015-02-28 – előzetes verzió

    POST /indexes/hotels/docs/search? api-version = 2015-02-28-Preview {"szűrő": "(baseRate ge 60 és baseRate lt 300) hotelName eq"Divatos maradás"vagy"}

11) Találati emeli ki az indexet, és térjen vissza töredék keresése

    GET /indexes/hotels/docs? keresési valami = & Jelöljön ki leírás & api-version = 2015-02-28-Preview =

    POST /indexes/hotels/docs/search? api-version = 2015-02-28-Preview {"search": "egy", "highlight": "description"}

12) Keresse meg a, és térjen vissza a dokumentumok megtalálhatók-e el egy hivatkozási közelebb helyről rendezve

    GET /indexes/hotels/docs? keresési valami = & $orderby=geo.distance (helyét, geography'POINT(-122.12315 47.88121) ") & api-version = 2015-02-28 – előzetes verzió

    POST /indexes/hotels/docs/search? api-version = 2015-02-28-Preview {"search": "egy", "orderby": "geo.distance (helyét, geography'POINT(-122.12315 47.88121)") "}

13) Keresse meg a feltételezve, hogy a relevanciaprofil "földrajzi" nevű két a távolságot pontozó függvények a, egy meghatározása a paraméter neve "currentLocation", a másik egy paraméter "lastLocation" meghatározása

    GET /indexes/hotels/docs? keresési valami = & scoringProfile = földrajzi & scoringParameter currentLocation--122.123,44.77233 & scoringParameter = lastLocation--121.499,44.2113 & api-version = 2015-02-28-Preview =

    POST /indexes/hotels/docs/search? api-version = 2015-02-28-Preview {"search": "egy", "scoringProfile": "földrajzi", "scoringParameters": ["currentLocation--122.123,44.77233", "lastLocation--121.499,44.2113"]}

14) Dokumentumok keresése az index használatával [egyszerű lekérdezés szintaxisát](https://msdn.microsoft.com/library/dn798920.aspx). A lekérdezés által visszaadott, ha a kereshető mezők tartalmaznak a feltételek "megerősítő" és "hely", de nem a "motel" hotels:

    GET /indexes/hotels/docs? keresési = megerősítő + hely-motel & searchMode = all & api-version = 2015-02-28 – előzetes verzió

    POST /indexes/hotels/docs/search? api-version = 2015-02-28-Preview {"search": "megerősítő + hely-motel", "searchMode": "all"}

Vegye figyelembe a használatát `searchMode=all` felett. Ennek a paraméternek, beleértve felülbírálja az alapértelmezett `searchMode=any`, biztosítani kell, hogy `-motel` azt jelenti, hogy ", és nem" helyett ", vagy nincs". Nélkül `searchMode=all`, ", vagy nem", amely korlátozza a keresési eredmények helyett bővíti kap, és szeretne egyes felhasználóknak counter-intuitive is lehet.

15) Dokumentumok keresése az index használatával [lucene lekérdezés szintaxisát](https://msdn.microsoft.com/library/mt589323.aspx). A lekérdezés által visszaadott szállodák, ahol a kategória mező tartalma: a kifejezés "költségvetés" és "nemrég felújított" kifejezést tartalmazó összes kereshető mezőt. "Nemrég felújított" kifejezést tartalmazó dokumentumokat magasabb rangsora miatt a kifejezés program érték (3)

    GET /indexes/hotels/docs? keresési kategória: költségvetés = és \"nemrég felújított\"^ 3 & searchMode = all & api-version = 2015-02-28-Preview & querytype teljes =

    POST /indexes/hotels/docs/search? api-version = 2015-02-28-Preview {"search": "kategória: költségvetés és \"nemrég felújított\"^ 3", "queryType": "teljes", "searchMode": "all"}

<a name="LookupAPI"></a>

## <a name="lookup-document"></a>Keresés a dokumentum
A **keresési dokumentum** Azure keresési művelet lekérdezi egy dokumentumot. Ez akkor hasznos, ha a felhasználó egy adott keresési eredmény kattint, és keresse meg a dokumentum részletes adatait szeretné.

    GET https://[service name].search.windows.net/indexes/[index name]/docs/[key]?[query parameters]
    api-key: [admin or query key]

**Kérés**

HTTPS szolgáltatáskérések szükség. A **keresési dokumentum** kérelmek az alábbiak szerint lehet létrehozni.

    GET /indexes/[index name]/docs/key?[query parameters]

Másik lehetőségként használhatja a hagyományos OData-szintaxis kulcskeresési:

    GET /indexes('[index name]')/docs('[key]')?[query parameters]

A kérelem URI-azonosítója tartalmaz egy [index neve] és [kulcs], mely dokumentum melyik index lekérése megadása. Egyszerre csak egy dokumentum kérheti le. Használjon **keresési** egyetlen kérelem több dokumentum beolvasása.

**Lekérdezés-paraméterek**

`$select=[string]`(választható) - vesszővel tagolt mezők beolvasásához listáját. Ha nincs megadva vagy `*`, megjelölve az lekérhető a séma összes mezők szerepelnek a leképezés.

`api-version=[string]`(kötelező). Az előzetes verzió `api-version=2015-02-28-Preview`. Lásd: [keresési Service Versioning](http://msdn.microsoft.com/library/azure/dn864560.aspx) részletek és alternatív verzióit.

Megjegyzés: A művelet a `api-version` egy lekérdezési paraméter van megadva.

**Kérelem fejlécei**

Az alábbi lista ismerteti a szükséges és választható kérelemfejlécekben.

* `api-key`: A `api-key` hitelesíteni a kérelmet a keresőszolgáltatása szolgál. Egy karakterláncértéket egyedi, a szolgáltatás URL-címre. A **keresési dokumentum** kérelem adhat meg, egy adminisztrációs kulcsot vagy egy lekérdezés kulcs `api-key`.

Konfigurálnia kell a szolgáltatás nevét, a kérelem URL-címe összeállításához. Beszerezheti a szolgáltatás nevét és `api-key` a szolgáltatás irányítópultján, az Azure portálon. Lásd: [Azure Search szolgáltatás létrehozása a portálon](search-create-service-portal.md) navigációs segítségét.

**Kérés törzsében**

nincs.

**Válasz**

Állapotkód: 200 OK visszaküldött a sikeres válasz.

    {
      field_name: field_value (fields matching the default or specified projection)
    }

**Példa**

Keresés a dokumentum kulcs '2'

    GET /indexes/hotels/docs/2?api-version=2015-02-28-Preview

Keresés a dokumentum kulcs "3" OData-szintaxis használatával:

    GET /indexes('hotels')/docs('3')?api-version=2015-02-28-Preview

<a name="CountDocs"></a>

## <a name="count-documents"></a>A dokumentumok száma
A **száma dokumentumok** művelet lekérdezi egy keresési indexszel dokumentumok száma számát. A `$count` szintaxisa az OData protokoll egy részét.

    GET https://[service name].search.windows.net/indexes/[index name]/docs/$count?api-version=[api-version]
    Accept: text/plain
    api-key: [admin or query key]

**Kérés**

HTTPS szolgáltatáskérések szükség. A **száma dokumentumok** kérelem a GET metódussal lehet létrehozni.

A kérelem URI azonosítója [index neve] közli a szolgáltatást, hogy a megadott index docs gyűjteményének összes elemek számát adja vissza.

`api-version=[string]`(kötelező). Az előzetes verzió `api-version=2015-02-28-Preview`. Lásd: [keresési Service Versioning](http://msdn.microsoft.com/library/azure/dn864560.aspx) részletek és alternatív verzióit.

**Kérelem fejlécei**

Az alábbi lista ismerteti a szükséges és választható kérelemfejlécekben.

* `Accept`: Ezt az értéket kell beállítani `text/plain`.
* `api-key`: A `api-key` hitelesíteni a kérelmet a keresőszolgáltatása szolgál. Egy karakterláncértéket egyedi, a szolgáltatás URL-címre. A **száma dokumentumok** kérelem adhat meg, egy adminisztrációs kulcsot vagy egy lekérdezés kulcs `api-key`.

Konfigurálnia kell a szolgáltatás nevét, a kérelem URL-címe összeállításához. Beszerezheti a szolgáltatás nevét és `api-key` a szolgáltatás irányítópultján, az Azure portálon. Lásd: [Azure Search szolgáltatás létrehozása a portálon](search-create-service-portal.md) navigációs segítségét.

**Kérés törzsében**

nincs.

**Válasz**

Állapotkód: 200 OK visszaküldött a sikeres válasz.

A választörzs a számérték tartalmaz egy egyszerű szöveges formátumú egész számként.

<a name="Suggestions"></a>

## <a name="suggestions"></a>Javaslatok
A **javaslatok** művelet lekérdezi a javaslatok generálása részleges kereséshez bemeneti alapján. Általában szolgál a keresési mezőbe begépelt javaslatok adnia felhasználók írnak a keresési feltételeket.

Cél dokumentumok, arra vonatkozóan, így a javasolt szöveg megismételhető, ha több jelölt dokumentumot a bemeneti azonos keresési feltételeknek megfelelő kérelmek javaslat célja. Használhat `$select` beolvasása más dokumentum mező (a dokumentum kulcsot is beleértve), így állapítható meg, melyik dokumentum minden javaslat forrását.

A **javaslatok** művelet GET vagy POST-kérelmet ad ki.

    GET https://[service name].search.windows.net/indexes/[index name]/docs/suggest?[query parameters]
    api-key: [admin or query key]

    POST https://[service name].search.windows.net/indexes/[index name]/docs/suggest?api-version=[api-version]
    Content-Type: application/json
    api-key: [admin or query key]

**Mikor érdemes használni a FELADÁS egy vagy több GET helyett**

Ha használ HTTP GET hívja a **javaslatok** API-t kell vegye figyelembe, hogy a kérelem URL-címe nem lehet hosszabb 8 KB-os. Ez elég általában a legtöbb alkalmazás esetén. Egyes alkalmazások azonban nagyon nagy lekérdezések, kifejezetten OData szűrőkifejezéseket eredményez. Ezek az alkalmazások a HTTP POST jobb választás, mivel használata esetén lehetővé teszi a GET-nál nagyobb szűrők. A FELADÁS egy vagy több a szűrő záradékok a korlátozás, nem a nyers szűrési karakterláncot, mivel a POST kérelem méretkorlátja nagyjából 16 MB méretét.

> [!NOTE]
> Annak ellenére, hogy a POST kérelem méretkorlátot nagyon nagy, szűrőkifejezéseket önkényesen összetett lehet. Lásd: [OData-kifejezésszintaxist](https://msdn.microsoft.com/library/dn798921.aspx) szűrő összetettsége korlátozásaival kapcsolatos további információt.
> 
> 

**Kérés**

HTTPS szolgáltatáskérések szükség. A **javaslatok** kérelem a GET vagy POST metódusok használatával lehet létrehozni.

A kérelem URI-azonosítója a lekérdezés index neve. Paraméterek, például a részlegesen bemeneti keresési kifejezés van megadva a lekérdezési karakterlánc GET kérelmek esetében, és a kérés törzsében POST esetén kéri.

Ajánlott eljárásként GET kérelmek létrehozásakor, ne felejtse el [URL-encode](https://msdn.microsoft.com/library/system.uri.escapedatastring.aspx) meghatározott lekérdezési paraméterek közvetlenül a REST API hívásakor. A **javaslatok** műveleteket, ilyenek:

* `$filter`
* `highlightPreTag`
* `highlightPostTag`
* `search`

A fenti lekérdezési paraméterek csak javasolt, URL-kódolást. Ha véletlenül URL-kódolására, a teljes lekérdezési karakterlánc (mindent után a?), kérelmek megszakad.

Emellett URL-kódolást csak akkor szükség a REST API használatával közvetlenül a GET hívásakor. Kódolás nélkül URL-cím szükség, ha hívása **javaslatok** használatával a FELADÁS egy vagy több, vagy használatakor a [.NET ügyféloldali kódtár](https://msdn.microsoft.com/library/dn951165.aspx), amely alkalmazás URL-címet meg.

**Lekérdezés-paraméterek**

**Javaslatok** lekérdezés feltételeket, és megadhatja a keresés viselkedését több paramétert fogad. Megadja, hogy ezek a paraméterek az URL-cím lekérdezési karakterlánc meghívásakor **javaslatok** GET keresztül, és a kérés törzsében meghívásakor JSON tulajdonságként **javaslatok** POST keresztül. Egyes paraméterek szintaxisa a következő némileg eltérő GET és POST között. Ezek a különbségek jeleztük megfelelő alatt:

`search=[string]`– a keresett szöveg felajánlja a lekérdezések használatával. Legalább 1 karakter, és legfeljebb 100 karakter lehet.

`highlightPreTag=[string]`(választható) - karakterlánc címkékhez lefoglalja a találatok kereséséhez. Meg kell a `highlightPostTag`.

> [!NOTE]
> Meghívásakor **javaslatok** GET használ, az URL-cím fenntartott karakterek százalék-kódolással kell (például % &#23; helyett).
> 
> 

`highlightPostTag=[string]`(választható) - karakterlánc címkékhez hozzáfűzi a keresési találatok. Meg kell a `highlightPreTag`.

> [!NOTE]
> Meghívásakor **javaslatok** GET használ, az URL-cím fenntartott karakterek százalék-kódolással kell (például % &#23; helyett).
> 
> 

`suggesterName=[string]`-a javaslattevő nevét a `suggesters` gyűjteményt, amely része az index definícióját. A `suggester` meghatározza, hogy melyik mezőkre javasolt lekérdezési kifejezések vizsgálata. Lásd: [Javaslattevők](#Suggesters) részleteiről.

`fuzzy=[boolean]`(nem kötelező, alapértelmezett = hamis) – Ha értéke igaz, ez az API megkeresi javaslatok még akkor is, ha a keresett szöveg helyettesített vagy hiányzó karakter szerepel. Amíg ez bizonyos esetekben jobb élményt nyújt a teljesítményt, intelligens javaslat keresések lassabban futnak, és több erőforrást, származik.

`searchFields=[string]`(nem kötelező) – a megadott keresési szöveg keresése vesszővel elválasztott mezők nevei a listában. Célmező javaslatok engedélyezni kell.

`$top=#`(nem kötelező, alapértelmezett = 5)-javaslatok beolvasása száma. 1 és 100 közötti számnak kell lennie.

> [!NOTE]
> Meghívásakor **javaslatok** POST használ, ez a paraméter neve `top` helyett `$top`.
> 
> 

`$filter=[string]`(nem kötelező) – a dokumentumok szűrő kifejezés javaslatok figyelembe venni.

> [!NOTE]
> Meghívásakor **javaslatok** POST használ, ez a paraméter neve `filter` helyett `$filter`.
> 
> 

`$orderby=[string]`(választható) - szűrje az eredményeket kifejezések vesszővel tagolt listája. Minden egyes kifejezés lehet, vagy egy mező nevét, vagy a hívás a `geo.distance()` függvény. Minden egyes kifejezés követhetnek `asc` jelezni a növekvő, és `desc` csökkenő jelzi. Az alapértelmezett növekvő sorrend megadásához. Egy legfeljebb 32 záradékok `$orderby`.

> [!NOTE]
> Meghívásakor **javaslatok** POST használ, ez a paraméter neve `orderby` helyett `$orderby`.
> 
> 

`$select=[string]`(választható) - vesszővel tagolt mezők beolvasásához listáját. Ha nincs megadva, csak a dokumentum kulcs és javaslat szöveget ad vissza. Minden mező úgy, hogy ez a paraméter explicit módon kérhet `*`.

> [!NOTE]
> Meghívásakor **javaslatok** POST használ, ez a paraméter neve `select` helyett `$select`.
> 
> 

`minimumCoverage`(nem kötelező, az alapértelmezett érték a 80-as) – 0 és 100 százalékos aránya az indexet, át kell gondolni ahhoz, hogy a lekérdezés kell jelenteni sikeres, a javaslatok lekérdezés jelző közötti szám. Alapértelmezés szerint az index legalább 80 %-át elérhetőnek kell lennie, vagy `Suggest` 503-as HTTP-állapotkód: ad vissza. Ha `minimumCoverage` és `Suggest` sikeres, térjen vissza a 200-as HTTP, és tartalmazzák a `@search.coverage` a válaszban az index a lekérdezésben szereplő százalékos értékét.

> [!NOTE]
> A paraméter értéke 100-nál kisebb search rendelkezésre állását, még a szolgáltatások csak egy replika azért hasznos lehet. Azonban nem minden egyező javaslatok garantáltan szerepel az eredményeket. Ha visszaírási fontosabb az alkalmazáshoz, mint a rendelkezésre állási, akkor a legjobb, ha nem alacsonyabb `minimumCoverage` a 80-as alapértelmezett érték alá.
> 
> 

`api-version=[string]`(kötelező). Az előzetes verzió `api-version=2015-02-28-Preview`. Lásd: [keresési Service Versioning](http://msdn.microsoft.com/library/azure/dn864560.aspx) részletek és alternatív verzióit.

Megjegyzés: A művelet a `api-version` egy lekérdezési paraméter, függetlenül attól, hogy meghívja az URL-címben megadott **javaslatok** GET vagy POST.

**Kérelem fejlécei**

Az alábbi lista ismerteti a szükséges és választható kérelemfejléc

* `api-key`: A `api-key` hitelesíteni a kérelmet a keresőszolgáltatása szolgál. Egy karakterláncértéket egyedi, a szolgáltatás URL-címre. A **javaslatok** kérelem adhat meg, egy adminisztrációs kulcsot vagy egy lekérdezés kulcsot a `api-key`.

Konfigurálnia kell a szolgáltatás nevét, a kérelem URL-címe összeállításához. Beszerezheti a szolgáltatás nevét és `api-key` a szolgáltatás irányítópultján, az Azure portálon. Lásd: [Azure Search szolgáltatás létrehozása a portálon](search-create-service-portal.md) navigációs segítségét.

**Kérés törzsében**

A GET: nincs.

A FELADÁS egy vagy több:

    {
      "filter": "odata_filter_expression",
      "fuzzy": true | false (default),
      "highlightPreTag": "pre_tag",
      "highlightPostTag": "post_tag",
      "minimumCoverage": # (% of index that must be covered to declare query successful; default 80),
      "orderby": "orderby_expression",
      "search": "partial_search_input",
      "searchFields": "field_name_1, field_name_2, ...",
      "select": "field_name_1, field_name_2, ...",
      "suggesterName": "suggester_name",
      "top": # (default 5)
    }

**Válasz**

Állapotkód: 200 OK visszaküldött a sikeres válasz.

    {
      "@search.coverage": # (if minimumCoverage was provided in the query),
      "value": [
        {
          "@search.text": "...",
          "<key field>": "..."
        },
        ...
      ]
    }

Ha a leképezés beállítás segítségével kérhető le a mezők szerepelnek a tömb egyes elemei:

    {
      "@search.coverage": # (if minimumCoverage was provided in the query),
      "value": [
        {
          "@search.text": "...",
          "<key field>": "..."
          <other projected data fields>
        },
        ...
      ]
    }

**Példa**

Ahol a részleges kereséshez bemeneti az "lux" 5 javaslatok beolvasása

    GET /indexes/hotels/docs/suggest?search=lux&$top=5&suggesterName=sg&api-version=2015-02-28-Preview

    POST /indexes/hotels/docs/suggest?api-version=2015-02-28-Preview
    {
      "search": "lux",
      "top": 5,
      "suggesterName": "sg"
    }
