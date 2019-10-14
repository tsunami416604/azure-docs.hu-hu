---
title: A Blobok használatának ismertetése a mesterséges intelligenciával
titleSuffix: Azure Search
description: Szemantikai, természetes nyelvi feldolgozási és képelemzési adatok hozzáadása az Azure-blobokhoz a Azure Search mesterséges intelligencia-gazdagító folyamatával.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: search
ms.topic: conceptual
ms.date: 10/09/2019
ms.openlocfilehash: 2513825fcb275aeb3c4f0ca49ff5f2a6bd9441f0
ms.sourcegitcommit: bd4198a3f2a028f0ce0a63e5f479242f6a98cc04
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/14/2019
ms.locfileid: "72303014"
---
# <a name="use-ai-to-understand-blob-data"></a>A Blobok használatának ismertetése a mesterséges intelligenciával

Az Azure Blob Storage-ban tárolt adatokat gyakran különböző strukturálatlan tartalmak, például képek, hosszú szöveges, PDF-és Office-dokumentumok. A Azure Search AI-képességeinek használatával számos módon megismerheti és kinyerheti a Blobok értékes információit. Példák a mesterséges intelligenciát tartalmazó tartalom alkalmazására:

+ Képekből származó szöveg kinyerése az optikai karakterfelismeréssel (OCR)
+ Jelenet leírása vagy Címkék készítése egy fényképből
+ Nyelv felismerése és szöveg lefordítása különböző nyelvekre
+ Feldolgozhatja az elnevezett entitás-felismeréssel rendelkező szöveget, és megkeresheti a személyekre, dátumokra, helyekre vagy szervezetekre mutató hivatkozásokat. 

Habár előfordulhat, hogy csak az egyik ilyen AI-képességre van szüksége, közösen kombinálja őket ugyanabba a folyamatba (például a szöveg kinyerése egy beolvasott képből, majd a benne hivatkozott összes dátum és hely megkeresése). 

A mesterséges intelligencia-gazdagítás új adatokat hoz létre, amelyeket szövegként rögzítettek, mezőkben tárolva. A bővítés után a teljes szöveges kereséssel elérheti ezeket az információkat egy keresési indexből, vagy az Azure Storage-ba is elküldheti a bővített dokumentumokat, így új alkalmazási élményeket érhet el, amelyek magukban foglalják a felderítési vagy elemzési forgatókönyvek adatait. 

Ebben a cikkben egy széles lencsén keresztül tekintjük meg a mesterséges intelligenciát, hogy gyorsan fel lehessen fogni a teljes folyamatot, a blobokban lévő nyers adatok átalakításával, vagy egy keresési indexben vagy egy Tudásbázisban.

## <a name="what-it-means-to-enrich-blob-data"></a>Mit jelent a blob-adatelemzés

Az *AI* -bővítés az Azure Search indexelési architektúrájának része, amely integrálja a Microsoft vagy az Ön által megadott egyéni AI-t a beépített mesterséges intelligenciával. Lehetővé teszi a teljes körű forgatókönyvek megvalósítását, ahol a blobokat (a meglévőket és az újakat is beleértve vagy azok frissítését) kell feldolgoznia, a képek és a szövegek kinyeréséhez, a különböző AI-funkciók használatával kinyerheti a kívánt információkat, és indexelheti őket egy Azure Search indexben a gyors kereséshez, a lekéréshez és a feltáráshoz. 

A bemenetek a Blobok egyetlen tárolóban, az Azure Blob Storage-ban. A Blobok szinte bármilyen típusú szöveges vagy képi adatok lehetnek. 

A kimenet mindig Azure Search index, amely a gyors szöveges kereséshez, a lekérésekhez és a feltáráshoz használható az ügyfélalkalmazások számára. Emellett a kimenet olyan *Tudásbázis* is lehet, amely az Azure-blobokban vagy az adatelemzési számítási feladatokban lévő eszközökön (például Power bi vagy adatelemző munkaterhelésekben) lévő, a kibővített dokumentumokat is kibővíti

A két között a folyamat architektúrája. A folyamat az *Indexelő* szolgáltatáson alapul, amelyhez hozzárendelhet egy *készségkészlet*, amely egy vagy több, a mesterséges intelligenciát biztosító *szaktudásból* áll. A folyamat célja, hogy olyan *dúsított dokumentumokat* hozzon létre, amelyek nyers tartalomként jelennek meg, de további szerkezetet, környezetet és információt vesznek fel a folyamaton keresztül. Az indexelés során a rendszer felhasználja a bővített dokumentumokat, így fordított indexeket és más, teljes szöveges keresésben vagy feltárásban és elemzésben használt struktúrákat hozhat létre.

## <a name="how-to-get-started"></a>A szolgáltatások használatba vétele

A Storage-fiók portálján közvetlenül is elindíthatja a lapot. Kattintson a **Azure Search hozzáadása** lehetőségre, és hozzon létre egy új Azure Search szolgáltatást, vagy válasszon ki egy meglévőt. Ha már rendelkezik egy meglévő keresési szolgáltatással ugyanahhoz az előfizetéshez, a **Azure Search Hozzáadás** gombra kattintva megnyithatja az adatimportálás varázslót, így azonnal áttekintheti az indexelést, a bővítést és az index definícióját.

