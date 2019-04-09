---
title: 'Oktatóanyag: A JSON-blobok – Azure Search félig-strutured adatok indexelése'
description: Ismerje meg az Azure Search és a Postman használatával félig strukturált Azure JSON-blobok indexelése és.
author: HeidiSteen
manager: cgronlun
services: search
ms.service: search
ms.topic: tutorial
ms.date: 04/08/2019
ms.author: heidist
ms.custom: seodec2018
ms.openlocfilehash: 8436bb1fc84d5a944b35cd7b2c9667d2148c0af3
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/08/2019
ms.locfileid: "59270464"
---
# <a name="tutorial-index-and-search-semi-structured-data-json-blobs-in-azure-search"></a>Oktatóanyag: Index, és részben strukturált adatok (JSON-blobok) keresése az Azure Search szolgáltatásban

Az Azure Search indexelheti a JSON-dokumentumok és az Azure blob storage-tömbök egy [indexelő](search-indexer-overview.md) , amely meg tudja részben strukturált adatokat olvasni. A részben strukturált adatok címkéket és jelölőket tartalmaznak, amelyek a tartalmakat választják el az adatokon belül. A különbség a között a strukturálatlan adatok, amelyek teljes mértékben indexelendő, és a formálisan strukturált adatok egy adatmodellt, például egy relációsadatbázis-sémát, mező alapon is indexelhető osztja.

