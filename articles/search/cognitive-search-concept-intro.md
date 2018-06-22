---
title: Adatok kinyerése, az Azure Search feldolgozása természetes nyelvű kognitív keresése |} Microsoft Docs
description: Adatok kinyerése, természetes nyelvű (NLP) és lemezkép feldolgozást kereshető tartalom létrehozása az Azure Search indexelő kognitív képességek segítségével.
manager: cgronlun
author: HeidiSteen
services: search
ms.service: search
ms.devlang: NA
ms.topic: conceptual
ms.date: 05/04/2018
ms.author: heidist
ms.openlocfilehash: 64b4c0a315e206cd260f2f1108362e92f55d1843
ms.sourcegitcommit: ea5193f0729e85e2ddb11bb6d4516958510fd14c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/21/2018
ms.locfileid: "36304632"
---
# <a name="what-is-cognitive-search"></a>Mi az a kognitív keresési?

Kognitív keresési az előzetes verziójú funkciók [Azure Search](search-what-is-azure-search.md), déli középső Régiójában és Nyugat-Európában, az indexelő munkaterhelések AI hozzáadó minden csomagban érhető el. Adatok kinyerése, természetes nyelvű, és lemezkép feldolgozást indexelés során strukturálatlan vagy nem kereshető tartalom a rejtett információkat talál, és lehetővé teszi az Azure Search kereshető.

AI integrációra van keresztül *kognitív képességek* forrás dokumentumok szekvenciális folyamatok, a keresési indexhez útvonal kiegészítése, amely. 

![Kognitív keresési folyamat diagramja](./media/cognitive-search-intro/cogsearch-architecture.png "kognitív keresési folyamat áttekintése")

Az indexelés során használt ismeretek előre definiált vagy egyéni lehetnek:

+ [Előre definiált ismeretek](cognitive-search-predefined-skills.md) kognitív szolgáltatások API-k használt ugyanazok AI az algoritmusok alapulnak: [nevű entitás felismerés](cognitive-search-skill-named-entity-recognition.md), [kulcs kifejezés kibontási](cognitive-search-skill-keyphrases.md), és [OCR](cognitive-search-skill-ocr.md)csak néhány. 

+ [Egyéni ismeretek](cognitive-search-create-custom-skill-example.md) semmilyen speciális feldolgozásához szükséges is fejleszthetők. Egyéni képességek példák előfordulhat, hogy egy adott tartományhoz, például a pénzügyi, tudományos kiadványok vagy orvosi célzó egyéni entitás modul vagy a dokumentum besorolás.

> [!NOTE]
> Kognitív keresési nyilvános előzetes verziójában, és jelenleg tartományregisztráció ingyenesen skillset végrehajtása. Ennek a funkciónak a díjszabását a későbbiekben jelentjük be.

## <a name="components-of-cognitive-search"></a>Kognitív keresési összetevői

A kognitív keresési folyamat alapuló [Azure Search *indexelők* ](search-indexer-overview.md) , amely bejárható adatforrások, és adja meg a végpont index feldolgozása. Ismeretek most már csatlakoztatott indexelők elfogja, és megfelelően a skillset dokumentumok bővíthesse adhat meg. Miután indexelt, elérheti összes tartalom keresési kérésekkel [lekérdezése az Azure Search által támogatott eszköztípusok](search-query-overview.md).  Ha most ismerkedik az indexelők, ez a szakasz végigvezeti a.

### <a name="source-data-and-document-cracking-phase"></a>Adatok forrás- és dokumentumszolgáltatások jelszófeltörő fázis

A feldolgozási sor elején rendelkezik strukturálatlan szöveg vagy nem szöveges tartalom (például a lemezkép és beolvasott dokumentum JPEG-fájlok). Adatok léteznie kell, hogy elérhető legyen az indexelő az Azure data storage szolgáltatásnak. Indexelők is "feltörhetők" szöveg kinyerése forrásadatok forrás dokumentumokat.

