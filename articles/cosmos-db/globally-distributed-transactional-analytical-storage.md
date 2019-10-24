---
title: Globálisan elosztott tranzakciós és analitikai tároló az Azure Cosmos-tárolók számára
description: Ismerje meg a tranzakciós és analitikai tárterületet, valamint az Azure Cosmos-tárolók konfigurációs beállításait.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/30/2019
ms.reviewer: sngun
ms.openlocfilehash: abf222b7a6d6e8fd053fa83c066d2b7850f575ab
ms.sourcegitcommit: 8074f482fcd1f61442b3b8101f153adb52cf35c9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/22/2019
ms.locfileid: "72756906"
---
# <a name="globally-distributed-transactional-and-analytical-storage-for-azure-cosmos-containers"></a>Globálisan elosztott tranzakciós és analitikai tároló az Azure Cosmos-tárolók számára

Az Azure Cosmos-tárolót a két tárolóeszköz, a tranzakciós tárolók és egy frissíthető analitikai tároló motorja belsőleg támogatja. A Storage-motorokat a rendszer naplózza és írásra optimalizálta a gyorsabb frissítésekhez. Azonban mindegyik kódolása másképp történik:

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
|Az adatelérést biztosító API-k  |   SQL, MongoDB, Cassandra, Gremlin, Tables és Etcd.       | Apache Spark         |
|Megőrzés (élettartam vagy TTL)   |  Házirend-alapú, az Azure Cosmos-tárolón konfigurált `DefaultTimeToLive` tulajdonság használatával.       |   Házirend-alapú, az Azure Cosmos-tárolón konfigurált `ColumnStoreTimeToLive` tulajdonság használatával.      |
|Díj GB-onként    |   0,25/GB      |  0,02/GB       |
|Tárolási tranzakciók díja    | A kiépített átviteli sebesség óradíja $0,008/100 RU/s.        |  A fogyasztáson alapuló adatátviteli sebességet az $0,05-es 10 000-es írási tranzakciók és $0,004 for 10 000-olvasási tranzakciók esetében számítjuk fel.       |

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

### <a name="on-demand-snapshots-and-time-travel-analytics"></a>Igény szerinti Pillanatképek és idő-utazási elemzés

Az Azure Cosmos-tárolók analitikai tárolójában tárolt adatairól bármikor készíthet pillanatképeket, ha meghívja a `CreateSnapshot (name, timestamp)` parancsot a tárolón. A pillanatképek neve "Bookmarks" a tárolóban korábban végrehajtott frissítések előzményeiben.

![Igény szerinti Pillanatképek és idő-utazási elemzés](./media/globally-distributed-transactional-analytical-storage/ondemand-analytical-data-snapshots.png)

A pillanatkép létrehozásának időpontjában a név mellett megadhatja azt az időbélyeget, amely meghatározza a tároló állapotát a frissítések előzményeiben. Ezután betöltheti a pillanatkép-adatok Sparkba való betöltését, és végrehajthatja a lekérdezéseket.

Jelenleg csak az igény szerinti pillanatképek készíthetők a tárolóban, a pillanatképek automatikus elkészítése az ütemezés alapján, vagy az egyéni szabályzatok még nem támogatottak.

### <a name="configure-and-tier-data-between-transactional-and-analytical-storage-independently"></a>A tranzakciós és az analitikus tárolás egymástól független beállítása és a rétegek közötti adatkezelés

A forgatókönyvtől függően önállóan engedélyezheti vagy letilthatja a két tárolási motort. Az egyes forgatókönyvek konfigurációi a következők:

