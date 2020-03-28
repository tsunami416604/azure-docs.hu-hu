---
title: 'Oktatóanyag: Indexelés i. félstrukturált adatok JSON-blobokban'
titleSuffix: Azure Cognitive Search
description: Megtudhatja, hogyan indexeli és kereshet félig strukturált Azure JSON-blobokat az Azure Cognitive Search REST API-k és postás használatával.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: tutorial
ms.date: 02/28/2020
ms.openlocfilehash: ce3b3839319de38020b968ff8db1ee6713b29c47
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "78269983"
---
# <a name="tutorial-index-json-blobs-from-azure-storage-using-rest"></a>Oktatóanyag: Index JSON-blobok az Azure Storage-ból rest használatával

Az Azure Cognitive Search indexelheti a JSON-dokumentumokat és -tömböket az Azure blobstorage-ban egy [olyan indexelő](search-indexer-overview.md) használatával, amely tudja, hogyan kell olvasni a félig strukturált adatokat. A részben strukturált adatok címkéket és jelölőket tartalmaznak, amelyek a tartalmakat választják el az adatokon belül. Felosztja a különbséget a strukturálatlan adatok, amelyeket teljes mértékben indexelt, és hivatalosan strukturált adatok, amelyek megfelelnek egy adatmodell, például egy relációs adatbázis séma, amely mezőnként indexelhető.

Ez az oktatóanyag a Postman és a [Search REST API-kat](https://docs.microsoft.com/rest/api/searchservice/) használja a következő feladatok végrehajtásához:

> [!div class="checklist"]
> * Azure Cognitive Search adatforrás konfigurálása egy Azure blobtárolóhoz
> * Azure Cognitive Search index létrehozása kereshető tartalom tárolására
> * Indexelő konfigurálása és futtatása a tároló olvasásához és a kereshető tartalom azure blobstorage-ból való kinyeréséhez
> * Keresés az újonnan létrehozott indexben

Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) mielőtt elkezdené.

## <a name="prerequisites"></a>Előfeltételek