![A dokumentum fázis repedés](./media/cognitive-search-intro/document-cracking-phase-blowup.png "dokumentum repedés")

 Támogatott források tartalmaznak az Azure blob Storage tárolóban, az Azure table storage, Azure SQL Database és Azure Cosmos DB. Szöveges tartalom a következő típusú történő kinyerésének: PDF, Word, PowerPoint, CSV-fájlok. A teljes listáját lásd: [támogatott formátumok](search-howto-indexing-azure-blob-storage.md#supported-document-formats).

### <a name="cognitive-skills-and-enrichment-phase"></a>Kognitív ismeretei és dúsító fázis

Dúsító keresztül van *kognitív képességek* atomi műveletek végrehajtása. Például, ha elvégezte a szöveges tartalom PDF-, entitás felismerés nyelvi észlelési, vagy alkalmazhat kulcs kifejezés kibontási új az indexben nem érhető el natív módon lévő mezőket a forrás létrehozásához. Teljes sorozatnak így képességek a feldolgozási soros nevezzük egy *skillset*.  

![Dúsító fázis](./media/cognitive-search-intro/enrichment-phase-blowup.png "dúsító fázis")

Egy skillset alapuló [kognitív képességek az előre megadott](cognitive-search-predefined-skills.md) vagy [egyéni ismeretek](cognitive-search-create-custom-skill-example.md) adja meg, és a skillset csatlakozzon. Egy skillset minimális vagy nagyon összetett lehet, és határozza meg, nem csak a feldolgozási típusú, de is műveletek sorrendjét. Egy skillset és a mező megfeleltetéseket, megadva, az indexelő része teljesen meghatározza a dúsító folyamat. Húzza az összes adatot együtt kapcsolatos további információkért lásd: [megadása egy skillset](cognitive-search-defining-skillset.md).

Belsőleg az adatcsatorna állít elő, bővített dokumentumok gyűjteménye. Eldöntheti, mely a bővített dokumentumok részei példánycímkének mezőket a keresési index kell rendelni. Például ha telepítette a legfontosabb kifejezések kivonása és az entitás felismerés képességek, majd a új mezők a bővített dokumentum részét képezik, és az index mezőibe rendelhetők. Lásd: [jegyzetek](cognitive-search-concept-annotations-syntax.md) bemeneti/kimeneti kialakulásához tájékozódhat.

### <a name="search-index-and-query-based-access"></a>Keresési index és a lekérdezés-alapú hozzáférés

Ha feldolgozása befejeződött, lehetősége van egy keresési corpus bővített dokumentumok, teljes szöveges kereshető az Azure Search álló. [Az index lekérdezése](search-query-overview.md) hogyan el a fejlesztők és a felhasználók a folyamat által létrehozott bővített tartalom. 

![A keresés ikonra index](./media/cognitive-search-intro/search-phase-blowup.png "Index keresési ikon")

Az index kívül esik, mint bármilyen más létrehozhat az Azure Search: kiegészíti az egyéni lekérdezések, aktiválják az intelligens egyeztetésű keresési lekérdezések, adja hozzá a szűrt keresési, vagy a keresési eredmények átalakításához profilok pontozási kísérletezhet.

Indexek akkor jönnek létre az index sémájából, amely meghatározza a mezők és attribútumok, és más szerkezetek adott index, például pontozási profilok csatolva, és szinonimát képezi le. Amennyiben az index meghatározása és feltöltve, indexelheti Növekményesen új és frissített adatforrás dokumentumok átvételéhez. Egyes módosítások teljes újjáépítése szükséges. A kis adatkészletet addig, amíg a séma-tervező nem stabil használja. A további tudnivalókért lásd az [indexek újraépítését](search-howto-reindex.md) ismertető cikket.

<a name="feature-concepts"></a>

## <a name="key-features-and-concepts"></a>A legfontosabb jellemzők és fogalmak

| Fogalom | Leírás| Hivatkozások |
|---------|------------|-------|
| Skillset | Egy legfelső szintű nevű erőforrás, amely tartalmazza a képességek egy gyűjtemény. Egy skillset a dúsító csővezeték. Meghívták az indexelő által indexelés során. | [Adja meg a skillset](cognitive-search-defining-skillset.md) |
| Kognitív szakértelem | Egy atomi átalakítást dúsító-feldolgozási folyamat. Gyakran a program egy összetevő, amely kinyeri vagy struktúra arra következtet, és ezért szabályozva az információhasználat, a bemeneti adatokat megértését. Szinte mindig a szöveges kimenete és a feldolgozási természetes nyelvű feldolgozási vagy kép feldolgozási, amely kinyeri vagy szöveges állít elő, a lemezkép bemenetei. Szakértelem kimenete index mező leképezve, vagy az alsóbb rétegbeli dúsító bemenetként használja. Szakértelem előre meghatározott és a Microsoft vagy egyéni által biztosított: Ön által létrehozott és telepített. | [Előre definiált képességek](cognitive-search-predefined-skills.md) |
| Adatok kinyerése | Magában foglalja az feldolgozását, de kognitív keresési, névvel rendelkező entitás felismerés szakértelem vonatkozó széles körének leggyakrabban használt Ha adatokat szeretne kinyerni (entitás) olyan forráshoz, amely natív módon nem adja meg ezt az információt. | [Nevesített entitás felismerés szakértelem](cognitive-search-skill-named-entity-recognition.md)| 
| Képfeldolgozás | Kikövetkezteti a szöveg, kép, például egy jellegzetes ismeri fel a vagy szöveges kiolvassa a lemezképre. Gyakori példák OCR karakterek feloldása beolvasott dokumentum (JPEG) fájlból, vagy egy utcaneve utca bejelentkezési tartalmazó fénykép ismer fel. | [Kép elemzés szakértelem](cognitive-search-skill-image-analysis.md) vagy [OCR szakértelem](cognitive-search-skill-ocr.md)
| Természetes nyelvek feldolgozása | A szöveg információkkal kapcsolatos szöveg bemenet feldolgozása. Nyelvi észlelése, a céggel kapcsolatos véleményeket elemzés és a kulcs kifejezés kibontási olyan természetes nyelvű feldolgozási alá tartozó képességek.  | [Kulcs kifejezés kibontási szakértelem](cognitive-search-skill-keyphrases.md), [nyelvi észlelési szakértelem](cognitive-search-skill-language-detection.md), [véleményeket elemzés szakértelem](cognitive-search-skill-sentiment.md) |
| A dokumentum repedés | A folyamat kibontása vagy létrehozása a szöveges tartalom nem szöveges forrásokból indexelés során. Optikai karakter felismerési (OCR) egy példa, de általában hivatkozik indexelő alapfunkciókat, az indexelő tartalmat ki az alkalmazás-fájlokból. Az adatforrás forrásfájl helyéhez és indexelő meghatározása mező hozzárendelések, így azok mindkét a dokumentum repedés. | Lásd: [indexelő](search-indexer-overview.md) |
| Kialakításában | Szöveg töredék konszolidálhatják egy nagyobb struktúra, vagy fordítva lebontva nagyobb szöveg adattömbök be további alárendelt feldolgozási kezelhető méretét. | [Formázóban szakértelem](cognitive-search-skill-shaper.md), [szöveg egyesülés szakértelem](cognitive-search-skill-textmerger.md), [szöveg szakértelem felosztása](cognitive-search-skill-textsplit.md) |
| Bővített dokumentumok | Egy átmeneti belső struktúra, a kódot közvetlenül nem elérhető. Bővített dokumentumok feldolgozása során jönnek létre, de csak végső kimenetek megmaradnak, a search-index. Mező megfeleltetéseket határozza meg, melyik adatelemek kerülnek az index. | Lásd: [bővített dokumentumok elérése](cognitive-search-tutorial-blob.md#access-enriched-document). |
| Indexelő |  A webbejáró, amely kereshető adatokat és a metaadatok a külső adatforráshoz, és az index és az adatokat az adatforrásból a dokumentum repedés közötti mezők leképezések alapján index tölti fel. Kognitív keresési fokozatokká az indexelő egy skillset hív, és a mező leképezéseit társítása dúsító kimenet célmező. az index tartalmazza. Indexelő meghatározása az összes utasításokat és hivatkozásokat az adatcsatorna műveletek tartalmaz, és az adatcsatorna meghívták az indexelő futtatásakor. | [Indexelők](search-indexer-overview.md) |
| Adatforrás  | A támogatott típusok Azure külső adatforráshoz való kapcsolódáshoz az indexelő által használt objektum. | Lásd: [indexelő](search-indexer-overview.md) |
| Index | A megőrzött keresési corpus az Azure Search egy index séma, amely meghatározza a mező struktúra és a használati össze. | [Az Azure Search indexek](search-what-is-an-index.md) | 


## <a name="where-do-i-start"></a>Hogyan kezdjek hozzá?

**1. lépés: A régió, az API-kat biztosít a search szolgáltatás létrehozása** 

+ USA déli középső régiója
+ Nyugat-Európa

**2. lépés: A munkafolyamat főkiszolgálóvá a gyakorlati tapasztalatok**

+ [Gyors üzembe helyezés (portál)](cognitive-search-quickstart-blob.md)
+ [Az oktatóanyag (HTTP-kérelmek)](cognitive-search-tutorial-blob.md)
+ [Példa egyéni ismeretek (C#)](cognitive-search-create-custom-skill-example.md)

**3. lépés:, Tekintse át az API-t (csak a többi)**

Jelenleg csak REST API-k vannak megadva. Használjon `api-version=2017-11-11-Preview` minden kérelemhez. A következő API-k segítségével kognitív keresési megoldás létrehozása. Csak két API-k hozzáadásakor vagy extended kognitív kereséshez. Más API-k általánosan elérhető verziók megegyező szintaxissal rendelkezik.

| REST API | Leírás |
|-----|-------------|
| [Adatforrás létrehozása](https://docs.microsoft.com/rest/api/searchservice/create-data-source)  | Bővített dokumentumok létrehozásához használt forrás-adatokat szolgáltató külső adatforrás azonosító erőforrás.  |
| [Hozzon létre Skillset (api-version = 2017-11-11 – előzetes verzió)](https://docs.microsoft.com/rest/api/searchservice/create-skillset)  | Használatát koordinációs erőforrás [képességek az előre megadott](cognitive-search-predefined-skills.md) és [egyéni kognitív képességek](cognitive-search-custom-skill-interface.md) dúsító-feldolgozási folyamat során indexelő használt. |
| [Index létrehozása](https://docs.microsoft.com/rest/api/searchservice/create-index)  | Az Azure Search-index kifejező séma. Az index mezőinek képezze le a forrásadatok mezők vagy mezők (például a szervezet nevekkel felismerés személy által létrehozott mező) dúsító fázis során előállított. |
| [Hozzon létre indexelőt (api-version = 2017-11-11 – előzetes verzió)](https://docs.microsoft.com/rest/api/searchservice/create-skillset)  | Egy erőforrás indexelés során használt összetevők meghatározása: egy adatforrást, egy skillset, mező társítást a forrás- és köztes adatstruktúrák cél index és az index magát. Az eseményindító adatfeldolgozást és dúsító fut az indexelő. A keresési corpus a a sémát indexeli, feltöltve forrásadatok keresztül skillsets dúsított alapján eredménye.  |

**Ellenőrzőlista: Egy tipikus munkafolyamat**

1. Részhalmaza reprezentatív minta az Azure adatforrás adatait. Indexelő időt vesz egy kisméretű, reprezentatív adatkészlet úgy kezdődnie, és majd kialakításához, Növekményesen a megoldás Miután kiforrottá válik.

1. Hozzon létre egy [adatforrás-objektum](https://docs.microsoft.com/rest/api/searchservice/create-data-source) az Azure Search számára adjon meg egy kapcsolati karakterláncot az adatok beolvasása.

1. Hozzon létre egy [skillset](https://docs.microsoft.com/rest/api/searchservice/create-skillset) dúsító leírásával.

1. Adja meg a [a sémát indexeli](https://docs.microsoft.com/rest/api/searchservice/create-index). A *mezők* forrásadatok mezőit a gyűjteménybe. Meg is helyettes ki további mezőket generált értékek dúsító során létrehozott tartalom tárolásához.

1. Adja meg a [indexelő](https://docs.microsoft.com/rest/api/searchservice/create-skillset) az adatforrás, skillset és index hivatkozik.

1. Az indexelő belül adja hozzá a *outputFieldMappings*. Ez a szakasz a sémát indexeli (a 4. lépés) a bemeneti adatok mezőket (a 3. lépésben) skillset kimenetének képezi le.

1. Küldési *létrehozása indexelő* kérjen a újonnan létrehozott (a POST kérelem a kérelem törzsében szereplő indexelő definition) az Azure Search indexelő Express. Ez a lépés nem hogyan futtatja az indexelő meghívása a folyamatot.

1. Eredmények értékelése és frissítés skillsets, séma vagy indexelő konfigurációs kód módosítása lekérdezéseket futtathat.

1. [Az indexelő alaphelyzetbe](search-howto-reindex.md) előtt újból összeállítani a folyamatot.

További információ a konkrét kérdések és problémák: [hibaelhárítási tippek](cognitive-search-concept-troubleshooting.md).

## <a name="next-steps"></a>További lépések

+ [Kognitív keresési dokumentáció](cognitive-search-resources-documentation.md)
+ [Gyors üzembe helyezés: Próbálja kognitív keresési portál forgatókönyv](cognitive-search-quickstart-blob.md)
+ [Oktatóanyag: Ismerje meg a kognitív keresési API-k](cognitive-search-tutorial-blob.md)
