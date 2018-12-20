---
title: Az oktatóanyag az Azure Blob storage – Azure Search JSON keresése
description: Ebben az oktatóanyagban megtanulhatja, hogyan kereshet részben strukturált Azure-beli blobadatokat az Azure Search használatával.
author: HeidiSteen
manager: cgronlun
services: search
ms.service: search
ms.topic: tutorial
ms.date: 07/12/2018
ms.author: heidist
ms.custom: seodec2018
ms.openlocfilehash: ba9b34dbd9d0959e79c755abc8dad9fe1d358a50
ms.sourcegitcommit: c94cf3840db42f099b4dc858cd0c77c4e3e4c436
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/19/2018
ms.locfileid: "53632942"
---
# <a name="tutorial-search-semi-structured-data-in-azure-cloud-storage"></a>Oktatóanyag: Részben strukturált adatok keresése az Azure felhőalapú tárolóban

Ebben a kétrészes oktatóanyag-sorozatban megismerheti, hogyan kereshet részben strukturált és strukturálatlan adatokat az Azure Search használatával. Az [1. rész](../storage/blobs/storage-unstructured-search.md) bemutatta a strukturálatlan adatok keresését, de az ehhez az oktatóanyaghoz fontos előfeltételeket is tartalmazott, ilyen például a tárfiók létrehozása. 

A 2. részben a hangsúly a részben strukturált adatokra helyeződik át, például az Azure-blobokban tárolt JSON-ra. A részben strukturált adatok címkéket és jelölőket tartalmaznak, amelyek a tartalmakat választják el az adatokon belül. Kompromisszumot jelent a holisztikusan indexelendő strukturálatlan adatok és a formálisan strukturált adatok között, amelyek egy adatmodellt követnek, például egy relációsadatbázis-sémát, amely mezőnként könnyen bejárható.

A 2. részben a következőkkel ismerkedhet meg:

> [!div class="checklist"]
> * Azure Search-adatforrás konfigurálása egy Azure Blob-tárolóhoz
> * Azure Search-index és indexelő létrehozása és feltöltése a tároló bejárásához és kereshető tartalom kinyeréséhez
> * Keresés az újonnan létrehozott indexben

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

* Az [előző oktatóanyag](../storage/blobs/storage-unstructured-search.md) befejezése, amely biztosítja a benne létrehozott tárfiókot és keresési szolgáltatást.

