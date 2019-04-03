---
title: Az oktatóanyag a Cognitive Services API-k hívása egy indexelési folyamat – Azure Search
description: Ez az oktatóanyag részletesen bemutat egy példát adatok mesterséges intelligencia segítségével történő kinyerésére, illetve természetes nyelvi és képfeldolgozásra az Azure Search adatkinyerési és -átalakítási indexelőszolgáltatásában.
manager: pablocas
author: luiscabrer
services: search
ms.service: search
ms.devlang: NA
ms.topic: tutorial
ms.date: 03/18/2019
ms.author: luisca
ms.custom: seodec2018
ms.openlocfilehash: f60b9002f939cbf4c3a0ecfb78b358598713ea1c
ms.sourcegitcommit: a60a55278f645f5d6cda95bcf9895441ade04629
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/03/2019
ms.locfileid: "58881630"
---
# <a name="tutorial-call-cognitive-services-apis-in-an-azure-search-indexing-pipeline-preview"></a>Oktatóanyag: Cognitive Services API-k hívja meg az Azure Search folyamat (előzetes verzió) indexelése

Eben az oktatóanyagban az adatok Azure Search-beli bővítésének programozási mechanikájával fogjuk megismertetni, *kognitív képességek* használatával. Képességek természetes nyelvi feldolgozást (NLP) és a lemezkép elemzési képességeket biztosítanak a Cognitive Services élvezik. Képességcsoport összeállítás és konfigurációja kibonthatja a szöveget, és képet vagy beolvasott dokumentum fájl szöveges ábrázolását. Nyelv, az entitások, kulcskifejezéseket és további is felismeri. A végeredmény gazdag további Azure Search-index, mesterséges Intelligencia által működtetett indexelése folyamat által létrehozott tartalom. 

Az oktatóanyagban REST API-hívásokat fog indítani az alábbi feladatok végrehajtásához:

> [!div class="checklist"]
> * Olyan indexelőfolyamat létrehozása, amely kibővíti a mintaadatokat egy indexhez vezető útvonalon
> * Beépített képességek használata: entitásfelismerés, nyelvfelismerés, szövegmanipuláció és kulcskifejezés-kinyerés
> * Megtudhatja, hogyan kapcsolhat össze képességeket egy képességcsoporton belül a bemenetek kimenetekhez való társításával
> * Kérések végrehajtása és az eredmények áttekintése
> * Az index és az indexelők alaphelyzetbe állítása további fejlesztés céljából

