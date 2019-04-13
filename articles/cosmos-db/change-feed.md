---
title: A módosítási hírcsatorna támogatása az Azure Cosmos DB használata
description: Használja az Azure Cosmos DB módosítási hírcsatorna támogatása a dokumentumok nyomon követésére, és végezze el a feldolgozási eseményalapú eseményindítókat például és a gyorsítótárak és elemzési rendszerek naprakészen tartása.
author: rimman
ms.author: rimman
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 11/06/2018
ms.reviewer: sngun
ms.custom: seodec18
ms.openlocfilehash: 85a1dad9feb15550cf27cf032802af5055fdf155
ms.sourcegitcommit: 1c2cf60ff7da5e1e01952ed18ea9a85ba333774c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/12/2019
ms.locfileid: "59525636"
---
# <a name="change-feed-in-azure-cosmos-db---overview"></a>Az Azure Cosmos DB – csatorna módosítása – áttekintés

Módosítási hírcsatorna támogatása az Azure Cosmos DB működését úgy egy Azure Cosmos DB-tárolók nem változott. Majd megjeleníti a dokumentumok a sorrendben, amelyben a módosítás módosult a listán. A módosítások meg lesznek őrizve, feldolgozhatók aszinkron és fokozatos módon is, a kimenet pedig több fogyasztó között is elosztható a párhuzamos feldolgozáshoz. 

Az Azure Cosmos DB kiválóan alkalmas IoT, a játékok, kiskereskedelmi, és a működési naplózási alkalmazások. Ezeket az alkalmazásokat a gyakori tervezési minta, hogy további műveletek indításához használja az adatok módosításait. További műveletek közé:

* Indítására egy értesítést vagy egy API-hívás, amikor egy cikk beszúrva vagy frissítve.
* Valós idejű streamfeldolgozás IoT vagy működési adatokkal kapcsolatos valós idejű elemzés.
* További adatmozgatás szinkronizálása egy gyorsítótárhoz vagy egy keresőmotor vagy egy adattárházat, vagy ritka elérésű tárolási adatok archiválása.

Az Azure Cosmos DB a változáscsatorna lehetővé teszi, hogy ezek a minták minden hatékony és méretezhető megoldások létrehozását a következő képen látható módon:

![Power valós idejű elemzési és számítási eseményvezérelt forgatókönyvek használatával az Azure Cosmos DB-módosítási hírcsatorna](./media/change-feed/changefeedoverview.png)

## <a name="supported-apis-and-client-sdks"></a>Támogatott API-k és az ügyfél SDK-k

Ez a funkció jelenleg a következő Azure Cosmos DB API-k és ügyféloldali SDK-k által támogatott.

| **Ügyfél-illesztőprogramok** | **Azure CLI** | **SQL API-HOZ** | **Cassandra API** | **Az Azure Cosmos DB MongoDB API-jaival** | **Gremlin API**|**Tábla API** |
| --- | --- | --- | --- | --- | --- | --- |
| .NET | NA | Igen | Nem | Nem | Igen | Nem |
|Java|NA|Igen|Nem|Nem|Igen|Nem|
|Python|NA|Igen|Nem|Nem|Igen|Nem|
|Csomópont/js rendszerekhez|NA|Igen|Nem|Nem|Igen|Nem|

## <a name="change-feed-and-different-operations"></a>Csatorna módosítása és a különböző műveletek

Még ma tekintse meg a csatorna módosítása az összes műveletet. A funkciót, ahol megadhatja a módosítási hírcsatornáról, adott műveletek esetében, például csak a frissítéseket, és nem Beszúrások még nem érhető el. Egy "helyreállítható jelölő" a cikk a frissítések és az alapján, vagy a módosítási hírcsatorna elemeinek feldolgozásakor szűrőt is hozzáadhat. Jelenleg a módosítási hírcsatorna jelentkezzen nem törli. Az előző példához hasonló, hozzáadhat egy helyreállítható jelölő az éppen törölt elemek, például adhat hozzá egy attribútumot az elem "törölt" nevű és "true" értékűre, és Élettartamot meg az elem, hogy automatikusan törli. A módosítási hírcsatorna történelmi elemek, például öt évvel ezelőtt hozzáadott cikkeket olvashat. Ha az elem nincs törölve áttekintheti, hogy a módosítás adatcsatorna lehetőség a tároló forrása.

