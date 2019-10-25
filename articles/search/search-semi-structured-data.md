---
title: 'REST-oktatóanyag: félig strutured adatok indexelése JSON-blobokban'
titleSuffix: Azure Cognitive Search
description: Ismerje meg, hogyan indexelheti és keresheti meg a félig strukturált Azure JSON-blobokat az Azure Cognitive Search REST API-k és a Poster használatával.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 569289a2d750f96423bd03ac82cb9e33f893ee15
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/23/2019
ms.locfileid: "72794295"
---
# <a name="rest-tutorial-index-and-search-semi-structured-data-json-blobs-in-azure-cognitive-search"></a>REST-oktatóanyag: részben strukturált adatok (JSON-Blobok) indexelése és keresése az Azure Cognitive Search

Az Azure Cognitive Search képes indexelni az Azure Blob Storage-ban található JSON-dokumentumokat és-tömböket olyan [Indexelő](search-indexer-overview.md) használatával, amely képes a részben strukturált információk beolvasására. A részben strukturált adatok címkéket és jelölőket tartalmaznak, amelyek a tartalmakat választják el az adatokon belül. Feldarabolja a strukturálatlan adatmennyiségek közötti különbséget, amelyeknek teljes mértékben indexelve kell lenniük, és az olyan, az adatmodellbe (például egy olyan kapcsolati adatbázis-sémához) tartozó, formálisan strukturált adat, amely egy mező alapján indexelhető.

