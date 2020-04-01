---
title: 'Oktatóanyag: REST és AI azure blobok felett'
titleSuffix: Azure Cognitive Search
description: A Postman és az Azure Cognitive Search REST API-k használatával végighaladhatja a szövegkinyerés és a természetes nyelvi feldolgozás a Blob storage-ban lévő tartalmak feldolgozására.
manager: nitinme
author: luiscabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: tutorial
ms.date: 02/26/2020
ms.openlocfilehash: 8acafa14afab507b704806056efac0f877a47684
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "78190722"
---
# <a name="tutorial-use-rest-and-ai-to-generate-searchable-content-from-azure-blobs"></a>Oktatóanyag: A REST és a AI használatával kereshető tartalmat hozhat létre az Azure-blobokból

Ha strukturálatlan szöveggel vagy lemezképpel rendelkezik az Azure Blob storage-ban, az [AI-bővítési folyamat](cognitive-search-concept-intro.md) kinyerheti az információkat, és új tartalmat hozhat létre, amely hasznos a teljes szöveges kereséshez vagy a tudásbányászati forgatókönyvekhez. Bár egy folyamat képes feldolgozni a képeket, ez a REST-oktatóanyag a szövegre összpontosít, a nyelvfelismerés és a természetes nyelvi feldolgozás alkalmazásával olyan új mezőket hozhat létre, amelyeket a lekérdezésekben, a mezőkben és a szűrőkben használhat.

