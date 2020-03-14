---
title: A módosítási hírcsatorna támogatása az Azure Cosmos DB használata
description: A Azure Cosmos DB módosítás-visszavételi támogatással nyomon követheti a dokumentumok, az eseményvezérelt feldolgozás, például az eseményindítók változásait, valamint naprakészen tarthatja a gyorsítótárait és az elemzési rendszereket.
author: TheovanKraay
ms.author: thvankra
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 11/25/2019
ms.reviewer: sngun
ms.custom: seodec18
ms.openlocfilehash: 898dfe7a619981b93af98effa942fdecbeb42dde
ms.sourcegitcommit: 512d4d56660f37d5d4c896b2e9666ddcdbaf0c35
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/14/2020
ms.locfileid: "79368128"
---
# <a name="change-feed-in-azure-cosmos-db---overview"></a>Azure Cosmos DB adatcsatorna módosítása – áttekintés

A változáscsatorna támogatása az Azure Cosmos DB-ben egy Azure Cosmos-tároló változásainak figyelésével működik. Ezután a módosításuk sorrendjében felsorolja a módosított dokumentumokat. A módosítások meg lesznek őrizve, feldolgozhatók aszinkron és fokozatos módon is, a kimenet pedig több fogyasztó között is elosztható a párhuzamos feldolgozás érdekében. 

Az Azure Cosmos DB kiválóan alkalmas IoT, a játékok, kiskereskedelmi, és a működési naplózási alkalmazások. Ezeket az alkalmazásokat a gyakori tervezési minta, hogy további műveletek indításához használja az adatok módosításait. További műveletek közé:

* Indítására egy értesítést vagy egy API-hívás, amikor egy cikk beszúrva vagy frissítve.
* Valós idejű streamfeldolgozás IoT vagy működési adatokkal kapcsolatos valós idejű elemzés.
* További adatmozgatás szinkronizálása egy gyorsítótárhoz vagy egy keresőmotor vagy egy adattárházat, vagy ritka elérésű tárolási adatok archiválása.

Az Azure Cosmos DB a változáscsatorna lehetővé teszi, hogy ezek a minták minden hatékony és méretezhető megoldások létrehozását a következő képen látható módon:

![Power valós idejű elemzési és számítási eseményvezérelt forgatókönyvek használatával az Azure Cosmos DB-módosítási hírcsatorna](./media/change-feed/changefeedoverview.png)

## <a name="supported-apis-and-client-sdks"></a>Támogatott API-k és az ügyfél SDK-k

Ez a funkció jelenleg a következő Azure Cosmos DB API-k és ügyféloldali SDK-k által támogatott.

| **Ügyfél-illesztőprogramok** | **Azure CLI** | **SQL API** | **Azure Cosmos DB a Cassandra-hez készült API** | **Azure Cosmos DB API-MongoDB** | **Gremlin API**|**Tábla API** |
| --- | --- | --- | --- | --- | --- | --- |
| .NET | NA | Igen | Igen | Igen | Igen | Nem |
|Java|NA|Igen|Igen|Igen|Igen|Nem|
|Python|NA|Igen|Igen|Igen|Igen|Nem|
|Csomópont/js rendszerekhez|NA|Igen|Igen|Igen|Igen|Nem|

## <a name="change-feed-and-different-operations"></a>Csatorna módosítása és a különböző műveletek

Még ma tekintse meg a csatorna módosítása az összes műveletet. A funkciót, ahol megadhatja a módosítási hírcsatornáról, adott műveletek esetében, például csak a frissítéseket, és nem Beszúrások még nem érhető el. A frissítésekhez és szűréshez hozzáadhat egy "Soft marker" elemet a változási csatornán lévő elemek feldolgozásakor. Az aktuális változási csatorna nem törli a naplókat. Az előző példához hasonló, hozzáadhat egy helyreállítható jelölő az éppen törölt elemek, például adhat hozzá egy attribútumot az elem "törölt" nevű és "true" értékűre, és Élettartamot meg az elem, hogy automatikusan törli. Elolvashatja a változási csatornát a korábbi elemek esetében (az elemnek megfelelő legutóbbi módosítást, nem tartalmazza a köztes változásokat), például az öt évvel ezelőtt hozzáadott elemeket. Ha az elem nincs törölve áttekintheti, hogy a módosítás adatcsatorna lehetőség a tároló forrása.

### <a name="sort-order-of-items-in-change-feed"></a>Rendezési sorrend a módosítási hírcsatorna-elem

A változáscsatorna elemek származnak sorrendjében azok módosításának időpontja. Ez a rendezési sorrend logikai partíciós kulcs alapján garantált.

### <a name="consistency-level"></a>Konzisztencia szintje

