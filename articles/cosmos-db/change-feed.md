---
title: A módosítási hírcsatorna támogatásának használata Azure Cosmos DB
description: A dokumentumok változásainak nyomon követéséhez és az eseményvezérelt feldolgozások (például triggerek) és a gyorsítótárak és az elemzési rendszerek naprakészen tartásához használja a Azure Cosmos DB módosítási csatorna támogatását.
author: TheovanKraay
ms.author: thvankra
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 11/25/2019
ms.reviewer: sngun
ms.custom: seodec18
ms.openlocfilehash: eef950c4e8c4a880d331022ed60477bebce65b5d
ms.sourcegitcommit: 48b7a50fc2d19c7382916cb2f591507b1c784ee5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/02/2019
ms.locfileid: "74689092"
---
# <a name="change-feed-in-azure-cosmos-db---overview"></a>Azure Cosmos DB adatcsatorna módosítása – áttekintés

Ha módosítani szeretné a hírcsatornák támogatását Azure Cosmos DB működik, egy Azure Cosmos-tárolóban figyelheti a módosításokat. Ezután a módosításuk sorrendjében felsorolja a módosított dokumentumokat. A módosítások meg lesznek őrizve, feldolgozhatók aszinkron és fokozatos módon is, a kimenet pedig több fogyasztó között is elosztható a párhuzamos feldolgozáshoz. 

A Azure Cosmos DB kiválóan alkalmas a IoT, a játékok, a kiskereskedelmi és az operatív naplózási alkalmazások számára. Ezeknek az alkalmazásoknak a közös kialakítási mintája az, hogy a további műveletek elindításához használja az adatmódosításokat. Példák a további műveletekre:

* Értesítés vagy API meghívásának indítása, amikor egy elem be van helyezve vagy frissül.
* Valós idejű adatfolyam-feldolgozás a IoT vagy a valós idejű elemzési folyamatok esetében az operatív adatfeldolgozás során.
* További adatáthelyezés a gyorsítótárral vagy a keresőmotorral vagy adattárházral történő szinkronizálással, vagy az adattárolással a hűtőházi tárolóba.

A Azure Cosmos DB változási csatornája lehetővé teszi, hogy hatékony és méretezhető megoldásokat hozzon létre az egyes mintákhoz, ahogy az alábbi képen is látható:

![A Azure Cosmos DB változási csatornájának használata valós idejű elemzési és eseményvezérelt számítástechnikai forgatókönyvekhez](./media/change-feed/changefeedoverview.png)

## <a name="supported-apis-and-client-sdks"></a>Támogatott API-k és ügyféloldali SDK-k

Ezt a funkciót jelenleg a következő Azure Cosmos DB API-k és ügyféloldali SDK-k támogatják.

| **Ügyfél-illesztőprogramok** | **Azure CLI** | **SQL API** | **Azure Cosmos DB a Cassandra-hez készült API** | **Azure Cosmos DB API-MongoDB** | **Gremlin API**|**Tábla API** |
| --- | --- | --- | --- | --- | --- | --- |
| .NET | n/a | Igen | Igen | Igen | Igen | Nem |
|Java|n/a|Igen|Igen|Igen|Igen|Nem|
|Python|n/a|Igen|Igen|Igen|Igen|Nem|
|Node/JS|n/a|Igen|Igen|Igen|Igen|Nem|

## <a name="change-feed-and-different-operations"></a>A hírcsatorna és a különböző műveletek módosítása

Ma az összes művelet megjelenik a változási hírcsatornában. Az a funkció, ahol a változási csatornát szabályozhatja, bizonyos műveletekhez, például a frissítésekhez, és nem szúrja be még nem érhető el. A frissítésekhez és szűréshez hozzáadhat egy "Soft marker" elemet a változási csatornán lévő elemek feldolgozásakor. Az aktuális változási csatorna nem törli a naplókat. Az előző példához hasonlóan hozzáadhat egy lágy jelölőt is a törölt elemekhez, például hozzáadhat egy attribútumot a "törölt" nevű elemhez, és beállíthatja azt "true" értékre, és beállíthatja az elem ÉLETTARTAMát, hogy az automatikusan törölhető legyen. Elolvashatja a változási csatornát a korábbi elemek esetében (az elemnek megfelelő legutóbbi módosítást, nem tartalmazza a köztes változásokat), például az öt évvel ezelőtt hozzáadott elemeket. Ha az objektum nem törlődik, a változási csatornát a tároló forrásaként is elolvashatja.

### <a name="sort-order-of-items-in-change-feed"></a>Elemek rendezési sorrendje a változási hírcsatornában

A hírcsatorna-elemek módosítása a módosítási idő sorrendjében történik. Ez a rendezési sorrend logikai partíciós kulcs alapján garantált.

