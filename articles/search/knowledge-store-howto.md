---
title: Hogyan beolvasni a Tudásbázis adatbányászati (előzetes verzió) – az Azure Search használatába
description: Ismerje meg a lépéseket, képi elemekben gazdag dokumentumok indexelése az Azure storage-fiókban Tudásbázis-áruházban az Azure Search folyamatok AI által létrehozott küldéséhez. Itt megtekintheti, formálja át, és felhasználását, képi elemekben gazdag dokumentumokat az Azure Search és az egyéb alkalmazásokban.
manager: cgronlun
author: HeidiSteen
services: search
ms.service: search
ms.topic: quickstart
ms.date: 06/29/2019
ms.author: heidist
ms.openlocfilehash: e50dfcdc5ac2fbe2435066546a340874e1b8f682
ms.sourcegitcommit: 978e1b8cac3da254f9d6309e0195c45b38c24eb5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/03/2019
ms.locfileid: "67551058"
---
# <a name="how-to-get-started-with-knowledge-mining-in-azure-search"></a>Tudásbázis adatbányászati az Azure Search az első lépések

> [!Note]
> Tudásbázis store előzetes állapotban van, nem éles használatra szánt. A [REST API verzióját 2019-05-06-Preview](search-api-preview.md) ezt a szolgáltatást biztosít. Rendszer jelenleg nem .NET SDK-t támogatja.
>
[Tudásbázis store](knowledge-store-concept-intro.md) alsóbb rétegbeli Tudásbázis adatbányászati más alkalmazásokban az Azure storage-fiók az indexelés során létrehozott AI bővített dokumentumok menti. Mentett végrehajtott információbeolvasás segítségével megismerheti és finomítsa az Azure Search indexelési folyamat. 

Határozza meg a Tudásbázis-tároló egy *indexmezők* hozta létre, és egy *indexelő*. A fizikai kifejezés a Tudásbázis-tárolók megadva a *leképezések* amelyek meghatározzák, hogy az adattárakon storage-ban. Ez a forgatókönyv befejezése időpontig létrejönnek az összes ilyen objektum, és tudni fogja, hogyan mindannyian működnek együtt. 

Ebben a gyakorlatban a mintaadatokat, szolgáltatások és az alapvető munkafolyamat létrehozásáról és használatáról az első Tudásbázis store hangsúlyt fektetve indexmezők definíció további eszközök elindítása.

## <a name="prerequisites"></a>Előfeltételek

Tudásbázis tároló több szolgáltatást, az Azure Blob storage és Azure Table storage fizikai tároló és az Azure Search és objektumok létrehozása és a Cognitive Services középpontjában van. Való ismerkedés során bizonyulhat a [alapszintű architektúra](knowledge-store-concept-intro.md) Ez a forgatókönyv előfeltétele.

Ez a rövid útmutató a következő szolgáltatásokat és eszközöket használatosak. 

