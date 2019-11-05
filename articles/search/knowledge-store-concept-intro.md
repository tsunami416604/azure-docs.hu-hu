---
title: A Knowledge Store bemutatása (előzetes verzió)
titleSuffix: Azure Cognitive Search
description: Gazdagított dokumentumokat küldhet az Azure Storage-ba, ahol megtekintheti, átalakíthatja és felhasználhatja a dúsított dokumentumokat az Azure Cognitive Search és más alkalmazásokban.
author: HeidiSteen
manager: nitinme
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 2e6d20a1eca7a6b3281e33d8534ab3456e79ccdf
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/04/2019
ms.locfileid: "73485082"
---
# <a name="introduction-to-knowledge-stores-in-azure-cognitive-search"></a>Az Azure Cognitive Search tudásbázisának bemutatása

> [!Note]
> A Knowledge áruház előzetes verzióban érhető el, és nem éles használatra készült. A [REST API 2019-05-06-es verziójának előzetes verziója](search-api-preview.md) biztosítja ezt a funkciót. Jelenleg nincs .NET SDK-támogatás.
>

A Knowledge Store az Azure Cognitive Search egyik funkciója, amely egy [mesterséges intelligencia](cognitive-search-concept-intro.md) -bővítési folyamat kimenetét őrzi meg későbbi elemzésekhez vagy más alsóbb rétegbeli feldolgozásokhoz. A *dúsított dokumentum* egy folyamat kimenete, amely az AI-folyamatokkal kinyert, strukturált és elemzett tartalomból készült. A standard AI-folyamatokban a dúsított dokumentumok átmenetiek, csak indexelés során használhatók, majd elvetették őket. A Knowledge Store-ban a dúsított dokumentumok megmaradnak. 

Ha korábban már használta a kognitív képességeket az Azure Cognitive Search, akkor már tudja, hogy a *szakértelmével* a bővítési folyamaton keresztül helyezi át a dokumentumot. Az eredmény lehet keresési index, vagy (ebben az előzetes verzióban új) vetítések egy Tudásbázisban. A két kimenet, a keresési index és a Tudásbázis, ugyanazokat a tartalmakat használja, de a tárolása és használata nagyon különböző módokon történik.

