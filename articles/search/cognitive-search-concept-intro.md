---
title: Bevezetés a a ai-dúsításba
titleSuffix: Azure Cognitive Search
description: A tartalomkinyerés, a természetes nyelvi feldolgozás (NLP) és a képfeldolgozás segítségével kereshető tartalmat hozhat létre az Azure Cognitive Search indexekben előre definiált kognitív képességekkel és egyéni AI-algoritmusokkal.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 03/24/2020
ms.openlocfilehash: a41dcc9c7ec86f41c64a69ea1aba762b960b2633
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80283021"
---
# <a name="getting-started-with-ai-enrichment"></a>A ai-dúsítás első lépései

Az AI-bővítés az Azure Cognitive Search indexelésének egyik képessége, amely a képek, blobok és más strukturálatlan adatforrások szövegeinek kinyerésére szolgál. A gazdagítás és a kitermelés teszi a tartalmat kereshetőbbé egy [indexben](search-what-is-an-index.md) vagy [tudástárolóban.](knowledge-store-concept-intro.md) A kibontás és a dúsítás az indexelési folyamathoz kapcsolódó *kognitív képességek* használatával valósul meg. A szolgáltatásba beépített kognitív képességek a következő kategóriákba tartoznak: 

+ **A természetes nyelvi feldolgozási** készségek közé tartozik [az entitásfelismerés, a](cognitive-search-skill-entity-recognition.md) [nyelvfelismerés,](cognitive-search-skill-language-detection.md) [a kulcskifejezések kinyerése,](cognitive-search-skill-keyphrases.md)a szövegkezelés, [a hangulatészlelés](cognitive-search-skill-sentiment.md)és [a személyazonosításra használt személy azonosítása.](cognitive-search-skill-pii-detection.md) Ezekkel a képességekkel a strukturálatlan szöveg kereshető és szűrhető mezőkként van leképezve az indexben.

+ **A képfeldolgozási** készségek közé tartozik [az optikai karakterfelismerés (OCR)](cognitive-search-skill-ocr.md) és a [vizuális jellemzők](cognitive-search-skill-image-analysis.md)azonosítása, például arcfelismerés, képértelmezés, képfelismerés (híres emberek és tereptárgyak) vagy olyan jellemzők, mint a képtájolás. Ezek a képességek szöveges ábrázolásokat hoznak létre a képtartalomról, így az Azure Cognitive Search lekérdezési képességeinek használatával kereshető.

![Dúsítási folyamat diagramja](./media/cognitive-search-intro/cogsearch-architecture.png "dúsítási folyamat – áttekintés")

