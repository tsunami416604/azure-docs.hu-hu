---
title: Globálisan elosztott tranzakciós és analitikai (privát előzetes verzió) tároló az Azure Cosmos-tárolók számára
description: Ismerje meg a tranzakciós és analitikai tárterületet, valamint az Azure Cosmos-tárolók konfigurációs beállításait.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/30/2019
ms.reviewer: sngun
ms.openlocfilehash: 18cf43ba137c92fc00d5f8e82e13501d03b4b6a3
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/25/2019
ms.locfileid: "75445403"
---
# <a name="globally-distributed-transactional-and-analytical-storage-for-azure-cosmos-containers"></a>Globálisan elosztott tranzakciós és analitikai tároló az Azure Cosmos-tárolók számára

Az Azure Cosmos-tárolót a két Storage-motor és egy frissíthető analitikai tároló motorja (privát előzetes verzió) belsőleg támogatja. A Storage-motorokat a rendszer naplózza és írásra optimalizálta a gyorsabb frissítésekhez. Azonban mindegyik kódolása másképp történik:

* **Tranzakciós tárolási motor** – a rendszer a gyors tranzakciós olvasások és lekérdezések során sor-orientált formátumban kódolja.

* **Analitikai tárolási motor** – a rendszer oszlopos formátumban kódolja a gyors analitikai lekérdezések és vizsgálatok céljából.

![Storage-motorok és Azure Cosmos DB API-hozzárendelés](./media/globally-distributed-transactional-analytical-storage/storage-engines-api-mapping.png)

A tranzakciós tárolási motort a helyi SSD-k végzik, az analitikai tárterületet pedig egy nem fürtözött SSD-tárolón tároljuk. Az alábbi táblázat a tranzakciós és az analitikai tároló közötti jelentős különbségeket rögzíti.


|Szolgáltatás  |Tranzakciós tár  |Analitikai tár |
|---------|---------|---------|
|Maximális tárterület egy Azure Cosmos-tárolóban |   Korlátlan      |    Korlátlan     |
|Maximális tárterület logikai partíciós kulcs alapján   |   10 GB      |   Korlátlan      |
|Tárolási kódolás  |   Sor-orientált, belső formátum használatával.   |   Oszlop-orientált, Apache Parquet formátumot használva. |
|Tárolási helység |   Helyi vagy fürtön belüli SSD-k által támogatott replikált tároló. |  A replikált tárolót az olcsó távoli/off-cluster SSD-k támogatták.       |
|Tartósság  |    99,99999 (7-9 s)     |  99,99999 (7-9 s)       |
|Az adatelérést biztosító API-k  |   SQL, MongoDB, Cassandra, Gremlin, Tables és etcd.       | Apache Spark         |
|Megőrzés (élettartam vagy TTL)   |  Házirend-alapú, az Azure Cosmos-tárolón konfigurált `DefaultTimeToLive` tulajdonság használatával.       |   Házirend-alapú, az Azure Cosmos-tárolón konfigurált `ColumnStoreTimeToLive` tulajdonság használatával.      |
|Díj GB-onként    |   Tekintse meg a [díjszabási oldalt](https://azure.microsoft.com/pricing/details/cosmos-db/)     |   Tekintse meg a [díjszabási oldalt](https://azure.microsoft.com/pricing/details/cosmos-db/)        |
|Tárolási tranzakciók díja    |  Tekintse meg a [díjszabási oldalt](https://azure.microsoft.com/pricing/details/cosmos-db/)         |   Tekintse meg a [díjszabási oldalt](https://azure.microsoft.com/pricing/details/cosmos-db/)        |

## <a name="benefits-of-transactional-and-analytical-storage"></a>A tranzakciós és analitikai tárolás előnyei

### <a name="no-etl-operations"></a>Nincs ETL-művelet

A hagyományos analitikai folyamatok több fázissal is összetettek, melyek mindegyike kinyert átalakítási-betöltési (ETL) műveleteket igényel a számítási és tárolási szintekhez. Összetett adatfeldolgozási architektúrákat eredményez. Ez nagy mennyiségű költséget jelent a tárolás és a számítás több szakasza esetében, valamint a nagy késést a különböző tárolási és számítási fázisok között továbbított nagy mennyiségű adatok miatt.  

A Azure Cosmos DB nem végezhető el az ETL-műveletek végrehajtása. Mindegyik Azure Cosmos-tárolót mind a tranzakciós, mind az analitikai tárolási motor támogatja, és a tranzakciós és az analitikai tárolási motor közötti adatátvitel az adatbázismotor és hálózati ugrások nélkül történik. Az eredményül kapott késés és a díjak jelentősen alacsonyabbak a hagyományos analitikai megoldásokkal szemben. Egyetlen globálisan elosztott tárolási rendszer is elérhető mind a tranzakciós, mind az analitikus számítási feladatokhoz.  

### <a name="store-multiple-versions-update-and-query-the-analytical-storage"></a>Az analitikai tár több verziójának, frissítésének és lekérdezésének tárolása

Az analitikai tároló teljes mértékben frissíthető, és tartalmazza az Azure Cosmos-tárolóban történt összes tranzakciós frissítés teljes verziójának előzményeit.

A tranzakciós tárolóban végzett minden frissítés 30 másodpercen belül látható az analitikai tárolóban. 

### <a name="globally-distributed-multi-master-analytical-storage"></a>Globálisan elosztott, több főkiszolgálós analitikai tár

Ha az Azure Cosmos-fiókja egyetlen régióra van korlátozva, a tárolókban tárolt (tranzakciós és analitikai tárolási) adattárolók egyetlen régióra is kiterjednek. Másfelől, ha az Azure Cosmos-fiókot globálisan terjesztik, a tárolókban tárolt adatforgalom is globálisan terjeszthető.

A több írási régióval konfigurált Azure Cosmos-fiókok esetében a tárolóba (mind a tranzakciós, mind az analitikai tárolóba) a rendszer mindig a helyi régióban hajtja végre a műveletet, így azok gyorsak.

Mind az önálló, mind a többrégiós Azure Cosmos-fiókok esetében függetlenül attól, hogy az egyetlen írási régió (egyetlen főkiszolgáló) vagy több írási régió (más néven több főkiszolgáló), mind a tranzakciós, mind az analitikus olvasási és lekérdezési műveleteket helyileg hajtja végre az adott régióban.

### <a name="performance-isolation-between-transactional-and-analytical-workloads"></a>Teljesítmény-elkülönítés tranzakciós és analitikai számítási feladatok között

Egy adott régióban a tranzakciós munkaterhelések a tároló tranzakciós/soros tárolóján működnek. Másfelől az analitikai számítási feladatok a tároló analitikus/oszlopos tárolóján működnek. A két tároló motor egymástól függetlenül működik, és szigorú teljesítménybeli elkülönítést biztosít a munkaterhelések között.

A tranzakciós munkaterhelések használják a kiépített átviteli sebességet (RUs). A tranzakciós munkaterhelésekkel ellentétben az elemzési számítási feladatok átviteli sebessége a tényleges felhasználáson alapul. Az analitikus számítási feladatok igény szerint használják az erőforrásokat.

## <a name="next-steps"></a>Következő lépések

* [Élettartam Azure Cosmos DB](time-to-live.md)