+ [Postman asztali alkalmazás](https://www.getpostman.com/), az Azure Search HTTP-kérelmek küldéséhez használt.

+ [Az Azure storage-fiók létrehozása](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account) mintaadatokat és az ismeretek tárolására tárolja. A Tudásbázis-tárolót az Azure storage fogja szerepel.

+ [Cognitive Services-erőforrás létrehozása](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) rétegben az S0 használatalapú AI végrehajtott információbeolvasás használt képességek széles broad-spectrum eléréséhez. Cognitive Services és az Azure Search szolgáltatás ugyanabban a régióban kell szükségesek.

+ [Az Azure Search szolgáltatás létrehozása](search-create-service-portal.md) vagy [keresse meg a meglévő service](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) az aktuális előfizetésben. Ebben az oktatóanyagban egy ingyenes szolgáltatás használhatja. 

Példa JSON-dokumentumokat és a egy Postman collection-fájl is szükségesek. Utasítások megkeresése és kiegészítő fájlok betöltése megtalálható a [mintaadatok létrehozása](#prepare-sample-data) szakaszban.

## <a name="get-a-key-and-url"></a>Egy kulcsot és egy URL-cím beszerzése

A REST-hívásokhoz minden kérésének tartalmaznia kell a szolgáltatás URL-címét és egy hozzáférési kulcsot. Mindkettőhöz létrejön egy keresési szolgáltatás, így ha hozzáadta az előfizetéséhez az Azure Searchöt, kövesse az alábbi lépéseket a szükséges információk beszerzéséhez:

1. [Jelentkezzen be az Azure Portalon](https://portal.azure.com/), és a search szolgáltatás **áttekintése** lapon, az URL-cím lekéréséhez. A végpontok például a következőképpen nézhetnek ki: `https://mydemo.search.windows.net`.

1. A **beállítások** > **kulcsok**, a szolgáltatás a teljes körű rendszergazdai kulcs beszerzése. Nincsenek két felcserélhetők adminisztrációs kulcsot, az üzletmenet folytonosságának megadott abban az esetben egy vihető kell. Használható vagy az elsődleges vagy másodlagos kulcsot a kérések hozzáadása, módosítása és törlése objektumokat.

    ![Egy HTTP-végpontját és hozzáférési kulcs lekérése](media/search-get-started-postman/get-url-key.png "HTTP végpontját és hozzáférési kulcs beszerzése")

Minden kérelemhez szükséges halasztása minden kérelemnél a szolgáltatásnak küldött api-kulcsát. A szolgáltatásnév és API-kulcs az egyes HTTP-kérés a következő szakaszokban képzésben.

<a name="prepare-sample-data"></a>

## <a name="prepare-sample-data"></a>Mintaadatok létrehozása

A Tudásbázis-felderítési bővítést folyamat a kimeneti tárolóban. Bemenetek "használhatatlan" adatokat, amely végső soron válik "használható" ahogy előrehaladunk az a folyamat állnak. Használhatatlan adatokat lehet például szöveg vagy kép jellemzők elemezni kell képfájlokat, vagy sűrű szöveges fájlok, amelyeket elemezhetők az entitásokat, kulcskifejezéseket és vélemények. 

Ebben a gyakorlatban sűrű szöveges fájlok (eseti információk) származik a [Caselaw hozzáférés projekt](https://case.law/bulk/download/) nyilvános adatok kötegelt letöltési oldalát. 10-dokumentum minta azt feltölteni a Githubra, ehhez a gyakorlathoz. 

Ebben a feladatban egy Azure Blob-tárolóba, az ezeket a dokumentumokat, a folyamat bemeneti adatokként fogja létrehozni. 

1. Töltse le és csomagolja ki a [Azure Search-mintaadatok](https://github.com/Azure-Samples/azure-search-sample-data/tree/master/caselaw) get-adattár a [Caselaw adatkészlet](https://github.com/Azure-Samples/azure-search-sample-data/tree/master/caselaw). 

1. [Jelentkezzen be az Azure Portalon](https://portal.azure.com)lépjen az Azure storage-fiókot, kattintson a **Blobok**, és kattintson a **+ tároló**.

1. [Hozzon létre egy blobtárolót](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal) a mintaadatokat tartalmazzák: 

   1. A tároló neve `caselaw-test`. 
   
   1. Nyilvános hozzáférési szint beállítása az érvényes értékeket.

1. A tároló létrehozása után nyissa meg és jelölje ki **feltöltése** a parancssávon.

   ![Töltse fel a parancssávon](media/search-semi-structured-data/upload-command-bar.png "parancssávon feltöltése")

1. Keresse meg a mappát tartalmazó a **caselaw-sample.json** mintafájlt. Válassza ki a fájlt, és kattintson a **feltöltése**.

1. Amíg az Azure storage-ban, lekérése a kapcsolati karakterlánc és a tároló nevét.  Ezek a karakterláncok mindkét kell [adatforrás létrehozása](#create-data-source):

   1. Az Áttekintés lapon kattintson a **Tárelérési kulcsok** , és másolja a *kapcsolati karakterlánc*. Először `DefaultEndpointsProtocol=https;` és a folyamat végén `EndpointSuffix=core.windows.net`. A fiók nevét és kulcsát vannak a kettő között. 

   1. A tároló nevének kell lennie `caselaw-test` vagy tetszőleges rendelve.



## <a name="set-up-postman"></a>A Postman beállítása

Postman a kérelmek és a JSON-dokumentumokat az Azure Search küldéséhez használni kívánt ügyfélalkalmazás. A kérések számos szintaxisok segítségével állíthatja csak a szükséges információkat ebben a cikkben. Azonban két, a legnagyobb kérelmek (az index létrehozása egy képességcsoport létrehozása) tartalmazza a részletes JSON-t ágyazhat be egy cikkben túl nagy. 

Ahhoz, hogy minden JSON-dokumentumok és kérelmek teljes mértékben rendelkezésre állnak, létrehozott egy Postman collection-fájl. Töltsön le, és ezt a fájlt, majd importálásával az első lépése a az ügyfél beállítása.

1. Töltse le és csomagolja ki a [minták az Azure Search Postman](https://github.com/Azure-Samples/azure-search-postman-samples) tárház.

1. Indítsa el a Postmant, és a Caselaw Postman-gyűjtemény importálása:

   1. Kattintson a **importálás** > **fájlok importálása a** > **fájlok kiválasztása**. 

   1. Keresse meg a \azure-search-postman-samples-master\azure-search-postman-samples-master\Caselaw mappát.

   1. Válassza ki **Caselaw.postman_collection_v2.json**. Négy kell megjelennie **POST** kéréseket a gyűjteményben.

   ![Postman-gyűjtemény Caselaw bemutató](media/knowledge-store-howto/postman-collection.png "Caselaw bemutató Postman-gyűjtemény")
   

## <a name="create-an-index"></a>Index létrehozása
    
Az első kérelem használja a [Index API létrehozása](https://docs.microsoft.com/rest/api/searchservice/create-data-source), az Azure Search-index létrehozása, amely tárolja az összes kereshető adatot. Az index határozza meg, az összes mező, paraméterek és attribútumok.

Nem feltétlenül kell a Tudásbázis adatbányászati index, de az indexelő nem fut, kivéve, ha az index biztosítja. 

1. Az URL-címben `https://YOUR-AZURE-SEARCH-SERVICE-NAME.search.windows.net/indexes?api-version=2019-05-06-Preview`, cserélje le `YOUR-AZURE-SEARCH-SERVICE-NAME` a keresési szolgáltatás nevére. 

1. Cserélje le a fejléc részen `<YOUR AZURE SEARCH ADMIN API-KEY>` az Azure Search API rendszergazdai kulcsot.

1. A szervezet a szakaszban a JSON-dokumentum az indexsémát. A külső felület az index áll kibontva látható-e a, a következő elemeket. A mezők gyűjteménye a caselaw adatkészlet mezőinek felel meg.

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

1. Bontsa ki a `fields` gyűjtemény. Tartalmazza az indexdefiníciót mikroszolgáltatásokból álló, egyszerű mezők tömeges [összetett mezők](search-howto-complex-data-types.md) beágyazott alépítményeit és gyűjteményeket.

   Szánjon egy kis időt a mező definíciója tekintse át a `casebody` sorok 302-384-et összetett mezőjében. Figyelje meg, hogy egy összetett mezőt tartalmazhat más összetett mezők, ha a hierarchikus reprezentációinak van szükség. Hierarchikus struktúra modellezhető index, a képességek alkalmazási lehetőségét, így létrehozását egy beágyazott adatszerkezetből a Tudásbázis tárolójában ide, és emellett egy leképezési látható módon.

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

1. Kattintson a **küldése** a kérés végrehajtásához.  Meg kell kapnia egy **állapota: 201 Created** üzenet adott válaszként.

<a name="create-data-source"></a>

## <a name="create-a-data-source"></a>Adatforrás létrehozása

A második kérés használja a [Data Source API létrehozása](https://docs.microsoft.com/rest/api/searchservice/create-data-source) szeretne csatlakozni az Azure Blob storage. 

1. Az URL-címben `https://YOUR-AZURE-SEARCH-SERVICE-NAME.search.windows.net/datasources?api-version=2019-05-06-Preview`, cserélje le `YOUR-AZURE-SEARCH-SERVICE-NAME` a keresési szolgáltatás nevére. 

1. Cserélje le a fejléc részen `<YOUR AZURE SEARCH ADMIN API-KEY>` az Azure Search API rendszergazdai kulcsot.

1. A szervezet a szakaszban a JSON-dokumentum tartalmazza a tárfiók kapcsolati karakterláncot, és a blob tároló nevét. A kapcsolati karakterlánc található a tárfiók található az Azure Portalon **Tárelérési kulcsok**. 

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

1. Kattintson a **küldése** a kérés végrehajtásához.  Meg kell kapnia egy **állapota: 201 Created** üzenet adott válaszként.



<a name="create-skillset"></a>

## <a name="create-a-skillset-and-knowledge-store"></a>Képességek készlete és indexmezők Tudásbázis tároló létrehozása

A harmadik kérelem a [indexmezők API létrehozása](https://docs.microsoft.com/rest/api/searchservice/create-skillset), milyen kognitív képességeket szeretne hívásokat indítani, hogyan összekapcsolja a forgatókönyv - képességek együtt, és a legfontosabb létrehozása az Azure Search-objektum, amely meghatározza egy Tudásbázis tár megadása.

1. Az URL-címben `https://YOUR-AZURE-SEARCH-SERVICE-NAME.search.windows.net/skillsets?api-version=2019-05-06-Preview`, cserélje le `YOUR-AZURE-SEARCH-SERVICE-NAME` a keresési szolgáltatás nevére. 

1. Cserélje le a fejléc részen `<YOUR AZURE SEARCH ADMIN API-KEY>` az Azure Search API rendszergazdai kulcsot.

1. A szervezet a szakaszban a JSON-dokumentumok esetében indexmezők definícióját. Összecsukott számára látható-e, a külső rendszerhéj, a képességek alkalmazási lehetőségét az alábbi elemekből áll. A `skills` gyűjteménye határozza meg a memórián belüli végrehajtott információbeolvasás, de a `knowledgeStore` definíció meghatározza a kimeneti módjára. A `cognitiveServices` definíciója a mesterséges Intelligencia Adatbővítés motorok létesített kapcsolat.

   ```json
   {
    "name": "caselaw-ss",
    "description": null,
    "skills": [],
    "cognitiveServices": [],
    "knowledgeStore": []
   }
   ```

1. Bontsa ki a `cognitiveServices` és `knowledgeStore` , hogy a kapcsolatadatok is. A példában ezek a karakterláncok találhatók a képességek alkalmazási lehetőségét definíciója után, a kérelem törzsében vége felé. 

   A `cognitiveServices`, üzembe helyezése egy erőforráshoz, az S0 csomag, az Azure Search megegyező régióban található. A cognitive Services nevével és kulcsával érheti el ugyanazon az oldalon az Azure Portalon. 
   
   A `knowledgeStore`, használhatja ugyanazt a caselaw Blob tároló használt kapcsolati karakterlánc.

    ```json
    "cognitiveServices": {
        "@odata.type": "#Microsoft.Azure.Search.CognitiveServicesByKey",
        "description": "YOUR-SAME-REGION-S0-COGNITIVE-SERVICES-RESOURCE",
        "key": "YOUR-COGNITIVE-SERVICES-KEY"
    },
    "knowledgeStore": {
        "storageConnectionString": "YOUR-STORAGE-ACCOUNT-CONNECTION-STRING",
    ```

1. Bontsa ki a képességek gyűjteményt, különösen a 85-ös és 179, sorok Shaper ismeretek jelölik. A Shaper szakértelem fontos, mert azt a Tudásbázis adatbányászati használni kívánt adatstruktúrák tartalomkiszolgálójáról. Képességcsoport a futtatás során ezen szerkezetek memórián belüli csak, de helyezi át a következő lépés, mivel láthatja, hogyan menthetők Ez a kimenet további feltárási Tudásbázis áruházbeli.

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

1. Bontsa ki a `projections` elemében `knowledgeStore`, már akár havi 262 sorban. Leképezések adja meg a Tudásbázis store összeállításban. Leképezések táblák-objektumok párok, de jelenleg csak egy időben vannak megadva. Amint láthatja, hogy az első leképezése a `tables` van megadva, de `objects` nem. A második ennek az ellenkezője.

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

1. Kattintson a **küldése** a kérés végrehajtásához. A következő választ kell kapnia **201-es** és megjelenítése a válasz első része, a következő példához hasonlóan néz ki.

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

A negyedik kérelem a [indexelő API létrehozása](https://docs.microsoft.com/rest/api/searchservice/create-indexer), az Azure Search indexelők létrehozása. Az indexelő a végrehajtó motor az indexelési folyamat. Az ebben a lépésben a mozgásban lévő adatoknak egyaránt mindössze eddig létrehozott definíciókat.

1. Az URL-címben `https://YOUR-AZURE-SEARCH-SERVICE-NAME.search.windows.net/indexers?api-version=2019-05-06-Preview`, cserélje le `YOUR-AZURE-SEARCH-SERVICE-NAME` a keresési szolgáltatás nevére. 

1. Cserélje le a fejléc részen `<YOUR AZURE SEARCH ADMIN API-KEY>` az Azure Search API rendszergazdai kulcsot.

1. A szervezet a szakaszban a JSON-dokumentum megadja az indexelő nevét. Egy adatforrás és az index az indexelő van szükség. A képességek alkalmazási lehetőségét az indexelők nem kötelező, de AI Adatbővítés szükséges.

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

1. Bontsa ki a outputFieldMappings. FieldMappings, egyéni leképezés egy adatforrásban lévő mező és a egy index mezői között használt, ellentétben a outputFieldMappings képi elemekben gazdag mezők leképezése használt, létrehozni és kitölti a rendszer a folyamat a kimeneti mezőkre index vagy leképezése.

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

1. Kattintson a **küldése** a kérés végrehajtásához. A következő választ kell kapnia **201-es** és a válasz törzsében (az áttekinthetőség vágott) a megadott kérelem tartalma majdnem azonos kell kinéznie.

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