Ez az oktatóanyag a Postman és a [Search REST API-kat](https://docs.microsoft.com/rest/api/searchservice/) használja a következő feladatok végrehajtásához:

> [!div class="checklist"]
> * Kezdje a teljes dokumentumokkal (strukturálatlan szöveggel), például PDF, HTML, DOCX és PPTX az Azure Blob storage-ban.
> * Definiáljon egy folyamatot, amely kibontja a szöveget, felismeri a nyelvet, felismeri az entitásokat, és észleli a kulcskifejezéseket.
> * Index definiálása a kimenet tárolására (nyers tartalom, valamint a folyamat által generált név-érték párok).
> * A folyamat végrehajtása átalakítások és elemzések indításához, valamint az index létrehozásához és betöltéséhez.
> * Fedezze fel az eredményeket a teljes szöveges keresés és a gazdag lekérdezésszintaxis használatával.

Ha nem rendelkezik Azure-előfizetéssel, nyisson meg egy [ingyenes fiókot,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) mielőtt elkezdené.

## <a name="prerequisites"></a>Előfeltételek

+ [Azure Storage](https://azure.microsoft.com/services/storage/)
+ [Postman asztali alkalmazás](https://www.getpostman.com/)
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

## <a name="2---set-up-postman"></a>2 - Postás beállítása

Indítsa el a Postmant, és hozzon létre egy HTTP-kérelmet. Ha nem ismeri ezt az eszközt, [olvassa el az Azure Cognitive Search REST API-k felfedezése postán .](search-get-started-postman.md)

Az oktatóanyagban használt kérési módszerek a **KÖVETKEZŐK: POST**, **PUT**és **GET**. A módszerek segítségével négy API-hívást a keresési szolgáltatás: hozzon létre egy adatforrást, egy skillset, egy index, és egy indexelő.

A fejlécek, állítsa be a `application/json` "Tartalom-típus", és állítsa be `api-key` az Azure Cognitive Search szolgáltatás felügyeleti api-kulcs. Miután beállította a fejléceket, a gyakorlat minden kéréséhez használhatja őket.

  ![Postás kérelem URL-címe és fejléce](media/search-get-started-postman/postman-url.png "Postás kérelem URL-címe és fejléce")

## <a name="3---create-the-pipeline"></a>3 - A folyamat létrehozása

Az Azure Cognitive Search, AI-feldolgozás történik indexelés (vagy adatbetöltés) során történik. A forgatókönyv ezen része négy objektumot hoz létre: adatforrás, indexdefiníció, skillset, indexelő. 

### <a name="step-1-create-a-data-source"></a>1. lépés: Adatforrás létrehozása

Egy [adatforrás-objektum](https://docs.microsoft.com/rest/api/searchservice/create-data-source) biztosítja a kapcsolati karakterláncot a fájlokat tartalmazó Blob-tárolóhoz.

1. Használja a **POST-ot** és a következő URL-címet, és cserélje le a YOUR-SERVICE-NAME-t a szolgáltatás tényleges nevére.

   ```http
   https://[YOUR-SERVICE-NAME].search.windows.net/datasources?api-version=2019-05-06
   ```

1. A kérelem **ben Body,** másolja a következő `connectionString` JSON-definíció, helyett a tényleges kapcsolat a tárfiók. 

   Ne felejtse el a tároló nevét is szerkeszteni. Azt javasolta, "fogaskerék-search-demo" a tároló nevét egy korábbi lépésben.

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
1. Küldje el a kérést. Meg kell jelennie egy 201-es állapotkódnak, amely megerősíti a sikert. 

Ha a 403-as vagy 404-es hibát kapja, ellenőrizze a kérés szerkezetét: az `api-version=2019-05-06` legyen a végpont, az `api-key` szerepeljen a fejlécben a `Content-Type` kifejezés után, az értékének pedig érvényesnek kell lennie egy keresési szolgáltatáshoz. Előfordulhat, hogy a JSON-dokumentumot egy online JSON-érvényesítőn keresztül szeretné futtatni, hogy megbizonyosodjon arról, hogy a szintaxis helyes. 

### <a name="step-2-create-a-skillset"></a>2. lépés: Szakértelem létrehozása

A [skillset objektum](https://docs.microsoft.com/rest/api/searchservice/create-skillset) a tartalomra alkalmazott dúsítási lépések készlete. 

1. Használja a **PUT-ot** és a következő URL-címet, amely a YOUR-SERVICE-NAME nevet a szolgáltatás tényleges nevére cseréli.

    ```http
    https://[YOUR-SERVICE-NAME].search.windows.net/skillsets/cog-search-demo-ss?api-version=2019-05-06
    ```

1. A kérelem **törzse**alkalmazásban másolja az alábbi JSON-definíciót. Ez a skillset a következő beépített képességekből áll.

   | Ügyességi                 | Leírás    |
   |-----------------------|----------------|
   | [Entitások felismerése](cognitive-search-skill-entity-recognition.md) | Kinyeri a személyek, szervezetek és helyek nevét a blob tárolóban lévő tartalomból. |
   | [Nyelvfelismerés](cognitive-search-skill-language-detection.md) | Észleli a tartalom nyelvét. |
   | [Szöveg felosztása](cognitive-search-skill-textsplit.md)  | A nagy tartalmakat kisebb adattömbökre bontja, mielőtt meghívja a kulcskifejezés-kivonási készséget. A kulcskifejezések kinyerése legfeljebb 50 000 karakter méterű bemeneteket fogad el. A mintafájlok közül néhányat fel kell osztani ahhoz, hogy beleférjen a korlátozásba. |
   | [Kulcskifejezések kinyerése](cognitive-search-skill-keyphrases.md) | Kihúzza a legfontosabb kifejezéseket. |

   Minden képesség a dokumentum tartalmán fut le. A feldolgozás során az Azure Cognitive Search feltöri az egyes dokumentumokat, hogy különböző fájlformátumokból olvasson be tartalmakat. A forrásfájlban talált szöveg a létrehozott ```content``` mezőbe kerül, amelyből dokumentumonként egy jön létre. Mint ilyen, a ```"/document/content"```bemenet lesz .

   A kulcskifejezések kinyeréséhez, mivel a szövegosztó készségét használjuk a nagyobb fájlok ```"document/pages/*"``` oldalakra bontásához, a ```"/document/content"```kulcsmondat-kinyerési szakértelem kontextusa (a dokumentum minden egyes oldalához) a helyett.

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

    ![A skillset megismerése](media/cognitive-search-tutorial-blob/skillset.png "A skillset megismerése")

1. Küldje el a kérést. Postman vissza kell adnia egy 201-es állapotkódot, ami megerősíti a sikert. 

> [!NOTE]
> A kimenetek hozzárendelhetők egy indexhez, bemenetként használhatók egy alsóbb rétegbeli képességhez, vagy a fentiek mindegyike lehetséges, akárcsak a nyelvkód esetében. Az indexben a nyelvkód a szűréskor lehet hasznos. A nyelvkódot bemenetként a szövegelemzési képességek használják, a szótördeléssel kapcsolatos nyelvi szabályok megadásához. A képességcsoportok alapvető tudnivalóval kapcsolatos bővebb információkért lásd: [Képességcsoport megadása](cognitive-search-defining-skillset.md).

### <a name="step-3-create-an-index"></a>3. lépés: Index létrehozása

Az [index](https://docs.microsoft.com/rest/api/searchservice/create-index) biztosítja a séma létrehozásához használt a tartalom fordított indexek és egyéb konstrukciók az Azure Cognitive Search létrehozásához. Az index legnagyobb összetevője a mezők gyűjteménye, ahol az adatok típusa és attribútumok határozzák meg a tartalmakat és a viselkedést az Azure Cognitive Search.

1. Használja **a PUT-ot** és a következő URL-t, amely a YOUR-SERVICE-NAME-t a szolgáltatás tényleges nevére cseréli, hogy megnevezze az indexet.

   ```http
   https://[YOUR-SERVICE-NAME].search.windows.net/indexes/cog-search-demo-idx?api-version=2019-05-06
   ```

1. A kérelem **törzsében**másolja a következő JSON-definíciót. A `content` mező magát a bizonylatot tárolja. A további `languageCode` `keyPhrases`mezők `organizations` a alkalmazáshoz, és a skillset által létrehozott új információkat (mezőket és értékeket) jelölnek.

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

1. Küldje el a kérést. Postman vissza kell adnia egy 201-es állapotkódot, ami megerősíti a sikert. 

### <a name="step-4-create-and-run-an-indexer"></a>4. lépés: Indexelő létrehozása és futtatása

Egy [indexelő](https://docs.microsoft.com/rest/api/searchservice/create-indexer) vezéreli a folyamatot. Az eddig létrehozott három összetevő (adatforrás, skillset, index) egy indexelő bemenete. Az indexelő létrehozása az Azure Cognitive Search az az esemény, amely a teljes folyamat mozgásba lép. 

1. Használja **a PUT-ot** és a következő URL-t, amely a YOUR-SERVICE-NAME-t a szolgáltatás tényleges nevére cseréli, hogy megnevezze az indexelőt.

   ```http
   https://[servicename].search.windows.net/indexers/cog-search-demo-idxr?api-version=2019-05-06
   ```

1. A kérelem **törzse**alkalmazásban másolja az alábbi JSON-definíciót. Figyelje meg a mezőleképezési elemeket; ezek a leképezések azért fontosak, mert meghatározzák az adatfolyamot. 

   A `fieldMappings` feldolgozása a skillset előtt lesz, és az adatforrásból tartalmat küld az index célmezőinek. A mezőleképezések segítségével létező, módosítatlan tartalmat küldhet az indexbe. Ha a mezőnevek és -típusok mindkét végén azonosak, nincs szükség leképezésre.

   `outputFieldMappings` A a képzettségek által létrehozott mezőkre vannak, és így a skillset futtatása után kerülnek feldolgozásra. A hivatkozások `sourceFieldNames` nem `outputFieldMappings` léteznek, amíg a dokumentum repedés vagy dúsítása létre őket. Az `targetFieldName` egy indexmező, amelyet az indexsémában határoznak meg.

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

1. Küldje el a kérést. A postásnak 201-es állapotkódot kell visszaadnia, amely megerősíti a sikeres feldolgozást. 

   A lépés utasításainak végrehajtása több percig is eltarthat. Annak ellenére, hogy az adatkészlet kis méretű, az analitikai képességek számítási igénye nagy. 

> [!NOTE]
> Az indexelő létrehozása elindítja a folyamatot. Ha probléma lép fel az adatok elérésével, a bemenetek és kimenetek leképezésével vagy a műveletek sorrendjével kapcsolatban, az ebben a szakaszban jelenik meg. Ha kód- vagy szkriptmódosításokkal szeretné újra futtatni a folyamatot, lehetséges, hogy először el kell távolítania az objektumokat. További információk: [Alaphelyzetbe állítás és ismételt futtatás](#reset).

#### <a name="about-indexer-parameters"></a>Indexelő paraméterek –

A szkript -1 értéket állít be a ```"maxFailedItems"``` paraméterhez, amely utasítja az indexelőmotort, hogy hagyja figyelmen kívül az adatimportálás közben felmerülő hibákat. Ez azért elfogadható, mert a bemutató adatforrásában olyan kevés dokumentum található. Nagyobb méretű adatforrás esetén 0-nál nagyobb értéket kell megadnia.

Az ```"dataToExtract":"contentAndMetadata"``` utasítás arra utasítja az indexelőt, hogy automatikusan bontsa ki a tartalmat különböző fájlformátumokból, valamint az egyes fájlokhoz kapcsolódó metaadatokból. 

Tartalom kinyerésekor az ```imageAction``` beállításával kinyerheti a szöveget az adatforrásban talált képekből. A ```"imageAction":"generateNormalizedImages"``` konfiguráció az OCR szakértelemmel és a szövegegyesítési szakértelemmel kombinálva arra utasítja az indexelőt, hogy bontsa ki a szöveget a képekből (például a "stop" szó egy közlekedési stop táblából), és ágyazza be a tartalommező részeként. Ez a működés mind a dokumentumokban beágyazott képekre (például egy PDF-fájlban található képre), mind az adatforrásban talált képekre (például egy JPG-fájlra) vonatkozik.

## <a name="4---monitor-indexing"></a>4 - Monitor indexelés

Az indexelés és a dúsítás akkor kezdődik, amikor elküldi az Indexelő létrehozása kérelmet. Attól függően, hogy melyik kognitív képességek definiált, indexelés eltarthat egy ideig. Ha kíváncsi rá, hogy az indexelő fut-e még, küldje el a következő kérést az indexelő állapotának ellenőrzéséhez.

1. Használja a **GET-t** és a következő URL-címet, amely a YOUR-SERVICE-NAME-t a szolgáltatás tényleges nevére cseréli, hogy megnevezze az indexelőt.

   ```http
   https://[YOUR-SERVICE-NAME].search.windows.net/indexers/cog-search-demo-idxr/status?api-version=2019-05-06
   ```

1. Tekintse át a választ, hogy megtudja, hogy az indexelő fut, vagy tekintse meg a hiba- és figyelmeztetési információkat.  

Ha az ingyenes szintet használja, a következő üzenet jelenik meg: "Nem lehetett kinyerni a tartalmat vagy a metaadatokat a dokumentumból. Csonkolt kibontott szöveg "32768" karakterekre". Ez az üzenet azért jelenik meg, mert az ingyenes szinten a blob indexelése[32 Ezres korláttal rendelkezik a karakterek kinyerésére.](search-limits-quotas-capacity.md#indexer-limits) Ez az üzenet nem jelenik meg ennél az adatkészletnél a magasabb szinteken. 

> [!NOTE]
> A figyelmeztetések bizonyos esetekben gyakoriak, és nem mindig jeleznek problémát. Ha például egy blobtároló képfájlokat tartalmaz, és a folyamat nem kezeli a képeket, figyelmeztetést kap arról, hogy a rendszerképek feldolgozása nem történt meg.

## <a name="5---search"></a>5 - Keresés

Most, hogy új mezőket és információkat hozott létre, futtasson le néhány lekérdezést a kognitív keresés értékének megértéséhez, mivel egy tipikus keresési forgatókönyvhöz kapcsolódik.

Emlékezzünk vissza, hogy a blobtartalommal kezdtük, `content` ahol a teljes dokumentum egyetlen mezőbe van csomagolva. Kereshet ebben a mezőben, és megkeresheti a lekérdezések egyezéseit.

1. Használja a **GET** és a következő URL-címet, amely a YOUR-SERVICE-NAME-t a szolgáltatás tényleges `content` nevére cseréli, hogy egy kifejezés vagy kifejezés példányait keresse meg, visszaadva a mezőt és az egyező dokumentumok számát.

   ```http
   https://[YOUR-SERVICE-NAME].search.windows.net/indexes/cog-search-demo-idx?search=*&$count=true&$select=content?api-version=2019-05-06
   ```
   
   A lekérdezés eredménye adja vissza a dokumentum tartalmát, amely ugyanazt az eredményt kapna, ha a blob indexelő nélkül a kognitív keresési folyamat. Ez a mező kereshető, de nem kivitelezhető, ha a laptokat, szűrőket vagy automatikus kiegészítést szeretne használni.

   ![Tartalommező kimenete](media/cognitive-search-tutorial-blob/content-output.png "Tartalommező kimenete")
   
1. A második lekérdezés, vissza néhány új mezők által létrehozott folyamat (személyek, szervezetek, helyek, languageCode). Mi kihagyva keyPhrases a rövidség, de meg kell tartalmaznia, ha szeretné látni ezeket az értékeket.

   ```http
   https://mydemo.search.windows.net/indexes/cog-search-demo-idx/docs?search=*&$count=true&$select=metadata_storage_name,persons,organizations,locations,languageCode&api-version=2019-05-06
   ```
   A $select utasítás mezői a Cognitive Services természetes nyelvi feldolgozási képességeiből létrehozott új információkat tartalmaznak. Ahogy az várható, van némi zaj az eredmények és a dokumentumok közötti eltérések, de sok esetben az analitikai modellek pontos eredményeket.

   Az alábbi képen Satya Nadella nyílt levelének eredményei láthatók, amikor átveszi a Vezérigazgató szerepét a Microsoftnál.

   ![Csővezeték kimenete](media/cognitive-search-tutorial-blob/pipeline-output.png "Csővezeték kimenete")

1. Ha meg szeretné tekinteni, hogyan használhatja ki ezeket a mezőket, adjon hozzá egy aspektusa paramétert az egyező dokumentumok hely szerinti összesítésének adására.

   ```http
   https://[YOUR-SERVICE-NAME].search.windows.net/indexes/cog-search-demo-idx/docs?search=*&facet=locations&api-version=2019-05-06
   ``` 

   Ebben a példában minden helyen 2 vagy 3 találat van.

   ![Facet kimenet](media/cognitive-search-tutorial-blob/facet-output.png "Facet kimenet")
   

1. Ebben az utolsó példában alkalmazzon szűrőt a szervezetek gyűjteményére, és a NASDAQ-on alapuló szűrési feltételekhez két egyezést ad vissza.

   ```http
   cog-search-demo-idx/docs?search=*&$filter=organizations/any(organizations: organizations eq 'NASDAQ')&$select=metadata_storage_name,organizations&$count=true&api-version=2019-05-06
   ```

Ezek a lekérdezések szemléltetik a lekérdezésszintaxisés a kognitív keresés által létrehozott új mezők szűrőinek néhány módját. További lekérdezési példákat a [Keresési dokumentumok REST API-jában,](https://docs.microsoft.com/rest/api/searchservice/search-documents#bkmk_examples) [az egyszerű szintaktikai lekérdezési példákban](search-query-simple-examples.md)és [a Teljes lucene-lekérdezési példákban található példák ban talál.](search-query-lucene-examples.md)

<a name="reset"></a>

## <a name="reset-and-rerun"></a>Alaphelyzetbe állítás és ismételt futtatás

A fejlesztés korai kísérleti szakaszában a legpraktikusabb módszer az iteráció tervezésére, hogy törölje az objektumokat az Azure Cognitive Search szolgáltatásból, és lehetővé tegye a kód újraépítését. Az erőforrásnevek egyediek. Egy objektum törlése révén újból létrehozhatja azt ugyanazzal a névvel.

A portál segítségével törölheti az indexeket, indexelőket, adatforrásokat és skillseteket. Az indexelő törlésekor tetszés szerint, szelektíven törölheti az indexet, a skillsetet és az adatforrást egyszerre.

![Keresési objektumok törlése](./media/cognitive-search-tutorial-blob-python/py-delete-indexer-delete-all.png "Keresési objektumok törlése a portálon")

Vagy használja **a DELETE parancsot,** és adja meg az URL-címeket az egyes objektumokhoz. A következő parancs töröl egy indexelőt.

```http
DELETE https://[YOUR-SERVICE-NAME].search.windows.net/indexers/cog-search-demo-idxr?api-version=2019-05-06
```

Sikeres törlés esetén a rendszer a 204-es állapotkódot adja vissza.

## <a name="takeaways"></a>Legfontosabb ismeretek

Ez az oktatóanyag a bővített indexelőfolyamat a komponensek (adatforrás, képességcsoport, index és indexelő) létrehozásával való összeállításának alapvető lépéseit ismerteti.

[Beépített készségek](cognitive-search-predefined-skills.md) kerültek bevezetésre, valamint skillset meghatározása és a mechanika láncolat készségek együtt bemenetek és outputok. Azt is `outputFieldMappings` megtanulta, hogy az indexelő definíciója szükséges a bővített értékek útválasztása a folyamatból egy kereshető index egy Azure Cognitive Search szolgáltatás.

Végül megismerte, hogyan tesztelheti az eredményeket, és hogyan állíthatja alaphelyzetbe a rendszert a későbbi futtatásokhoz. Megtanulta, hogy ha lekérdezéseket futtat az indexen, az a bővített indexelési folyamat által létrehozott kimenetet adja vissza. 

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha saját előfizetésében dolgozik, a projekt végén célszerű eltávolítani azokat az erőforrásokat, amelyekre már nincs szüksége. A továbbra is futó erőforrások költségekkel járhatnak. Az erőforrások egyesével is törölhetők, de az erőforráscsoport törlésével egyszerre eltávolítható az összes erőforrás is.

Az erőforrásokat a portálon keresheti meg és kezelheti a bal oldali navigációs ablak Minden erőforrás vagy Erőforráscsoport hivatkozásával.

## <a name="next-steps"></a>További lépések

Most, hogy már ismeri az AI-bővítési folyamat összes objektumát, vessünk egy közelebbi pillantást a skillset definíciókra és az egyéni készségekre.

> [!div class="nextstepaction"]
> [Hogyan hozzunk létre egy skillset](cognitive-search-defining-skillset.md)