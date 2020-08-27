---
title: A blob Storage-beli adatkezelési szolgáltatással való Ismerkedés
titleSuffix: Azure Cognitive Search
description: Szemantikai, természetes nyelvi feldolgozási és képelemzési adatok hozzáadása az Azure-blobokhoz az Azure Cognitive Search AI-bővítési folyamatának használatával.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: ce5eafe0b36f07d8de366b6d4adb92e894fcb67e
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/26/2020
ms.locfileid: "88936741"
---
# <a name="use-ai-to-understand-blob-storage-data"></a>A blob Storage-beli adatkezelési szolgáltatással való Ismerkedés

Az Azure Blob Storage-ban tárolt adatokat gyakran különböző strukturálatlan tartalmak, például képek, hosszú szöveges, PDF-és Office-dokumentumok. Az Azure Cognitive Search AI-képességeinek használatával számos módon megismerheti és kinyerheti a Blobok értékes információit. Példák a mesterséges intelligenciát tartalmazó tartalom alkalmazására:

+ Képekből származó szöveg kinyerése az optikai karakterfelismeréssel (OCR)
+ Jelenet leírása vagy Címkék készítése egy fényképből
+ Nyelv felismerése és szöveg lefordítása különböző nyelvekre
+ Feldolgozhatja az elnevezett entitás-felismeréssel rendelkező szöveget, és megkeresheti a személyekre, dátumokra, helyekre vagy szervezetekre mutató hivatkozásokat. 

Habár előfordulhat, hogy csak az egyik ilyen AI-képességre van szüksége, közösen kombinálja őket ugyanabba a folyamatba (például a szöveg kinyerése egy beolvasott képből, majd a benne hivatkozott összes dátum és hely megkeresése). 

A mesterséges intelligencia-gazdagítás új adatokat hoz létre, amelyeket szövegként rögzítettek, mezőkben tárolva. A bővítés után a teljes szöveges kereséssel elérheti ezeket az információkat egy keresési indexből, vagy az Azure Storage-ba is elküldheti a bővített dokumentumokat, így új alkalmazási élményeket érhet el, amelyek magukban foglalják a felderítési vagy elemzési forgatókönyvek adatait. 

Ebben a cikkben egy széles lencsén keresztül tekintjük meg a mesterséges intelligenciát, hogy gyorsan fel lehessen fogni a teljes folyamatot, a blobokban lévő nyers adatok átalakításával, vagy egy keresési indexben vagy egy Tudásbázisban.

## <a name="what-it-means-to-enrich-blob-data-with-ai"></a>Mit jelent a blob-adatelemzés a mesterséges intelligenciával

Az *AI* -bővítés az Azure Cognitive Search indexelési architektúrájának része, amely integrálja a Microsoft vagy az Ön által megadott egyéni AI-t a beépített mesterséges intelligenciával. Segít olyan végpontok közötti forgatókönyvek megvalósításában, amelyekben a blobokat (a meglévőket és az újakat is beleértve, vagy azok frissítését) kell feldolgoznia. a képek és a szövegek kinyeréséhez nyissa meg az összes formátumot 

A bemenetek a Blobok egyetlen tárolóban, az Azure Blob Storage-ban. A Blobok szinte bármilyen típusú szöveges vagy képi adatok lehetnek. 

A kimenet mindig keresési index, amely a gyors szöveges kereséshez, a lekérésekhez és a feltáráshoz használható az ügyfélalkalmazások számára. Emellett a kimenet olyan *Tudásbázis* is lehet, amely az Azure-blobokban vagy az adatelemzési számítási feladatokban lévő eszközökön (például Power bi vagy adatelemző munkaterhelésekben) lévő, a kibővített dokumentumokat is kibővíti

