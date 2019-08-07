---
title: 'REST-oktatóanyag: Cognitive Services hívása AI-dúsítási folyamatban – Azure Search'
description: Az adatok kinyerésének, természetes nyelvének és képai-feldolgozásának példája a Azure Search indexelve az adatok kinyeréséhez és a JSON-Blobok átalakításához a Poster és a REST API használatával.
manager: pablocas
author: luiscabrer
services: search
ms.service: search
ms.devlang: NA
ms.topic: tutorial
ms.date: 05/28/2019
ms.author: luisca
ms.subservice: cognitive-search
ms.openlocfilehash: d431f0ced5b417e178e064dca347ae8d78f14e5d
ms.sourcegitcommit: bc3a153d79b7e398581d3bcfadbb7403551aa536
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/06/2019
ms.locfileid: "68840870"
---
# <a name="rest-tutorial-call-cognitive-services-apis-in-an-azure-search-indexing-pipeline"></a>REST-oktatóanyag: Cognitive Services API-k hívása egy Azure Search indexelési folyamatban

Eben az oktatóanyagban az adatok Azure Search-beli bővítésének programozási mechanikájával fogjuk megismertetni, *kognitív képességek* használatával. A szaktudást a természetes nyelvi feldolgozási (NLP) és a Cognitive Services képelemzési képességei is alátámasztják. A készségkészlet-összeállításon és-konfiguráción keresztül kinyerheti egy rendszerkép vagy beolvasott dokumentum szövegét és szöveges ábrázolását. Emellett a nyelv, az entitások, a legfontosabb kifejezések és egyebek is észlelhetők. A végeredmény egy Azure Search indexben gazdag további tartalom, amely AI-bővítésekkel lett létrehozva egy indexelési folyamatban. 

Az oktatóanyagban REST API-hívásokat fog indítani az alábbi feladatok végrehajtásához:

> [!div class="checklist"]
> * Olyan indexelőfolyamat létrehozása, amely kibővíti a mintaadatokat egy indexhez vezető útvonalon
> * Beépített képességek használata: entitásfelismerés, nyelvfelismerés, szövegmanipuláció és kulcskifejezés-kinyerés
> * Megtudhatja, hogyan kapcsolhat össze képességeket egy képességcsoporton belül a bemenetek kimenetekhez való társításával
> * Kérések végrehajtása és az eredmények áttekintése
> * Az index és az indexelők alaphelyzetbe állítása további fejlesztés céljából

