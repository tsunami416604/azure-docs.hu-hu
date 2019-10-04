---
title: 'REST-oktatóanyag: AI-bővítési folyamat létrehozása a kognitív kereséssel – Azure Search'
description: Átlépési példa a szöveg kinyerésére és a természetes nyelvi feldolgozásra a JSON-blobokban lévő tartalommal a Poster és a Azure Search REST API-k használatával.
manager: nitinme
author: luiscabrer
services: search
ms.service: search
ms.topic: tutorial
ms.date: 08/23/2019
ms.author: luisca
ms.openlocfilehash: 6f7c5e2955c57e0e1891593504e5eec1a06bbb04
ms.sourcegitcommit: 3f22ae300425fb30be47992c7e46f0abc2e68478
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/25/2019
ms.locfileid: "71265369"
---
# <a name="tutorial-add-structure-to-unstructured-content-with-cognitive-search"></a>Oktatóanyag: Struktúra hozzáadása strukturálatlan tartalomhoz kognitív kereséssel

Ha strukturálatlan szöveg-vagy képtartalommal rendelkezik, a Azure Search [kognitív keresési](cognitive-search-concept-intro.md) funkciója segítséget nyújt az adatok kinyeréséhez és olyan új tartalmak létrehozásához, amelyek hasznosak a teljes szöveges kereséshez és a tudás-adatbányászati forgatókönyvekhez. Bár a kognitív keresés képes a képfájlok (JPG, PNG, TIFF) feldolgozására, ez az oktatóanyag a Word-alapú tartalomra összpontosít, a nyelvfelismerés és a szöveges elemzés alkalmazásával új mezőket és információkat hozhat létre a lekérdezésekben, a dimenziókban és a szűrőkben.

> [!div class="checklist"]
> * Az Azure Blob Storage-ban a teljes dokumentumokkal (strukturálatlan szöveggel), például a PDF, az MD, a DOCX és a PPTX formátummal kezdheti meg a használatot.
> * Definiáljon egy olyan folyamatot, amely kibontja a szöveget, észleli a nyelvet, felismeri az entitásokat, és észleli a legfontosabb kifejezéseket.
> * Definiáljon egy indexet a kimenet (nyers tartalom, valamint a folyamat által generált név-érték párok) tárolására.
> * A folyamat végrehajtásával megkezdheti az átalakításokat és az elemzést, valamint az index létrehozását és betöltését.
> * A teljes szöveges kereséssel és a részletes lekérdezési szintaxissal megismerheti az eredményeket.

A bemutató elvégzéséhez több szolgáltatásra is szüksége lesz, valamint a [Poster Desktop alkalmazás](https://www.getpostman.com/) vagy egy másik webes tesztelési eszköz, amellyel REST API hívásokat hajthat végre. 

Ha nem rendelkezik Azure-előfizetéssel, a Kezdés előtt nyisson meg egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) .

## <a name="download-files"></a>Fájlok letöltése

