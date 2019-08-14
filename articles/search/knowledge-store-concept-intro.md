---
title: A Knowledge Store bemutatása (előzetes verzió) – Azure Search
description: Gazdagított dokumentumokat küldhet az Azure Storage-ba, ahol megtekintheti, átalakíthatja és felhasználhatja a Azure Search és más alkalmazásokban található dúsított dokumentumokat.
manager: nitinme
author: HeidiSteen
services: search
ms.service: search
ms.subservice: cognitive-search
ms.devlang: NA
ms.topic: overview
ms.date: 08/02/2019
ms.author: heidist
ms.openlocfilehash: 8605a2c954eec2a57925557f8823a3614ebdc266
ms.sourcegitcommit: fe50db9c686d14eec75819f52a8e8d30d8ea725b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/14/2019
ms.locfileid: "69013381"
---
# <a name="what-is-knowledge-store-in-azure-search"></a>Mi az a Knowledge Store a Azure Searchban?

> [!Note]
> A Knowledge áruház előzetes verzióban érhető el, és nem éles használatra készült. A [REST API 2019-05-06-es verziójának előzetes verziója](search-api-preview.md) biztosítja ezt a funkciót. Jelenleg nincs .NET SDK-támogatás.
>

A Knowledge Store a Azure Search egy funkciója, amely az AI-alapú indexelési folyamat [(kognitív keresés)](cognitive-search-concept-intro.md)által létrehozott bővített dokumentumokat és metaadatokat menti. A bővített dokumentum egy olyan folyamat kimenete, amely a Cognitive Services erőforrásai által kinyert, strukturált és elemzett tartalomból lett létrehozva. A standard AI-alapú folyamatokban a dúsított dokumentumok átmenetiek, csak indexelés során használatosak, majd elvetették őket. A Knowledge Store használatával a dokumentumokat a rendszer a későbbi kiértékeléshez, feltáráshoz, és potenciálisan az alárendelt adatelemzési számítási feladatokhoz is felhasználhatja. 

Ha korábban már használta a kognitív keresést, akkor már tudja, hogy a szakértelmével egy dokumentum a dúsítások sorozatából való áthelyezésére szolgál. Az eredmény lehet egy Azure Search index, vagy (ebben az előzetes verzióban új) vetítések egy Tudásbázisban. A két kimenet, a keresési index és a Knowledge Store, fizikailag különbözik egymástól. Ugyanazt a tartalmat használják, de nagyon különböző módokon vannak tárolva és használva.

Fizikailag, a rendszer létrehoz egy tudásbázist egy Azure Storage-fiókban az Azure Table Storage vagy a blob Storage szolgáltatásban, attól függően, hogy hogyan konfigurálja a folyamatot. Minden olyan eszköz vagy folyamat, amely képes az Azure Storage-hoz kapcsolódni, felhasználhatja a Tudásbázis tartalmát.

A kivetítések az adattárakban lévő adatstrukturálás mechanizmusa. A kivetítések segítségével például megadhatja, hogy a kimenet egyetlen blobként vagy kapcsolódó táblák gyűjteménye legyen mentve. Az Azure Storage beépített Storage Explorerán keresztül könnyedén megtekintheti a Knowledge Store [](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer?tabs=windows) -tartalmakat.

![Knowledge Store a folyamat ábráján](./media/knowledge-store-concept-intro/annotationstore_sans_internalcache.png "Knowledge Store a folyamat ábráján")

A Knowledge Store használatához adjon hozzá egy `knowledgeStore` elemet egy készségkészlet, amely az indexelési folyamat lépés-Wise műveleteit definiálja. A végrehajtás során Azure Search létrehoz egy helyet az Azure Storage-fiókban, és kitölti azt a folyamat által létrehozott definíciókkal és tartalommal.

## <a name="benefits-of-knowledge-store"></a>A Knowledge Store előnyei

A Knowledge Store strukturálatlan és félig strukturált adatfájlok, például a Blobok, az elemzésen átesett képfájlok, vagy akár az új űrlapokra átalakított strukturált adatok összegyűjtését teszi lehetővé. Az előzetes verzióra írt [lépésenkénti útmutatóban](knowledge-store-howto.md) megtekintheti, hogy a vastag JSON-dokumentumok hogyan vannak particionálva alstruktúrákba, új struktúrákba felépítve, és egyéb módon elérhetővé válnak az alárendelt folyamatokhoz, például a géphez tanulási és adatelemzési számítási feladatok.

Bár hasznos lehet megtekinteni, hogy mit hozhat létre a mesterséges intelligencia-alapú indexelési folyamat, a Knowledge Store valódi ereje az Adatátalakítási képesség. Érdemes lehet egy alapszintű készségkészlet, majd megismételni a szerkezet növelését, amelyet később új struktúrákba, más alkalmazásokban pedig a Azure Searchon kívül használhat.