Fizikailag a Knowledge Store az [Azure Storage](https://docs.microsoft.com/azure/storage/common/storage-account-overview), az Azure Table Storage, az Azure Blob Storage vagy mindkettő. Minden olyan eszköz vagy folyamat, amely képes az Azure Storage-hoz kapcsolódni, felhasználhatja a Tudásbázis tartalmát.

![Knowledge Store a folyamat ábráján](./media/knowledge-store-concept-intro/knowledge-store-concept-intro.svg "Knowledge Store a folyamat ábráján")

A Knowledge Store használatához adjon hozzá egy `knowledgeStore` elemet egy készségkészlet, amely az indexelési folyamat lépés-Wise műveleteit definiálja. A végrehajtás során az Azure Cognitive Search létrehoz egy helyet az Azure Storage-fiókban, és a dúsított dokumentumokat a konfigurációtól függően blobként vagy táblázatként adja meg.

## <a name="benefits-of-knowledge-store"></a>A Knowledge Store előnyei

A Knowledge Store strukturálatlan és félig strukturált adatfájlok, például a Blobok, az elemzésen átesett képfájlok, vagy akár az új űrlapokra átalakított strukturált adatok összegyűjtését teszi lehetővé. Egy [lépésenkénti útmutatóban](knowledge-store-howto.md)megtekintheti, hogyan particionálja a vastag JSON-dokumentumot alstruktúrákba, új struktúrákba felépítve, és egyéb módon elérhetővé teszi azokat az alárendelt folyamatokhoz, mint például a gépi tanulás és az adattudomány számítási feladatok.

Bár hasznos lehet megtekinteni, hogy mit hozhat létre az AI-bővítési folyamat, a Knowledge Store valódi ereje az Adatátalakítási képesség. Érdemes lehet egy alapszintű készségkészlet, majd megismételni a szerkezet növelését, amelyet később új struktúrákba, más alkalmazásokban pedig az Azure Cognitive Search is használhat.

Enumerált, a Knowledge Store előnyei a következők:

+ A kereséstől eltérő [elemzési és jelentéskészítő eszközökön](#tools-and-apps) a dúsított dokumentumokat használhatja. A Power BI a Power Query vonzó választás, de az Azure Storage-hoz csatlakoztatható bármely eszköz vagy alkalmazás lehívható a létrehozott Tudásbázisból.

+ Egy AI-indexelési folyamat pontosítása a lépések és a készségkészlet-definíciók hibakeresése közben. A Tudásbázis egy AI-indexelési folyamat készségkészlet-definíciójának termékét jeleníti meg. Ezeket az eredményeket a jobb készségkészlet kialakításához használhatja, mert láthatja, hogy pontosan mit néz ki a gazdagítás. Az Azure Storage [Storage Explorer](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer?tabs=windows) használatával megtekintheti a Tudásbázis tartalmát.

+ Az új űrlapokra alakítja az adathalmazt. Az átformálás a szakértelmével-ben kodifikált, de a lényeg az, hogy egy készségkészlet most már megadhatja ezt a képességet. A feladathoz való igazodás érdekében a [shapeer-képesség](cognitive-search-skill-shaper.md) az Azure Cognitive Search kiterjeszthető. Az átformálás lehetővé teszi, hogy olyan leképezést határozzon meg, amely igazodik az adatfelhasználáshoz a kapcsolatok megőrzése mellett.

> [!Note]
> Újdonságok a mesterséges intelligencia és a kognitív képességek terén? Az Azure Cognitive Search Cognitive Services látási és nyelvi funkciókkal integrálható és gazdagíthatja a forrásadatokat az optikai karakterfelismerés (OCR) révén a képfájlok, az entitások felismerése és a szöveges fájlokból kinyert kulcsfontosságú kifejezések használatával. További információ: [AI-bővítés az Azure Cognitive Searchban](cognitive-search-concept-intro.md).

## <a name="creating-a-knowledge-store"></a>Tudásbázis létrehozása

A Tudásbázis egy [készségkészlet](cognitive-search-working-with-skillsets.md)része, amely viszont egy [Indexelő](search-indexer-overview.md)része. 

Ebben az előzetes verzióban létrehozhat egy tudásbázist a REST API és a `api-version=2019-05-06-Preview`használatával vagy a portál **adatimportálás** varázslójával.

### <a name="json-representation-of-a-knowledge-store"></a>A Knowledge Store JSON-ábrázolása

A következő JSON egy olyan `knowledgeStore`t határoz meg, amely egy készségkészlet része, amelyet egy indexelő hív meg (nem látható). Ha már ismeri a mesterséges intelligenciát, a készségkészlet határozza meg az egyes dúsított dokumentumok létrehozását, rendszerezését és tartalmát. A készségkészlet tartalmaznia kell legalább egy képességet, amely valószínűleg egy formáló képesség, ha az adatstruktúrákat modulálja.

A `knowledgeStore` kapcsolatokből és kivetítésből áll. 

+ A kapcsolódás egy olyan Storage-fiókhoz, amely ugyanabban a régióban található, mint az Azure Cognitive Search. 

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

Ha egy Tudásbázis kimenete egy mesterséges intelligencia-dúsítási folyamatból származik, milyen bemenetek vannak? A kinyerni, bővíteni és végül menteni kívánt eredeti adatok a keresési indexelő által támogatott bármely Azure-adatforrásból származhatnak: 

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
| index | [Index létrehozása](https://docs.microsoft.com/rest/api/searchservice/create-index)  | Keresési indexet kifejező séma. Az indexben található mezők a forrásadatok mezőire vagy a dúsítási fázisban előállított mezőkre (például az entitások felismerése által létrehozott szervezeti nevekre vonatkozó mezőre) vonatkoznak. |
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

## <a name="next-steps"></a>További lépések

A Knowledge Store az Azure Storage-fiókokhoz való hozzáférésre alkalmas bármely ügyfélalkalmazás által a készségkészlet tervezésekor hasznosnak tartja a dúsított dokumentumok megőrzését, illetve az új struktúrák és tartalmak felhasználását.

A dúsított dokumentumok létrehozásának legegyszerűbb módja az **adatimportálás** varázsló, de a Poster és a REST API is használható, ami akkor hasznos, ha az objektumok létrehozásának és hivatkozásának módját szeretné betekintésbe venni.

> [!div class="nextstepaction"]
> [Hozzon létre egy tudásbázist a portálon](knowledge-store-create-portal.md) [,
> hozzon létre egy tudásbázist a Poster és a REST APi használatával](knowledge-store-create-rest.md)