### <a name="sort-order-of-items-in-change-feed"></a>Rendezési sorrend a módosítási hírcsatorna-elem

A változáscsatorna elemek származnak sorrendjében azok módosításának időpontja. A rendezési sorrend garantáltan logikai partíciókulcson.

### <a name="change-feed-in-multi-region-azure-cosmos-accounts"></a>Az Azure Cosmos-fiókok többrégiós csatorna módosítása

Egy több régióban az Azure Cosmos-fiók egy írási régiót átadja a feladatokat, ha módosítási hírcsatorna a manuális feladatátvételt művelet között fog működni, és összefüggő lesz.

### <a name="change-feed-and-time-to-live-ttl"></a>Csatorna módosítása és élettartamot (TTL)

Ha egy élettartam (idő élettartama) tulajdonsága egy elemet a -1, módosítási hírcsatorna örökre megmaradnak. Az adatok nem törlődnek, ha a csatorna módosítása megmarad.  

### <a name="change-feed-and-etag-lsn-or-ts"></a>Csatorna módosítása és _etag, _lsn vagy _ts

A belső _etag formátuma, és nem megtétele függőségi rajta, mivel bármikor módosíthatja. _ts módosítását vagy a létrehozás időbélyegzője. Időrendi összehasonlító _ts is használhat. _lsn egy hozzáadott a változáscsatorna csak; Kötegazonosító Ez azt jelenti, hogy a tranzakció azonosítója. Előfordulhat, hogy hány elemet azonos _lsn. A FeedResponse ETag eltér a _etag akkor jelenik meg a cikket. _etag belső azonosítója, és ez használatos egyidejűségi vezérlő a verziójának használatát a cikk arról tájékoztatja, mivel az ETag használt műveleti sorrend a hírcsatorna.

## <a name="change-feed-use-cases-and-scenarios"></a>Csatorna használati esetekre és forgatókönyvekre módosítása

Módosítási hírcsatorna lehetővé teszi, hogy az írási műveletek nagy mennyiségű nagy adatkészletek feldolgozása hatékony. Módosítási hírcsatorna is kínál egy teljes adatkészleten azonosításához, mi változott a lekérdezés helyett.

### <a name="use-cases"></a>Használati esetek

Például a csatorna módosítása a következő feladatokat végezheti hatékonyan:

* Frissítse a gyorsítótárat, a keresési index frissítése vagy frissítése egy adattárházat az Azure Cosmos DB-ben tárolt adatokat.

* Egy alkalmazás-szintű adatok rétegezési és archiválási megvalósítását, például "gyors elérésű adatok" tárolására az Azure Cosmos DB és más tárolási rendszerek "ritkán használt adatok" például elavulnak [Azure Blob Storage](../storage/common/storage-introduction.md).

* Hajtsa végre a nulla-ideje áttelepítések egy másik Azure-Cosmos-fiók vagy egy másik Azure-Cosmos-tároló egy másik logikai partíciós kulccsal.