|Alkalmazási helyzet |Tranzakciós tárolási beállítás  |Analitikai tárolási beállítás |
|---------|---------|---------|
|Analitikai számítási feladatok kizárólagos futtatása (végtelen megőrzéssel) |  DefaultTimeToLive = 0       |  ColumnStoreTimeToLive =-1       |
|Kizárólag tranzakciós munkaterhelések futtatása (végtelen megőrzéssel)  |   DefaultTimeToLive =-1      |  ColumnStoreTimeToLive = 0       |
|Tranzakciós és analitikai számítási feladatok futtatása (végtelen megőrzéssel)   |   DefaultTimeToLive =-1      | ColumnStoreTimeToLive =-1        |
|Mind a tranzakciós, mind az analitikai számítási feladatok futtatása (eltérő megőrzési időközökkel, más néven tárolási rétegek)  |  DefaultTimeToLive = <Value1>       |     ColumnStoreTimeToLive = <Value2>    |

1. **A tároló konfigurálása kizárólag analitikai számítási feladatokhoz (végtelen megőrzéssel)**

   Az Azure Cosmos-tárolót kizárólag analitikai számítási feladatokhoz állíthatja be. Ennek a konfigurációnak van olyan előnye, hogy nem kell fizetnie a tranzakciós tárterületért. Ha a cél az, hogy csak analitikus számítási feladatokhoz használja a tárolót, letilthatja a tranzakciós tárolót úgy, hogy a Cosmos tárolóban a `DefaultTimeToLive` 0-ra állítja, és az analitikai tárolót a végtelen megőrzés beállításával engedélyezheti `ColumnStoreTimeToLive` – 1 értékre.

   ![Elemzési számítási feladatok végtelen megőrzéssel](./media/globally-distributed-transactional-analytical-storage/analytical-workload-configuration.png)

1. **A tároló konfigurálása kizárólag tranzakciós munkaterhelésekhez (végtelen megőrzéssel)**

   Az Azure Cosmos-tárolót kizárólag tranzakciós munkaterhelések esetében konfigurálhatja. Az analitikai tároló letiltásához állítsa be a `ColumnStoreTimeToLive` 0-ra értéket a tárolóra, és az analitikai tárolót a végtelen megőrzés beállítással engedélyezheti a `DefaultTimeToLive` – 1 értékre való állításával.

   ![Tranzakciós munkaterhelések végtelen megőrzéssel](./media/globally-distributed-transactional-analytical-storage/transactional-workload-configuration.png)

1. **A tároló konfigurálása tranzakciós és analitikai számítási feladatokhoz (végtelen megőrzéssel)**

   Az Azure Cosmos-tárolót mind a tranzakciós, mind az analitikus számítási feladatokhoz beállíthatja, a teljes teljesítmény-elkülönítéssel együtt. Engedélyezheti az analitikai tárterületet, ha a `ColumnStoreTimeToLive` az-1 értékre állítja, és lehetővé teszi a tranzakciós tárolást a végtelen megőrzéssel a `DefaultTimeToLive ` – 1 érték beállításával.

   ![Tranzakciós és analitikai számítási feladatok végtelen megőrzéssel](./media/globally-distributed-transactional-analytical-storage/analytical-transactional-configuration-infinite-retention.png)

1. **A tároló konfigurálása tranzakciós és analitikai számítási feladatokhoz tárolási réteggel**

   Az Azure Cosmos-tárolót mind a tranzakciós, mind az analitikus számítási feladatokhoz beállíthatja, a különböző adatmegőrzési intervallumok közötti teljes teljesítmény-elkülönítéssel. Azure Cosmos DB fogja kényszeríteni, hogy az analitikai tárterületet mindig hosszabb ideig őrzi meg a tranzakciós tárterületnél.

   A tranzakciós tárterületet a végtelen megőrzés beállításával engedélyezheti, ha a `DefaultTimeToLive` értéket < 1. érték > és az analitikai tároló engedélyezését `ColumnStoreTimeToLive` < Value 2 > értékre állítva. A Azure Cosmos DB kikényszeríti, hogy a < Value 2 > mindig nagyobb legyen, mint < 1 > érték.

   ![Tranzakciós és analitikai számítási feladatok tárolási szinttel](./media/globally-distributed-transactional-analytical-storage/analytical-transactional-configuration-specified-retention.png)

## <a name="next-steps"></a>Következő lépések

* [Élettartam Azure Cosmos DB](time-to-live.md)
