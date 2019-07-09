---
title: 'Python-oktatóprogram: Meghívja a Cognitive Servicest egy indexelési folyamat – Azure Search'
description: '. Lépés: adatok kinyerése, természetes nyelvi és image AI példán keresztül feldolgozása az Azure Search egy Jupyter Python notebook használatával. Kinyert adatok indexelése és lekérdezés könnyen elérhetőek.'
manager: cgronlun
author: LisaLeib
services: search
ms.service: search
ms.devlang: python
ms.topic: tutorial
ms.date: 06/04/2019
ms.author: v-lilei
ms.openlocfilehash: b1166e0acdbc9371b1c7ca2361fc6ebb7479b6a7
ms.sourcegitcommit: 2e4b99023ecaf2ea3d6d3604da068d04682a8c2d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/09/2019
ms.locfileid: "67672071"
---
# <a name="python-tutorial-call-cognitive-services-apis-in-an-azure-search-indexing-pipeline"></a>Python-oktatóprogram: Az egy Azure Search szolgáltatásban az indexelés folyamat a Cognitive Services API-k meghívása

Eben az oktatóanyagban az adatok Azure Search-beli bővítésének programozási mechanikájával fogjuk megismertetni, *kognitív képességek* használatával. Képességek természetes nyelvi feldolgozást (NLP) és a lemezkép elemzési képességeket biztosítanak a Cognitive Services élvezik. Képességcsoport összeállítás és konfigurációja kibonthatja a szöveget, és képet vagy beolvasott dokumentum fájl szöveges ábrázolását. Nyelv, az entitások, kulcskifejezéseket és további is felismeri. Ez az Azure Search-index, mesterséges Intelligencia végrehajtott információbeolvasás egy indexelési folyamat használatával létrehozott gazdag további tartalmat. 

Ebben az oktatóanyagban a Python használata lesz a következő feladatokat végezheti el:

> [!div class="checklist"]
> * Olyan indexelőfolyamat létrehozása, amely kibővíti a mintaadatokat egy indexhez vezető útvonalon
> * Beépített képességek használata: entitásfelismerés, nyelvfelismerés, szövegmanipuláció és kulcskifejezés-kinyerés
> * Megtudhatja, hogyan kapcsolhat össze képességeket egy képességcsoporton belül a bemenetek kimenetekhez való társításával
> * Kérések végrehajtása és az eredmények áttekintése
> * Az index és az indexelők alaphelyzetbe állítása további fejlesztés céljából

