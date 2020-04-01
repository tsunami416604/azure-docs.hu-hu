---
title: Tudástár (előzetes verzió) létrehozása rest használatával
titleSuffix: Azure Cognitive Search
description: A REST API-val és postásnal hozzon létre egy Azure Cognitive Search tudástárolót az AI-bővítési folyamatból való további bővítéshez. Ez a funkció jelenleg nyilvános előzetes verzióban érhető el.
author: HeidiSteen
manager: nitinme
ms.author: heidist
ms.service: cognitive-search
ms.topic: tutorial
ms.date: 12/30/2019
ms.openlocfilehash: 478a7e03b432006b429c96e03307fd8e494c88ff
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "77472315"
---
# <a name="create-a-knowledge-store-using-rest-and-postman"></a>Tudástár létrehozása rest és postás használatával

> [!IMPORTANT] 
> A tudástároló jelenleg nyilvános előzetes verzióban érhető el. Az előzetes verzió funkció szolgáltatásszint-szerződés nélkül érhető el, és éles számítási feladatokhoz nem ajánlott. További információt a Microsoft Azure előzetes verziók kiegészítő használati feltételei című [témakörben talál.](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) A [REST API 2019-05-06-preview verziója](search-api-preview.md) előnézeti funkciókat biztosít. Jelenleg korlátozott a portál támogatása, és nincs .NET SDK-támogatás.

A tudástároló egy Azure Cognitive Search dúsítási folyamat kimenetét tartalmazza későbbi elemzésvagy más alsóbb rétegbeli feldolgozás hoz. Az AI-dúsított folyamat elfogadja a képfájlokat vagy a strukturálatlan szövegfájlokat, indexeli őket az Azure Cognitive Search használatával, alkalmazza a Cognitive Services ai-dúsításokat (például a képelemzés és a természetes nyelvi feldolgozás), majd menti az eredményeket egy tudástárolóba az Azure Storage-ban. Az Azure Portalon a Power BI vagy a Storage Explorer eszközeivel megismerheti a tudástárolót.

Ebben a cikkben a REST API-felület használatával betöltése, indexelése és a ai-dúsítások egy sor szállodai értékelések. A szállodai vélemények importálása az Azure Blob storage-ba történik. Az eredmények et tudástárolóként menti az Azure Table storage.

A tudástároló létrehozása után megtudhatja, hogyan érheti el a tudástárolót a [Storage Explorer](knowledge-store-view-storage-explorer.md) vagy a [Power BI](knowledge-store-connect-power-bi.md)segítségével.

Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) mielőtt elkezdené.