A két között a folyamat architektúrája. A folyamat az *Indexelő* szolgáltatáson alapul, amelyhez hozzárendelhet egy *készségkészlet*, amely egy vagy több, a mesterséges intelligenciát biztosító *szaktudásból* áll. A folyamat célja, hogy olyan *dúsított dokumentumokat* hozzon létre, amelyek nyers tartalomként jelennek meg, de további szerkezetet, környezetet és információt vesznek fel a folyamaton keresztül. Az indexelés során a rendszer felhasználja a bővített dokumentumokat, így fordított indexeket és más, teljes szöveges keresésben vagy feltárásban és elemzésben használt struktúrákat hozhat létre.

## <a name="start-with-services"></a>Kezdés a szolgáltatásokkal

Az Azure Cognitive Search és az Azure Blob Storage szükséges. A blob Storage-ban olyan tárolóra van szükség, amely a forrás tartalmát biztosítja.

A Storage-fiók portálján közvetlenül is elindíthatja a lapot. A bal oldali navigációs oldal **blob Service** területén kattintson az **Azure Cognitive Search hozzáadása** elemre egy új szolgáltatás létrehozásához, vagy válasszon ki egy meglévőt. 

Miután hozzáadta az Azure-Cognitive Search a Storage-fiókjához, az Azure-adatforrásokban lévő adatforrások standard folyamatát követheti. Javasoljuk, hogy az Azure Cognitive Searchban az **adatimportálás** VARÁZSLÓT az AI-bővítés egyszerű bevezetéséhez. Ez a rövid útmutató végigvezeti a következő lépéseken: [AI-dúsítási folyamat létrehozása a portálon](cognitive-search-quickstart-blob.md). 

A következő részekben további összetevőket és fogalmakat vizsgálunk.

## <a name="use-a-blob-indexer"></a>BLOB-indexelő használata

Az AI-bővítés egy indexelési folyamat bővítménye, és az Azure Cognitive Searchban ezek a folyamatok egy *Indexelő*fölé épülnek. Az indexelő egy adatforrást támogató alszolgáltatás, amely belső logikával rendelkezik a mintavételi adatokhoz, a metaadatok beolvasásához, az adatok lekéréséhez és az adatok natív formátumokból való szerializálásához a további importáláshoz. Az indexelő gyakran saját maguk használják az importáláshoz, elkülönítve az AI-től, de ha mesterséges intelligenciát szeretne létrehozni, szüksége lesz egy indexelő és egy készségkészlet. Ez a szakasz kiemeli az Indexelő; a következő szakasz a szakértelmével koncentrál.

Az Azure Storage-beli Blobok indexelése az [azure Cognitive Search blob Storage indexelő](search-howto-indexing-azure-blob-storage.md)használatával történik. Az indexelő az **adatimportálás** varázsló, a REST API vagy a .net SDK használatával hívható meg. A kódban ezt az indexelő kell használnia a típus megadásával, valamint az Azure Storage-fiókkal és a blob-tárolóval együtt tartalmazó kapcsolatok adatainak biztosításával. A blobokat egy virtuális könyvtár létrehozásával, amelyet aztán paraméterként adhat át, vagy szűrheti a fájltípusok kiterjesztését.

