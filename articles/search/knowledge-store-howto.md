---
title: Ismerkedés a Knowledge Store szolgáltatással (előzetes verzió) – Azure Search
description: Megtudhatja, hogyan küldhet a Azure Search AI-indexelési folyamatok által létrehozott bővített dokumentumokat az Azure Storage-fiókban található tudásbázisba. Itt megtekintheti, átalakíthatja és felhasználhatja a dúsított dokumentumokat Azure Search és más alkalmazásokban.
manager: cgronlun
author: HeidiSteen
services: search
ms.service: search
ms.subservice: cognitive-search
ms.topic: tutorial
ms.date: 06/29/2019
ms.author: heidist
ms.openlocfilehash: 7539481105e1c1fd3b6816778e31e6edde8842d3
ms.sourcegitcommit: bc3a153d79b7e398581d3bcfadbb7403551aa536
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/06/2019
ms.locfileid: "68840809"
---
# <a name="how-to-get-started-with-knowledge-store-in-azure-search"></a>Ismerkedés a Knowledge Store szolgáltatással a Azure Search

> [!Note]
> A Knowledge áruház előzetes verzióban érhető el, és nem éles használatra készült. A [REST API 2019-05-06-es verziójának előzetes verziója](search-api-preview.md) biztosítja ezt a funkciót. Jelenleg nincs .NET SDK-támogatás.
>
A [Knowledge Store](knowledge-store-concept-intro.md) a más alkalmazásokban az Azure Storage-fiókba való adatbányászat során létrehozott AI-gazdagított dokumentumokat menti az indexelés során. A mentett bővítéseket is használhatja a Azure Search indexelési folyamat megértéséhez és pontosításához. 

A tudásbázist egy *készségkészlet* határozza meg, és egy indexelőhozza létre. A rendszer a tárolóban lévő adatstruktúrákat meghatározó kivetítéseken keresztül adja meg a Tudásbázis fizikai kifejezését. A bemutató befejezése után létrehozta az összes objektumot, és tudni fogja, hogyan illeszkednek egymáshoz. 

Ebben a gyakorlatban a mintaadatok, szolgáltatások és eszközök használatával megismerheti az első készségkészlet-definíciók létrehozásához és használatához szükséges alapvető munkafolyamatot.

## <a name="prerequisites"></a>Előfeltételek

A Knowledge Store több szolgáltatás központjában található, az Azure Blob Storage és az Azure Table Storage fizikai tárolást biztosít, valamint Azure Search és Cognitive Services az objektumok létrehozásához és frissítéséhez. Az alapszintű [architektúra](knowledge-store-concept-intro.md) ismerete az útmutató előfeltétele.

Ebben a rövid útmutatóban a következő szolgáltatásokat és eszközöket használjuk. 