* Alkalmazzon [lambda architektúra](https://blogs.technet.microsoft.com/msuspartner/2016/01/27/azure-partner-community-big-data-advanced-analytics-and-lambda-architecture/) az Azure Cosmos DB, ahol az Azure Cosmos DB támogatja a valós idejű, batch és rétegek szolgáló lekérdezés, ezáltal lehetővé téve a lambda architektúra használatával alacsony teljes bekerülési Költséget.

* Fogadni és tárolja az eseményadatokat az eszközök, érzékelők, infrastruktúra és alkalmazások, és ezek az események valós időben például feldolgozni használatával [Spark](../hdinsight/spark/apache-spark-overview.md).  A következő kép bemutatja, hogyan valósítható meg az Azure Cosmos DB-n keresztül módosítási hírcsatorna a lambda architektúra:

![Az Azure Cosmos DB-alapú lambda folyamat fenntartásával és a lekérdezések](./media/change-feed/lambda.png)

### <a name="scenarios"></a>Forgatókönyvek

Az alábbiakban a forgatókönyvek is egyszerűen implementálni a módosítás adatcsatornával:

* Belül a [kiszolgáló nélküli](https://azure.microsoft.com/solutions/serverless/) webes és mobilalkalmazásokat, nyomon követheti az eseményeket, mint például az összes módosítást a felhasználói profilt, beállítások vagy azok helyétől, és aktiválja a bizonyos műveletek, például az eszközök leküldéses értesítések küldése használatával [Azure Functions](change-feed-functions.md).

* Készítsen játékot használata Azure Cosmos DB, akkor is, például használata módosításcsatornáját befejezett játékok a pontszámok alapján a valós idejű ranglisták megvalósításához.


## <a name="working-with-change-feed"></a>Csatorna módosítása használata

Az alábbi beállítások segítségével módosítási hírcsatorna használhatja:

* [Az Azure Functions használatával módosítási hírcsatorna](change-feed-functions.md)
* [A változáscsatorna feldolgozói könyvtárával használatával módosítási hírcsatorna](change-feed-processor.md) 

Módosítási hírcsatorna érhető el a tárolóban lévő minden egyes logikai partíciós kulcs, és hogy szét lehetnek osztva egy vagy több ügyfél párhuzamos feldolgozásra az alábbi képen látható módon.

![Elosztott feldolgozásához Azure Cosmos DB – csatorna módosítása](./media/change-feed/changefeedvisual.png)

## <a name="features-of-change-feed"></a>Módosítási hírcsatorna funkciói

* Minden Azure-Cosmos-fiókok számára alapértelmezés szerint engedélyezve van a módosítási hírcsatorna.

* Használhatja a [kiosztott átviteli sebesség](request-units.md) olvasni a változáscsatorna, ugyanúgy, mint bármely más Azure Cosmos DB műveletet, az Azure Cosmos database társított régiók egyikében.

* A módosítási hírcsatorna beszúrások és a tárolóban lévő elemek frissítési műveleteket tartalmaz. Rögzítheti a törlések az elemek (például dokumentumok) belül "helyreállítható törlés" jelölő beállításával törlése helyett. Másik lehetőségként beállíthatja egy véges lejárati idejét az elemek a [TTL képesség](time-to-live.md). Például 24 óra és -felhasználási rögzítheti a tulajdonság értékét törli. Ezzel a megoldással hogy feldolgozza a módosításokat a TTL lejárata időszaknál rövidebb időközt belül. 

* Minden módosítás elem pontosan egyszer jelenik meg a változáscsatorna, és az ügyfelek az ellenőrzőpontok használata logikát kell kezelni. Ha szeretné elkerülni az ellenőrzőpontok kezelésének bonyolultságát, a változáscsatorna feldolgozói könyvtárával biztosít automatikus ellenőrzőpont-készítés és a "legalább egyszeri" szemantika. Lásd: [használatával módosítási hírcsatorna a változáscsatorna feldolgozói kódtára](change-feed-processor.md).

* Egy adott elem csak a legutóbbi módosítás a módosítási napló tartalmazza. Közbenső változások nem érhető el.

* A módosítási hírcsatorna sorrendje módosításának belül minden egyes logikai partíciókulcs-értékkel van rendezve. Nincs garantált rendelés partíciókulcs között.

* Változások a bármely-időponthoz, amely nem rögzített adatok megőrzési időszak, amelyekhez le változások szinkronizálhatók.

* Változások az Azure Cosmos-tároló az összes logikai partíciókulcsok párhuzamosan érhetők el. Ez a funkció lehetővé teszi, hogy a változásokat a nagy méretű tárolók több fogyasztó a párhuzamos feldolgozásra.

* Alkalmazások egyszerre több módosítási hírcsatorna ugyanazt a tárolót a kérhetnek. ChangeFeedOptions.StartTime használható egy kezdeti kiindulási pontot biztosít. Ha például a folytatási kód egy megadott idő értékeknek megfelelő található. A continuationtoken argumentumot használja, ha meg van adva, a WINS-keresztül a StartTime és StartFromBeginning értékeket. A pontosság ChangeFeedOptions.StartTime ~ 5 másodperc. 

## <a name="next-steps"></a>További lépések

Folytassa további információ a változáscsatorna az alábbi cikkeket:

* [Olvassa el a beállításokat a módosítási hírcsatornáról](read-change-feed.md)
* [Az Azure Functions használatával módosítási hírcsatorna](change-feed-functions.md)
* [Feldolgozói kódtára használatával módosítási hírcsatorna](change-feed-processor.md)