Miközben a változási csatornát egy végleges konzisztencia-szinten fogyasztja, előfordulhat, hogy a későbbi módosítási hírcsatorna olvasási műveletei között ismétlődő események szerepelnek (az egyik olvasási művelet utolsó eseménye a következőként jelenik meg).

### <a name="change-feed-in-multi-region-azure-cosmos-accounts"></a>Az Azure Cosmos-fiókok többrégiós csatorna módosítása

Egy több régióban az Azure Cosmos-fiók egy írási régiót átadja a feladatokat, ha módosítási hírcsatorna a manuális feladatátvételt művelet között fog működni, és összefüggő lesz.

### <a name="change-feed-and-time-to-live-ttl"></a>Csatorna módosítása és élettartamot (TTL)

Ha egy élettartam (idő élettartama) tulajdonsága egy elemet a -1, módosítási hírcsatorna örökre megmaradnak. Az adatok nem törlődnek, ha a csatorna módosítása megmarad.  

### <a name="change-feed-and-_etag-_lsn-or-_ts"></a>Csatorna módosítása és _etag, _lsn vagy _ts

A belső _etag formátuma, és nem megtétele függőségi rajta, mivel bármikor módosíthatja. _ts módosítását vagy a létrehozás időbélyegzője. Időrendi összehasonlító _ts is használhat. _lsn egy olyan batch-azonosító, amely csak a változási hírcsatornához lett hozzáadva; Ez a tranzakció AZONOSÍTÓját jelöli. Előfordulhat, hogy hány elemet azonos _lsn. A FeedResponse ETag eltér a _etag akkor jelenik meg a cikket. _etag belső azonosítója, és ez használatos egyidejűségi vezérlő a verziójának használatát a cikk arról tájékoztatja, mivel az ETag használt műveleti sorrend a hírcsatorna.

## <a name="change-feed-use-cases-and-scenarios"></a>Csatorna használati esetekre és forgatókönyvekre módosítása

Módosítási hírcsatorna lehetővé teszi, hogy az írási műveletek nagy mennyiségű nagy adatkészletek feldolgozása hatékony. Módosítási hírcsatorna is kínál egy teljes adatkészleten azonosításához, mi változott a lekérdezés helyett.

### <a name="use-cases"></a>Használati esetek

Például a csatorna módosítása a következő feladatokat végezheti hatékonyan:

* Frissítse a gyorsítótárat, a keresési index frissítése vagy frissítése egy adattárházat az Azure Cosmos DB-ben tárolt adatokat.

* Egy alkalmazás szintű adatréteget és archiválást valósíthat meg, például a "gyakori adatok" tárolása Azure Cosmos DB és a "hideg adatok" más tárolási rendszerekre, például az [Azure Blob Storage](../storage/common/storage-introduction.md).

* Hajtsa végre a nulla-ideje áttelepítések egy másik Azure-Cosmos-fiók vagy egy másik Azure-Cosmos-tároló egy másik logikai partíciós kulccsal.

