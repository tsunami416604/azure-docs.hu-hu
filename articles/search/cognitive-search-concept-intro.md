---
title: A kognitív keresés, az adatok kinyerése, a természetes nyelvi mesterséges Intelligencia folyamat – Azure Search
description: Tartalom kinyerés, természetes nyelvi feldolgozási (NLP) és képfeldolgozó kereshető tartalom létrehozása az Azure Search szolgáltatásban az indexelés kognitív képességek és AI-algoritmusokat.
manager: cgronlun
author: HeidiSteen
services: search
ms.service: search
ms.devlang: NA
ms.topic: overview
ms.date: 04/05/2019
ms.author: heidist
ms.custom: seodec2018
ms.openlocfilehash: c421d99f1071c7a38cfe315cc3054136f81598e0
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/18/2019
ms.locfileid: "59265968"
---
# <a name="what-is-cognitive-search-in-azure-search"></a>Mi az Azure Search "cognitive search"?

A kognitív keresés az AI szolgáltatása az Azure Search szolgáltatásban használt szöveg kinyerése a képek, blobok és más strukturálatlan adatforrások – győződjön meg arról, hogy több kereshető az Azure Search-index a tartalom bővítését. Kinyerés és Adatbővítés keresztül megvalósított *kognitív képességeket* egy indexelési folyamat csatlakozik. Mesterséges Intelligencia végrehajtott információbeolvasás támogatottak a következő módon: 

+ **Természetes nyelvi feldolgozás** képességek közé tartozik [entitások felismerése](cognitive-search-skill-entity-recognition.md), [nyelvfelismerés](cognitive-search-skill-language-detection.md), [kulcsfontosságú kifejezések kinyerése](cognitive-search-skill-keyphrases.md), szöveges adatkezelési és [hangulatfelismerés](cognitive-search-skill-sentiment.md). Ezek a képességek a strukturálatlan szöveg feltételezheti, hogy új űrlapok mapován jako az indexben lévő kereshető, és a szűrhető mezők.

+ **Képfeldolgozás** képességek közé tartozik [optikai karakterfelismerés (OCR)](cognitive-search-skill-ocr.md) és azonosítása [vizuális jellemzőket](cognitive-search-skill-image-analysis.md), például arcfelismerés, kép értelmezését, felismerés (kép) híres személyek és tereptárgyak felismerése) vagy a színeket vagy a kép tájolásának-attribútumok. Képek tartalmának, kereshető használatával az Azure Search lekérdezési képességek szövegesen hozhat létre.

![A kognitív keresés adatfolyamat ábrája](./media/cognitive-search-intro/cogsearch-architecture.png "Cognitive Search folyamat áttekintése")

