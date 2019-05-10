---
title: Tudásbázis Store (előzetes verzió) – Azure Search az első lépések
description: Ismerje meg a lépéseket, képi elemekben gazdag dokumentumok indexelése az Azure storage-fiókban Tudásbázis-áruházban az Azure Search folyamatok AI által létrehozott küldéséhez. Itt megtekintheti, formálja át, és felhasználását, képi elemekben gazdag dokumentumokat az Azure Search és az egyéb alkalmazásokban.
manager: cgronlun
author: HeidiSteen
services: search
ms.service: search
ms.topic: quickstart
ms.date: 05/08/2019
ms.author: heidist
ms.openlocfilehash: d9006e3fcfc9691b9f3eec4b86c545fd3fea9f8a
ms.sourcegitcommit: 399db0671f58c879c1a729230254f12bc4ebff59
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/09/2019
ms.locfileid: "65471744"
---
# <a name="how-to-get-started-with-knowledge-store"></a>Hogyan kell a Tudásbázis Store használatának első lépései

[Tudásbázis Store](knowledge-store-concept-intro.md) az Azure Search szolgáltatásban, amely menti a mesterséges Intelligencia végrehajtott információbeolvasás egy indexelési folyamat Tudásbázis adatbányászatra más alkalmazásokban létrehozott új előzetes verziójú funkció. Mentett végrehajtott információbeolvasás segítségével megismerheti és finomítsa az Azure Search indexelési folyamat.

Tudásbázis áruházbeli határozza meg a képességek alkalmazási lehetőségét. A normál Azure Search teljes szöveges keresés alkalmazási célját, a képességek alkalmazási lehetőségét AI végrehajtott információbeolvasás kereshetővé tartalom több biztosít. Tudásbázis adatbányászati forgatókönyvek esetén, a képességek alkalmazási lehetőségét a szerepkör létrehozása, feltöltése, és elemzési több adatstruktúrák tárolására vagy más alkalmazások és folyamatok modellezése.

Ebben a gyakorlatban a mintaadatokat, szolgáltatások és az alapvető munkafolyamat létrehozásáról és használatáról az első Tudásbázis store hangsúlyt fektetve indexmezők definíció további eszközök elindítása.

## <a name="prerequisites"></a>Előfeltételek

Ez a rövid útmutató az alábbi szolgáltatások, eszközök és adatok használatosak. 

+ [Az Azure Search szolgáltatás létrehozása](search-create-service-portal.md) vagy [keresse meg a meglévő service](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) az aktuális előfizetésben. Ebben az oktatóanyagban egy ingyenes szolgáltatás használhatja. 

+ [Az Azure storage-fiók létrehozása](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account) a mintaadatok tárolásához. A Tudásbázis-tárolót az Azure storage fogja szerepel. 

+ [Cognitive Services-erőforrás létrehozása](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) rétegben az S0 használatalapú AI végrehajtott információbeolvasás használt képességek széles broad-spectrum eléréséhez. Ugyanabban a régióban ehhez az erőforráshoz, és az Azure Search szolgáltatás szükségesek.

