---
title: A Knowledge Store bemutatása (előzetes verzió) – Azure Search
description: Gazdagított dokumentumokat küldhet az Azure Storage-ba, ahol megtekintheti, átalakíthatja és felhasználhatja a Azure Search és más alkalmazásokban található dúsított dokumentumokat.
manager: nitinme
author: HeidiSteen
services: search
ms.service: search
ms.topic: overview
ms.date: 08/02/2019
ms.author: heidist
ms.openlocfilehash: b092c7251bc2a6794db36f8eaa279a7eeb931723
ms.sourcegitcommit: 6eecb9a71f8d69851bc962e2751971fccf29557f
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/17/2019
ms.locfileid: "72533779"
---
# <a name="what-is-knowledge-store-in-azure-search"></a>Mi az a Knowledge Store a Azure Searchban?

> [!Note]
> A Knowledge áruház előzetes verzióban érhető el, és nem éles használatra készült. A [REST API 2019-05-06-es verziójának előzetes verziója](search-api-preview.md) biztosítja ezt a funkciót. Jelenleg nincs .NET SDK-támogatás.
>

A Knowledge Store a Azure Search egyik funkciója, amely egy [mesterséges intelligencia](cognitive-search-concept-intro.md) -bővítési folyamat kimenetét őrzi meg későbbi elemzések vagy más alsóbb rétegbeli feldolgozás céljából. A bővített *dokumentum* egy olyan folyamat kimenete, amely a Cognitive Services erőforrásai által kinyert, strukturált és elemzett tartalomból lett létrehozva. A standard AI-alapú folyamatokban a dúsított dokumentumok átmenetiek, csak indexelés során használatosak, majd elvetették őket. A Knowledge Store-ban a dokumentumok más alkalmazásokban vagy az adatelemzési folyamatokban való használatra lesznek mentve. 

Ha korábban már használta a mesterséges intelligenciával Azure Search kapcsolatos ismereteket, már tudja, hogy a *szakértelmével* egy dokumentum a dúsítások sorozatából való áthelyezésére szolgál. Az eredmény lehet egy Azure Search index, vagy (ebben az előzetes verzióban új) vetítések egy Tudásbázisban. A két kimenet, a keresési index és a Knowledge Store, fizikailag különbözik egymástól. Ugyanazt a tartalmat használják, de nagyon különböző módokon vannak tárolva és használva.