1. Nyissa meg ezt a [OneDrive mappát](https://1drv.ms/f/s!As7Oy81M_gVPa-LCb5lC_3hbS-4) , és a bal felső sarokban kattintson a **Letöltés** elemre a fájlok számítógépre másolásához. 

1. Kattintson a jobb gombbal a zip-fájlra, és válassza az **összes kibontása**lehetőséget. A különböző típusok 14 fájlból állnak. Ehhez a gyakorlathoz a 7-et fogja használni.

## <a name="1---create-services"></a>1 – szolgáltatások létrehozása

Ez az útmutató Azure Searcht használ az indexeléshez és lekérdezésekhez, Cognitive Services az AI-bővítéshez és az Azure Blob Storage-hoz az adatkezeléshez. Ha lehetséges, hozzon létre mindhárom szolgáltatást ugyanabban a régióban és erőforráscsoporthoz a közelség és kezelhetőség érdekében. A gyakorlatban az Azure Storage-fiók bármely régióban lehet.

### <a name="start-with-azure-storage"></a>Első lépések az Azure Storage-ban

1. [Jelentkezzen be a Azure Portalba](https://portal.azure.com/) , és kattintson az **+ erőforrás létrehozása**elemre.

1. Keressen rá a *Storage-fiókra* , és válassza ki a Microsoft Storage-fiók ajánlatát.

   ![Storage-fiók létrehozása](media/cognitive-search-tutorial-blob/storage-account.png "Storage-fiók létrehozása")

1. Az alapok lapon a következő elemek szükségesek. Minden más esetében fogadja el az alapértelmezett értékeket.

   + **Erőforráscsoport**. Válasszon ki egy meglévőt, vagy hozzon létre egy újat, de ugyanazt a csoportot használja az összes szolgáltatáshoz, hogy együtt lehessen kezelni őket.

   + A **Storage-fiók neve**. Ha úgy gondolja, hogy több erőforrása is van ugyanazzal a típussal, használja a nevet típus és régió szerint egyértelműsítse, például *blobstoragewestus*. 

   + **Hely**. Ha lehetséges, válassza ki a Azure Search és Cognitive Serviceshoz használt helyet. Egyetlen hely érvényteleníti A sávszélességgel kapcsolatos díjakat.

   + **Fiók típusa**. Válassza ki az alapértelmezett *StorageV2 (általános célú v2)* .

1. A szolgáltatás létrehozásához kattintson a **felülvizsgálat + létrehozás** lehetőségre.

1. A létrehozás után kattintson **az erőforrás** megnyitása lehetőségre az Áttekintés lap megnyitásához.

1. Kattintson a **Blobok** szolgáltatás elemre.

1. Kattintson a **+ tároló** elemre egy tároló létrehozásához, és nevezze el a *fogaskerék-Search-demo kifejezést*.

1. Válassza a *fogaskerék-keresés-bemutató* lehetőséget, majd kattintson a **feltöltés** gombra, és nyissa meg azt a mappát, ahová a letöltött fájlokat mentette. Válassza ki az összes nem képfájlt. 7 fájlnak kell lennie. A feltöltéshez kattintson **az OK** gombra.

   ![Minta fájlok feltöltése](media/cognitive-search-tutorial-blob/sample-files.png "Minta fájlok feltöltése")

1. Mielőtt elkezdené az Azure Storage-t, szerezzen be egy kapcsolatok karakterláncot, hogy Azure Search-ban is megfogalmazható legyen a kapcsolatok. 

   1. Lépjen vissza a Storage-fiók áttekintés lapjára (az általunk használt *blobstragewestus* példaként használták). 
   
   1. A bal oldali navigációs panelen válassza a **hozzáférési kulcsok** lehetőséget, és másolja a kapcsolati karakterláncok egyikét. 

   A kapcsolódási karakterlánc az alábbi példához hasonló URL-cím:

      ```http
      DefaultEndpointsProtocol=https;AccountName=cogsrchdemostorage;AccountKey=<your account key>;EndpointSuffix=core.windows.net
      ```

1. Mentse a kapcsolódási karakterláncot a Jegyzettömbbe. Később szüksége lesz rá az adatforrás-kapcsolatok beállításakor.

### <a name="cognitive-services"></a>Cognitive Services

A kognitív keresésekben a mesterséges intelligenciát Cognitive Services, a természetes nyelv és a képfeldolgozás Text Analytics és Computer Vision is támogatja. Ha a cél egy tényleges prototípus vagy projekt végrehajtása volt, akkor ezen a ponton Cognitive Services (ugyanabban a régióban kell kiépíteni, mint Azure Search), hogy az indexelési műveletekhez csatolható legyen.

Ennél a gyakorlatnál azonban kihagyhatja az erőforrás-kiépítés lehetőségeit, mert Azure Search a háttérben Cognitive Services csatlakozhat a jelenetek mögött, és az indexelő futtatásakor 20 ingyenes tranzakciót biztosít. Mivel ez az oktatóanyag 7 tranzakciót használ, az ingyenes kiosztás elegendő. Nagyobb projektek esetében tervezze meg Cognitive Services kiépítés az utólagos elszámolású S0 szinten. További információ: [Cognitive Services csatolása](cognitive-search-attach-cognitive-services.md).

### <a name="azure-search"></a>Azure Search

A harmadik összetevő Azure Search, amelyet [a portálon lehet létrehozni](search-create-service-portal.md). A bemutató elvégzéséhez használhatja az ingyenes szintet. 

Ahogy az Azure Blob Storage-hoz, szánjon egy kis időt a hozzáférési kulcs gyűjtésére. Tovább, amikor megkezdi a kérelmek strukturálását, meg kell adnia az egyes kérések hitelesítéséhez használt Endpoint és admin API-kulcsot.

### <a name="get-an-admin-api-key-and-url-for-azure-search"></a>Rendszergazdai API-kulcs és URL-cím beszerzése Azure Search

1. [Jelentkezzen be a Azure Portalba](https://portal.azure.com/), és a keresési szolgáltatás **áttekintése** lapon szerezze be a keresési szolgáltatás nevét. A szolgáltatás nevét a végpont URL-címének áttekintésével ellenőrizheti. Ha a végpont URL- `https://mydemo.search.windows.net`címe volt, a szolgáltatás neve `mydemo`a következő lesz:.

2. A **Beállítások** > **kulcsaiban**kérjen meg egy rendszergazdai kulcsot a szolgáltatásra vonatkozó összes jogosultsághoz. Az üzletmenet folytonossága érdekében két, egymással megváltoztathatatlan rendszergazdai kulcs áll rendelkezésre. Az objektumok hozzáadására, módosítására és törlésére vonatkozó kérésekhez használhatja az elsődleges vagy a másodlagos kulcsot is.

    Kérje le a lekérdezési kulcsot is. Ajánlott a lekérdezési kérelmeket csak olvasási hozzáféréssel kibocsátani.

![A szolgáltatás nevének és a rendszergazda és a lekérdezési kulcsok beszerzése](media/search-get-started-nodejs/service-name-and-keys.png)

Minden kérelemhez API-kulcs szükséges a szolgáltatásnak küldött összes kérelem fejlécében. Egy érvényes kulcs a kérést küldő alkalmazás és az azt kezelő szolgáltatás közötti megbízhatósági kapcsolatot hoz létre a kérelmek alapján.

## <a name="2---set-up-postman"></a>2 – Poster beállítása

Indítsa el a Postmant, és hozzon létre egy HTTP-kérelmet. Ha nem ismeri ezt az eszközt, tekintse meg a következőt: [Azure Search REST API-k megismerése a Poster használatával](search-get-started-postman.md).

Az oktatóanyagban használt metódusok a **post**, a **put**és a **Get**. A következő módszerekkel hozhat létre négy API-hívást a keresési szolgáltatáshoz: adatforrás, készségkészlet, index és indexelő létrehozása.

A fejlécek területen állítsa be a "Content- `application/json` type" `api-key` értéket, és állítsa a Azure Search szolgáltatás felügyeleti API-kulcsára. Miután beállította a fejléceket, használhatja azokat minden kérelemhez ebben a gyakorlatban.

  ![Poster-kérelem URL-címe és fejléce](media/search-get-started-postman/postman-url.png "Poster-kérelem URL-címe és fejléce")

## <a name="3---create-the-pipeline"></a>3 – a folyamat létrehozása

Azure Search az AI-feldolgozás az indexelés (vagy az adatfeldolgozás) során következik be. Az útmutató ezen része négy objektumot hoz létre: adatforrás, index definíció, készségkészlet, indexelő. 

### <a name="step-1-create-a-data-source"></a>1\. lépés: Adatforrás létrehozása

Az [adatforrás-objektumok](https://docs.microsoft.com/rest/api/searchservice/create-data-source) biztosítják a kapcsolódási karakterláncot a fájlokat tartalmazó blob-tárolóhoz.

1. A **post** és a következő URL-cím helyett használja a-Service-Name kifejezést a szolgáltatás tényleges nevével.

   ```http
   https://[YOUR-SERVICE-NAME].search.windows.net/datasources?api-version=2019-05-06
   ```

1. A kérelem **törzsében**másolja a következő JSON-definíciót, `connectionString` és cserélje le a-t a Storage-fiókja tényleges kapcsolatára. 

   Ne felejtse el szerkeszteni a tároló nevét is. Egy korábbi lépésben a tároló neveként a "fogaskerék-keresés – bemutató" kifejezést javasoltuk.

    ```json
    {
      "name" : "cog-search-demo-ds",
      "description" : "Demo files to demonstrate cognitive search capabilities.",
      "type" : "azureblob",
      "credentials" :
      { "connectionString" :
        "DefaultEndpointsProtocol=https;AccountName=<YOUR-STORAGE-ACCOUNT>;AccountKey=<YOUR-ACCOUNT-KEY>;"
      },
      "container" : { "name" : "<YOUR-BLOB-CONTAINER-NAME>" }
    }
    ```
1. Küldje el a kérést. Meg kell jelennie a 201-es állapotkód megerősítő sikerességének. 

Ha a 403-as vagy 404-es hibát kapja, ellenőrizze a kérés szerkezetét: az `api-version=2019-05-06` legyen a végpont, az `api-key` szerepeljen a fejlécben a `Content-Type` kifejezés után, az értékének pedig érvényesnek kell lennie egy keresési szolgáltatáshoz. Előfordulhat, hogy a JSON-dokumentumot egy online JSON-érvényesítő használatával szeretné futtatni, hogy ellenőrizze a szintaxis helyességét. 

### <a name="step-2-create-a-skillset"></a>2\. lépés: Képességcsoport létrehozása

A [készségkészlet objektum](https://docs.microsoft.com/rest/api/searchservice/create-skillset) a tartalomra alkalmazott dúsítási lépések halmaza. 

1. Használja a **put** és a következő URL-címet, és cserélje le a-Service-Name kifejezést a szolgáltatás tényleges nevére.

    ```http
    https://[YOUR-SERVICE-NAME].search.windows.net/skillsets/cog-search-demo-ss?api-version=2019-05-06
    ```

1. A kérelem **törzsében**másolja az alábbi JSON-definíciót. Ez a készségkészlet a következő beépített készségekből áll.

   | Ügyességi                 | Leírás    |
   |-----------------------|----------------|
   | [Entitások felismerése](cognitive-search-skill-entity-recognition.md) | Kibontja a személyek, szervezetek és helyszínek nevét a blob-tároló tartalmából. |
   | [Nyelvfelismerés](cognitive-search-skill-language-detection.md) | Észleli a tartalom nyelvét. |
   | [Szöveg felosztása](cognitive-search-skill-textsplit.md)  | Megszakítja a nagyméretű tartalmakat kisebb adattömbökbe, mielőtt meghívja a Key kifejezés kinyerési készségét. A kulcskifejezések kinyerése legfeljebb 50 000 karakter méterű bemeneteket fogad el. A mintafájlok közül néhányat fel kell osztani ahhoz, hogy beleférjen a korlátozásba. |
   | [Kulcskifejezések kinyerése](cognitive-search-skill-keyphrases.md) | Lekéri a legfontosabb mondatokat. |

   Minden képesség a dokumentum tartalmán fut le. A feldolgozás során az Azure Search a különféle fájlformátumok tartalmának olvasása érdekében minden dokumentumot tördel. A forrásfájlban talált szöveg a létrehozott ```content``` mezőbe kerül, amelyből dokumentumonként egy jön létre. A bemenet így lesz ```"/document/content"```.

   A legfontosabb mondatok kinyeréséhez, mivel a Text Splitter skill használatával nagyobb méretű fájlokat kell megtörni a lapokra, a kulcs kifejezés kinyerési képességének ```"document/pages/*"``` (a dokumentum minden oldalához) kontextusa a ```"/document/content"```helyett.

    ```json
    {
      "description": "Extract entities, detect language and extract key-phrases",
      "skills":
      [
        {
          "@odata.type": "#Microsoft.Skills.Text.EntityRecognitionSkill",
          "categories": [ "Person", "Organization", "Location" ],
          "defaultLanguageCode": "en",
          "inputs": [
            { "name": "text", "source": "/document/content" }
          ],
          "outputs": [
            { "name": "persons", "targetName": "persons" },
            { "name": "organizations", "targetName": "organizations" },
            { "name": "locations", "targetName": "locations" }
          ]
        },
        {
          "@odata.type": "#Microsoft.Skills.Text.LanguageDetectionSkill",
          "inputs": [
            { "name": "text", "source": "/document/content" }
          ],
          "outputs": [
            { "name": "languageCode", "targetName": "languageCode" }
          ]
        },
        {
          "@odata.type": "#Microsoft.Skills.Text.SplitSkill",
          "textSplitMode" : "pages",
          "maximumPageLength": 4000,
          "inputs": [
            { "name": "text", "source": "/document/content" },
            { "name": "languageCode", "source": "/document/languageCode" }
          ],
          "outputs": [
            { "name": "textItems", "targetName": "pages" }
          ]
        },
        {
          "@odata.type": "#Microsoft.Skills.Text.KeyPhraseExtractionSkill",
          "context": "/document/pages/*",
          "inputs": [
            { "name": "text", "source": "/document/pages/*" },
            { "name":"languageCode", "source": "/document/languageCode" }
          ],
          "outputs": [
            { "name": "keyPhrases", "targetName": "keyPhrases" }
          ]
        }
      ]
    }
    ```
    Alább a képességcsoport grafikai ábrázolása látható. 

    ![Képességcsoport értelmezése](media/cognitive-search-tutorial-blob/skillset.png "Képességcsoport értelmezése")

1. Küldje el a kérést. A Poster-nek a 201-es állapotkódot kell visszaadnia. 

> [!NOTE]
> A kimenetek hozzárendelhetők egy indexhez, bemenetként használhatók egy alsóbb rétegbeli képességhez, vagy a fentiek mindegyike lehetséges, akárcsak a nyelvkód esetében. Az indexben a nyelvkód a szűréskor lehet hasznos. A nyelvkódot bemenetként a szövegelemzési képességek használják, a szótördeléssel kapcsolatos nyelvi szabályok megadásához. A képességcsoportok alapvető tudnivalóval kapcsolatos bővebb információkért lásd: [Képességcsoport megadása](cognitive-search-defining-skillset.md).

### <a name="step-3-create-an-index"></a>3\. lépés: Index létrehozása

Az [index](https://docs.microsoft.com/rest/api/searchservice/create-index) biztosítja azt a sémát, amely a tartalom fizikai kifejezésének létrehozásához használható invertált indexekben és más szerkezetekben Azure Search. Az index legnagyobb összetevője a mezők gyűjteménye, ahol az adattípus és az attribútumok határozzák meg a tartalmakat és a viselkedéseket Azure Searchban.

1. Használja az **put** és a következő URL-címet, és cserélje le a-Service-Name kifejezést a szolgáltatás tényleges nevére, és nevezze el az indexet.

   ```http
   https://[YOUR-SERVICE-NAME].search.windows.net/indexes/cog-search-demo-idx?api-version=2019-05-06
   ```

1. A kérelem **törzsében**másolja a következő JSON-definíciót. A `content` mező maga tárolja a dokumentumot. További mezők a `languageCode`, `keyPhrases`a és `organizations` a készségkészlet által létrehozott új információk (mezők és értékek) megjelenítéséhez.

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
          "name": "metadata_storage_name",
          "type": "Edm.String",
          "searchable": false,
          "filterable": false,
          "facetable": false,
          "sortable": false
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
          "name": "persons",
          "type": "Collection(Edm.String)",
          "searchable": true,
          "sortable": false,
          "filterable": true,
          "facetable": true
        },
        {
          "name": "organizations",
          "type": "Collection(Edm.String)",
          "searchable": true,
          "sortable": false,
          "filterable": true,
          "facetable": true
        },
        {
          "name": "locations",
          "type": "Collection(Edm.String)",
          "searchable": true,
          "sortable": false,
          "filterable": true,
          "facetable": true
        }
      ]
    }
    ```

1. Küldje el a kérést. A Poster-nek a 201-es állapotkódot kell visszaadnia. 

### <a name="step-4-create-and-run-an-indexer"></a>4\. lépés: Indexelő létrehozása és futtatása

Az [Indexelő](https://docs.microsoft.com/rest/api/searchservice/create-indexer) vezeti a folyamatot. Az eddig létrehozott három összetevő (az adatforrás, a készségkészlet, az index) egy indexelő bemenete. A Azure Search indexelő létrehozása az a folyamat, amely a teljes folyamatot mozgásba helyezi. 

1. Használja az **put** és a következő URL-címet, és cserélje le a-Service-Name kifejezést a szolgáltatás tényleges nevére, hogy az indexelő nevet adja.

   ```http
   https://[servicename].search.windows.net/indexers/cog-search-demo-idxr?api-version=2019-05-06
   ```

1. A kérelem **törzsében**másolja az alábbi JSON-definíciót. Figyelje meg a mező-hozzárendelési elemeket; Ezek a leképezések azért fontosak, mert definiálják az adatfolyamot. 

   A `fieldMappings` feldolgozása a készségkészlet előtt történik, és az adatforrásból az indexben lévő mezőkbe küld tartalmat. A mező-hozzárendelések használatával meglévő, nem módosított tartalmakat küldhet az indexbe. Ha a mezők nevei és típusai mindkét végén azonosak, nincs szükség leképezésre.

   A `outputFieldMappings` a szaktudás által létrehozott mezőkre vonatkoznak, amelyek feldolgozása a készségkészlet futtatása után történik. A `sourceFieldNames` alkalmazásban `outputFieldMappings` való hivatkozás nem létezik, amíg a dokumentum repedése vagy a dúsítása létre nem hozza őket. A `targetFieldName` az index sémában definiált index mezője.

    ```json
    {
      "name":"cog-search-demo-idxr",    
      "dataSourceName" : "cog-search-demo-ds",
      "targetIndexName" : "cog-search-demo-idx",
      "skillsetName" : "cog-search-demo-ss",
      "fieldMappings" : [
        {
          "sourceFieldName" : "metadata_storage_path",
          "targetFieldName" : "id",
          "mappingFunction" :
            { "name" : "base64Encode" }
        },
        {
          "sourceFieldName" : "metadata_storage_name",
          "targetFieldName" : "metadata_storage_name",
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
          "sourceFieldName" : "/document/persons",
          "targetFieldName" : "persons"
        },
        {
          "sourceFieldName" : "/document/organizations",
          "targetFieldName" : "organizations"
        },
        {
          "sourceFieldName" : "/document/locations",
          "targetFieldName" : "locations"
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
          "parsingMode": "default",
          "firstLineContainsHeaders": false,
          "delimitedTextDelimiter": ","
        }
      }
    }
    ```

1. Küldje el a kérést. A Poster-nek a 201-as állapotkódot kell visszaadnia, amely megerősíti a sikeres feldolgozást. 

   A lépés utasításainak végrehajtása több percig is eltarthat. Annak ellenére, hogy az adatkészlet kis méretű, az analitikai képességek számítási igénye nagy. 

> [!NOTE]
> Az indexelő létrehozása elindítja a folyamatot. Ha probléma lép fel az adatok elérésével, a bemenetek és kimenetek leképezésével vagy a műveletek sorrendjével kapcsolatban, az ebben a szakaszban jelenik meg. Ha kód- vagy szkriptmódosításokkal szeretné újra futtatni a folyamatot, lehetséges, hogy először el kell távolítania az objektumokat. További információk: [Alaphelyzetbe állítás és ismételt futtatás](#reset).

#### <a name="about-indexer-parameters"></a>Az indexelő paramétereinek ismertetése

A szkript -1 értéket állít be a ```"maxFailedItems"``` paraméterhez, amely utasítja az indexelőmotort, hogy hagyja figyelmen kívül az adatimportálás közben felmerülő hibákat. Ez azért fogadható el, mert a bemutató adatforrásának van ilyen kevés dokumentuma. Nagyobb méretű adatforrás esetén 0-nál nagyobb értéket kell megadnia.

Az ```"dataToExtract":"contentAndMetadata"``` utasítás azt jelzi, hogy az indexelő automatikusan Kinyeri a tartalmat a különböző fájlformátumokból, valamint az egyes fájlokhoz kapcsolódó metaadatokat. 

Tartalom kinyerésekor az ```imageAction``` beállításával kinyerheti a szöveget az adatforrásban talált képekből. A ```"imageAction":"generateNormalizedImages"``` konfiguráció az OCR-képesség és a szöveg egyesítése képességgel együtt azt jelzi, hogy az indexelő Kinyeri a szöveget a képekből (például a "Leállítás" szót egy forgalom leállításakor), és beágyazza azt a tartalom mező részévé. Ez a működés mind a dokumentumokban beágyazott képekre (például egy PDF-fájlban található képre), mind az adatforrásban talált képekre (például egy JPG-fájlra) vonatkozik.

## <a name="4---monitor-indexing"></a>4 – indexelés figyelése

Az indexelés és a dúsítás a Create Indexer-kérelem elküldésekor azonnal megkezdődik. Az indexelés eltarthat egy ideig, attól függően, hogy milyen kognitív ismereteket határoz meg. Ha kíváncsi rá, hogy az indexelő fut-e még, küldje el a következő kérést az indexelő állapotának ellenőrzéséhez.

1. Használja a **Get** és a következő URL-címet, és cserélje le a-Service-Name kifejezést a szolgáltatás tényleges nevére, hogy az indexelő nevet adja.

   ```http
   https://[YOUR-SERVICE-NAME].search.windows.net/indexers/cog-search-demo-idxr/status?api-version=2019-05-06
   ```

1. Tekintse át a választ, és Ismerje meg, hogy az indexelő fut-e, vagy hogy megtekintse a hiba-és figyelmeztetési információkat.  

Ha az ingyenes szintet használja, a következő üzenet várható: "" nem tudott kinyerni tartalmat vagy metaadatokat a dokumentumból. Csonkolt kinyert szöveg a következő karakterekre: "32768". Ez az üzenet akkor jelenik meg, ha az ingyenes szinten lévő blob-indexelés[32K korláttal](search-limits-quotas-capacity.md#indexer-limits)rendelkezik. Ez az üzenet nem jelenik meg ennél a magasabb szintű adatkészletnél. 

> [!NOTE]
> A figyelmeztetések gyakran előfordulnak bizonyos helyzetekben, és nem mindig jeleznek problémát. Ha például egy blob-tároló képfájlokat tartalmaz, és a folyamat nem kezeli a lemezképeket, a rendszer figyelmeztetést küld arról, hogy a képek feldolgozása nem történt meg.

## <a name="5---search"></a>5 – keresés

Most, hogy új mezőket és információkat hozott létre, futtasson néhány lekérdezést a kognitív keresés értékének megismeréséhez, mivel az egy tipikus keresési forgatókönyvhöz kapcsolódik.

Ne felejtse el, hogy a blob tartalmával kezdtük el, ahol a teljes dokumentum `content` egyetlen mezőbe van csomagolva. Kereshet ebben a mezőben, és megkeresheti az egyezéseket a lekérdezésekben.

1. Használja a **Get** és a következő URL-címet, cserélje le a-Service-Name kifejezést a szolgáltatás tényleges nevére, és keressen rá egy kifejezés vagy kifejezés példányaira, `content` adja vissza a mezőt és a megfelelő dokumentumok számát.

   ```http
   https://[YOUR-SERVICE-NAME].search.windows.net/indexes/cog-search-demo-idx?search=*&$count=true&$select=content?api-version=2019-05-06
   ```
   
   A lekérdezés eredménye visszaküldi a dokumentum tartalmát, ami ugyanazt az eredményt kapja, ha a blob indexelő a kognitív keresési folyamat nélkül használta. Ez a mező kereshető, de nem használható, ha dimenziókat, szűrőket vagy automatikus kiegészítést szeretne használni.

   ![Tartalom mező kimenete](media/cognitive-search-tutorial-blob/content-output.png "Tartalom mező kimenete")
   
1. A második lekérdezés esetében a folyamat által létrehozott új mezőket (személyek, szervezetek, helyszínek, languageCode) kell visszaadnia. A rövidítések kimaradnak, de érdemes megadnia, ha meg szeretné jeleníteni ezeket az értékeket.

   ```http
   https://mydemo.search.windows.net/indexes/cog-search-demo-idx/docs?search=*&$count=true&$select=metadata_storage_name,persons,organizations,locations,languageCode&api-version=2019-05-06
   ```
   A $select utasítás mezői a Cognitive Services természetes nyelvi feldolgozási képességeiből létrehozott új információkat tartalmazzák. Ahogy várható, némi zaj van a dokumentumok eredményei és variációja között, de sok esetben az analitikai modellek pontos eredményeket hoznak létre.

   Az alábbi ábrán a a Microsoftnál a VEZÉRIGAZGATÓi szerepkört feltételezve, a következő képen láthatók a nagy-és nagybetűk.

   ![Folyamat kimenete](media/cognitive-search-tutorial-blob/pipeline-output.png "Folyamat kimenete")

1. Ha szeretné megtekinteni, hogyan használhatja ki ezeket a mezőket, adjon hozzá egy dimenziós paramétert a megfelelő dokumentumok hely szerinti összesítésének visszaadásához.

   ```http
   https://[YOUR-SERVICE-NAME].search.windows.net/indexes/cog-search-demo-idx/docs?search=*&facet=locations&api-version=2019-05-06
   ``` 

   Ebben a példában az egyes helyekhez 2 vagy 3 egyezés van.

   ![Dimenzió kimenete](media/cognitive-search-tutorial-blob/facet-output.png "Dimenzió kimenete")
   

1. Ebben a végső példában egy szűrőt alkalmaz a szervezetek gyűjteményére, és két egyezést ad vissza a NASDAQ alapján történő szűrési feltételekhez.

   ```http
   cog-search-demo-idx/docs?search=*&$filter=organizations/any(organizations: organizations eq 'NASDAQ')&$select=metadata_storage_name,organizations&$count=true&api-version=2019-05-06
   ```

Ezek a lekérdezések néhány módszert mutatnak be, amelyekkel a lekérdezési szintaxissal és szűrőkkel dolgozhat a kognitív keresés által létrehozott új mezőkben. További példákat a példák [a keresési dokumentumok REST API](https://docs.microsoft.com/rest/api/searchservice/search-documents#bkmk_examples), az [egyszerű szintaxisú lekérdezési példák](search-query-simple-examples.md)és a [teljes Lucene lekérdezési](search-query-lucene-examples.md)példák című részben talál.

<a name="reset"></a>

## <a name="reset-and-rerun"></a>Alaphelyzetbe állítás és ismételt futtatás

A folyamat fejlesztésének kezdeti, kísérleti szakaszaiban a kialakítás legpraktikusabb megközelítése az, ha törli az objektumokat az Azure Search szolgáltatásból, és hagyja, hogy a kód újraépítse azokat. Az erőforrásnevek egyediek. Egy objektum törlése révén újból létrehozhatja azt ugyanazzal a névvel.

Ha az új definíciókkal újra el szeretné végezni a dokumentumok indexelését:

1. Törölje az indexelő, az index és a készségkészlet.
2. Objektumok módosítása.
3. Hozza létre újból a szolgáltatást a folyamat futtatásához. 

A portál használatával törölhet indexeket, indexelő objektumokat és szakértelmével, vagy használhatja a Delete ( **Törlés** ) lehetőséget, és megadhatja az egyes objektumokhoz tartozó URL-címeket. A következő parancs törli az indexelő.

```http
DELETE https://[YOUR-SERVICE-NAME]].search.windows.net/indexers/cog-search-demo-idxr?api-version=2019-05-06
```

Sikeres törlés esetén a rendszer a 204-es állapotkódot adja vissza.

Ahogy a kód egyre kiforrottabbá válik, jó ötlet lehet az újraépítési stratégia finomítása. A további tudnivalókért lásd az [indexek újraépítését](search-howto-reindex.md) ismertető cikket.

## <a name="takeaways"></a>Legfontosabb ismeretek

Ez az oktatóanyag a bővített indexelőfolyamat a komponensek (adatforrás, képességcsoport, index és indexelő) létrehozásával való összeállításának alapvető lépéseit ismerteti.

Bemutattuk az [előre meghatározott képességeket](cognitive-search-predefined-skills.md), valamint a képességcsoport megadását, illetve a képességek bemenetek és kimenetek segítségével történő összekapcsolásának működését. Azt is megtanulta, hogy az indexelő definíciójának `outputFieldMappings` eleme szükséges a bővített értékek a folyamatból az Azure Search szolgáltatásban található, kereshető indexbe történő átirányításához.

Végül megismerte, hogyan tesztelheti az eredményeket, és hogyan állíthatja alaphelyzetbe a rendszert a későbbi futtatásokhoz. Megtanulta, hogy ha lekérdezéseket futtat az indexen, az a bővített indexelési folyamat által létrehozott kimenetet adja vissza. 

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Az oktatóanyagok után fölöslegessé vált elemek eltávolítása leggyorsabban az Azure Search és az Azure Blob szolgáltatást tartalmazó erőforráscsoport törlésével végezhető el. Feltéve, hogy mindkét szolgáltatást ugyanabban a csoportban helyezte üzembe, törölje az erőforráscsoportot, amellyel véglegesen eltávolíthatja annak teljes tartalmát, a rövid útmutató során létrehozott összes szolgáltatást és tárolt tartalmat is beleértve. A portálon az erőforráscsoport neve az egyes szolgáltatások Áttekintés lapján szerepel.

## <a name="next-steps"></a>További lépések

Egyéni képességekkel testre szabhatja vagy kibővítheti a folyamatot. Egyéni képességek létrehozása és egy képességcsoporthoz adása révén saját kezűleg írt szöveg- vagy képelemzést használhat. 

> [!div class="nextstepaction"]
> [Példa: Egyéni képesség létrehozása a kognitív kereséshez](cognitive-search-create-custom-skill-example.md)