Miután hozzáadta Azure Search a Storage-fiókjához, a szabványos folyamat használatával bővítheti az összes Azure-adatforrásban tárolt adatait. Feltételezve, hogy már rendelkezik blob-tartalommal, a Azure Search az adatimportálás varázslót használhatja az AI-bővítés egyszerű első bevezetéséhez. Ez a rövid útmutató ismerteti a következő lépéseket: [AI-dúsítási folyamat létrehozása a portálon](cognitive-search-quickstart-blob.md). 

A következő részekben további összetevőket és fogalmakat vizsgálunk.

## <a name="use-blob-indexers"></a>BLOB-indexelő használata

Az AI-bővítés egy indexelési folyamat bővítménye, és Azure Searchban ezek a folyamatok egy *Indexelő*fölé épülnek. Az indexelő egy adatforrást támogató alszolgáltatás, amely belső logikával rendelkezik a mintavételi adatokhoz, a metaadatok beolvasásához, az adatok lekéréséhez és az adatok natív formátumokból való szerializálásához a további importáláshoz. Az indexelő gyakran saját maguk használják az importáláshoz, elkülönítve az AI-től, de ha mesterséges intelligenciát szeretne létrehozni, szüksége lesz egy indexelő és egy készségkészlet. Ebben a szakaszban az indexelő fogunk összpontosítani.

Az Azure Storage-beli Blobok indexelése a [Azure Search blob Storage indexelő](search-howto-indexing-azure-blob-storage.md)használatával történik. Ezt az indexelő úgy hívja meg, hogy beállítja a típust, és olyan kapcsolatokat biztosít, amely tartalmaz egy Azure Storage-fiókot a blob-tárolóval együtt. Ha korábban már szervezett blobokat egy virtuális könyvtárba, amelyet aztán paraméterként adhat át, a blob indexelő lekéri a teljes tárolóból.