> [!TIP]
> A [cikkhez](https://www.getpostman.com/) a Postman asztali alkalmazását javasoljuk. A cikk [forráskódja](https://github.com/Azure-Samples/azure-search-postman-samples/tree/master/knowledge-store) tartalmazza az összes kérést tartalmazó Postman-gyűjteményt. 

## <a name="create-services-and-load-data"></a>Szolgáltatások létrehozása és adatok betöltése

Ez a rövid útmutató az Azure Cognitive Search, az Azure Blob storage és az [Azure Cognitive Services](https://azure.microsoft.com/services/cognitive-services/) a ai. 

Mivel a munkaterhelés olyan kicsi, a Cognitive Services a színfalak mögött, hogy ingyenes feldolgozást biztosít legfeljebb 20 tranzakciók naponta. Mivel az adatkészlet olyan kicsi, kihagyhatja a Cognitive Services-erőforrás létrehozását vagy csatolását.

1. [Letöltés HotelReviews_Free.csv](https://knowledgestoredemo.blob.core.windows.net/hotel-reviews/HotelReviews_Free.csv?sp=r&st=2019-11-04T01:23:53Z&se=2025-11-04T16:00:00Z&spr=https&sv=2019-02-02&sr=b&sig=siQgWOnI%2FDamhwOgxmj11qwBqqtKMaztQKFNqWx00AY%3D). Ezek az adatok a CSV fájlban mentett hotel-felülvizsgálati adatok (Kaggle.com származnak), és 19 darab vásárlói visszajelzést tartalmaznak egy szállodáról. 

1. [Hozzon létre egy Azure-tárfiókot,](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account?tabs=azure-portal) vagy [keressen egy meglévő fiókot](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Storage%2storageAccounts/) az aktuális előfizetésében. Az Azure storage-t mind az importálandó nyers tartalomhoz, mind a tudástárolóhoz használni fogja, amely a végeredmény.

   Válassza ki a **StorageV2 (általános célú V2)** fiók típusát.

1. Nyissa meg a Blob-szolgáltatások lapjait, és hozzon létre egy *hotel-vélemények nevű tárolót.*

1. Kattintson a **Feltöltés** gombra.

    ![Az adatok feltöltése](media/knowledge-store-create-portal/upload-command-bar.png "Töltse fel a szállodai értékeléseket")

1. Válassza ki az első lépésben letöltött **HotelReviews-Free.csv** fájlt.

    ![Az Azure Blob-tároló létrehozása](media/knowledge-store-create-portal/hotel-reviews-blob-container.png "Az Azure Blob-tároló létrehozása")

1. Ezzel az erőforrással már majdnem végzett, de mielőtt kilépne ezekből a lapokból, a bal oldali navigációs ablakban található hivatkozással nyissa meg a **Hozzáférési kulcsok** lapot. Szerezzen be egy kapcsolati karakterláncot az adatok Blob storage-ból történő lekéréséhez. A kapcsolati karakterlánc a következő példához hasonlóan néz ki:`DefaultEndpointsProtocol=https;AccountName=<YOUR-ACCOUNT-NAME>;AccountKey=<YOUR-ACCOUNT-KEY>;EndpointSuffix=core.windows.net`

1. Még mindig a portálon, váltson az Azure Cognitive Search. [Hozzon létre egy új szolgáltatást,](search-create-service-portal.md) vagy [keressen egy meglévő szolgáltatást.](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) Ehhez a gyakorlathoz ingyenes szolgáltatást használhat.

## <a name="configure-postman"></a>Postman konfigurálása

Telepítse és állítsa be a Postmant.

### <a name="download-and-install-postman"></a>A Postman letöltése és telepítése

1. Töltse le a [Postman gyűjtemény forráskódját.](https://github.com/Azure-Samples/azure-search-postman-samples/blob/master/knowledge-store/KnowledgeStore.postman_collection.json)
1. Válassza a > **Fájlimportálás** lehetőséget a forráskód Postman beimportálásához. **File**
1. Válassza a **Gyűjtemények** lapot, majd a **...** (három pont) gombot.
1. Válassza a **Szerkesztés** elemet. 
   
   ![Navigációt megjelenítő Postman alkalmazás](media/knowledge-store-create-rest/postman-edit-menu.png "Ugrás a Postman Szerkesztés menüjére")
1. A **Szerkesztés** párbeszédpanelen válassza a **Változók** lapot. 

A **Változók** lapon hozzáadhat olyan értékeket, amelyeket a Postman minden alkalommal felcserél, amikor egy adott változót kettős kapcsos zárójelben találkozik. A Postman például lecseréli a szimbólumot `{{admin-key}}` `admin-key`a beállításhoz beállított aktuális értékre. A postás URL-ekben, fejlécekben, a kérelemtörzsben és így tovább. 

A ( A) `admin-key`értékének bekedvéért nyissa meg az `search-service-name` Azure Cognitive Search szolgáltatást, és válassza a **Kulcsok** lapot. [Create services](#create-services-and-load-data) `storage-account-name` A `storage-connection-string` tárfiók **Hozzáférési kulcsok** lapján található érték kel állítható be. A többi érték alapértelmezéseit is meghagyhatja.

![Postman alkalmazás változók lap](media/knowledge-store-create-rest/postman-variables-window.png "Postás változóinak ablaka")


| Változó    | Honnan szerezhető be? |
|-------------|-----------------|
| `admin-key` | Az Azure Cognitive Search szolgáltatás **Keys** oldalán.  |
| `api-version` | Kilépés **2019-05-06-Preview-ként.** |
| `datasource-name` | Hagyja, mint **a szálloda-felülvizsgálat-ds**. | 
| `indexer-name` | Hagyja, mint **hotel-értékelés-ixr**. | 
| `index-name` | Hagyja, mint **hotel-reviews-ix**. | 
| `search-service-name` | Az Azure Cognitive Search szolgáltatás neve. Az URL- cím `https://{{search-service-name}}.search.windows.net`. | 
| `skillset-name` | Hagyja, mint **a szálloda-értékelés-ss**. | 
| `storage-account-name` | A tárfiók neve. | 
| `storage-connection-string` | A tárfiók Hozzáférési **kulcsok** lapján válassza a **key1** > **Kapcsolati karakterlánc lehetőséget.** | 
| `storage-container-name` | Hagyja el **a szálloda-értékelés**. | 

### <a name="review-the-request-collection-in-postman"></a>A kérelemgyűjtemény áttekintése a Postman ben

Tudástároló létrehozásakor négy HTTP-kérelmet kell kiadnia: 

- **PUT-kérelem az index létrehozásához:** Ez az index tartalmazza az Azure Cognitive Search által használt és visszaadott adatokat.
- **POST-kérelem az adatforrás létrehozásához:** Ez az adatforrás az Azure Cognitive Search viselkedését kapcsolja össze az adat- és tudástároló tárfiókjával. 
- **PUT-kérelem a skillset létrehozásához:** A skillset határozza meg az adatokra alkalmazott dúsításokat és a tudástároló szerkezetét.
- **PUT-kérelem az indexelő létrehozásához:** Az indexelő futtatása beolvassa az adatokat, alkalmazza a skillset, és tárolja az eredményeket. Ezt a kérést utoljára kell futtatnia.

A [forráskód](https://github.com/Azure-Samples/azure-search-postman-samples/blob/master/knowledge-store/KnowledgeStore.postman_collection.json) egy postás gyűjteményt tartalmaz, amely a négy kérelmet tartalmazza. A kérelmek kiadásához a Postman alkalmazásban válassza ki a kérelem lapját. Ezután `api-key` adjon `Content-Type` hozzá és kérjen fejléceket. Állítsa a `api-key` `{{admin-key}}`értékét. Állítsa az `Content-type` `application/json`értéket . 

![Képernyőkép a Postman fejlécek felületéről](media/knowledge-store-create-rest/postman-headers-ui.png)

> [!Note]
> Minden kérésben be kell állítania `api-key` és `Content-type` fejléceket kell beállítania. Ha a Postman felismer egy változót, a `{{admin-key}}` változó narancssárga szövegben jelenik meg, mint az előző képernyőképen. Ha a változó hibásan van beírva, piros szöveggel jelenik meg.
>

## <a name="create-an-azure-cognitive-search-index"></a>Egy Azure Cognitive Search-index létrehozása

Hozzon létre egy Azure Cognitive Search indexet, amely azokat az adatokat jelöli, amelyek érdeklik a keresés, a szűrés és a fejlesztések alkalmazása. Hozza létre az indexet put `https://{{search-service-name}}.search.windows.net/indexes/{{index-name}}?api-version={{api-version}}`kérés kibocsátásával a számára. A Postman a dupla `{{search-service-name}}`kapcsos zárójelben (például ) `{{index-name}}`és `{{api-version}}`) lévő szimbólumokat a [Postás konfigurálása](#configure-postman)című könyvben beállított értékekre cseréli. Ha egy másik eszközt használ a REST-parancsok kiadásához, ezeket a változókat saját magának kell helyettesítenie.

Állítsa be az Azure Cognitive Search index szerkezetét a kérelem törzsében. A Postman alkalmazásban a `api-key` `Content-type` fejlécek és a fejlécek beállítása után lépjen a kérelem **Törzs** ablaktáblájára. A következő JSON-t kell látnia. Ha nem, válassza **a Nyers** > **JSON (alkalmazás/json)** lehetőséget, majd a következő kódot illessze be törzsként:

```JSON
{
    "name": "{{index-name}}",
    "fields": [
        { "name": "address", "type": "Edm.String", "searchable": false, "filterable": false, "sortable": false, "facetable": false },
        { "name": "categories", "type": "Edm.String", "searchable": false, "filterable": false, "sortable": false, "facetable": false },
        { "name": "city", "type": "Edm.String", "filterable": false, "sortable": false, "facetable": false },
        { "name": "country", "type": "Edm.String", "searchable": false, "filterable": false, "sortable": false, "facetable": false },
        { "name": "latitude", "type": "Edm.String", "searchable": false, "filterable": false, "sortable": false, "facetable": false },
        { "name": "longitude", "type": "Edm.String", "searchable": false, "filterable": false, "sortable": false, "facetable": false },
        { "name": "name", "type": "Edm.String", "filterable": false, "sortable": false, "facetable": false },
        { "name": "postalCode", "type": "Edm.String", "searchable": false, "filterable": false, "sortable": false, "facetable": false },
        { "name": "province", "type": "Edm.String", "searchable": false, "filterable": false, "sortable": false, "facetable": false },
        { "name": "reviews_date", "type": "Edm.DateTimeOffset", "searchable": false, "filterable": false, "sortable": false, "facetable": false },
        { "name": "reviews_dateAdded", "type": "Edm.DateTimeOffset", "searchable": false, "filterable": false, "sortable": false, "facetable": false },
        { "name": "reviews_rating", "type": "Edm.String", "searchable": false, "filterable": false, "sortable": false, "facetable": false },
        { "name": "reviews_text", "type": "Edm.String", "filterable": false,  "sortable": false, "facetable": false },
        { "name": "reviews_title", "type": "Edm.String", "searchable": false, "filterable": false, "sortable": false, "facetable": false },
        { "name": "reviews_username", "type": "Edm.String", "searchable": false, "filterable": false, "sortable": false, "facetable": false },
        { "name": "AzureSearch_DocumentKey", "type": "Edm.String", "searchable": false, "filterable": false, "sortable": false, "facetable": false, "key": true },
        { "name": "metadata_storage_content_type", "type": "Edm.String", "searchable": false, "filterable": false, "sortable": false, "facetable": false },
        { "name": "metadata_storage_size", "type": "Edm.Int64", "searchable": false, "filterable": false, "sortable": false, "facetable": false},
        { "name": "metadata_storage_last_modified", "type": "Edm.DateTimeOffset", "searchable": false, "filterable": false, "sortable": false, "facetable": false },
        { "name": "metadata_storage_name", "type": "Edm.String", "searchable": false, "filterable": false, "sortable": false, "facetable": false },
        { "name": "metadata_storage_path", "type": "Edm.String", "searchable": false, "filterable": false, "sortable": false, "facetable": false },
        { "name": "Sentiment", "type": "Collection(Edm.Double)", "searchable": false, "filterable": true, "retrievable": true, "sortable": false, "facetable": true },
        { "name": "Language", "type": "Edm.String", "filterable": true, "sortable": false, "facetable": true },
        { "name": "Keyphrases", "type": "Collection(Edm.String)", "filterable": true, "sortable": false, "facetable": true }
    ]
}

```

Ez az indexdefiníció olyan adatok kombinációja, amelyeket meg szeretne mutatni a felhasználónak (a szálloda neve, a tartalom áttekintése, a dátum), a keresési metaadatok és a ai-növelési adatok (hangulat, kulcsmondatok és nyelv).

A PUT-kérelem kiadásához válassza a **Küldés** lehetőséget. Meg kell jelennie az állapotnak. `201 - Created` Ha más állapotot lát, a **Törzs** ablaktáblán keressen egy hibaüzenetet tartalmazó JSON-választ. 

## <a name="create-the-datasource"></a>Az adatforrás létrehozása

Ezután csatlakoztassa az Azure Cognitive Search-et a Blob storage-ban tárolt szállodai adatokhoz. Az adatforrás létrehozásához küldjön postai kérelmet a rendszernek. `https://{{search-service-name}}.search.windows.net/datasources?api-version={{api-version}}` Be kell `api-key` állítania a és `Content-Type` a fejléceket a korábban tárgyaltak szerint. 

A Postman alkalmazásban nyissa meg az **Adatforrás létrehozása** kérelmet, majd a **Törzs** ablaktáblát. Az alábbi kódot kell látnia:

```json
{
  "name" : "{{datasource-name}}",
  "description" : "Demo files to demonstrate knowledge store capabilities.",
  "type" : "azureblob",
  "credentials" : { "connectionString" : "{{storage-connection-string}}" },
  "container" : { "name" : "{{storage-container-name}}" }
}
```

A POST-kérelem kiadásához válassza a **Küldés** lehetőséget. 

## <a name="create-the-skillset"></a>A skillset létrehozása 

A következő lépés a skillset megadása, amely meghatározza mind az alkalmazandó fejlesztéseket, mind az eredmények tárolásának helyében lévő tudástárolót. A Postman csoportban válassza **a Skillset létrehozása** lapot. Ez a kérés `https://{{search-service-name}}.search.windows.net/skillsets/{{skillset-name}}?api-version={{api-version}}`put-ot küld a számára. Állítsa `api-key` be `Content-type` a fejléceket, ahogy korábban tette. 

Két nagy legfelső szintű objektum `skills` `knowledgeStore`létezik: és a. Az `skills` objektumon belüli minden objektum egy dúsítási szolgáltatás. Minden dúsító `inputs` `outputs`szolgáltatás rendelkezik és. A `LanguageDetectionSkill` kimenete `targetName` `Language`. Ennek a csomópontnak az értékét a legtöbb más szakértelem használja bemenetként. A forrás `document/Language`. Az egyik csomópont kimenetének egy másik csomópontba való bevitele `ShaperSkill`még nyilvánvalóbb a rendszerben, amely meghatározza, hogy az adatok hogyan jutnak be a tudástároló tábláiba.

Az `knowledge_store` objektum a Postman változón keresztül csatlakozik a `{{storage-connection-string}}` tárfiókhoz. `knowledge_store`A továbbfejlesztett dokumentum, valamint a tudástárolóban lévő táblázatok és oszlopok közötti leképezések készletét tartalmazza. 

A skillset létrehozásához válassza a **Küldés** gombot a Postman alkalmazásban a kérés elküldéséhez:

```json
{
    "name": "{{skillset-name}}",
    "description": "Skillset to detect language, extract key phrases, and detect sentiment",
    "skills": [ 
        {
            "@odata.type": "#Microsoft.Skills.Text.SplitSkill", 
            "context": "/document/reviews_text", "textSplitMode": "pages", "maximumPageLength": 5000,
            "inputs": [ 
                { "name": "text", "source": "/document/reviews_text" },
                { "name": "languageCode", "source": "/document/Language" }
            ],
            "outputs": [
                { "name": "textItems", "targetName": "pages" }
            ]
        },
        {
            "@odata.type": "#Microsoft.Skills.Text.SentimentSkill",
            "context": "/document/reviews_text/pages/*",
            "inputs": [
                { "name": "text", "source": "/document/reviews_text/pages/*" },
                { "name": "languageCode", "source": "/document/Language" }
            ],
            "outputs": [
                { "name": "score", "targetName": "Sentiment" }
            ]
        },
        {
            "@odata.type": "#Microsoft.Skills.Text.LanguageDetectionSkill",
            "context": "/document",
            "inputs": [
                { "name": "text", "source": "/document/reviews_text" }
            ],
            "outputs": [
                { "name": "languageCode", "targetName": "Language" }
            ]
        },
        {
            "@odata.type": "#Microsoft.Skills.Text.KeyPhraseExtractionSkill",
            "context": "/document/reviews_text/pages/*",
            "inputs": [
                { "name": "text",  "source": "/document/reviews_text/pages/*" },
                { "name": "languageCode",  "source": "/document/Language" }
            ],
            "outputs": [
                { "name": "keyPhrases" , "targetName": "Keyphrases" }
            ]
        },
        {
            "@odata.type": "#Microsoft.Skills.Util.ShaperSkill",
            "context": "/document",
            "inputs": [
                { "name": "name",  "source": "/document/name" },
                { "name": "reviews_date",  "source": "/document/reviews_date" },
                { "name": "reviews_rating",  "source": "/document/reviews_rating" },
                { "name": "reviews_text",  "source": "/document/reviews_text" },
                { "name": "reviews_title",  "source": "/document/reviews_title" },
                { "name": "AzureSearch_DocumentKey",  "source": "/document/AzureSearch_DocumentKey" },
                { 
                    "name": "pages",
                    "sourceContext": "/document/reviews_text/pages/*",
                    "inputs": [
                        { "name": "SentimentScore", "source": "/document/reviews_text/pages/*/Sentiment" },
                        { "name": "LanguageCode", "source": "/document/Language" },
                        { "name": "Page", "source": "/document/reviews_text/pages/*" },
                        { 
                            "name": "keyphrase", "sourceContext": "/document/reviews_text/pages/*/Keyphrases/*",
                            "inputs": [
                                { "name": "Keyphrases", "source": "/document/reviews_text/pages/*/Keyphrases/*" }
                            ]
                        }
                    ]
                }
            ],
            "outputs": [
                { "name": "output" , "targetName": "tableprojection" }
            ]
        }
    ],
    "knowledgeStore": {
        "storageConnectionString": "{{storage-connection-string}}",
        "projections": [
            {
                "tables": [
                    { "tableName": "hotelReviewsDocument", "generatedKeyName": "Documentid", "source": "/document/tableprojection" },
                    { "tableName": "hotelReviewsPages", "generatedKeyName": "Pagesid", "source": "/document/tableprojection/pages/*" },
                    { "tableName": "hotelReviewsKeyPhrases", "generatedKeyName": "KeyPhrasesid", "source": "/document/tableprojection/pages/*/keyphrase/*" },
                    { "tableName": "hotelReviewsSentiment", "generatedKeyName": "Sentimentid", "source": "/document/tableprojection/pages/*/sentiment/*" }
                ],
                "objects": []
            },
            {
                "tables": [
                    { 
                        "tableName": "hotelReviewsInlineDocument", "generatedKeyName": "Documentid", "sourceContext": "/document",
                        "inputs": [
                            { "name": "name", "source": "/document/name"},
                            { "name": "reviews_date", "source": "/document/reviews_date"},
                            { "name": "reviews_rating", "source": "/document/reviews_rating"},
                            { "name": "reviews_text", "source": "/document/reviews_text"},
                            { "name": "reviews_title", "source": "/document/reviews_title"},
                            { "name": "AzureSearch_DocumentKey", "source": "/document/AzureSearch_DocumentKey" }
                        ]
                    },
                    { 
                        "tableName": "hotelReviewsInlinePages", "generatedKeyName": "Pagesid", "sourceContext": "/document/reviews_text/pages/*",
                        "inputs": [
                            { "name": "SentimentScore", "source": "/document/reviews_text/pages/*/Sentiment"},
                            { "name": "LanguageCode", "source": "/document/Language"},
                            { "name": "Page", "source": "/document/reviews_text/pages/*" }
                        ]
                    },
                    { 
                        "tableName": "hotelReviewsInlineKeyPhrases", "generatedKeyName": "kpidv2", "sourceContext": "/document/reviews_text/pages/*/Keyphrases/*",
                        "inputs": [
                            { "name": "Keyphrases", "source": "/document/reviews_text/pages/*/Keyphrases/*" }
                        ]
                    }
                ],
                "objects": []
            }
        ]
    }
}
```

## <a name="create-the-indexer"></a>Az indexelő létrehozása

Az utolsó lépés az indexelő létrehozása. Az indexelő beolvassa az adatokat, és aktiválja a skillset. A Postman, válassza ki az **Indexelő létrehozása** kérelmet, majd tekintse át a törzset. Az indexelő definíciója számos más, már létrehozott erőforrásra utal: az adatforrásra, az indexre és a skillsetre. 

Az `parameters/configuration` objektum azt szabályozza, hogy az indexelő hogyan hogyan hogyan fejti be az adatokat. Ebben az esetben a bemeneti adatok egyetlen dokumentumban vannak, amely fejlécsés vesszővel tagolt értékekkel rendelkezik. A dokumentumkulcs a dokumentum egyedi azonosítója. A kódolás előtt a dokumentumkulcs a forrásdokumentum URL-címe. Végül a skillset kimeneti értékek, például a nyelvi kód, a hangulat és a kulcskifejezések, a dokumentumban lévő helyükre vannak leképezve. Bár a program a `Language`rendszernek egyetlen értéke van a rendszerhez, `Sentiment` a tömb minden elemére alkalmazza a program. `pages` `Keyphrases`egy olyan tömb, amely a `pages` tömb minden elemére is vonatkozik.

Miután beállította `Content-type` a és a `api-key` fejléceket, és meggyőződött arról, hogy a kérelem törzse hasonló a következő forráskódhoz, válassza a **Küldés** postásnak lehetőséget. A postás put-kérelmet küld a számára. `https://{{search-service-name}}.search.windows.net/indexers/{{indexer-name}}?api-version={{api-version}}` Az Azure Cognitive Search létrehozza és futtatja az indexelőt. 

```json
{
    "name": "{{indexer-name}}",
    "dataSourceName": "{{datasource-name}}",
    "skillsetName": "{{skillset-name}}",
    "targetIndexName": "{{index-name}}",
    "parameters": {
        "configuration": {
            "dataToExtract": "contentAndMetadata",
            "parsingMode": "delimitedText",
            "firstLineContainsHeaders": true,
            "delimitedTextDelimiter": ","
        }
    },
    "fieldMappings": [
        {
            "sourceFieldName": "AzureSearch_DocumentKey",
            "targetFieldName": "AzureSearch_DocumentKey",
            "mappingFunction": { "name": "base64Encode" }
        }
    ],
    "outputFieldMappings": [
        { "sourceFieldName": "/document/reviews_text/pages/*/Keyphrases/*", "targetFieldName": "Keyphrases" },
        { "sourceFieldName": "/document/Language", "targetFieldName": "Language" },
        { "sourceFieldName": "/document/reviews_text/pages/*/Sentiment", "targetFieldName": "Sentiment" }
    ]
}
```

## <a name="run-the-indexer"></a>Az indexelő futtatása 

Az Azure Portalon nyissa meg az Azure Cognitive Search szolgáltatás **áttekintése** lapot. Válassza az **Indexelők** lapot, majd a **hotels-reviews-ixr**lehetőséget. Ha az indexelő még nem futott, válassza a **Futtatás lehetőséget.** Az indexelési feladat a nyelvfelismeréssel kapcsolatos figyelmeztetéseket is felvethet. Az adatok tartalmaznak néhány olyan értékelést, amely olyan nyelveken íródott, amelyeket a kognitív képességek még nem támogatnak. 

## <a name="next-steps"></a>További lépések

Most, hogy a Cognitive Services használatával bővítette az adatokat, és az eredményeket egy tudástárba vetítette, a Storage Explorer vagy a Power BI segítségével megismerheti a bővített adatkészletet.

Ha meg szeretné tudni, hogyan fedezheti fel ezt a tudástárolót a Storage Explorer használatával, olvassa el az útmutatót:

> [!div class="nextstepaction"]
> [Megtekintés a Storage Explorerrel](knowledge-store-view-storage-explorer.md)

A tudástároló nak a Power BI-hoz való csatlakoztatásáról az útmutatóban olvashat:

> [!div class="nextstepaction"]
> [Kapcsolódás Power BI-jal](knowledge-store-connect-power-bi.md)

Ha meg szeretné ismételni ezt a gyakorlatot, vagy ki próbálhatja egy másik AI-bővítési forgatókönyvet, törölje a **hotel-reviews-idxr** indexelőt. Az indexelő törlése nullára állítja vissza az ingyenes napi tranzakciószámlálót.