Fizikailag a Knowledge Store egy [Azure Storage-fiók](https://docs.microsoft.com/azure/storage/common/storage-account-overview), amely az Azure Table Storage, az Azure Blob Storage vagy mindkettő, attól függően, hogy hogyan konfigurálja a folyamatot. Minden olyan eszköz vagy folyamat, amely tud csatlakozni egy Azure Storage-fiókhoz, felhasználhatja egy Tudásbázis tartalmát.

A kivetítések az adattárakban lévő adatstrukturálás mechanizmusa. A kivetítések segítségével például megadhatja, hogy a kimenet egyetlen blobként vagy kapcsolódó táblák gyűjteménye legyen mentve. Az Azure Storage beépített [Storage Explorerán](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer?tabs=windows) keresztül könnyedén megtekintheti a Knowledge Store-tartalmakat.

![Knowledge Store a folyamat ábráján](./media/knowledge-store-concept-intro/annotationstore_sans_internalcache.png "Knowledge Store a folyamat ábráján")

A Knowledge Store használatához adjon hozzá egy `knowledgeStore` elemet egy készségkészlet, amely az indexelési folyamat lépés-Wise műveleteit definiálja. A végrehajtás során Azure Search létrehoz egy helyet az Azure Storage-fiókban, és a dúsított dokumentumokat a folyamaton belül létrehozott definícióval.

## <a name="benefits-of-knowledge-store"></a>A Knowledge Store előnyei

A Knowledge Store strukturálatlan és félig strukturált adatfájlok, például a Blobok, az elemzésen átesett képfájlok, vagy akár az új űrlapokra átalakított strukturált adatok összegyűjtését teszi lehetővé. Az előzetes verzióra írt [lépésenkénti útmutatóban](knowledge-store-howto.md) megtekintheti, hogy a vastag JSON-dokumentumok hogyan vannak particionálva alstruktúrákba, új struktúrákba felépítve, és egyéb módon elérhetővé válnak az alárendelt folyamatokhoz, például a géphez tanulási és adatelemzési számítási feladatok.

Bár hasznos lehet megtekinteni, hogy mit hozhat létre a mesterséges intelligencia-alapú indexelési folyamat, a Knowledge Store valódi ereje az Adatátalakítási képesség. Érdemes lehet egy alapszintű készségkészlet, majd megismételni a szerkezet növelését, amelyet később új struktúrákba, más alkalmazásokban pedig a Azure Searchon kívül használhat.

Enumerált, a Knowledge Store előnyei a következők:

+ A kereséstől eltérő [elemzési és jelentéskészítő eszközökön](#tools-and-apps) a dúsított dokumentumokat használhatja. A Power BI a Power Query vonzó választás, de az Azure Storage-hoz csatlakoztatható bármely eszköz vagy alkalmazás lehívható a létrehozott Tudásbázisból.

+ Egy AI-indexelési folyamat pontosítása a lépések és a készségkészlet-definíciók hibakeresése közben. A Tudásbázis egy AI-indexelési folyamat készségkészlet-definíciójának termékét jeleníti meg. Ezeket az eredményeket a jobb készségkészlet kialakításához használhatja, mert láthatja, hogy pontosan mit néz ki a gazdagítás. Az Azure Storage [Storage Explorer](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer?tabs=windows) használatával megtekintheti a Tudásbázis tartalmát.

+ Az új űrlapokra alakítja az adathalmazt. Az átformálás a szakértelmével-ben kodifikált, de a lényeg az, hogy egy készségkészlet most már megadhatja ezt a képességet. A Azure Searchban lévő [formáló képesség](cognitive-search-skill-shaper.md) ki lett terjesztve a feladat befogadására. Az átformálás lehetővé teszi, hogy olyan leképezést határozzon meg, amely igazodik az adatfelhasználáshoz a kapcsolatok megőrzése mellett.

> [!Note]
> Nem ismeri a Cognitive Servicest használó AI-alapú indexelést? A Azure Search Cognitive Services jövőkép és nyelvi funkciók integrálásával kinyerheti és gazdagíthatja a forrásadatokat a képfájlok, az entitások felismerése és a szöveges fájlok kinyerése, valamint a szövegfájlokból való kivonások használatával. További információ: [Mi a kognitív keresés?](cognitive-search-concept-intro.md).

## <a name="creating-a-knowledge-store"></a>Tudásbázis létrehozása

A Tudásbázis egy [készségkészlet](cognitive-search-working-with-skillsets.md)része, amely viszont egy [Indexelő](search-indexer-overview.md)része. 

Ebben az előzetes verzióban létrehozhat egy tudásbázist a REST API és a `api-version=2019-05-06-Preview` használatával vagy a portál **adatimportálás** varázslójával.

### <a name="json-representation-of-a-knowledge-store"></a>A Knowledge Store JSON-ábrázolása

A következő JSON egy olyan `knowledgeStore`t határoz meg, amely egy készségkészlet része, amelyet egy indexelő hív meg (nem látható). Ha már ismeri a mesterséges intelligenciát, a készségkészlet határozza meg az egyes dúsított dokumentumok létrehozását, rendszerezését és tartalmát. A készségkészlet tartalmaznia kell legalább egy képességet, amely valószínűleg egy formáló képesség, ha az adatstruktúrákat modulálja.

A `knowledgeStore` kapcsolatokből és kivetítésből áll. 

+ A kapcsolódás egy olyan Storage-fiókhoz van, amely ugyanabban a régióban található, mint Azure Search. 

+ A vetítések Tables-Objects párok. `Tables` megadhatja a dúsított dokumentumok fizikai kifejezését az Azure Table Storage-ban. `Objects` adja meg a fizikai objektumokat az Azure Blob Storage-ban.

```json
{
  "name": "my-new-skillset",
  "description": "Example showing knowledgeStore placement in a skillset.",
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
               
            ]      
        },
        { 
            "tables": [ 
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

### <a name="sources-of-data-for-a-knowledge-store"></a>A Tudásbázisban tárolt adatforrások

Ha egy Tudásbázis kimenete egy mesterséges intelligencia-dúsítási folyamatból származik, milyen bemenetek vannak? A kinyerni, bővíteni és végül menteni kívánt eredeti adatok bármely, Azure Search indexelő által támogatott Azure-adatforrásból származhatnak: 

* [Azure Cosmos DB](search-howto-index-cosmosdb.md)

* [Azure Blob Storage](search-howto-indexing-azure-blob-storage.md)

* [Azure Table storage](search-howto-indexing-azure-tables.md)

* [Azure SQL](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md)

Az indexelő és a szakértelmével segítségével kinyerheti és gazdagíthatja vagy átalakíthatja ezt a tartalmat egy indexelési munkaterhelés részeként, majd mentheti az eredményeket egy tudásbázisba.

### <a name="rest-apis-used-in-creation-of-a-knowledge-store"></a>A Knowledge Store létrehozásakor használt REST API-k

Csak két API rendelkezik a Tudásbázis létrehozásához szükséges bővítményekkel (Készségkészlet létrehozása és indexelő létrehozása). Más API-k is használhatók.

| Objektum | REST API | Leírás |
|--------|----------|-------------|
| Adatforrás | [Adatforrás létrehozása](https://docs.microsoft.com/rest/api/searchservice/create-data-source)  | Egy külső Azure-adatforrást azonosító erőforrás, amely a dúsított dokumentumok létrehozásához használt forrásadatokat biztosít.  |
| készségkészlet | [Készségkészlet létrehozása (API-Version = 2019-05 -06-előzetes verzió)](https://docs.microsoft.com/rest/api/searchservice/create-skillset)  | Egy erőforrás, amely összehangolja a [beépített készségek](cognitive-search-predefined-skills.md) használatát és a dúsítási folyamat során használt [Egyéni kognitív képességeket](cognitive-search-custom-skill-interface.md) az indexelés során. A készségkészlet `knowledgeStore` definíciója alárendelt elem. |
| index | [Index létrehozása](https://docs.microsoft.com/rest/api/searchservice/create-index)  | Egy Azure Search indexet kifejező séma. Az indexben található mezők a forrásadatok mezőire vagy a dúsítási fázisban előállított mezőkre (például az entitások felismerése által létrehozott szervezeti nevekre vonatkozó mezőre) vonatkoznak. |
| Indexelő | [Indexelő létrehozása (API-Version = 2019-05-06)](https://docs.microsoft.com/rest/api/searchservice/create-skillset)  | Az indexelés során használt összetevőket meghatározó erőforrás: beleértve az adatforrást, a készségkészlet, a forrás-és a közbenső adatstruktúrákat a célként megadott indexbe, valamint magát az indexet. Az indexelő futtatása az adatfeldolgozáshoz és a dúsításhoz használt trigger. A kimenet egy olyan keresési index, amely az index sémán alapul, és a forrásadatok alapján van feltöltve, és a szakértelmével-n keresztül dúsított.  |

### <a name="physical-composition-of-a-knowledge-store"></a>A Tudásbázis fizikai összetétele

 A *kivetítés*, amely egy `knowledgeStore` definíció eleme, a kimenet sémáját és szerkezetét fogalmazza meg, hogy az megfeleljen a kívánt felhasználásnak. Több kivetítést is megadhat, ha olyan alkalmazásokkal rendelkezik, amelyek különböző formátumokban és alakzatokban használják az adattípust. 

A vetítések objektumokként vagy táblákként is megadhatók:

+ Objektumként a leképezés a blob Storage-ba kerül, ahol a leképezés egy tárolóba kerül, ahol az objektumok vagy a JSON hierarchikus ábrázolásai olyan forgatókönyvekhez használhatók, mint például az adatelemzési folyamat.

+ Táblázatként a leképezés táblázatos tárterületre mutat. A táblázatos ábrázolás megőrzi a kapcsolatokat olyan forgatókönyvek esetén, mint például az adatelemzés vagy az Exportálás adatkeretként a gépi tanuláshoz. A dúsított kivetítések később könnyedén importálhatók más adattárakba. 

A Tudásbázisban több kivetítést is létrehozhat, hogy a szervezetében különböző választókerületek is megfeleljenek. Előfordulhat, hogy a fejlesztőnek hozzá kell férnie egy dúsított dokumentum teljes JSON-ábrázolásához, míg az adatszakértők és az elemzők a készségkészlet által formázott, szemcsés vagy moduláris adatstruktúrákat is igénybe vehetnek.

Ha például a dúsítási folyamat egyik célja, hogy egy modell betanítására szolgáló adatkészletet is hozzon létre, akkor az objektum-tárolóba történő adatvetítés egyik módja lenne az adatelemzési folyamatokban lévő adat használatának. Ha egy gyors Power BI irányítópultot szeretne létrehozni a dúsított dokumentumok alapján, a táblázatos kivetítés jól működik.

<a name="tools-and-apps"></a>

## <a name="connecting-with-tools-and-apps"></a>Csatlakozás eszközökhöz és alkalmazásokhoz

Ha a dúsítások már léteznek a tárolóban, az Azure Blobhoz vagy a Table Storage-hoz kapcsolódó bármilyen eszköz vagy technológia használható a tartalmak feltárására, elemzésére és felhasználására. A következő lista a kezdete:

+ [Storage Explorer](knowledge-store-view-storage-explorer.md) a dúsított dokumentum-struktúra és-tartalom megtekintéséhez. Tekintse meg ezt az alapkonfigurációként szolgáló eszközként a Knowledge Store-tartalmak megtekintéséhez.

+ [Power bi](knowledge-store-connect-power-bi.md) a jelentéskészítési és elemzési eszközökhöz, ha numerikus adatai vannak.

+ [Azure Data Factory](https://docs.microsoft.com/azure/data-factory/) a további manipulációhoz.


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

<!-- ## Where do I start?

We recommend the Free service for learning purposes, but be aware that the number of free transactions is limited to 20 documents per day, per subscription.

When using multiple services, create all of your services in the same region for best performance and to minimize costs. You are not charged for bandwidth for inbound data or outbound data that goes to another service in the same region.

**Step 1: [Create an Azure Search resource](search-create-service-portal.md)** 

**Step 2: [Create an Azure storage account](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account?tabs=azure-portal)** 

**Step 3: [Create a Cognitive Services resource](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account)** 

**Step 4: [Get started with the portal](cognitive-search-quickstart-blob.md) - or - [Get started with sample data using REST and Postman](knowledge-store-howto.md)** 

You can use REST `api-version=2019-05-06-Preview` to construct an AI-based pipeline that includes knowledge store. In the newest preview API, the Skillset object provides the `knowledgeStore` definition. -->

## <a name="next-steps"></a>Következő lépések

A Knowledge Store az Azure Storage-fiókokhoz való hozzáférésre alkalmas bármely ügyfélalkalmazás által a készségkészlet tervezésekor hasznosnak tartja a dúsított dokumentumok megőrzését, illetve az új struktúrák és tartalmak felhasználását.

A dúsított dokumentumok létrehozásának legegyszerűbb módja az **adatimportálás** varázsló, de a Poster és a REST API is használható, ami akkor hasznos, ha az objektumok létrehozásának és hivatkozásának módját szeretné betekintésbe venni.

> [!div class="nextstepaction"]
> [Hozzon létre egy tudásbázist a portálon](knowledge-store-create-portal.md) [, 
>  hozzon létre egy tudásbázist a Poster és a REST APi használatával](knowledge-store-create-rest.md)