A kimenet egy teljes szöveges, kereshető index az Azure Search szolgáltatásban. Az indexet egyéb standard képességekkel, például [szinonimákkal](search-synonyms.md), [pontozási profilokkal](https://docs.microsoft.com/rest/api/searchservice/add-scoring-profiles-to-a-search-index), [elemzőkkel](search-analyzers.md) és [szűrőkkel](search-filters.md) is bővítheti.

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

> [!NOTE]
> December 21, 2018-as, lesz egy Cognitive Services-erőforrás társítása egy Azure Search-képességek alkalmazási lehetőségét. Ez lehetővé teszi indexmezők végrehajtási díjszabási elindításához. Ezen a napon is megkezdjük a dokumentumfeltörést fázis részeként a lemezkép kinyerési díjszabási. A szövegek dokumentumokból való kinyerése továbbra is ingyenesen használható.
>
> A végrehajtás beépített képességek díjat számítunk fel a meglévő [használatalapú-as-, a Cognitive Services nyissa meg az árat](https://azure.microsoft.com/pricing/details/cognitive-services/) . Kép kinyerési díjszabás az előzetes verziók díjszabása díjat számítunk fel, és a leírt a [díjszabását ismertető oldalt az Azure Search](https://go.microsoft.com/fwlink/?linkid=2042400). További [információ](cognitive-search-attach-cognitive-services.md).

## <a name="prerequisites"></a>Előfeltételek

Nem járatos a kognitív keresés használatában? Olvassa el a [kognitív keresés bemutatását](cognitive-search-concept-intro.md), amelyből megismerheti a funkciót, vagy próbálja ki a [portál rövid útmutatóját](cognitive-search-quickstart-blob.md), amely gyakorlati megközelítéssel mutatja be a legfontosabb alapelveket.

REST-hívások az Azure Search-be való küldéséhez használja a PowerShellt vagy egy webes tesztelőeszközt (pl.: Telerik Fiddler vagy Postman), a HTTP-kérések összeállításához. Ha nem ismeri ezeket az eszközöket, olvassa el [az Azure Search REST API-k a Fiddlerrel vagy a Postmannel történő vizsgálatát](search-fiddler.md) ismertető cikket.

Az [Azure Portal](https://portal.azure.com/) segítségével hozzon létre teljes körű munkafolyamatban használt szolgáltatásokat. 

### <a name="set-up-azure-search"></a>Az Azure Search beállítása

Első lépésként regisztráljon az Azure Search szolgáltatásra. 

1. Nyissa meg az [Azure Portalt](https://portal.azure.com), és jelentkezzen be Azure-fiókjával.

1. Kattintson az **Erőforrás létrehozása** gombra, keresse meg az Azure Search szolgáltatást, majd kattintson a **Létrehozás** gombra. Ha első alkalommal próbálkozik keresési szolgáltatás beállításával, tekintse meg az [Azure Search szolgáltatás a portálon történő létrehozását](search-create-service-portal.md) ismertető szakaszt.

   ![A portál irányítópultja](./media/cognitive-search-tutorial-blob/create-search-service-full-portal.png "Azure Search szolgáltatás létrehozása a portálon")

1. Az Erőforráscsoport résznél hozzon létre egy erőforráscsoportot az oktatóanyag során létrehozott erőforrások tárolására. Ezáltal könnyebb lesz az erőforrások eltávolítása, miután az oktatóanyag végére ért.

1. A helyen válasszon egy közeli régiót, az adatok és más felhőalapú alkalmazáshoz.

1. A Tarifacsomagra vonatkozóan az oktatóanyagok és rövid útmutatók elvégzéséhez létrehozhat egy **ingyenes** szolgáltatást. A saját adatok mélyrehatóbb vizsgálatához hozzon létre egy **Alapszintű** vagy **Standard** [fizetős szolgáltatást](https://azure.microsoft.com/pricing/details/search/). 

   Az ingyenes szolgáltatás legfeljebb 3 indexet és 2 perc indexelést tartalmazhat, 16 MB-os maximális blobmérettel, amely nem elegendő a kognitív keresés funkcióinak teljes körű használatához. A különböző csomagok korlátozásait a [szolgáltatási korlátozásokat](search-limits-quotas-capacity.md) ismertető részben tekintheti meg.

   ![A portál szolgáltatás-definíció lap](./media/cognitive-search-tutorial-blob/create-search-service1.png "szolgáltatás definíció lap a portálon")
   ![szolgáltatás definíció lap a portálon](./media/cognitive-search-tutorial-blob/create-search-service2.png "Service definition lap az a portálon")

 
1. A szolgáltatási információk gyors eléréséhez rögzítse a szolgáltatást az irányítópulton.

   ![Szolgáltatásdefiníciós oldal a portálon](./media/cognitive-search-tutorial-blob/create-search-service3.png "Szolgáltatásdefiníciós oldal a portálon")

1. A szolgáltatás létrehozása után gyűjtse össze a következő információkat: **URL-cím** – Áttekintés lapon, és **api-kulcs** (elsődleges vagy másodlagos) a kulcsok lapról.

   ![Végpont- és kulcsadatok a portálon](./media/cognitive-search-tutorial-blob/create-search-collect-info.png "Végpont- és kulcsadatok a portálon")

### <a name="set-up-azure-blob-service-and-load-sample-data"></a>Az Azure Blob szolgáltatás beállítása és a mintaadatok betöltése

A bővítési folyamat az Azure-adatforrásokból hívja le az adatokat. A forrásadatoknak egy [Azure Search-indexelő](search-indexer-overview.md) által támogatott adatforrástípusból kell származniuk. Vegye figyelembe, hogy az Azure Table Storage a kognitív keresés nem támogatott. Ebben a gyakorlatban a blobtárolót használjuk több tartalomtípus bemutatásához.

1. [Töltse le a mintaadatokat](https://1drv.ms/f/s!As7Oy81M_gVPa-LCb5lC_3hbS-4). A mintaadatokat különböző típusú fájlok egy kis készlete alkotja. 

1. Regisztráljon az Azure Blob Storage-ra, hozzon létre egy tárfiókot, jelentkezzen be a Storage Explorerbe, és hozzon létre egy `basicdemo` nevű tárolót. Részletes útmutatást az [Azure Storage Explorer rövid útmutatójában](../storage/blobs/storage-quickstart-blobs-storage-explorer.md) talál.

1. A mintafájlok az Azure Storage Explorerrel való feltöltéséhez a létrehozott `basicdemo` nevű tárolóban kattintson a **Feltöltés** gombra.

1. A mintafájlok feltöltése után kérje le a tároló nevét és a kapcsolati karakterláncot a Blob Storage-hoz. Ezt megteheti úgy is, ha megkeresi a tárfiókját az Azure Portalon. Lépjen az **Elérési kulcsokhoz**, majd másolja ki a **Kapcsolati karakterlánc** mező tartalmát.

   A kapcsolati karakterláncnak a következő példához hasonló URL-címnek kell lennie:

      ```http
      DefaultEndpointsProtocol=https;AccountName=cogsrchdemostorage;AccountKey=<your account key>;EndpointSuffix=core.windows.net
      ```

A kapcsolati karakterlánc megadásának egyéb módjai is vannak, például egy közös hozzáférésű jogosultságkód biztosítása. Az adatforrások hitelesítő adataival kapcsolatos további információért lásd: [Indexelés az Azure Blob Storage-ban](search-howto-indexing-azure-blob-storage.md#Credentials).

## <a name="create-a-data-source"></a>Adatforrás létrehozása

Most, hogy előkészítette a szolgáltatásokat és a forrásfájlokat, hozzáláthat az indexelőfolyamat komponenseinek összeállításához. Kezdje egy [adatforrás-objektummal](https://docs.microsoft.com/rest/api/searchservice/create-data-source), amely meghatározza az Azure Search számára a külső forrásadatok lekérésének módját.

A jelen oktatóanyag esetében használja a REST API-t, valamint egy olyan eszközt, amely képes HTTP-kérések összeállítására és elküldésére (például: PowerShell, Postman vagy Fiddler). A kérés fejlécében adja meg az Azure Search szolgáltatás létrehozásakor használt szolgáltatásnevet és a keresési szolgáltatáshoz létrehozott api-kulcsot. A kéréstörzsben adja meg a blobtároló nevét és a kapcsolati karakterláncot.

### <a name="sample-request"></a>Kérésminta
```http
POST https://[service name].search.windows.net/datasources?api-version=2017-11-11-Preview
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

Mivel ez az első kérése, az Azure Portalon ellenőrizze, hogy létrejött-e az adatforrás az Azure Searchhöz. A keresési szolgáltatás Irányítópult lapján ellenőrizze, hogy az Adatforrások csempén megjelent-e az új elem. Lehet, hogy várnia kell néhány percet, amíg a portáloldal frissül. 

  ![Adatforrások csempe a portálon](./media/cognitive-search-tutorial-blob/data-source-tile.png "Adatforrások csempe a portálon")

Ha a 403-as vagy 404-es hibát kapja, ellenőrizze a kérés szerkezetét: az `api-version=2017-11-11-Preview` legyen a végpont, az `api-key` szerepeljen a fejlécben a `Content-Type` kifejezés után, az értékének pedig érvényesnek kell lennie egy keresési szolgáltatáshoz. A fejlécet az oktatóanyag további lépéseiben ismét felhasználhatja.

## <a name="create-a-skillset"></a>Képességcsoport létrehozása

Ebben a lépésben bővítési lépések egy olyan készletét fogja megadni, amelyet alkalmazni szeretne az adatain. Minden bővítési lépés egy *képesség*, a bővítési lépések készlete pedig a *képességcsoport*. Ez az oktatóanyag [előre meghatározott kognitív képességeket](cognitive-search-predefined-skills.md) használ a képességcsoporthoz:

+ [Nyelvfelismeréssel](cognitive-search-skill-language-detection.md) azonosítja a tartalom nyelvét.

+ A [szöveg felosztásával](cognitive-search-skill-textsplit.md) a nagyméretű tartalmakat kisebb darabokra bontja, mielőtt meghívná a kulcskifejezések kinyerését. A kulcskifejezések kinyerése legfeljebb 50 000 karakter méterű bemeneteket fogad el. A mintafájlok közül néhányat fel kell osztani ahhoz, hogy beleférjen a korlátozásba.

+ A [Megnevezett entitások felismerése](cognitive-search-skill-named-entity-recognition.md) kinyeri a szervezetek nevét a blobtároló tartalmából.

+ A [Kulcskifejezések kinyerése](cognitive-search-skill-keyphrases.md) lehívja a leggyakoribb kulcskifejezéseket. 

### <a name="sample-request"></a>Kérésminta
Mielőtt elindítaná ezt a REST-hívást, ne feledje lecserélni az alábbi kérésben szereplő szolgáltatásnevet és a rendszergazdai kulcsot, ha az eszköz két hívás között nem őrzi meg a kérés fejlécét. 

Ez a kérés egy képességcsoportot hoz létre. Az oktatóanyag hátralévő részében hivatkozzon a ```demoskillset``` képességcsoport-névre.

```http
PUT https://[servicename].search.windows.net/skillsets/demoskillset?api-version=2017-11-11-Preview
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
      "@odata.type": "#Microsoft.Skills.Text.NamedEntityRecognitionSkill",
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

#### <a name="about-the-request"></a>Tudnivalók a kérésről

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
PUT https://[servicename].search.windows.net/indexes/demoindex?api-version=2017-11-11-Preview
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

Eddig létrehozott egy adatforrást, egy képességcsoportot és egy indexet. Ez a három összetevő egy olyan [indexelő](search-indexer-overview.md) része lesz, amely az egyes részeket egyetlen többszakaszos műveletben egyesíti. A három rész egy indexelőben való egyesítéséhez mezőleképezéseket kell meghatároznia. A mezőleképezések az indexelő definíciójának részét képezik, és a kérés elküldésekor átalakításokat hajtanak végre.

Nem bővített indexelés esetén az indexelő definíciója egy nem kötelező *fieldMappings* (Mezőleképezések) nevű szakaszt is tartalmaz, ha a mezők neve vagy adattípusa nem pontosan egyezik, vagy ha függvényt szeretne használni.

Bővítési folyamattal rendelkező kognitív keresési számítási feladatok estén az indexelő egy *outputFieldMappings* (Kimeneti-mezőleképezések) nevű szakaszt is igényel. Ezek a leképezéseket a rendszer akkor használja, ha egy belső folyamat (a bővítési folyamat) szerepel a mezők értékeinek forrásaként. Az *outputFieldMappings* egyedi jellemzői közé tartozik a bővítés részeként (a formázó képességgel) létrehozott komplex típusok kezelésének képessége. Emellett dokumentumonként még számos elem előfordulhat (például több szervezet egy dokumentumon belül). Az *outputFieldMappings* szerkezet utasíthatja a rendszert arra, hogy az elemek gyűjteményét „összesimítsa” egyetlen rekordba.

### <a name="sample-request"></a>Kérésminta

Mielőtt elindítaná ezt a REST-hívást, ne feledje lecserélni az alábbi kérésben szereplő szolgáltatásnevet és a rendszergazdai kulcsot, ha az eszköz két hívás között nem őrzi meg a kérés fejlécét. 

Emellett adja meg az indexelő nevét is. Az oktatóanyag hátralévő részében használja a ```demoindexer``` nevet.

```http
PUT https://[servicename].search.windows.net/indexers/demoindexer?api-version=2017-11-11-Preview
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

### <a name="explore-the-request-body"></a>Ismerkedés a kéréstörzzsel

A szkript -1 értéket állít be a ```"maxFailedItems"``` paraméterhez, amely utasítja az indexelőmotort, hogy hagyja figyelmen kívül az adatimportálás közben felmerülő hibákat. Ez azért hasznos, mert az adatforrás kevés dokumentumot tartalmaz. Nagyobb méretű adatforrás esetén 0-nál nagyobb értéket kell megadnia.

Figyelje meg a ```"dataToExtract":"contentAndMetadata"``` utasítást a konfigurációs paraméterekben. Ez az utasítást meghatározza, hogy az indexelő automatikusan kinyerje a tartalmat a különböző fájlformátumokból, beleértve az egyes fájlokra vonatkozó metaadatokat is. 

Tartalom kinyerésekor az ```imageAction``` beállításával kinyerheti a szöveget az adatforrásban talált képekből. A ```"imageAction":"generateNormalizedImages"``` konfigurációs, optikai Karakterfelismerés szakértelem és szöveges egyesítése szakértelem arra utasítja a szöveg kinyerése a rendszerképek (például a word-forgalom bejelentkezéshez leállítása a "stop"), és beágyazza azt content mezőjének részeként az indexelő. Ez a működés mind a dokumentumokban beágyazott képekre (például egy PDF-fájlban található képre), mind az adatforrásban talált képekre (például egy JPG-fájlra) vonatkozik.

## <a name="check-indexer-status"></a>Az indexelő állapotának ellenőrzése

Az indexelő meghatározását követően az indexelő a kérés elküldésekor automatikusan lefut. Az indexelés a vártnál tovább is eltarthat attól függően, hogy mely kognitív képességeket adta meg. Ha kíváncsi rá, hogy az indexelő fut-e még, küldje el a következő kérést az indexelő állapotának ellenőrzéséhez.

```http
GET https://[servicename].search.windows.net/indexers/demoindexer/status?api-version=2017-11-11-Preview
api-key: [api-key]
Content-Type: application/json
```

A válasz megmutatja, hogy fut-e az indexelő. Ha az indexelés befejeződött, használjon egy másik HTTP GET-parancsot a STATUS végponton (a fent látható módon) a bővítés során esetlegesen felmerülő hibákat tartalmazó jelentések és figyelmeztetések megjelenítéséhez.  

A figyelmeztetések bizonyos forrásfájl- és képességkombinációk esetében gyakoriak, és nem mindig utalnak hibára. Ebben az oktatóanyagban a figyelmeztetések jóindulatúak (például nincs szöveges bemenet a JPEG-fájlokból). Az állapotválasz áttekintésével részletes információkhoz juthat az indexelés során felmerülő figyelmeztetésekkel kapcsolatban.
 
## <a name="verify-content"></a>Tartalom ellenőrzése

Az indexelés befejezését követően futtasson olyan lekérdezéseket, amelyek egyéni mezők tartalmát adják vissza. Alapértelmezés szerint az Azure Search az első 50 eredményt adja vissza. A mintaadatok mérete kicsi, ezért az alapértelmezett beállítás megfelelő. Azonban nagyobb méretű adatkészletek esetén előfordulhat, hogy a lekérdezési karakterláncban meg kell adnia a megfelelő paramétereket, hogy a rendszer több eredményt adjon vissza. Részletes utasításokért lásd: [Eredmények lapokra bontása az Azure Searchben](search-pagination-page-layout.md).

Ellenőrzési lépésként kérdezze le az index összes mezőjét.

```http
GET https://[servicename].search.windows.net/indexes/demoindex?api-version=2017-11-11-Preview
api-key: [api-key]
Content-Type: application/json
```

A kimenet az egyes mezők nevét, típusát és attribútumait tartalmazó indexséma.

Indítson egy másodlagos lekérdezést a `"*"` kifejezésre egy mező, például az `organizations` összes tartalmának lekéréséhez.

```http
GET https://[servicename].search.windows.net/indexes/demoindex/docs?search=*&$select=organizations&api-version=2017-11-11-Preview
api-key: [api-key]
Content-Type: application/json
```

Ismételje meg a műveletet a gyakorlatban szereplő content (tartalom), language (nyelv), keyphrases (kulcskifejezések) és organizations (szervezetek) mezővel. Egyszerre több mezőt is lekérhet a vesszővel elválasztott listát használó `$select` megadásával.

A lekérdezési karakterlánc összetettségétől és hosszától függően használhatja a GET vagy a POST metódust. További információkért lásd: [Lekérdezés a REST API-val](https://docs.microsoft.com/rest/api/searchservice/search-documents).

<a name="access-enriched-document"></a>

## <a name="accessing-the-enriched-document"></a>A bővített dokumentum elérése

A kognitív kereséssel megtekintheti a bővített dokumentum szerkezetét. A bővített dokumentumok a bővítés során létrejött, majd a folyamat végeztével törlődő ideiglenes szerkezetek.

Ha pillanatképet szeretne készíteni az indexelés során létrejött bővített dokumentumról, adja hozzá az indexhez az ```enriched``` mezőt. Az indexelő automatikusan hozzáadja a mezőhöz az adott dokumentum bővítéseinek karakterláncos leképezését.

Az ```enriched``` mező egy sztringet tartalmaz, amely a JSON-ban szereplő memóriabeli bővített dokumentum logikai leképezése.  A mező értéke azonban egy érvényes JSON-dokumentum. Mivel az idézőjelek előtt escape-karakter áll, a `\"` karaktereket `"` karakterre kell cserélnie, ha a dokumentumot formázott JSON-ként szeretné megtekinteni.  

Az ```enriched``` mező célja a hibakeresés, annak érdekében, hogy segítsen azon tartalom logikai alakzatának megértésében, amely szerint a kifejezések értékelve lesznek. Ez hasznos eszközként szolgálat a képességcsoport megértéséhez és hibakereséséhez.

Ismételje meg az előző gyakorlatot, egy `enriched` mezőt is megadva a bővített dokumentum tartalmának rögzítéséhez:

### <a name="request-body-syntax"></a>Kéréstörzs szintaxisa
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
    },
    {
      "name": "enriched",
      "type": "Edm.String",
      "searchable": false,
      "sortable": false,
      "filterable": false,
      "facetable": false
    }
  ]
}
```
<a name="reset"></a>

## <a name="reset-and-rerun"></a>Alaphelyzetbe állítás és ismételt futtatás

A folyamat fejlesztésének kezdeti, kísérleti szakaszaiban a kialakítás legpraktikusabb megközelítése az, ha törli az objektumokat az Azure Search szolgáltatásból, és hagyja, hogy a kód újraépítse azokat. Az erőforrásnevek egyediek. Egy objektum törlése révén újból létrehozhatja azt ugyanazzal a névvel.

Ha az új definíciókkal újra el szeretné végezni a dokumentumok indexelését:

1. Törölje az indexet a megőrzött adatok eltávolításához. Törölje az indexet, hogy újra létrehozhassa a szolgáltatásban.
2. Módosítson egy képességcsoport és egy indexdefiníciót.
3. Hozza létre újból az indexet és az indexelőt a szolgáltatásban a folyamat futtatásához. 

Használhatja a portál törli az indexek, indexelők és ismereteket.

```http
DELETE https://[servicename].search.windows.net/skillsets/demoskillset?api-version=2017-11-11-Preview
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
> [Példa: hozzon létre egy egyéni szakértelem](cognitive-search-create-custom-skill-example.md)