Az Azure Cognitive Search kognitív képességei a Cognitive Services API-k előre betanított gépi tanulási modelljein alapulnak: [számítógépes látás](https://docs.microsoft.com/azure/cognitive-services/computer-vision/) és [szövegelemzés.](https://docs.microsoft.com/azure/cognitive-services/text-analytics/overview) 

A természetes nyelvi és képfeldolgozási a rendszer az adatbetöltési fázisban kerül alkalmazásra, és az eredmények a dokumentum összetételének részévé válnak az Azure Cognitive Search kereshető indexében. Az adatok forrása Egy Azure-adatkészlet, majd egy indexelési folyamaton keresztül, akívánt [beépített képességek](cognitive-search-predefined-skills.md) van szüksége. Az architektúra bővíthető, így ha a beépített szakértelem nem elegendőek, létrehozhat és csatolhat [egyéni képességeket](cognitive-search-create-custom-skill-example.md) az egyéni feldolgozás integrálásához. Ilyen lehet például egy egyéni entitásmodul vagy dokumentumosztályozó, amely egy adott területet, például a pénzügyeket, a tudományos publikációkat vagy az orvostudományt célozza meg.

## <a name="when-to-use-ai-enrichment"></a>Mikor kell használni az AI-dúsítást?

Érdemes érdemes a beépített kognitív képességek, ha a nyers tartalom strukturálatlan szöveg, képtartalom, vagy a tartalom, amely nyelvi felismerés és fordítás szükséges. A a mi alkalmazása a beépített kognitív képességek segítségével feloldhatja ezt a tartalmat, növelve annak értékét és hasznosságát a keresési és adatelemzési alkalmazásokban. 

Emellett érdemes lehet egy egyéni szakértelem hozzáadása, ha nyílt forráskódú, harmadik féltől származó vagy külső kódot szeretne integrálni a folyamatba. A különböző dokumentumtípusok jelentős jellemzőit azonosító besorolási modellek ebbe a kategóriába tartoznak, de minden olyan csomag, amely hozzáadott értéket ad a tartalomhoz, használható.

### <a name="more-about-built-in-skills"></a>További információk a beépített készségekről

A beépített képességek használatával összeállított [skillset](cognitive-search-defining-skillset.md) a következő alkalmazási forgatókönyvekhez megfelelő:

+ A teljes szöveget kereshetővé tenni kívánt beolvasott dokumentumok (JPEG). Optikai karakterfelismerő (OCR) szakértelem csatolásával azonosíthatja, kinyerheti és beláthatja a JPEG-fájlokszövegét.

+ PDF-fájlok kombinált képpel és szöveggel. A PDF-dokumentumokban lévő szöveg az indexelés során dúsítási lépések használata nélkül is kinyerhető, de a kép és a természetes nyelvi feldolgozás hozzáadása gyakran jobb eredményt eredményez, mint amit a szabványos indexelés biztosít.

+ Többnyelvű tartalom, amelyre nyelvfelismerést és esetleg szövegfordítást szeretne alkalmazni.

+ Strukturálatlan vagy félig strukturált dokumentumok, amelyek olyan tartalmat tartalmaznak, amelynek rejtett jelentése vagy környezete el van rejtve a nagyobb dokumentumban. 

  A blobok gyakran tartalmaznak nagy mennyiségű tartalmat, amely egyetlen "mezőbe" van csomagolva. Ha kép- és természetes nyelvi feldolgozási készségeket csatol egy indexelőhöz, olyan új információkat hozhat létre, amelyek a nyers tartalomban vannak, de más módon nem kerülnek külön mezőként. Néhány használatra kész beépített kognitív képességek, amelyek segíthetnek: kulcskifejezések kinyerése, hangulatelemzés és entitásfelismerés (személyek, szervezetek és helyek).

  Ezenkívül a beépített szakértelem szövegfelosztással, egyesítési és alakzatműveletekkel is használható a tartalom átstrukturálása révén.

### <a name="more-about-custom-skills"></a>További információk az egyéni készségekről

Az egyéni képességek összetettebb forgatókönyveket is támogathatnak, például az űrlapok felismerését vagy az egyéni entitásészlelést az [egyéni szakértelem webes felületén](cognitive-search-custom-skill-interface.md)megadott és körbefolyatott modell használatával. Az egyéni képességek közé tartozik az [Űrlapfelismerő](/azure/cognitive-services/form-recognizer/overview), a [Bing Entity Search API](https://docs.microsoft.com/azure/search/cognitive-search-create-custom-skill-example)integrációja és az egyéni [entitásfelismerés](https://github.com/Microsoft/SkillsExtractorCognitiveSearch).


## <a name="steps-in-an-enrichment-pipeline"></a>A dúsítási folyamat lépései

A dúsítási folyamat [*indexelőkön*](search-indexer-overview.md)alapul. Az indexelők az index és a dokumentumfeltörés adatforrása közötti mező-mező leképezések alapján feltöltenek egy indexet. Skills, most csatolt indexelők, elfogása és gazdagítják dokumentumok szerint a skillset (ek) határozza meg. Az indexelést követően az Azure Cognitive Search által támogatott összes [lekérdezéstípuson](search-query-overview.md)keresztül érheti el a tartalmat.  Ha most az indexelők, ez a szakasz végigvezeti a lépéseket.

### <a name="step-1-connection-and-document-cracking-phase"></a>1. lépés: A be- és kirepedési fázis

A folyamat kezdetén strukturálatlan szöveg vagy nem szöveges tartalom (például képek, beolvasott dokumentumok vagy JPEG-fájlok) található. Az adatoknak létezniük kell egy olyan Azure-adattárolási szolgáltatásban, amelyet egy indexelő érhet el. Az indexelők "feltörhetik" a forrásdokumentumokat, hogy szöveget nyerjenek ki a forrásadatokból. A dokumentumfeltörés az a folyamat, amelynek során nem szöveges forrásokból szövegtartalmat lehet kinyerni vagy létrehozni az indexelés során.

![Dokumentum feltörési fázis](./media/cognitive-search-intro/document-cracking-phase-blowup.png "dokumentum feltörése")

 A támogatott források közé tartozik az Azure blob storage, az Azure table storage, az Azure SQL Database és az Azure Cosmos DB. A szövegalapú tartalom a következő fájltípusokból nyerhető ki: PDF-fájlok, Word, PowerPoint, CSV-fájlok. A teljes listát a [Támogatott formátumok című témakörben található.](search-howto-indexing-azure-blob-storage.md#supported-document-formats) Az indexelés időt vesz igénybe, ezért kezdje egy kis, reprezentatív adatkészlettel, majd a megoldás érlelődésével fokozatosan.

### <a name="step-2-cognitive-skills-and-enrichment-phase"></a>2. lépés: Kognitív készségek és gazdagodási fázis

A dúsítást *kognitív képességekkel* végezzük, atomi műveleteket végezve. Ha például feltört egy PDF-dokumentumot, entitásfelismerést, nyelvfelismerést vagy kulcskifejezés-kinyerést alkalmazhat olyan új mezők létrehozásához az indexben, amelyek nem érhetők el natív módon a forrásban. A folyamatban használt készségek gyűjteményét összesen *skillsetnek nevezzük.*  

![Dúsítási fázis](./media/cognitive-search-intro/enrichment-phase-blowup.png "dúsítási fázis")

A skillset alapul [beépített kognitív képességek](cognitive-search-predefined-skills.md) vagy egyéni [képességek](cognitive-search-create-custom-skill-example.md) ön által biztosított, és csatlakozzon a skillset. A skillset lehet minimális vagy nagyon összetett, és nem csak a feldolgozás típusát határozza meg, hanem a műveletek sorrendjét is. A skillset, valamint az indexelő részeként definiált mezőleképezések teljes mértékben meghatározza a dúsítási folyamatot. Az összes darab összehúzásáról további információt a [Skillset definiálása](cognitive-search-defining-skillset.md)című témakörben talál.

Belsőleg a folyamat bővített dokumentumok gyűjteményét hozza létre. Eldöntheti, hogy a bővített dokumentumok mely részei legyenek leképezve a keresési index indexindexindexindexében indexelhető mezőkhöz. Ha például a kulcsmondat-kinyerést és az entitásfelismerési készségeket alkalmazta, ezek az új mezők a bővített dokumentum részévé válnak, és az index mezőihez rendelhetők hozzá. A [bemeneti/kimeneti formációkról](cognitive-search-concept-annotations-syntax.md) további információt a Jegyzetek című témakörben olvashat.

#### <a name="add-a-knowledgestore-element-to-save-enrichments"></a>Tudástároló elem hozzáadása a dúsítások mentéséhez

[Search REST api-version=2019-05-06-Preview](search-api-preview.md) kiterjeszti skillsets egy `knowledgeStore` definíciót, amely egy Azure storage-kapcsolat és előrejelzések, amelyek leírják, hogyan tárolja a dúsítások. Ez az indexen kívül van. Egy szabványos AI-folyamatban a bővített dokumentumok átmenetiek, csak indexelés során használatosak, majd elvetik őket. A tudástárolóval a bővített dokumentumok megőrződnek. További információ: [Tudástároló (előzetes verzió)](knowledge-store-concept-intro.md).

### <a name="step-3-search-index-and-query-based-access"></a>3. lépés: Keresési index és lekérdezésalapú hozzáférés

Amikor a feldolgozás befejeződött, rendelkezik egy bővített dokumentumokból álló keresési indexszel, amely teljes mértékben kereshető az Azure Cognitive Search-ben. [Az index lekérdezése az,](search-query-overview.md) hogy a fejlesztők és a felhasználók hogyan férhetnek hozzá a folyamat által létrehozott bővített tartalomhoz. 

![Index kereséssel ikonnal](./media/cognitive-search-intro/search-phase-blowup.png "Index kereséssel ikonnal")

Az index olyan, mint bármely más, amelyet az Azure Cognitive Search számára hozhat létre: kiegészítheti az egyéni elemzőkkel, intelligens keresési lekérdezéseket hívhat meg, szűrt keresést adhat hozzá, vagy kísérletezhet a pontozási profilokkal a keresési eredmények átformálásához.

Az indexek egy indexsémából jönnek létre, amely meghatározza egy adott indexhez kapcsolódó mezőket, attribútumokat és egyéb konstrukciókat, például a pontozási profilokat és a szinonimaleképezéseket. Az index definiálása és feltöltése után az új és frissített forrásdokumentumok felvételéhez fokozatosan indexelheti. Bizonyos módosítások teljes újraépítést igényelnek. Egy kis adatkészletet kell használnia, amíg a sématerv stabil nem lesz. A további tudnivalókért lásd az [indexek újraépítését](search-howto-reindex.md) ismertető cikket.

**Feladatlista: Tipikus munkafolyamat**

1. Az Azure-forrásadatok részhalmaza egy reprezentatív mintába. Az indexelés időt vesz igénybe, ezért kezdje egy kis, reprezentatív adatkészlettel, majd a megoldás érlelődésével fokozatosan.

1. Hozzon létre egy [adatforrás-objektumot](https://docs.microsoft.com/rest/api/searchservice/create-data-source) az Azure Cognitive Search szolgáltatásban, hogy az adatok lekéréséhez kapcsolati karakterláncot biztosítson.

1. Hozzon létre egy [skillset](https://docs.microsoft.com/rest/api/searchservice/create-skillset) gazdagítási lépéseket.

1. Adja meg az [indexsémát](https://docs.microsoft.com/rest/api/searchservice/create-index). A *Mezők* gyűjtemény a forrásadatokból származó mezőket tartalmaz. További mezőket is ki kell tolnia a dúsítás során létrehozott tartalom hozadékának tárolására.

1. Adja meg az adatforrásra, a skillsetre és az indexre hivatkozó [indexelőt.](https://docs.microsoft.com/rest/api/searchservice/create-skillset)

1. Az indexelőn belül adja hozzá az *outputFieldMappings értéket.* Ez a szakasz leképezi a skillset kimenetét (3. lépésben) az indexséma bemeneti mezőihez (a 4. lépésben).

1. Küldje *létre az indexelő létrehozása* kérelmet az imént létrehozott (egy POST-kérelem egy indexelő definícióval a kérelem törzsében) az indexelő kifejezésére az Azure Cognitive Search. Ez a lépés az indexelő futtatásának módját, a folyamat meghívását.

1. Lekérdezések futtatásával kiértékelheti az eredményeket, és módosíthatja a kódot a skillsets, séma vagy indexelő konfigurációjának frissítéséhez.

1. [Állítsa alaphelyzetbe az indexelőt](search-howto-reindex.md) a folyamat újraépítése előtt.

## <a name="next-steps"></a>További lépések

+ [AI dúsítási dokumentációs hivatkozások](cognitive-search-resources-documentation.md)
+ [Példa: Egyéni szakértelem létrehozása a ai-bővítéshez (C#)](cognitive-search-create-custom-skill-example.md)
+ [Rövid útmutató: Próbálja ki a ai-dúsítást egy portál-útmutatóban](cognitive-search-quickstart-blob.md)
+ [Oktatóanyag: Ismerje meg az AI gazdagítási API-kat](cognitive-search-tutorial-blob.md)
+ [Tudástároló (előzetes verzió)](knowledge-store-concept-intro.md)
+ [Tudástároló létrehozása a REST-ben](knowledge-store-create-rest.md)
+ [Hibaelhárítási tippek](cognitive-search-concept-troubleshooting.md)