* REST-ügyfél telepítése és a HTTP-kérelmek létrehozásának ismerete. Ebben az oktatóanyagban a [Postman](https://www.getpostman.com/) szolgáltatást használjuk. Nyugodtan használhat egy másik REST-ügyfélt, ha azt már jól ismeri.

> [!NOTE]
> Ez az oktatóanyag a JSON-tömbök támogatására támaszkodik, amely jelenleg előzetes verziójú funkció az Azure Search szolgáltatásban. A portálon nem érhető el. Ezért az előzetes verziójú, ezt a funkciót biztosító REST API-t használjuk, és egy REST-ügyféleszköz segítségével hívjuk meg az API-t.

## <a name="set-up-postman"></a>A Postman beállítása

Indítsa el a Postmant, és hozzon létre egy HTTP-kérelmet. Ha nem ismeri ezt az eszközt, további információért tekintse meg a következőt: [Az Azure Search REST API-k vizsgálata a Fiddlerrel vagy a Postmannel](search-fiddler.md).

A kérelem metódusa az oktatóanyagban található minden hívás esetében: POST. A fejléckulcsok Content-type (tartalomtípus) és api-key (API-kulcs) típusúak. A fejléckulcsok értékei: application/json (alkalmazás/json) és admin key (adminisztrációs kulcs, amely az elsődleges keresési kulcs helyőrzője). A törzs az a hely, ahol elhelyezi a hívás tényleges tartalmát. Attól függően, hogy milyen ügyfélt használ, különböző módszerekkel hozhatja létre a lekérdezést, de ezek az alapvető tudnivalók.

  ![Részben strukturált keresés](media/search-semi-structured-data/postmanoverview.png)

A jelen oktatóanyagban bemutatott REST-hívásokhoz szükség van a keresési API-kulcsra. Az API-kulcsot a keresési szolgáltatás **Kulcsok** területén találja meg. Ennek az API-kulcsnak szerepelnie kell minden API-hívás fejlécében (cserélje le rá az előző képernyőképen látható „adminisztrációs kulcsot”), amelyet ezen oktatóanyagban hoz létre. Őrizze meg a kulcsot, mivel minden híváshoz szüksége lesz rá.

  ![Részben strukturált keresés](media/search-semi-structured-data/keys.png)

## <a name="download-the-sample-data"></a>A mintaadatok letöltése

Előkészítettünk Önnek egy mintaadatkészletet. **Töltse le a [clinical-trials-json.zip](https://github.com/Azure-Samples/storage-blob-integration-with-cdn-search-hdi/raw/master/clinical-trials-json.zip) fájlt**, és bontsa ki a saját mappájába.

A mintában példa JSON-fájlok találhatók, amelyek eredetileg a [clinicaltrials.gov](https://clinicaltrials.gov/ct2/results) oldalról származó szöveges fájlok. A kényelmes használat érdekében ezeket JSON-fájlokká alakítottuk át.

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

Jelentkezzen be az [Azure Portalra](https://portal.azure.com).

## <a name="upload-the-sample-data"></a>A mintaadatok feltöltése

Az Azure Portalon lépjen vissza az [előző oktatóanyagban](../storage/blobs/storage-unstructured-search.md) létrehozott tárfiókhoz. Ezután nyissa meg az **adattárolót**, és kattintson a **Feltöltés** elemre.

Kattintson a **Speciális** elemre, írja be a clinical-trials-json kifejezést, majd töltse fel az összes letöltött JSON-fájlt.

  ![Részben strukturált keresés](media/search-semi-structured-data/clinicalupload.png)

Ha befejeződött a feltöltés, a fájlok a saját almappájukban jelennek meg az adattárolóban.

## <a name="connect-your-search-service-to-your-container"></a>Keresési szolgáltatás csatlakoztatása a tárolóhoz

A Postmannel három API-hívást indítunk a keresési szolgáltatás felé annak érdekében, hogy létrehozzunk egy adatforrást, egy indexet és egy indexelőt. Az adatforrás tartalmaz egy, a tárfiókjára irányuló mutatót és a JSON-adatait. A keresési szolgáltatás az adatok betöltésekor hozza létre a kapcsolatot.

A lekérdezési sztringnek tartalmaznia kell az **api-version=2016-09-01-Preview** elemet, és minden hívásnak egy **201 Created** elemet kell visszaadnia. Az általánosan elérhető api-version még nem képes a JSON-t egy JSON-tömbként kezelni, jelenleg erre csak az előzetes api-version képes.

Hajtsa végre az alábbi három API-hívást a REST-ügyfélről.

### <a name="create-a-datasource"></a>Adatforrás létrehozása

Az adatforrás határozza meg, hogy mely adatok legyenek indexelve.

A hívás végpontja: `https://[service name].search.windows.net/datasources?api-version=2016-09-01-Preview`. Cserélje le a `[service name]` elemet a keresési szolgáltatás nevére.

Ehhez a híváshoz szükség lesz a tárfiókja nevére és a tárfiók kulcsára. A tárfiók kulcsa megtalálható az Azure Portalon a tárfiókja **Hozzáférési kulcsok** részében. Ennek helye az alábbi képen látható:

  ![Részben strukturált keresés](media/search-semi-structured-data/storagekeys.png)

A hívás törzsében cserélje le a `[storage account name]` és a `[storage account key]` elemet a hívás végrehajtása előtt.

```json
{
    "name" : "clinical-trials-json",
    "type" : "azureblob",
    "credentials" : { "connectionString" : "DefaultEndpointsProtocol=https;AccountName=[storage account name];AccountKey=[storage account key];" },
    "container" : { "name" : "data", "query" : "clinical-trials-json" }
}
```

A válasznak így kell kinéznie:

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
    
A második API-hívás létrehoz egy indexet. Az index határozza meg az összes paramétert és ezek attribútumait.

A hívás URL-címe: `https://[service name].search.windows.net/indexes?api-version=2016-09-01-Preview`. Cserélje le a `[service name]` elemet a keresési szolgáltatás nevére.

Első lépésként cserélje le az URL-címet. Ezután másolja és illessze be a következő kódot a törzsbe, majd futtassa a lekérdezést.

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

A válasznak így kell kinéznie:

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

### <a name="create-an-indexer"></a>Indexelő létrehozása

Az indexelő a cél keresési indexhez csatlakoztatja az adatforrást, és opcionálisan ütemezést biztosít az adatfrissítés automatizálásához.

A hívás URL-címe: `https://[service name].search.windows.net/indexers?api-version=2016-09-01-Preview`. Cserélje le a `[service name]` elemet a keresési szolgáltatás nevére.

Első lépésként cserélje le az URL-címet. Ezután másolja és illessze be a következő kódot a törzsbe, majd futtassa a lekérdezést.

```json
{
  "name" : "clinical-trials-json-indexer",
  "dataSourceName" : "clinical-trials-json",
  "targetIndexName" : "clinical-trials-json-index",
  "parameters" : { "configuration" : { "parsingMode" : "jsonArray" } }
}
```

A válasznak így kell kinéznie:

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

## <a name="search-your-json-files"></a>JSON-fájlok keresése

Most, hogy a keresési szolgáltatást csatlakoztatta az adattárolóhoz, megkezdheti fájljai keresését.

Nyissa meg az Azure Portalt, és lépjen vissza a keresési szolgáltatásához. Pontosan úgy, ahogyan az előző oktatóanyagban tette.

  ![Strukturálatlan keresés](media/search-semi-structured-data/indexespane.png)

### <a name="user-defined-metadata-search"></a>Felhasználó által definiált metaadatok keresése

Mint korábban, az adatok többféle módon kérdezhetők le: teljes szöveges keresés, rendszertulajdonságok vagy felhasználó által definiált metaadatok használatával. A rendszertulajdonságok és a felhasználó által definiált metaadatok is csak a `$select` paraméterrel kereshetők, ha a célindex létrehozása során **lekérdezhetőként** lettek megjelölve. Létrehozásuk után az index paraméterei nem módosíthatók. Azonban lehetőség van további paraméterek hozzáadására.

Egy példa alapszintű lekérdezésre: `$select=Gender,metadata_storage_size`. Ez a lekérdezés korlátozza ezt a két paramétert.

  ![Részben strukturált keresés](media/search-semi-structured-data/lastquery.png)

Egy példa összetettebb lekérdezésre: `$filter=MinimumAge ge 30 and MaximumAge lt 75`. Ez a lekérdezés csak olyan eredményeket ad vissza, ahol a paraméterek MinimumAge értéke legalább 30, és a MaximumAge értéke kisebb, mint 75.

  ![Részben strukturált keresés](media/search-semi-structured-data/metadatashort.png)

Nyugodtan kísérletezhet, és néhány további lekérdezést is kipróbálhat. Használhat logikai operátorokat (and, or, not) és összehasonlító operátorokat (eq, ne, gt, lt, ge, le) is. A sztring-összehasonlítások megkülönböztetik a kis- és nagybetűket.

A `$filter` paraméter csak olyan metaadatokkal működik, amelyek szűrhetőként lettek megjelölve az index létrehozásakor.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Az oktatóanyagok után feleslegessé vált elemek az Azure Search szolgáltatást tartalmazó erőforráscsoport törlésével távolíthatók el a leggyorsabban. Most törölheti az erőforráscsoportot, amivel véglegesen eltávolíthatja a teljes tartalmát. A portálon az erőforráscsoport neve az Azure Search szolgáltatás Áttekintés lapján szerepel.

## <a name="next-steps"></a>További lépések

Mesterséges intelligencia által vezérelt algoritmusokat csatolhat egy indexelőfolyamathoz. Következő lépésként folytassa az alábbi oktatóanyaggal.

> [!div class="nextstepaction"]
> [Dokumentumok indexelése az Azure Blob Storage-ban](search-howto-indexing-azure-blob-storage.md)