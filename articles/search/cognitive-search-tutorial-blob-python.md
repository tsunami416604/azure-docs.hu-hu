---
title: 'Oktatóanyag: Python és AI azure-blobok felett'
titleSuffix: Azure Cognitive Search
description: Egy Jupyter Python-jegyzetfüzet és az Azure Cognitive Search REST API-k használatával végighaladhatja a szövegkinyerés és a természetes nyelvi feldolgozás a Blob storage-ban lévő tartalmak feldolgozására.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.devlang: python
ms.topic: tutorial
ms.date: 02/26/2020
ms.openlocfilehash: e7708b0043b7f5baf2c12e813306595cc358a01d
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "78194054"
---
# <a name="tutorial-use-python-and-ai-to-generate-searchable-content-from-azure-blobs"></a>Oktatóanyag: A Python és a AI használatával kereshető tartalmat hozhat létre az Azure-blobokból

Ha strukturálatlan szöveggel vagy lemezképpel rendelkezik az Azure Blob storage-ban, az [AI-bővítési folyamat](cognitive-search-concept-intro.md) kinyerheti az információkat, és új tartalmat hozhat létre, amely hasznos a teljes szöveges kereséshez vagy a tudásbányászati forgatókönyvekhez. Bár egy folyamat képes feldolgozni a képeket, ez a Python-oktatóanyag a szövegre összpontosít, a nyelvfelismerés és a természetes nyelvi feldolgozás alkalmazásával új mezőket hozhat létre, amelyeket a lekérdezésekben, a mezőkben és a szűrőkben használhat.