A kimenet egy teljes szöveges, kereshető index az Azure Search szolgáltatásban. Az indexet egyéb standard képességekkel, például [szinonimákkal](search-synonyms.md), [pontozási profilokkal](https://docs.microsoft.com/rest/api/searchservice/add-scoring-profiles-to-a-search-index), [elemzőkkel](search-analyzers.md) és [szűrőkkel](search-filters.md) is bővítheti. 

Ebben az oktatóanyagban az ingyenes szolgáltatás fut, de az ingyenes tranzakciók száma korlátozott a naponta 20 dokumentumokhoz. Ha azt szeretné, ez az oktatóanyag többször futtatása ugyanazon a napon, használja az egy kisebb fájlt, hogy a további futtatási illeszkednek.

> [!NOTE]
> Bontsa ki a hatókört által a feldolgozás, gyakoriságának növelése további dokumentumok hozzáadása, vagy adja hozzá a további AI-algoritmusokat, kell [számlázható Cognitive Services-erőforrás csatolása](cognitive-search-attach-cognitive-services.md). A díjakat API-k hívásakor, a Cognitive Services, valamint a lemezkép kinyerése a az Azure Search-dokumentumfeltörést fázis részeként. Nem számítunk fel díjat a szövegkinyerés dokumentumok közül.
>
> Végrehajtási beépített képességek a meglévő díjakat [használatalapú-as-, a Cognitive Services nyissa meg az árat](https://azure.microsoft.com/pricing/details/cognitive-services/). A kép kinyerési díjszabás leírása a [díjszabását ismertető oldalt az Azure Search](https://go.microsoft.com/fwlink/?linkid=2042400).

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

Ebben az oktatóanyagban a következő szolgáltatások, eszközök és adatok szerepelnek. 

+ [Az Azure storage-fiók létrehozása](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account) a mintaadatok tárolásához. Ellenőrizze, hogy a storage-fiókot az Azure Search és ugyanabban a régióban.

+ [Anaconda 3.x](https://www.anaconda.com/distribution/#download-section), így a Python 3.x és Jupyter-Notebookjait.

+ [Mintaadatok](https://1drv.ms/f/s!As7Oy81M_gVPa-LCb5lC_3hbS-4) lemezkészletből egy kis fájlt különböző típusú. 

+ [Az Azure Search szolgáltatás létrehozása](search-create-service-portal.md) vagy [keresse meg a meglévő service](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) az aktuális előfizetésben. Ebben az oktatóanyagban egy ingyenes szolgáltatás használhatja.

## <a name="get-a-key-and-url"></a>Egy kulcsot és egy URL-cím beszerzése

Az Azure Search szolgáltatás kezelése, szüksége lesz a szolgáltatás URL-CÍMÉT és a egy hozzáférési kulcsot. Mindkettőhöz létrejön egy keresési szolgáltatás, így ha hozzáadta az előfizetéséhez az Azure Searchöt, kövesse az alábbi lépéseket a szükséges információk beszerzéséhez:

1. [Jelentkezzen be az Azure Portalon](https://portal.azure.com/), és a search szolgáltatás **áttekintése** lapon, az URL-cím lekéréséhez. A végpontok például a következőképpen nézhetnek ki: `https://mydemo.search.windows.net`.

1. A **beállítások** > **kulcsok**, a szolgáltatás a teljes körű rendszergazdai kulcs beszerzése. Nincsenek két felcserélhetők adminisztrációs kulcsot, az üzletmenet folytonosságának megadott abban az esetben egy vihető kell. Használható vagy az elsődleges vagy másodlagos kulcsot a kérések hozzáadása, módosítása és törlése objektumokat.

![Egy HTTP-végpontját és hozzáférési kulcs lekérése](media/search-get-started-postman/get-url-key.png "HTTP végpontját és hozzáférési kulcs beszerzése")

Minden kérelemhez szükséges halasztása minden kérelemnél a szolgáltatásnak küldött api-kulcsát. Érvényes kulcs megbízhatósági, egy kérelem alapon, a kérés és az azt kezelő szolgáltatás küldő alkalmazás közötti kapcsolatot hoz létre.

## <a name="prepare-sample-data"></a>Mintaadatok létrehozása

A bővítési folyamat az Azure-adatforrásokból hívja le az adatokat. A forrásadatoknak egy [Azure Search-indexelő](search-indexer-overview.md) által támogatott adatforrástípusból kell származniuk. Az Azure Table Storage a kognitív keresés nem támogatott. Ebben a gyakorlatban a blobtárolót használjuk több tartalomtípus bemutatásához.

1. [Jelentkezzen be az Azure Portalon](https://portal.azure.com)lépjen az Azure storage-fiókot, kattintson a **Blobok**, és kattintson a **+ tároló**.

1. [Hozzon létre egy blobtárolót](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal) mintaadatok tárolásához. A nyilvános hozzáférés szintje beállíthatja az érvényes értékek bármelyikére.

1. A tároló létrehozása után nyissa meg és jelölje ki **feltöltése** a parancssávon az előző lépésben letöltött minta fájlok feltöltéséhez.

   ![Forrásfájlok az Azure Blob Storage-ban](./media/cognitive-search-quickstart-blob/sample-data.png)

1. A mintafájlok feltöltése után kérje le a tároló nevét és a kapcsolati karakterláncot a Blob Storage-hoz. Ezt megteheti úgy is, ha megkeresi a tárfiókját az Azure Portalon. Kattintson a **hozzáférési kulcsok**, majd másolja a **kapcsolati karakterlánc** mező.

A kapcsolati karakterlánc lesz ebben a formátumban: `DefaultEndpointsProtocol=https;AccountName=<YOUR-STORAGE-ACCOUNT-NAME>;AccountKey=<YOUR-STORAGE-ACCOUNT-KEY>;EndpointSuffix=core.windows.net`

Tartsa meg a kapcsolati karakterlánc praktikus. Szüksége lesz rá egy későbbi lépésben.

A kapcsolati karakterlánc megadásának egyéb módjai is vannak, például egy közös hozzáférésű jogosultságkód biztosítása. Az adatforrások hitelesítő adataival kapcsolatos további információért lásd: [Indexelés az Azure Blob Storage-ban](search-howto-indexing-azure-blob-storage.md#Credentials).

## <a name="create-a-jupyter-notebook"></a>Jupyter notebook létrehozása

> [!Note]
> Ez a cikk bemutatja, hogyan hozhat létre egy adatforrást, index, indexelő és Python-szkriptek sorozatát képességek alkalmazási lehetőségét. A teljes körű jegyzetfüzet-példa letöltéséhez nyissa meg a [Azure-Search-python-samples-tárház](https://github.com/Azure-Samples/azure-search-python-samples/tree/master/Tutorial-AI-Enrichment-Jupyter-Notebook).

Indítsa el a Jupyter Notebookot, és hozzon létre egy új Python 3-jegyzetfüzetet Anaconda-kezelő használatával.

## <a name="connect-to-azure-search"></a>Kapcsolódás az Azure Search

A notebook futtassa ezt a szkriptet a JSON-fájllal működik, és HTTP-kérések kidolgozásában függvénykönyvtárak betöltésére.

```python
import json
import requests
from pprint import pprint
```

Ezt követően adja meg az adatforrás, index, indexelő és indexmezők nevét. Futtassa ezt a szkriptet a neveket, ebben az oktatóanyagban meg.

```python
#Define the names for the data source, skillset, index and indexer
datasource_name="cogsrch-py-datasource"
skillset_name="cogsrch-py-skillset"
index_name="cogsrch-py-index"
indexer_name="cogsrch-py-indexer"
```

> [!Tip]
> Egy ingyenes szolgáltatás, az Ön legfeljebb három indexek, indexelők és adatforrások. Az oktatóanyagban mindegyikből egyet hozhat majd létre. Győződjön meg arról, hogy a hely az új objektumokat hozhat létre, mielőtt továbblép bármely.

A következő parancsfájlt cserélje le a helyőrzőket a keresési szolgáltatáshoz (a SEARCH-szolgáltatás neve) és rendszergazdai API-kulcsának (a-ADMIN-API-kulcs), és futtassa azt állíthatja be a keresési szolgáltatás végpontját.

```python
#Setup the endpoint
endpoint = 'https://<YOUR-SEARCH-SERVICE-NAME>.search.windows.net/'
headers = {'Content-Type': 'application/json',
        'api-key': '<YOUR-ADMIN-API-KEY>' }
params = {
    'api-version': '2019-05-06'
}
```

## <a name="create-a-data-source"></a>Adatforrás létrehozása

Most, hogy előkészítette a szolgáltatásokat és a forrásfájlokat, hozzáláthat az indexelőfolyamat komponenseinek összeállításához. A kezdő egy adatforrás-objektum, amely arra utasítja az Azure Search hogyan kérheti le a külső adatforrást.

Cserélje le a helyőrző a BLOB-RESOURCE-KAPCSOLATI karakterlánc a következő parancsfájlt, a blob az előző lépésben létrehozott kapcsolati karakterláncára. Ezután futtassa a szkriptet nevű adatforrás létrehozása `cogsrch-py-datasource`.

```python
#Create a data source
datasourceConnectionString = "<YOUR-BLOB-RESOURCE-CONNECTION-STRING>"
datasource_payload = {
    "name": datasource_name,
    "description": "Demo files to demonstrate cognitive search capabilities.",
    "type": "azureblob",
    "credentials": {
    "connectionString": datasourceConnectionString
   },
    "container": {
     "name": "basic-demo-data-pr"
   }
}
r = requests.put( endpoint + "/datasources/" + datasource_name, data=json.dumps(datasource_payload), headers=headers, params=params )
print (r.status_code)
```

A kérelem sikeres megerősítése 201-es állapotkódot kell visszaadnia.

Az Azure Portalon, a keresési szolgáltatás irányítópult lapon győződjön meg arról, hogy a cogsrch-py-adatforrás megjelenik a **adatforrások** listája. Kattintson a **frissítése** frissíteni a lapot.

![Adatforrások csempe a portálon](./media/cognitive-search-tutorial-blob-python/py-data-source-tile.png "Adatforrások csempe a portálon")

## <a name="create-a-skillset"></a>Képességcsoport létrehozása

Ebben a lépésben határoz Adatbővítés ismertetett lépések a alkalmazni az adatokon. Minden bővítési lépés egy *képesség*, a bővítési lépések készlete pedig a *képességcsoport*. Ebben az oktatóanyagban [beépített kognitív képességeket](cognitive-search-predefined-skills.md) készségeitől számára:

+ [Nyelvfelismeréssel](cognitive-search-skill-language-detection.md) azonosítja a tartalom nyelvét.

+ A [szöveg felosztásával](cognitive-search-skill-textsplit.md) a nagyméretű tartalmakat kisebb darabokra bontja, mielőtt meghívná a kulcskifejezések kinyerését. A kulcskifejezések kinyerése legfeljebb 50 000 karakter méterű bemeneteket fogad el. A mintafájlok közül néhányat fel kell osztani ahhoz, hogy beleférjen a korlátozásba.

+ [Entitások felismerése](cognitive-search-skill-entity-recognition.md) szervezetek nevei kinyerését tartalom blob-tárolóban.

+ A [Kulcskifejezések kinyerése](cognitive-search-skill-keyphrases.md) lehívja a leggyakoribb kulcskifejezéseket. 

### <a name="python-script"></a>Python-szkript
Futtassa a következő szkriptet egy nevű képességcsoport létrehozása `cogsrch-py-skillset`.

```python
#Create a skillset
skillset_payload = {
  "name": skillset_name,
  "description":
  "Extract entities, detect language and extract key-phrases",
  "skills":
  [
    {
      "@odata.type": "#Microsoft.Skills.Text.EntityRecognitionSkill",
      "categories": [ "Organization" ],
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
      "textSplitMode" : "pages",
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
          "name":"languageCode", "source": "/document/languageCode"
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

r = requests.put(endpoint + "/skillsets/" + skillset_name, data=json.dumps(skillset_payload), headers=headers, params=params)
print(r.status_code)
```

A kérelem sikeres megerősítése 201-es állapotkódot kell visszaadnia.

A kulcsfontosságú kifejezések kinyerése szakértelem minden lapon a alkalmazni. Úgy, hogy a környezet `"document/pages/*"`, futtatja a enricher (a dokumentum oldalainak) a dokumentumoldal/tömb minden tagja számára.

Minden képesség a dokumentum tartalmán fut le. A feldolgozás során az Azure Search a különféle fájlformátumok tartalmának olvasása érdekében minden dokumentumot tördel. A forrásfájl található szöveg kerül egy `content` mező, egy az egyes dokumentumok. Állítsa be, a bemeneti `"/document/content"`.

Alább a képességcsoport grafikai ábrázolása látható.

![Képességcsoport értelmezése](media/cognitive-search-tutorial-blob/skillset.png "Képességcsoport értelmezése")

Egy index használja majd bemenetként az alsóbb rétegbeli szakértelem vagy mindkettőhöz, mint a nyelvi kód kimenetek is le lehet képezni. Az indexben a nyelvkód a szűréskor lehet hasznos. A nyelvkódot bemenetként a szövegelemzési képességek használják, a szótördeléssel kapcsolatos nyelvi szabályok megadásához.

A képességcsoportok alapvető tudnivalóval kapcsolatos bővebb információkért lásd: [Képességcsoport megadása](cognitive-search-defining-skillset.md).

## <a name="create-an-index"></a>Index létrehozása

Ebben a szakaszban adja meg az indexsémát megadásával az indexben kereshető mezőket, és az egyes mezőkhöz a keresési tulajdonságok beállításához. A mezők típussal is rendelkeznek, emellett olyan attribútumokat tartalmazhatnak, amelyek meghatározzák a mező használatának módját (kereshető, rendezhető stb.). Az indexben szereplő mezőneveknek nem kell pontosan megegyezniük a forrásban szereplő mezők nevével. Egy későbbi lépésben mezőleképezéseket fog hozzáadni egy indexelőhöz a forrás-cél mezőkhöz való csatlakozás céljából. Ebben a lépésben a keresőalkalmazásra vonatkozó mezőelnevezési konvenciók használatával határozza meg az indexet.

A gyakorlat során az alábbi mezőket és mezőtípusokat használjuk:

| mezőnevek: | id         | content   | languageCode | keyPhrases         | organizations     |
|--------------|----------|-------|----------|--------------------|-------------------|
| mezőtípusok: | Edm.String|Edm.String| Edm.String| List<Edm.String>  | List<Edm.String>  |

Futtassa ezt a szkriptet az nevű index létrehozásához `cogsrch-py-index`.

```python
#Create an index
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

r = requests.put(endpoint + "/indexes/" + index_name, data=json.dumps(index_payload), headers=headers, params=params)
print(r.status_code)
```

A kérelem sikeres megerősítése 201-es állapotkódot kell visszaadnia.

További információk az indexek meghatározásáról: [Index létrehozása (Azure Search REST API)](https://docs.microsoft.com/rest/api/searchservice/create-index).

## <a name="create-an-indexer-map-fields-and-execute-transformations"></a>Indexelő létrehozása, mezők leképezése és átalakítások elvégzése

Eddig létrehozott egy adatforrást, egy készségeitől és az index. Ez a három összetevő egy olyan [indexelő](search-indexer-overview.md) része lesz, amely az egyes részeket egyetlen többszakaszos műveletben egyesíti. Ezek az objektumok összekapcsolása az indexelőt, meg kell határoznia a mező-leképezések.

+ A fieldMappings dolgozza fel előbb a képességek alkalmazási lehetőségét, az adatforrásból származó mezők forrás az indexben lévő cél mezők leképezése. Ha a mező nevét és típusát azonosak mindkét végén, nincs leképezés nem szükséges.

+ A outputFieldMappings feldolgozása után hivatkozik, amely még nem léteznek, amíg dokumentumfeltörést sourceFieldNames készségeitől vagy Adatbővítés létrehozza őket. A targetFieldName egy mezőt az indexben.

Bemenetek, kimenetek csatlakoztatása, mellett is használhatja Mezőleképezések datové struktury simítják. További információkért lásd: [képi elemekben gazdag mezők leképezése egy kereshető indexet](cognitive-search-output-field-mapping.md).

E-parancsprogrammal hozzon létre egy indexelőt nevű `cogsrch-py-indexer`.

```python
# Create an indexer
indexer_payload = {
    "name": indexer_name,
    "dataSourceName": datasource_name,
    "targetIndexName": index_name,
    "skillsetName": skillset_name,
    "fieldMappings" : [
    {
      "sourceFieldName" : "metadata_storage_path",
      "targetFieldName" : "id",
      "mappingFunction" :
        { "name" : "base64Encode" }
    },
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
    {
      "sourceFieldName" : "/document/pages/*/keyPhrases/*",
      "targetFieldName" : "keyPhrases"
    },
    {
      "sourceFieldName": "/document/languageCode",
      "targetFieldName": "languageCode"
    }
  ],
   "parameters":
  {
    "maxFailedItems":-1,
    "maxFailedItemsPerBatch":-1,
    "configuration":
    {
      "dataToExtract": "contentAndMetadata",
      "imageAction": "generateNormalizedImages"
    }
  }
}

r = requests.put(endpoint + "/indexers/" + indexer_name, data=json.dumps(indexer_payload), headers=headers, params=params)
print(r.status_code)
```

A kérelem gyorsan az 201-es állapotkódot kell visszaadnia, azonban a feldolgozás befejezéséhez több percig is eltarthat. Noha az adatkészlet kisméretű, elemzési képességek, például képelemzés, nagy számítási igényű, és időt vesz igénybe.

Használja a [indexelő állapotának](#check-indexer-status) szkript a következő szakaszban, az indexelő folyamat befejeződésének megállapítása.

> [!TIP]
> Az indexelő létrehozása elindítja a folyamatot. Ha fér hozzá az adatokhoz, leképezés bemeneteit és kimeneteit, probléma van, vagy a műveletek sorrendjét, az meg fog jelenni ezen a ponton. Futtassa újra a folyamat a változások kódot vagy szkriptet, szükség lehet először töröljön objektumokat. További információk: [Alaphelyzetbe állítás és ismételt futtatás](#reset).

#### <a name="explore-the-request-body"></a>Ismerkedés a kéréstörzzsel

A szkript -1 értéket állít be a `"maxFailedItems"` paraméterhez, amely utasítja az indexelőmotort, hogy hagyja figyelmen kívül az adatimportálás közben felmerülő hibákat. Ez azért hasznos, mert az adatforrás kevés dokumentumot tartalmaz. Nagyobb méretű adatforrás esetén 0-nál nagyobb értéket kell megadnia.

Figyelje meg a `"dataToExtract":"contentAndMetadata"` utasítást a konfigurációs paraméterekben. A jelen nyilatkozat arról tájékoztatja, hogy az indexelő a tartalom kibontása a különböző fájlformátumok és az egyes fájlok tartozó metaadatokat.

Tartalom kinyerésekor az `imageAction` beállításával kinyerheti a szöveget az adatforrásban talált képekből. A `"imageAction":"generateNormalizedImages"` konfigurációs, optikai Karakterfelismerés szakértelem és szöveges egyesítése szakértelem arra utasítja a szöveg kinyerése a rendszerképek (például a word-forgalom bejelentkezéshez leállítása a "stop"), és beágyazza azt content mezőjének részeként az indexelő. Ez a viselkedés a dokumentumok (PDF-mérőműszerkép áttekinteni) a beágyazott képek és a kép az adatforrás, például a JPG-fájlt a vonatkozik.

<a name="check-indexer-status"></a>

## <a name="check-indexer-status"></a>Az indexelő állapotának ellenőrzése

Az indexelő meghatározását követően az indexelő a kérés elküldésekor automatikusan lefut. Az indexelés a vártnál tovább is eltarthat attól függően, hogy mely kognitív képességeket adta meg. Ismerje meg, hogy indexelő feldolgozása befejeződött, futtassa az alábbi parancsfájlt.

```python
#Get indexer status
r = requests.get(endpoint + "/indexers/" + indexer_name + "/status", headers=headers,params=params)
pprint(json.dumps(r.json(), indent=1))
```

A választ, a figyelő a "lastResult" a "status" és "endTime" értékeket. Rendszeresen futtassa a szkriptet, ellenőrizheti az állapotot. Az indexelő befejezése után az állapot "sikeres" állítja be, "endTime" kell megadni, és a válasz tartalmazza a hibák és figyelmeztetések Adatbővítés során fellépő.

![Indexelő létrehozása](./media/cognitive-search-tutorial-blob-python/py-indexer-is-created.png "indexelő létrehozása")

A figyelmeztetések bizonyos forrásfájl- és képességkombinációk esetében gyakoriak, és nem mindig utalnak hibára. Ebben az oktatóanyagban a figyelmeztetések olyan jóindulatú. Például a JPEG-fájlok, amelynek nincs szöveg jelennek meg a figyelmeztetést ezen a képernyőképen látható.

![A példában az indexelő figyelmeztetés](./media/cognitive-search-tutorial-blob-python/py-indexer-warning-example.png "példa indexelő figyelmeztetés")

## <a name="query-your-index"></a>Az index lekérdezése

Az indexelés befejezését követően futtasson olyan lekérdezéseket, amelyek egyéni mezők tartalmát adják vissza. Alapértelmezés szerint az Azure Search az első 50 eredményt adja vissza. A mintaadatok mérete kicsi, ezért az alapértelmezett beállítás megfelelő. Azonban nagyobb méretű adatkészletek esetén előfordulhat, hogy a lekérdezési karakterláncban meg kell adnia a megfelelő paramétereket, hogy a rendszer több eredményt adjon vissza. Részletes utasításokért lásd: [Eredmények lapokra bontása az Azure Searchben](search-pagination-page-layout.md).

Ellenőrzési lépésként kérdezze le az index összes mezőjét.

```python
#Query the index for all fields
r = requests.get(endpoint + "/indexes/" + index_name, headers=headers,params=params)
pprint(json.dumps(r.json(), indent=1))
```

Az eredmények az alábbi példához hasonlóan kell kinéznie. Csak a képernyőképen látható, a válasz egy részét.

![Minden mező indexe lekérdezés](./media/cognitive-search-tutorial-blob-python/py-query-index-for-fields.png "minden mező az index lekérdezése")

A kimenet az egyes mezők nevét, típusát és attribútumait tartalmazó indexséma.

Indítson egy másodlagos lekérdezést a `"*"` kifejezésre egy mező, például az `organizations` összes tartalmának lekéréséhez.

```python
#Query the index to return the contents of organizations
r = requests.get(endpoint + "/indexes/" + index_name + "/docs?&search=*&$select=organizations", headers=headers, params=params)
pprint(json.dumps(r.json(), indent=1))
```

Az eredmények az alábbi példához hasonlóan kell kinéznie. Csak a képernyőképen látható, a válasz egy részét.

![A szervezetek tartalmát lekérdezés indexe](./media/cognitive-search-tutorial-blob-python/py-query-index-for-organizations.png "szervezetek tartalmát adja vissza, az index lekérdezése")

Ismételje meg a műveletet további mezőket: tartalom, languageCode, keyPhrases és szervezetek ebben a gyakorlatban. Egyszerre több mezőt is lekérhet a vesszővel elválasztott listát használó `$select` megadásával.

A lekérdezési karakterlánc összetettségétől és hosszától függően használhatja a GET vagy a POST metódust. További információkért lásd: [Lekérdezés a REST API-val](https://docs.microsoft.com/rest/api/searchservice/search-documents).
it <a name="reset"></a>

## <a name="reset-and-rerun"></a>Alaphelyzetbe állítás és ismételt futtatás

A folyamat fejlesztésének kezdeti, kísérleti szakaszaiban a kialakítás legpraktikusabb megközelítése az, ha törli az objektumokat az Azure Search szolgáltatásból, és hagyja, hogy a kód újraépítse azokat. Az erőforrásnevek egyediek. Egy objektum törlése révén újból létrehozhatja azt ugyanazzal a névvel.

Ha az új definíciókkal újra el szeretné végezni a dokumentumok indexelését:

1. Törölje az indexet a megőrzött adatok eltávolításához. Törölje az indexet, hogy újra létrehozhassa a szolgáltatásban.
2. Módosítsa a képességek készlete és indexmezők definíciókat.
3. Hozza létre újból az indexet és az indexelőt a szolgáltatásban a folyamat futtatásához.

Használhatja a portál törli az indexek, indexelők és ismereteket. Ha törli az indexelő, igény szerint, külön-külön törölheti az index, készségeitől és az adatforrás egy időben.

![Keresés objektumok törlése](./media/cognitive-search-tutorial-blob-python/py-delete-indexer-delete-all.png "Delete keresési objektumokat a portálon")

Törölheti is őket egy parancsfájl használatával. A következő parancsfájl törli a létrehozott készségeitől. Könnyedén módosíthatja az index, indexelő és adatforrás törlésére irányuló kérést.

```python
#delete the skillset
r = requests.delete(endpoint + "/skillsets/" + skillset_name, headers=headers, params=params)
pprint(json.dumps(r.json(), indent=1))
```

Ahogy a kód egyre kiforrottabbá válik, jó ötlet lehet az újraépítési stratégia finomítása. A további tudnivalókért lásd az [indexek újraépítését](search-howto-reindex.md) ismertető cikket.

## <a name="takeaways"></a>Legfontosabb ismeretek

Ez az oktatóanyag a bővített indexelőfolyamat a komponensek (adatforrás, képességcsoport, index és indexelő) létrehozásával való összeállításának alapvető lépéseit ismerteti.

[Képességek az előre meghatározott](cognitive-search-predefined-skills.md) jelentek meg, indexmezők definíciókat és lánc képességek együtt bemenetek és kimenetek módja együtt. Azt is megtanulta, hogy az indexelő definíciójának `outputFieldMappings` eleme szükséges a bővített értékek a folyamatból az Azure Search szolgáltatásban található, kereshető indexbe történő átirányításához.

Végül útmutatóból megtudhatta, hogyan tesztelheti az eredményeket, és további iteráció során a rendszer. Megtanulta, hogy ha lekérdezéseket futtat az indexen, az a bővített indexelési folyamat által létrehozott kimenetet adja vissza. Ebben a kiadásban szerepel egy belső szerkezetek (a rendszer által létrehozott bővített dokumentumok) megjelenítésére szolgáló mechanizmus. Azt is megtanulta, hogyan indexelő állapotát, és milyen objektumokat törölni kell a folyamat ismételt futtatása előtt.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Az oktatóanyagok után fölöslegessé vált elemek eltávolítása leggyorsabban az Azure Search és az Azure Blob szolgáltatást tartalmazó erőforráscsoport törlésével végezhető el. Ha mindkét szolgáltatás ugyanabba a csoportba helyezi, véglegesen törli a teljes tartalmát, beleértve a szolgáltatások és a tárolt tartalmat, amelyet ebben az oktatóanyagban létrehozott erőforráscsoport törléséhez. A portálon az erőforráscsoport neve az egyes szolgáltatások Áttekintés lapján szerepel.

## <a name="next-steps"></a>További lépések

Egyéni képességekkel testre szabhatja vagy kibővítheti a folyamatot. Egyéni képességek létrehozása és egy képességcsoporthoz adása révén saját kezűleg írt szöveg- vagy képelemzést használhat.

> [!div class="nextstepaction"]
> [Példa: Cognitive Search egyéni műveleteket létrehozása](cognitive-search-create-custom-skill-example.md)
