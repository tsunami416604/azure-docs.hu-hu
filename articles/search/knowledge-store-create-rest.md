---
title: Hozzon létre egy Knowledge Store-t a REST-Azure Search használatával
description: A REST API és a Poster használatával hozzon létre egy Azure Search tudásbázist, amely a kognitív keresési folyamaton belül megőrzi a dúsítást.
author: lobrien
services: search
ms.service: search
ms.topic: tutorial
ms.date: 10/01/2019
ms.author: laobri
ms.openlocfilehash: b67f0cf60d279c7bc52b4114d29c37847f5c57f1
ms.sourcegitcommit: 824e3d971490b0272e06f2b8b3fe98bbf7bfcb7f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/10/2019
ms.locfileid: "72244468"
---
# <a name="create-an-azure-search-knowledge-store-by-using-rest"></a>Azure Search Knowledge Store létrehozása REST használatával

A Azure Search Knowledge Store szolgáltatása megőrzi a mesterséges intelligencia-bővítési folyamat kimenetét a későbbi elemzésekhez vagy más alsóbb rétegbeli feldolgozásokhoz. Egy mesterséges intelligenciával rendelkező folyamat képfájlokat vagy strukturálatlan szövegfájlokat fogad el, Azure Search használatával indexeli azokat, az Azure-Cognitive Services mesterséges intelligenciát alkalmaz (például a képelemzést és a természetes nyelvi feldolgozást), majd menti az eredményeket egy tudásbázisba. tárolás az Azure Storage-ban. A Azure Portalban Power BI vagy Storage Explorer eszközökkel is elvégezheti a Tudásbázis megismerését.

Ebben a cikkben a REST API felület használatával végezheti el, indexelheti és alkalmazhatja a mesterséges intelligenciát a szállodai felülvizsgálatok készletén. A szállodai értékelések importálása az Azure Blob Storage-ba történik. Az eredményeket a rendszer az Azure Table Storage szolgáltatásban található Tudásbázisban menti.

Miután létrehozta a tudásbázist, megtudhatja, hogyan érheti el a [Storage Explorer](knowledge-store-view-storage-explorer.md) vagy [Power bi](knowledge-store-connect-power-bi.md)használatával a Knowledge Store-t.

## <a name="create-services"></a>Szolgáltatások létrehozása

Hozza létre a következő szolgáltatásokat:

- Hozzon létre egy [Azure Search szolgáltatást](search-create-service-portal.md) , vagy [keressen meglévő szolgáltatást](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) a jelenlegi előfizetésében. Ehhez az oktatóanyaghoz használhatja az ingyenes szolgáltatást.

- Hozzon létre egy [Azure Storage-fiókot](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account) a mintaadatok és a Tudásbázis tárolására. A Storage-fióknak ugyanazt a helyet kell használnia (például az USA nyugati régióját) a Azure Search szolgáltatáshoz. A **fióktípus** értékének a következőnek kell lennie: **StorageV2 (általános célú v2)** (alapértelmezett) vagy **Storage (általános célú v1)** .

