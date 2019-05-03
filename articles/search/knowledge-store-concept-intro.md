---
title: Tudásbázis Store bemutatása és áttekintése – Azure Search
description: Továbbfejlesztett dokumentumokat küldeni az Azure storage, ahol megtekintheti, formálja át és felhasználását képi elemekben gazdag dokumentumokat az Azure Search és az egyéb alkalmazásokban.
manager: cgronlun
author: HeidiSteen
services: search
ms.service: search
ms.devlang: NA
ms.topic: overview
ms.date: 05/02/2019
ms.author: heidist
ms.openlocfilehash: 3000016de934aaa3faab96821f9747ea4b571ef7
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/02/2019
ms.locfileid: "65026997"
---
# <a name="what-is-knowledge-store-in-azure-search"></a>Mit jelent a Tudásbázis Store az Azure Search?

Tudásbázis Store az Azure Search szolgáltatásban választható szolgáltatás jelenleg nyilvános előzetes verzióban, amely menti a jelentéstétellel dokumentumok és metaadat-mesterséges intelligencián alapuló indexelése folyamat által létrehozott [(cognitive search)](cognitive-search-concept-intro.md). Tudásbázis Store alapját a folyamat részeként konfigurálnia az Azure storage-fiókhoz. Ha engedélyezve van, a keresési szolgáltatás ezt a tárfiókot minden egyes képi elemekben gazdag dokumentum reprezentációját gyorsítótárazásához használja. 

A kognitív keresés korábban már használta, ha már tudja, hogy használható-e a szakértelmével áthelyezése egy dokumentumot keresztül végrehajtott információbeolvasás sorozata. Az eredménye lehet az Azure Search-index, vagy (új az előzetes verzió) leképezések a Tudásbázis-tárolóban.

Leképezések a mechanizmus egy alsóbb rétegbeli alkalmazás felhasználásra adatok rendszerezéséhez. Használhat [Tártallózó](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer?tabs=windows) az Azure storage, vagy minden olyan alkalmazás, amely csatlakozik az Azure storage számára készült, amelyek új lehetőségeket felhasználásához nyit bővített dokumentumokat. Néhány példa olyan adatelemzési folyamatok és egyéni elemzési.

![Adatfolyamat ábrája a Tudásbázis Store](./media/knowledge-store-concept-intro/annotationstore_sans_internalcache.png "Tudásbázis Store a adatfolyamat ábrája")

Tudásbázis Store használatához adjon hozzá egy `knowledgeStore` elem a képességek alkalmazási lehetőségét, amely meghatározza a többlépcsős műveletek egy indexelési folyamat. A futtatás során az Azure Search létrehoz egy tárolóhelyet az Azure storage-fiókban, és a definíciók és a folyamat által létrehozott tartalom alapján tölti ki.

## <a name="benefits-of-knowledge-store"></a>Tudásbázis Store előnyei

A Tudásbázis biztosít, struktúra, a környezet és a tényleges tartalom - származó strukturálatlan és részben strukturált adatok fájlok, blobok, képfájlokat, amelyek az elemzés, például adatokat, vagy akár strukturált, amely az új űrlapok átalakítja az adatokat tárolja. Az egy [lépésenkénti útmutató](knowledge-store-howto.md) első kézből JSON-dokumentumok sűrű alépítményeit, új struktúrákat, az elkészített particionálva és más módon elérhetővé vált a aktiválásához hogyan látható írt ebben az előzetes verzióban, folyamatok például a machine learning és az adatok adatelemzési számítási feladatokhoz.

Bár kíváncsi a mesterséges intelligencián alapuló indexelése folyamat eredményezhet, a Tudásbázis Store előnye rendszer azon képessége, hogy alakítsa át az adatokat. Kezdje egy egyszerű indexmezők előfordulhat, hogy, és adja hozzá a struktúra, amely új struktúrákat, fogyóeszközök más alkalmazásokban az Azure Search mellett, majd kombinálhatja növekvő mértékű majd megismételheti.

A felsorolt, Tudásbázis Store előnyei a következők:

+ A képi elemekben gazdag dokumentumok felhasználására [elemzési és jelentéskészítési eszközökkel](#tools-and-apps) keresési eltérő. A Power BI a Power Query kényszerítő közül választhat, de bármely eszköz vagy alkalmazás, amely képes kapcsolódni az Azure storage által létrehozott Tudásbázis áruházbeli kérheti le.

+ Az AI-indexelési folyamat finomíthatja a lépéseket és indexmezők definíciók hibakeresése során. Tudásbázis áruházbeli bemutatja, a termék egy indexmezők definíció mesterséges indexeléshez folyamatban. Használhatja ezeket az eredményeket egy jobb indexmezők tervezhet, mivel láthatja, hogy pontosan a végrehajtott információbeolvasás kinézni. Használhat [Tártallózó](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer?tabs=windows) egy Tudásbázis tároló tartalmának megtekintése az Azure storage-ban.

+ Az adatok formázása az új űrlapok. A átalakításakor van szerkezetbe, az ismereteket, de a pont, a képességek alkalmazási lehetőségét most már megadhat ezt a funkciót. A [Shaper szakértelem](cognitive-search-skill-shaper.md) az Azure Search most már elérhető ez a feladat megvalósításához. Átalakításakor lehetővé teszi, hogy definiálja, amely igazodik a felhasználás céljának az adatok megőrzése mellett a kapcsolatokat a leképezés.

> [!Note]
> Nem ismeri a mesterséges intelligencián alapuló indexelése a Cognitive Services használatával? Az Azure Search kinyeréséhez és az optikai karakterfelismerés (OCR) képfájlokat, entitások felismerése és kulcsszókeresés származó szöveges fájlok és több adatforrás adatokat feldúsítani a Cognitive Services Látástechnológia, és a nyelvi funkciók integrálható. További információkért lásd: [Mi a cognitive search?](cognitive-search-concept-intro.md).

## <a name="create-a-knowledge-store"></a>A Tudásbázis-tároló létrehozása

Tudásbázis áruházbeli egy indexmezők definíció részét képezi. Az előzetes verzióhoz, hozza létre igényel a REST API használatával `api-version=2019-05-06-Preview` vagy a **adatimportálás** varázsló a portálon.

A következő JSON adja meg egy `knowledgeStore`, amely része a képességek alkalmazási lehetőségét, amelyet az indexelő (nem látható) által. Leképezések belül specifikációját a `knowledgeStore` meghatározza, hogy e táblákat vagy objektumok jönnek létre az Azure storage-ban.

Ha már ismeri a mesterséges intelligencián alapuló indexelést, a képességek alkalmazási lehetőségét definíciója a létrehozása, a szervezet és a jelentéstétellel dokumentum anyag határozza meg.

```json
{
  "name": "my-new-skillset",
  "description": 
  "Example showing knowledgeStore placement, supported in api-version=2019-05-06-Preview. You need at least one skill, most likely a Shaper skill if you are modulating data structures.",
  "skills":
  [
    {
    "@odata.type": "#Microsoft.Skills.Util.ShaperSkill",
    "context": "/document/content/phrases/*",
    "inputs": [
        {
        "name": "text",
        "source": "/document/content/phrases/*"
        },
        {
        "name": "sentiment",
        "source": "/document/content/phrases/*/sentiment"
        }
    ],
    "outputs": [
        {
        "name": "output",
        "targetName": "analyzedText"
        }
    ]
    },
  ],
  "cognitiveServices": 
    {
    "@odata.type": "#Microsoft.Azure.Search.CognitiveServicesByKey",
    "description": "mycogsvcs resource in West US 2",
    "key": "<your key goes here>"
    },
  "knowledgeStore": { 
    "storageConnectionString": "<your connection string goes here>", 
    "projections": [ 
        { 
            "tables": [  
            { "tableName": "Reviews", "generatedKeyName": "ReviewId", "source": "/document/Review" , "sourceContext": null, "inputs": []}, 
            { "tableName": "Sentences", "generatedKeyName": "SentenceId", "source": "/document/Review/Sentences/*", "sourceContext": null, "inputs": []}, 
            { "tableName": "KeyPhrases", "generatedKeyName": "KeyPhraseId", "source": "/document/Review/Sentences/*/KeyPhrases", "sourceContext": null, "inputs": []}, 
            { "tableName": "Entities", "generatedKeyName": "EntityId", "source": "/document/Review/Sentences/*/Entities/*" ,"sourceContext": null, "inputs": []} 

            ], 
            "objects": [ 
                { 
                "storageContainer": "Reviews", 
                "format": "json", 
                "source": "/document/Review", 
                "key": "/document/Review/Id" 
                } 
            ]      
        }    
    ]     
    } 
}
```

## <a name="components-backing-a-knowledge-store"></a>A Tudásbázis store biztonsági összetevők

Hozzon létre egy Tudásbázis-tárolót, a következő szolgáltatásokat és összetevőket kell.

### <a name="1---source-data"></a>1 – forrásadatok

Az adatok vagy a bővíteni kívánt dokumentumokat léteznie kell egy Azure Search-indexelők által támogatott Azure-adatforrás: 

* [Azure SQL](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md)

* [Azure Cosmos DB](search-howto-index-cosmosdb.md)

* [Azure Blob Storage](search-howto-indexing-azure-blob-storage.md)

[Az Azure Table storage](search-howto-indexing-azure-tables.md) kimenő adatokat a Tudásbázis-tárolóban is használható, de nem használható erőforrás-mesterséges intelligencián alapuló indexelése folyamat bejövő adatok.

### <a name="2---azure-search-service"></a>2 – azure Search szolgáltatás

Az Azure Search szolgáltatást, és a REST API létrehozása és konfigurálása kibővített adatok használt objektumok is szükséges. A REST API létrehozásához egy Tudásbázis store `api-version=2019-05-06-Preview`.

Az Azure Search biztosít az indexelő funkció, és az indexelők használhatók fel a teljes folyamat-végpontok, az Azure-tárolóban megőrzött, képi elemekben gazdag dokumentumok eredményez. Indexelők használata egy adatforrást, egy index és egy indexmezők –, amelyek mindegyike létrehozása és a egy Tudásbázis-tároló feltöltése.

| Object | REST API | Leírás |
|--------|----------|-------------|
| adatforrás | [Adatforrás létrehozása](https://docs.microsoft.com/rest/api/searchservice/create-data-source)  | Egy erőforrás, képi elemekben gazdag dokumentumok létrehozásához használt forrás-adatokat biztosítva külső Azure adatforrás azonosítása.  |
| Képességcsoport | [Képességcsoport létrehozása (api-version = a 2019-05-06)](https://docs.microsoft.com/rest/api/searchservice/create-skillset)  | Koordinációs használatát erőforrás [beépített képességek](cognitive-search-predefined-skills.md) és [egyéni kognitív képességeket](cognitive-search-custom-skill-interface.md) Adatbővítés folyamatban az indexelés során használt. |
| index | [Index létrehozása](https://docs.microsoft.com/rest/api/searchservice/create-index)  | A séma megadása az Azure Search-index. Mezőkre az indexben képezze le a forrásadatok mezők vagy előállított felderítési bővítést fázisában (például egy mezőt az entitások felismerése által létrehozott szervezet neve) mezőt. |
| indexelő | [Indexelő létrehozása (api-version = a 2019-05-06)](https://docs.microsoft.com/rest/api/searchservice/create-skillset)  | Az indexelés során használt összetevőket meghatározása erőforrás: többek között egy adatforrást, a képességek alkalmazási lehetőségét, mező társítást a forrás- és a köztes adatokat struktúrák célindex és magát az index. Az indexelő futtatása az adatfeldolgozás és a felderítési bővítést az eseményindító. A kimenet a szakértelmével keresztül bővített adatforrás adatokkal feltöltve az indexséma alapuló keresési indexet.  |

### <a name="3---cognitive-services"></a>3 – cognitive Services

A képességek alkalmazási lehetőségét megadott végrehajtott információbeolvasás a Computer Vision és nyelvi funkciókat a Cognitive Services alapulnak. Cognitive Services-funkció révén a képességek alkalmazási lehetőségét az indexelés során szolgáltatást veszi igénybe. A képességek alkalmazási lehetőségét képességek áll, és képességek a számítógépes Látástechnológiai és nyelvi funkciók vannak kötve. A Cognitive Services integrálása fog [Cognitive Services-erőforrás csatolása](cognitive-search-attach-cognitive-services.md) , a képességek alkalmazási lehetőségét.

### <a name="4---storage-account"></a>4 – storage-fiók

Az Azure Storage-fiókban az Azure Search egy Blob-tárolóba vagy a táblák, attól függően, hogy hogyan konfigurálhat egy indexmezők hoz létre. Ha az adatok Azure Blob vagy Table storage-ból származik, akkor már be. Ellenkező esetben szüksége lesz egy Azure storage-fiók létrehozása. Táblázatok és objektumok az Azure storage-ban a képi elemekben gazdag dokumentumokat, a mesterséges intelligencián alapuló indexelési folyamat által létrehozott tartalmaznak.

A storage-fiók van megadva a képességek alkalmazási lehetőségét. A `api-version=2019-05-06-Preview`, a indexmezők-definíció Tudásbázis store definícióját tartalmazza, így megadhatja a fiók adatait.

<a name="tools-and-apps"></a>

### <a name="5---access-and-consume"></a>5 – elérése és felhasználása

Miután a végrehajtott információbeolvasás létezik, a tárolás, bármely eszköz vagy technológia, amely csatlakozik az Azure Blob vagy Table storage segítségével ismerje meg, elemzése, vagy a tartalom felhasználásához. Az alábbi lista a Kezdés:

+ [Storage Explorer](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer?tabs=windows) képi elemekben gazdag dokumentum szerkezete és a tartalom megtekintéséhez. Megfontolandó szempontok a referenciakonfiguráció eszközként Tudásbázis tároló tartalmának megtekintéséhez.

+ [Power bi-ban a Power Query](https://support.office.com/article/connect-to-microsoft-azure-blob-storage-power-query-f8165faa-4589-47b1-86b6-7015b330d13e) a természetes nyelvű lekérdezések, vagy használja a jelentéskészítési és elemzési eszközök ha numerikus adatokat.

+ [Az Azure Data Factory](https://docs.microsoft.com/azure/data-factory/) további kezelésével kapcsolatos.

+ Az Azure Search-index használatával indexelt, hogy tartalomhoz teljes szöveges keresés [cognitive search](cognitive-search-concept-intro.md).

## <a name="document-persistence"></a>A dokumentum adatmegőrzés

A storage-fiókon belül a végrehajtott információbeolvasás lehet kifejezni táblák az Azure Table Storage vagy az Azure Blob storage-objektumokat. A visszaírási, hogy végrehajtott információbeolvasás, egyszer tárolja, használhatók-e forrásként adatokat tölthet be más adatbázisok és eszközök

+ A TABLE storage akkor hasznos, ha azt szeretné, egy séma-kompatibilis ábrázolása az adatok táblázatos formában. Ha azt szeretné, formálja át, vagy kombinálhatja az elemet új módon, a Table storage lehetővé teszi a szükséges granularitási.

+ A BLOB storage hoz létre egy átfogó JSON-reprezentációval a dokumentumot. Egy indexmezők mindkét tárolási lehetőség használatával széles skáláját kifejezések kigyűjtése.

+ Az Azure Search továbbra is fennáll, egy index tartalmát. Ha a forgatókönyv nem search-kapcsolódó, például ha a cél az, egy másik eszköz elemzéseket végezhet, törölheti az index, amely a folyamat hoz létre. De is képes tartani az index, és használja egy beépített eszköz, például [keresési ablak](search-explorer.md) a harmadik közepes (mögött a Storage Explorerrel és az analytics alkalmazás) a tartalom való interakcióhoz.

Dokumentum tartalmát, valamint képi elemekben gazdag dokumentumok a metaadatok, a képességek alkalmazási lehetőségét verzió, amely a végrehajtott információbeolvasás előállított közé tartozik.  

## <a name="inside-a-knowledge-store"></a>Tudásbázis áruházbeli belül

A Tudásbázis-tároló egy jegyzet gyorsítótár és leképezések áll. A *gyorsítótár* a szolgáltatás belsőleg használt gyorsítótár képességek eredményeinek és változásainak követése. A *leképezése* a séma és a végrehajtott információbeolvasás, amelyek megfelelnek a felhasználás céljának struktúráját határozza meg. Nincs ismeret üzletenként egy gyorsítótár, de több leképezések. 

A gyorsítótár nem mindig egy blob-tárolóba, de leképezések is csuklós, táblák vagy objektumok:

+ A leképezés olyan objektum, a Blob storage, ahol a leképezés egy tárolót, amelyen belül az objektumok vagy hierarchikus semmilyen felelősséget a JSON-ban egy adatelemzési folyamatok például a mentett képezi le.

+ A leképezés táblázatként, a Table storage képezi le. Táblázatos szimbolizáló kapcsolatok, például a adatelemzés vagy az exportálás, a machine Learning adatkeretek megőrzi. A képi elemekben gazdag leképezések majd lehet könnyen importálja más adattárakban. 

Létrehozhat több leképezések a Tudásbázis-tárolóban, a szervezet különböző választókerületeket befogadásához. A fejlesztő előfordulhat, hogy hozzá kell férniük a teljes JSON-reprezentációja az entitásokkal dokumentum közben az adatszakértők vagy adatelemzők érdemes részletes vagy moduláris datové struktury alakú által a képességek alkalmazási lehetőségét.

Például ha egyik célja a Adatbővítés folyamat is létre kell hoznia egy adatkészletet, a modell betanításához használja, az adatok kivetítésének az objektumtároló lehet használni az adatokat a data science folyamatok az egyik lehetőség. Azt is megteheti Ha azt szeretné, képi elemekben gazdag dokumentumok alapú gyors Power BI-irányítópult létrehozása a táblázatos leképezés is működne.

<!---
## Data lifecycle and billing

Each time you run the indexer, the cache in Azure storage is updated if the skillset definition or underlying source data has changed. As input documents are edited or deleted, changes are propagated through the annotation cache to the projections, ensuring that your projected data is a current representation of your inputs at the end of the indexer run. 

Generally speaking, pipeline processing can be an all-or-nothing operation, but Azure Search can process incremental changes, which saves you time and money.

If a document is new or updated, all skills are run. If only the skillset changes, reprocessing is scoped to just those skills and documents affected by your edit.

### Changes to a skillset
Suppose that you have a pipeline composed of multiple skills, operating over a large body of static data (for example, scanned documents), that takes 8 hours and costs $200 to create the knowledge store. Now suppose you need to tweak one of the skills in the skillset. Rather than starting over, Azure Search can determine which skill is affected, and reprocess only that skill. Cached data and projections that are unaffected by the change remain intact in the knowledge store.

### Changes in the data
Scenarios can vary considerably, but let's suppose instead of static data, you have volatile data that changes between indexer invocations. Given no changes to the skillset, you are charged for processing the delta of new and modified document. The timestamp information varies by data source, but for illustration, in a Blob container, Azure Search looks at the `lastmodified` date to determine which blobs need to be ingested.

> [!Note]
> While you can edit the data in the projections, any edits will be overwritten on the next pipeline invocation, assuming the document in source data is updated. 

### Deletions

Although Azure Search creates and updates structures and content in Azure storage, it does not delete them. Projections and cached documents continue to exist even when the skillset is deleted. As the owner of the storage account, you should delete a projection if it is no longer needed. 

### Tips for development

+ Start small with a representative sample of your data as you make significant changes to skillset composition. As your design finalizes, you can slowly add more data during later-stage development, and then roll in the entire data set when you are comfortable with the pipeline composition.

+ Retain control over indexer invocation. Indexers can run on a schedule, which is helpful for solutions that are rolled into production, but less helpful if you are actively developing your pipeline. During development, avoid schedules so that you don’t lose track of cache or projection state. Once your solution is in production and skillset composition is static, you can put the indexer on a schedule to pick up routine changes in the external source data. 

-->

## <a name="where-do-i-start"></a>Hogyan kezdjek hozzá?

Azt javasoljuk, az ingyenes szolgáltatás tanulási célokra, de vegye figyelembe, hogy az ingyenes tranzakciók száma / nap-előfizetésenként 20 dokumentumok korlátozódik.

Több szolgáltatás használatakor az összes szolgáltatását ugyanabban a régióban a legjobb teljesítmény és költségek minimalizálása érdekében hozzon létre. Sávszélesség megadása a bejövő és kimenő adatokat, amely egy másik szolgáltatásnak ugyanabban a régióban nem díjkötelesek.

**1. lépés: [Az Azure Search-erőforrás létrehozásához](search-create-service-portal.md)** 

**2. lépés: [Az Azure storage-fiók létrehozása](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account?tabs=azure-portal)** 

**3. lépés: [Cognitive Services-erőforrás létrehozása](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account)** 

**4. lépés: [A portál használatának első lépései](cognitive-search-quickstart-blob.md) – vagy – [mintaadatok REST és a Postman használatával – első lépések](knowledge-store-howto.md)** 

Használhatja a többi `api-version=2019-05-06-Preview` egy mesterséges intelligencián alapuló folyamatot, amely tartalmazza a Tudásbázis Store létrehozására. A legújabb előzetes verziója API, a képességek alkalmazási lehetőségét objektum tartalmazza a `knowledgeStore` definíciója.

## <a name="takeaways"></a>Legfontosabb ismeretek

Tudásbázis Store számos előnyt kínál, de nem kizárólag a jelentéstétellel dokumentumok keresési eltérő forgatókönyvekben használatát engedélyezi, vezérlők költség, és eltéréseket a Adatbővítés folyamat kezelése. Ezek a funkciók minden érhetők el egyszerűen egy storage-fiók hozzáadása a képességek alkalmazási lehetőségét, és a frissített kifejezés nyelv használatával leírtak szerint által használandó [hogyan kezdheti el a Tudásbázis Store](knowledge-store-howto.md). 

## <a name="next-steps"></a>További lépések

A képi elemekben gazdag dokumentumok létrehozásának legegyszerűbb keresztül a **adatimportálás** varázsló.

> [!div class="nextstepaction"]
> [Rövid útmutató: A portál az útmutató a kognitív keresés kipróbálása](cognitive-search-quickstart-blob.md)