Az indexelő a "dokumentum repedése", a Blobok megnyitása a tartalom vizsgálatához. Az adatforráshoz való csatlakozás után ez a folyamat első lépése. A blob-adatok esetében itt a PDF, az Office-dokumentumok, a képek és más tartalomtípusok észlelhetők. A szöveg kibontásával nem számítunk fel díjat. A képek kinyerésével kapcsolatos nagy mennyiségű dokumentum díjait a [díjszabás lapon](https://azure.microsoft.com/pricing/details/search/)találja.

Bár az összes dokumentum meg lesz repedt, a dúsítás csak akkor történik meg, ha explicit módon megadja a szükséges képességeket. Ha például a folyamat kizárólag képelemzésből áll, akkor a rendszer figyelmen kívül hagyja a tárolóban vagy a dokumentumokban lévő szöveget.

A blob indexelő konfigurációs paramétereket tartalmaz, és támogatja a változások követését, ha az alapul szolgáló adatok elegendő információt biztosítanak. További információ az [Azure Cognitive Search blob Storage indexelő](search-howto-indexing-azure-blob-storage.md)alapfunkcióival kapcsolatban.

## <a name="add-ai-components"></a>AI-összetevők hozzáadása

Az AI-dúsítás olyan modulokra utal, amelyek mintákat vagy tulajdonságokat keresnek, majd ennek megfelelően hajtanak végre egy műveletet. Az Arcfelismerés a fényképekben, a fényképek szöveges leírásában, a dokumentumokban található kulcsfontosságú kifejezések észlelésében, valamint az OCR (vagy a nyomtatott vagy kézírásos szöveg felismerése bináris fájlokban) szemléltető példákat mutat be.

Az Azure Cognitive Searchban a *képességek* a mesterséges intelligencia-feldolgozás egyes összetevői, amelyek önállóan vagy más képességekkel kombinálva is használhatók. 

+ A beépített képességeket Cognitive Services támogatja, és a képek elemzése a Computer Visionon alapul, és a természetes nyelvi feldolgozás a Text Analytics alapján történik. A teljes lista a tartalom- [gazdagítás beépített képességei](cognitive-search-predefined-skills.md)című témakörben található.

+ Az egyéni képességek egyéni kódok, amelyek egy olyan [illesztőfelület-definícióba](cognitive-search-custom-skill-interface.md) vannak becsomagolva, amely lehetővé teszi az integrációt a folyamatba. Az ügyfél-megoldásokban általános gyakorlat, hogy mindkettőt a nyílt forráskódú, harmadik féltől származó vagy az első féltől származó AI-modulokat biztosító egyéni ismeretekkel együtt használja.

A *készségkészlet* a folyamatokban használt szaktudás gyűjteménye, amely a dokumentum repedési fázisának meghívása után válik elérhetővé. Az indexelő pontosan egy készségkészlet tud felhasználni, de a készségkészlet egy indexelő függetlenül létezik, így más forgatókönyvekben is felhasználható.

Az egyéni képességek bonyolultak lehetnek, de a megvalósítás szempontjából egyszerűek és egyszerűen megtekinthetők. Ha olyan meglévő csomagokat használ, amelyek minta-megfeleltetési vagy besorolási modelleket biztosítanak, a blobokból kinyert tartalmat átadhatja a feldolgozásra. Mivel az AI-bővítés Azure-alapú, a modellnek az Azure-on is kell lennie. Néhány gyakori üzemeltetési módszer [Azure functions](cognitive-search-create-custom-skill-example.md) vagy [tárolók](https://github.com/Microsoft/SkillsExtractorCognitiveSearch)használatát is magában foglalja.

A Cognitive Services által támogatott beépített készségekhez szükség van egy [csatolt Cognitive Servicesra](cognitive-search-attach-cognitive-services.md) , amely hozzáférést biztosít az erőforráshoz. Egy teljes körű kulcs a képek elemzéséhez, a nyelvfelismerés, a szöveg fordításához és a szöveges elemzésekhez. Az egyéb beépített képességek az Azure Cognitive Search funkciói, és nem igényelnek további szolgáltatást vagy kulcsot. A Text formáló, a Splitter és az egyesítés például olyan segítő képességekre mutat, amelyek néha szükségesek a folyamat tervezésekor.

Ha kizárólag egyéni képességeket és beépített segédprogram-képességeket használ, akkor nincs Cognitive Serviceshoz kapcsolódó függőség vagy költség.

<!-- ## Order of operations

Now we've covered indexers, content extraction, and skills, we can take a closer look at pipeline mechanisms and order of operations.

A skillset is a composition of one or more skills. When multiple skills are involved, the skillset operates as sequential pipeline, producing dependency graphs, where output from one skill becomes input to another. 

For example, given a large blob of unstructured text, a sample order of operations for text analytics might be as follows:

1. Use Text Splitter to break the blob into smaller parts.
1. Use Language Detection to determine if content is English or another language.
1. Use Text Translator to get all text into a common language.
1. Run Entity Recognition, Key Phrase Extraction, or Sentiment Analysis on chunks of text. In this step, new fields are created and populated. Entities might be location, people, organization, dates. Key phrases are short combinations of words that appear to belong together. Sentiment score is a rating on continuum of negative (0) to positive (1) sentiment.
1. Use Text Merger to reconstitute the document from the smaller chunks. -->

## <a name="consume-ai-enriched-output-in-downstream-solutions"></a>Mesterséges intelligenciát használó kimenet felhasználása az alsóbb rétegbeli megoldásokban

Az AI-bővítés kimenete az Azure-Cognitive Search keresési indexe vagy az Azure Storage-ban található [Tudásbázis](knowledge-store-concept-intro.md) .

Az Azure Cognitive Searchban egy keresési indexet használunk az interaktív feltáráshoz, amely ingyenes szöveges és szűrt lekérdezéseket használ egy ügyfélalkalmazás számára. Az AI-n keresztül létrehozott bővített dokumentumok JSON formátumban vannak formázva, és ugyanúgy vannak indexelve, mint az összes dokumentum az Azure Cognitive Search-ban, az indexelő által biztosított összes előnyt kihasználva. Az indexelés során például a blob indexelő a konfigurációs paraméterekre és beállításokra hivatkozik bármely mező-hozzárendelés vagy az észlelési logika kihasználása érdekében. Ezek a beállítások teljes mértékben elérhetők a rendszeres indexeléshez és a mesterséges intelligenciával bővített számítási feladatokhoz. Az indexelés után, ha a tartalom az Azure Cognitive Searchon van tárolva, gazdag lekérdezéseket hozhat létre, és szűrheti a tartalmakat a tartalom megismerése érdekében.

Az Azure Storage-ban a Knowledge Store két megnyilvánulással rendelkezik: egy blob-tárolóval vagy a Table Storage tábláival. 

+ A blob-tárolók teljes egészében rögzítik a dúsított dokumentumokat, ami akkor lehet hasznos, ha más folyamatokra szeretne csatlakozni. 

+ Ezzel szemben a Table Storage a dúsított dokumentumok fizikai kivetítéseit is képes kezelni. Létrehozhat szeleteket vagy olyan dúsított dokumentumok rétegeit, amelyek meghatározott részeket tartalmaznak vagy kizárhatnak. A Power BI elemzéséhez az Azure Table Storage táblái az adatforrások lesznek a további vizualizációk és feltárások számára.

A folyamat végén található dúsított dokumentumok eltérnek az eredeti bemeneti verziótól, mert további, a dúsítás során kinyert vagy létrehozott adatokat tartalmazó mezők jelennek meg. Így az eredeti és a létrehozott tartalom kombinációjával is dolgozhat, függetlenül attól, hogy melyik kimeneti struktúrát használja.

## <a name="next-steps"></a>További lépések

A mesterséges intelligenciával sokkal többet is megtudhat az Azure Storage-ban tárolt adatairól, többek között a Cognitive Services különböző módokon történő kombinálásával, valamint egyéni ismeretek készítésével olyan esetekre, amikor a forgatókönyvhöz nem áll rendelkezésre kognitív szolgáltatás. Az alábbi hivatkozásokra kattintva többet is megtudhat.

+ [Blobok feltöltése, letöltése és listázása a Azure Portal (Azure Blob Storage)](../storage/blobs/storage-quickstart-blobs-portal.md)
+ [BLOB-indexelő beállítása (Azure Cognitive Search)](search-howto-indexing-azure-blob-storage.md) 
+ [AI-dúsítás áttekintése (Azure Cognitive Search)](cognitive-search-concept-intro.md) 
+ [Készségkészlet létrehozása (Azure Cognitive Search)](cognitive-search-defining-skillset.md)
+ [Csomópontok leképezése egy jegyzet fájában (Azure Cognitive Search)](cognitive-search-output-field-mapping.md)