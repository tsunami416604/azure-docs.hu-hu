---
title: Az AI-bővítés bemutatása
titleSuffix: Azure Cognitive Search
description: Tartalom kinyerése, természetes nyelvi feldolgozás (NLP) és képfeldolgozás, kereshető tartalom létrehozása az Azure-ban Cognitive Search a kognitív képességek és AI-algoritmusok használatával.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: overview
ms.date: 11/04/2019
ms.openlocfilehash: 92fe564b849c728952dd549757be42b8b5131b25
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/03/2019
ms.locfileid: "74791030"
---
# <a name="introduction-to-ai-in-azure-cognitive-search"></a>Az AI bemutatása az Azure-ban Cognitive Search

Az AI-bővítés az Azure Cognitive Search a képekből, blobokból és egyéb strukturálatlan adatforrásokból származó szöveg kinyerésére szolgáló funkciója – a tartalom gazdagítása, hogy azok kereshetőek legyenek egy indexben vagy egy Tudásbázisban. A kinyerést és a dúsítást az indexelési folyamathoz csatolt *kognitív képességek* valósítják meg. A szolgáltatásba beépített kognitív képességek a következő kategóriákba sorolhatók: 

+ A **természetes nyelvi feldolgozási** képességek közé tartozik az [entitások felismerése](cognitive-search-skill-entity-recognition.md), a [nyelvfelismerés](cognitive-search-skill-language-detection.md), a [kulcsfontosságú kifejezés kinyerése](cognitive-search-skill-keyphrases.md), a szöveg-manipuláció és a [hangulat észlelése](cognitive-search-skill-sentiment.md). Ezekkel a képességekkel a strukturálatlan szöveg feltételezheti, hogy az indexben kereshető és szűrhető mezőkként leképezett új űrlapok is megadhatók.

+ A **képfeldolgozási** képességek közé tartozik az [optikai karakterfelismerés (OCR)](cognitive-search-skill-ocr.md) és a [vizuális funkciók](cognitive-search-skill-image-analysis.md)azonosítása, például az arc észlelése, a képek értelmezése, a képfelismerés (híres személyek és tereptárgyak) vagy az attribútumok, például a színek vagy a képek tájolása. Az Azure Cognitive Search összes lekérdezési funkciójának használatával szöveget hozhat létre a képtartalom ábrázolásával.

![Dúsítási folyamat diagramja](./media/cognitive-search-intro/cogsearch-architecture.png "a dúsítási folyamat áttekintése")