- Ajánlott: szerezze be a [Poster Desktop alkalmazást](https://www.getpostman.com/) , amely a kérelmeket Azure Searchba küldi. A REST API bármely olyan eszközzel használható, amely képes a HTTP-kérések és válaszok kezelésére. A Poster jó választás a REST API-k feltárására. Ebben a cikkben a Poster-t használjuk. A cikk [forráskódja](https://github.com/Azure-Samples/azure-search-postman-samples/blob/master/knowledge-store/KnowledgeStore.postman_collection.json) a kérelmek Poster-gyűjteményét is tartalmazza. 

## <a name="store-the-data"></a>Az adattárolás

Töltse be a Hotel a CSV-fájlt az Azure Blob Storage-ba, hogy az egy Azure Search indexelő számára legyen elérhető, és a mesterséges intelligencia-bővítési folyamaton keresztül is elérhető legyen.

### <a name="create-a-blob-container-by-using-the-data"></a>BLOB-tároló létrehozása az adatai alapján

1. Töltse le a CSV-fájlban (HotelReviews_Free. csv) mentett, a [szállodai felülvizsgálati](https://knowledgestoredemo.blob.core.windows.net/hotel-reviews/HotelReviews_Free.csv?st=2019-07-29T17%3A51%3A30Z&se=2021-07-30T17%3A51%3A00Z&sp=rl&sv=2018-03-28&sr=c&sig=LnWLXqFkPNeuuMgnohiz3jfW4ijePeT5m2SiQDdwDaQ%3D) adatfájlt. Ezek az adatok a Kaggle.com származnak, és a felhasználói visszajelzéseket tartalmazzák a szállodákról.
1. Jelentkezzen be a [Azure Portalba](https://portal.azure.com) , és nyissa meg az Azure Storage-fiókját.
1. Hozzon létre egy [BLOB-tárolót](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal). A tároló létrehozásához a Storage-fiók bal oldali menüjében válassza a **Blobok**lehetőséget, majd válassza a **tároló**elemet.
1. Az új tároló **neve**mezőbe írja be a következőt: **Hotel-Reviews**.
1. A **nyilvános hozzáférési szint**beállításnál válasszon ki egy tetszőleges értéket. Az alapértelmezett értéket használtuk.
1. A blob-tároló létrehozásához kattintson **az OK gombra** .
1. Nyissa meg az új **hotelek – áttekintés** tárolót, válassza a **feltöltés**lehetőséget, majd válassza ki az első lépésben letöltött HotelReviews-Free. csv fájlt.

    ![Töltse fel]az(media/knowledge-store-create-portal/upload-command-bar.png "adatfeltöltés a szállodai felülvizsgálatokat")

1. Válassza a **feltöltés** lehetőséget a CSV-fájl Azure Blob Storage-ba történő importálásához. Az új tároló megjelenik:

    ![A blob-tároló létrehozása](media/knowledge-store-create-portal/hotel-reviews-blob-container.png "a blob-tároló") létrehozása

## <a name="configure-postman"></a>Postman konfigurálása

A Poster telepítése és beállítása.

### <a name="download-and-install-postman"></a>Poster letöltése és telepítése

1. Töltse le a [Poster-gyűjtemény forráskódját](https://github.com/Azure-Samples/azure-search-postman-samples/blob/master/knowledge-store/KnowledgeStore.postman_collection.json).
1. Válassza a **fájl** > **Importálás** lehetőséget a forráskód Poster-ba történő importálásához.
1. Válassza a **gyűjtemények** fület, majd kattintson a **...** (három pont) gombra.
1. Válassza a **Szerkesztés** elemet. 
   
   ![Poster-alkalmazás, amely azt mutatja],(media/knowledge-store-create-rest/postman-edit-menu.png "hogy a navigációs menü Szerkesztés menüjében")
1. A **Szerkesztés** párbeszédpanelen válassza a **változók** lapot. 

A **változók** lapon olyan értékeket adhat hozzá a Poster-feladatokhoz, amelyek minden alkalommal felvesznek egy adott változót a kettős zárójelek között. Például a Poster helyettesíti a `{{admin-key}}` szimbólumot a `admin-key` értékhez beállított aktuális értékkel. A Poster lehetővé teszi a helyettesítést az URL-címek, a fejlécek, a kérés törzse és így tovább. 

A `admin-key` értékének lekéréséhez lépjen a Azure Search szolgáltatásra, és válassza a **kulcsok** fület. módosítsa a `search-service-name` és a `storage-account-name` értéket a [szolgáltatások létrehozása](#create-services)területen kiválasztott értékekre. A Storage-fiók **hozzáférési kulcsok** lapján található érték használatával állítsa be a `storage-connection-string` értéket. A többi értéknél meghagyhatja az alapértelmezett értékeket.

![Poster alkalmazás-változók lap]–(media/knowledge-store-create-rest/postman-variables-window.png "a Poster változói ablaka")


| Változó    | Honnan szerezheti be |
|-------------|-----------------|
| `admin-key` | A Azure Search szolgáltatás **kulcsok** lapján.  |
| `api-version` | **2019-05-06 – előzetes**verzióként érhető el. |
| `datasource-name` | Távozás a **Hotel-Reviews-DS**. | 
| `indexer-name` | Távozás a **Hotel-Reviews-IXR**. | 
| `index-name` | Hagyja a **Hotel-Reviews-IX**. | 
| `search-service-name` | A Azure Search szolgáltatás fő neve. Az URL-cím `https://{{search-service-name}}.search.windows.net`. | 
| `skillset-name` | Távozás a **Hotel-Reviews-SS**. | 
| `storage-account-name` | A Storage-fiók fő neve. | 
| `storage-connection-string` | A Storage-fiók **hozzáférési kulcsok** lapján válassza a **key1** > **kapcsolati karakterlánc**lehetőséget. | 
| `storage-container-name` | Távozás a **Hotel-Reviews-** ben. | 

### <a name="review-the-request-collection-in-postman"></a>Tekintse át a Poster kérelem-gyűjteményét

A Tudásbázis létrehozásakor négy HTTP-kérelmet kell kiadnia: 

- **Az index létrehozásának kérése**: ez az index a Azure Search által használt és visszaadott adatok tárolására szolgál.
- **Az adatforrás létrehozásához szükséges post kérelem**: ez az adatforrás összekapcsolja az Azure Search viselkedését az adat-és a Tudásbázis Storage-fiókjával. 
- **Kérelem létrehozása a készségkészlet létrehozásához**: a készségkészlet meghatározza az adataira alkalmazott dúsításokat és a Tudásbázis struktúráját.
- **Put-kérelem az indexelő létrehozásához**: az indexelő beolvassa az adatokat, alkalmazza a készségkészlet, és az eredményeket tárolja. Ezt a kérést utoljára kell futtatnia.

A [forráskód](https://github.com/Azure-Samples/azure-search-postman-samples/blob/master/knowledge-store/KnowledgeStore.postman_collection.json) tartalmaz egy Poster-gyűjteményt, amelyben a négy kérelem szerepel. A kérések kiadásához a Poster lapon válassza ki a kérelemhez tartozó fület. Ezután adja hozzá `api-key` és `Content-Type` kérelmek fejlécét. Állítsa a `api-key` értéket `{{admin-key}}` értékre. Állítsa a `Content-type` értéket `application/json` értékre. 

![A Poster felületének fejléceit bemutató képernyőfelvétel](media/knowledge-store-create-rest/postman-headers-ui.png)

> [!Note]
> Az összes kérelemben be kell állítania `api-key` és `Content-type` fejléceket. Ha a Poster egy változót is felismer, a változó narancssárga szövegben jelenik meg, mint az előző képernyőképen `{{admin-key}}`. Ha a változó hibásan van írva, vörös szövegben jelenik meg.
>

## <a name="create-an-azure-search-index"></a>Azure Search-index létrehozása

Hozzon létre egy Azure Search indexet, amely azokat az adattípusokat jelöli, amelyeknek érdeklik a keresés, a szűrés és a fejlesztések alkalmazása. Hozza létre az indexet egy PUT-kérelem `https://{{search-service-name}}.search.windows.net/indexes/{{index-name}}?api-version={{api-version}}` értékre való kiállításával. A Poster lecseréli a kapcsos zárójelek közé foglalt szimbólumokat (például `{{search-service-name}}`, `{{index-name}}` és `{{api-version}}`), a [Poster configureban](#configure-postman)beállított értékekkel. Ha más eszközt használ a REST-parancsok kiadásához, ezeket a változókat saját kezűleg kell helyettesítenie.

Állítsa be Azure Search index struktúráját a kérelem törzsében. A Poster-ben a `api-key` és a `Content-type` fejlécek beállítása után lépjen a kérelem **törzs** ablaktáblájába. A következő JSON-t kell látnia. Ha nem, válassza a **Raw** > **JSON (alkalmazás/JSON)** elemet, majd illessze be a következő kódot törzsként:

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

Az index definíciója olyan adatok kombinációja, amelyeket szeretne bemutatni a felhasználónak (a szálloda neve, a tartalom áttekintése, a dátum), a keresési metaadatok és az AI-javító adatok (hangulat, kifejezés és nyelv).

Válassza a **Küldés** lehetőséget a Put kérelem kiválasztásához. A következő állapotnak kell megjelennie: `201 - Created`. Ha más állapotot lát, a **törzs** ablaktáblán keressen egy olyan JSON-választ, amely egy hibaüzenetet tartalmaz. 

## <a name="create-the-datasource"></a>Az adatforrás létrehozása

Ezután kapcsolódjon a Azure Search az [adattárban](#store-the-data)tárolt adatszolgáltatáshoz. Az adatforrás létrehozásához küldjön egy POST-kérelmet `https://{{search-service-name}}.search.windows.net/datasources?api-version={{api-version}}` értékre. A korábban leírtaknak megfelelően be kell állítania a `api-key` és a `Content-Type` fejléceket. 

A Poster területen lépjen az **adatforrás létrehozása** kérelemre, majd a **törzs** ablaktáblára. A következő kódot kell megjelennie:

```json
{
  "name" : "{{datasource-name}}",
  "description" : "Demo files to demonstrate knowledge store capabilities.",
  "type" : "azureblob",
  "credentials" : { "connectionString" : "{{storage-connection-string}}" },
  "container" : { "name" : "{{storage-container-name}}" }
}
```

Kattintson a **Küldés** gombra a post kérelem kiadásához. 

## <a name="create-the-skillset"></a>A készségkészlet létrehozása 

A következő lépés a készségkészlet megadása, amely meghatározza az alkalmazandó fejlesztéseket és az eredményeket tároló tudásbázist. A Poster területen válassza a **Létrehozás a készségkészlet** lapot. Ez a kérelem küld egy PUT `https://{{search-service-name}}.search.windows.net/skillsets/{{skillset-name}}?api-version={{api-version}}`. Állítsa be a `api-key` és a `Content-type` fejlécet, ahogy korábban tette. 

Két nagyméretű legfelső szintű objektum létezik: `skills` és `knowledgeStore`. A `skills` objektumon belüli összes objektum egy dúsítási szolgáltatás. Minden dúsítási szolgáltatás `inputs` és `outputs`. A `LanguageDetectionSkill` kimeneti `targetName` `Language`. Ennek a csomópontnak az értékét a más képességek többsége használja bemenetként. A forrás `document/Language`. Egy csomópont kimenetének egy másikhoz való bevitelének lehetősége még nyilvánvalóbb a `ShaperSkill` értékben, amely meghatározza, hogy az adatok hogyan áramlanak be a Knowledge Store tábláiba.

A `knowledge_store` objektum a `{{storage-connection-string}}` Poster változón keresztül csatlakozik a Storage-fiókhoz. a `knowledge_store` a tudásbázisban található bővített dokumentum és táblák és oszlopok közötti leképezések készletét tartalmazza. 

A készségkészlet létrehozásához kattintson a Poster **Send (Küldés** ) gombjára a kérelem elvégzéséhez:

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

Az utolsó lépés az indexelő létrehozása. Az indexelő beolvassa az adatokat, és aktiválja a készségkészlet. A Poster területen válassza az **Indexelő kérés létrehozása** lehetőséget, majd tekintse át a törzset. Az indexelő definíciója több, már létrehozott erőforrásra hivatkozik: az adatforrás, az index és a készségkészlet. 

A `parameters/configuration` objektum azt szabályozza, hogy az indexelő Hogyan nyelje le az adatmennyiséget. Ebben az esetben a bemeneti adatok egy olyan dokumentumban vannak, amely tartalmaz egy fejlécet és egy vesszővel tagolt értéket. A dokumentum kulcsa a dokumentum egyedi azonosítója. A kódolás előtt a dokumentum kulcsa a forrásdokumentum URL-címe. Végül a készségkészlet kimeneti értékei, például a Nyelvkód, a hangulat és a legfontosabb kifejezések a dokumentumban lévő helyükre vannak leképezve. Bár a `Language` értéke egyetlen érték, a rendszer a `Sentiment` értéket alkalmazza a `pages` tömb egyes elemeire. a `Keyphrases` egy olyan tömb, amely a `pages` tömb egyes elemeire is vonatkozik.

Miután beállította a `api-key` és a `Content-type` fejléceket, és győződjön meg arról, hogy a kérelem törzse hasonló a következő forráskódhoz, válassza a **Küldés** Poster-ban lehetőséget. A Poster elküld egy PUT kérelmet `https://{{search-service-name}}.search.windows.net/indexers/{{indexer-name}}?api-version={{api-version}}` értékre. Azure Search létrehozza és futtatja az indexelő. 

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

A Azure Portal nyissa meg a Azure Search szolgáltatás **Áttekintés** lapját. Válassza ki az **Indexelő** fület, majd válassza a **Hotels-Reviews-IXR**lehetőséget. Ha az indexelő még nem fut, válassza a **Futtatás**lehetőséget. Előfordulhat, hogy az indexelési feladat bizonyos, a nyelvi felismeréssel kapcsolatos figyelmeztetéseket eredményezhet. Az adatelemzés tartalmaz néhány olyan nyelvet, amely a kognitív képességek által még nem támogatott nyelveken íródott. 

## <a name="next-steps"></a>Következő lépések

Most, hogy a Cognitive Services és az eredmények egy tudásbázisba való kivetítésével bővíti az adatait, a Storage Explorer vagy a Power BI használatával megismerheti a dúsított adatkészletet.

Ha szeretné megtudni, hogyan derítheti fel ezt a tudásbázist Storage Explorer használatával, tekintse meg ezt a bemutatót:

> [!div class="nextstepaction"]
> [Megtekintés Storage Explorer](knowledge-store-view-storage-explorer.md)

A következő útmutatóból megtudhatja, hogyan csatlakoztatható a Power BI a Knowledge Store-hoz:

> [!div class="nextstepaction"]
> [Kapcsolódás PowerBI-jal](knowledge-store-connect-power-bi.md)

Ha szeretné megismételni ezt a gyakorlatot, vagy próbáljon meg egy másik AI-bővítési bemutatót használni, törölje a **Hotel-Reviews-idxr** indexelő. Az indexelő törlése visszaállítja az ingyenes napi tranzakció számlálóját nullára.
