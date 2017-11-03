---
title: "Az Azure felhőalapú tárolást félig strukturált adatok keresése"
description: "A keresés blob félig strukturált adatok Azure Search használatával."
author: roygara
manager: timlt
ms.service: search
ms.topic: tutorial
ms.date: 10/12/2017
ms.author: v-rogara
ms.custom: mvc
ms.openlocfilehash: ea57fa35f09299f95cdfd3c11b44657d35972295
ms.sourcegitcommit: e6029b2994fa5ba82d0ac72b264879c3484e3dd0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/24/2017
---
# <a name="search-semi-structured-data-in-cloud-storage"></a>Keresési félig strukturált adatok felhőben

Ezen kétrészes oktatóanyag sorozat megismerheti, hogyan használja az Azure search félig strukturált és strukturálatlan adatok kereséséhez. Ez az oktatóanyag bemutatja, hogyan kereshet félig strukturált adatok, például JSON tárolódnak az Azure-blobokat. Félig strukturált adatok címkéket és az adatok tartalmakra, amelyek jeleinek tartalmazza. Strukturált adatok abban, hogy nem hivatalosan szerkezete a adatmodell, például egy relációs adatbázis-séma abban különbözik.

Ebben a részben azt fedezi hogyan:

> [!div class="checklist"]
> * Hozzon létre és tölthet fel indexet belül az Azure Search szolgáltatást
> * Az index az Azure Search szolgáltatás segítségével

> [!NOTE]
> "A JSON-tömb támogatás az Azure Search előzetes verziójú funkciók nem. Nincs jelenleg elérhető a portálon. Emiatt használunk az előzetes REST API-t, így ez a szolgáltatás és az API REST ügyféleszközökben."

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzéséhez:
* Fejezze be a [előző oktatóanyag](../storage/blobs/storage-unstructured-search.md)
    * Ez az oktatóanyag használja az előző oktatóanyagban létrehozott tárolási fiók és a keresési szolgáltatást
* Telepítse a többi ügyfél, és segít megérteni, hogyan hozható létre egy HTTP-kérelem


## <a name="set-up-the-rest-client"></a>A többi ügyfél beállítása