+ [Postman asztali alkalmazás](https://www.getpostman.com/) a kérelmek küldését az Azure Search.

+ [Postman-gyűjtemény](https://github.com/Azure-Samples/azure-search-postman-samples/tree/master/Caselaw) előkészített kérések egy adatforrást, index, készségeitől és az indexelő létrehozása. Több objektumdefiníciók túl hosszúak tartalmazza az ebben a cikkben. Ez a gyűjtemény teljes egészében az index és indexmezők definíciók megtekintéséhez be kell szereznie.

+ [Mintaadatok Caselaw](https://github.com/Azure-Samples/azure-search-sample-data/tree/master/caselaw) származó a [Caselaw hozzáférés projekt](https://case.law/bulk/download/) nyilvános adatok kötegelt letöltési oldalát. A gyakorlat, az első 10 dokumentumok, az első letöltés (Arkansas) használja. 10-dokumentum minta azt feltölteni a Githubra, ehhez a gyakorlathoz.

## <a name="get-a-key-and-url"></a>Egy kulcsot és egy URL-cím beszerzése

A REST-hívásokhoz minden kérésének tartalmaznia kell a szolgáltatás URL-címét és egy hozzáférési kulcsot. Mindkettőhöz létrejön egy keresési szolgáltatás, így ha hozzáadta az előfizetéséhez az Azure Searchöt, kövesse az alábbi lépéseket a szükséges információk beszerzéséhez:

1. [Jelentkezzen be az Azure Portalon](https://portal.azure.com/), és a search szolgáltatás **áttekintése** lapon, az URL-cím lekéréséhez. A végpontok például a következőképpen nézhetnek ki: `https://mydemo.search.windows.net`.

1. A **beállítások** > **kulcsok**, a szolgáltatás a teljes körű rendszergazdai kulcs beszerzése. Nincsenek két felcserélhetők adminisztrációs kulcsot, az üzletmenet folytonosságának megadott abban az esetben egy vihető kell. Használható vagy az elsődleges vagy másodlagos kulcsot a kérések hozzáadása, módosítása és törlése objektumokat.

    ![Egy HTTP-végpontját és hozzáférési kulcs lekérése](media/search-fiddler/get-url-key.png "HTTP végpontját és hozzáférési kulcs beszerzése")

Minden kérelemhez szükséges halasztása minden kérelemnél a szolgáltatásnak küldött api-kulcsát.

## <a name="prepare-sample-data"></a>Mintaadatok létrehozása

1. [Jelentkezzen be az Azure Portalon](https://portal.azure.com)lépjen az Azure storage-fiókot, kattintson a **Blobok**, és kattintson a **+ tároló**.

1. [Hozzon létre egy blobtárolót](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal) mintaadatok tárolásához. A tároló neve "caselaw-test" használja. A nyilvános hozzáférés szintje beállíthatja az érvényes értékek bármelyikére.

1. A tároló létrehozása után nyissa meg és jelölje ki **feltöltése** a parancssávon.

   ![Töltse fel a parancssávon](media/search-semi-structured-data/upload-command-bar.png "parancssávon feltöltése")

1. Keresse meg a mappát tartalmazó a **caselaw-sample.json** mintafájlt. Válassza ki a fájlt, és kattintson a **feltöltése**.


## <a name="set-up-postman"></a>A Postman beállítása

Indítsa el a Postmant, és a Caselaw Postman-gyűjtemény importálása. Azt is megteheti állítsa be a HTTP-kérések egy sorozat. Ha ismeri ezt az eszközt, tekintse meg [Ismerkedés az Azure Search REST API-k a postmannel](search-fiddler.md).

+ Ez az útmutató minden hívás érték-kérési metódus **PUT** vagy **POST**.
+ Kérelemfejlécek (2) a következők: "Content-type" értékre az "application/json", "api-key" értékre az "admin key" (adminisztrációs kulcs, elsődleges keresési kulcs helyőrzője) jelölik. 
+ Kérelem törzse hely, ahol elhelyezi a hívás tényleges tartalmát. 

  ![Részben strukturált keresés](media/search-semi-structured-data/postmanoverview.png)

A postmannel, hogy a keresési szolgáltatás négy API-hívások egy adatforrást, egy index, a képességek alkalmazási lehetőségét és az indexelő - létrehozása ebben a sorrendben. Az adatforrás tartalmaz egy mutatót a JSON-adatok és az Azure storage-fiók. A keresési szolgáltatás lehetővé teszi a kapcsolatot, az adatok importálásakor.

[Egy képességcsoport létrehozása](#create-skillset) Ez az útmutató célja van: azt adja meg a felderítési bővítést lépéseket, és hogyan rendszer megőrzi az adatokat a Tudásbázis-tárolóban.

URL-cím végponthoz meg kell adnia az api-verziót és a egy hívás adja vissza egy **201 Created**. Az előzetes api-Version egy indexmezők Tudásbázis áruházbeli támogatása való létrehozásának `2019-05-06-Preview` (kis-és nagybetűket).

A REST-ügyfélről hajtsa végre a következő API-hívások.

## <a name="create-a-data-source"></a>Adatforrás létrehozása

A [Data Source API létrehozása](https://docs.microsoft.com/rest/api/searchservice/create-data-source) létrehoz egy Azure Search-objektumot, amely megadja, hogy milyen adatok legyenek indexelve.

Az a hívás végpontja `https://[service name].search.windows.net/datasources?api-version=2019-05-06-Preview` 

1. Cserélje le a `[service name]` elemet a keresési szolgáltatás nevére. 

2. Ez a hívás a kérelem törzsének tartalmaznia kell a tárfiók kapcsolati karakterláncot, és a blob tároló nevét. A kapcsolat az Azure Portalon, a storage-fiók belül található **Tárelérési kulcsok**. 

   Ellenőrizze, hogy cserélje le a kapcsolati karakterlánc és a blob tároló neve a kérés törzsében. a hívás végrehajtása előtt.

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

3. Küldje el a kérést. A következő választ kell kapnia **201-es** és a válasz törzse majdnem megegyezik a megadott kérelem tartalma kell kinéznie.

    ```json
    {
        "name": "caselaw-ds",
        "description": null,
        "type": "azureblob",
        "subtype": null,
        "credentials": {
            "connectionString": "DefaultEndpointsProtocol=https;AccountName=<your storage account>;AccountKey=<your storage key>;EndpointSuffix=core.windows.net"
        },
        "container": {
            "name": "<your blob container name>",
            "query": null
        },
        "dataChangeDetectionPolicy": null,
        "dataDeletionDetectionPolicy": null
    }
    ```

## <a name="create-an-index"></a>Index létrehozása
    
A második hívás [Index API létrehozása](https://docs.microsoft.com/rest/api/searchservice/create-data-source), az Azure Search-index létrehozása, amely tárolja az összes kereshető adatot. Az index határozza meg, az összes mező, paraméterek és attribútumok.

Nem feltétlenül kell a Tudásbázis adatbányászati index, de az indexelő nem fut, kivéve, ha az index biztosítja. 

Ez a hívás URL-címe `https://[service name].search.windows.net/indexes?api-version=2019-05-06-Preview`

1. Cserélje le a `[service name]` elemet a keresési szolgáltatás nevére.

2. Másolja az indexdefiníciót a kérelemtörzsbe Postman-gyűjteményben a Create Index kérelemből. Az Indexdefiníció több száz sor itt nyomtatása túl hosszú. 

   A külső felület az index a következő elemekből áll. 

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

3. A `fields` gyűjtemény tartalmazza az indexdefiníciót használtakhoz. Egyszerű mezőket tartalmaz [összetett mezők](search-howto-complex-data-types.md) beágyazott alépítményeit és gyűjteményeket.

   Tekintse át a mezőt definíciója `casebody` a vonalak 302-384-et. Figyelje meg, hogy egy összetett mezőt tartalmazhat más összetett mezők, ha a hierarchikus reprezentációinak van szükség.

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

4. Küldje el a kérést. 

   A következő választ kell kapnia **201-es** és a következő példában az első néhány mezők hasonlóan néz ki:

    ```json
    {
        "name": "caselaw",
        "defaultScoringProfile": null,
        "fields": [
            {
                "name": "id",
                "type": "Edm.String",
                "searchable": true,
                "filterable": true,
                "retrievable": true,
                "sortable": true,
                "facetable": true,
                "key": true,
                "indexAnalyzer": null,
                "searchAnalyzer": null,
                "analyzer": null,
                "synonymMaps": []
            },
            {
                "name": "name",
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
      . . .
    ```

<a name="create-skillset"></a>

## <a name="create-a-skillset-and-knowledge-store"></a>Képességek készlete és indexmezők Tudásbázis tároló létrehozása

A [indexmezők API létrehozása](https://docs.microsoft.com/rest/api/searchservice/create-skillset) létrehoz egy Azure Search-objektumot, amely meghatározza, milyen kognitív képességeket szeretne hívásokat indítani, hogyan összekapcsolja a forgatókönyv - képességek együtt, és a legfontosabb ismeretek áruházbeli megadása.

Az a hívás végpontja `https://[service name].search.windows.net/skillsets?api-version=2019-05-06-Preview`

1. Cserélje le a `[service name]` elemet a keresési szolgáltatás nevére.

2. Másolja a képességek alkalmazási lehetőségét definíciót a Postman-gyűjteményben, a kérelemtörzsbe Képességcsoport létrehozása kérelemből. A képességek alkalmazási lehetőségét definíció több száz sor itt nyomtatása túl hosszú, de a lépéseknek az ismertetése, ez a forgatókönyv.

   A külső rendszerhéj, a képességek alkalmazási lehetőségét az alábbi elemekből áll. A `skills` gyűjteménye határozza meg a memórián belüli végrehajtott információbeolvasás, de a `knowledgeStore` definíció meghatározza a kimeneti módjára. A `cognitiveServices` definíciója a mesterséges Intelligencia Adatbővítés motorok létesített kapcsolat.

   ```json
   {
    "name": "caselaw-ss",
    "description": null,
    "skills": [],
    "cognitiveServices": [],
    "knowledgeStore": []
   }
   ```

3. Először állítsa be `cognitiveServices` és `knowledgeStore` kulcs és a kapcsolati karakterláncot. A példában ezek a karakterláncok találhatók a képességek alkalmazási lehetőségét definíciója után, a kérelem törzsében vége felé. Használja a Cognitive Services-erőforrás, az S0 csomag, Azure Search ugyanabban a régióban üzembe helyezve.

    ```json
    "cognitiveServices": {
        "@odata.type": "#Microsoft.Azure.Search.CognitiveServicesByKey",
        "description": "YOUR-SAME-REGION-S0-COGNITIVE-SERVICES-RESOURCE",
        "key": "YOUR-COGNITIVE-SERVICES-KEY"
    },
    "knowledgeStore": {
        "storageConnectionString": "YOUR-STORAGE-ACCOUNT-CONNECTION-STRING",
    ```

3. Tekintse át a képességek gyűjteményhez, különösen a 85-ös és 170, sorok Shaper ismeretek jelölik. A Shaper szakértelem fontos, mert azt a Tudásbázis adatbányászati használni kívánt adatstruktúrák tartalomkiszolgálójáról. Képességcsoport a futtatás során ezen szerkezetek memórián belüli csak, de helyezi át a következő lépés, mivel láthatja, hogyan menthetők Ez a kimenet további feltárási Tudásbázis áruházbeli.

   Az alábbi kódrészlet 217 sor van. 

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

3. Tekintse át a `projections` elemében `knowledgeStore`, már akár havi 262 sorban. Leképezések adja meg a Tudásbázis store összeállításban. Leképezések táblák-objektumok párok, de jelenleg csak egy időben vannak megadva. Amint láthatja, hogy az első leképezése a `tables` van megadva, de `objects` nem. A második ennek az ellenkezője.

   Az Azure storage-ban táblákat hoz létre minden egyes létrehozott tábla a Table storage-ban, és az egyes objektumok egy tároló beolvasása a Blob storage-ban.

   BLOB-objektumok általában a teljes kifejezés-felderítési bővítést tartalmaz. Táblák általában részleges végrehajtott információbeolvasás kombinációit, amelyek az adott célra rendezze el úgy a tartalmaznak. Ez a példa bemutatja egy esetekben és a vélemények tábla, de nem jelennek meg entitások, ügyvédi, bírák és fél hasonlóan más táblák.

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

5. Küldje el a kérést. A következő választ kell kapnia **201-es** és megjelenítése a válasz első része, a következő példához hasonlóan néz ki.

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

## <a name="create-and-run-an-indexer"></a>Hozzon létre, és a egy indexelő futtatása

A [indexelő API létrehozása](https://docs.microsoft.com/rest/api/searchservice/create-indexer) hoz létre, és azonnal végrehajtja az indexelő. Az ebben a lépésben a mozgásban lévő adatoknak egyaránt mindössze eddig létrehozott definíciókat. Az indexelő futása azonnal, mivel a szolgáltatás nem létezik. Ha létezik, a POST híváson meglévő indexelő egy frissítési művelet.

Az a hívás végpontja `https://[service name].search.windows.net/indexers?api-version=2019-05-06-Preview`

1. Cserélje le a `[service name]` elemet a keresési szolgáltatás nevére. 

2. Ez a hívás a kérés törzse megadja az indexelő nevét. Egy adatforrás és az index az indexelő van szükség. A képességek alkalmazási lehetőségét az indexelők nem kötelező, de AI Adatbővítés szükséges.

    ```json
    {
        "name": "caselaw-idxr",
        "description": null,
        "dataSourceName": "caselaw-ds",
        "skillsetName": "caselaw-ss",
        "targetIndexName": "caselaw",
        "disabled": null,
        "schedule": null,
        "parameters": {
            "batchSize": 1,
            "maxFailedItems": null,
            "maxFailedItemsPerBatch": null,
            "base64EncodeKeys": null,
            "configuration": {
                "parsingMode": "jsonLines"
            }
        },
        "fieldMappings": [],
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
    }
    ```

3. Küldje el a kérést. A következő választ kell kapnia **201-es** és a válasz törzsében (az áttekinthetőség vágott) a megadott kérelem tartalma majdnem azonos kell kinéznie.

    ```json
    {
        "name": "caselaw-idxr",
        "description": null,
        "dataSourceName": "caselaw-ds",
        "skillsetName": "caselaw-ss",
        "targetIndexName": "caselaw",
        "disabled": null,
        "schedule": null,
        "parameters": {
            "batchSize": 1,
            "maxFailedItems": null,
            "maxFailedItemsPerBatch": null,
            "base64EncodeKeys": null,
            "configuration": {
                "parsingMode": "jsonLines"
            }
        },
        "fieldMappings": [],
        "outputFieldMappings": [
            {
                "sourceFieldName": "/document/casebody/data/opinions/*/text/pages/*/people/*",
                "targetFieldName": "people",
                "mappingFunction": null
            }
        ]
    }
    ```

## <a name="explore-knowledge-store"></a>Tudásbázis store megismerése

Megkezdheti, amint az első dokumentum importálása. Ez a feladat [ **Tártallózó** ](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-storage-explorer) a portálon.

Fontos vegye figyelembe, hogy egy Tudásbázis store egy teljes körűen le lett választva, az Azure Search. Az Azure Search-index és a Tudásbázis store mindkét adatreprezentációt és tartalmát, de itt rész módon tartalmaznak. Az index használja a teljes szöveges keresés, a szűrt keresés és az összes, az az Azure Search támogatott forgatókönyveket. Másik lehetőségként folytatná csak a Tudásbázis store, elemezheti a tartalmak más eszközök csatlakoztatása a.

## <a name="takeaways"></a>Legfontosabb ismeretek

Most már létrehozta az első Tudásbázis store az Azure storage- és Storage Explorer segítségével megtekintheti a végrehajtott információbeolvasás. Ez a alapvető tárolt végrehajtott információbeolvasás használatának élményét. 

## <a name="next-steps"></a>További lépések

A Shaper szakértelem hajtja végre a feladatát, új alakzatok egyesíthetők részletes adatot űrlapok létrehozásával. A következő lépésben tekintse át a felhasználásukról részleteiért szakértelem referencia lapját.

> [!div class="nextstepaction"]
> [Shaper szakértelem referencia](cognitive-search-skill-shaper.md)


<!---
## Keep This

How to convert unformatted JSON into an indented JSON document structure that allows you to quickly identify nested structures. Useful for creating an index that includes complex types.

1. Use Visual Studio Code.
2. Open data.jsonl
--->