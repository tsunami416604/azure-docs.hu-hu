---
title: A kognitív keresés és az AI-bővítés bemutatása – Azure Search
description: Tartalom kinyerése, természetes nyelvi feldolgozás (NLP) és képfeldolgozás, kereshető tartalom létrehozása Azure Search indexeléshez a kognitív képességek és AI-algoritmusok használatával.
manager: nitinme
author: HeidiSteen
services: search
ms.service: search
ms.subservice: cognitive-search
ms.topic: overview
ms.date: 08/15/2019
ms.author: heidist
ms.openlocfilehash: 89c56496ed1f356ffedf4df93f1728bd4acda323
ms.sourcegitcommit: 36e9cbd767b3f12d3524fadc2b50b281458122dc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/20/2019
ms.locfileid: "69639957"
---
# <a name="what-is-cognitive-search-in-azure-search"></a>Mi a "kognitív keresés" a Azure Searchban?

A kognitív keresés a Azure Search mesterséges intelligencia szolgáltatása, amely képekből, blobokból és egyéb strukturálatlan adatforrásokból származó szöveg kinyerésére szolgál – a tartalom gazdagítása, hogy a Azure Search indexben jobban kereshető legyen. A kinyerést és a dúsítást az indexelési folyamathoz csatolt *kognitív képességek* valósítják meg. Az AI-bővítések a következő módokon támogatottak: 

+ A **természetes nyelvi feldolgozási** képességek közé tartozik az [entitások felismerése](cognitive-search-skill-entity-recognition.md), a [nyelvfelismerés](cognitive-search-skill-language-detection.md), a [kulcsfontosságú kifejezés](cognitive-search-skill-keyphrases.md)kinyerése, a szöveg-manipuláció és a [hangulat észlelése](cognitive-search-skill-sentiment.md). Ezekkel a képességekkel a strukturálatlan szöveg feltételezheti, hogy az indexben kereshető és szűrhető mezőkként leképezett új űrlapok is megadhatók.

+ A képfeldolgozási képességek közé tartozik az [optikai karakterfelismerés (OCR)](cognitive-search-skill-ocr.md) és a [vizuális funkciók](cognitive-search-skill-image-analysis.md)azonosítása, például az Arcfelismerés, a képek értelmezése, a képfelismerés (híres személyek és tereptárgyak) vagy a hasonló attribútumok színek vagy képek tájolása Az Azure Search összes lekérdezési funkciójának használatával szöveget hozhat létre a képtartalom ábrázolásával.

![Kognitív keresési folyamat diagramja] A (./media/cognitive-search-intro/cogsearch-architecture.png "kognitív keresési folyamatok áttekintése")

