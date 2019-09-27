---
title: Knowledge Store létrehozása REST-Azure Search használatával
description: Hozzon létre egy Azure Search tudásbázist a kognitív keresési folyamattal való megőrzéshez a REST API és a Poster használatával.
author: lobrien
services: search
ms.service: search
ms.subservice: cognitive-search
ms.topic: tutorial
ms.date: 09/13/2019
ms.author: laobri
ms.openlocfilehash: 50648d22a62199d27374a1cacf617858ce9e7d6a
ms.sourcegitcommit: e9936171586b8d04b67457789ae7d530ec8deebe
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/27/2019
ms.locfileid: "71329273"
---
# <a name="create-an-azure-search-knowledge-store-using-rest"></a>Azure Search Knowledge Store létrehozása REST használatával

A Knowledge Store a Azure Search egyik funkciója, amely egy mesterséges intelligencia-bővítési folyamat kimenetét őrzi meg későbbi elemzések vagy más alsóbb rétegbeli feldolgozás céljából. Egy mesterséges intelligenciával rendelkező folyamat képfájlokat vagy strukturálatlan szövegfájlokat fogad, indexeli őket a Azure Search használatával, alkalmazza a mesterséges intelligenciát Cognitive Services (például a képelemzést és a természetes nyelvi feldolgozást), majd menti az eredményeket egy Azure-beli tudásbázisba Storage. Ezután használhatja a Power BI vagy a Storage Explorer eszközöket a Knowledge Store megismeréséhez.

Ebben a cikkben a REST API felületet fogja használni a mesterséges intelligenciák betöltésére, indexelésére és alkalmazására a szállodai felülvizsgálatok készletén. A szállodai felülvizsgálatok az Azure Blob Storageba lesznek importálva, és az eredményeket a rendszer az Azure Table Storage Tudásbázisban tárolja.

Miután létrehozta a tudásbázist, megtudhatja, hogyan érheti el ezt a Knowledge Store-t [Storage Explorer](knowledge-store-view-storage-explorer.md) vagy [Power bi](knowledge-store-connect-power-bi.md)használatával.

## <a name="1---create-services"></a>1 – szolgáltatások létrehozása

+ [Hozzon létre egy Azure Search szolgáltatást](search-create-service-portal.md) , vagy [keressen egy meglévő szolgáltatást](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) a jelenlegi előfizetése alatt. Ehhez az oktatóanyaghoz használhatja az ingyenes szolgáltatást.

+ [Hozzon létre egy Azure Storage-fiókot](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account) a mintaadatok és a Tudásbázis tárolására. A Storage-fióknak ugyanazt a helyet kell használnia (például az USA nyugati régióját) a Azure Search szolgáltatáshoz. A *fiók típusának* StorageV2 kell lennie *(általános célú v2)* (alapértelmezett) vagy *Storage (általános célú v1)* .

