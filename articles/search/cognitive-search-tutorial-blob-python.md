---
title: 'Oktatóanyag: készségkészlet létrehozása a Pythonban REST API-k használatával'
titleSuffix: Azure Cognitive Search
description: A Jupyter Python notebook használatával bemutatjuk az adatok kinyerésének, természetes nyelvének és képai-feldolgozásának példáját az Azure Cognitive Search. A kinyert adatértékek indexelve vannak, és a lekérdezés könnyen elérhető.
manager: nitinme
author: LisaLeib
ms.author: v-lilei
ms.service: cognitive-search
ms.devlang: python
ms.topic: tutorial
ms.date: 11/04/2019
ms.openlocfilehash: 1e404998c8f49852248a754e7134f439dcdf5b04
ms.sourcegitcommit: 598c5a280a002036b1a76aa6712f79d30110b98d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/15/2019
ms.locfileid: "74113682"
---
# <a name="tutorial-create-an-ai-enrichment-pipeline-using-rest-and-python"></a>Oktatóanyag: AI-dúsítási folyamat létrehozása a REST és a Python használatával

Ebben az oktatóanyagban megismerheti az Azure-Cognitive Search programozási feladatainak *megismerését a kognitív képességek*segítségével. A szaktudást a természetes nyelvi feldolgozási (NLP) és a Cognitive Services képelemzési képességei is alátámasztják. A készségkészlet-összeállításon és-konfiguráción keresztül kinyerheti egy rendszerkép vagy beolvasott dokumentum szövegét és szöveges ábrázolását. Emellett a nyelv, az entitások, a legfontosabb kifejezések és egyebek is észlelhetők. Ennek eredménye egy keresési indexben található, mesterséges intelligenciával létrehozott, az indexelési folyamat során létrejövő további tartalom. 

Ebben az oktatóanyagban a Python használatával hajtja végre a következő feladatokat:

> [!div class="checklist"]
> * Olyan indexelőfolyamat létrehozása, amely kibővíti a mintaadatokat egy indexhez vezető útvonalon
> * Beépített képességek használata: entitásfelismerés, nyelvfelismerés, szövegmanipuláció és kulcskifejezés-kinyerés
> * Megtudhatja, hogyan kapcsolhat össze képességeket egy képességcsoporton belül a bemenetek kimenetekhez való társításával
> * Kérések végrehajtása és az eredmények áttekintése
> * Az index és az indexelők alaphelyzetbe állítása további fejlesztés céljából