Ebben az oktatóanyagban használja a [Azure Search REST API-k](https://docs.microsoft.com/rest/api/searchservice/) és a egy REST-ügyfél a következő feladatokat:

> [!div class="checklist"]
> * Azure Search-adatforrás konfigurálása egy Azure Blob-tárolóhoz
> * A kereshető tartalmak tartalmaznak az Azure Search-index létrehozása
> * Konfigurálja és a tároló és kereshető tartalom kinyeréséhez az Azure blob storage-indexelő futtatása
> * Keresés az újonnan létrehozott indexben

> [!NOTE]
> Ez az oktatóanyag a JSON-tömbök támogatására támaszkodik, amely jelenleg előzetes verziójú funkció az Azure Search szolgáltatásban. A portálon nem érhető el. Ezért az előzetes verziójú, ezt a funkciót biztosító REST API-t használjuk, és egy REST-ügyféleszköz segítségével hívjuk meg az API-t.

## <a name="prerequisites"></a>Előfeltételek

Ez a rövid útmutató az alábbi szolgáltatások, eszközök és adatok használatosak. 

[Az Azure Search szolgáltatás létrehozása](search-create-service-portal.md) vagy [keresse meg a meglévő service](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) az aktuális előfizetésben. Ebben az oktatóanyagban egy ingyenes szolgáltatás használhatja. 

[Az Azure storage-fiók létrehozása](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account), majd [hozzon létre egy blobtárolót](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal) mintaadatok tárolásához. Mivel a kapcsolat kulcs és a tárolási fiók nevét fogja használni, ellenőrizze, hogy a tároló nyilvános hozzáférés szintje "Tároló (névtelen olvasási hozzáférés tárolók)".

[Postman asztali alkalmazás](https://www.getpostman.com/) az Azure Search kérelmek küldésére szolgál.

[Klinikai-kísérletek-json.zip](https://github.com/Azure-Samples/storage-blob-integration-with-cdn-search-hdi/raw/master/clinical-trials-json.zip) ebben az oktatóanyagban használt adatokat tartalmaz. Töltse le és csomagolja ki ezt a fájlt a saját mappájába. Adatok származnak [clinicaltrials.gov](https://clinicaltrials.gov/ct2/results), ebben az oktatóanyagban a konvertált JSON-ná.

## <a name="get-a-key-and-url"></a>Egy kulcsot és egy URL-cím beszerzése

A REST-hívásokhoz minden kérésének tartalmaznia kell a szolgáltatás URL-címét és egy hozzáférési kulcsot. Mindkettőhöz létrejön egy keresési szolgáltatás, így ha hozzáadta az előfizetéséhez az Azure Searchöt, kövesse az alábbi lépéseket a szükséges információk beszerzéséhez:

1. [Jelentkezzen be az Azure Portalon](https://portal.azure.com/), és a search szolgáltatás **áttekintése** lapon, az URL-cím lekéréséhez. A végpontok például a következőképpen nézhetnek ki: `https://mydemo.search.windows.net`.

1. A **beállítások** > **kulcsok**, a szolgáltatás a teljes körű rendszergazdai kulcs beszerzése. Nincsenek két felcserélhetők adminisztrációs kulcsot, az üzletmenet folytonosságának megadott abban az esetben egy vihető kell. Használható vagy az elsődleges vagy másodlagos kulcsot a kérések hozzáadása, módosítása és törlése objektumokat.

![Egy HTTP-végpontját és hozzáférési kulcs lekérése](media/search-fiddler/get-url-key.png "HTTP végpontját és hozzáférési kulcs beszerzése")

Minden kérelemhez szükséges halasztása minden kérelemnél a szolgáltatásnak küldött api-kulcsát. Érvényes kulcs birtokában kérelmenként létesíthető megbízhatósági kapcsolat a kérést küldő alkalmazás és az azt kezelő szolgáltatás között.

## <a name="prepare-sample-data"></a>Mintaadatok létrehozása

1. Keresse meg a mintaadatokat, a rendszer letöltötte.

1. [Jelentkezzen be az Azure Portalon](https://portal.azure.com), keresse meg az Azure storage-fiók és a Blob-tárolóba, majd kattintson **feltöltése**.

1. Kattintson a **Speciális** elemre, írja be a clinical-trials-json kifejezést, majd töltse fel az összes letöltött JSON-fájlt.

  ![Részben strukturált keresés](media/search-semi-structured-data/clinicalupload.png)

Ha befejeződött a feltöltés, a fájlok a saját almappájukban jelennek meg az adattárolóban.

## <a name="set-up-postman"></a>A Postman beállítása

Indítsa el a Postmant, és hozzon létre egy HTTP-kérelmet. Ha ismeri ezt az eszközt, tekintse meg [Ismerkedés az Azure Search REST API-k a postmannel](search-fiddler.md).

A kérelem a módszer minden hívás ebben az oktatóanyagban **POST**. A fejléckulcsok Content-type (tartalomtípus) és api-key (API-kulcs) típusúak. A fejléckulcsok értékei: application/json (alkalmazás/json) és admin key (adminisztrációs kulcs, amely az elsődleges keresési kulcs helyőrzője). A törzs az a hely, ahol elhelyezi a hívás tényleges tartalmát. Attól függően, hogy milyen ügyfélt használ, különböző módszerekkel hozhatja létre a lekérdezést, de ezek az alapvető tudnivalók.

  ![Részben strukturált keresés](media/search-semi-structured-data/postmanoverview.png)

A Postmannel három API-hívást indítunk a keresési szolgáltatás felé annak érdekében, hogy létrehozzunk egy adatforrást, egy indexet és egy indexelőt. Az adatforrás tartalmaz egy, a tárfiókjára irányuló mutatót és a JSON-adatait. A keresési szolgáltatás az adatok betöltésekor hozza létre a kapcsolatot.

A lekérdezési karakterláncnak tartalmaznia kell egy előzetes verziójú API (például **api-version = 2017-11-11-Preview**) és a egy hívás adja vissza egy **201 Created**. Az általánosan elérhető api-version még nem képes a JSON-t egy JSON-tömbként kezelni, jelenleg erre csak az előzetes api-version képes.

Hajtsa végre az alábbi három API-hívást a REST-ügyfélről.

## <a name="create-a-data-source"></a>Adatforrás létrehozása

Egy adatforrás egy Azure Search-objektum, amely megadja, hogy milyen adatok legyenek indexelve.

A hívás végpontja: `https://[service name].search.windows.net/datasources?api-version=2016-09-01-Preview`. Cserélje le a `[service name]` elemet a keresési szolgáltatás nevére. Ehhez a híváshoz szükség lesz a tárfiókja nevére és a tárfiók kulcsára. A tárfiók kulcsa megtalálható az Azure Portalon a tárfiókja **Hozzáférési kulcsok** részében. Ennek helye az alábbi képen látható:

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

## <a name="create-an-index"></a>Index létrehozása
    
A második API-hívás létrehoz egy Azure Search-index. Az index határozza meg az összes paramétert és ezek attribútumait.

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

## <a name="create-and-run-an-indexer"></a>Hozzon létre, és a egy indexelő futtatása

Az indexelő csatlakoztatja az adatforrást, importálja az adatokat a cél keresési indexhez, és opcionálisan biztosítja az Adatfrissítés automatizálásához ütemezés szerint.

A hívás URL-címe: `https://[service name].search.windows.net/indexers?api-version=2016-09-01-Preview`. Cserélje le a `[service name]` elemet a keresési szolgáltatás nevére.

Első lépésként cserélje le az URL-címet. Ezután másolja és illessze be az alábbi kódot a törzsbe, és a kérelem elküldéséhez. A kérelem feldolgozása azonnal. Ha a válasz fog érkezni, kap, amely a teljes szöveges index kereshető.

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

Most már számára küldhet lekérdezéseket az index. Ez a feladat [ **keresési ablak** ](search-explorer.md) a portálon.

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
> [Dokumentumok indexelése az Azure Blob Storage](search-howto-indexing-azure-blob-storage.md)