### <a name="change-feed-in-multi-region-azure-cosmos-accounts"></a>A többrégiós Azure Cosmos-fiókok csatornájának módosítása

Ha egy többrégiós Azure Cosmos-fiókban egy írási régió feladatátvételt hajt végre, akkor a változási csatorna a manuális feladatátvételi művelet során fog működni, és összefüggő lesz.

### <a name="change-feed-and-time-to-live-ttl"></a>A hírcsatorna és az élettartam (TTL) módosítása

Ha a TTL (time to Live) tulajdonság egy elemre van állítva, akkor a változási csatorna örökre fennmarad. Ha a rendszer nem törli az adatvesztést, akkor a változási hírcsatornában marad.  

### <a name="change-feed-and-_etag-_lsn-or-_ts"></a>A hírcsatorna és a _etag módosítása, _lsn vagy _ts

A _etag formátuma belső, és nem érdemes attól függővé tenni, mert bármikor megváltozhat. _ts egy módosítás vagy egy létrehozási időbélyeg. A _ts a kronológiai összehasonlításhoz használható. _lsn egy olyan batch-azonosító, amely csak a változási hírcsatornához lett hozzáadva; Ez a tranzakció AZONOSÍTÓját jelöli. Előfordulhat, hogy számos elemnek ugyanaz a _lsna. A FeedResponse lévő ETag eltér az elemen látható _etagtól. a _etag egy belső azonosító, amelyet a rendszer az elem verziójával kapcsolatos Egyidejűség-vezérlésre használ, míg a ETag a hírcsatorna előkészítésére szolgál.

## <a name="change-feed-use-cases-and-scenarios"></a>Hírcsatorna-használati esetek és forgatókönyvek módosítása

A módosítási hírcsatorna lehetővé teszi a nagy adatkészletek hatékony feldolgozását nagy mennyiségű írási művelettel. A Change feed a teljes adatkészlet lekérdezését is lehetővé teszi a megváltozott változások azonosítására.

### <a name="use-cases"></a>Használati példák

A változási hírcsatornával például a következő feladatokat végezheti el hatékonyan:

* Frissítsen egy gyorsítótárat, frissítsen egy keresési indexet, vagy frissítsen egy adattárházat Azure Cosmos DBban tárolt adattal.

* Egy alkalmazás szintű adatréteget és archiválást valósíthat meg, például a "gyakori adatok" tárolása Azure Cosmos DB és a "hideg adatok" más tárolási rendszerekre, például az [Azure Blob Storage](../storage/common/storage-introduction.md).

* Nulla idejű áttelepítést végezhet egy másik Azure Cosmos-fiókba vagy egy másik Azure Cosmos-tárolóba egy másik logikai partíciós kulccsal.