Ebben az oktatóanyagban az [Azure Cognitive Search REST API-kat](https://docs.microsoft.com/rest/api/searchservice/) és egy Rest-ügyfelet használ a következő feladatok elvégzéséhez:

> [!div class="checklist"]
> * Azure Cognitive Search-adatforrás konfigurálása Azure Blob-tárolóhoz
> * Kereshető tartalmat tartalmazó Azure Cognitive Search index létrehozása
> * Indexelő konfigurálása és futtatása a tároló olvasásához és a kereshető tartalom kinyeréséhez az Azure Blob Storage-ból
> * Keresés az újonnan létrehozott indexben

## <a name="prerequisites"></a>Előfeltételek

Ebben a rövid útmutatóban a következő szolgáltatásokat, eszközöket és adatfájlokat használja a rendszer. 

[Hozzon létre egy Azure Cognitive Search szolgáltatást](search-create-service-portal.md) , vagy [keressen egy meglévő szolgáltatást](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) a jelenlegi előfizetése alatt. Ehhez az oktatóanyaghoz használhatja az ingyenes szolgáltatást. 

[Hozzon létre egy Azure Storage-fiókot](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account) a mintaadatok tárolásához.

[Poster Desktop-alkalmazás](https://www.getpostman.com/) , amely kéréseket küld az Azure Cognitive Searchnak.

A [Clinical-Trials-JSON. zip](https://github.com/Azure-Samples/storage-blob-integration-with-cdn-search-hdi/raw/master/clinical-trials-json.zip) tartalmazza az oktatóanyagban használt adatkészleteket. Töltse le és csomagolja ki a fájlt a saját mappájába. Az adatok a [clinicaltrials.gov](https://clinicaltrials.gov/ct2/results)-ből származnak, és a JSON formátumba konvertálódnak erre az oktatóanyagra.

## <a name="get-a-key-and-url"></a>Kulcs és URL-cím lekérése

A REST-hívásokhoz minden kérésének tartalmaznia kell a szolgáltatás URL-címét és egy hozzáférési kulcsot. A Search szolgáltatás mindkettővel jön létre, így ha az előfizetéshez hozzáadta az Azure Cognitive Searcht, kövesse az alábbi lépéseket a szükséges információk beszerzéséhez:

1. [Jelentkezzen be a Azure Portalba](https://portal.azure.com/), és a keresési szolgáltatás **Áttekintés** lapján töltse le az URL-címet. A végpontok például a következőképpen nézhetnek ki: `https://mydemo.search.windows.net`.

1. A **beállítások** > **kulcsok**területen szerezze be a szolgáltatásra vonatkozó teljes körű jogosultságokat. Az üzletmenet folytonossága érdekében két, egymással megváltoztathatatlan rendszergazdai kulcs áll rendelkezésre. Az objektumok hozzáadására, módosítására és törlésére vonatkozó kérésekhez használhatja az elsődleges vagy a másodlagos kulcsot is.

![HTTP-végpont és elérési kulcs beszerzése](media/search-get-started-postman/get-url-key.png "HTTP-végpont és elérési kulcs beszerzése")

Minden kérelemhez API-kulcs szükséges a szolgáltatásnak küldött összes kéréshez. Érvényes kulcs birtokában kérelmenként létesíthető megbízhatósági kapcsolat a kérést küldő alkalmazás és az azt kezelő szolgáltatás között.

## <a name="prepare-sample-data"></a>Mintaadatok előkészítése

1. [Jelentkezzen be a Azure Portalba](https://portal.azure.com), navigáljon az Azure Storage-fiókjához, kattintson a **Blobok**elemre, majd a **+ tároló**elemre.

1. [Hozzon létre egy BLOB-tárolót](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal) a mintaadatok tárolásához. Megadhatja a nyilvános hozzáférési szintet bármelyik érvényes értékéhez.

1. A tároló létrehozása után nyissa meg, majd válassza a parancssáv **feltöltés** elemét.

   ![Feltöltés a parancssáv](media/search-semi-structured-data/upload-command-bar.png "Feltöltés a parancssáv")

1. Navigáljon a minta fájlokat tartalmazó mappához. Jelölje ki az összeset, majd kattintson a **feltöltés**elemre.

   ![Fájlok feltöltése](media/search-semi-structured-data/clinicalupload.png "Fájlok feltöltése")

Ha befejeződött a feltöltés, a fájlok a saját almappájukban jelennek meg az adattárolóban.

## <a name="set-up-postman"></a>A Postman beállítása

Indítsa el a Postmant, és hozzon létre egy HTTP-kérelmet. Ha nem ismeri ezt az eszközt, tekintse meg az [Azure Cognitive Search REST API-k a Poster használatával való megismerését](search-get-started-postman.md)ismertető témakört.

Az oktatóanyag minden hívásának kérelmi metódusa **post**. A fejléckulcsok Content-type (tartalomtípus) és api-key (API-kulcs) típusúak. A fejléckulcsok értékei: application/json (alkalmazás/json) és admin key (adminisztrációs kulcs, amely az elsődleges keresési kulcs helyőrzője). A törzs az a hely, ahol elhelyezi a hívás tényleges tartalmát. Attól függően, hogy milyen ügyfélt használ, különböző módszerekkel hozhatja létre a lekérdezést, de ezek az alapvető tudnivalók.

  ![Részben strukturált keresés](media/search-semi-structured-data/postmanoverview.png)

A Postmannel három API-hívást indítunk a keresési szolgáltatás felé annak érdekében, hogy létrehozzunk egy adatforrást, egy indexet és egy indexelőt. Az adatforrás tartalmaz egy, a tárfiókjára irányuló mutatót és a JSON-adatait. A keresési szolgáltatás az adatok betöltésekor hozza létre a kapcsolatot.

A lekérdezési karakterláncoknak meg kell adniuk egy API-verziót, és minden hívásnak egy **201**-as értéket kell visszaadnia. A JSON-tömbök használatának általánosan elérhető API-verziója `2019-05-06`.

Hajtsa végre az alábbi három API-hívást a REST-ügyfélről.

## <a name="create-a-data-source"></a>Adatforrás létrehozása

Az [adatforrás létrehozása API](https://docs.microsoft.com/rest/api/searchservice/create-data-source)létrehoz egy Azure Cognitive Search objektumot, amely meghatározza, hogy milyen adatindexet szeretne.

A hívás végpontja: `https://[service name].search.windows.net/datasources?api-version=2019-05-06`. Cserélje le a `[service name]` elemet a keresési szolgáltatás nevére. 

Ehhez a híváshoz a kérelem törzsének tartalmaznia kell a Storage-fiók nevét, a Storage-fiók kulcsát és a blob-tároló nevét. A tárfiók kulcsa megtalálható az Azure Portalon a tárfiókja **Hozzáférési kulcsok** részében. Ennek helye az alábbi képen látható:

  ![Részben strukturált keresés](media/search-semi-structured-data/storagekeys.png)

A hívás végrehajtása előtt ügyeljen arra, hogy a hívás törzsében `[storage account name]`, `[storage account key]`és `[blob container name]` cserélje le.

```json
{
    "name" : "clinical-trials-json",
    "type" : "azureblob",
    "credentials" : { "connectionString" : "DefaultEndpointsProtocol=https;AccountName=[storage account name];AccountKey=[storage account key];" },
    "container" : { "name" : "[blob container name]"}
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
        "name": "[mycontainernamehere]",
        "query": null
    },
    "dataChangeDetectionPolicy": null,
    "dataDeletionDetectionPolicy": null
}
```

## <a name="create-an-index"></a>Index létrehozása
    
A második hívás [index API-t hoz létre](https://docs.microsoft.com/rest/api/searchservice/create-indexer), amely egy Azure Cognitive Search indexet hoz létre, amely az összes kereshető adattal tárolja. Az index határozza meg az összes paramétert és ezek attribútumait.

A hívás URL-címe: `https://[service name].search.windows.net/indexes?api-version=2019-05-06`. Cserélje le a `[service name]` elemet a keresési szolgáltatás nevére.

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

## <a name="create-and-run-an-indexer"></a>Indexelő létrehozása és futtatása

Az indexelő összekapcsolja az adatforrást, importálja az adatmennyiséget a cél keresési indexbe, és opcionálisan biztosít egy ütemtervet az Adatfrissítés automatizálásához. A REST API [Indexelő létrehozása](https://docs.microsoft.com/rest/api/searchservice/create-indexer).

A hívás URL-címe: `https://[service name].search.windows.net/indexers?api-version=2019-05-06`. Cserélje le a `[service name]` elemet a keresési szolgáltatás nevére.

Első lépésként cserélje le az URL-címet. Ezután másolja és illessze be a következő kódot a törzsbe, és küldje el a kérelmet. A kérés feldolgozása azonnal megtörténik. Ha a válasz visszatér, egy teljes szöveges kereshető indextel fog rendelkezni.

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

Az első dokumentum betöltését követően megkezdheti a keresést. Ehhez a feladathoz a portálon használja a [**keresési Explorert**](search-explorer.md) .

A Azure Portalban nyissa meg a keresési szolgáltatás **áttekintése** lapot, és keresse meg az **indexek** listában létrehozott indexet.

Ügyeljen arra, hogy az imént létrehozott indexet válassza. 

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

Az oktatóanyag után a leggyorsabb megoldás az Azure Cognitive Search szolgáltatást tartalmazó erőforráscsoport törlésével. Most törölheti az erőforráscsoportot, amivel véglegesen eltávolíthatja a teljes tartalmát. A portálon az erőforráscsoport neve az Azure Cognitive Search szolgáltatás áttekintés lapján található.

## <a name="next-steps"></a>Következő lépések

A JSON-Blobok indexeléséhez több módszer és több lehetőség is van. A következő lépésként tekintse át és tesztelje a különböző lehetőségeket, hogy megnézze, mi a legmegfelelőbb a forgatókönyvhöz.

> [!div class="nextstepaction"]
> [JSON-Blobok indexelése az Azure Cognitive Search blob indexelő használatával](search-howto-index-json-blobs.md)