+ [Azure Storage](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account)
+ [Postman asztali alkalmazás](https://www.getpostman.com/)
+ Meglévő [keresési szolgáltatás](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) [létrehozása](search-create-service-portal.md) vagy keresése 

> [!Note]
> Használhatja az ingyenes szolgáltatást az oktatóanyaghoz. Az ingyenes keresési szolgáltatás három indexre, három indexelőre és három adatforrásra korlátozza. Az oktatóanyagban mindegyikből egyet hozhat majd létre. Mielőtt elkezdené, győződjön meg róla, hogy van hely a szolgáltatás, hogy elfogadja az új forrásokat.

## <a name="download-files"></a>Fájlok letöltése

[Clinical-trials-json.zip](https://github.com/Azure-Samples/storage-blob-integration-with-cdn-search-hdi/raw/master/clinical-trials-json.zip) tartalmazza az ebben az oktatóanyagban használt adatokat. Töltse le és csomagolja ki a fájlt a saját mappájába. Az adatok [az clinicaltrials.gov](https://clinicaltrials.gov/ct2/results)származó , az oktatóanyag hoz JSON-ra konvertálva.

## <a name="1---create-services"></a>1 - Szolgáltatások létrehozása

Ez az oktatóanyag az Azure Cognitive Search indexelési és lekérdezések, és az Azure Blob storage az adatok biztosításához. 

Ha lehetséges, hozzon létre mind ugyanabban a régióban, mind az erőforráscsoportban a közelség és a kezelhetőség érdekében. A gyakorlatban az Azure Storage-fiók bármely régióban lehet.

### <a name="start-with-azure-storage"></a>Kezdje az Azure Storage szolgáltatással

1. [Jelentkezzen be az Azure Portalra,](https://portal.azure.com/) és kattintson **a + Erőforrás létrehozása gombra.**

1. Keressen *tárfiókot,* és válassza a Microsoft tárfiók-ajánlatát.

   ![Tárfiók létrehozása](media/cognitive-search-tutorial-blob/storage-account.png "Tárfiók létrehozása")

1. Az Alapok lapon a következő elemek szükségesek. Fogadja el az alapértelmezett értékeket minden máshoz.

   + **Erőforráscsoport**. Jelöljön ki egy meglévőt, vagy hozzon létre egy újat, de használja ugyanazt a csoportot az összes szolgáltatáshoz, hogy együttesen kezelhesse őket.

   + **Tárfiók neve**. Ha úgy gondolja, hogy több azonos típusú erőforrással rendelkezik, használja a nevet típus és régió, például *blobstoragewestus*szerint. 

   + **Hely**. Ha lehetséges, válassza ki ugyanazt a helyet, amelyet az Azure Cognitive Search és a Cognitive Services használt. Egyetlen hely érvényteleníti a sávszélesség-díjakat.

   + **Számla fajta**. Válassza ki az alapértelmezett, *StorageV2 (általános célú v2)*.

1. A szolgáltatás létrehozásához kattintson a **Véleményezés + Létrehozás** gombra.

1. Létrehozása után kattintson **az Ugrás az erőforrásra** gombra az Áttekintés lap megnyitásához.

1. Kattintson **a Blobs** szolgáltatás elemre.

1. [Hozzon létre egy Blob-tárolót](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal) mintaadatok tárolására. A nyilvános hozzáférési szintet bármely érvényes értékére állíthatja be.

1. A tároló létrehozása után nyissa meg, és válassza a **Feltöltés** lehetőséget a parancssávon.

   ![Feltöltés a parancssávra](media/search-semi-structured-data/upload-command-bar.png "Feltöltés a parancssávra")

1. Nyissa meg a mintafájlokat tartalmazó mappát. Jelölje ki az összeset, majd kattintson a **Feltöltés gombra.**

   ![Fájlok feltöltése](media/search-semi-structured-data/clinicalupload.png "Fájlok feltöltése")

Ha befejeződött a feltöltés, a fájlok a saját almappájukban jelennek meg az adattárolóban.

### <a name="azure-cognitive-search"></a>Azure Cognitive Search

A következő erőforrás az Azure Cognitive Search, amelyet [a portálon hozhat létre.](search-create-service-portal.md) Az ingyenes szint segítségével elvégezheti ezt a forgatókönyvet. 

Az Azure Blob storage-hoz is szánjon egy kis ideig a hozzáférési kulcs összegyűjtését. Továbbá, amikor elkezdi strukturálni a kérelmeket, meg kell adnia a végpont és a rendszergazdai api-kulcs hitelesítéséhez használt minden egyes kérelmet.

### <a name="get-a-key-and-url"></a>Kulcs és URL beszerezése

A REST-hívásokhoz minden kérésének tartalmaznia kell a szolgáltatás URL-címét és egy hozzáférési kulcsot. A keresési szolgáltatás mindkettővel jön létre, így ha hozzáadta az Azure Cognitive Search-et az előfizetéséhez, kövesse az alábbi lépéseket a szükséges információk beszerezéséhez:

1. [Jelentkezzen be az Azure Portalon,](https://portal.azure.com/)és a keresési szolgáltatás **áttekintése** lapon lekell szereznie az URL-címet. A végpontok például a következőképpen nézhetnek ki: `https://mydemo.search.windows.net`.

1. A **Beállítások** > **kulcsok**párbeszédpanelen szerezzen be egy rendszergazdai kulcsot a szolgáltatás teljes jogához. Két cserélhető rendszergazdai kulcs van, amelyek az üzletmenet folytonosságát biztosítják arra az esetre, ha át kell görgetnie egyet. Az elsődleges vagy másodlagos kulcsot objektumok hozzáadására, módosítására és törlésére irányuló kérelmeken használhatja.

![HTTP-végpont és hozzáférési kulcs beszerezni](media/search-get-started-postman/get-url-key.png "HTTP-végpont és hozzáférési kulcs beszerezni")

Minden kérelemhez api-kulcs szükséges a szolgáltatásnak küldött minden kéréshez. Érvényes kulcs birtokában kérelmenként létesíthető megbízhatósági kapcsolat a kérést küldő alkalmazás és az azt kezelő szolgáltatás között.

## <a name="2---set-up-postman"></a>2 - Postás beállítása

Indítsa el a Postmant, és hozzon létre egy HTTP-kérelmet. Ha nem ismeri ezt az eszközt, [olvassa el az Azure Cognitive Search REST API-k felfedezése postán .](search-get-started-postman.md)

A kérelem módszerek minden hívás a bemutató **POST** és **GET**. Három API-hívást fog kezdeményezni a keresési szolgáltatáshoz adatforrás, index és indexelő létrehozásához. Az adatforrás tartalmaz egy, a tárfiókjára irányuló mutatót és a JSON-adatait. A keresési szolgáltatás az adatok betöltésekor hozza létre a kapcsolatot.

A fejlécek, állítsa be a `application/json` "Tartalom-típus", és állítsa be `api-key` az Azure Cognitive Search szolgáltatás felügyeleti api-kulcs. Miután beállította a fejléceket, a gyakorlat minden kéréséhez használhatja őket.

  ![Postás kérelem URL-címe és fejléce](media/search-get-started-postman/postman-url.png "Postás kérelem URL-címe és fejléce")

Az URI-knak api-verziót kell megadniuk, és minden hívásnak **201 Created 201-et kell visszaadnia.** A JSON-tömbök általánosan elérhető `2019-05-06`api-verziója a .

## <a name="3---create-a-data-source"></a>3 - Adatforrás létrehozása

Az [adatforrás létrehozása API](https://docs.microsoft.com/rest/api/searchservice/create-data-source) létrehoz egy Azure Cognitive Search objektumot, amely meghatározza, hogy milyen adatokat indexeljen.

1. Állítsa a hívás végpontját `https://[service name].search.windows.net/datasources?api-version=2019-05-06`a beállításra. Cserélje le a `[service name]` elemet a keresési szolgáltatás nevére. 

1. Másolja a következő JSON-t a kérelem törzsébe.

    ```json
    {
        "name" : "clinical-trials-json-ds",
        "type" : "azureblob",
        "credentials" : { "connectionString" : "DefaultEndpointsProtocol=https;AccountName=[storage account name];AccountKey=[storage account key];" },
        "container" : { "name" : "[blob container name]"}
    }
    ```

1. Cserélje le a kapcsolati karakterláncot a fiókjához érvényes karakterláncra.

1. Cserélje le a "[blob tároló neve]" a mintaadatokhoz létrehozott tárolóra. 

1. Küldje el a kérést. A válasznak így kell kinéznie:

    ```json
    {
        "@odata.context": "https://exampleurl.search.windows.net/$metadata#datasources/$entity",
        "@odata.etag": "\"0x8D505FBC3856C9E\"",
        "name": "clinical-trials-json-ds",
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

## <a name="4---create-an-index"></a>4 - Index létrehozása
    
A második hívás a [Create Index API,](https://docs.microsoft.com/rest/api/searchservice/create-index)létrehozva egy Azure Cognitive Search index, amely tárolja az összes kereshető adatokat. Az index határozza meg az összes paramétert és ezek attribútumait.

1. Állítsa a hívás végpontját `https://[service name].search.windows.net/indexes?api-version=2019-05-06`a beállításra. Cserélje le a `[service name]` elemet a keresési szolgáltatás nevére.

1. Másolja a következő JSON-t a kérelem törzsébe.

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
      ]
    }
   ```

1. Küldje el a kérést. A válasznak így kell kinéznie:

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
          }
    ```

## <a name="5---create-and-run-an-indexer"></a>5 - Indexelő létrehozása és futtatása

Az indexelő csatlakozik az adatforráshoz, adatokat importál a célkeresési indexbe, és opcionálisan ütemezést biztosít az adatfrissítés automatizálásához. A REST API [create indexelő](https://docs.microsoft.com/rest/api/searchservice/create-indexer).

1. Állítsa be a hívás `https://[service name].search.windows.net/indexers?api-version=2019-05-06`URI-ját. Cserélje le a `[service name]` elemet a keresési szolgáltatás nevére.

1. Másolja a következő JSON-t a kérelem törzsébe.

    ```json
    {
      "name" : "clinical-trials-json-indexer",
      "dataSourceName" : "clinical-trials-json-ds",
      "targetIndexName" : "clinical-trials-json-index",
      "parameters" : { "configuration" : { "parsingMode" : "jsonArray" } }
    }
    ```

1. Küldje el a kérést. A kérés feldolgozása azonnal megtörténik. Amikor a válasz jön vissza, akkor egy index, amely teljes szöveges kereshető. A válasznak így kell kinéznie:

    ```json
    {
        "@odata.context": "https://exampleurl.search.windows.net/$metadata#indexers/$entity",
        "@odata.etag": "\"0x8D505FDE143D164\"",
        "name": "clinical-trials-json-indexer",
        "description": null,
        "dataSourceName": "clinical-trials-json-ds",
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

## <a name="6---search-your-json-files"></a>6 - Keresés a JSON fájlok

Az első dokumentum betöltése után már elkezdheti a keresést.

1. Módosítsa az igét **get**gombra.

1. Állítsa be a hívás `https://[service name].search.windows.net/indexes/clinical-trials-json-index/docs?search=*&api-version=2019-05-06&$count=true`URI-ját. Cserélje le a `[service name]` elemet a keresési szolgáltatás nevére.

1. Küldje el a kérést. Ez egy meghatározatlan teljes szöveges keresési lekérdezés, amely az indexben visszakereshetőként megjelölt összes mezőt, valamint egy dokumentumszámot ad vissza. A válasznak így kell kinéznie:

    ```json
    {
        "@odata.context": "https://exampleurl.search.windows.net/indexes('clinical-trials-json-index')/$metadata#docs(*)",
        "@odata.count": 100,
        "value": [
            {
                "@search.score": 1.0,
                "FileName": "NCT00000102.txt",
                "MinimumAge": 14,
                "Title": "Congenital Adrenal Hyperplasia: Calcium Channels as Therapeutic Targets",
                "MyURL": "https://azure.storagedemos.com/clinical-trials/NCT00000102.txt",
                "Gender": "Both",
                "MaximumAge": 35,
                "Summary": "This study will test the ability of extended release nifedipine (Procardia XL), a blood pressure medication, to permit a decrease in the dose of glucocorticoid medication children take to treat congenital adrenal hyperplasia (CAH).",
                "NCTID": "NCT00000102",
                "Phase": "Phase 1/Phase 2",
                "Date": "ClinicalTrials.gov processed this data on October 25, 2016",
                "OverallStatus": "Completed",
                "OrgStudyId": "NCRR-M01RR01070-0506",
                "HealthyVolunteers": "No",
                "Keywords": [],
                "metadata_storage_last_modified": "2019-04-09T18:16:24Z",
                "metadata_storage_size": "33060",
                "metadata_content_type": null
            },
            . . . 
    ```

1. Adja `$select` hozzá a lekérdezési paramétert, `https://[service name].search.windows.net/indexes/clinical-trials-json-index/docs?search=*&$select=Gender,metadata_storage_size&api-version=2019-05-06&$count=true`hogy az eredmények kevesebb mezőre korlátozódjanak: .  Ebben a lekérdezésben 100 dokumentum egyezik, de alapértelmezés szerint az Azure Cognitive Search csak 50-et ad vissza az eredményekben.

   ![Paraméteres lekérdezés](media/search-semi-structured-data/lastquery.png "Lemásosított lekérdezés")

1. Egy összetettebb lekérdezés például `$filter=MinimumAge ge 30 and MaximumAge lt 75`a , amely csak azokat az eredményeket adja vissza, ahol a MinimumAge paraméterek 30-nál nagyobbak vagy egyenlők, és a MaximumAge 75-nél kisebb. Cserélje `$select` le a `$filter` kifejezést a kifejezésre.

   ![Részben strukturált keresés](media/search-semi-structured-data/metadatashort.png)

Használhatja a logikai operátorokat (és vagy nem) és az összehasonlító operátorokat (eq, ne, gt, lt, ge, le). A sztring-összehasonlítások megkülönböztetik a kis- és nagybetűket. További információt és példákat az [Egyszerű lekérdezés létrehozása](search-query-simple-examples.md)című témakörben talál.

> [!NOTE]
> A `$filter` paraméter csak olyan metaadatokkal működik, amelyek szűrhetőként lettek megjelölve az index létrehozásakor.

## <a name="reset-and-rerun"></a>Alaphelyzetbe állítás és ismételt futtatás

A fejlesztés korai kísérleti szakaszában a legpraktikusabb módszer az iteráció tervezésére, hogy törölje az objektumokat az Azure Cognitive Search szolgáltatásból, és lehetővé tegye a kód újraépítését. Az erőforrásnevek egyediek. Egy objektum törlése révén újból létrehozhatja azt ugyanazzal a névvel.

A portál segítségével törölheti az indexeket, indexelők és adatforrások. Vagy használja **a DELETE parancsot,** és adja meg az URL-címeket az egyes objektumokhoz. A következő parancs töröl egy indexelőt.

```http
DELETE https://[YOUR-SERVICE-NAME].search.windows.net/indexers/clinical-trials-json-indexer?api-version=2019-05-06
```

Sikeres törlés esetén a rendszer a 204-es állapotkódot adja vissza.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha saját előfizetésében dolgozik, a projekt végén célszerű eltávolítani azokat az erőforrásokat, amelyekre már nincs szüksége. A továbbra is futó erőforrások költségekkel járhatnak. Az erőforrások egyesével is törölhetők, de az erőforráscsoport törlésével egyszerre eltávolítható az összes erőforrás is.

Az erőforrásokat a portálon keresheti meg és kezelheti a bal oldali navigációs ablak Minden erőforrás vagy Erőforráscsoport hivatkozásával.

## <a name="next-steps"></a>További lépések

Most, hogy már ismeri az Azure Blob indexelésének alapjait, vessünk egy közelebbi pillantást a JSON-blobok indexelő konfigurációjának az Azure Storage-ban.

> [!div class="nextstepaction"]
> [JSON blob indexelésének konfigurálása](search-howto-index-json-blobs.md)