A kimenet egy teljes szöveges kereshető index az Azure Cognitive Searchon. Az indexet egyéb standard képességekkel, például [szinonimákkal](search-synonyms.md), [pontozási profilokkal](https://docs.microsoft.com/rest/api/searchservice/add-scoring-profiles-to-a-search-index), [elemzőkkel](search-analyzers.md) és [szűrőkkel](search-filters.md) is bővítheti. 

Ez az oktatóanyag az ingyenes szolgáltatáson fut, de az ingyenes tranzakciók száma napi 20 dokumentumra korlátozódik. Ha az oktatóanyagot többször is futtatni szeretné ugyanazon a napon, használjon kisebb fájlméretet, hogy több futtatással is elfér.

> [!NOTE]
> Ha a hatókört a feldolgozás gyakoriságának növelésével, további dokumentumok hozzáadásával vagy további AI-algoritmusok hozzáadásával bővíti, akkor [a számlázható Cognitive Services erőforrást kell csatolnia](cognitive-search-attach-cognitive-services.md). Az API-k Cognitive Services-ben való meghívásakor felmerülő díjak, valamint a képek kinyerése a dokumentum repedésének részeként az Azure Cognitive Searchban. A dokumentumokból való szöveg kinyerése díjmentes.
>
> A beépített készségek elvégzése a meglévő Cognitive Services utólagos elszámolású [díjszabás szerint](https://azure.microsoft.com/pricing/details/cognitive-services/)történik. A rendszerkép kibontásának díjszabását az [Azure Cognitive Search díjszabási oldalán](https://go.microsoft.com/fwlink/?linkid=2042400)találja.

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

Ebben az oktatóanyagban a következő szolgáltatásokat, eszközöket és az adateszközöket használjuk. 

+ [Hozzon létre egy Azure Storage-fiókot](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account) a mintaadatok tárolásához. Győződjön meg arról, hogy a Storage-fiók ugyanabban a régióban található, mint az Azure Cognitive Search.

+ [Anaconda 3. x](https://www.anaconda.com/distribution/#download-section), amely Python 3. x és Jupyter jegyzetfüzeteket biztosít.

+ A [mintaadatok](https://1drv.ms/f/s!As7Oy81M_gVPa-LCb5lC_3hbS-4) egy kisméretű, különböző típusú fájlból állnak. 

+ [Hozzon létre egy Azure Cognitive Search szolgáltatást](search-create-service-portal.md) , vagy [keressen egy meglévő szolgáltatást](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) a jelenlegi előfizetése alatt. Ehhez az oktatóanyaghoz használhatja az ingyenes szolgáltatást.

## <a name="get-a-key-and-url"></a>Kulcs és URL-cím lekérése

Az Azure Cognitive Search szolgáltatással való kommunikációhoz szüksége lesz a szolgáltatás URL-címére és egy hozzáférési kulcsra. A Search szolgáltatás mindkettővel jön létre, így ha az előfizetéshez hozzáadta az Azure Cognitive Searcht, kövesse az alábbi lépéseket a szükséges információk beszerzéséhez:

1. [Jelentkezzen be a Azure Portalba](https://portal.azure.com/), és a keresési szolgáltatás **Áttekintés** lapján töltse le az URL-címet. A végpontok például a következőképpen nézhetnek ki: `https://mydemo.search.windows.net`.

1. A **beállítások** > **kulcsok**területen kérjen meg egy rendszergazdai kulcsot a szolgáltatásra vonatkozó összes jogosultsághoz. Az üzletmenet folytonossága érdekében két, egymással megváltoztathatatlan rendszergazdai kulcs áll rendelkezésre. Az objektumok hozzáadására, módosítására és törlésére vonatkozó kérésekhez használhatja az elsődleges vagy a másodlagos kulcsot is.

![HTTP-végpont és elérési kulcs beszerzése](media/search-get-started-postman/get-url-key.png "HTTP-végpont és elérési kulcs beszerzése")

Minden kérelemhez API-kulcs szükséges a szolgáltatásnak küldött összes kéréshez. Egy érvényes kulcs a kérést küldő alkalmazás és az azt kezelő szolgáltatás közötti megbízhatósági kapcsolatot hoz létre a kérelmek alapján.

## <a name="prepare-sample-data"></a>Mintaadatok előkészítése

A bővítési folyamat az Azure-adatforrásokból hívja le az adatokat. A forrásadatok egy [Azure Cognitive Search indexelő](search-indexer-overview.md)által támogatott adatforrás-típusból származnak. Ebben a gyakorlatban a blobtárolót használjuk több tartalomtípus bemutatásához.

1. [Jelentkezzen be a Azure Portalba](https://portal.azure.com), navigáljon az Azure Storage-fiókjához, kattintson a **Blobok**elemre, majd a **+ tároló**elemre.

1. [Hozzon létre egy BLOB-tárolót](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal) a mintaadatok tárolásához. Megadhatja a nyilvános hozzáférési szintet bármelyik érvényes értékéhez.

1. A tároló létrehozása után nyissa meg, és válassza a parancssáv **feltöltés** elemét az előző lépésben letöltött mintaképek feltöltéséhez.

   ![Forrásfájlok az Azure Blob Storage-ban](./media/cognitive-search-quickstart-blob/sample-data.png)

1. A mintafájlok feltöltése után kérje le a tároló nevét és a kapcsolati sztringet a Blob Storage-hoz. Ezt megteheti úgy is, ha megkeresi a tárfiókját az Azure Portalon. Kattintson a **hozzáférési kulcsok**elemre, majd másolja a **kapcsolati karakterlánc** mezőt.

A következő formátumú a kapcsolatok karakterlánca: `DefaultEndpointsProtocol=https;AccountName=<YOUR-STORAGE-ACCOUNT-NAME>;AccountKey=<YOUR-STORAGE-ACCOUNT-KEY>;EndpointSuffix=core.windows.net`

Tartsa kéznél a kapcsolatok karakterláncát. Egy későbbi lépésben szüksége lesz rá.

A kapcsolati sztring megadásának egyéb módjai is vannak, például egy közös hozzáférésű jogosultságkód biztosítása. Az adatforrások hitelesítő adataival kapcsolatos további információért lásd: [Indexelés az Azure Blob Storage-ban](search-howto-indexing-azure-blob-storage.md#Credentials).

## <a name="create-a-jupyter-notebook"></a>Jupyter notebook létrehozása

> [!Note]
> Ebből a cikkből megtudhatja, hogyan hozhat létre egy adatforrást, egy indexet, egy indexelő és egy készségkészlet egy sor Python-parancsfájl használatával. A teljes jegyzetfüzet-példa letöltéséhez nyissa meg az [Azure-Search-Python-Samples](https://github.com/Azure-Samples/azure-search-python-samples/tree/master/Tutorial-AI-Enrichment-Jupyter-Notebook)tárházat.

A anaconda Navigator használatával indítsa el Jupyter Notebook és hozzon létre egy új Python 3 jegyzetfüzetet.

## <a name="connect-to-azure-cognitive-search"></a>Kapcsolódás az Azure Cognitive Searchhoz

A jegyzetfüzetben futtassa ezt a szkriptet a JSON használatához használt könyvtárak betöltéséhez és a HTTP-kérések összeállításához.

```python
import json
import requests
from pprint import pprint
```

Ezt követően adja meg az adatforrás, az index, az indexelő és a készségkészlet nevét. Futtassa ezt a szkriptet az oktatóanyag nevének beállításához.

```python
# Define the names for the data source, skillset, index and indexer
datasource_name = "cogsrch-py-datasource"
skillset_name = "cogsrch-py-skillset"
index_name = "cogsrch-py-index"
indexer_name = "cogsrch-py-indexer"
```

> [!Tip]
> Az ingyenes szolgáltatásokban három index, indexelő és adatforrásra van korlátozva. Az oktatóanyagban mindegyikből egyet hozhat majd létre. Győződjön meg arról, hogy van olyan hely, amely új objektumokat hoz létre, mielőtt továbblépne.

A következő parancsfájlban cserélje le a keresési szolgáltatás (a-SEARCH-SERVICE-NAME) és a felügyeleti API-kulcs (a-ADMIN-API-kulcs) helyőrzőit, majd futtassa a keresési szolgáltatás végpontjának beállításához.

```python
# Setup the endpoint
endpoint = 'https://<YOUR-SEARCH-SERVICE-NAME>.search.windows.net/'
headers = {'Content-Type': 'application/json',
           'api-key': '<YOUR-ADMIN-API-KEY>'}
params = {
    'api-version': '2019-05-06'
}
```

## <a name="create-a-data-source"></a>Adatforrás létrehozása

Most, hogy előkészítette a szolgáltatásokat és a forrásfájlokat, hozzáláthat az indexelőfolyamat komponenseinek összeállításához. Kezdje egy olyan adatforrás-objektummal, amely azt jelzi, hogy az Azure Cognitive Search a külső forrásadatok lekérését.

A következő parancsfájlban cserélje le a helyőrzőt a-BLOB-RESOURCE-replace-STRING értékre az előző lépésben létrehozott blobhoz tartozó kapcsolatok karakterláncával. Ezután futtassa a parancsfájlt egy `cogsrch-py-datasource`nevű adatforrás létrehozásához.

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
        "name": "basic-demo-data-pr"
    }
}
r = requests.put(endpoint + "/datasources/" + datasource_name,
                 data=json.dumps(datasource_payload), headers=headers, params=params)
print(r.status_code)
```

A kérelemnek 201-as állapotkódot kell visszaadnia.

A Azure Portal keresési szolgáltatás irányítópultján ellenőrizze, hogy az **adatforrások** listában megjelenik-e az cogsrch-adatforrás. A lap frissítéséhez kattintson a **frissítés** gombra.

![Adatforrások csempéje a portálon](./media/cognitive-search-tutorial-blob-python/py-data-source-tile.png "Adatforrások csempéje a portálon")

## <a name="create-a-skillset"></a>Képességcsoport létrehozása

Ebben a lépésben az adataira vonatkozó dúsítási lépések készletét fogja meghatározni. Minden bővítési lépés egy *képesség*, a bővítési lépések készlete pedig a *képességcsoport*. Ez az oktatóanyag [beépített kognitív képességeket](cognitive-search-predefined-skills.md) használ a készségkészlet:

+ [Nyelvfelismeréssel](cognitive-search-skill-language-detection.md) azonosítja a tartalom nyelvét.

+ A [szöveg felosztásával](cognitive-search-skill-textsplit.md) a nagyméretű tartalmakat kisebb darabokra bontja, mielőtt meghívná a kulcskifejezések kinyerését. A kulcskifejezések kinyerése legfeljebb 50 000 karakter méterű bemeneteket fogad el. A mintafájlok közül néhányat fel kell osztani ahhoz, hogy beleférjen a korlátozásba.

+ [Entitások felismerése](cognitive-search-skill-entity-recognition.md) a blob-tároló tartalmából származó szervezetek nevének kinyeréséhez.

+ A [Kulcskifejezések kinyerése](cognitive-search-skill-keyphrases.md) lehívja a leggyakoribb kulcskifejezéseket. 

### <a name="python-script"></a>Python-szkript
Futtassa a következő szkriptet egy `cogsrch-py-skillset`nevű készségkészlet létrehozásához.

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

A kérelemnek 201-as állapotkódot kell visszaadnia.

Az egyes lapokon a kinyerési képességre vonatkozó kifejezést alkalmazzuk. Ha a környezetet úgy állítja be, hogy `"document/pages/*"`, akkor ezt a dúsítást a dokumentum/oldal tömb minden egyes tagja számára futtatja (a dokumentum minden oldalához).

Minden képesség a dokumentum tartalmán fut le. A feldolgozás során az Azure Cognitive Search kihasználja az egyes dokumentumokat a különböző fájlformátumokból származó tartalmak olvasásához. A forrásfájlban található szöveg egy `content` mezőbe kerül, amely minden dokumentumhoz egy. Ezért `"/document/content"`ként állítsa be a bemenetet.

Alább a képességcsoport grafikai ábrázolása látható.

![Készségkészlet megismerése](media/cognitive-search-tutorial-blob/skillset.png "Készségkészlet megismerése")

A kimenetek leképezhetők egy indexre, amely bemenetként használható egy alsóbb rétegbeli képességhez vagy mindkettőhöz, akárcsak a nyelvi kóddal. Az indexben a nyelvkód a szűréskor lehet hasznos. A nyelvkódot bemenetként a szövegelemzési képességek használják, a szótördeléssel kapcsolatos nyelvi szabályok megadásához.

A képességcsoportok alapvető tudnivalóval kapcsolatos bővebb információkért lásd: [Képességcsoport megadása](cognitive-search-defining-skillset.md).

## <a name="create-an-index"></a>Index létrehozása

Ebben a szakaszban megadhatja az index sémát úgy, hogy megadja a kereshető indexbe felvenni kívánt mezőket, és beállítja az egyes mezők keresési attribútumait. A mezők típussal is rendelkeznek, emellett olyan attribútumokat tartalmazhatnak, amelyek meghatározzák a mező használatának módját (kereshető, rendezhető stb.). Az indexben szereplő mezőneveknek nem kell pontosan megegyezniük a forrásban szereplő mezők nevével. Egy későbbi lépésben mezőleképezéseket fog hozzáadni egy indexelőhöz a forrás-cél mezőkhöz való csatlakozás céljából. Ebben a lépésben a keresőalkalmazásra vonatkozó mezőelnevezési konvenciók használatával határozza meg az indexet.

A gyakorlat során az alábbi mezőket és mezőtípusokat használjuk:

| mezőnevek: | id         | content   | languageCode | keyPhrases         | organizations     |
|--------------|----------|-------|----------|--------------------|-------------------|
| mezőtípusok: | Edm.String|Edm.String| Edm.String| List<Edm.String>  | List<Edm.String>  |

Futtassa ezt a szkriptet a `cogsrch-py-index`nevű index létrehozásához.

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

A kérelemnek 201-as állapotkódot kell visszaadnia.

Az index definiálásával kapcsolatos további tudnivalókért tekintse meg az [index létrehozása (Azure Cognitive Search REST API)](https://docs.microsoft.com/rest/api/searchservice/create-index)című témakört.

## <a name="create-an-indexer-map-fields-and-execute-transformations"></a>Indexelő létrehozása, mezők leképezése és átalakítások elvégzése

Eddig létrehozott egy adatforrást, egy készségkészlet és egy indexet. Ez a három összetevő egy olyan [indexelő](search-indexer-overview.md) része lesz, amely az egyes részeket egyetlen többszakaszos műveletben egyesíti. Ha ezeket az objektumokat egy indexelő alkalmazásban szeretné összekapcsolni, meg kell adnia a mezők leképezéseit.

+ A fieldMappings a rendszer az adatforrásból a készségkészlet, a leképezési forrás mezőinek feldolgozásával dolgozza fel az indexben lévő mezőkbe. Ha a mezők nevei és típusai mindkét végén azonosak, nincs szükség leképezésre.

+ A outputFieldMappings a készségkészlet után dolgozzák fel, hivatkozva a sourceFieldNames, amelyek nem léteznek a dokumentum repedésének vagy dúsításának létrehozásakor. A targetFieldName egy index mezője.

A bemenetek kimenetekhez való csatlakoztatása mellett mező-hozzárendelések is használhatók az adatstruktúrák leállításához. További információ: [a dúsított mezők leképezése kereshető indexbe](cognitive-search-output-field-mapping.md).

Futtassa ezt a parancsfájlt egy `cogsrch-py-indexer`nevű indexelő létrehozásához.

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

A kérésnek gyorsan vissza kell térnie a 201-es állapotkóddal, azonban a feldolgozás több percet is igénybe vehet. Bár az adathalmaz kicsi, az analitikai képességek, például a képelemzés, a számítási igényű, és időt vesz igénybe.

A következő szakaszban az [Indexelő állapotának ellenőrzése](#check-indexer-status) parancsfájl használatával határozhatja meg, hogy mikor fejeződött be az indexelő folyamat.

> [!TIP]
> Az indexelő létrehozása elindítja a folyamatot. Ha probléma merül fel az adatok elérésekor, a bemenetek és kimenetek leképezése vagy a műveletek sorrendjével, akkor a jelen szakaszban fog megjelenni. Ha újra szeretné futtatni a folyamatot a kód vagy a parancsfájl módosításával, akkor először törölnie kell az objektumokat. További információk: [Alaphelyzetbe állítás és ismételt futtatás](#reset).

#### <a name="explore-the-request-body"></a>Ismerkedés a kéréstörzzsel

A szkript -1 értéket állít be a `"maxFailedItems"` paraméterhez, amely utasítja az indexelőmotort, hogy hagyja figyelmen kívül az adatimportálás közben felmerülő hibákat. Ez azért hasznos, mert az adatforrás kevés dokumentumot tartalmaz. Nagyobb méretű adatforrás esetén 0-nál nagyobb értéket kell megadnia.

Figyelje meg a `"dataToExtract":"contentAndMetadata"` utasítást a konfigurációs paraméterekben. Ez az utasítás azt jelzi, hogy az indexelő Kinyeri a tartalmat különböző fájlformátumokból és az egyes fájlokhoz kapcsolódó metaadatokat.

Tartalom kinyerésekor az `imageAction` beállításával kinyerheti a szöveget az adatforrásban talált képekből. A `"imageAction":"generateNormalizedImages"` konfiguráció az OCR-képesség és a szöveg egyesítési képességeivel együtt azt jelzi, hogy az indexelő Kinyeri a szöveget a képekből (például a "Leállítás" szót egy forgalom leállításakor), és beágyazza azt a Content (tartalom) mező részeként. Ez a viselkedés a dokumentumokban beágyazott képekre (például egy PDF-fájlra) és az adatforrásban található rendszerképekre is vonatkozik (például egy JPG-fájlra).

<a name="check-indexer-status"></a>

## <a name="check-indexer-status"></a>Az indexelő állapotának ellenőrzése

Az indexelő meghatározását követően az indexelő a kérés elküldésekor automatikusan lefut. Az indexelés a vártnál tovább is eltarthat attól függően, hogy mely kognitív képességeket adta meg. Ha szeretné megtudni, hogy az indexelő feldolgozása befejeződött-e, futtassa a következő szkriptet.

```python
# Get indexer status
r = requests.get(endpoint + "/indexers/" + indexer_name +
                 "/status", headers=headers, params=params)
pprint(json.dumps(r.json(), indent=1))
```

A válaszban figyelje az "állapot" és a "Befejezés" érték "lastResult" értékét. Rendszeresen futtassa a parancsfájlt az állapot vizsgálatához. Az indexelő befejeződése után az állapot a "sikeres" értékre lesz állítva, a "Befejezés" értéket adja meg, és a válasz tartalmazni fog minden olyan hibát és figyelmeztetést, amely a dúsítás során történt.

![Az indexelő létrejött](./media/cognitive-search-tutorial-blob-python/py-indexer-is-created.png "Az indexelő létrejött")

A figyelmeztetések bizonyos forrásfájl- és képességkombinációk esetében gyakoriak, és nem mindig utalnak hibára. Ebben az oktatóanyagban a figyelmeztetések jóindulatú. Például az egyik JPEG-fájl, amely nem rendelkezik szöveggel, megjelenik a képernyőképen látható figyelmeztetés.

![Példa indexelő figyelmeztetésre](./media/cognitive-search-tutorial-blob-python/py-indexer-warning-example.png "Példa indexelő figyelmeztetésre")

## <a name="query-your-index"></a>Az index lekérdezése

Az indexelés befejezését követően futtasson olyan lekérdezéseket, amelyek egyéni mezők tartalmát adják vissza. Alapértelmezés szerint az Azure Cognitive Search a legfontosabb 50 eredményeket adja vissza. A mintaadatok mérete kicsi, ezért az alapértelmezett beállítás megfelelő. Azonban nagyobb méretű adatkészletek esetén előfordulhat, hogy a lekérdezési sztringben meg kell adnia a megfelelő paramétereket, hogy a rendszer több eredményt adjon vissza. Útmutatásért lásd: [How to Page results in Azure Cognitive Search](search-pagination-page-layout.md).

Ellenőrzési lépésként kérdezze le az index összes mezőjét.

```python
# Query the index for all fields
r = requests.get(endpoint + "/indexes/" + index_name,
                 headers=headers, params=params)
pprint(json.dumps(r.json(), indent=1))
```

Az eredményeknek az alábbi példához hasonlóan kell kinéznie. A képernyőkép csak a válasz egy részét jeleníti meg.

![Lekérdezési index az összes mezőhöz](./media/cognitive-search-tutorial-blob-python/py-query-index-for-fields.png "Az összes mező indexének lekérdezése")

A kimenet az egyes mezők nevét, típusát és attribútumait tartalmazó indexséma.

Indítson egy másodlagos lekérdezést a `"*"` kifejezésre egy mező, például az `organizations` összes tartalmának lekéréséhez.

```python
# Query the index to return the contents of organizations
r = requests.get(endpoint + "/indexes/" + index_name +
                 "/docs?&search=*&$select=organizations", headers=headers, params=params)
pprint(json.dumps(r.json(), indent=1))
```

Az eredményeknek az alábbi példához hasonlóan kell kinéznie. A képernyőkép csak a válasz egy részét jeleníti meg.

![A szervezetek tartalmának lekérdezési indexe](./media/cognitive-search-tutorial-blob-python/py-query-index-for-organizations.png "Az index lekérdezése a szervezetek tartalmának visszaküldéséhez")

Ismételje meg a műveletet további mezők esetén: tartalom, languageCode, kifejezés és szervezet ebben a gyakorlatban. Egyszerre több mezőt is lekérhet a vesszővel elválasztott listát használó `$select` megadásával.

A lekérdezési sztring összetettségétől és hosszától függően használhatja a GET vagy a POST metódust. További információkért lásd: [Lekérdezés a REST API-val](https://docs.microsoft.com/rest/api/searchservice/search-documents).

<a name="reset"></a>

## <a name="reset-and-rerun"></a>Alaphelyzetbe állítás és ismételt futtatás

A folyamat-fejlesztés korai kísérleti szakaszaiban a tervezési iterációk legalkalmasabb megközelítése az objektumok törlése az Azure Cognitive Search, és lehetővé teszi a kód újraépítését. Az erőforrásnevek egyediek. Egy objektum törlése révén újból létrehozhatja azt ugyanazzal a névvel.

Ha az új definíciókkal újra el szeretné végezni a dokumentumok indexelését:

1. Törölje az indexet a megőrzött adatok eltávolításához. Törölje az indexet, hogy újra létrehozhassa a szolgáltatásban.
2. Módosítsa a készségkészlet és az index definícióit.
3. Hozza létre újból az indexet és az indexelőt a szolgáltatásban a folyamat futtatásához.

A portál használatával törölhet indexeket, indexelő és szakértelmével. Az indexelő törlésekor lehetősége van arra is, hogy az indexet, az készségkészlet és az adatforrást szelektíven törölje egyszerre.

![Keresési objektumok törlése](./media/cognitive-search-tutorial-blob-python/py-delete-indexer-delete-all.png "Keresési objektumok törlése a portálon")

Parancsfájl használatával is törölheti őket. A következő szkript törli az általunk létrehozott készségkészlet. Az index, az indexelő és az adatforrás törlésére vonatkozó kérést egyszerűen módosíthatja.

```python
# delete the skillset
r = requests.delete(endpoint + "/skillsets/" + skillset_name,
                    headers=headers, params=params)
pprint(json.dumps(r.json(), indent=1))
```

Ahogy a kód egyre kiforrottabbá válik, jó ötlet lehet az újraépítési stratégia finomítása. A további tudnivalókért lásd az [indexek újraépítését](search-howto-reindex.md) ismertető cikket.

## <a name="takeaways"></a>Legfontosabb ismeretek

Ez az oktatóanyag a bővített indexelőfolyamat a komponensek (adatforrás, képességcsoport, index és indexelő) létrehozásával való összeállításának alapvető lépéseit ismerteti.

A [beépített képességek](cognitive-search-predefined-skills.md) , valamint a készségkészlet-definíciók és a képességek összevonása a bemeneteken és kimeneteken keresztül történik. Azt is megtanulta, hogy az indexelő definíciójában szereplő `outputFieldMappings` szükséges a folyamatból származó, az Azure Cognitive Search szolgáltatásban kereshető indexként való útválasztáshoz.

Végezetül megtanulta, hogyan tesztelheti az eredményeket, és hogyan állíthatja alaphelyzetbe a rendszer további iterációkat. Megtanulta, hogy ha lekérdezéseket futtat az indexen, az a bővített indexelési folyamat által létrehozott kimenetet adja vissza. Ebben a kiadásban szerepel egy belső szerkezetek (a rendszer által létrehozott bővített dokumentumok) megjelenítésére szolgáló mechanizmus. Azt is megtanulta, hogyan ellenőrizhető az indexelő állapota, és milyen objektumokat kell törölni a folyamat újrafuttatása előtt.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Az oktatóanyag elvégzésének leggyorsabb módja az Azure Cognitive Search Service és az Azure Blob servicet tartalmazó erőforráscsoport törlése. Feltételezve, hogy mindkét szolgáltatást ugyanabba a csoportba helyezi, törölje az erőforráscsoportot az összes benne lévő szolgáltatás végleges törléséhez, beleértve a szolgáltatásokat és az oktatóanyaghoz létrehozott tárolt tartalmakat is. A portálon az erőforráscsoport neve az egyes szolgáltatások Áttekintés lapján szerepel.

## <a name="next-steps"></a>Következő lépések

Egyéni képességekkel testre szabhatja vagy kibővítheti a folyamatot. Egyéni képességek létrehozása és egy képességcsoporthoz adása révén saját kezűleg írt szöveg- vagy képelemzést használhat.

> [!div class="nextstepaction"]
> [Példa: egyéni képesség létrehozása AI-bővítéshez](cognitive-search-create-custom-skill-example.md)