Ez az oktatóanyag a Python és a [SEARCH REST API-kat](https://docs.microsoft.com/rest/api/searchservice/) használja a következő feladatok végrehajtásához:

> [!div class="checklist"]
> * Kezdje a teljes dokumentumokkal (strukturálatlan szöveggel), például PDF, HTML, DOCX és PPTX az Azure Blob storage-ban.
> * Definiáljon egy folyamatot, amely kibontja a szöveget, felismeri a nyelvet, felismeri az entitásokat, és észleli a kulcskifejezéseket.
> * Index definiálása a kimenet tárolására (nyers tartalom, valamint a folyamat által generált név-érték párok).
> * A folyamat végrehajtása átalakítások és elemzések indításához, valamint az index létrehozásához és betöltéséhez.
> * Fedezze fel az eredményeket a teljes szöveges keresés és a gazdag lekérdezésszintaxis használatával.

Ha nem rendelkezik Azure-előfizetéssel, nyisson meg egy [ingyenes fiókot,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) mielőtt elkezdené.

## <a name="prerequisites"></a>Előfeltételek

+ [Azure Storage](https://azure.microsoft.com/services/storage/)
+ [Anaconda 3,7](https://www.anaconda.com/distribution/#download-section)
+ Meglévő [keresési szolgáltatás](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) [létrehozása](search-create-service-portal.md) vagy keresése 

> [!Note]
> Használhatja az ingyenes szolgáltatást az oktatóanyaghoz. Az ingyenes keresési szolgáltatás három indexre, három indexelőre és három adatforrásra korlátozza. Az oktatóanyagban mindegyikből egyet hozhat majd létre. Mielőtt elkezdené, győződjön meg róla, hogy van hely a szolgáltatás, hogy elfogadja az új forrásokat.

## <a name="download-files"></a>Fájlok letöltése

1. Nyissa meg ezt a [OneDrive mappát,](https://1drv.ms/f/s!As7Oy81M_gVPa-LCb5lC_3hbS-4) és a bal felső sarokban kattintson a **Letöltés gombra** a fájlok számítógépre másolásához. 

1. Kattintson a jobb gombbal a zip fájlra, és válassza **az Összes kibontása**parancsot. Jelenleg 14 kép különböző típusú. Ehhez a gyakorlathoz 7-et fog használni.

## <a name="1---create-services"></a>1 - Szolgáltatások létrehozása

Ez az oktatóanyag az Azure Cognitive Search indexelési és lekérdezések, cognitive Services a háttérrendszer a ai-bővítés, és az Azure Blob storage az adatok biztosításához. Ez az oktatóanyag a Cognitive Services-en naponta 20 tranzakció ingyenes lefoglalása alatt marad, így csak a kereséshez és a tároláshoz szükséges szolgáltatások hoznak létre.

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

1. Kattintson **a + Container** egy tárolót, és nevezze el *fogaskerék-search-demo*.

1. Válassza *a fogaskerék-keresés-bemutató* lehetőséget, majd a **Feltöltés gombra** kattintva nyissa meg azt a mappát, ahová a fájlokat mentette. Jelölje ki az összes nem képfájlt. 7 aktád kellene, hogy legyen. A feltöltéshez kattintson az **OK** gombra.

   ![Mintafájlok feltöltése](media/cognitive-search-tutorial-blob/sample-files.png "Mintafájlok feltöltése")

1. Mielőtt elhagyja az Azure Storage-t, szerezzen be egy kapcsolati karakterláncot, hogy megfogalmazhassa a kapcsolatot az Azure Cognitive Search szolgáltatásban. 

   1. Tallózzon vissza a tárfiók áttekintése lapra (példaként *a blobstragewestust* használtuk). 
   
   1. A bal oldali navigációs ablakban válassza az **Access billentyűk** lehetőséget, és másolja az egyik kapcsolati karakterláncot. 

   A kapcsolati karakterlánc a következő példához hasonló URL-cím:

      ```http
      DefaultEndpointsProtocol=https;AccountName=cogsrchdemostorage;AccountKey=<your account key>;EndpointSuffix=core.windows.net
      ```

1. Mentse a kapcsolati karakterláncot a Jegyzettömbbe. Az adatforrás-kapcsolat beállításakor később szüksége lesz rá.

### <a name="cognitive-services"></a>Cognitive Services

A AI-bővítést a Cognitive Services támogatja, beleértve a Szövegelemzést és a Computer Vision-t a természetes nyelv és a képfeldolgozás érdekében. Ha a cél az volt, hogy egy tényleges prototípus vagy projekt, akkor ezen a ponton kiépítés Kognitív szolgáltatások (ugyanabban a régióban, mint az Azure Cognitive Search), így csatolja az indexelési műveletekhez.

Ebben a gyakorlatban azonban kihagyhatja az erőforrás-kiépítést, mert az Azure Cognitive Search a színfalak mögött csatlakozhat a Cognitive Serviceshez, és indexelőnként 20 ingyenes tranzakciót biztosíthat. Mivel ez az oktatóanyag 7 tranzakciót használ, az ingyenes kiosztás elegendő. Nagyobb projektek esetén tervezze meg a Cognitive Services üzembe helyezhető a felosztó-ki-megy S0 szinten. További információ: [A Cognitive Services csatolása.](cognitive-search-attach-cognitive-services.md)

### <a name="azure-cognitive-search"></a>Azure Cognitive Search

A harmadik összetevő az Azure Cognitive Search, amelyet [a portálon hozhat létre.](search-create-service-portal.md) Az ingyenes szint segítségével elvégezheti ezt a forgatókönyvet. 

Az Azure Blob storage-hoz is szánjon egy kis ideig a hozzáférési kulcs összegyűjtését. Továbbá, amikor elkezdi strukturálni a kérelmeket, meg kell adnia a végpont és a rendszergazdai api-kulcs hitelesítéséhez használt minden egyes kérelmet.

### <a name="get-an-admin-api-key-and-url-for-azure-cognitive-search"></a>Rendszergazdai API-kulcs és URL-cím beszerezni e cognitive Search szolgáltatáshoz

1. [Jelentkezzen be az Azure Portalra](https://portal.azure.com/), és a keresési szolgáltatás **áttekintése** lapon a keresési szolgáltatás nevét. A szolgáltatás nevét a végpont URL-címének áttekintésével ellenőrizheti. Ha a végpont `https://mydemo.search.windows.net`URL-címe lenne, `mydemo`a szolgáltatás neve a .

2. A **Beállítások** > **kulcsok**párbeszédpanelen szerezzen be egy rendszergazdai kulcsot a szolgáltatás teljes jogához. Két cserélhető rendszergazdai kulcs van, amelyek az üzletmenet folytonosságát biztosítják arra az esetre, ha át kell görgetnie egyet. Az elsődleges vagy másodlagos kulcsot objektumok hozzáadására, módosítására és törlésére irányuló kérelmeken használhatja.

   A lekérdezési kulcs beszerezése is. Ajánlott csak olvasási hozzáféréssel rendelkező lekérdezési kérelmeket kiadni.

   ![A szolgáltatás névének, a rendszergazdai és lekérdezési kulcsoknak a beszereznie](media/search-get-started-nodejs/service-name-and-keys.png)

Minden kérelemhez api-kulcs szükséges a szolgáltatásnak küldött minden kérés fejlécében. Az érvényes kulcs kérésenként megbízhatóságot hoz létre a kérelmet küldő alkalmazás és az azt kezelő szolgáltatás között.

## <a name="2---start-a-notebook"></a>2 - Notebook indítása

Hozza létre a jegyzetfüzetet az alábbi utasítások használatával, vagy töltsön le egy kész jegyzetfüzetet az [Azure-Search-python-samples tárból.](https://github.com/Azure-Samples/azure-search-python-samples/tree/master/Tutorial-AI-Enrichment)

Az Anaconda Navigator használatával elindíthatja a Jupyter notebookot, és létrehozhat egy új Python 3-jegyzetfüzetet.

A jegyzetfüzetben futtassa ezt a parancsfájlt a JSON-nal való munkához használt tárak betöltéséhez és a HTTP-kérelmek megfogalmazásához.

```python
import json
import requests
from pprint import pprint
```

Ugyanebben a jegyzetfüzetben adja meg az adatforrás, az index, az indexelő és a skillset nevét. Futtassa ezt a parancsfájlt az oktatóanyag nevének beállításához.

```python
# Define the names for the data source, skillset, index and indexer
datasource_name = "cogsrch-py-datasource"
skillset_name = "cogsrch-py-skillset"
index_name = "cogsrch-py-index"
indexer_name = "cogsrch-py-indexer"
```

A következő parancsfájlban cserélje le a keresési szolgáltatás helyőrzőit (YOUR-SEARCH-SERVICE-NAME) és a felügyeleti API-kulcsot (YOUR-ADMIN-API-KEY), majd futtassa azt a keresési szolgáltatás végpontjának beállításához.

```python
# Setup the endpoint
endpoint = 'https://<YOUR-SEARCH-SERVICE-NAME>.search.windows.net/'
headers = {'Content-Type': 'application/json',
           'api-key': '<YOUR-ADMIN-API-KEY>'}
params = {
    'api-version': '2019-05-06'
}
```

## <a name="3---create-the-pipeline"></a>3 - A folyamat létrehozása

Az Azure Cognitive Search, AI-feldolgozás történik indexelés (vagy adatbetöltés) során történik. A forgatókönyv ezen része négy objektumot hoz létre: adatforrás, indexdefiníció, skillset, indexelő. 

### <a name="step-1-create-a-data-source"></a>1. lépés: Adatforrás létrehozása

Egy [adatforrás-objektum](https://docs.microsoft.com/rest/api/searchservice/create-data-source) biztosítja a kapcsolati karakterláncot a fájlokat tartalmazó Blob-tárolóhoz.

A következő parancsfájlban cserélje le a PLACEHOLDER YOUR-BLOB-RESOURCE-CONNECTION-STRING helyőrzőt az előző lépésben létrehozott blob kapcsolati karakterláncára. Cserélje le a tároló helyőrző szövegét. Ezután futtassa a parancsfájlt `cogsrch-py-datasource`egy nevű adatforrás létrehozásához.

```python
# Create a data source
datasourceConnectionString = "<YOUR-BLOB-RESOURCE-CONNECTION-STRING>"
datasource_payload = {
    "name": datasource_name,
    "description": "Demo files to demonstrate cognitive search capabilities.",
    "type": "azureblob",
    "credentials": {
        "connectionString": datasourceConnectionString
    },
    "container": {
        "name": "<YOUR-BLOB-CONTAINER-NAME>"
    }
}
r = requests.put(endpoint + "/datasources/" + datasource_name,
                 data=json.dumps(datasource_payload), headers=headers, params=params)
print(r.status_code)
```

A kérelemnek egy 201-es állapotkódot kell visszaadnia, amely megerősíti a sikert.

Az Azure Portalon, a keresési szolgáltatás irányítópultján lapon ellenőrizze, hogy a fogaskerék-py-datasource jelenik meg az **adatforrások** listájában. A lap frissítéséhez kattintson a **Frissítés** gombra.

![Adatforrások csempe a portálon](./media/cognitive-search-tutorial-blob-python/py-data-source-tile.png "Adatforrások csempe a portálon")

### <a name="step-2-create-a-skillset"></a>2. lépés: Szakértelem létrehozása

Ebben a lépésben meghatározza az adatokra alkalmazandó dúsítási lépések készletét. Minden bővítési lépés egy *képesség*, a bővítési lépések készlete pedig a *képességcsoport*. Ez az oktatóanyag [beépített kognitív képességeket](cognitive-search-predefined-skills.md) használ a skillsethez:

+ [Entitásfelismerés](cognitive-search-skill-entity-recognition.md) a szervezetek nevének kibontása a blob tárolóban lévő tartalomból.

+ [Nyelvfelismeréssel](cognitive-search-skill-language-detection.md) azonosítja a tartalom nyelvét.

+ A [szöveg felosztásával](cognitive-search-skill-textsplit.md) a nagyméretű tartalmakat kisebb darabokra bontja, mielőtt meghívná a kulcskifejezések kinyerését. A kulcskifejezések kinyerése legfeljebb 50 000 karakter méterű bemeneteket fogad el. A mintafájlok közül néhányat fel kell osztani ahhoz, hogy beleférjen a korlátozásba.

+ A [Kulcskifejezések kinyerése](cognitive-search-skill-keyphrases.md) lehívja a leggyakoribb kulcskifejezéseket. 

Futtassa a következő parancsfájlt `cogsrch-py-skillset`a .

```python
# Create a skillset
skillset_payload = {
    "name": skillset_name,
    "description":
    "Extract entities, detect language and extract key-phrases",
    "skills":
    [
        {
            "@odata.type": "#Microsoft.Skills.Text.EntityRecognitionSkill",
            "categories": ["Organization"],
            "defaultLanguageCode": "en",
            "inputs": [
                {
                    "name": "text", "source": "/document/content"
                }
            ],
            "outputs": [
                {
                    "name": "organizations", "targetName": "organizations"
                }
            ]
        },
        {
            "@odata.type": "#Microsoft.Skills.Text.LanguageDetectionSkill",
            "inputs": [
                {
                    "name": "text", "source": "/document/content"
                }
            ],
            "outputs": [
                {
                    "name": "languageCode",
                    "targetName": "languageCode"
                }
            ]
        },
        {
            "@odata.type": "#Microsoft.Skills.Text.SplitSkill",
            "textSplitMode": "pages",
            "maximumPageLength": 4000,
            "inputs": [
                {
                    "name": "text",
                    "source": "/document/content"
                },
                {
                    "name": "languageCode",
                    "source": "/document/languageCode"
                }
            ],
            "outputs": [
                {
                    "name": "textItems",
                    "targetName": "pages"
                }
            ]
        },
        {
            "@odata.type": "#Microsoft.Skills.Text.KeyPhraseExtractionSkill",
            "context": "/document/pages/*",
            "inputs": [
                {
                    "name": "text", "source": "/document/pages/*"
                },
                {
                    "name": "languageCode", "source": "/document/languageCode"
                }
            ],
            "outputs": [
                {
                    "name": "keyPhrases",
                    "targetName": "keyPhrases"
                }
            ]
        }
    ]
}

r = requests.put(endpoint + "/skillsets/" + skillset_name,
                 data=json.dumps(skillset_payload), headers=headers, params=params)
print(r.status_code)
```

A kérelemnek egy 201-es állapotkódot kell visszaadnia, amely megerősíti a sikert.

A kulcskifejezés-kivonási szakértelem minden egyes oldalon érvényes. Ha a környezetet a beállításra `"document/pages/*"`állítja, akkor ezt a dúsítót a dokumentum/oldal tömb minden egyes tagjára futtatja (a dokumentum minden egyes oldalához).

Minden képesség a dokumentum tartalmán fut le. A feldolgozás során az Azure Cognitive Search feltöri az egyes dokumentumokat, hogy különböző fájlformátumokból olvasson be tartalmakat. A forrásfájlban található szöveg `content` egy mezőbe kerül, minden dokumentumhoz egy- Ezért állítsa be `"/document/content"`a bemenetet a .

Alább a képességcsoport grafikai ábrázolása látható.

![A skillset megismerése](media/cognitive-search-tutorial-blob/skillset.png "A skillset megismerése")

A kimenetek leképezhetők egy indexre, amely et egy alsóbb rétegbeli szakértelem bemeneteként használnak, vagy mindkettőhöz, mint a nyelvi kód esetében. Az indexben a nyelvkód a szűréskor lehet hasznos. A nyelvkódot bemenetként a szövegelemzési képességek használják, a szótördeléssel kapcsolatos nyelvi szabályok megadásához.

A képességcsoportok alapvető tudnivalóval kapcsolatos bővebb információkért lásd: [Képességcsoport megadása](cognitive-search-defining-skillset.md).

### <a name="step-3-create-an-index"></a>3. lépés: Index létrehozása

Ebben a szakaszban úgy határozhatja meg az indexsémát, hogy megadja a kereshető indexbe felvenni kívánt mezőket, és beállítja az egyes mezők keresési attribútumait. A mezők típussal is rendelkeznek, emellett olyan attribútumokat tartalmazhatnak, amelyek meghatározzák a mező használatának módját (kereshető, rendezhető stb.). Az indexben szereplő mezőneveknek nem kell pontosan megegyezniük a forrásban szereplő mezők nevével. Egy későbbi lépésben mezőleképezéseket fog hozzáadni egy indexelőhöz a forrás-cél mezőkhöz való csatlakozás céljából. Ebben a lépésben a keresőalkalmazásra vonatkozó mezőelnevezési konvenciók használatával határozza meg az indexet.

A gyakorlat során az alábbi mezőket és mezőtípusokat használjuk:

| mezőnevek: | id         | content   | languageCode | keyPhrases         | organizations     |
|--------------|----------|-------|----------|--------------------|-------------------|
| mezőtípusok: | Edm.String|Edm.String| Edm.String| List<Edm.String>  | List<Edm.String>  |

Futtassa ezt a `cogsrch-py-index`parancsfájlt a nevű index létrehozásához.

```python
# Create an index
index_payload = {
    "name": index_name,
    "fields": [
        {
            "name": "id",
            "type": "Edm.String",
            "key": "true",
            "searchable": "true",
            "filterable": "false",
            "facetable": "false",
            "sortable": "true"
        },
        {
            "name": "content",
            "type": "Edm.String",
            "sortable": "false",
            "searchable": "true",
            "filterable": "false",
            "facetable": "false"
        },
        {
            "name": "languageCode",
            "type": "Edm.String",
            "searchable": "true",
            "filterable": "false",
            "facetable": "false"
        },
        {
            "name": "keyPhrases",
            "type": "Collection(Edm.String)",
            "searchable": "true",
            "filterable": "false",
            "facetable": "false"
        },
        {
            "name": "organizations",
            "type": "Collection(Edm.String)",
            "searchable": "true",
            "sortable": "false",
            "filterable": "false",
            "facetable": "false"
        }
    ]
}

r = requests.put(endpoint + "/indexes/" + index_name,
                 data=json.dumps(index_payload), headers=headers, params=params)
print(r.status_code)
```

A kérelemnek egy 201-es állapotkódot kell visszaadnia, amely megerősíti a sikert.

Az index definiálásáról az [Index létrehozása (Azure Cognitive Search REST API) című](https://docs.microsoft.com/rest/api/searchservice/create-index)témakörben olvashat bővebben.

### <a name="step-4-create-and-run-an-indexer"></a>4. lépés: Indexelő létrehozása és futtatása

Egy [indexelő](https://docs.microsoft.com/rest/api/searchservice/create-indexer) vezéreli a folyamatot. Az eddig létrehozott három összetevő (adatforrás, skillset, index) egy indexelő bemenete. Az indexelő létrehozása az Azure Cognitive Search az az esemény, amely a teljes folyamat mozgásba lép. 

Ha ezeket az objektumokat egy indexelőben szeretné összekötni, meg kell határoznia a mezőleképezéseket.

+ A fieldMappings feldolgozása a skillset előtt lesz feldolgozva, leképezve a forrásmezőket az adatforrásból az index célmezőibe. Ha a mezőnevek és -típusok mindkét végén azonosak, nincs szükség leképezésre.

+ A outputFieldMappings feldolgozása a skillset után, hivatkozva sourceFieldNames, amelyek nem léteznek, amíg a dokumentum repedés vagy dúsítása létrehozza őket. A targetFieldName egy index mezője.

A kimenetekhez való bemenetek csatlakoztatása mellett mezőleképezéseket is használhat az adatstruktúrák simításához. További információt a [Bővített mezők leképezése kereshető indexhez](cognitive-search-output-field-mapping.md)című témakörben talál.

Futtassa ezt a parancsfájlt egy indexelő nevű létrehozásához. `cogsrch-py-indexer`

```python
# Create an indexer
indexer_payload = {
    "name": indexer_name,
    "dataSourceName": datasource_name,
    "targetIndexName": index_name,
    "skillsetName": skillset_name,
    "fieldMappings": [
        {
            "sourceFieldName": "metadata_storage_path",
            "targetFieldName": "id",
            "mappingFunction":
            {"name": "base64Encode"}
        },
        {
            "sourceFieldName": "content",
            "targetFieldName": "content"
        }
    ],
    "outputFieldMappings":
    [
        {
            "sourceFieldName": "/document/organizations",
            "targetFieldName": "organizations"
        },
        {
            "sourceFieldName": "/document/pages/*/keyPhrases/*",
            "targetFieldName": "keyPhrases"
        },
        {
            "sourceFieldName": "/document/languageCode",
            "targetFieldName": "languageCode"
        }
    ],
    "parameters":
    {
        "maxFailedItems": -1,
        "maxFailedItemsPerBatch": -1,
        "configuration":
        {
            "dataToExtract": "contentAndMetadata",
            "imageAction": "generateNormalizedImages"
        }
    }
}

r = requests.put(endpoint + "/indexers/" + indexer_name,
                 data=json.dumps(indexer_payload), headers=headers, params=params)
print(r.status_code)
```

A kérelem nek hamarosan 201-es állapotkódot kell visszaadnia, azonban a feldolgozás több percet is igénybe vehet. Bár az adatkészlet kicsi, az analitikus képességek, például a képelemzés, számításilag intenzívek és időt vesznek igénybe.

[Figyelheti az indexelő állapotát,](#check-indexer-status) hogy megállapítsa, mikor fut vagy fejeződik be az indexelő.

> [!TIP]
> Az indexelő létrehozása elindítja a folyamatot. Ha probléma merül fel az adatok elérése, a bemenetek és kimenetek leképezése vagy a műveletek sorrendjének elérése kor, akkor ebben a szakaszban jelenik meg. A folyamat kód- vagy parancsfájlmódosításokkal történő újbóli futtatásához először törölnie kell az objektumokat. További információk: [Alaphelyzetbe állítás és ismételt futtatás](#reset).

#### <a name="about-the-request-body"></a>A kérelem törzséről

A szkript -1 értéket állít be a `"maxFailedItems"` paraméterhez, amely utasítja az indexelőmotort, hogy hagyja figyelmen kívül az adatimportálás közben felmerülő hibákat. Ez azért hasznos, mert az adatforrás kevés dokumentumot tartalmaz. Nagyobb méretű adatforrás esetén 0-nál nagyobb értéket kell megadnia.

Figyelje meg a `"dataToExtract":"contentAndMetadata"` utasítást a konfigurációs paraméterekben. Ez az utasítás arra utasítja az indexelőt, hogy a tartalmat különböző fájlformátumokból és az egyes fájlokhoz kapcsolódó metaadatokból nyerje ki.

Tartalom kinyerésekor az `imageAction` beállításával kinyerheti a szöveget az adatforrásban talált képekből. A `"imageAction":"generateNormalizedImages"` konfiguráció az OCR szakértelemmel és a szövegegyesítési szakértelemmel kombinálva arra utasítja az indexelőt, hogy bontsa ki a szöveget a képekből (például a "stop" szó egy közlekedési stop táblából), és ágyazza be a tartalommező részeként. Ez a viselkedés a dokumentumokba ágyazott képekre (a PDF-dokumentumban lévő képre) és az adatforrásban található képekre is vonatkozik, például egy JPG-fájlra.

<a name="check-indexer-status"></a>

## <a name="4---monitor-indexing"></a>4 - Monitor indexelés

Az indexelő meghatározását követően az indexelő a kérés elküldésekor automatikusan lefut. Az indexelés a vártnál tovább is eltarthat attól függően, hogy mely kognitív képességeket adta meg. Ha meg szeretné tudni, hogy az indexelő feldolgozása befejeződött-e, futtassa a következő parancsfájlt.

```python
# Get indexer status
r = requests.get(endpoint + "/indexers/" + indexer_name +
                 "/status", headers=headers, params=params)
pprint(json.dumps(r.json(), indent=1))
```

A válaszban figyelje a "lastResult" az "állapot" és a "endTime" értékeket. Rendszeresen futtassa a parancsfájlt az állapot ellenőrzéséhez. Amikor az indexelő befejeződött, az állapot "sikeres" lesz, egy "endTime" lesz megadva, és a válasz tartalmazza a dúsítás során előforduló hibákat és figyelmeztetéseket.

![Az indexelő létrejön](./media/cognitive-search-tutorial-blob-python/py-indexer-is-created.png "Az indexelő létrejön")

A figyelmeztetések bizonyos forrásfájl- és képességkombinációk esetében gyakoriak, és nem mindig utalnak hibára. Sok figyelmeztetés jóindulatú. Ha például olyan JPEG-fájlt indexel, amely nem tartalmaz szöveget, a figyelmeztetés jelenik meg ezen a képernyőképen.

![Példa indexelő figyelmeztetés](./media/cognitive-search-tutorial-blob-python/py-indexer-warning-example.png "Példa indexelő figyelmeztetés")

## <a name="5---search"></a>5 - Keresés

Az indexelés befejezését követően futtasson olyan lekérdezéseket, amelyek egyéni mezők tartalmát adják vissza. Alapértelmezés szerint az Azure Cognitive Search az 50 legjobb eredményt adja vissza. A mintaadatok mérete kicsi, ezért az alapértelmezett beállítás megfelelő. Azonban nagyobb méretű adatkészletek esetén előfordulhat, hogy a lekérdezési sztringben meg kell adnia a megfelelő paramétereket, hogy a rendszer több eredményt adjon vissza. További információt a Találatokat az Azure Cognitive Search alkalmazásban található [oldaltalálatai](search-pagination-page-layout.md)között olvashat.

Ellenőrzési lépésként az összes mezőt megjelenítő indexdefiníciót kapja.

```python
# Query the service for the index definition
r = requests.get(endpoint + "/indexes/" + index_name,
                 headers=headers, params=params)
pprint(json.dumps(r.json(), indent=1))
```

Az eredményeknek a következő példához hasonlóan kell kinézniük. A képernyőkép csak a válasz egy részét jeleníti meg.

![Lekérdezési index az összes mezőhöz](./media/cognitive-search-tutorial-blob-python/py-query-index-for-fields.png "Az összes mező indexének lekérdezése")

A kimenet az egyes mezők nevét, típusát és attribútumait tartalmazó indexséma.

Indítson egy másodlagos lekérdezést a `"*"` kifejezésre egy mező, például az `organizations` összes tartalmának lekéréséhez.

```python
# Query the index to return the contents of organizations
r = requests.get(endpoint + "/indexes/" + index_name +
                 "/docs?&search=*&$select=organizations", headers=headers, params=params)
pprint(json.dumps(r.json(), indent=1))
```

Az eredményeknek a következő példához hasonlóan kell kinézniük. A képernyőkép csak a válasz egy részét jeleníti meg.

![Szervezetek tartalmának lekérdezési indexe](./media/cognitive-search-tutorial-blob-python/py-query-index-for-organizations.png "Az index lekérdezése a szervezetek tartalmának visszaadásához")

Ismételje meg a műveletet további mezők esetén: tartalom, languageCode, keyPhrases és szervezetek ebben a gyakorlatban. Egyszerre több mezőt is lekérhet a vesszővel elválasztott listát használó `$select` megadásával.

A lekérdezési sztring összetettségétől és hosszától függően használhatja a GET vagy a POST metódust. További információkért lásd: [Lekérdezés a REST API-val](https://docs.microsoft.com/rest/api/searchservice/search-documents).

<a name="reset"></a>

## <a name="reset-and-rerun"></a>Alaphelyzetbe állítás és ismételt futtatás

A fejlesztés korai kísérleti szakaszában a legpraktikusabb módszer az iteráció tervezésére, hogy törölje az objektumokat az Azure Cognitive Search szolgáltatásból, és lehetővé tegye a kód újraépítését. Az erőforrásnevek egyediek. Egy objektum törlése révén újból létrehozhatja azt ugyanazzal a névvel.

A portál segítségével törölheti az indexeket, indexelőket, adatforrásokat és skillseteket. Az indexelő törlésekor tetszés szerint, szelektíven törölheti az indexet, a skillsetet és az adatforrást egyszerre.

![Keresési objektumok törlése](./media/cognitive-search-tutorial-blob-python/py-delete-indexer-delete-all.png "Keresési objektumok törlése a portálon")

Parancsfájl használatával is törölheti őket. A következő parancsfájl bemutatja, hogyan kell törölni egy skillset. 

```python
# delete the skillset
r = requests.delete(endpoint + "/skillsets/" + skillset_name,
                    headers=headers, params=params)
pprint(json.dumps(r.json(), indent=1))
```

Sikeres törlés esetén a rendszer a 204-es állapotkódot adja vissza.

## <a name="takeaways"></a>Legfontosabb ismeretek

Ez az oktatóanyag a bővített indexelőfolyamat a komponensek (adatforrás, képességcsoport, index és indexelő) létrehozásával való összeállításának alapvető lépéseit ismerteti.

[Bevezették a beépített készségeket,](cognitive-search-predefined-skills.md) valamint a skillset-definíciókat, valamint a készségek összeláncolódását a bemenetek és outputok révén. Azt is `outputFieldMappings` megtanulta, hogy az indexelő definíciója szükséges a bővített értékek útválasztása a folyamatból egy kereshető index egy Azure Cognitive Search szolgáltatás.

Végül megtanulta, hogyan tesztelheti az eredményeket, és hogyan állíthatja vissza a rendszert további ismétlések esetén. Megtanulta, hogy ha lekérdezéseket futtat az indexen, az a bővített indexelési folyamat által létrehozott kimenetet adja vissza. Ebben a kiadásban szerepel egy belső szerkezetek (a rendszer által létrehozott bővített dokumentumok) megjelenítésére szolgáló mechanizmus. Azt is megtanulta, hogyan ellenőrizheti az indexelő állapotát, és milyen objektumokat kell törölni a folyamat újrafuttatása előtt.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha saját előfizetésében dolgozik, a projekt végén célszerű eltávolítani azokat az erőforrásokat, amelyekre már nincs szüksége. A továbbra is futó erőforrások költségekkel járhatnak. Az erőforrások egyesével is törölhetők, de az erőforráscsoport törlésével egyszerre eltávolítható az összes erőforrás is.

Az erőforrásokat a portálon keresheti meg és kezelheti a bal oldali navigációs ablak Minden erőforrás vagy Erőforráscsoport hivatkozásával.

## <a name="next-steps"></a>További lépések

Most, hogy már ismeri az AI-bővítési folyamat összes objektumát, vessünk egy közelebbi pillantást a skillset definíciókra és az egyéni készségekre.

> [!div class="nextstepaction"]
> [Hogyan hozzunk létre egy skillset](cognitive-search-defining-skillset.md)