* [Lambda-architektúra](https://blogs.technet.microsoft.com/msuspartner/2016/01/27/azure-partner-community-big-data-advanced-analytics-and-lambda-architecture/) implementálása Azure Cosmos db használatával, ahol a Azure Cosmos db a valós idejű, a kötegelt és a lekérdezési rétegeket is támogatja, ami lehetővé teszi a lambda architektúra alacsony TCO-vel való használatát.

* Az adatok az eszközökről, érzékelőkről, infrastruktúrából és alkalmazásokból érkeznek és tárolhatók, és valós időben dolgozzák fel ezeket az eseményeket, például a [Spark](../hdinsight/spark/apache-spark-overview.md)használatával.  A következő kép bemutatja, hogyan valósítható meg az Azure Cosmos DB-n keresztül módosítási hírcsatorna a lambda architektúra:

![Az Azure Cosmos DB-alapú lambda folyamat fenntartásával és a lekérdezések](./media/change-feed/lambda.png)

### <a name="scenarios"></a>Forgatókönyvek

Az alábbiakban a forgatókönyvek is egyszerűen implementálni a módosítás adatcsatornával:

* A [kiszolgáló](https://azure.microsoft.com/solutions/serverless/) nélküli webes vagy mobil alkalmazásaiban nyomon követheti az eseményeket, például az ügyfelek profiljára, beállításaira vagy helyükre vonatkozó módosításokat, és elindíthat bizonyos műveleteket, például leküldéses értesítéseket küldhet az eszközeiknek [Azure functions](change-feed-functions.md)használatával.

* Készítsen játékot használata Azure Cosmos DB, akkor is, például használata módosításcsatornáját befejezett játékok a pontszámok alapján a valós idejű ranglisták megvalósításához.


## <a name="working-with-change-feed"></a>Csatorna módosítása használata

Az alábbi beállítások segítségével módosítási hírcsatorna használhatja:

* [A Change feed használata a Azure Functions](change-feed-functions.md)
* [Módosítási hírcsatorna használata a Change feed processzorral](change-feed-processor.md) 

Módosítási hírcsatorna érhető el a tárolóban lévő minden egyes logikai partíciós kulcs, és hogy szét lehetnek osztva egy vagy több ügyfél párhuzamos feldolgozásra az alábbi képen látható módon.

![Elosztott feldolgozásához Azure Cosmos DB – csatorna módosítása](./media/change-feed/changefeedvisual.png)

## <a name="features-of-change-feed"></a>Módosítási hírcsatorna funkciói

* Minden Azure-Cosmos-fiókok számára alapértelmezés szerint engedélyezve van a módosítási hírcsatorna.

* Az Azure Cosmos-adatbázishoz társított régiókban a [kiépített átviteli sebesség](request-units.md) használatával a változási csatornát ugyanúgy használhatja, mint bármely más Azure Cosmos db művelethez.

* A módosítási hírcsatorna beszúrások és a tárolóban lévő elemek frissítési műveleteket tartalmaz. Rögzítheti a törlések az elemek (például dokumentumok) belül "helyreállítható törlés" jelölő beállításával törlése helyett. Másik lehetőségként megadhat egy véges lejárati időszakot az elemekhez az [élettartam lehetőséggel](time-to-live.md). Például 24 óra és -felhasználási rögzítheti a tulajdonság értékét törli. Ezzel a megoldással hogy feldolgozza a módosításokat a TTL lejárata időszaknál rövidebb időközt belül. 

* Minden módosítás elem pontosan egyszer jelenik meg a változáscsatorna, és az ügyfelek az ellenőrzőpontok használata logikát kell kezelni. Ha el szeretné kerülni az ellenőrzőpontok kezelésének bonyolultságát, akkor a változási hírcsatorna-feldolgozó automatikus ellenőrzőpontot és "legalább egyszer" szemantikaot biztosít. Lásd: [a Change feed használata a Change feed Processor használatával](change-feed-processor.md).

* Egy adott elem csak a legutóbbi módosítás a módosítási napló tartalmazza. Közbenső változások nem érhető el.

* A módosítási hírcsatorna sorrendje módosításának belül minden egyes logikai partíciókulcs-értékkel van rendezve. Nincs garantált rendelés partíciókulcs között.

* Változások a bármely-időponthoz, amely nem rögzített adatok megőrzési időszak, amelyekhez le változások szinkronizálhatók.

* Változások az Azure Cosmos-tároló az összes logikai partíciókulcsok párhuzamosan érhetők el. Ez a funkció lehetővé teszi, hogy a változásokat a nagy méretű tárolók több fogyasztó a párhuzamos feldolgozásra.

* Az alkalmazások egyszerre több módosítási csatornát is igényelhetnek ugyanazon a tárolón. ChangeFeedOptions.StartTime használható egy kezdeti kiindulási pontot biztosít. Ha például a folytatási kód egy megadott idő értékeknek megfelelő található. A continuationtoken argumentumot használja, ha meg van adva, a WINS-keresztül a StartTime és StartFromBeginning értékeket. A pontosság ChangeFeedOptions.StartTime ~ 5 másodperc. 

## <a name="change-feed-in-apis-for-cassandra-and-mongodb"></a>Adatcsatorna módosítása a Cassandra és a MongoDB API-khoz

A MongoDB API-ban és a Cassandra API-ben predikátummal végzett lekérdezésekben a változási csatorna funkciói módosulnak. Ha többet szeretne megtudni a MongoDB API megvalósítási részleteiről, tekintse meg a [streamek módosítása Azure Cosmos db a MONGODB API-ban](mongodb-change-streams.md)című témakört.

A natív Apache Cassandra lehetővé teszi az adatváltozások rögzítését (CDC), egy olyan mechanizmust, amely az archiváláshoz adott táblákat jelöl, valamint elutasítja az írásokat az adott táblákba, amint a CDC-naplóhoz konfigurálható méretű lemez van elérve. A Cassandra Azure Cosmos DB API-ban az adatcsatorna módosítása funkció javítja a CQL-on keresztüli predikátumok lekérdezésének lehetőségét. További információ a megvalósítás részleteiről: [a Azure Cosmos db API használata a Cassandra-ben](cassandra-change-feed.md)című témakörben.

## <a name="next-steps"></a>Következő lépések

Folytassa további információ a változáscsatorna az alábbi cikkeket:

* [Beállítások a változási csatorna olvasásához](read-change-feed.md)
* [A Change feed használata a Azure Functions](change-feed-functions.md)
* [A csatorna módosítása](change-feed-processor.md)