+ [Tegye közzé a Poster Desktop alkalmazást](https://www.getpostman.com/), amely a HTTP-kérelmek Azure Search való küldésére szolgál.

+ [Hozzon létre egy Azure Storage-fiókot](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account) a mintaadatok és a Knowledge Store tárolásához. A Tudásbázis az Azure Storage-ban fog létezni.

+ [Hozzon létre egy Cognitive Services erőforrást](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) a S0 utólagos elszámolású szinten, hogy széles spektrumú hozzáférést biztosítson az AI-bővítésekben használt szaktudások teljes skálája számára. Cognitive Services és az Azure Search szolgáltatásnak ugyanabban a régióban kell lennie.

+ [Hozzon létre egy Azure Search szolgáltatást](search-create-service-portal.md) , vagy [keressen egy meglévő szolgáltatást](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) a jelenlegi előfizetése alatt. Ehhez az oktatóanyaghoz használhatja az ingyenes szolgáltatást. 

A JSON-dokumentumok és a Poster-gyűjtemények is szükségesek. A kiegészítő fájlok keresésének és betöltésének utasításait a [Mintaadatok előkészítése](#prepare-sample-data) szakaszban találja.

## <a name="get-a-key-and-url"></a>Kulcs és URL-cím lekérése

A REST-hívásokhoz minden kérésének tartalmaznia kell a szolgáltatás URL-címét és egy hozzáférési kulcsot. Mindkettőhöz létrejön egy keresési szolgáltatás, így ha hozzáadta az előfizetéséhez az Azure Searchöt, kövesse az alábbi lépéseket a szükséges információk beszerzéséhez:

1. [Jelentkezzen be a Azure Portalba](https://portal.azure.com/), és a keresési szolgáltatás **Áttekintés** lapján töltse le az URL-címet. A végpontok például a következőképpen nézhetnek ki: `https://mydemo.search.windows.net`.

1. A **Beállítások** > **kulcsaiban**kérjen meg egy rendszergazdai kulcsot a szolgáltatásra vonatkozó összes jogosultsághoz. Az üzletmenet folytonossága érdekében két, egymással megváltoztathatatlan rendszergazdai kulcs áll rendelkezésre. Az objektumok hozzáadására, módosítására és törlésére vonatkozó kérésekhez használhatja az elsődleges vagy a másodlagos kulcsot is.

    ![Http-végpont és elérési kulcs](media/search-get-started-postman/get-url-key.png "Http-végpont és elérési kulcs") beszerzése

Minden kérelemhez API-kulcs szükséges a szolgáltatásnak küldött összes kéréshez. A szolgáltatás nevét és API-kulcsát minden HTTP-kérelemben meg kell adnia a következő szakaszokban.

<a name="prepare-sample-data"></a>

## <a name="prepare-sample-data"></a>Mintaadatok előkészítése

Egy Tudásbázis tartalmazza a dúsítási folyamat kimenetét. A bemenetek "használhatatlan" adatokat tartalmaznak, amelyek végső soron "felhasználható" állapotba kerülnek, ahogy az a folyamaton halad át. A használhatatlan adatok például tartalmazhatnak olyan képfájlokat, amelyeket elemezni kell szöveg-vagy képtulajdonságok esetén, vagy az entitások, a kulcsfontosságú kifejezések vagy a hangulat alapján elemezhető sűrű szöveges fájlok. 

Ez a gyakorlat a [Caselaw Access Project](https://case.law/bulk/download/) nyilvános tömeges adatletöltési lapjáról származó, sűrű szövegfájlokat (esetjog-információkat) használ. Feltöltöttünk egy 10 dokumentumból álló mintát a GitHubba ehhez a gyakorlathoz. 

Ebben a feladatban létrehoz egy Azure BLOB-tárolót, amellyel a dokumentumok bemenetként használhatók a folyamathoz. 

1. Töltse le és csomagolja ki a [Azure Search minta](https://github.com/Azure-Samples/azure-search-sample-data/tree/master/caselaw) adattárházat a [Caselaw](https://github.com/Azure-Samples/azure-search-sample-data/tree/master/caselaw)-adatkészlet beszerzéséhez. 

1. [Jelentkezzen be a Azure Portalba](https://portal.azure.com), navigáljon az Azure Storage-fiókjához, kattintson a **Blobok**elemre, majd a **+ tároló**elemre.

1. [Hozzon létre egy blob](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal) -tárolót a mintaadatok tárolására: 

   1. Nevezze el a `caselaw-test`tárolót. 
   
   1. Állítsa a nyilvános hozzáférési szintet bármelyik érvényes értékére.

1. A tároló létrehozása után nyissa meg, majd válassza a parancssáv **feltöltés** elemét.

   ![Feltöltés a parancssáv](media/search-semi-structured-data/upload-command-bar.png "Feltöltés a parancssáv")

1. Navigáljon a **caselaw-sample. JSON** fájlt tartalmazó mappához. Válassza ki a fájlt, majd kattintson a **feltöltés**elemre.

1. Az Azure Storage-ban a kapcsolódási karakterlánc és a tároló neve olvasható.  Mindkét sztringre szüksége lesz az [adatforrás létrehozásakor](#create-data-source):

   1. Az Áttekintés lapon kattintson a **hozzáférési kulcsok** elemre, és másolja a *kapcsolati karakterláncot*. Ezzel elindul `DefaultEndpointsProtocol=https;` , és a- `EndpointSuffix=core.windows.net`vel zárul. A fiók neve és kulcsa a (z) között van. 

   1. A tároló nevének vagy bármely `caselaw-test` hozzárendelt névnek kell lennie.



## <a name="set-up-postman"></a>A Postman beállítása

A Poster az ügyfélalkalmazás, amelyet a kérések és a JSON-dokumentumok küldéséhez fog használni a Azure Search. A kérések közül több is megfogalmazható a cikkben található információk használatával. Azonban a legnagyobb kérések közül kettő (index létrehozása, készségkészlet létrehozása) olyan részletes JSON-t tartalmaz, amely túl nagy ahhoz, hogy beágyazni lehessen egy cikkben. 

Az összes JSON-dokumentum és-kérelem teljes körű elérhetővé tételéhez létrehozunk egy Poster-gyűjteményt tartalmazó fájlt. A fájl letöltése és importálása az első feladat az ügyfél beállításakor.

1. Töltse le és csomagolja ki a [Azure Search Poster Samples](https://github.com/Azure-Samples/azure-search-postman-samples) repositoryt.

1. Indítsa el a Poster-t, és importálja a Caselaw Poster-gyűjteményt:

   1. Kattintson azimportfájl > importálása fájlok elemre. >  

   1. Navigáljon a \azure-search-postman-samples-master\azure-search-postman-samples-master\Caselaw mappára.

   1. Válassza ki a **Caselaw. postman_collection_v2. JSON**fájlt. A gyűjteményben négy **post** kérelemnek kell megjelennie.

   ![Poster-gyűjtemény a Caselaw] -bemutatóhoz (media/knowledge-store-howto/postman-collection.png "Poster-gyűjtemény a Caselaw") -bemutatóhoz
   

## <a name="create-an-index"></a>Index létrehozása
    
Az első kérelem a [create index API](https://docs.microsoft.com/rest/api/searchservice/create-data-source)-t használja, és létrehoz egy Azure Search indexet, amely az összes kereshető adatértéket tárolja. Az index határozza meg az összes mezőt, paramétert és attribútumot.

Nem feltétlenül szükséges az adatbányászati index, de az indexelő nem fog futni, hacsak nincs megadva index. 

1. Az URL- `https://YOUR-AZURE-SEARCH-SERVICE-NAME.search.windows.net/indexes?api-version=2019-05-06-Preview`címben cserélje `YOUR-AZURE-SEARCH-SERVICE-NAME` le a kifejezést a keresési szolgáltatás nevére. 

1. A fejléc szakaszban cserélje le `<YOUR AZURE SEARCH ADMIN API-KEY>` a (Azure Search) felügyeleti API-kulcsát.

1. A Body (törzs) szakaszban a JSON-dokumentum egy index séma. A láthatóságra összecsukva az index külső rendszerhéja a következő elemekből áll. A mezők gyűjteménye megfelel a caselaw adatkészlet mezőinek.

   ```json
   {
      "name": "caselaw",
      "defaultScoringProfile": null,
      "fields": [],
      "scoringProfiles": [],
      "corsOptions": null,
      "suggesters": [],
      "analyzers": [],
      "tokenizers": [],
      "tokenFilters": [],
      "charFilters": [],
      "encryptionKey": null
   }
   ```

1. Bontsa `fields` ki a gyűjteményt. Tartalmazza az index definíciójának nagy részét, amely egyszerű mezőket, beágyazott alstruktúrákat és gyűjteményeket tartalmazó [összetett mezőket](search-howto-complex-data-types.md) tartalmaz.

   Szánjon egy kis időt a `casebody` komplex mezőhöz tartozó mező definíciójának áttekintésére a 302-384-es sorokban. Figyelje meg, hogy egy összetett mező más összetett mezőket is tartalmazhat, amikor szükség van a hierarchikus ábrázolásokra. A hierarchikus struktúrák modellezése egy indexben, az itt látható módon, valamint egy készségkészlet kivetítése is lehet, így a rendszer létrehoz egy beágyazott adatstruktúrát a Tudásbázisban.

   ```json
   {
    "name": "casebody",
    "type": "Edm.ComplexType",
    "fields": [
        {
            "name": "status",
            "type": "Edm.String",
            "searchable": true,
            "filterable": true,
            "retrievable": true,
            "sortable": true,
            "facetable": true,
            "key": false,
            "indexAnalyzer": null,
            "searchAnalyzer": null,
            "analyzer": null,
            "synonymMaps": []
        },
        {
            "name": "data",
            "type": "Edm.ComplexType",
            "fields": [
                {
                    "name": "head_matter",
                    "type": "Edm.String",
                    "searchable": true,
                    "filterable": false,
                    "retrievable": true,
                    "sortable": false,
                    "facetable": false,
                    "key": false,
                    "indexAnalyzer": null,
                    "searchAnalyzer": null,
                    "analyzer": null,
                    "synonymMaps": []
                },
                {
                    "name": "opinions",
                    "type": "Collection(Edm.ComplexType)",
                    "fields": [
                        {
                            "name": "author",
                            "type": "Edm.String",
                            "searchable": true,
                            "filterable": true,
                            "retrievable": true,
                            "sortable": false,
                            "facetable": true,
                            "key": false,
                            "indexAnalyzer": null,
                            "searchAnalyzer": null,
                            "analyzer": null,
                            "synonymMaps": []
                        },
                        {
                            "name": "text",
                            "type": "Edm.String",
                            "searchable": true,
                            "filterable": false,
                            "retrievable": true,
                            "sortable": false,
                            "facetable": false,
                            "key": false,
                            "indexAnalyzer": null,
                            "searchAnalyzer": null,
                            "analyzer": null,
                            "synonymMaps": []
                        },
                        {
                            "name": "type",
                            "type": "Edm.String",
                            "searchable": true,
                            "filterable": true,
                            "retrievable": true,
                            "sortable": false,
                            "facetable": true,
                            "key": false,
                            "indexAnalyzer": null,
                            "searchAnalyzer": null,
                            "analyzer": null,
                            "synonymMaps": []
                        }
                    ]
                },
    . . .
   ```

1. A kérelem végrehajtásához kattintson a **Küldés** gombra.  Meg kell kapnia **az állapotot: 201 az** üzenet válaszként lett létrehozva.

<a name="create-data-source"></a>

## <a name="create-a-data-source"></a>Adatforrás létrehozása

A második kérelem az [adatforrás létrehozása API](https://docs.microsoft.com/rest/api/searchservice/create-data-source) -t használja az Azure Blob Storage-hoz való kapcsolódáshoz. 

1. Az URL- `https://YOUR-AZURE-SEARCH-SERVICE-NAME.search.windows.net/datasources?api-version=2019-05-06-Preview`címben cserélje `YOUR-AZURE-SEARCH-SERVICE-NAME` le a kifejezést a keresési szolgáltatás nevére. 

1. A fejléc szakaszban cserélje le `<YOUR AZURE SEARCH ADMIN API-KEY>` a (Azure Search) felügyeleti API-kulcsát.

1. A szövegtörzs szakaszban a JSON-dokumentum tartalmazza a Storage-fiók és a blob-tároló nevét. A kapcsolati karakterlánc a Storage-fiók **hozzáférési kulcsainak**Azure Portal található. 

    ```json
    {
        "name": "caselaw-ds",
        "description": null,
        "type": "azureblob",
        "subtype": null,
        "credentials": {
            "connectionString": "DefaultEndpointsProtocol=https;AccountName=<YOUR-STORAGE-ACCOUNT>;AccountKey=<YOUR-STORAGE-KEY>;EndpointSuffix=core.windows.net"
        },
        "container": {
            "name": "<YOUR-BLOB-CONTAINER-NAME>",
            "query": null
        },
        "dataChangeDetectionPolicy": null,
        "dataDeletionDetectionPolicy": null
    }
    ```

1. A kérelem végrehajtásához kattintson a **Küldés** gombra.  Meg kell kapnia **az állapotot: 201 az** üzenet válaszként lett létrehozva.



<a name="create-skillset"></a>

## <a name="create-a-skillset-and-knowledge-store"></a>Készségkészlet és a Knowledge Store létrehozása

A harmadik kérelem a [create KÉSZSÉGKÉSZLET API](https://docs.microsoft.com/rest/api/searchservice/create-skillset)-t használja, és létrehoz egy Azure Search objektumot, amely meghatározza, hogy milyen kognitív ismereteket kell hívni, hogyan lehet a tudást egyesíteni, és ami a legfontosabb ebben a bemutatóban – a Tudásbázis megadása.

1. Az URL- `https://YOUR-AZURE-SEARCH-SERVICE-NAME.search.windows.net/skillsets?api-version=2019-05-06-Preview`címben cserélje `YOUR-AZURE-SEARCH-SERVICE-NAME` le a kifejezést a keresési szolgáltatás nevére. 

1. A fejléc szakaszban cserélje le `<YOUR AZURE SEARCH ADMIN API-KEY>` a (Azure Search) felügyeleti API-kulcsát.

1. A Body (törzs) szakaszban a JSON-dokumentum egy készségkészlet-definíció. A láthatóságra összecsukva a készségkészlet külső rendszerhéja a következő elemekből áll. A `skills` gyűjtemény meghatározza a memóriában lévő bővítéseket, de a `knowledgeStore` definíció meghatározza a kimenet tárolási módját. A `cognitiveServices` definíció a mesterséges intelligencia-gazdagító motorokkal létesített kapcsolat.

   ```json
   {
    "name": "caselaw-ss",
    "description": null,
    "skills": [],
    "cognitiveServices": [],
    "knowledgeStore": []
   }
   ```

1. `cognitiveServices` Bontsa `knowledgeStore` ki a elemet, és adja meg a kapcsolatok adatait. A példában ezek a karakterláncok a készségkészlet-definíció után találhatók a kérelem törzsének vége felé. 

   A `cognitiveServices`esetében hozzon létre egy erőforrást a S0 szinten, amely ugyanabban a régióban található, mint Azure Search. A cognitiveServices nevét és kulcsát a Azure Portal azonos oldaláról kérheti le. 
   
   A `knowledgeStore`esetében ugyanazt a caselaw használja, mint a blob-tárolóhoz.

    ```json
    "cognitiveServices": {
        "@odata.type": "#Microsoft.Azure.Search.CognitiveServicesByKey",
        "description": "YOUR-SAME-REGION-S0-COGNITIVE-SERVICES-RESOURCE",
        "key": "YOUR-COGNITIVE-SERVICES-KEY"
    },
    "knowledgeStore": {
        "storageConnectionString": "YOUR-STORAGE-ACCOUNT-CONNECTION-STRING",
    ```

1. Bontsa ki a szaktudás gyűjteményt, különösen az 85-es és a 179-es vonalakon található formáló képességeket. A formáló szakértelme azért fontos, mert összeállítja azokat az adatstruktúrákat, amelyeket a tudás kitermeléséhez szeretne használni. A készségkészlet végrehajtása során ezek a struktúrák csak memóriában vannak, de a következő lépésre való áttérés során látni fogja, hogyan menthető a kimenet egy tudásbázisba a további feltáráshoz.

   Az alábbi kódrészlet a 217-es sorból származik. 

    ```json
    "name": "Opinions",
    "source": null,
    "sourceContext": "/document/casebody/data/opinions/*",
    "inputs": [
        {
            "name": "Text",
            "source": "/document/casebody/data/opinions/*/text"
        },
        {
            "name": "Author",
            "source": "/document/casebody/data/opinions/*/author"
        },
        {
            "name": "Entities",
            "source": null,
            "sourceContext": "/document/casebody/data/opinions/*/text/pages/*/entities/*",
            "inputs": [
                {
                    "name": "Entity",
                    "source": "/document/casebody/data/opinions/*/text/pages/*/entities/*/value"
                },
                {
                    "name": "EntityType",
                    "source": "/document/casebody/data/opinions/*/text/pages/*/entities/*/category"
                }
            ]
        }
    ]
   . . .
   ```

1. Bontsa `projections` ki `knowledgeStore`az elemet a alkalmazásban, az 262-es vonaltól kezdve. A kivetítések határozzák meg a Knowledge Store összeállítását. A vetítések a Tables-Objects párokban vannak megadva, de jelenleg csak egy időpontban. Ahogy az első kivetítésben látható, az `tables` meg van adva `objects` , de nem. A másodikban ez az ellenkezője.

   Az Azure Storage-ban a létrehozott táblák táblázatos tárolásban lesznek létrehozva, és minden objektum egy tárolót kap a blob Storage-ban.

   A blob-objektumok jellemzően egy alkoholtartalom teljes kifejezését tartalmazzák. A táblázatok jellemzően részleges dúsításokat tartalmaznak, amelyek az adott célra rendezett kombinációkban szerepelnek. Ez a példa egy Cases táblát és egy véleményeket tartalmazó táblázatot mutat be, de nem látható más táblák, például entitások, ügyvédek, bírák és felek.

    ```json
    "projections": [
        {
            "tables": [
                {
                    "tableName": "Cases",
                    "generatedKeyName": "CaseId",
                    "source": "/document/Case"
                },
                {
                    "tableName": "Opinions",
                    "generatedKeyName": "OpinionId",
                    "source": "/document/Case/OpinionsSnippets/*"
                }
            ],
            "objects": []
        },
        {
            "tables": [],
            "objects": [
                {
                    "storageContainer": "enrichedcases",
                    
                    "source": "/document/CaseFull"
                }
            ]
        }
    ]
    ```

1. A kérelem végrehajtásához kattintson a **Küldés** gombra. A válasznak **201** -nek kell lennie, és az alábbi példához hasonlóan kell kinéznie, amely a válasz első részét mutatja.

    ```json
    {
    "name": "caselaw-ss",
    "description": null,
    "skills": [
        {
            "@odata.type": "#Microsoft.Skills.Text.SplitSkill",
            "name": "SplitSkill#1",
            "description": null,
            "context": "/document/casebody/data/opinions/*/text",
            "defaultLanguageCode": "en",
            "textSplitMode": "pages",
            "maximumPageLength": 5000,
            "inputs": [
                {
                    "name": "text",
                    "source": "/document/casebody/data/opinions/*/text
                }
            ],
            "outputs": [
                {
                    "name": "textItems",
                    "targetName": "pages"
                }
            ]
        },
        . . .
    ```

## <a name="create-and-run-an-indexer"></a>Indexelő létrehozása és futtatása

A negyedik kérelem a [create Indexer API](https://docs.microsoft.com/rest/api/searchservice/create-indexer)-t használja, és létrehoz egy Azure Search indexelő. Az indexelő az indexelési folyamat végrehajtó motorja. Az eddig létrehozott összes definíció a következő lépéssel kerül mozgásba.

1. Az URL- `https://YOUR-AZURE-SEARCH-SERVICE-NAME.search.windows.net/indexers?api-version=2019-05-06-Preview`címben cserélje `YOUR-AZURE-SEARCH-SERVICE-NAME` le a kifejezést a keresési szolgáltatás nevére. 

1. A fejléc szakaszban cserélje le `<YOUR AZURE SEARCH ADMIN API-KEY>` a (Azure Search) felügyeleti API-kulcsát.

1. A törzs szakaszban a JSON-dokumentum az indexelő nevét adja meg. Az indexelő megköveteli az adatforrás és az index megírását. Egy készségkészlet nem kötelező az indexelő számára, de a mesterséges intelligenciához szükséges.

    ```json
    {
        "name": "caselaw-idxr",
        "description": null,
        "dataSourceName": "caselaw-ds",
        "skillsetName": "caselaw-ss",
        "targetIndexName": "caselaw",
        "disabled": null,
        "schedule": null,
        "parameters": { },
        "fieldMappings": [],
        "outputFieldMappings": [ ]
    ```

1. Bontsa ki a outputFieldMappings. A fieldMappings ellentétben, amelyek az adatforrásban lévő mezők és egy index mezőinek egyéni leképezésére szolgálnak, a outputFieldMappings a folyamat által létrehozott és kitöltött mezők leképezésére szolgálnak az index vagy a vetítés során.

    ```json
    "outputFieldMappings": [
        {
            "sourceFieldName": "/document/casebody/data/opinions/*/text/pages/*/people/*",
            "targetFieldName": "people",
            "mappingFunction": null
        },
        {
            "sourceFieldName": "/document/casebody/data/opinions/*/text/pages/*/organizations/*",
            "targetFieldName": "orginizations",
            "mappingFunction": null
        },
        {
            "sourceFieldName": "/document/casebody/data/opinions/*/text/pages/*/locations/*",
            "targetFieldName": "locations",
            "mappingFunction": null
        },
        {
            "sourceFieldName": "/document/Case/OpinionsSnippets/*/Entities/*",
            "targetFieldName": "entities",
            "mappingFunction": null
        },
        {
            "sourceFieldName": "/document/casebody/data/opinions/*/text/pages/*/keyPhrases/*",
            "targetFieldName": "keyPhrases",
            "mappingFunction": null
        }
    ]
    ```

1. A kérelem végrehajtásához kattintson a **Küldés** gombra. A válasznak **201** -nek kell lennie, és a válasz törzsének majdnem azonosnak kell lennie a megadott adattartalommal (rövidítve).

    ```json
    {
        "name": "caselaw-idxr",
        "description": null,
        "dataSourceName": "caselaw-ds",
        "skillsetName": "caselaw-ss",
        "targetIndexName": "caselaw",
        "disabled": null,
        "schedule": null,
        "parameters": { },
        "fieldMappings": [],
        "outputFieldMappings": [ ]
    }
    ```

## <a name="explore-knowledge-store"></a>Ismerkedés a Knowledge Store szolgáltatással

Az első dokumentum importálása után megkezdheti a felfedezést. Ehhez a feladathoz használja a [**Storage Explorer**](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-storage-explorer) a portálon.

Fontos tisztában lenni azzal, hogy a Knowledge Store teljesen le van választva Azure Searchról. A Azure Search index és a Knowledge Store egyaránt tartalmaz adatábrázolást és-tartalmakat, de részben a különböző módokat. Használja az indexet a teljes szöveges kereséshez, a szűrt kereséshez és a Azure Search által támogatott összes forgatókönyvhöz. Vagy lépjen tovább a saját tudásbázisához, és csatolja más eszközöket a tartalmak elemzéséhez.

## <a name="takeaways"></a>Legfontosabb ismeretek

Ezzel létrehozta az első Azure Storage-beli áruházát, és Storage Explorer használta a dúsítások megtekintéséhez. Ez a tárolt alkoholtartalom-növeléssel kapcsolatos alapvető tapasztalat. 

## <a name="next-steps"></a>További lépések

A formáló képesség az új alakzatokhoz egyesíthető, részletes adatűrlapok létrehozásának nagy mennyiségű emelése. A következő lépésként tekintse át ennek a képességnek a hivatkozási oldalát, amely részletesen ismerteti a használati módját.

> [!div class="nextstepaction"]
> [A formálói szakértelem referenciája](cognitive-search-skill-shaper.md)


<!---
## Keep This

How to convert unformatted JSON into an indented JSON document structure that allows you to quickly identify nested structures. Useful for creating an index that includes complex types.

1. Use Visual Studio Code.
2. Open data.jsonl
--->