Az indexelő a "dokumentum repedése", az adatforráshoz való csatlakozás után pedig a folyamat első lépése. A blob-adatok esetében itt a PDF, az Office-dokumentumok, a képek és más tartalomtípusok észlelhetők. A szöveg kibontásával nem számítunk fel díjat. A képek kibontásával kapcsolatos dokumentum a Azure Search [díjszabási oldalán](https://azure.microsoft.com/pricing/details/search/)megtalált díjszabás szerint kerül kiszámlázásra.

Bár az összes dokumentum meg lesz repedt, a dúsítás csak akkor történik meg, ha explicit módon megadja a szükséges képességeket. Ha például a folyamat kizárólag szöveges elemzésből áll, a tárolóban vagy a dokumentumokban lévő összes rendszerkép figyelmen kívül lesz hagyva.

A blob indexelő konfigurációs paramétereket tartalmaz, és támogatja a változások követését, ha az alapul szolgáló adatok elegendő információt biztosítanak. További információt a [Azure Search blob Storage-indexelő](search-howto-indexing-azure-blob-storage.md)alapfunkciói című témakörben olvashat.

## <a name="add-ai"></a>AI hozzáadása

A *képességek* az AI-feldolgozás egyes összetevői, amelyek önálló vagy más, szekvenciális feldolgozásra alkalmas képességekkel kombinálva használhatók. 

+ A beépített képességeket Cognitive Services támogatja, és a képek elemzése a Computer Visionon alapul, és a természetes nyelvi feldolgozás a Text Analytics alapján történik. Néhány példa az [OCR](cognitive-search-skill-ocr.md), az [entitások felismerése](cognitive-search-skill-entity-recognition.md)és a [képek elemzése](cognitive-search-skill-image-analysis.md). A beépített képességek teljes listáját megtekintheti a [tartalom-gazdagítás előre definiált képességeiben](cognitive-search-predefined-skills.md).

+ Az egyéni képességek egyéni kódok, amelyek egy olyan illesztőfelület-definícióba vannak becsomagolva, amely lehetővé teszi az integrációt a folyamatba. Az ügyfél-megoldásokban általános gyakorlat, hogy mindkettőt a nyílt forráskódú, harmadik féltől származó vagy az első féltől származó AI-modulokat biztosító egyéni ismeretekkel együtt használja.

A *készségkészlet* a folyamatokban használt szaktudás gyűjteménye, amely a dokumentum repedési fázisának meghívása után válik elérhetővé. Az indexelő pontosan egy készségkészlet tud felhasználni, de a készségkészlet egy indexelő függetlenül létezik, így más forgatókönyvekben is felhasználható.

Az egyéni képességek bonyolultak lehetnek, de a megvalósítás szempontjából egyszerűek és egyszerűen megtekinthetők. Ha olyan meglévő csomagokat használ, amelyek minta-megfeleltetési vagy besorolási modelleket biztosítanak, a blobokból kinyert tartalmat átadhatja a feldolgozásra. Mivel az AI-bővítés Azure-alapú, a modellnek az Azure-on is kell lennie. Néhány gyakori üzemeltetési módszer [Azure functions](cognitive-search-create-custom-skill-example.md) vagy [tárolók](https://github.com/Microsoft/SkillsExtractorCognitiveSearch)használatát is magában foglalja.

A Cognitive Services által támogatott beépített készségekhez szükség van egy [csatolt Cognitive Servicesra](cognitive-search-attach-cognitive-services.md) , amely hozzáférést biztosít az erőforráshoz. Egy teljes körű kulcs a képek elemzéséhez, a nyelvfelismerés, a szöveg fordításához és a szöveges elemzésekhez. A többi beépített képesség Azure Search szolgáltatásai, és nincs szükség további szolgáltatásra vagy kulcsra. A Text formáló, a Splitter és az egyesítés például olyan segítő képességekre mutat, amelyek néha szükségesek a folyamat tervezésekor.

Ha kizárólag egyéni képességeket és beépített segédprogram-képességeket használ, akkor nincs Cognitive Serviceshoz kapcsolódó függőség vagy költség.

## <a name="order-of-operations"></a>Műveletek sorrendje

Most, hogy az indexelő, a tartalom kinyerése és a képességek is megtalálhatók, alaposabban szemügyre vesszük a folyamat mechanizmusait és a műveletek sorrendjét.

A készségkészlet egy vagy több ismeret összetétele. Ha több ismeretre van szükség, a készségkészlet szekvenciális folyamatként működik, és függőségi diagramokat hoz létre, ahol az egyik képesség kimenete egy másikba kerül. 

Ha például egy nagy méretű, strukturálatlan szöveget tartalmazó blobot adott meg, a szöveges elemzésekhez tartozó műveletek mintavételi sorrendje a következő lehet:

1. A szöveges osztó használatával a blobot kisebb részekre lehet bontani.
1. A Nyelvfelismerés használatával megállapíthatja, hogy a tartalom angol vagy más nyelven van-e.
1. A Text Translator használatával minden szöveg egy közös nyelven olvasható be.
1. Entitások felismerésének, Kulcsszókeresésának vagy Hangulatelemzésének futtatása a szövegben. Ebben a lépésben új mezőket hoz létre és tölt fel. Lehetséges, hogy az entitások helye, személy, szervezet, dátum. A legfontosabb kifejezések olyan szavak rövid kombinációi, amelyek úgy jelennek meg, hogy együtt jelenjenek meg. A hangulati pontszám a negatív (0) és a pozitív (1) hangulatnak megfelelő Értékelés.
1. A szöveges egyesítés használatával visszaalakíthatja a dokumentumot a kisebb adattömbökből.


## <a name="outputs-and-use-cases"></a>Kimenetek és használati esetek

A folyamat végén található dúsított dokumentumok eltérnek az eredeti bemeneti verziótól, mert további, a dúsítás során kinyert vagy létrehozott adatokat tartalmazó mezők jelennek meg. Így több módon is dolgozhat az eredeti és a létrehozott értékek kombinálásával.

A kimeneti űrlapok a Azure Search keresési indexei, illetve az Azure Storage-ban található tudásbázisok.

Azure Search a bővített dokumentumok a JSON-ban vannak formázva, és ugyanúgy indexelhető, mint az összes dokumentum indexelve, és az előnyeit az indexelő biztosítja. A dúsított dokumentumok mezői egy index sémára vannak leképezve. Az indexelés során a blob indexelő a konfigurációs paraméterekre és beállításokra hivatkozik bármely mező-hozzárendelés vagy a megadott észlelési logika kihasználása érdekében. Az indexelés után, ha a tartalom Azure Search van tárolva, gazdag lekérdezéseket hozhat létre, és szűrheti a tartalmakat a tartalom megismeréséhez.

Az Azure Storage-ban a Knowledge Store két megnyilvánulással rendelkezik: egy blob-tárolóval vagy a Table Storage tábláival. A blob-tárolók teljes egészében rögzítik a dúsított dokumentumokat, ami akkor lehet hasznos, ha más folyamatokra szeretne csatlakozni. Ezzel szemben a Table Storage a dúsított dokumentumok fizikai kivetítéseit is képes kezelni. Létrehozhat szeleteket vagy olyan dúsított dokumentumok rétegeit, amelyek meghatározott részeket tartalmaznak vagy kizárhatnak. A Power BI elemzéséhez az Azure Table Storage táblái az adatforrások lesznek a további vizualizációk és feltárások számára.

## <a name="next-steps"></a>Következő lépések

A mesterséges intelligenciával sokkal többet is megtudhat az Azure Storage-ban tárolt adatairól, többek között a Cognitive Services különböző módokon történő kombinálásával, valamint egyéni ismeretek készítésével olyan esetekre, amikor a forgatókönyvhöz nem áll rendelkezésre kognitív szolgáltatás. Az alábbi hivatkozásokra kattintva többet is megtudhat.

> [!div class="nextstepaction"]
> [AI](cognitive-search-concept-intro.md)-bővítés áttekintése  
> [készségkészlet létrehozása](cognitive-search-defining-skillset.md)
> [Térkép csomópontok egy jegyzet fájában](cognitive-search-output-field-mapping.md)