Az Azure Search olyan kognitív machine learning-modellek a Cognitive Services API-k alapul: [Számítógépes Látástechnológia](https://docs.microsoft.com/azure/cognitive-services/computer-vision/) és [Szövegelemzés](https://docs.microsoft.com/azure/cognitive-services/text-analytics/overview). 

Természetes nyelvi és képfeldolgozás alkalmazni az adatok betöltési fázisban az eredményeket a dokumentum létrehozása az Azure Search a kereshető indexet az részévé. Az adatok egy Azure data beállított tárolási forrása és egy indexelési folyamat használatával, amelyik keresztül továbbítja [beépített képességek](cognitive-search-predefined-skills.md) van szüksége. Az architektúra olyan bővíthető, így ha a beépített képességek nem elegendő, létrehozása és csatolása [egyéni képesség](cognitive-search-create-custom-skill-example.md) egyéni feldolgozási integrálásához. Példa lehet egy adott tartományban, például a pénzügyi, a tudományos vagy orvosi célzó egyéni entitás modul vagy a dokumentum besorolás.

> [!NOTE]
> 2018. December 21., kezdési is [Cognitive Services-erőforrás csatolása](cognitive-search-attach-cognitive-services.md) és a egy Azure Search-képességek alkalmazási lehetőségét. Ez lehetővé teszi indexmezők végrehajtási díjszabási elindításához. Ezen a napon azt is már díjszabási kép kinyerése a dokumentumfeltörést fázis részeként. A dokumentumok szövegkinyerés továbbra is ingyenesen kínáljuk.
>
> Beépített képességek végrehajtási, a Cognitive Services számítunk fel díjat, számoljuk fel a meglévő [használatalapú – mint-akkor lépjen ár](https://azure.microsoft.com/pricing/details/cognitive-services/) . Kép kinyerési díjszabás, egy Azure Search számítunk fel díjat, jelenleg előzetes verzió díjszabása leírt számlázása a [díjszabását ismertető oldalt az Azure Search](https://go.microsoft.com/fwlink/?linkid=2042400).

## <a name="components-of-cognitive-search"></a>A kognitív keresés összetevői

Cognitive search szolgáltatás az előzetes verziójú funkció [Azure Search](search-what-is-azure-search.md).

A kognitív keresés folyamat alapján [Azure Search *indexelők* ](search-indexer-overview.md) , feltérképezi az adatforrásokat, és teljes körű index feldolgozási biztosítják. Képességek már csatlakoztatott elfogja, az indexelők és bővítését készségeitől dokumentumokat határoz meg. Miután indexelt, elérheti végig az összes keresési kéréseket a tartalomhoz [lekérdezése az Azure Search által támogatott típusok](search-query-overview.md).  Ha most ismerkedik az indexelők, ez a szakasz részletesen ismerteti a lépéseket.

### <a name="step-1-connection-and-document-cracking-phase"></a>1. lépés: Kapcsolat és a dokumentumleképezési fázis

A folyamat elején rendelkezik strukturálatlan szöveges vagy nem szöveges tartalmak (például a lemezkép és a beolvasott dokumentum JPEG-fájlok). Adatok léteznie kell egy Azure storage szolgáltatás, amely az indexelő által hozzáférhető. Az indexelők is "feltörhetők" szöveg kinyerésére forrásadatok forrás dokumentumokat.

![Fázis dokumentumleképezési](./media/cognitive-search-intro/document-cracking-phase-blowup.png "dokumentumfeltörést")

 Támogatott az adatforrásokba tartoznak az Azure blob storage-ba, az Azure table storage, Azure SQL Database és Azure Cosmos DB-hez. A következő típusú szöveges tartalom kinyerésének: PDF-, Word, PowerPoint-és CSV-fájlok. A teljes listát lásd: [támogatott formátumok](search-howto-indexing-azure-blob-storage.md#supported-document-formats).

### <a name="step-2-cognitive-skills-and-enrichment-phase"></a>2. lépés: Kognitív képességeket és Adatbővítés fázis

Keresztül történik Adatbővítés *kognitív képességeket* atomi műveletek végrehajtása. Például ha már rendelkezik a szöveges tartalom a PDF-, alkalmazhatja entitások felismerése nyelvfelismerés, vagy a kulcsfontosságú kifejezések kinyerése új mezőt az indexben, amelyek nem érhető el natív módon a forrás előállításához. Érvényesítette, a képességek a folyamatban használt a gyűjtemény neve egy *indexmezők*.  

![Adatbővítés fázis](./media/cognitive-search-intro/enrichment-phase-blowup.png "Adatbővítés fázis")

A képességek alkalmazási lehetőségét alapján [kognitív képességek az előre meghatározott](cognitive-search-predefined-skills.md) vagy [egyéni képesség](cognitive-search-create-custom-skill-example.md) adja meg, és csatlakozhat a képességek alkalmazási lehetőségét. A képességek alkalmazási lehetőségét minimális vagy nagyon összetett is lehet, és meghatározza, hogy a feldolgozás nem csupán a típusát, hanem műveletek sorrendjét. A képességek alkalmazási lehetőségét emellett a részeként az indexelő teljes megadja a Adatbővítés folyamat definiálva mezőmegfeleltetéseket. Melyekbe az összes elemet kapcsolatos további információkért lásd: [Képességcsoport](cognitive-search-defining-skillset.md).

Belsőleg a folyamat állít elő, képi elemekben gazdag dokumentumok gyűjteményét. Megadhatja, hogy mely részei a jelentéstétellel dokumentumok indexelhető mezőket a keresési index hozzá kell rendelni. Például ha telepítette a kulcskifejezések kinyerése és az entitások felismerése képességeit, majd új mezők válnak a jelentéstétellel dokumentum egy része, és akkor is le lehet képezni a az index mezőt. Lásd: [jegyzetek](cognitive-search-concept-annotations-syntax.md) bemeneti/kimeneti kialakításokat tájékozódhat.

### <a name="step-3-search-index-and-query-based-access"></a>3. lépés: Search-index és lekérdezés-alapú hozzáférés

A feldolgozás végeztével a keresési forrásgyűjteményébe álló jelentéstétellel dokumentumok, teljes szöveges átböngészhető az Azure Search rendelkezik. [Az index lekérdezése](search-query-overview.md) hogyan fejlesztők és a felhasználók érhetik el a képi elemekben gazdag tartalmat, a folyamat által generált. 

![A keresés ikonra index](./media/cognitive-search-intro/search-phase-blowup.png "Index a keresés ikonra")

Az index van, mint bármilyen más is létrehozhatók az Azure search: az egyéni elemzőket kiegészítik, intelligens keresési lekérdezések meghívása, szűrt keresés hozzáadása, vagy kísérletezhet a pontozási profilok, hogy alakítsa át a keresési eredmények között.

Indexek jönnek létre az index sémájából, amely meghatározza a mezők és attribútumok, és egyéb szerkezetek adott indexének, például a pontozási profilok csatlakozik, és szinonimát társít. Index van definiálva, és a feltöltve, indexelésére használhatja, Növekményesen, válasszon ki forrást az új és frissített dokumentumokat. Egyes módosítások van szükség teljes. Amíg a séma tervező nem stabil, használjon egy kisméretű adatkészlet. A további tudnivalókért lásd az [indexek újraépítését](search-howto-reindex.md) ismertető cikket.

<a name="feature-concepts"></a>

## <a name="key-features-and-concepts"></a>A legfontosabb jellemzők és fogalmak

| Fogalom | Leírás| Hivatkozások |
|---------|------------|-------|
| Képességcsoport | A legfelső szintű erőforrás, amely tartalmazza a képességek egy gyűjtemény neve. A képességek alkalmazási lehetőségét a Adatbővítés folyamatban. Az indexelő által az indexelés során indítva. | [Képességcsoport megadása](cognitive-search-defining-skillset.md) |
| A cognitive szakértelem | Egy atomi átalakítást Adatbővítés folyamatban. Gyakran nem egy összetevő, amely kinyeri vagy kikövetkezteti a struktúrát, és ezért úgy bővíti a bemeneti adatok megértését. Szinte mindig a kimeneti szöveges pedig a feldolgozás természetes nyelvi feldolgozás, vagy képfeldolgozás, amely kinyeri vagy kép bemeneti szöveg hoz létre. Szakértelem kimenete egy mezőt az indexben leképezve, vagy az alsóbb rétegbeli Adatbővítés bemenetként használja. Szakértelem előre meghatározott és egyéni vagy a Microsoft által biztosított: létrehozott és telepített, Ön által. | [Előre megadott képesség](cognitive-search-predefined-skills.md) |
| Adatok kinyerése | Széles feldolgozási, de a cognitive search, a nevesített entitások felismerése szakértelem vonatkozó leggyakrabban használt (entitás) adatok kinyerése egy forrás, amely nem natív módon biztosítja ezt az információt tartalmazza. | [Nevesített entitások felismerése szakértelem](cognitive-search-skill-named-entity-recognition.md)| 
| Képfeldolgozás | Kikövetkezteti a szöveg-lemezképről, például egy tereptárgyak felismerése, vagy szöveges kigyűjti a képen. Gyakori például OCR karakterek emelésére fájlból beolvasott dokumentum (JPEG), vagy FELISMERVE utcát és bejelentkezési tartalmazó fénykép az utca nevét. | [Kép elemzése szakértelem](cognitive-search-skill-image-analysis.md) vagy [OCR szakértelem](cognitive-search-skill-ocr.md)
| Természetes nyelvek feldolgozása | Elemzések és a bemeneti szöveg információinak szövegfeldolgozást. Kulcsszókeresés, nyelvfelismerés és hangulatelemzés olyan képességek, amelyek a természetes nyelvi feldolgozás alá tartozik.  | [Key kifejezés kinyerése szakértelem](cognitive-search-skill-keyphrases.md), [nyelv észlelése szakértelem](cognitive-search-skill-language-detection.md), [Sentiment Analysis szakértelem](cognitive-search-skill-sentiment.md) |
| Dokumentumleképezési | A folyamat kibontása vagy szöveges tartalom létrehozása nem szöveges forrásokból származó indexelés során. Optikai karakterfelismerés (OCR) példaként, de általában hivatkozik az alapfunkciókra indexelő, az indexelő tartalmat bontja az alkalmazás fájljait. Forrásfájljainak helyét, és az indexelő definíciója, mező-leképezések biztosítása az adatforrás azok mind a dokumentumleképezési. | Lásd: [indexelők](search-indexer-overview.md) |
| Alakításra | Szövegtöredékei kialakíthattunk egy nagyobb struktúra, vagy fordítva felosztania nagyobb szöveges tömbökben be további alárendelt feldolgozáshoz kezelhető mérettel. | [Shaper szakértelem](cognitive-search-skill-shaper.md), [szöveg egyesülés szakértelem](cognitive-search-skill-textmerger.md), [szöveg felosztása szakértelem](cognitive-search-skill-textsplit.md) |
| Továbbfejlesztett dokumentumok | Egy átmeneti belső szerkezetét, nem közvetlenül elérhető, a kódban. Továbbfejlesztett dokumentumok feldolgozása során jönnek létre, de csak végső kimenetek megmaradnak a search-index. Mezőleképezések határozza meg, melyik adatelem hozzáadódnak az index. | Lásd: [képi elemekben gazdag dokumentumok elérése](cognitive-search-tutorial-blob.md#access-enriched-document). |
| Indexelő |  A webbejáró, amely kinyeri a kereshető adatok és metaadatok egy külső adatforrásból, és a egy index, az index és az adatforrás dokumentumfeltörést közötti mező mező leképezések alapján tölti fel. A kognitív keresés végrehajtott információbeolvasás az indexelő hívja meg a képességek alkalmazási lehetőségét, és tartalmazza a mezőmegfeleltetéseket Adatbővítés kimeneti cél mezőkre az indexben való társítása. Az indexelő definíciója tartalmaz minden utasításokat és hivatkozásokat folyamat műveletekhez, és az indexelő futtatása a folyamat hív meg. | [Indexelők](search-indexer-overview.md) |
| Adatforrás  | Az Azure-ban támogatott típusú külső adatforráshoz való kapcsolódáshoz az indexelő által használt objektum. | Lásd: [indexelők](search-indexer-overview.md) |
| Index | A megőrzött keresési forrásgyűjteményébe az Azure Search szolgáltatásban az indexsémát, amely meghatározza a mező struktúra és használat alapján készült. | [Indexek az Azure Search szolgáltatásban](search-what-is-an-index.md) | 

<a name="where-do-i-start"></a>

## <a name="where-do-i-start"></a>Hogyan kezdjek hozzá?

**1. lépés: [Az Azure Search-erőforrás létrehozásához](search-create-service-portal.md)** 

**2. lépés: Próbálja meg néhány gyorsútmutatókat és gyakorlati tapasztalatokat példáit**

+ [Rövid útmutató (portál)](cognitive-search-quickstart-blob.md)
+ [Az oktatóanyag (HTTP-kérések)](cognitive-search-tutorial-blob.md)
+ [A példában egyéni képesség (C#)](cognitive-search-create-custom-skill-example.md)

Javasolt az ingyenes szolgáltatás tanulási célokra, de vegye figyelembe, hogy ingyenes tranzakciók száma korlátozott a naponta 20 dokumentumokhoz. A rövid útmutató és oktatóanyag futtatásához a napon, használhat egy kisebb fájlt megadva (10 dokumentumok), így mindkét gyakorlatokban is elférjen.

**3. lépés: Tekintse át az API-t (csak REST)**

Jelenleg csak REST API-k találhatók. Használat `api-version=2017-11-11-Preview` minden kérelemhez. A következő API-k használatával hozhat létre egy cognitive search-megoldását. Csak két API-k hozzáadásakor vagy kiterjesztett cognitive search. Más API-k az általánosan elérhető verzió megegyező szintaxissal rendelkezik.

| REST API | Leírás |
|-----|-------------|
| [Adatforrás létrehozása](https://docs.microsoft.com/rest/api/searchservice/create-data-source)  | Egy erőforrás azonosítása egy külső adatforrásból, képi elemekben gazdag dokumentumok létrehozásához használt forrás-adatokat biztosítva.  |
| [Képességcsoport létrehozása (api-version = 2017-11-11-előzetes verzió)](https://docs.microsoft.com/rest/api/searchservice/create-skillset)  | Koordinációs használatát erőforrás [képességek az előre meghatározott](cognitive-search-predefined-skills.md) és [egyéni kognitív képességeket](cognitive-search-custom-skill-interface.md) Adatbővítés folyamatban az indexelés során használt. |
| [Index létrehozása](https://docs.microsoft.com/rest/api/searchservice/create-index)  | A séma megadása az Azure Search-index. Mezőkre az indexben képezze le a forrásadatok mezők vagy előállított felderítési bővítést fázisában (például egy mezőt az entitások felismerése által létrehozott szervezet neve) mezőt. |
| [Indexelő létrehozása (api-version = 2017-11-11-előzetes verzió)](https://docs.microsoft.com/rest/api/searchservice/create-skillset)  | Az indexelés során használt összetevőket meghatározása erőforrás: többek között egy adatforrást, a képességek alkalmazási lehetőségét, mező társítást a forrás- és a köztes adatokat struktúrák célindex és magát az index. Az indexelő futtatása az adatfeldolgozás és a felderítési bővítést az eseményindító. A kimenet egy keresési forrásgyűjteményébe az indexséma szakértelmével keresztül bővített adatforrás adatokkal feltöltve alapján.  |

**Ellenőrzőlista: A jellemző munkafolyamatokat**

1. Része az Azure forrás adatait egy reprezentatív mintát. Indexelő időt vesz egy kis méretű, képviselő adatkészlet tehát elindításához, és majd építhető Növekményesen, a megoldás kiforrottá.

1. Hozzon létre egy [adatforrás-objektum](https://docs.microsoft.com/rest/api/searchservice/create-data-source) az Azure Search, adjon meg egy kapcsolati karakterláncot az adatok beolvasásáért.

1. Hozzon létre egy [indexmezők](https://docs.microsoft.com/rest/api/searchservice/create-skillset) Adatbővítés lépésekkel.

1. Adja meg a [indexsémát](https://docs.microsoft.com/rest/api/searchservice/create-index). A *mezők* forrásadatok mezőit a gyűjteménybe. Meg is helyettes ki további mezőket létrehozott értékek Adatbővítés során létrehozott tartalom tárolásához.

1. Adja meg a [indexelő](https://docs.microsoft.com/rest/api/searchservice/create-skillset) hivatkozik az adatforrást, a készségeitől és az index.

1. Az indexelő belül adjon hozzá *outputFieldMappings*. Ebben a szakaszban (a 3. lépésben) készségeitől kimenete (a 4. lépés) az indexséma bemenetek mezőihez rendeli hozzá.

1. Küldés *indexelő létrehozása* kérelem a létrehozott (POST kérelem a kérelem törzsében szereplő az indexelő meghatározását a) az indexelő az Azure Search Express. Ez a lépés nem futtatunk az indexelő a folyamat meghívását.

1. Eredmények kiértékelése és a kód frissítése ismereteket, séma vagy az indexelő konfigurációjának módosítása lekérdezéseket futtathat.

1. [Az indexelő alaphelyzetbe állítása](search-howto-reindex.md) a folyamat újbóli létrehozása előtt.

További információ a konkrét kérdések és problémák: [hibaelhárítási tippek](cognitive-search-concept-troubleshooting.md).

## <a name="next-steps"></a>További lépések

+ [A kognitív keresés dokumentációja](cognitive-search-resources-documentation.md)
+ [Rövid útmutató: A portál az útmutató a kognitív keresés kipróbálása](cognitive-search-quickstart-blob.md)
+ [Oktatóanyag: Ismerje meg, a cognitive search API-k](cognitive-search-tutorial-blob.md)