Az oktatóanyag teljesítéséhez szüksége van egy REST-ügyfél. Ez az oktatóanyag céljából használjuk [Postman](https://www.getpostman.com/). Szabadon használhatja egy másik többi ügyfél, ha már tudjuk, egy adott.

Miután telepítette a Postman, indítsa el.

Ha az első alkalommal REST-hívások Azure-ba, ez az oktatóanyag a fontos összetevők rövid ismertetése: A kérés minden hívás ebben az oktatóanyagban metódus "POST". A fejléc kulcsai "Content-type" és "api-kulcsot." A fejléc kulcsok értékei a "application/json" és a "adminisztrációs kulcsot" (az adminisztrációs kulcsot helyőrzője az elsődleges kulcs) kulcsattribútumokkal. A szervezetnek, a tényleges tartalmát a hívás helyétől. Attól függően, hogy az ügyfél használ előfordulhat, hogy néhány változata hogyan hozható létre a lekérdezést, de azok alapjait.

  ![Félig strukturált keresése](media/search-semi-structured-data/postmanoverview.png)

Az ebben az oktatóanyagban szereplő REST-hívások a keresési api-kulcsot meg kell adni. Az api-kulcs alatt található **kulcsok** belül a keresési szolgáltatáshoz. Az api-kulcs fejlécében szereplő minden API-hívás (a név felülírandó "admin key" az előző képernyőképet vele) kell lennie, ez az oktatóanyag arra utasítja, hogy. Tartsa meg a kulcsot, mert minden egyes kell.

  ![Félig strukturált keresése](media/search-semi-structured-data/keys.png)

## <a name="download-the-sample-data"></a>Töltse le a mintaadatokat

Egy minta adatkészlet Önnek készült. **Töltse le [klinikai kísérletek json.zip](https://github.com/Azure-Samples/storage-blob-integration-with-cdn-search-hdi/raw/master/clinical-trials-json.zip)**  és csomagolja ki azt a saját mappába.

A mintában van például JSON-fájlokat, amelyek nem az eredeti szöveg fájlok nyert [clinicaltrials.gov](https://clinicaltrials.gov/ct2/results). A Microsoft azokat való konvertálása JSON a felhasználók kényelme érdekében.

## <a name="log-in-to-azure"></a>Jelentkezzen be az Azure-ba

Jelentkezzen be az [Azure portálra](http://portal.azure.com).

## <a name="upload-the-sample-data"></a>A mintaadatok feltöltése

Az Azure portálon lépjen vissza a storage-fiók létrehozása a [az oktatóanyag előző](../storage/blobs/storage-unstructured-search.md). Nyissa meg a **adatok** tárolót, majd kattintson **feltöltése**.

Kattintson a **speciális**, és adja meg a "klinikai-próbaverzió-json", majd töltse fel az összes letöltött JSON-fájlokat.

  ![Félig strukturált keresése](media/search-semi-structured-data/clinicalupload.png)

Ha befejeződött a feltöltés, a fájlok az adatokat tároló belül a saját almappájában kell megjelennie.

## <a name="connect-your-search-service-to-your-container"></a>A keresési szolgáltatáshoz kapcsolódni a tárolóhoz

Postman ahhoz, hogy hozzon létre egy adatforrást, egy index és az indexelő három API-hívásokat a keresőszolgáltatása használjuk. Az adatforrás egy mutatót a tárfiók és a JSON-adatokat tartalmaz. A keresési szolgáltatás lehetővé teszi a kapcsolat, az adatok betöltésekor.

A lekérdezési karakterláncnak tartalmaznia kell **api-version = 2016 09-01. dátumú előnézeti** és minden hívás kell visszaadnia egy **201 Created**. Az általánosan elérhető api-version még nem rendelkezik olyan jellegű kezelik a json-, egy jsonArray, jelenleg csak a minta api-verzió.

A következő három API-hívások végrehajtása a többi ügyfélről.

### <a name="create-a-datasource"></a>Egy adatforrás létrehozása

Adatforrás határozza meg, hogy milyen adatokat kell index.

A végpont hívás `https://[service name].search.windows.net/datasources?api-version=2016-09-01-Preview`. Cserélje le `[service name]` nevű, a keresési szolgáltatáshoz.

Ez a hívás szüksége van a tárfiók és a tárfiók kulcsának neve. A tárfiók hívóbetűjét az Azure portálon belül a tárfiókban található **hívóbetűk**. Helye az alábbi ábrán látható:

  ![Félig strukturált keresése](media/search-semi-structured-data/storagekeys.png)

Cserélje le a `[storage account name]` és `[storage account key]` törzsében. a hívás a hívás végrehajtása előtt.

```json
{
    "name" : "clinical-trials-json",
    "type" : "azureblob",
    "credentials" : { "connectionString" : "DefaultEndpointsProtocol=https;AccountName=[storage account name];AccountKey=[storage account key];" },
    "container" : { "name" : "data", "query" : "clinical-trials-json" }
}
```

A válasz hasonlóan kell kinéznie:

```json
{
    "@odata.context": "https://exampleurl.search.windows.net/$metadata#datasources/$entity",
    "@odata.etag": "\"0x8D505FBC3856C9E\"",
    "name": "clinical-trials-json",
    "description": null,
    "type": "azureblob",
    "subtype": null,
    "credentials": {
        "connectionString": "DefaultEndpointsProtocol=https;AccountName=[mystorageaccounthere];AccountKey=[[myaccountkeyhere]]];"
    },
    "container": {
        "name": "data",
        "query": "clinical-trials-json"
    },
    "dataChangeDetectionPolicy": null,
    "dataDeletionDetectionPolicy": null
}
```

### <a name="create-an-index"></a>Index létrehozása
    
A második API-hívás indexet hoz létre. Az index határozza meg, a paraméterek és attribútumaik.

Az URL-címe az e híváshoz `https://[service name].search.windows.net/indexes?api-version=2016-09-01-Preview`. Cserélje le `[service name]` nevű, a keresési szolgáltatáshoz.

Először cserélje le az URL-címet. Másolja és illessze be a következő kódot a szervezet és a lekérdezés futtatásához.

```json
{
  "name": "clinical-trials-json-index",  
  "fields": [
  {"name": "FileName", "type": "Edm.String", "searchable": false, "retrievable": true, "facetable": false, "filterable": false, "sortable": true},
  {"name": "Description", "type": "Edm.String", "searchable": true, "retrievable": false, "facetable": false, "filterable": false, "sortable": false},
  {"name": "MinimumAge", "type": "Edm.Int32", "searchable": false, "retrievable": true, "facetable": true, "filterable": true, "sortable": true},
  {"name": "Title", "type": "Edm.String", "searchable": true, "retrievable": true, "facetable": false, "filterable": true, "sortable": true},
  {"name": "URL", "type": "Edm.String", "searchable": false, "retrievable": false, "facetable": false, "filterable": false, "sortable": false},
  {"name": "MyURL", "type": "Edm.String", "searchable": false, "retrievable": true, "facetable": false, "filterable": false, "sortable": false},
  {"name": "Gender", "type": "Edm.String", "searchable": false, "retrievable": true, "facetable": true, "filterable": true, "sortable": false},
  {"name": "MaximumAge", "type": "Edm.Int32", "searchable": false, "retrievable": true, "facetable": true, "filterable": true, "sortable": true},
  {"name": "Summary", "type": "Edm.String", "searchable": true, "retrievable": true, "facetable": false, "filterable": false, "sortable": false},
  {"name": "NCTID", "type": "Edm.String", "key": true, "searchable": true, "retrievable": true, "facetable": false, "filterable": true, "sortable": true},
  {"name": "Phase", "type": "Edm.String", "searchable": false, "retrievable": true, "facetable": true, "filterable": true, "sortable": false},
  {"name": "Date", "type": "Edm.String", "searchable": false, "retrievable": true, "facetable": false, "filterable": false, "sortable": true},
  {"name": "OverallStatus", "type": "Edm.String", "searchable": false, "retrievable": true, "facetable": true, "filterable": true, "sortable": false},
  {"name": "OrgStudyId", "type": "Edm.String", "searchable": true, "retrievable": true, "facetable": false, "filterable": true, "sortable": false},
  {"name": "HealthyVolunteers", "type": "Edm.String", "searchable": false, "retrievable": true, "facetable": true, "filterable": true, "sortable": false},
  {"name": "Keywords", "type": "Collection(Edm.String)", "searchable": true, "retrievable": true, "facetable": true, "filterable": false, "sortable": false},
  {"name": "metadata_storage_last_modified", "type":"Edm.DateTimeOffset", "searchable": false, "retrievable": true, "filterable": true, "sortable": false},
  {"name": "metadata_storage_size", "type":"Edm.String", "searchable": false, "retrievable": true, "filterable": true, "sortable": false},
  {"name": "metadata_content_type", "type":"Edm.String", "searchable": true, "retrievable": true, "filterable": true, "sortable": false}
  ],
  "suggesters": [
  {
    "name": "sg",
    "searchMode": "analyzingInfixMatching",
    "sourceFields": ["Title"]
  }
  ]
}
```

A válasz hasonlóan kell kinéznie:

```json
{
    "@odata.context": "https://exampleurl.search.windows.net/$metadata#indexes/$entity",
    "@odata.etag": "\"0x8D505FC00EDD5FA\"",
    "name": "clinical-trials-json-index",
    "fields": [
        {
            "name": "FileName",
            "type": "Edm.String",
            "searchable": false,
            "filterable": false,
            "retrievable": true,
            "sortable": true,
            "facetable": false,
            "key": false,
            "indexAnalyzer": null,
            "searchAnalyzer": null,
            "analyzer": null,
            "synonymMaps": []
        },
        {
            "name": "Description",
            "type": "Edm.String",
            "searchable": true,
            "filterable": false,
            "retrievable": false,
            "sortable": false,
            "facetable": false,
            "key": false,
            "indexAnalyzer": null,
            "searchAnalyzer": null,
            "analyzer": null,
            "synonymMaps": []
        },
        ...
          "scoringProfiles": [],
    "defaultScoringProfile": null,
    "corsOptions": null,
    "suggesters": [],
    "analyzers": [],
    "tokenizers": [],
    "tokenFilters": [],
    "charFilters": []
}
```

### <a name="create-an-indexer"></a>Hozzon létre egy indexelőt

Az indexelő az adatforráshoz kapcsolódik a cél search-indexet, és opcionálisan biztosítja a automatizálhatja az adatfrissítési ütemezés szerint.

Az URL-címe az e híváshoz `https://[service name].search.windows.net/indexers?api-version=2016-09-01-Preview`. Cserélje le `[service name]` nevű, a keresési szolgáltatáshoz.

Először cserélje le az URL-címet. Másolja és illessze be a következő kódot a szervezet és a lekérdezés futtatásához.

```json
{
  "name" : "clinical-trials-json-indexer",
  "dataSourceName" : "clinical-trials-json",
  "targetIndexName" : "clinical-trials-json-index",
  "parameters" : { "configuration" : { "parsingMode" : "jsonArray" } }
}
```

A válasz hasonlóan kell kinéznie:

```json
{
    "@odata.context": "https://exampleurl.search.windows.net/$metadata#indexers/$entity",
    "@odata.etag": "\"0x8D505FDE143D164\"",
    "name": "clinical-trials-json-indexer",
    "description": null,
    "dataSourceName": "clinical-trials-json",
    "targetIndexName": "clinical-trials-json-index",
    "schedule": null,
    "parameters": {
        "batchSize": null,
        "maxFailedItems": null,
        "maxFailedItemsPerBatch": null,
        "base64EncodeKeys": null,
        "configuration": {
            "parsingMode": "jsonArray"
        }
    },
    "fieldMappings": [],
    "enrichers": [],
    "disabled": null
}
```

## <a name="search-your-json-files"></a>A JSON-fájlok keresése

Most, hogy a keresési szolgáltatás az adatokat tároló csatlakoztatva van, megkezdheti a fájlok keresése.

Nyissa meg az Azure-portálon, és lépjen vissza a keresési szolgáltatáshoz. Ahogy az előző oktatóanyag.

  ![Strukturálatlan keresése](media/search-semi-structured-data/indexespane.png)

### <a name="user-defined-metadata-search"></a>Felhasználó által definiált metaadatok keresése

Mivel előtt, az adatok a számos módon lehet lekérdezni: teljes szöveges keresés, a rendszer tulajdonságai, vagy a felhasználó által definiált metaadatok. Rendszer tulajdonságai és a felhasználó által definiált metaadatok előfordulhat, hogy csak keres a a `$select` paraméternek, ha volt megjelölve **lekérhető** a cél-index létrehozása közben. Az index paraméter nem lehet módosítani a létrehozásuk után. Azonban további paraméterek adhatók meg.

Például egy egyszerű lekérdezést `$select=Gender,metadata_storage_size`, amely korlátozza, hogy két paraméterek visszaállításához.

  ![Félig strukturált keresése](media/search-semi-structured-data/lastquery.png)

Összetettebb lekérdezés például `$filter=MinimumAge ge 30 and MaximumAge lt 75`, amely csak eredményt, ahol a paraméterek MinimumAge nagyobb vagy egyenlő, 30 és MaximumAge kisebb, mint 75 ad vissza.

  ![Félig strukturált keresése](media/search-semi-structured-data/metadatashort.png)

Ha szeretné kipróbálni, majd próbálja meg néhány további lekérdezések, saját magának, nyugodtan ehhez. Logikai operátorok használhatók tudja (és, vagy nem) és összehasonlító operátorok (eq, ne, gt, lt, ge, le). Karakterláncok összehasonlítására-és nagybetűk.

A `$filter` paraméter csak akkor vannak megjelölve az index létrehozása szűrhető metaadatokkal működik.

## <a name="next-steps"></a>Következő lépések

Ebben az oktatóprogramban megismerte félig strukturált adatok, például az az Azure search használatával keresése:

> [!div class="checklist"]
> * A REST API használatával Azure Search szolgáltatás létrehozása
> * Az Azure keresési szolgáltatását használja a tároló keresése

Kattintson ide további információt a keresés.

> [!div class="nextstepaction"]
> [Az Azure Blob Storage dokumentumok indexelő](search-howto-indexing-azure-blob-storage.md)