+ Ajánlott: [Poster Desktop-alkalmazás](https://www.getpostman.com/) , amely a kérelmek küldését Azure Search. A REST API bármely olyan eszközzel használható, amely képes a HTTP-kérések és válaszok kezelésére. A Poster jó választás a REST API-k feltárására, és ebben a cikkben fogjuk használni. A cikk [forráskódja](https://github.com/Azure-Samples/azure-search-postman-searches/Tutorial/Knowledge_Store/KnowledgeStore.postman_collection.json) továbbá a kérelmek Poster-gyűjteményét is tartalmazza. 

## <a name="2---store-the-data"></a>2 – az adatgyűjtés tárolása

Töltse be a Hotel a CSV-fájlt az Azure Blob Storage-ba, hogy az egy Azure Search indexelő számára legyen elérhető, és a mesterséges intelligencia-bővítési folyamaton keresztül is elérhető legyen.

### <a name="create-an-azure-blob-container-with-the-data"></a>Azure Blob-tároló létrehozása az adattal

1. [Töltse le a CSV-fájlban (HotelReviews_Free. csv) mentett, a szállodai felülvizsgálati](https://knowledgestoredemo.blob.core.windows.net/hotel-reviews/HotelReviews_Free.csv?st=2019-07-29T17%3A51%3A30Z&se=2021-07-30T17%3A51%3A00Z&sp=rl&sv=2018-03-28&sr=c&sig=LnWLXqFkPNeuuMgnohiz3jfW4ijePeT5m2SiQDdwDaQ%3D)adatfájlt. Ezek az adatok a Kaggle.com származnak, és a felhasználói visszajelzéseket tartalmazzák a szállodákról.
1. [Jelentkezzen be a Azure Portalba](https://portal.azure.com), és navigáljon az Azure Storage-fiókjához.
1. [Hozzon létre egy BLOB-tárolót](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal). A tároló létrehozásához a Storage-fiók bal oldali navigációs sávján kattintson a **Blobok**elemre, majd a parancssáv **+ tároló** elemére.
1. Az új tároló **neve**mezőbe írja be `hotel-reviews`a következőt:.
1. Válassza ki a **nyilvános hozzáférési szintet**. Az alapértelmezett értéket használtuk.
1. Az Azure Blob-tároló létrehozásához kattintson **az OK** gombra.
1. Nyissa meg `hotels-review` az új tárolót, kattintson a **feltöltés**gombra, és válassza ki az első lépésben letöltött **HotelReviews-Free. csv** fájlt.

    ![Adatok feltöltése](media/knowledge-store-create-portal/upload-command-bar.png "A szállodai értékelések feltöltése")

1. Kattintson a **feltöltés** gombra a CSV-fájl Azure Blob Storageba való importálásához. Ekkor megjelenik az új tároló.

    ![Az Azure Blob-tároló létrehozása](media/knowledge-store-create-portal/hotel-reviews-blob-container.png "Az Azure Blob-tároló létrehozása")

## <a name="3---configure-postman"></a>3 – Poster konfigurálása

Töltse le a [Poster-gyűjtemény forráskódját](https://github.com/Azure-Samples/azure-search-postman-searches/Tutorial/Knowledge_Store/KnowledgeStore.postman_collection.json) , és importálja a Poster-ba a **fájl, importálás..** . használatával. Váltson a **gyűjtemények** lapra, és kattintson a **...** gombra, és válassza a **Szerkesztés**lehetőséget. 

![Poster-alkalmazás, amely a navigációs](media/knowledge-store-create-rest/postman-edit-menu.png "menüben navigáljon a Poster szerkesztési menüjébe")

Az eredményül kapott szerkesztési párbeszédablakban navigáljon a **változók** lapra. 

A **változók** lapon megadhatja azokat az értékeket, amelyeket a Poster a kettős zárójelek között minden alkalommal felcserél. A Poster például lecseréli a `{{admin-key}}` szimbólumot a `admin-key` "aktuális értékével". A Poster ezt a helyettesítést URL-címek, fejlécek, a kérés törzse és így tovább teszi. 

A `admin-key` érték a Search Service **kulcsok** lapján található. Az [1. lépésben](#1---create-services)kiválasztott értékekre `search-service-name` és `storage-account-name` értéket kell módosítania. Állítsa be a `storage-connection-string` értéket a Storage-fiók **hozzáférési kulcsok** lapján lévő értékből. A többi érték változatlan maradhat.

![Poster alkalmazás-változók lap]–(media/knowledge-store-create-rest/postman-variables-window.png "a Poster változói ablaka")


| Változó    | Honnan szerezheti be |
|-------------|-----------------|
| `admin-key` | Search Service, **kulcsok** lap              |
| `api-version` | Szabadság "2019-05-06 – előzetes verzió" |
| `datasource-name` | Szabadság: "Hotel-Reviews-DS" | 
| `indexer-name` | Szabadság: "Hotel-Reviews-IXR" | 
| `index-name` | Szabadság: "Hotel-Reviews-IX" | 
| `search-service-name` | Search Service, fő név. URL-cím: @no__t – 0 | 
| `skillset-name` | Szabadság: "Hotel-Reviews-SS" | 
| `storage-account-name` | Storage-fiók, fő név | 
| `storage-connection-string` | Storage-fiók, **hozzáférési kulcsok** lap, **key1** **kapcsolati sztringje** | 
| `storage-container-name` | Távozás a "Hotel-Reviews" kifejezésre | 

### <a name="review-the-request-collection-in-postman"></a>Tekintse át a Poster kérelem-gyűjteményét

A Tudásbázis létrehozásához négy HTTP-kérelem kibocsátására van szükség: 

1. Egy PUT kérelem az index létrehozásához. Ez az index tartalmazza a Azure Search által használt és visszaadott adatkészleteket.
1. Egy POST-kérelem az adatforrás létrehozásához. Ez az adatforrás csatlakoztatja a Azure Search viselkedését az adat-és Tudásbázis Storage-fiókjához. 
1. Egy PUT kérelem a készségkészlet létrehozásához. A készségkészlet meghatározza az adataira alkalmazott dúsításokat és a Tudásbázis struktúráját.
1. Egy PUT-kérelem az indexelő létrehozásához. Az Indexer futtatása beolvassa az adatokat, alkalmazza a készségkészlet, és az eredményeket tárolja. Ezt a kérést utoljára kell futtatnia.

A [forráskód](https://github.com/Azure-Samples/azure-search-postman-searches/Tutorial/Knowledge_Store/KnowledgeStore.postman_collection.json) a következő négy kérelemmel rendelkező Poster-gyűjteményt tartalmaz. A kérések kiküldéséhez váltson a Poster-kérelem fülére, és vegye fel `api-key` és `Content-Type` kérelmek fejlécét. Állítsa a `api-key` értéket `{{admin-key}}` értékre. Állítsa a `Content-type` értéket `application/json` értékre. 

> [!div class="mx-imgBorder"]
> @no__t – 0Screenshot megjelenítve a Poster felületét a következő fejlécekhez: @ no__t-1

> [!Note]
> Az összes kérelemben `api-key` és `Content-type` fejléceket kell beállítania. Ha a Poster egy változót ismer fel, az a képen látható módon narancssárga szövegként jelenik meg, a képernyőképen a `{{admin-key}}` értékkel együtt. Ha a változó hibásan van írva, piros szövegben jelenik meg.
>

## <a name="4---create-an-azure-search-index"></a>4 – Azure Search index létrehozása

Létre kell hoznia egy Azure Search indexet, amely azokat az adattípusokat jelöli, amelyeknek a keresését, szűrését és fejlesztését érdekli. Az index létrehozásához hozzon létre egy PUT kérelmet `https://{{search-service-name}}.search.windows.net/indexes/{{index-name}}?api-version={{api-version}}` értékre. A Poster a [3. lépésben](#3---configure-postman)megadott értékekkel, például a `{{search-service-name}}`, a `{{index-name}}` és a `{{api-version}}` zárójelek közé tartozó szimbólumokat cseréli le. Ha más eszközt használ a REST-parancsok kiadására, akkor ezeket a változókat saját kezűleg kell helyettesítenie.

Adja meg a Azure Search index struktúráját a kérelem törzsében. A Poster-ben a `api-key` és a `Content-type` fejlécek beállítása után váltson a kérelem **törzs** ablaktáblájára. Ekkor meg kell jelennie a következő JSON-nek, de ha nem, válassza a **RAW** és a **JSON (alkalmazás/JSON)** elemet, és illessze be a következő kódot a törzsbe:

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

Láthatja, hogy ez az index-definíció a felhasználó számára (a szálloda neve, a tartalom áttekintése, a dátum stb.), a keresési metaadatok és az AI-javító adatok (az érzelmek, a kifejezések és a nyelv) kombinációja.

Nyomja meg a **Küldés** gombot a Put kérelem kiküldéséhez. A `201 - Created` állapotjelző üzenetnek kell megjelennie. Ha más állapotot kap, a **törzs** ablaktábla egy hibaüzenettel rendelkező JSON-választ fog megjeleníteni. 

## <a name="5---create-the-datasource"></a>5 – az adatforrás létrehozása

Most csatlakoznia kell Azure Search a [2. lépésben](#2---store-the-data)tárolt adatszolgáltatáshoz. Az adatforrás létrehozása `https://{{search-service-name}}.search.windows.net/datasources?api-version={{api-version}}` BEJEGYZÉSsel történik. A korábban megadott `api-key` és `Content-Type` fejléceket is be kell állítania. 

A Poster-ban nyissa meg az "adatforrás létrehozása" kérést. Váltson a **törzs** ablaktáblára, amelynek a következő kódnak kell lennie:

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

## <a name="6---create-the-skillset"></a>6 – a Készségkészlet létrehozása 

A következő lépés a Készségkészlet megadása, amely meghatározza az alkalmazandó fejlesztéseket és az eredményeket tároló tudásbázist. A Poster alatt nyissa meg a "Készségkészlet létrehozása" lapot. Ez a kérelem a következőt küldi el: `https://{{search-service-name}}.search.windows.net/skillsets/{{skillset-name}}?api-version={{api-version}}`.
Állítsa be a `api-key` és a `Content-type` fejléceket, ahogy korábban végzett. 

Két nagyméretű legfelső szintű objektum létezik: `"skills"` és `"knowledgeStore"`. A `"skills"` objektumon belüli összes objektum egy dúsítási szolgáltatás. Minden dúsítási szolgáltatás `"inputs"` és `"outputs"`. Figyelje meg, hogy a `LanguageDetectionSkill` kimeneti `targetName` `"Language"`. Ennek a csomópontnak az értékét a más képességek többsége használja bemenetként, a forrás pedig `document/Language`. Az egyik csomópont kimenetének egy másikhoz való bevitelének lehetősége még nyilvánvalóbb a `ShaperSkill` értékben, amely meghatározza, hogy az adatok hogyan áramlanak be a Tudásbázis tábláiba.

A `"knowledge_store"` objektum a `{{storage-connection-string}}` Poster változón keresztül csatlakozik a Storage-fiókhoz. Ezt követően a kibővített dokumentum és táblák és oszlopok között olyan leképezéseket tartalmaz, amelyek magukban a Tudásbázisban lesznek elérhetők. 

A készségkészlet létrehozásához a Poster **Send (Küldés** ) gombjára kattintva helyezze el a kérelmet.

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

## <a name="7---create-the-indexer"></a>7 – az indexelő létrehozása

Az utolsó lépés az indexelő létrehozása, amely ténylegesen beolvassa az adatokat, és aktiválja a készségkészlet. A Poster-ben váltson az "indexelő" lekérdezésre, és tekintse át a törzset. Amint láthatja, az indexelő definíciója több, már létrehozott erőforrásra hivatkozik – az adatforrás, az index és a készségkészlet. 

A `"parameters/configuration"` objektum azt szabályozza, hogy az indexelő Hogyan nyelje le az adatmennyiséget. Ebben az esetben a bemeneti adatok egy fejléc és vesszővel tagolt értékeket tartalmazó, egyetlen dokumentumban találhatók. A dokumentum kulcsa a dokumentum egyedi azonosítója, amely előtt a kódolás a forrásdokumentum URL-címe lesz. Végül a készségkészlet kimeneti értékei, például a Nyelvkód, a hangulat és a legfontosabb kifejezések a dokumentum megfelelő helyeire vannak leképezve. Figyelje meg, hogy a `Language` értéknél egyetlen érték van, `Sentiment` a `pages` tömb egyes elemeire lesz alkalmazva. a `Keyphrases` maga a tömb, és a `pages` tömb minden elemére is vonatkozik.

Miután beállította a `api-key` és a `Content-type` fejléceket, és megerősítette, hogy a kérelem törzse hasonló a következő forráskódhoz, kattintson a **Send** in poster (küldés) gombra. A Poster `https://{{search-service-name}}.search.windows.net/indexers/{{indexer-name}}?api-version={{api-version}}` értékűre HELYEZi a kérelmet. Azure Search létrehozza és futtatja az indexelő. 

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

## <a name="8---run-the-indexer"></a>8 – az indexelő futtatása 

A Azure Portal navigáljon a Search Service **áttekintéséhez** , és válassza ki az **Indexelő** lapot. Kattintson az előző lépésben létrehozott **Hotels-Reviews-IXR** elemre. Ha az indexelő még nem fut, kattintson a **Futtatás** gombra. Előfordulhat, hogy az indexelési feladat bizonyos, a nyelvi felismeréssel kapcsolatos figyelmeztetéseket is tartalmazhat, mivel az adatok tartalmazzák a kognitív képességek által még nem támogatott nyelveken írt értékeléseket. 

## <a name="next-steps"></a>További lépések

Most, hogy az adatait a kognitív szolgáltatásokkal gazdagítja, és az eredményeket egy tudásbázisba tervezte, Storage Explorer vagy Power BI használatával megismerheti a dúsított adatkészletet.

Az alábbi útmutatóban megismerheti, hogyan derítheti fel ezt a tudásbázist a Storage Explorer használatával.

> [!div class="nextstepaction"]
> [Megtekintés Storage Explorer](knowledge-store-view-storage-explorer.md)

A következő útmutatóban megismerheti, hogyan csatlakoztatható a Power BI a Knowledge Store-hoz.

> [!div class="nextstepaction"]
> [Kapcsolódás PowerBI-jal](knowledge-store-connect-power-bi.md)

Ha szeretné megismételni ezt a gyakorlatot, vagy próbáljon meg egy másik AI-bővítési bemutatót használni, törölje a *Hotel-Reviews-idxr* indexelő. Az indexelő törlése visszaállítja az ingyenes napi tranzakció számlálóját nullára.