* [Lambda-architektúra](https://blogs.technet.microsoft.com/msuspartner/2016/01/27/azure-partner-community-big-data-advanced-analytics-and-lambda-architecture/) implementálása Azure Cosmos db használatával, ahol a Azure Cosmos db a valós idejű, a kötegelt és a lekérdezési rétegeket is támogatja, ami lehetővé teszi a lambda architektúra alacsony TCO-vel való használatát.

* Az adatok az eszközökről, érzékelőkről, infrastruktúrából és alkalmazásokból érkeznek és tárolhatók, és valós időben dolgozzák fel ezeket az eseményeket, például a [Spark](../hdinsight/spark/apache-spark-overview.md)használatával.  Az alábbi képen bemutatjuk, hogyan valósítható meg a lambda architektúra a Azure Cosmos DB használatával a változási csatornán keresztül:

![Azure Cosmos DB-alapú lambda-folyamat a betöltéshez és a lekérdezéshez](./media/change-feed/lambda.png)

### <a name="scenarios"></a>Alkalmazási helyzetek

Az alábbiakban néhány olyan forgatókönyvet talál, amelyekkel könnyedén megvalósíthatja a változási hírcsatornát:

* A [kiszolgáló](https://azure.microsoft.com/solutions/serverless/) nélküli webes vagy mobil alkalmazásaiban nyomon követheti az eseményeket, például az ügyfelek profiljára, beállításaira vagy helyükre vonatkozó módosításokat, és elindíthat bizonyos műveleteket, például leküldéses értesítéseket küldhet az eszközeiknek [Azure functions](change-feed-functions.md)használatával.

* Ha Azure Cosmos DBt használ egy játék létrehozásához, például a változási hírcsatorna használatával valós idejű ranglistákat hozhat létre a befejezett játékok eredményei alapján.


## <a name="working-with-change-feed"></a>A változási csatorna használata

A Change feed a következő beállításokkal használható:

* [A Change feed használata a Azure Functions](change-feed-functions.md)
* [Módosítási hírcsatorna használata a Change feed processzorral](change-feed-processor.md) 

A módosítási hírcsatorna a tárolóban lévő minden logikai partíciós kulcshoz elérhető, és az alábbi képen látható módon osztható fel egy vagy több felhasználó között párhuzamos feldolgozásra.

![Azure Cosmos DB változási csatorna elosztott feldolgozása](./media/change-feed/changefeedvisual.png)

## <a name="features-of-change-feed"></a>A változási hírcsatorna funkciói

* A módosítási hírcsatorna alapértelmezés szerint engedélyezve van az összes Azure Cosmos-fiók esetében.

* Az Azure Cosmos-adatbázishoz társított régiókban a [kiépített átviteli sebesség](request-units.md) használatával a változási csatornát ugyanúgy használhatja, mint bármely más Azure Cosmos db művelethez.

* A módosítási hírcsatorna tartalmazza a tárolóban lévő elemekhez tartozó lapkákat és frissítési műveleteket. A törlések rögzítéséhez állítson be egy "Soft-Delete" jelzőt az elemeken belül (például dokumentumok) a törlés helyett. Másik lehetőségként megadhat egy véges lejárati időszakot az elemekhez az [élettartam lehetőséggel](time-to-live.md). Tegyük fel például, hogy 24 óra, és az adott tulajdonság értékeként rögzíti a törléseket. Ezzel a megoldással a módosításokat a TTL lejárati idejénél rövidebb idő elteltével kell feldolgoznia. 

* Egy elem minden módosítása pontosan egyszer jelenik meg a változási hírcsatornában, és az ügyfeleknek az ellenőrzőpont-logikát kell kezelnie. Ha el szeretné kerülni az ellenőrzőpontok kezelésének bonyolultságát, akkor a változási hírcsatorna-feldolgozó automatikus ellenőrzőpontot és "legalább egyszer" szemantikaot biztosít. Lásd: [a Change feed használata a Change feed Processor használatával](change-feed-processor.md).

* A módosítási napló csak az adott elemek legutóbbi módosítását tartalmazza. Előfordulhat, hogy a köztes változások nem érhetők el.

* A változási csatornát a logikai partíciók minden egyes kulcsának értékén belüli módosítás sorrendjében rendezi a rendszer. Nincs garantált megrendelés a partíciós kulcs értékei között.

* A módosítások bármilyen időpontból szinkronizálhatók, azaz nincs olyan rögzített adatmegőrzési időszak, amelynél a módosítások elérhetők.

* A módosítások párhuzamosan érhetők el egy Azure Cosmos-tároló összes logikai partíciós kulcsához. Ez a funkció lehetővé teszi, hogy a nagyméretű tárolók módosításait több fogyasztó is párhuzamosan dolgozza fel.

* Az alkalmazások egyszerre több módosítási csatornát is igényelhetnek ugyanazon a tárolón. A ChangeFeedOptions. Starter használható a kezdeti kiindulási pont biztosításához. Például az adott időponthoz tartozó folytatási token megkereséséhez. A Continuationtoken argumentumot használja, ha meg van adva, a WINS a kezdő és a StartFromBeginning értékeket adja meg. A ChangeFeedOptions. kezdő időpont pontossága ~ 5 másodperc. 

## <a name="change-feed-in-apis-for-cassandra-and-mongodb"></a>Adatcsatorna módosítása a Cassandra és a MongoDB API-khoz

A MongoDB API-ban és a Cassandra API-ben predikátummal végzett lekérdezésekben a változási csatorna funkciói módosulnak. Ha többet szeretne megtudni a MongoDB API megvalósítási részleteiről, tekintse meg a [streamek módosítása Azure Cosmos db a MONGODB API-ban](mongodb-change-streams.md)című témakört.

A natív Apache Cassandra lehetővé teszi az adatváltozások rögzítését (CDC), egy olyan mechanizmust, amely az archiváláshoz adott táblákat jelöl, valamint elutasítja az írásokat az adott táblákba, amint a CDC-naplóhoz konfigurálható méretű lemez van elérve. A Cassandra Azure Cosmos DB API-ban az adatcsatorna módosítása funkció javítja a CQL-on keresztüli predikátumok lekérdezésének lehetőségét. További információ a megvalósítás részleteiről: [a Azure Cosmos db API használata a Cassandra-ben](cassandra-change-feed.md)című témakörben.

## <a name="next-steps"></a>Következő lépések

A következő cikkekben további tudnivalókat talál a hírcsatornák változásáról:

* [Beállítások a változási csatorna olvasásához](read-change-feed.md)
* [A Change feed használata a Azure Functions](change-feed-functions.md)
* [A csatorna módosítása](change-feed-processor.md)