Az Azure Cognitive Search kognitív képességei az előre betanított gépi tanulási modelleken alapulnak Cognitive Services API-k: [Computer Vision](https://docs.microsoft.com/azure/cognitive-services/computer-vision/) és [text Analysis](https://docs.microsoft.com/azure/cognitive-services/text-analytics/overview). 

A természetes nyelv és a képfeldolgozás az adatfeldolgozási fázisban történik, és az eredmények egy dokumentum összeállításának részévé válnak az Azure Cognitive Search kereshető indexében. Az adatforrások Azure-adatkészletként vannak kialakítva, majd egy indexelési folyamaton keresztül leküldve, amely a szükséges [beépített képességekkel](cognitive-search-predefined-skills.md) rendelkezik. Az architektúra bővíthető, így ha a beépített képességek nem elegendőek, létrehozhat és csatolhat [Egyéni képességeket](cognitive-search-create-custom-skill-example.md) az egyéni feldolgozás integrálásához. Ilyenek például a pénzügy, a tudományos publikációk vagy az orvostudomány egy adott tartományhoz tartozó egyéni entitás-modul vagy dokumentum-osztályozó.

> [!NOTE]
> Ha a hatókört a feldolgozás gyakoriságának növelésével, további dokumentumok hozzáadásával vagy további AI-algoritmusok hozzáadásával bővíti, akkor [a számlázható Cognitive Services erőforrást kell csatolnia](cognitive-search-attach-cognitive-services.md). Az API-k Cognitive Services-ben való meghívásakor felmerülő díjak, valamint a képek kinyerése a dokumentum repedésének részeként az Azure Cognitive Searchban. A dokumentumokból való szöveg kinyerése díjmentes.
>
> A beépített készségek elvégzése a meglévő Cognitive Services utólagos elszámolású [díjszabás szerint](https://azure.microsoft.com/pricing/details/cognitive-services/)történik. A rendszerkép kibontásának díjszabását az [Azure Cognitive Search díjszabási oldalán](https://go.microsoft.com/fwlink/?linkid=2042400)találja.

## <a name="when-to-use-cognitive-skills"></a>Mikor kell használni a kognitív képességeket

Érdemes megfontolni a beépített kognitív ismeretek használatát, ha a nyers tartalma strukturálatlan szöveg, képtartalom vagy olyan tartalom, amely nyelvi észlelést és fordítást igényel. Ha a mesterséges intelligenciát a beépített kognitív ismeretek használatával szeretné feloldani, a keresési és adatelemzési alkalmazásaiban megnövelheti a tartalmat, és növelheti annak értékét és hasznosságát. 

Emellett érdemes lehet egyéni képességet is felvennie, ha olyan nyílt forráskódú, harmadik féltől származó vagy első féltől származó kóddal rendelkezik, amelyet be szeretne építeni a folyamatba. A különböző dokumentumtípusok kiugró jellemzőit azonosító besorolási modellek ebbe a kategóriába tartoznak, de a tartalomhoz hozzáadott csomagok is felhasználhatók.

### <a name="more-about-built-in-skills"></a>További információ a beépített képességekről

A beépített képességekkel összeállított készségkészlet jól illeszkedik a következő alkalmazási forgatókönyvekhez:

+ A teljes szöveges keresést végezni kívánó beolvasott dokumentumok (JPEG). A JPEG-fájlok szövegének azonosításához, kinyeréséhez és betöltéséhez egy optikai karakterfelismerési (OCR) képességet is csatolhat.

+ Kombinált képpel és szöveggel rendelkező PDF-fájlok. A PDF-fájlokban lévő szöveg kinyerhető az indexelés során a dúsítási lépések használata nélkül, de a képek és a természetes nyelvi feldolgozás hozzáadása gyakran jobb eredményt eredményezhet, mint a szabványos indexelés.

+ Többnyelvű tartalom, amelyre alkalmazni kívánja a nyelvi észlelést és a szöveges fordítást.

+ Strukturálatlan vagy részben strukturált dokumentumok, amelyek olyan tartalmat tartalmaznak, amely a nagyobb dokumentumban rejtett jelentéssel vagy kontextussal rendelkezik. 

  A Blobok különösen gyakran tartalmaznak egy olyan nagy méretű tartalmat, amely egyetlen "mező"be van csomagolva. Ha rendszerképeket és természetes nyelvi feldolgozási képességeket csatol egy indexelő alkalmazáshoz, létrehozhat olyan új adatokat, amelyek a nyers tartalomban is fennmaradnak, de másképp nem különálló mezőkként. Néhány használatra kész, beépített kognitív képesség, amely segítséget nyújt a kulcsfontosságú kifejezés kinyeréséhez, a hangulat elemzéséhez és az entitások felismeréséhez (személyek, szervezetek és helyszínek).

  Emellett a beépített készségek is használhatók a tartalom átstrukturálása a szöveg felosztása, egyesítése és alakja műveletekkel.

### <a name="more-about-custom-skills"></a>További információ az egyéni képességekről

Az egyéni képességek olyan összetettebb forgatókönyveket is támogatnak, mint például az űrlapok felismerése vagy az egyéni entitások észlelése az Ön által megadott és az [Egyéni szakértelem webes felületén](cognitive-search-custom-skill-interface.md)beburkolt modell használatával. Az egyéni képességek számos példája például az [űrlap-felismerő](/azure/cognitive-services/form-recognizer/overview), a [Bing Entity Search API](https://docs.microsoft.com/azure/search/cognitive-search-create-custom-skill-example)integrációja és az [egyéni entitások felismerése](https://github.com/Microsoft/SkillsExtractorCognitiveSearch).


## <a name="components-of-an-enrichment-pipeline"></a>A dúsítási folyamat összetevői

A dúsítási folyamat olyan [*Indexelő*](search-indexer-overview.md) eszközökön alapul, amelyek az adatforrásokat térképezik fel, és a végpontok közötti index feldolgozását biztosítják. A szaktudás mostantól az indexelő anyagokhoz van csatolva, a dokumentumok elfogása és gazdagítása az Ön által meghatározott készségkészlet szerint. Az indexelés után az [Azure Cognitive Search által támogatott összes lekérdezési típuson](search-query-overview.md)keresztül érheti el a tartalmat.  Ha még nem ismeri az indexeket, ez a szakasz végigvezeti a lépéseken.

### <a name="step-1-connection-and-document-cracking-phase"></a>1\. lépés: a kapcsolatok és a dokumentumok repedésének fázisa

A folyamat elején strukturálatlan szöveggel vagy nem szöveges tartalommal (például kép és beolvasott dokumentum JPEG-fájlok) rendelkezik. Az adattáraknak olyan Azure-beli adattárolási szolgáltatásban kell lenniük, amely egy indexelő segítségével érhető el. Az indexelő "kiváló" forrás dokumentumokat gyűjthetnek a forrásadatokből származó szöveg kinyeréséhez.

![A dokumentum repedésének fázisa](./media/cognitive-search-intro/document-cracking-phase-blowup.png "a dokumentum repedése")

 A támogatott források közé tartozik az Azure Blob Storage, az Azure Table Storage, a Azure SQL Database és az Azure Cosmos DB. A szöveges tartalmat a következő fájltípusokból lehet kinyerni: PDF-fájlok, Word-, PowerPoint-és CSV-fájlok. A teljes listát lásd: [támogatott formátumok](search-howto-indexing-azure-blob-storage.md#supported-document-formats).

### <a name="step-2-cognitive-skills-and-enrichment-phase"></a>2\. lépés: a kognitív képességek és a gazdagodás fázisa

A gazdagodás az atomi műveleteket végző *kognitív képességeken* keresztül történik. Ha például egy PDF-fájlból szöveges tartalmat használ, alkalmazhatja az entitás-felismerési nyelvfelismerés vagy a fő kifejezés kinyerése lehetőséget, hogy az indexben olyan új mezőket hozzon létre, amelyek nem érhetők el natív módon a forrásban. A folyamat során használt szaktudás gyűjteményét teljes egészében *készségkészlet*nevezzük.  

![Dúsítási fázis](./media/cognitive-search-intro/enrichment-phase-blowup.png "dúsítási fázis")

A készségkészlet a [beépített kognitív képességek](cognitive-search-predefined-skills.md) vagy az Ön által biztosított [egyéni képességek](cognitive-search-create-custom-skill-example.md) , valamint a készségkészlet való kapcsolódás alapján történik. A készségkészlet minimális vagy nagyon összetett lehet, és nem csak a feldolgozás típusát, hanem a műveletek sorrendjét is meghatározhatja. A készségkészlet és az indexelő részeként definiált mező-hozzárendelések teljes mértékben a dúsítási folyamatot határozzák meg. További információ ezekről a darabokról: [Készségkészlet definiálása](cognitive-search-defining-skillset.md).

Belsőleg a folyamat gazdagított dokumentumok gyűjteményét hozza létre. Eldöntheti, hogy a bővített dokumentumok mely részeit kell leképezni a keresési index indexelhető mezőihez. Ha például alkalmazotta a fő kifejezéseket és az entitás-felismerési képességeket, akkor ezek az új mezők a dúsított dokumentum részévé válnak, és az index mezőire képezhetők le. A bemeneti/kimeneti formációkkal kapcsolatos további tudnivalókért tekintse meg a [megjegyzéseket](cognitive-search-concept-annotations-syntax.md) .

#### <a name="add-a-knowledgestore-element-to-save-enrichments"></a>KnowledgeStore elem hozzáadása a dúsítások mentéséhez

[Keresési REST API-Version = 2019-05 -06 – az előzetes verzió](search-api-preview.md) kibővíti a szakértelmével-t egy olyan `knowledgeStore`-definícióval, amely egy Azure Storage-kapcsolattal és-vetítéssel foglalkozik, amely leírja a dúsítások tárolási módját. 

Ha egy tudásbázist ad hozzá egy készségkészlet, lehetővé teszi a teljes szöveges kereséstől eltérő forgatókönyvek megjelenítését a bővítések számára. További információ: [Knowledge Store (előzetes verzió)](knowledge-store-concept-intro.md).

### <a name="step-3-search-index-and-query-based-access"></a>3\. lépés: a keresési index és a lekérdezésen alapuló hozzáférés

A feldolgozás befejezésekor egy olyan keresési index található, amely az Azure Cognitive Searchban teljes mértékben kereshető dokumentumokból áll. [Az index lekérdezése](search-query-overview.md) , hogy a fejlesztők és a felhasználók hogyan érhetik el a folyamat által generált dúsított tartalmat. 

![Index keresési ikonnal](./media/cognitive-search-intro/search-phase-blowup.png "Index keresési ikonnal")

Az index olyan, mint bármely más, amelyet az Azure Cognitive Search hoz létre: kiegészítheti az egyéni elemzőket, a fuzzy keresési lekérdezések meghívását, a szűrt keresés hozzáadását, illetve a keresési eredmények átalakítására szolgáló pontozási profilokkal való kísérletezést.

Az indexek olyan index-sémából jönnek létre, amely meghatározza az adott indexhez csatolt mezőket, attribútumokat és egyéb szerkezeteket, például a pontozási profilokat és a szinonimákat. Az indexek definiálása és feltöltése után a rendszer növekményes módon indexelheti az új és a frissített forrásdokumentum-dokumentumokat. Bizonyos módosítások teljes újraépítést igényelnek. Kis adatkészletet kell használnia, amíg a séma kialakítása nem stabil. A további tudnivalókért lásd az [indexek újraépítését](search-howto-reindex.md) ismertető cikket.

<a name="feature-concepts"></a>

## <a name="key-features-and-concepts"></a>Alapfunkciók és -fogalmak

| Fogalom | Leírás| Hivatkozások |
|---------|------------|-------|
| készségkészlet | A legfelső szintű névvel ellátott erőforrás, amely a szaktudás gyűjteményét tartalmazza. A készségkészlet a dúsítási folyamat. Egy indexelő indexelése során hívja meg. | Lásd: [Készségkészlet megadása](cognitive-search-defining-skillset.md) |
| Kognitív képességek | Egy dúsítási folyamat atomi átalakítása. Gyakran előfordul, hogy egy olyan összetevő, amely kinyeri vagy kikövetkezteti a struktúrát, így fokozza a bemeneti adatok megértését. Szinte minden esetben a kimenet Text-alapú, a feldolgozás pedig természetes nyelvi feldolgozás vagy képfeldolgozás, amely a képbemenetek szövegét kinyeri vagy hozza létre. A szaktudás kimenete egy index egy mezőjéhez rendelhető hozzá, vagy az alsóbb rétegbeli dúsítás bemenetként használható. A szaktudás előre definiált és a Microsoft által biztosított, vagy egyéni: Ön által létrehozott és telepített. | [Beépített kognitív képességek](cognitive-search-predefined-skills.md) |
| Adatbányászat | A feldolgozás széles körét fedi le, de az AI-bővítésre is vonatkozik, az entitás-felismerési képesség általában az adatok (entitások) olyan forrásból való kinyerésére szolgál, amely nem biztosítja az adott információt natív módon. | Lásd: [entitás-felismerési szakértelem](cognitive-search-skill-entity-recognition.md) és [dokumentum-kinyerési képesség (előzetes verzió)](cognitive-search-skill-document-extraction.md)| 
| Képfeldolgozás | Kikövetkezteti a szöveget egy képből, például felismerhetővé teszi a tereptárgyak felismerését vagy szöveg kinyerését egy képből. Gyakori példák a beolvasott dokumentumokból (JPEG-fájlokból) származó karakterek feloldására szolgáló OCR-t, illetve az utca nevének felismerését egy utcai aláírást tartalmazó fényképen. | Lásd: [képelemzési szakértelem](cognitive-search-skill-image-analysis.md) vagy [OCR-képesség](cognitive-search-skill-ocr.md)
| Természetes nyelvek feldolgozása | Szöveg-feldolgozás a szöveges bemenetekkel kapcsolatos információkhoz és adatokhoz. A nyelvfelismerés, a hangulat elemzése és a kulcsfontosságú kifejezés kinyerése a természetes nyelvi feldolgozás alá eső képességek.  | Lásd: [kulcsszókeresés skill](cognitive-search-skill-keyphrases.md), [nyelvfelismerés skill](cognitive-search-skill-language-detection.md), [text Translation skill (előzetes verzió)](cognitive-search-skill-text-translation.md), [Hangulatelemzés skill](cognitive-search-skill-sentiment.md) |
| A dokumentum repedése | Szöveges tartalom kinyerésének vagy létrehozásának folyamata a nem szöveges forrásokból az indexelés során. Az optikai karakterfelismerés (OCR) egy példa, de általában az alapszintű indexelő funkcióra hivatkozik, mivel az indexelő Kinyeri a tartalmat az alkalmazás fájljaiból. A forrásfájl helyét biztosító adatforrás és az indexelő definíciója, amely a mezők leképezéseit biztosítja, a dokumentumok repedésének kulcsfontosságú tényezői. | Lásd: [Indexelő – áttekintés](search-indexer-overview.md) |
| Alakításában | Összevonhatja a szöveges töredékeket egy nagyobb struktúrába, vagy megfordíthatja a nagyobb szöveges adattömböket egy kezelhető méretre a további alsóbb rétegbeli feldolgozás érdekében. | Lásd: [formáló képesség](cognitive-search-skill-shaper.md), [szöveges egyesítési szakértelem](cognitive-search-skill-textmerger.md), [szöveg felosztása](cognitive-search-skill-textsplit.md) készség |
| Dúsított dokumentumok | Egy átmeneti belső struktúra, amely a feldolgozás során keletkezik, és a végső kimenet egy keresési indexben jelenik meg. A készségkészlet határozza meg, hogy mely dúsítások vannak elvégezve. A mező-hozzárendelések határozzák meg, hogy mely adatelemek legyenek hozzáadva az indexhez. Ha szeretné, létrehozhat egy tudásbázist, amellyel megőrizheti és felderítheti a dúsított dokumentumokat olyan eszközökkel, mint a Storage Explorer, a Power BI vagy bármely más eszköz, amely az Azure Blob Storage-hoz csatlakozik. | Lásd: [Knowledge Store (előzetes verzió)](knowledge-store-concept-intro.md) |
| Indexelő |  Egy webbejáró, amely Kinyeri a kereshető adatokat és metaadatokat egy külső adatforrásból, és feltölti az indexet az index és az adatforrása közötti mező-mező leképezések alapján. Az AI-bővítésekhez az indexelő meghívja a készségkészlet, és tartalmazza azokat a mező-hozzárendeléseket, amelyek a dúsítási kimenetet társítják az index mezőihez. Az indexelő definíciója a feldolgozási műveletek összes utasítását és hivatkozását tartalmazza, a folyamat pedig az indexelő futtatásakor lesz meghívva. A további beállításokkal újra felhasználhatja a meglévő feldolgozást, és csak azokat a lépéseket és képességeket hajthatja végre, amelyek módosultak. | Lásd: [Indexelő](search-indexer-overview.md) és [növekményes indexelés (előzetes verzió)](cognitive-search-incremental-indexing-conceptual.md). |
| Adatforrás  | Az indexelő által az Azure-ban támogatott típusok külső adatforráshoz való kapcsolódásra használt objektum. | Lásd: [Indexelő – áttekintés](search-indexer-overview.md) |
| Index | Egy megőrzött keresési index az Azure Cognitive Searchban, amely egy olyan index-sémából épül, amely meghatározza a mező szerkezetét és használatát. | Lásd: [alapszintű index létrehozása](search-what-is-an-index.md) | 
| Tudástár | Olyan Storage-fiók, amelyben a keresési indexen kívül a dúsított dokumentumok formázható és kiterjeszthetők | Lásd: [a Knowledge Store bemutatása](knowledge-store-concept-intro.md) | 
| Indexelő gyorsítótár | A Storage-fiókhoz tartozó szaktudás kimeneteit az indexelő gyorsítótárazza. A gyorsítótár lehetővé teszi a indexeer számára, hogy csökkentse a nagy mennyiségű dokumentum újrafeldolgozásának költségeit a készségkészlet szerkesztésekor. | Lásd: [növekményes indexelés](cognitive-search-incremental-indexing-conceptual.md) | 

<a name="where-do-i-start"></a>

## <a name="where-do-i-start"></a>Hogyan kezdjek hozzá?

**1. lépés: [Azure Cognitive Search-erőforrás létrehozása](search-create-service-portal.md)** 

**2. lépés: próbálja ki néhány rövid útmutatót és példát a gyakorlati tapasztalatokra**

+ [Gyors útmutató (portál)](cognitive-search-quickstart-blob.md)
+ [Oktatóanyag (HTTP-kérések)](cognitive-search-tutorial-blob.md)
+ [Példa: egyéni képesség létrehozása AI-dúsításhoz (C#)](cognitive-search-create-custom-skill-example.md)

Javasoljuk, hogy az ingyenes szolgáltatás tanulási célokra legyen elérhető, azonban az ingyenes tranzakciók száma napi 20 dokumentumra korlátozódik. Ha a gyors üzembe helyezést és az oktatóanyagot egy nap alatt szeretné futtatni, használjon kisebb fájlméretet (10 dokumentumot), hogy mindkét gyakorlatban illeszkedjen, vagy törölje a gyors útmutatóban vagy oktatóanyagban használt indexelő, hogy a számlálót nulla értékre állítsa.

**3. lépés: az API áttekintése**

A REST `api-version=2019-05-06` a kéréseken vagy a .NET SDK-ban is használható. Ha a Knowledge Store-t vizsgálja, használja helyette az előzetes verziójú REST API (`api-version=2019-05-06-Preview`).

Ez a lépés a REST API-kkal hozza létre az AI-gazdagító megoldást. Az AI-bővítéshez csak két API van hozzáadva vagy kibővítve. Más API-k ugyanazzal a szintaxissal rendelkeznek, mint az általánosan elérhető verziók.

| REST API | Leírás |
|-----|-------------|
| [Adatforrás létrehozása](https://docs.microsoft.com/rest/api/searchservice/create-data-source)  | Egy olyan erőforrás, amely egy külső adatforrást azonosít, amely a dúsított dokumentumok létrehozásához használt forrásadatokat biztosít.  |
| [Készségkészlet létrehozása (API-Version = 2019-05-06)](https://docs.microsoft.com/rest/api/searchservice/create-skillset)  | Ez az API kifejezetten az AI-bővítésre vonatkozik. Ez egy olyan erőforrás, amely összehangolja a [beépített készségek](cognitive-search-predefined-skills.md) használatát és a dúsítási folyamat során használt [Egyéni kognitív képességeket](cognitive-search-custom-skill-interface.md) az indexelés során. |
| [Index létrehozása](https://docs.microsoft.com/rest/api/searchservice/create-index)  | Egy Azure Cognitive Search indexet kifejező séma. Az indexben található mezők a forrásadatok mezőire vagy a dúsítási fázisban előállított mezőkre (például az entitások felismerése által létrehozott szervezeti nevekre vonatkozó mezőre) vonatkoznak. |
| [Indexelő létrehozása (API-Version = 2019-05-06)](https://docs.microsoft.com/rest/api/searchservice/create-skillset)  | Az indexelés során használt összetevőket meghatározó erőforrás: beleértve az adatforrást, a készségkészlet, a forrás-és a közbenső adatstruktúrákat a célként megadott indexbe, valamint magát az indexet. Az indexelő futtatása az adatfeldolgozáshoz és a dúsításhoz használt trigger. A kimenet egy olyan keresési index, amely az index sémán alapul, és a forrásadatok alapján van feltöltve, és a szakértelmével-n keresztül dúsított. Ez a meglévő API a készségkészlet tulajdonság bevonásával kognitív keresési helyzetekben bővíthető. |

**Ellenőrzőlista: egy tipikus munkafolyamat**

1. Az Azure-forrásadatok részhalmaza egy reprezentatív mintának. Az indexelés időt vesz igénybe, hogy egy kisebb, reprezentatív adatkészletet hozzon létre, majd fokozatosan felépítse a megoldás idejére.

1. Hozzon létre egy [adatforrás-objektumot](https://docs.microsoft.com/rest/api/searchservice/create-data-source) az Azure Cognitive Searchban, és adjon meg egy kapcsolódási karakterláncot az adatok lekéréséhez.

1. Hozzon létre egy [készségkészlet](https://docs.microsoft.com/rest/api/searchservice/create-skillset) a gazdagodás lépéseivel.

1. Adja meg az [index sémát](https://docs.microsoft.com/rest/api/searchservice/create-index). A *mezők* gyűjteménye mezőket tartalmaz a forrásadatokből. Emellett további mezőket kell kimutatnia, amelyekkel megtarthatja a gazdagodás során létrehozott tartalomhoz generált értékeket.

1. Az adatforrásra, a készségkészlet és az indexre hivatkozó [Indexelő](https://docs.microsoft.com/rest/api/searchservice/create-skillset) definiálása.

1. Az indexelő alkalmazásban adja hozzá a *outputFieldMappings*. Ez a szakasz a készségkészlet (a 3. lépésben) lévő kimeneteket a tárgymutató sémájának bemenet mezőibe (a 4. lépésben) képezi le.

1. Küldje el az imént létrehozott *Indexer* -kérést (egy post-kérést a kérelem törzsében lévő indexelő definícióval), hogy kifejezze az indexelő az Azure Cognitive Searchban. Ez a lépés az indexelő futtatásának módja, a folyamat meghívása.

1. Lekérdezések futtatása az eredmények kiértékeléséhez és a kód módosítása a szakértelmével, a séma vagy az indexelő konfigurációjának frissítéséhez.

1. Az [Indexelő alaphelyzetbe állítása](search-howto-reindex.md) a folyamat újraépítése előtt.

További információ az adott kérdésekről vagy problémákról: [hibaelhárítási tippek](cognitive-search-concept-troubleshooting.md).

## <a name="next-steps"></a>Következő lépések

+ [AI-gazdagító dokumentációs hivatkozások](cognitive-search-resources-documentation.md)
+ [Gyors útmutató: az AI-gazdagítás kipróbálása egy portálon](cognitive-search-quickstart-blob.md)
+ [Oktatóanyag: az AI-gazdagító API-k megismerése](cognitive-search-tutorial-blob.md)
+ [Knowledge Store (előzetes verzió)](knowledge-store-concept-intro.md)
+ [Útmutató a Knowledge Store-hoz](knowledge-store-howto.md)