A Azure Search kognitív képességei a Cognitive Services API-k Machine learning-modelljein alapulnak: [Computer Vision](https://docs.microsoft.com/azure/cognitive-services/computer-vision/) és [szöveg elemzése](https://docs.microsoft.com/azure/cognitive-services/text-analytics/overview). 

A természetes nyelv és a képfeldolgozás az adatfeldolgozási fázisban történik, és az eredmények a dokumentumok összeállításának részévé válnak a Azure Search kereshető indexében. Az adatforrások Azure-adatkészletként vannak kialakítva, majd egy indexelési folyamaton keresztül leküldve, amely a szükséges [beépített képességekkel](cognitive-search-predefined-skills.md) rendelkezik. Az architektúra bővíthető, így ha a beépített képességek nem elegendőek, létrehozhat és csatolhat [Egyéni képességeket](cognitive-search-create-custom-skill-example.md) az egyéni feldolgozás integrálásához. Ilyenek például a pénzügy, a tudományos publikációk vagy az orvostudomány egy adott tartományhoz tartozó egyéni entitás-modul vagy dokumentum-osztályozó.

> [!NOTE]
> Ha a hatókört a feldolgozás gyakoriságának növelésével, további dokumentumok hozzáadásával vagy további AI-algoritmusok hozzáadásával bővíti, akkor [a számlázható Cognitive Services erőforrást](cognitive-search-attach-cognitive-services.md)kell csatolnia. Az API-k Cognitive Services-ben való meghívásakor felmerülő díjak, valamint a képek kinyerése a dokumentum repedési szakaszának részeként Azure Search. A dokumentumokból való szöveg kinyerése díjmentes.
>
> A beépített készségek elvégzése a meglévő Cognitive Services utólagos elszámolású [díjszabás szerint](https://azure.microsoft.com/pricing/details/cognitive-services/)történik. A rendszerkép kibontásának díjszabását a [Azure Search díjszabási oldalán](https://go.microsoft.com/fwlink/?linkid=2042400)találja.

## <a name="when-to-use-cognitive-search"></a>Mikor kell használni a kognitív keresést?

Az előre elkészített ismeretekkel rendelkező kognitív keresés jól illeszkedik a következő alkalmazási forgatókönyvekhez:

+ A teljes szöveges keresést végezni kívánó beolvasott dokumentumok (JPEG). A JPEG-fájlok szövegének azonosításához, kinyeréséhez és betöltéséhez egy optikai karakterfelismerési (OCR) képességet is csatolhat.

+ Kombinált képpel és szöveggel rendelkező PDF-fájlok. A PDF-fájlok szövege kinyerhető Azure Search indexelés során a kognitív keresés használata nélkül, de a képek és a természetes nyelvi feldolgozás hozzáadásával gyakran jobb eredményt hozhat, mint a szabványos indexelés.

+ Többnyelvű tartalom, amelyre alkalmazni kívánja a nyelvi észlelést és a szöveges fordítást.

+ Strukturálatlan vagy részben strukturált dokumentumok, amelyek olyan tartalmat tartalmaznak, amely a nagyobb dokumentumban rejtett jelentéssel vagy kontextussal rendelkezik. 

  A Blobok különösen gyakran tartalmaznak egy olyan nagy méretű tartalmat, amely egyetlen "mező"be van csomagolva. Ha rendszerképeket és természetes nyelvi feldolgozási képességeket csatol egy indexelő alkalmazáshoz, létrehozhat olyan új adatokat, amelyek a nyers tartalomban is fennmaradnak, de másképp nem különálló mezőkként. Néhány használatra kész, beépített kognitív képesség, amely segítséget nyújt a kulcsfontosságú kifejezés kinyeréséhez, a hangulat elemzéséhez és az entitások felismeréséhez (személyek, szervezetek és helyszínek).

  Emellett az előre elkészített képességek is használhatók a tartalom átstrukturálása a szöveg felosztása, egyesítése és alakja műveletekkel.

Az egyéni képességek olyan összetettebb forgatókönyveket is támogatnak, mint például az űrlapok felismerése vagy az egyéni entitások észlelése az Ön által megadott és az [Egyéni szakértelem webes felületén](cognitive-search-custom-skill-interface.md)beburkolt modell használatával. Az egyéni képességek számos példája például az [űrlap-felismerő](), a [Bing Entity Search API](https://docs.microsoft.com/azure/search/cognitive-search-create-custom-skill-example)integrációja és az [egyéni entitások felismerése](https://github.com/Microsoft/SkillsExtractorCognitiveSearch).


## <a name="component-pipeline-of-cognitive-search"></a>A kognitív keresés összetevő-folyamata

A kognitív keresési folyamat olyan [Azure Search indexelő ](search-indexer-overview.md) anyagokon alapul, amelyek adatforrásokat térképeznek fel, és a végpontok közötti index feldolgozását biztosítják. A szaktudás mostantól az indexelő anyagokhoz van csatolva, a dokumentumok elfogása és gazdagítása az Ön által meghatározott készségkészlet szerint. Az indexelés után a [Azure Search által támogatott](search-query-overview.md)összes lekérdezési típuson keresztül érheti el a tartalmat.  Ha még nem ismeri az indexeket, ez a szakasz végigvezeti a lépéseken.

### <a name="step-1-connection-and-document-cracking-phase"></a>1\. lépés: A kapcsolatok és a dokumentumok repedésének fázisa

A folyamat elején strukturálatlan szöveggel vagy nem szöveges tartalommal (például kép és beolvasott dokumentum JPEG-fájlok) rendelkezik. Az adattáraknak olyan Azure-beli adattárolási szolgáltatásban kell lenniük, amely egy indexelő segítségével érhető el. Az indexelő "kiváló" forrás dokumentumokat gyűjthetnek a forrásadatokből származó szöveg kinyeréséhez.

A ![dokumentum repedésének fázisa] a (./media/cognitive-search-intro/document-cracking-phase-blowup.png "dokumentum repedése")

 A támogatott források közé tartozik az Azure Blob Storage, az Azure Table Storage, a Azure SQL Database és az Azure Cosmos DB. A szöveges tartalom a következő fájltípusokból is kinyerhető: PDF-fájlok, Word-, PowerPoint-és CSV-fájlok. A teljes listát lásd: [támogatott formátumok](search-howto-indexing-azure-blob-storage.md#supported-document-formats).

### <a name="step-2-cognitive-skills-and-enrichment-phase"></a>2\. lépés: Kognitív képességek és dúsítási fázis

A gazdagodás az atomi műveleteket végző *kognitív képességeken* keresztül történik. Ha például egy PDF-fájlból szöveges tartalmat használ, alkalmazhatja az entitás-felismerési nyelvfelismerés vagy a fő kifejezés kinyerése lehetőséget, hogy az indexben olyan új mezőket hozzon létre, amelyek nem érhetők el natív módon a forrásban. A folyamat során használt szaktudás gyűjteményét teljes egészében *készségkészlet*nevezzük.  

![Dúsítási fázis](./media/cognitive-search-intro/enrichment-phase-blowup.png "dúsítási fázis")

A készségkészlet az [előre meghatározott kognitív képességek](cognitive-search-predefined-skills.md) vagy az Ön által biztosított [egyéni képességek](cognitive-search-create-custom-skill-example.md) , valamint a készségkészlet való kapcsolódás alapján történik. A készségkészlet minimális vagy nagyon összetett lehet, és nem csak a feldolgozás típusát, hanem a műveletek sorrendjét is meghatározhatja. A készségkészlet és az indexelő részeként definiált mező-hozzárendelések teljes mértékben a dúsítási folyamatot határozzák meg. További információ ezekről a darabokról: [Készségkészlet definiálása](cognitive-search-defining-skillset.md).

Belsőleg a folyamat gazdagított dokumentumok gyűjteményét hozza létre. Eldöntheti, hogy a bővített dokumentumok mely részeit kell leképezni a keresési index indexelhető mezőihez. Ha például alkalmazotta a fő kifejezéseket és az entitás-felismerési képességeket, akkor ezek az új mezők a dúsított dokumentum részévé válnak, és az index mezőire képezhetők le. A bemeneti/kimeneti formációkkal kapcsolatos további tudnivalókért tekintse meg a [megjegyzéseket](cognitive-search-concept-annotations-syntax.md) .

#### <a name="add-a-knowledgestore-element-to-save-enrichments"></a>KnowledgeStore elem hozzáadása a dúsítások mentéséhez

A [Search szolgáltatás REST API-verziója = 2019-05-06](search-api-preview.md) kibővíti a szakértelmével egy olyan knowledgeStore-definícióval, amely egy Azure Storage-kapcsolattal és-kivetítésekkel rendelkezik, amelyek leírják a dúsítások tárolási módját. 

Ha egy tudásbázist ad hozzá egy készségkészlet, lehetővé teszi a teljes szöveges kereséstől eltérő forgatókönyvek megjelenítését a bővítések számára. További információ: [Mi az a Knowledge Store](knowledge-store-concept-intro.md).

### <a name="step-3-search-index-and-query-based-access"></a>3\. lépés: Keresési index és lekérdezésen alapuló hozzáférés

Ha elkészült a feldolgozással, a rendszer olyan keresési indexet tartalmaz, amely a Azure Searchban található, teljes szöveges kereséssel rendelkező, dúsított dokumentumokból áll. [Az index lekérdezése](search-query-overview.md) , hogy a fejlesztők és a felhasználók hogyan érhetik el a folyamat által generált dúsított tartalmat. 

![Index keresési ikonnal](./media/cognitive-search-intro/search-phase-blowup.png "Index keresési ikonnal")

Az index olyan, mint bármely más, amelyet a Azure Search hozhat létre: kiegészítheti az egyéni elemzőket, a fuzzy keresési lekérdezések meghívását, a szűrt keresés hozzáadását, illetve a keresési eredmények átformálásához a pontozási profilokkal való kísérletezést.

Az indexek olyan index-sémából jönnek létre, amely meghatározza az adott indexhez csatolt mezőket, attribútumokat és egyéb szerkezeteket, például a pontozási profilokat és a szinonimákat. Az indexek definiálása és feltöltése után a rendszer növekményes módon indexelheti az új és a frissített forrásdokumentum-dokumentumokat. Bizonyos módosítások teljes újraépítést igényelnek. Kis adatkészletet kell használnia, amíg a séma kialakítása nem stabil. A további tudnivalókért lásd az [indexek újraépítését](search-howto-reindex.md) ismertető cikket.

<a name="feature-concepts"></a>

## <a name="key-features-and-concepts"></a>A legfontosabb jellemzők és fogalmak

| Fogalom | Leírás| Hivatkozások |
|---------|------------|-------|
| Készségkészlet | A legfelső szintű névvel ellátott erőforrás, amely a szaktudás gyűjteményét tartalmazza. A készségkészlet a dúsítási folyamat. Egy indexelő indexelése során hívja meg. | [Készségkészlet definiálása](cognitive-search-defining-skillset.md) |
| Kognitív képességek | Egy dúsítási folyamat atomi átalakítása. Gyakran előfordul, hogy egy olyan összetevő, amely kinyeri vagy kikövetkezteti a struktúrát, így fokozza a bemeneti adatok megértését. Szinte minden esetben a kimenet Text-alapú, a feldolgozás pedig természetes nyelvi feldolgozás vagy képfeldolgozás, amely a képbemenetek szövegét kinyeri vagy hozza létre. A szaktudás kimenete egy index egy mezőjéhez rendelhető hozzá, vagy az alsóbb rétegbeli dúsítás bemenetként használható. A szaktudás előre definiált és a Microsoft által biztosított, vagy egyéni: Ön által létrehozott és telepített. | [Előre definiált képességek](cognitive-search-predefined-skills.md) |
| Adatok kinyerése | A feldolgozás széles körét fedi le, de a kognitív keresésre is vonatkozik, az entitások felismerési készsége általában az adatok (egy entitás) olyan forrásból való kinyerésére szolgál, amely nem biztosítja az adott információt natív módon. | [Entitás-felismerési szakértelem](cognitive-search-skill-entity-recognition.md)| 
| Képfeldolgozás | Kikövetkezteti a szöveget egy képből, például felismerhetővé teszi a tereptárgyak felismerését vagy szöveg kinyerését egy képből. Gyakori példák a beolvasott dokumentumokból (JPEG-fájlokból) származó karakterek feloldására szolgáló OCR-t, illetve az utca nevének felismerését egy utcai aláírást tartalmazó fényképen. | [Rendszerkép-elemzési szakértelem](cognitive-search-skill-image-analysis.md) vagy [OCR-képesség](cognitive-search-skill-ocr.md)
| Természetes nyelvek feldolgozása | Szöveg-feldolgozás a szöveges bemenetekkel kapcsolatos információkhoz és adatokhoz. A nyelvfelismerés, a hangulat elemzése és a kulcsfontosságú kifejezés kinyerése a természetes nyelvi feldolgozás alá eső képességek.  | [Kulcsszókeresés skill](cognitive-search-skill-keyphrases.md), [Nyelvfelismerés skill](cognitive-search-skill-language-detection.md), [Hangulatelemzés skill](cognitive-search-skill-sentiment.md) |
| A dokumentum repedése | Szöveges tartalom kinyerésének vagy létrehozásának folyamata a nem szöveges forrásokból az indexelés során. Az optikai karakterfelismerés (OCR) egy példa, de általában az alapszintű indexelő funkcióra hivatkozik, mivel az indexelő Kinyeri a tartalmat az alkalmazás fájljaiból. A forrásfájl helyét biztosító adatforrás és az indexelő definíciója, amely a mezők leképezéseit biztosítja, a dokumentumok repedésének kulcsfontosságú tényezői. | Lásd [](search-indexer-overview.md) : indexelő |
| Alakításában | Összevonhatja a szöveges töredékeket egy nagyobb struktúrába, vagy megfordíthatja a nagyobb szöveges adattömböket egy kezelhető méretre a további alsóbb rétegbeli feldolgozás érdekében. | [Formáló képesség](cognitive-search-skill-shaper.md), [szöveges egyesítési szakértelem](cognitive-search-skill-textmerger.md), [szöveg felosztása](cognitive-search-skill-textsplit.md) készség |
| Dúsított dokumentumok | Egy átmeneti belső struktúra, amely a feldolgozás során keletkezik, és a végső kimenet egy keresési indexben jelenik meg. A készségkészlet határozza meg, hogy mely dúsítások vannak elvégezve. A mező-hozzárendelések határozzák meg, hogy mely adatelemek legyenek hozzáadva az indexhez. Ha szeretné, létrehozhat egy tudásbázist, amellyel megőrizheti és felderítheti a dúsított dokumentumokat olyan eszközökkel, mint a Storage Explorer, a Power BI vagy bármely más eszköz, amely az Azure Blob Storage-hoz csatlakozik. | Lásd: [Knowledge Store (előzetes verzió)](knowledge-store-concept-intro.md). |
| Indexelő |  Egy webbejáró, amely Kinyeri a kereshető adatokat és metaadatokat egy külső adatforrásból, és feltölti az indexet az index és az adatforrása közötti mező-mező leképezések alapján. A kognitív keresések gazdagítása érdekében az indexelő meghívja a készségkészlet, és tartalmazza a mezőhöz tartozó leképezéseket, amelyek a dúsítási kimenetet társítják az indexben lévő mezőkhöz. Az indexelő definíciója a feldolgozási műveletek összes utasítását és hivatkozását tartalmazza, a folyamat pedig az indexelő futtatásakor lesz meghívva. | [Indexelők](search-indexer-overview.md) |
| Adatforrás  | Az indexelő által az Azure-ban támogatott típusok külső adatforráshoz való kapcsolódásra használt objektum. | Lásd [](search-indexer-overview.md) : indexelő |
| Index | Megőrzött keresési index a Azure Searchban, amely egy olyan index-sémából épül, amely meghatározza a mező szerkezetét és használatát. | [Indexek Azure Search](search-what-is-an-index.md) | 

<a name="where-do-i-start"></a>

## <a name="where-do-i-start"></a>Hogyan kezdjek hozzá?

**1. lépés: [Azure Search erőforrás létrehozása](search-create-service-portal.md)** 

**2. lépés: Próbálja ki néhány rövid útmutatót és példát a gyakorlati tapasztalatokra**

+ [Gyors útmutató (portál)](cognitive-search-quickstart-blob.md)
+ [Oktatóanyag (HTTP-kérések)](cognitive-search-tutorial-blob.md)
+ [Példa: Egyéni képesség létrehozása a kognitív kereséshez (C#)](cognitive-search-create-custom-skill-example.md)

Javasoljuk, hogy az ingyenes szolgáltatás tanulási célokra legyen elérhető, azonban az ingyenes tranzakciók száma napi 20 dokumentumra korlátozódik. Ha egy nap alatt egyszerre szeretné futtatni a rövid útmutatót és az oktatóanyagot, használjon kisebb fájlméretet (10 dokumentumot), hogy mindkét gyakorlatban illeszkedjen, vagy törölje a gyors útmutatóban vagy az oktatóanyagban használt indexelő is.

**3. lépés: Az API áttekintése**

A REST `api-version=2019-05-06` on kérelmek vagy a .net SDK is használható. 

Ez a lépés a REST API-kat használja egy kognitív keresési megoldás létrehozásához. A kognitív kereséshez csak két API van hozzáadva vagy kiterjesztve. Más API-k ugyanazzal a szintaxissal rendelkeznek, mint az általánosan elérhető verziók.

| REST API | Leírás |
|-----|-------------|
| [Adatforrás létrehozása](https://docs.microsoft.com/rest/api/searchservice/create-data-source)  | Egy olyan erőforrás, amely egy külső adatforrást azonosít, amely a dúsított dokumentumok létrehozásához használt forrásadatokat biztosít.  |
| [Készségkészlet létrehozása (API-Version = 2019-05-06)](https://docs.microsoft.com/rest/api/searchservice/create-skillset)  | Ez az API a kognitív keresésre vonatkozik. Ez egy olyan erőforrás, amely összehangolja az [előre definiált ismeretek](cognitive-search-predefined-skills.md) használatát és a dúsítási folyamat során használt [Egyéni kognitív képességeket](cognitive-search-custom-skill-interface.md) az indexelés során. |
| [Index létrehozása](https://docs.microsoft.com/rest/api/searchservice/create-index)  | Egy Azure Search indexet kifejező séma. Az indexben található mezők a forrásadatok mezőire vagy a dúsítási fázisban előállított mezőkre (például az entitások felismerése által létrehozott szervezeti nevekre vonatkozó mezőre) vonatkoznak. |
| [Indexelő létrehozása (API-Version = 2019-05-06)](https://docs.microsoft.com/rest/api/searchservice/create-skillset)  | Az indexelés során használt összetevőket meghatározó erőforrás: beleértve az adatforrást, a készségkészlet, a forrás-és a közbenső adatstruktúrákat a célként megadott indexbe, valamint magát az indexet. Az indexelő futtatása az adatfeldolgozáshoz és a dúsításhoz használt trigger. A kimenet egy olyan keresési index, amely az index sémán alapul, és a forrásadatok alapján van feltöltve, és a szakértelmével-n keresztül dúsított. Ez a meglévő API a készségkészlet tulajdonság bevonásával kognitív keresési helyzetekben bővíthető. |

**Ellenőrzőlista Egy tipikus munkafolyamat**

1. Az Azure-forrásadatok részhalmaza egy reprezentatív mintának. Az indexelés időt vesz igénybe, hogy egy kisebb, reprezentatív adatkészletet hozzon létre, majd fokozatosan felépítse a megoldás idejére.

1. Hozzon létre egy adatforrás- [objektumot](https://docs.microsoft.com/rest/api/searchservice/create-data-source) a Azure Searchban, és adjon meg egy kapcsolódási karakterláncot az adatok lekéréséhez.

1. Hozzon létre egy [készségkészlet](https://docs.microsoft.com/rest/api/searchservice/create-skillset) a gazdagodás lépéseivel.

1. Adja meg az [index sémát](https://docs.microsoft.com/rest/api/searchservice/create-index). A *mezők* gyűjteménye mezőket tartalmaz a forrásadatokből. Emellett további mezőket kell kimutatnia, amelyekkel megtarthatja a gazdagodás során létrehozott tartalomhoz generált értékeket.

1. Az adatforrásra, a készségkészlet és az indexre hivatkozó [Indexelő](https://docs.microsoft.com/rest/api/searchservice/create-skillset) definiálása.

1. Az indexelő alkalmazásban adja hozzá a *outputFieldMappings*. Ez a szakasz a készségkészlet (a 3. lépésben) lévő kimeneteket a tárgymutató sémájának bemenet mezőibe (a 4. lépésben) képezi le.

1. Küldje el az imént létrehozott Indexer-kérést (egy post-kérést a kérelem törzsében lévő indexelő definícióval), hogy kifejezze az indexelő Azure Searchban. Ez a lépés az indexelő futtatásának módja, a folyamat meghívása.

1. Lekérdezések futtatása az eredmények kiértékeléséhez és a kód módosítása a szakértelmével, a séma vagy az indexelő konfigurációjának frissítéséhez.

1. [](search-howto-reindex.md) Az indexelő alaphelyzetbe állítása a folyamat újraépítése előtt.

További információ az adott kérdésekről vagy problémákról: [hibaelhárítási tippek](cognitive-search-concept-troubleshooting.md).

## <a name="next-steps"></a>További lépések

+ [Kognitív keresési dokumentáció](cognitive-search-resources-documentation.md)
+ [Rövid útmutató: A kognitív keresés kipróbálása egy portálon – útmutató](cognitive-search-quickstart-blob.md)
+ [Oktatóanyag: Ismerje meg a kognitív keresési API-kat](cognitive-search-tutorial-blob.md)
+ [A Knowledge Store áttekintése](knowledge-store-concept-intro.md)
+ [Útmutató a Knowledge Store-hoz](knowledge-store-howto.md)