Enumerált, a Knowledge Store előnyei a következők:

+ A kereséstől eltérő [elemzési és jelentéskészítő eszközökön](#tools-and-apps) a dúsított dokumentumokat használhatja. A Power BI a Power Query vonzó választás, de az Azure Storage-hoz csatlakoztatható bármely eszköz vagy alkalmazás lehívható a létrehozott Tudásbázisból.

+ Egy AI-indexelési folyamat pontosítása a lépések és a készségkészlet-definíciók hibakeresése közben. A Tudásbázis egy AI-indexelési folyamat készségkészlet-definíciójának termékét jeleníti meg. Ezeket az eredményeket a jobb készségkészlet kialakításához használhatja, mert láthatja, hogy pontosan mit néz ki a gazdagítás. Az Azure Storage [Storage Explorer](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer?tabs=windows) használatával megtekintheti a Tudásbázis tartalmát.

+ Az új űrlapokra alakítja az adathalmazt. Az átformálás a szakértelmével-ben kodifikált, de a lényeg az, hogy egy készségkészlet most már megadhatja ezt a képességet. A Azure Searchban lévő [formáló képesség](cognitive-search-skill-shaper.md) ki lett terjesztve a feladat befogadására. Az átformálás lehetővé teszi, hogy olyan leképezést határozzon meg, amely igazodik az adatfelhasználáshoz a kapcsolatok megőrzése mellett.

> [!Note]
> Nem ismeri a Cognitive Servicest használó AI-alapú indexelést? A Azure Search Cognitive Services jövőkép és nyelvi funkciók integrálásával kinyerheti és gazdagíthatja a forrásadatokat a képfájlok, az entitások felismerése és a szöveges fájlok kinyerése, valamint a szövegfájlokból való kivonások használatával. További információ: [Mi a kognitív keresés?](cognitive-search-concept-intro.md).

## <a name="create-a-knowledge-store"></a>Knowledge Store létrehozása

A Tudásbázis egy készségkészlet-definíció részét képezi. Ebben az előzetes verzióban `api-version=2019-05-06-Preview` a létrehozásához a portálon a REST API, a vagy az adatimportálás varázsló szükséges.

A következő JSON a `knowledgeStore`, amely egy készségkészlet része, amelyet egy indexelő hív meg (nem látható). A kivetítések meghatározása a meghatározza `knowledgeStore` , hogy a táblák vagy objektumok az Azure Storage-ban jönnek-e létre.

Ha már ismeri az AI-alapú indexelést, a készségkészlet definíciója határozza meg az egyes dúsított dokumentumok létrehozását, szervezetét és tartalmát.

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

## <a name="components-backing-a-knowledge-store"></a>A Knowledge Store-t támogató összetevők

A Knowledge Store létrehozásához a következő szolgáltatásokra és összetevőkre van szükség.

### <a name="1---source-data"></a>1 – forrásadatok

A bővíteni kívánt adatforrásoknak és dokumentumoknak Azure Search indexelő által támogatott Azure-adatforrásban kell lenniük: 

* [Azure SQL](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md)

* [Azure Cosmos DB](search-howto-index-cosmosdb.md)

* [Azure Blob Storage](search-howto-indexing-azure-blob-storage.md)

Az [Azure Table Storage](search-howto-indexing-azure-tables.md) egy tudásbázisbeli kimenő adathoz használható, de nem használható erőforrásként a bejövő adathoz egy AI-alapú indexelési folyamat számára.

### <a name="2---azure-search-service"></a>2 – Azure Search szolgáltatás

Szükség van egy Azure Search szolgáltatásra és a REST API az adatbővítéshez használt objektumok létrehozására és konfigurálására is. A Tudásbázis `api-version=2019-05-06-Preview`létrehozásához szükséges REST API.

Azure Search biztosítja az Indexelő szolgáltatást, és az indexelő a teljes folyamat végpontok közötti, az Azure Storage-ban megőrzött és dúsított dokumentumait használja. Az indexelő egy adatforrást, egy indexet és egy készségkészlet használ, amely a Tudásbázis létrehozásához és feltöltéséhez szükséges.

| Object | REST API | Leírás |
|--------|----------|-------------|
| adatforrás | [Adatforrás létrehozása](https://docs.microsoft.com/rest/api/searchservice/create-data-source)  | Egy külső Azure-adatforrást azonosító erőforrás, amely a dúsított dokumentumok létrehozásához használt forrásadatokat biztosít.  |
| készségkészlet | [Készségkészlet létrehozása (API-Version = 2019-05-06)](https://docs.microsoft.com/rest/api/searchservice/create-skillset)  | Egy erőforrás, amely összehangolja a [beépített készségek](cognitive-search-predefined-skills.md) használatát és a dúsítási folyamat során használt [Egyéni kognitív képességeket](cognitive-search-custom-skill-interface.md) az indexelés során. |
| index | [Index létrehozása](https://docs.microsoft.com/rest/api/searchservice/create-index)  | Egy Azure Search indexet kifejező séma. Az indexben található mezők a forrásadatok mezőire vagy a dúsítási fázisban előállított mezőkre (például az entitások felismerése által létrehozott szervezeti nevekre vonatkozó mezőre) vonatkoznak. |
| indexelő | [Indexelő létrehozása (API-Version = 2019-05-06)](https://docs.microsoft.com/rest/api/searchservice/create-skillset)  | Az indexelés során használt összetevőket meghatározó erőforrás: beleértve az adatforrást, a készségkészlet, a forrás-és a közbenső adatstruktúrákat a célként megadott indexbe, valamint magát az indexet. Az indexelő futtatása az adatfeldolgozáshoz és a dúsításhoz használt trigger. A kimenet egy olyan keresési index, amely az index sémán alapul, és a forrásadatok alapján van feltöltve, és a szakértelmével-n keresztül dúsított.  |

### <a name="3---cognitive-services"></a>3 – Cognitive Services

A készségkészlet megadott dúsítások a Cognitive Services Computer Vision és nyelvi szolgáltatásain alapulnak. Az indexelés során a rendszer kihasznál Cognitive Services funkciókat a készségkészlet. A készségkészlet a szaktudás összetétele, és a képességek bizonyos Computer Vision és nyelvi funkciókhoz vannak kötve. A Cognitive Services integrálásához [egy Cognitive Services erőforrást fog csatolni](cognitive-search-attach-cognitive-services.md) egy készségkészlet.

### <a name="4---storage-account"></a>4 – Storage-fiók

Az Azure Storage-fiókjában Azure Search létrehoz egy BLOB-tárolót vagy-táblákat, attól függően, hogy hogyan konfigurálja a készségkészlet. Ha az adatai az Azure Blob vagy table Storage szolgáltatásból származnak, akkor már be van állítva. Ellenkező esetben létre kell hoznia egy Azure Storage-fiókot. Az Azure Storage-ban található táblák és objektumok tartalmazzák az AI-alapú indexelési folyamat által létrehozott dúsított dokumentumokat.

A Storage-fiók meg van adva a készségkészlet. A `api-version=2019-05-06-Preview`-ben a készségkészlet definíciója tartalmaz egy Tudásbázis-definíciót, amely a fiókadatok megadására szolgál.

<a name="tools-and-apps"></a>

### <a name="5---access-and-consume"></a>5 – hozzáférés és felhasználás

Ha a dúsítások már léteznek a tárolóban, az Azure Blobhoz vagy a Table Storage-hoz kapcsolódó bármilyen eszköz vagy technológia használható a tartalmak feltárására, elemzésére és felhasználására. A következő lista a kezdete:

+ [Storage Explorer](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer?tabs=windows) a dúsított dokumentum-struktúra és-tartalom megtekintéséhez. Tekintse meg ezt az alapkonfigurációként szolgáló eszközként a Knowledge Store-tartalmak megtekintéséhez.

+ [Power bi a](https://support.office.com/article/connect-to-microsoft-azure-blob-storage-power-query-f8165faa-4589-47b1-86b6-7015b330d13e) természetes nyelvi lekérdezések Power Queryével, vagy ha numerikus adattal rendelkezik, használja a jelentéskészítési és elemzési eszközöket.

+ [Azure Data Factory](https://docs.microsoft.com/azure/data-factory/) a további manipulációhoz.

+ Azure Search index a teljes szöveges kereséshez a [kognitív keresés](cognitive-search-concept-intro.md)használatával indexelt tartalmakon.

## <a name="document-persistence"></a>Dokumentumok megőrzése

A Storage-fiókon belül a dúsítás az Azure Table Storage-ban vagy az Azure Blob Storage-ban található objektumokként is kifejezhető. Ne felejtse el, hogy a-ben tárolt bővítések forrásaként használhatók más adatbázisokba és eszközökbe való betöltéshez.

+ A Table Storage akkor hasznos, ha az adatokat táblázatos formában ábrázoló, sémát támogató ábrázolásra szeretné használni. Ha új módokon szeretné átalakítani vagy újraegyesíteni az elemeket, a Table Storage biztosítja a szükséges részletességet.

+ A blob Storage az egyes dokumentumok egy teljes körű JSON-reprezentációját hozza létre. Az egyik készségkészlet mindkét tárolási lehetőségét használhatja a kifejezések teljes skálájának lekéréséhez.

+ Azure Search megőrzi a tartalmat egy indexben. Ha a forgatókönyv nem kereséssel kapcsolatos, például ha a cél egy másik eszköz elemzése, akkor törölheti a folyamat által létrehozott indexet. Ugyanakkor megtarthatja az indexet is, és használhat egy beépített eszközt, például a [Search Explorert](search-explorer.md) harmadik médiumként (Storage Explorer és az elemzési alkalmazás mögött) a tartalommal való interakcióhoz.

A dokumentumok tartalmával együtt a bővített dokumentumok tartalmazzák a dúsítást előkészítő készségkészlet-verzió metaadatait.  

## <a name="inside-a-knowledge-store"></a>Egy tudásbázison belül

A Knowledge Store egy Megjegyzés-gyorsítótárból és-kivetítésből áll. A szolgáltatás a gyorsítótárat belsőleg használja a képességek eredményeinek gyorsítótárazására és a változások nyomon követésére. A *kivetítés* határozza meg a kívánt felhasználási feltételeknek megfelelő dúsítások sémáját és szerkezetét. Az egyes tudásbázisokban egy gyorsítótár található, de több kivetítés is. 

A gyorsítótár mindig blob-tároló, de a vetítések táblázatként vagy objektumként is megadhatók:

+ Objektumként a leképezés a blob Storage-ba kerül, ahol a leképezés egy tárolóba kerül, ahol az objektumok vagy a JSON hierarchikus ábrázolásai olyan forgatókönyvekhez használhatók, mint például az adatelemzési folyamat.

+ Táblázatként a leképezés táblázatos tárterületre mutat. A táblázatos ábrázolás megőrzi a kapcsolatokat olyan forgatókönyvek esetén, mint például az adatelemzés vagy az Exportálás adatkeretként a gépi tanuláshoz. A dúsított kivetítések később könnyedén importálhatók más adattárakba. 

A Tudásbázisban több kivetítést is létrehozhat, hogy a szervezetében különböző választókerületek is megfeleljenek. Előfordulhat, hogy a fejlesztőnek hozzá kell férnie egy dúsított dokumentum teljes JSON-ábrázolásához, míg az adatszakértők és az elemzők a készségkészlet által formázott, szemcsés vagy moduláris adatstruktúrákat is igénybe vehetnek.

Ha például a dúsítási folyamat egyik célja, hogy egy modell betanítására szolgáló adatkészletet is hozzon létre, akkor az objektum-tárolóba történő adatvetítés egyik módja lenne az adatelemzési folyamatokban lévő adat használatának. Ha egy gyors Power BI irányítópultot szeretne létrehozni a dúsított dokumentumok alapján, a táblázatos kivetítés jól működik.

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

Javasoljuk, hogy az ingyenes szolgáltatást tanulási célokra használja, de vegye figyelembe, hogy az ingyenes tranzakciók száma napi 20 dokumentumra van korlátozva, előfizetéssel.

Több szolgáltatás használatakor a legjobb teljesítmény érdekében hozza létre az összes szolgáltatást ugyanabban a régióban, és csökkentse a költségeket. Nem számítunk fel díjat a bejövő és a kimenő adatforgalomért, amelyek ugyanabba a régióba tartozó másik szolgáltatásra mutatnak.

**1. lépés: [Azure Search erőforrás létrehozása](search-create-service-portal.md)** 

**2. lépés: [Azure Storage-fiók létrehozása](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account?tabs=azure-portal)** 

**3. lépés: [Cognitive Services erőforrás létrehozása](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account)** 

**4. lépés: Ismerkedjen meg [a portál](cognitive-search-quickstart-blob.md) -vagy- [Ismerkedés a MINTAADATOK használatával a REST és a Poster használatával](knowledge-store-howto.md)** 

A REST `api-version=2019-05-06-Preview` használatával olyan mesterséges intelligencia-alapú folyamatot hozhat létre, amely tartalmazza a Knowledge Store-t. A legújabb előzetes verzió API-ban a készségkészlet objektum biztosítja `knowledgeStore` a definíciót.

## <a name="takeaways"></a>Legfontosabb ismeretek

A Knowledge Store számos előnyt kínál, többek között a nem a keresés, a költségcsökkentés és a drift kezelése a dúsítási folyamat során a dúsított dokumentumok használatának lehetővé tételéhez. Ezek a funkciók egyszerűen használhatók egy Storage-fiók készségkészlet való hozzáadásával és a frissített kifejezés nyelvének használatával, a következő témakörben ismertetett módon: a [Knowledge Store használatának első lépései](knowledge-store-howto.md). 

## <a name="next-steps"></a>További lépések

A dúsított dokumentumok létrehozásának legegyszerűbb módja az adatimportálás varázsló .

> [!div class="nextstepaction"]
> [Rövid útmutató: A kognitív keresés kipróbálása egy portálon – útmutató](cognitive-search-quickstart-blob.md)
