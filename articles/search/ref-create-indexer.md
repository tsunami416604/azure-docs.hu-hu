---
title: Hozzon létre indexelőt (az Azure Search szolgáltatás REST api-version = 2017-11-11 – előzetes verzió)
description: Az előnézetben API indexelők vannak terjeszteni outputFieldMapping paraméterek dúsító kimeneti leképezhető egy mező az Azure Search-index.
services: search
manager: pablocas
author: luiscabrer
ms.author: luisca
ms.date: 05/01/2018
ms.prod: azure
ms.service: search
ms.devlang: rest-api
ms.workload: search
ms.topic: language-reference
ms.openlocfilehash: 595502ecf0a78491c73800e8077de65707c7a486
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/20/2018
---
# <a name="create-indexer-azure-search-service-rest-api-version2017-11-11-preview"></a>Hozzon létre indexelőt (az Azure Search szolgáltatás REST api-version = 2017-11-11 – előzetes verzió)

Egy a dokumentációt, preview-specifikus verziója telepítve az API-hivatkozás hozzáadása [kognitív keresési](cognitive-search-concept-intro.md) az indexelő létrehozása API elemek. Például a [általánosan elérhető](https://docs.microsoft.com/rest/api/searchservice/create-indexer) verzió, létrehozhat egy új indexelő belül az Azure Search szolgáltatást használja a HTTP POST-kérelmet. 

```http
POST https://[service name].search.windows.net/indexers?api-version=2017-11-11-Preview
    Content-Type: application/json  
    api-key: [admin key]  
```  
A **api-kulcs** egy adminisztrációs kulcsot (szemben a lekérdezési kulcs) kell lennie. A hitelesítési részében [az Azure Search biztonsági](search-security-overview.md) kulcsok tájékozódhat. [Azure Search szolgáltatás létrehozása a portálon](search-create-service-portal.md) a szolgáltatás URL-címe és a kérésben használt tulajdonságait ismerteti.

Azt is megteheti PUT használja, és adja meg az adatforrás neve URI-n. Ha az adatforrás nem létezik, a rendszer létrehozza.  

```http
PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=[api-version]  
```  
A **api-version** szükséges. Az aktuális általánosan rendelkezésre álló verziója `api-version=2017-11-11`, de kognitív keresési szüksége az előzetes verzióra: `api-version=2017-11-11-Preview`.  Lásd: [az Azure Search API-verziók](search-api-versions.md) részleteiről.

Adatok-platform-specifikus indexelők létrehozásával útmutatásért kezdődnie [indexelők áttekintése](search-indexer-overview.md), mely tartalmazza a teljes listáját [kapcsolódó cikkekben](search-indexer-overview.md#next-steps).

> [!NOTE]  
>  IP-címek függ az indexelők engedélyezett maximális számát. Az ingyenes szolgáltatás lehetővé teszi, hogy a 3 indexelők. Standard szolgáltatás lehetővé teszi, hogy 50 indexelők. Szabványos nagy felbontású szolgáltatások nem támogatják az indexelő minden. Lásd: [szolgáltatásra vonatkozó korlátozások](search-limits-quotas-capacity.md) részleteiről.    

## <a name="request"></a>Kérés  

A [adatforrás](https://docs.microsoft.com/rest/api/searchservice/create-data-source), [index](https://docs.microsoft.com/rest/api/searchservice/create-index), és [skillset](ref-create-skillset.md) részei egy [indexelő](search-indexer-overview.md) definition, de egyes nem használható egy független összetevő különböző kombinációkban. Például használhatja ugyanerre az adatforrásra, több indexelők, vagy több indexelők, vagy egy index írása több indexelők ugyanazt az indexet.

 A kérelem törzse egy indexelő-definíció, az alábbi részeket tartalmazza.

+ [dataSourceName](#dataSourceName)
+ [targetIndexName](#targetIndexName)
+ [skillsetName](#skillset)
+ [schedule](#indexer-schedule)
+ [paraméterek](#indexer-parameters)
+ [fieldMappings](#field-mappings)
+ [outputFieldMappings](#output-fieldmappings)

## <a name="request-syntax"></a>Szintaxis

A kérelem hasznos rendszerezésére szolgál szintaxisa a következő. A cikk egy minta kérelem valósul meg.  

```json
{   
    "name" : "Required for POST, optional for PUT. The name of the indexer",  
    "description" : "Optional. Anything you want, or null",  
    "dataSourceName" : "Required. The name of an existing data source",  
    "targetIndexName" : "Required. The name of an existing index",  
    "skillsetName" : "Required for cognitive search enrichment",
    "schedule" : { Optional, but immediately runs once if unspecified. See Indexing Schedule below. },  
    "parameters" : { Optional. See Indexing Parameters below. },  
    "fieldMappings" : { Optional. See fieldMappings below. },
    "outputFieldMappings" : { Required for enrichment pipelines. See outputFieldMappings below. },
    "disabled" : Optional boolean value indicating whether the indexer is disabled. False by default.
}  
```
<a name="dataSourceName"></a>

### <a name="datasourcename"></a>"dataSourceName"

A [adatforrás-definíció](https://docs.microsoft.com/rest/api/searchservice/create-data-source) gyakran tartalmaz, amelyek használatával az indexelő kihasználni a forrás platform jellemzők tulajdonságai. Ilyen az indexelőnek át az adatforrás meghatározza, hogy az egyes tulajdonságok és a paraméterek, a rendelkezésre állási ilyen tartalomtípus az Azure SQL Database az Azure-blobokat vagy a lekérdezés időkorlátja szűrés. 

<a name="targetIndexName"></a>

### <a name="targetindexname"></a>"targetIndexName"

Egy [a sémát indexeli](https://docs.microsoft.com/rest/api/searchservice/create-index) határozza meg a kereshető, szűrhető, lekérhető és más adatok, amelyek meghatározzák, hogyan használja a mezőt tartalmazó mezők gyűjteményében. Indexelő, az indexelő bejárja az adatforrás, opcionálisan repedések dokumentumok és információkat, rendezi sorba az eredményeket a JSON és a indexeli a tartalom a séma definiálva az index alapján.

<a name="skillset"></a>

### <a name="skillsetname"></a>"skillsetName"

[Kognitív keresési (előzetes verzió)](cognitive-search-concept-intro.md) természetes nyelvű és képességek az Azure Search adatfeldolgozást az entitásokat, a legfontosabb kifejezések, a nyelv, a információk kinyerése lemezképeket, és így tovább során alkalmazott feldolgozása lemezkép hivatkozik. Tartalom alkalmazott transzformáció van keresztül *ismeretek*, amelyek mindegyikében egyetlen [ *skillset*](ref-create-skillset.md), egy indexelő egy. Az adatforrások és indexek, egy skillset egy független csatol az indexelő összetevő. Egy skillset más indexelők is újból használni, de a minden indexelő csak egy skillset egyszerre.
 
<a name="indexer-schedule"></a>

### <a name="schedule"></a>"ütemezés"  
Az indexelő opcionálisan megadható ütemezés szerint. Egy ütemezés nélkül az indexelő futása azonnal amikor elküldi a kérelmet: kapcsolódás, bejárását és az adatforrás indexelő. Bizonyos esetekben, beleértve az indexelő hosszan futó feladatokat, ütemezés esetén használt [kiterjesztése a feldolgozási időszakában](search-howto-reindex.md#scale-out-indexing) meghaladja a legfeljebb 24 órás. Ha ütemezés szerint meg adva, az indexelő rendszeres ütemezés szerint futtatja. Az ütemező részét; egy külső ütemező nem használható. A **ütemezés** az alábbi attribútumok tartoznak: 

-   **időköz**: kötelező. Egy időtartamértéket, amely megadja az időközt vagy az indexelő időszak fut. A legkisebb megengedett intervallum értéke öt perc; a leghosszabb egy nap. Az XSD "daytimeduration típusú" értéknek kell formázni (korlátozott részhalmaza egy [ISO 8601 időtartama](http://www.w3.org/TR/xmlschema11-2/#dayTimeDuration) érték). A minta: `"P[nD][T[nH][nM]]".` példák: `PT15M` a 15 percenként `PT2H` minden 2 órán át.  

-   **startTime**: nem kötelező. Az indexelő kell elindultak, amikor egy UTC datetime.  

<a name="indexer-parameters"></a>

### <a name="parameters"></a>"paraméterek"

Az indexelő opcionálisan eltarthat futtatási viselkedés módosítása konfigurációs paramétereket. Konfigurációs paraméterei vesszővel tagolt az indexelő kérésre. 

```json
    {
      "name" : "my-blob-indexer-for-cognitive-search",
      ... other indexer properties
      "parameters" : { "maxFailedItems" : "15", "batchSize" : "100", "configuration" : { "parsingMode" : "json", "indexedFileNameExtensions" : ".json, .jpg, .png", "imageAction" : "generateNormalizedImages", "dataToExtract" : "contentAndMetadata" } }
    }
```

#### <a name="general-parameters-for-all-indexers"></a>Összes indexelő általános paraméterei

| Paraméter | Típus és a megengedett értékek   | Használat  |
|-----------|------------|--------------------------|--------|
| `"batchSize"` | Egész szám<br/>Alapértelmezett érték a forrás-specifikus (Azure SQL Database és Azure Cosmos DB, 10, az Azure Blob Storage az 1000) | Adja meg az elemek száma, amelyek az adatforrásból beolvasása és indexelt a kötegek teljesítményének javítása érdekében. |
| `"maxFailedItems"` | Egész szám<br/>Az alapértelmezett érték: 0 | Mielőtt egy indexelő futtatása tekinthető hiba tűrését hibák száma. Ha nem szeretné, hogy az indexelő megszakításához hibáit értéke -1. Nem sikerült az elemekről információ kérheti le [indexelő állapotának beolvasása](https://docs.microsoft.com/rest/api/searchservice/get-indexer-status).  |
| `"maxFailedItemsPerBatch"` | Egész szám<br/>Az alapértelmezett érték: 0 | Az egyes kötegekben, az indexelő futtatása előtt tűrését hibák száma van tekintve. Ha nem szeretné, hogy az indexelő megszakításához hibáit értéke -1. |

#### <a name="blob-configuration-parameters"></a>A BLOB-konfigurációs paraméterek

Számos paraméter kizárólagosak egy adott indexelőnek, például a [Azure blobindexelés](search-howto-indexing-azure-blob-storage.md).

| Paraméter | Típus és a megengedett értékek   | Használat  |
|-----------|---------------------------|--------|
| `"parsingMode"` | Karakterlánc<br/>`"text"`<br/>`"delimitedText"`<br/>`"json"`<br/>`"jsonArray"`  | A [Azure-blobok](search-howto-indexing-azure-blob-storage.md), beállíthatja, hogy `text` egyszerű szöveges fájlok, a blob Storage tárolóban az indexelési teljesítmény javítása érdekében. <br/>A [CSV blobok](search-howto-index-csv-blobs.md), beállíthatja, hogy `delimitedText` amikor blobok olyan egyszerű CSV-fájlok. <br/>A [JSON-blobok](search-howto-index-json-blobs.md)kell állítani, `json` strukturált kivonat tartalomhoz, vagy `jsonArray` (előzetes verzió) kibontásához a tömb egyes elemei az Azure Search különálló dokumentumként. |
| `"excludedFileNameExtensions"` | Karakterlánc<br/>vesszővel tagolt listája | A [Azure-blobok](search-howto-indexing-azure-blob-storage.md), figyelmen kívül hagyja a fájltípusok listájában. Például ".png, .png, .mp4" kizárása sikerült kihagyhatja ezeket a fájlokat az indexelés során. | 
| `"indexedFileNameExtensions"` | Karakterlánc<br/>vesszővel tagolt listája | A [Azure-blobok](search-howto-indexing-azure-blob-storage.md), blobok választja ki, ha a fájl kiterjesztése szerepel a listában. Például akkor lehetett összpontosítson, az adott alkalmazás indexelő ".docx, .pptx, .msg" fájlok kifejezetten tartalmazza az adott fájltípus esetében. | 
| `"failOnUnsupportedContentType"` | true <br/>Hamis (alapértelmezés) | A [Azure-blobok](search-howto-indexing-azure-blob-storage.md), beállíthatja, hogy `false` Ha szeretné-e a rendszer észlelt egy nem támogatott tartalomtípus, és minden tartalomtípusokat (fájlkiterjesztések) előzetesen nem tudja folytatni. |
| `"failOnUnprocessableDocument"` | true <br/>Hamis (alapértelmezés)| A [Azure-blobok](search-howto-indexing-azure-blob-storage.md), beállíthatja, hogy `false` Ha folytatja a indexelő, ha egy dokumentum sikertelen indexelő. |
| `"indexStorageMetadataOnlyForOversizedDocuments"` | true <br/>Hamis (alapértelmezés)| A [Azure-blobok](search-howto-indexing-azure-blob-storage.md), ez a tulajdonság beállítása `true` továbbra is a blob tartalma túl nagy feldolgozni a tároló metaadatait indexelésre.  Hibák túlméretes blobok alapértelmezés szerint kell kezelni. A blob mérete korlátok, lásd: [szolgáltatásra vonatkozó korlátozások](search-limits-quotas-capacity.md). |
| `"delimitedTextHeaders"` | Karakterlánc<br/>vesszővel tagolt listája| A [CSV blobok (előzetes verzió)](search-howto-index-csv-blobs.md), oszlopfejlécek, hasznos, ha a forrás mezők leképezése a cél mezők index vesszővel tagolt listáját adja meg. |
| `"delimitedTextDelimiter"` | Karakterlánc<br/>Felhasználó által definiált | A [CSV blobok (előzetes verzió)](search-howto-index-csv-blobs.md), adja meg a sor végén elválasztó, amelyben soronként elindul egy új dokumentumot CSV-fájlok (például ""|"`).  |
| `"firstLineContainsHeaders"` | Igaz (alapértelmezett) <br/>false | A [CSV blobok (előzetes verzió)](search-howto-index-csv-blobs.md), azt jelzi, hogy az első sort (kötelező) minden egyes blob tartalmaz-e a fejléceket.|
| `"documentRoot"`  | Karakterlánc<br/>Felhasználó által definiált | A [JSON-tömbök (előzetes verzió)](search-howto-index-json-blobs.md#nested-json-arrays), megadva a strukturált vagy félig strukturált dokumentum, egy elérési utat is megadhat a tömbhöz e tulajdonság használatával. |
| `"dataToExtract"` | Karakterlánc<br/>`"storageMetadata"`<br/>`"allMetadata"`<br/>`"contentAndMetadata"` (alapértelmezett) | A [Azure-blobok](search-howto-indexing-azure-blob-storage.md):<br/>Beállítása `"storageMetadata"` indexek csak a [szabványos blob tulajdonságait és a felhasználó által megadott metaadatok](../storage/blobs/storage-properties-metadata.md). <br/>Beállítása `"allMetadata"` kinyerni az Azure blob storage-alrendszere által megadott metaadatok és a [tartalomtípus adott metaadatokat](search-howto-indexing-azure-blob-storage.md#ContentSpecificMetadata) (például metaadatok egyedi csak .png fájlok) indexelt. <br/>Beállítása `"contentAndMetadata"` metaadatok és a szöveges tartalom kibontani minden egyes blob. <br/><br/>A [lemezkép-elemzés a kognitív keresési (előzetes verzió)](cognitive-search-concept-image-scenarios.md), amikor `"imageAction"` értéke `"generateNormalizedImages"`, a `"dataToExtract"` beállítás arról értesíti az indexelő, mely adatok kinyerése a kép tartalmához. Beágyazott kép tartalmának vonatkozik egy. PDF- vagy más alkalmazás vagy képfájlok például .jpg és .png, az Azure-blobokat.  |
| `"imageAction"` |  Karakterlánc<br/>`"none"`<br/>`"generateNormalizedImages"` | A [Azure-blobok](search-howto-indexing-azure-blob-storage.md), beállíthatja, hogy`"none"` beágyazott képek vagy képfájlok adatkészlet figyelmen kívül hagyja. Ez az alapértelmezett beállítás. <br/><br/>A [lemezkép-elemzés a kognitív keresési](cognitive-search-concept-image-scenarios.md), beállíthatja, hogy`"generateNormalizedImages"` szöveg kinyerése képek (például a word "stop" a a forgalom Stop bejelentkezési), és beágyazása a content mezőjének részeként. Kép elemzés során az indexelő hoz létre egy tömb normalizált képek dokumentum repedés részeként, és beágyazza a generált információ a tartalom mezőbe. Ez a művelet megköveteli, hogy `"dataToExtract"` értéke `"contentAndMetadata"`. A normalizált lemezkép egységes kép kimeneti, méretű és forgatni egységes megjelenítést lépteti elő, ha képek visual keresési eredmények között, ami további feldolgozás hivatkozik (például azonos méretűnek fényképek egy grafikonon szabályozhatja a látható[JFK bemutató](https://github.com/Microsoft/AzureSearch_JFK_Files)). Ezt az információt az egyes lemezképek jön létre, amikor használata |


#### <a name="other-configuration-parameters"></a>Egyéb konfigurációs paramétert is

A következő paraméterek az Azure SQL Database vonatkoznak.

| Paraméter | Típus és a megengedett értékek   | Használat  |
|-----------|---------------------------|--------|
| `"queryTimeout"` | Karakterlánc<br/>"óó: pp:"<br/>"00: 05:00"| A [Azure SQL Database](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md), használja ezt a paramétert, 5 perces alapértelmezett túl az időtúllépési érték növeléséhez.|

<a name="field-mappings"></a>

### <a name="fieldmappings"></a>"fieldMappings"

Az indexelő definíciók mező társítások mező leképezése egy mezőt az Azure Search-index tartalmaz. Attól függően, hogy a tartalom átvitele követi közvetlen vagy bővített elérési társítások két típusa van:

+ **fieldMappings** nem kötelező, ha a forrás-cél mező nevei nem egyeznek, vagy ha szeretne megadni egy függvényt alkalmazza.
+ **outputFieldMappings** szükség, ha az Ön által létrehozott [egy dúsító adatcsatorna](cognitive-search-concept-intro.md). Egy dúsító sorban a kimeneti mező egy olyan konstrukció, a dúsító során meghatározott. A kimeneti mező Előfordulhat például, a dokumentum két külön mezői alapján dúsító során beépített összetett struktúrában. 

A következő példának, vegye fontolóra a forrástábla mezőt `_id`. Az Azure Search nem engedélyezi a mező kell átnevezni, aláhúzásjelet, kezdve mező nevét. Ehhez használja a `fieldMappings` tulajdonsága az indexelő az alábbiak szerint:

```json
"fieldMappings" : [ { "sourceFieldName" : "_id", "targetFieldName" : "id" } ]
```

Több mező leképezéseket adhat meg:

```json
"fieldMappings" : [
    { "sourceFieldName" : "_id", "targetFieldName" : "id" },
    { "sourceFieldName" : "_timestamp", "targetFieldName" : "timestamp" }
]
```

A forrás- és cél mezőnevek nem különböztetik meg.

Ahol mező hozzárendelések hasznosak forgatókönyvekkel kapcsolatos további tudnivalókért lásd: [keresési indexelő mező hozzárendelések](https://docs.microsoft.com/azure/search/search-indexer-field-mappings).

<a name="output-fieldmappings"></a>

### <a name="outputfieldmappings"></a>"outputFieldMappings"

A [kognitív keresési](cognitive-search-concept-intro.md) forgatókönyvek, amelyben egy skillset az indexelő van kötve, hozzá kell adnia `outputFieldMappings` rendelje hozzá a kimenetet egy dúsító lépés, amely az index kereshető mező tartalmát.

```json
  "outputFieldMappings" : [
        {
          "sourceFieldName" : "/document/organizations", 
          "targetFieldName" : "organizations"
        },
        {
          "sourceFieldName" : "/document/pages/*/keyPhrases/*", 
          "targetFieldName" : "keyphrases"
        },
        {
            "sourceFieldName": "/document/languageCode",
            "targetFieldName": "language",
            "mappingFunction": null
        }      
   ],
```

<a name="FieldMappingFunctions"></a>

### <a name="field-mapping-functions"></a>A mező leképezési funkciók

Mező hozzárendelések is használható forrás mező értékét átalakítására *leképezési funkciók mezőben*. Például egy tetszőleges karakterlánc lehet base64-kódolású így egy dokumentum kulcsmező feltöltéséhez használható.

Mikor és hogyan mező leképezési funkciók kapcsolatos további információkért lásd: [mező leképezési funkciók](https://docs.microsoft.com/azure/search/search-indexer-field-mappings#field-mapping-functions).

## <a name="request-examples"></a>Példák  
 Az első példában létrehozza indexelőt, amely által hivatkozott tábla adatait átmásolja a `ordersds` a forrás a `orders` index ütemezés szerint 2015 jan. 1 UTC elindul, és óránként futtat. Minden indexelő meghívás sikeres, ha 5-nél több elemek indexelése meghiúsulhat, ha az egyes kötegekben, és legfeljebb 10 elemet indexelése meghiúsulhat, ha összesen.  

```json
{
    "name" : "myindexer",  
    "description" : "a cool indexer",  
    "dataSourceName" : "ordersds",  
    "targetIndexName" : "orders",  
    "schedule" : { "interval" : "PT1H", "startTime" : "2018-01-01T00:00:00Z" },  
    "parameters" : { "maxFailedItems" : 10, "maxFailedItemsPerBatch" : 5 }  
}
```

A második példáját mutatja be egy kognitív keresési művelet, a hivatkozás egy skillset által jelzett és [outputFieldMappings](#output-fieldmappings). [Skillsets](ref-create-skillset.md) magas szintű erőforrás, külön definiált. Ez a példa rövidítése az indexelő meghatározása a [kognitív keresési oktatóanyag](cognitive-search-tutorial-blob.md).

```json
{
  "name":"demoindexer", 
  "dataSourceName" : "demodata",
  "targetIndexName" : "demoindex",
  "skillsetName" : "demoskillset",
  "fieldMappings" : [
    {
        "sourceFieldName" : "content",
        "targetFieldName" : "content"
    }
   ],
  "outputFieldMappings" : 
  [
    {
        "sourceFieldName" : "/document/organizations", 
        "targetFieldName" : "organizations"
    },
  ],
  "parameters":
  {
    "maxFailedItems":-1,
    "configuration": 
    {
    "dataToExtract": "contentAndMetadata",
    "imageAction": "generateNormalizedImages"
    }
  }
}
```

## <a name="response"></a>Válasz  
 A kérelem sikeres 201 létrehozva.  

## <a name="see-also"></a>Lásd még

+ [Az indexelő áttekintése](search-indexer-overview.md)
+ [Kognitív keresési áttekintése](cognitive-search-concept-intro.md)
+ [Gyors üzembe helyezés: Próbálja kognitív keresése](cognitive-search-quickstart-blob.md)
+ [Hogyan mezők](cognitive-search-output-field-mapping.md)