A kimenet egy teljes szöveges, kereshető index az Azure Search szolgáltatásban. Az indexet egyéb standard képességekkel, például [szinonimákkal](search-synonyms.md), [pontozási profilokkal](https://docs.microsoft.com/rest/api/searchservice/add-scoring-profiles-to-a-search-index), [elemzőkkel](search-analyzers.md) és [szűrőkkel](search-filters.md) is bővítheti.

Ez az oktatóanyag az ingyenes szolgáltatáson fut, de az ingyenes tranzakciók száma napi 20 dokumentumra korlátozódik. Ha az oktatóanyagot többször is futtatni szeretné ugyanazon a napon, használjon kisebb fájlméretet, hogy több futtatással is elfér.

> [!NOTE]
> Ha a hatókört a feldolgozás gyakoriságának növelésével, további dokumentumok hozzáadásával vagy további AI-algoritmusok hozzáadásával bővíti, akkor [a számlázható Cognitive Services erőforrást](cognitive-search-attach-cognitive-services.md)kell csatolnia. Az API-k Cognitive Services-ben való meghívásakor felmerülő díjak, valamint a képek kinyerése a dokumentum repedési szakaszának részeként Azure Search. A dokumentumokból való szöveg kinyerése díjmentes.
>
> A beépített készségek elvégzése a meglévő Cognitive Services utólagos elszámolású [díjszabás szerint](https://azure.microsoft.com/pricing/details/cognitive-services/)történik. A rendszerkép kibontásának díjszabását a [Azure Search díjszabási oldalán](https://go.microsoft.com/fwlink/?linkid=2042400)találja.

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

Ebben az oktatóanyagban a következő szolgáltatásokat, eszközöket és az adateszközöket használjuk. 

+ [Hozzon létre egy Azure Storage-fiókot](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account) a mintaadatok tárolásához. Győződjön meg arról, hogy a Storage-fiók ugyanabban a régióban található, mint a Azure Search.

+ A [Poster Desktop alkalmazás](https://www.getpostman.com/) használatával Rest-hívásokat indíthat a Azure Search.

+ A [mintaadatok](https://1drv.ms/f/s!As7Oy81M_gVPa-LCb5lC_3hbS-4) egy kisméretű, különböző típusú fájlból állnak. 

+ [Hozzon létre egy Azure Search szolgáltatást](search-create-service-portal.md) , vagy [keressen egy meglévő szolgáltatást](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) a jelenlegi előfizetése alatt. Ehhez az oktatóanyaghoz használhatja az ingyenes szolgáltatást.

## <a name="get-a-key-and-url"></a>Kulcs és URL-cím lekérése

A REST-hívásokhoz minden kérésének tartalmaznia kell a szolgáltatás URL-címét és egy hozzáférési kulcsot. Mindkettőhöz létrejön egy keresési szolgáltatás, így ha hozzáadta az előfizetéséhez az Azure Searchöt, kövesse az alábbi lépéseket a szükséges információk beszerzéséhez:

1. [Jelentkezzen be a Azure Portalba](https://portal.azure.com/), és a keresési szolgáltatás **Áttekintés** lapján töltse le az URL-címet. A végpontok például a következőképpen nézhetnek ki: `https://mydemo.search.windows.net`.

1. A **Beállítások** > **kulcsaiban**kérjen meg egy rendszergazdai kulcsot a szolgáltatásra vonatkozó összes jogosultsághoz. Az üzletmenet folytonossága érdekében két, egymással megváltoztathatatlan rendszergazdai kulcs áll rendelkezésre. Az objektumok hozzáadására, módosítására és törlésére vonatkozó kérésekhez használhatja az elsődleges vagy a másodlagos kulcsot is.

![Http-végpont és elérési kulcs](media/search-get-started-postman/get-url-key.png "Http-végpont és elérési kulcs") beszerzése

Minden kérelemhez API-kulcs szükséges a szolgáltatásnak küldött összes kéréshez. Érvényes kulcs birtokában kérelmenként létesíthető megbízhatósági kapcsolat a kérést küldő alkalmazás és az azt kezelő szolgáltatás között.

## <a name="prepare-sample-data"></a>Mintaadatok előkészítése

A bővítési folyamat az Azure-adatforrásokból hívja le az adatokat. A forrásadatoknak egy [Azure Search-indexelő](search-indexer-overview.md) által támogatott adatforrástípusból kell származniuk. Az Azure Table Storage a kognitív keresések esetében nem támogatott. Ebben a gyakorlatban a blobtárolót használjuk több tartalomtípus bemutatásához.

1. [Jelentkezzen be a Azure Portalba](https://portal.azure.com), navigáljon az Azure Storage-fiókjához, kattintson a **Blobok**elemre, majd a **+ tároló**elemre.

1. [Hozzon létre egy blob](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal) -tárolót a mintaadatok tárolásához. Megadhatja a nyilvános hozzáférési szintet bármelyik érvényes értékéhez.

1. A tároló létrehozása után nyissa meg, és válassza a parancssáv **feltöltés** elemét az előző lépésben letöltött mintaképek feltöltéséhez.

   ![Forrásfájlok az Azure Blob Storage-ban](./media/cognitive-search-quickstart-blob/sample-data.png)

1. A mintafájlok feltöltése után kérje le a tároló nevét és a kapcsolati karakterláncot a Blob Storage-hoz. Ezt megteheti úgy is, ha megkeresi a tárfiókját az Azure Portalon. Lépjen az **Elérési kulcsokhoz**, majd másolja ki a **Kapcsolati karakterlánc** mező tartalmát.

   A kapcsolati karakterláncnak a következő példához hasonló URL-címnek kell lennie:

      ```http
      DefaultEndpointsProtocol=https;AccountName=cogsrchdemostorage;AccountKey=<your account key>;EndpointSuffix=core.windows.net
      ```

A kapcsolati karakterlánc megadásának egyéb módjai is vannak, például egy közös hozzáférésű jogosultságkód biztosítása. Az adatforrások hitelesítő adataival kapcsolatos további információért lásd: [Indexelés az Azure Blob Storage-ban](search-howto-indexing-azure-blob-storage.md#Credentials).

## <a name="set-up-postman"></a>A Postman beállítása

Indítsa el a Postmant, és hozzon létre egy HTTP-kérelmet. Ha nem ismeri ezt az eszközt, tekintse meg a következőt: [Azure Search REST API](search-get-started-postman.md)-k megismerése a Poster használatával.

Az oktatóanyagban használt metódusok a **post**, a **put**és a **Get**. A fejléc kulcsa "Content-Type" (alkalmazás/JSON), az "API-Key" pedig a Azure Search szolgáltatás rendszergazdai kulcsára van beállítva. A törzs az a hely, ahol elhelyezi a hívás tényleges tartalmát. 

  ![Részben strukturált keresés](media/search-semi-structured-data/postmanoverview.png)

A Poster használatával négy API-hívást hajtunk végre a keresési szolgáltatásban egy adatforrás, egy készségkészlet, egy index és egy indexelő létrehozása érdekében. Az adatforrás tartalmaz egy, a tárfiókjára irányuló mutatót és a JSON-adatait. A keresési szolgáltatás az adatok betöltésekor hozza létre a kapcsolatot.


## <a name="create-a-data-source"></a>Adatforrás létrehozása

Most, hogy előkészítette a szolgáltatásokat és a forrásfájlokat, hozzáláthat az indexelőfolyamat komponenseinek összeállításához. Kezdje egy [adatforrás-objektummal](https://docs.microsoft.com/rest/api/searchservice/create-data-source), amely meghatározza az Azure Search számára a külső forrásadatok lekérésének módját.

A kérés fejlécében adja meg az Azure Search szolgáltatás létrehozásakor használt szolgáltatásnevet és a keresési szolgáltatáshoz létrehozott api-kulcsot. A kéréstörzsben adja meg a blobtároló nevét és a kapcsolati karakterláncot.

### <a name="sample-request"></a>Kérésminta
```http
POST https://[service name].search.windows.net/datasources?api-version=2019-05-06
Content-Type: application/json
api-key: [admin key]
```
#### <a name="request-body-syntax"></a>Kéréstörzs szintaxisa
```json
{
  "name" : "demodata",
  "description" : "Demo files to demonstrate cognitive search capabilities.",
  "type" : "azureblob",
  "credentials" :
  { "connectionString" :
    "DefaultEndpointsProtocol=https;AccountName=<your account name>;AccountKey=<your account key>;"
  },
  "container" : { "name" : "<your blob container name>" }
}
```
Küldje el a kérést. A webes tesztelőeszköznek a 201-es, sikerességet jelző állapotkódot kell visszaadnia. 

Mivel ez az első kérése, az Azure Portalon ellenőrizze, hogy létrejött-e az adatforrás az Azure Searchhöz. A keresési szolgáltatás irányítópultján ellenőrizze, hogy az adatforrások listában új elem szerepel-e. Lehet, hogy várnia kell néhány percet, amíg a portáloldal frissül. 

  ![Adatforrások csempe a portálon](./media/cognitive-search-tutorial-blob/data-source-tile.png "Adatforrások csempe a portálon")

Ha a 403-as vagy 404-es hibát kapja, ellenőrizze a kérés szerkezetét: az `api-version=2019-05-06` legyen a végpont, az `api-key` szerepeljen a fejlécben a `Content-Type` kifejezés után, az értékének pedig érvényesnek kell lennie egy keresési szolgáltatáshoz. A fejlécet az oktatóanyag további lépéseiben ismét felhasználhatja.

## <a name="create-a-skillset"></a>Képességcsoport létrehozása

Ebben a lépésben bővítési lépések egy olyan készletét fogja megadni, amelyet alkalmazni szeretne az adatain. Minden bővítési lépés egy *képesség*, a bővítési lépések készlete pedig a *képességcsoport*. Ez az oktatóanyag [beépített kognitív képességeket](cognitive-search-predefined-skills.md) használ a készségkészlet:

+ [Nyelvfelismeréssel](cognitive-search-skill-language-detection.md) azonosítja a tartalom nyelvét.

+ A [szöveg felosztásával](cognitive-search-skill-textsplit.md) a nagyméretű tartalmakat kisebb darabokra bontja, mielőtt meghívná a kulcskifejezések kinyerését. A kulcskifejezések kinyerése legfeljebb 50 000 karakter méterű bemeneteket fogad el. A mintafájlok közül néhányat fel kell osztani ahhoz, hogy beleférjen a korlátozásba.

+ [Entitások felismerése](cognitive-search-skill-entity-recognition.md) a blob-tároló tartalmából származó szervezetek nevének kinyeréséhez.

+ A [Kulcskifejezések kinyerése](cognitive-search-skill-keyphrases.md) lehívja a leggyakoribb kulcskifejezéseket. 

### <a name="sample-request"></a>Kérésminta
Mielőtt elindítaná ezt a REST-hívást, ne feledje lecserélni az alábbi kérésben szereplő szolgáltatásnevet és a rendszergazdai kulcsot, ha az eszköz két hívás között nem őrzi meg a kérés fejlécét. 

Ez a kérés egy képességcsoportot hoz létre. Az oktatóanyag hátralévő részében hivatkozzon a ```demoskillset``` képességcsoport-névre.

```http
PUT https://[servicename].search.windows.net/skillsets/demoskillset?api-version=2019-05-06
api-key: [admin key]
Content-Type: application/json
```
#### <a name="request-body-syntax"></a>Kéréstörzs szintaxisa
```json
{
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
```

Küldje el a kérést. A webes tesztelőeszköznek a 201-es, sikerességet jelző állapotkódot kell visszaadnia. 

#### <a name="explore-the-request-body"></a>Ismerkedés a kéréstörzzsel

Figyelje meg a kulcskifejezések kinyerési képességének alkalmazását az egyes oldalakon. A ```"document/pages/*"``` kontextus beállításával a bővítőt a dokumentum- vagy oldaltömb minden egyes tagján (azaz a dokumentum minden oldalán) futtatja.

Minden képesség a dokumentum tartalmán fut le. A feldolgozás során az Azure Search a különféle fájlformátumok tartalmának olvasása érdekében minden dokumentumot tördel. A forrásfájlban talált szöveg a létrehozott ```content``` mezőbe kerül, amelyből dokumentumonként egy jön létre. Ebből kifolyólag a bemenet legyen a következő: ```"/document/content"```.

Alább a képességcsoport grafikai ábrázolása látható. 

![Képességcsoport értelmezése](media/cognitive-search-tutorial-blob/skillset.png "Képességcsoport értelmezése")

A kimenetek hozzárendelhetők egy indexhez, bemenetként használhatók egy alsóbb rétegbeli képességhez, vagy a fentiek mindegyike lehetséges, akárcsak a nyelvkód esetében. Az indexben a nyelvkód a szűréskor lehet hasznos. A nyelvkódot bemenetként a szövegelemzési képességek használják, a szótördeléssel kapcsolatos nyelvi szabályok megadásához.

A képességcsoportok alapvető tudnivalóval kapcsolatos bővebb információkért lásd: [Képességcsoport megadása](cognitive-search-defining-skillset.md).

## <a name="create-an-index"></a>Index létrehozása

Ebben a szakaszban egy indexsémát határoz meg a kereshető indexben szereplő mezők és az egyes mezők keresési attribútumainak megadásával. A mezők típussal is rendelkeznek, emellett olyan attribútumokat tartalmazhatnak, amelyek meghatározzák a mező használatának módját (kereshető, rendezhető stb.). Az indexben szereplő mezőneveknek nem kell pontosan megegyezniük a forrásban szereplő mezők nevével. Egy későbbi lépésben mezőleképezéseket fog hozzáadni egy indexelőhöz a forrás-cél mezőkhöz való csatlakozás céljából. Ebben a lépésben a keresőalkalmazásra vonatkozó mezőelnevezési konvenciók használatával határozza meg az indexet.

A gyakorlat során az alábbi mezőket és mezőtípusokat használjuk:

| mezőnevek: | `id`       | content   | languageCode | keyPhrases         | organizations     |
|--------------|----------|-------|----------|--------------------|-------------------|
| mezőtípusok: | Edm.String|Edm.String| Edm.String| List<Edm.String>  | List<Edm.String>  |


### <a name="sample-request"></a>Kérésminta
Mielőtt elindítaná ezt a REST-hívást, ne feledje lecserélni az alábbi kérésben szereplő szolgáltatásnevet és a rendszergazdai kulcsot, ha az eszköz két hívás között nem őrzi meg a kérés fejlécét. 

Ez a kérés egy indexet hoz létre. Az oktatóanyag hátralévő részében használja a ```demoindex``` indexnevet.

```http
PUT https://[servicename].search.windows.net/indexes/demoindex?api-version=2019-05-06
api-key: [api-key]
Content-Type: application/json
```
#### <a name="request-body-syntax"></a>Kéréstörzs szintaxisa

```json
{
  "fields": [
    {
      "name": "id",
      "type": "Edm.String",
      "key": true,
      "searchable": true,
      "filterable": false,
      "facetable": false,
      "sortable": true
    },
    {
      "name": "content",
      "type": "Edm.String",
      "sortable": false,
      "searchable": true,
      "filterable": false,
      "facetable": false
    },
    {
      "name": "languageCode",
      "type": "Edm.String",
      "searchable": true,
      "filterable": false,
      "facetable": false
    },
    {
      "name": "keyPhrases",
      "type": "Collection(Edm.String)",
      "searchable": true,
      "filterable": false,
      "facetable": false
    },
    {
      "name": "organizations",
      "type": "Collection(Edm.String)",
      "searchable": true,
      "sortable": false,
      "filterable": false,
      "facetable": false
    }
  ]
}
```
Küldje el a kérést. A webes tesztelőeszköznek a 201-es, sikerességet jelző állapotkódot kell visszaadnia. 

További információk az indexek meghatározásáról: [Index létrehozása (Azure Search REST API)](https://docs.microsoft.com/rest/api/searchservice/create-index).


## <a name="create-an-indexer-map-fields-and-execute-transformations"></a>Indexelő létrehozása, mezők leképezése és átalakítások elvégzése

Eddig létrehozott egy adatforrást, egy képességcsoportot és egy indexet. Ez a három összetevő egy olyan [indexelő](search-indexer-overview.md) része lesz, amely az egyes részeket egyetlen többszakaszos műveletben egyesíti. A három rész egy indexelőben való egyesítéséhez mezőleképezéseket kell meghatároznia. 

+ A fieldMappings a rendszer az adatforrásból a készségkészlet, a leképezési forrás mezőinek feldolgozásával dolgozza fel az indexben lévő mezőkbe. Ha a mezők nevei és típusai mindkét végén azonosak, nincs szükség leképezésre.

+ A outputFieldMappings a készségkészlet után dolgozzák fel, hivatkozva a sourceFieldNames, amelyek nem léteznek a dokumentum repedésének vagy dúsításának létrehozásakor. A targetFieldName egy index mezője.

A bemenetek kimenetekhez való csatlakoztatása mellett mező-hozzárendelések is használhatók az adatstruktúrák leállításához. További információ: [a dúsított mezők leképezése kereshető indexbe](cognitive-search-output-field-mapping.md).

### <a name="sample-request"></a>Kérésminta

Mielőtt elindítaná ezt a REST-hívást, ne feledje lecserélni az alábbi kérésben szereplő szolgáltatásnevet és a rendszergazdai kulcsot, ha az eszköz két hívás között nem őrzi meg a kérés fejlécét. 

Emellett adja meg az indexelő nevét is. Az oktatóanyag hátralévő részében használja a ```demoindexer``` nevet.

```http
PUT https://[servicename].search.windows.net/indexers/demoindexer?api-version=2019-05-06
api-key: [api-key]
Content-Type: application/json
```
#### <a name="request-body-syntax"></a>Kéréstörzs szintaxisa

```json
{
  "name":"demoindexer", 
  "dataSourceName" : "demodata",
  "targetIndexName" : "demoindex",
  "skillsetName" : "demoskillset",
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
```

Küldje el a kérést. A webes tesztelőeszköznek a 201-es, sikeres feldolgozást jelző állapotkódot kell visszaadnia. 

A lépés utasításainak végrehajtása több percig is eltarthat. Annak ellenére, hogy az adatkészlet kis méretű, az analitikai képességek számítási igénye nagy. Néhány képesség, például a képelemzés futásideje hosszú.

> [!TIP]
> Az indexelő létrehozása elindítja a folyamatot. Ha probléma lép fel az adatok elérésével, a bemenetek és kimenetek leképezésével vagy a műveletek sorrendjével kapcsolatban, az ebben a szakaszban jelenik meg. Ha kód- vagy szkriptmódosításokkal szeretné újra futtatni a folyamatot, lehetséges, hogy először el kell távolítania az objektumokat. További információk: [Alaphelyzetbe állítás és ismételt futtatás](#reset).

#### <a name="explore-the-request-body"></a>Ismerkedés a kéréstörzzsel

A szkript -1 értéket állít be a ```"maxFailedItems"``` paraméterhez, amely utasítja az indexelőmotort, hogy hagyja figyelmen kívül az adatimportálás közben felmerülő hibákat. Ez azért hasznos, mert az adatforrás kevés dokumentumot tartalmaz. Nagyobb méretű adatforrás esetén 0-nál nagyobb értéket kell megadnia.

Figyelje meg a ```"dataToExtract":"contentAndMetadata"``` utasítást a konfigurációs paraméterekben. Ez az utasítást meghatározza, hogy az indexelő automatikusan kinyerje a tartalmat a különböző fájlformátumokból, beleértve az egyes fájlokra vonatkozó metaadatokat is. 

Tartalom kinyerésekor az ```imageAction``` beállításával kinyerheti a szöveget az adatforrásban talált képekből. A ```"imageAction":"generateNormalizedImages"``` konfiguráció az OCR-képesség és a szöveg egyesítése képességgel együtt azt jelzi, hogy az indexelő Kinyeri a szöveget a képekből (például a "Leállítás" szót egy forgalom leállításakor), és beágyazza azt a tartalom mező részévé. Ez a működés mind a dokumentumokban beágyazott képekre (például egy PDF-fájlban található képre), mind az adatforrásban talált képekre (például egy JPG-fájlra) vonatkozik.

## <a name="check-indexer-status"></a>Az indexelő állapotának ellenőrzése

Az indexelő meghatározását követően az indexelő a kérés elküldésekor automatikusan lefut. Az indexelés a vártnál tovább is eltarthat attól függően, hogy mely kognitív képességeket adta meg. Ha kíváncsi rá, hogy az indexelő fut-e még, küldje el a következő kérést az indexelő állapotának ellenőrzéséhez.

```http
GET https://[servicename].search.windows.net/indexers/demoindexer/status?api-version=2019-05-06
api-key: [api-key]
Content-Type: application/json
```

A válasz megmutatja, hogy fut-e az indexelő. Ha az indexelés befejeződött, használjon egy másik HTTP GET-parancsot a STATUS végponton (a fent látható módon) a bővítés során esetlegesen felmerülő hibákat tartalmazó jelentések és figyelmeztetések megjelenítéséhez.  

A figyelmeztetések bizonyos forrásfájl- és képességkombinációk esetében gyakoriak, és nem mindig utalnak hibára. Ebben az oktatóanyagban a figyelmeztetések jóindulatúak (például nincs szöveges bemenet a JPEG-fájlokból). Az állapotválasz áttekintésével részletes információkhoz juthat az indexelés során felmerülő figyelmeztetésekkel kapcsolatban.
 
## <a name="query-your-index"></a>Az index lekérdezése

Az indexelés befejezését követően futtasson olyan lekérdezéseket, amelyek egyéni mezők tartalmát adják vissza. Alapértelmezés szerint az Azure Search az első 50 eredményt adja vissza. A mintaadatok mérete kicsi, ezért az alapértelmezett beállítás megfelelő. Azonban nagyobb méretű adatkészletek esetén előfordulhat, hogy a lekérdezési karakterláncban meg kell adnia a megfelelő paramétereket, hogy a rendszer több eredményt adjon vissza. Részletes utasításokért lásd: [Eredmények lapokra bontása az Azure Searchben](search-pagination-page-layout.md).

Ellenőrzési lépésként kérdezze le az index összes mezőjét.

```http
GET https://[servicename].search.windows.net/indexes/demoindex?api-version=2019-05-06
api-key: [api-key]
Content-Type: application/json
```

A kimenet az egyes mezők nevét, típusát és attribútumait tartalmazó indexséma.

Indítson egy másodlagos lekérdezést a `"*"` kifejezésre egy mező, például az `organizations` összes tartalmának lekéréséhez.

```http
GET https://[servicename].search.windows.net/indexes/demoindex/docs?search=*&$select=organizations&api-version=2019-05-06
api-key: [api-key]
Content-Type: application/json
```

Ismételje meg a műveletet további mezők esetén: tartalom, languageCode, kifejezés és szervezet ebben a gyakorlatban. Egyszerre több mezőt is lekérhet a vesszővel elválasztott listát használó `$select` megadásával.

A lekérdezési karakterlánc összetettségétől és hosszától függően használhatja a GET vagy a POST metódust. További információkért lásd: [Lekérdezés a REST API-val](https://docs.microsoft.com/rest/api/searchservice/search-documents).



<a name="reset"></a>

## <a name="reset-and-rerun"></a>Alaphelyzetbe állítás és ismételt futtatás

A folyamat fejlesztésének kezdeti, kísérleti szakaszaiban a kialakítás legpraktikusabb megközelítése az, ha törli az objektumokat az Azure Search szolgáltatásból, és hagyja, hogy a kód újraépítse azokat. Az erőforrásnevek egyediek. Egy objektum törlése révén újból létrehozhatja azt ugyanazzal a névvel.

Ha az új definíciókkal újra el szeretné végezni a dokumentumok indexelését:

1. Törölje az indexet a megőrzött adatok eltávolításához. Törölje az indexet, hogy újra létrehozhassa a szolgáltatásban.
2. Módosítson egy képességcsoport és egy indexdefiníciót.
3. Hozza létre újból az indexet és az indexelőt a szolgáltatásban a folyamat futtatásához. 

A portál használatával törölhet indexeket, indexelő és szakértelmével.

```http
DELETE https://[servicename].search.windows.net/skillsets/demoskillset?api-version=2019-05-06
api-key: [api-key]
Content-Type: application/json
```

Sikeres törlés esetén a rendszer a 204-es állapotkódot adja vissza.

Ahogy a kód egyre kiforrottabbá válik, jó ötlet lehet az újraépítési stratégia finomítása. A további tudnivalókért lásd az [indexek újraépítését](search-howto-reindex.md) ismertető cikket.

## <a name="takeaways"></a>Legfontosabb ismeretek

Ez az oktatóanyag a bővített indexelőfolyamat a komponensek (adatforrás, képességcsoport, index és indexelő) létrehozásával való összeállításának alapvető lépéseit ismerteti.

Bemutattuk az [előre meghatározott képességeket](cognitive-search-predefined-skills.md), valamint a képességcsoport megadását, illetve a képességek bemenetek és kimenetek segítségével történő összekapcsolásának működését. Azt is megtanulta, hogy az indexelő definíciójának `outputFieldMappings` eleme szükséges a bővített értékek a folyamatból az Azure Search szolgáltatásban található, kereshető indexbe történő átirányításához.

Végül megismerte, hogyan tesztelheti az eredményeket, és hogyan állíthatja alaphelyzetbe a rendszert a későbbi futtatásokhoz. Megtanulta, hogy ha lekérdezéseket futtat az indexen, az a bővített indexelési folyamat által létrehozott kimenetet adja vissza. Ebben a kiadásban szerepel egy belső szerkezetek (a rendszer által létrehozott bővített dokumentumok) megjelenítésére szolgáló mechanizmus. Emellett azt is megtanulta, hogyan ellenőrizheti az indexelő állapotát, illetve hogy melyik objektumokat kell törölnie a folyamat újrafuttatása előtt.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Az oktatóanyagok után fölöslegessé vált elemek eltávolítása leggyorsabban az Azure Search és az Azure Blob szolgáltatást tartalmazó erőforráscsoport törlésével végezhető el. Feltéve, hogy mindkét szolgáltatást ugyanabban a csoportban helyezte üzembe, törölje az erőforráscsoportot, amellyel véglegesen eltávolíthatja annak teljes tartalmát, a rövid útmutató során létrehozott összes szolgáltatást és tárolt tartalmat is beleértve. A portálon az erőforráscsoport neve az egyes szolgáltatások Áttekintés lapján szerepel.

## <a name="next-steps"></a>További lépések

Egyéni képességekkel testre szabhatja vagy kibővítheti a folyamatot. Egyéni képességek létrehozása és egy képességcsoporthoz adása révén saját kezűleg írt szöveg- vagy képelemzést használhat. 

> [!div class="nextstepaction"]
> [Példa: Egyéni képesség létrehozása a kognitív kereséshez](cognitive-search-create-custom-skill-example.md)
