---
title: A fürt teljesítményének figyelése – Azure HDInsight
description: Apache Hadoop-fürtök állapotának és teljesítményének figyelése az Azure HDInsight-ben.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 11/27/2019
ms.openlocfilehash: 72006f907a1c1641308c8ee43e7a405765410789
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/09/2020
ms.locfileid: "75770883"
---
# <a name="monitor-cluster-performance-in-azure-hdinsight"></a>A fürt teljesítményének figyelése az Azure HDInsight

A HDInsight-fürtök állapotának és teljesítményének figyelése elengedhetetlen az optimális teljesítmény és erőforrás-kihasználtság fenntartása érdekében. A figyelés a fürt konfigurációs hibái és a felhasználói kódok problémáinak észlelésében és kezelésében is segítséget nyújt.

A következő szakaszok azt ismertetik, hogyan lehet figyelni és optimalizálni a fürtök terhelését, Apache Hadoop a fonal-várólistákat és észlelni a tárolási sávszélesség-szabályozással kapcsolatos problémákat.

## <a name="monitor-cluster-load"></a>Fürt terhelésének figyelése

A Hadoop-fürtök a legoptimálisabb teljesítményt biztosíthatják, ha a fürt terhelése egyenletesen oszlik el az összes csomóponton. Ez lehetővé teszi a feldolgozási feladatok futtatását anélkül, hogy a RAM, a CPU vagy a lemez erőforrásai korlátozzák az egyes csomópontokon.

Ha magas szintű áttekintést szeretne kapni a fürt csomópontjairól és betöltéséről, jelentkezzen be a [Ambari webes felhasználói felületére](hdinsight-hadoop-manage-ambari.md), majd válassza a **gazdagépek** lapot. A gazdagépek teljes tartományneveik szerepelnek a felsorolásban. Az egyes gazdagépek működési állapotát színes állapot kijelzője mutatja:

| Szín | Leírás |
| --- | --- |
| Piros | A gazdagépen legalább egy fő összetevő nem működik. Vigye az egérmutatót egy olyan elemleírás megjelenítéséhez, amely felsorolja az érintett összetevőket. |
| Orange | A gazdagépen legalább egy másodlagos összetevő nem működik. Vigye az egérmutatót egy olyan elemleírás megjelenítéséhez, amely felsorolja az érintett összetevőket. |
| Sárga | A Ambari-kiszolgáló több mint 3 percen belül nem kapott szívverést a gazdagépről. |
| Zöld | Normál Futási állapot. |
 
Emellett az egyes gazdagépek magok számát és a RAM mennyiségét, valamint a lemezek kihasználtságát és a terhelés átlagát ábrázoló oszlopokat is láthat.

![Apache Ambari-gazdagépek lapja – áttekintés](./media/hdinsight-key-scenarios-to-monitor/apache-ambari-hosts-tab.png)

A gazdagépen futó összetevők és azok metrikáinak részletes megtekintéséhez válassza ki az állomásnév bármelyikét. A metrikák a CPU-használat, a terhelés, a lemezhasználat, a memóriahasználat, a hálózati használat és a folyamatok száma szerint választható idővonalként jelennek meg.

![Az Apache Ambari-gazdagép részleteinek áttekintése](./media/hdinsight-key-scenarios-to-monitor/apache-ambari-host-details.png)

A riasztások beállításával és a metrikák megtekintésével kapcsolatos részletekért lásd: [HDInsight-fürtök kezelése az Apache Ambari webes felületének használatával](hdinsight-hadoop-manage-ambari.md) .

## <a name="yarn-queue-configuration"></a>A FONALak várólistájának konfigurációja

A Hadoop különböző szolgáltatásokkal rendelkezik, amelyek az elosztott platformon futnak. A fonal (még egy másik erőforrás-egyeztető) koordinálja ezeket a szolgáltatásokat, és lefoglalja a fürt erőforrásait, így biztosítva, hogy a terhelés egyenletesen oszlik el a fürtön.

A fonal a JobTracker, az erőforrás-kezelés és a feladatok ütemezésének és figyelésének két feladatát osztja el két démonban: egy globális erőforrás-kezelőt és egy alkalmazáson belüli ApplicationMaster (AM).

A Resource Manager egy *tiszta ütemező*, és kizárólag az összes versengő alkalmazás között a rendelkezésre álló erőforrások egyeztetését. A Resource Manager biztosítja, hogy minden erőforrás mindig használatban legyen, optimalizálja a különböző állandókat, például a SLA-kat, a kapacitási garanciákat és így tovább. A ApplicationMaster egyezteti az erőforrásokat a Resource Managerben, és együttműködik a NodeManager (ok) val a tárolók és az erőforrások felhasználásának végrehajtásához és figyeléséhez.

Ha több bérlő is osztozik egy nagy fürtön, a fürt erőforrásainak versenye van. A CapacityScheduler egy csatlakoztatható ütemező, amely a kérések várólistára helyezésével segíti az erőforrások megosztását. A CapacityScheduler a *hierarchikus várólistákat* is támogatja annak biztosítására, hogy az erőforrások meg legyenek osztva egy szervezet alvárólisták között, mielőtt más alkalmazások várólistái is használhatják az ingyenes erőforrásokat.

A fonal lehetővé teszi, hogy erőforrásokat foglaljon le ezekhez a várólistákhoz, és megjeleníti, hogy az összes rendelkezésre álló erőforrás hozzá van-e rendelve. A várólistákkal kapcsolatos információk megtekintéséhez jelentkezzen be a Ambari webes felhasználói felületére, majd a felső menüben válassza a **fonal Queue Manager** lehetőséget.

![Apache Ambari-FONALak üzenetsor-kezelője](./media/hdinsight-key-scenarios-to-monitor/apache-yarn-queue-manager.png)

A FONALak üzenetsor-kezelője lapon láthatók a bal oldali várólisták listája, valamint az egyesekhez rendelt kapacitások százalékos aránya.

![A CÉRNA üzenetsor-kezelő részletei lap](./media/hdinsight-key-scenarios-to-monitor/yarn-queue-manager-details.png)

Részletesebben tekintse meg a várólistákat a Ambari irányítópultján, a bal oldali listából válassza ki a **fonal** szolgáltatást. Ezután a **gyors hivatkozások** legördülő menüben válassza ki a **Resource Manager felhasználói felületét** az aktív csomópont alatt.

![Resource Manager felhasználói felület menü hivatkozásai](./media/hdinsight-key-scenarios-to-monitor/resource-manager-ui-menu-link.png)

A Resource Manager felhasználói felületén válassza a **Scheduler** lehetőséget a bal oldali menüben. Megjelenik az *alkalmazás-várólisták*alatt található várólisták listája. Itt megtekintheti az egyes várólistákhoz használt kapacitást, a feladatok elosztásának módját, valamint azt, hogy az adott feladatok erőforrás-korlátozottak-e.

![Apache HAdoop Resource Manager felhasználói felület menü](./media/hdinsight-key-scenarios-to-monitor/resource-manager-ui-menu.png)

## <a name="storage-throttling"></a>Tárolás szabályozása

A fürt teljesítményének szűk keresztmetszete a tárolási szinten fordulhat elő. Ez a szűk keresztmetszet leggyakrabban a bemeneti/kimeneti (i/o) műveletek *blokkolása* miatt fordul elő, amelyek akkor történnek, ha a futó feladatok több IO-t küldenek, mint amennyit a Storage szolgáltatás tud kezelni. Ez a blokkolás létrehoz egy várólistát a feldolgozásra váró IO-kérelmekről, amíg a jelenlegi IOs-t nem dolgozza fel. A blokkok *tárolási szabályozás*miatt, amely nem egy fizikai korlát, hanem a tárolási szolgáltatás által a szolgáltatói szerződés (SLA) által kiszabott korlát. Ez a korlát biztosítja, hogy egyetlen ügyfél vagy bérlő se Sajátítsa el a szolgáltatást. Az SLA korlátozza a másodpercenkénti IOs (IOPS) számát az Azure Storage-ban – részletekért lásd a [skálázhatóságot és a teljesítményre vonatkozó célokat a standard Storage-fiókok esetében](../storage/common/scalability-targets-standard-account.md).

Ha az Azure Storage-t használja, a tárolással kapcsolatos problémák figyelésével, beleértve a szabályozást is, tekintse meg az [Microsoft Azure Storage figyelése, diagnosztizálása és hibaelhárítása](https://docs.microsoft.com/azure/storage/storage-monitoring-diagnosing-troubleshooting)című témakört.

Ha a fürtön lévő tároló Azure Data Lake Storage (ADLS), akkor a sávszélesség-korlátozás miatt a szabályozás legvalószínűbb. Ebben az esetben a szabályozás a feladatok naplóiban előforduló szabályozási hibák megfigyelésével azonosítható. A ADLS kapcsolatos további információkért tekintse meg a szabályozás szakaszt a megfelelő szolgáltatáshoz a következő cikkekben:

* [Teljesítmény-finomhangolási útmutató a HDInsight és a Azure Data Lake Storage Apache Hive](../data-lake-store/data-lake-store-performance-tuning-hive.md)
* [Teljesítmény-finomhangolási útmutató a HDInsight és Azure Data Lake Storage MapReduce](../data-lake-store/data-lake-store-performance-tuning-mapreduce.md)
* [Teljesítmény-finomhangolási útmutató a HDInsight és a Azure Data Lake Storage Apache Storm](../data-lake-store/data-lake-store-performance-tuning-storm.md)

## <a name="next-steps"></a>Következő lépések

A fürtök hibaelhárításával és figyelésével kapcsolatos további információkért tekintse meg az alábbi hivatkozásokat:

* [HDInsight-naplók elemzése](hdinsight-debug-jobs.md)
* [Alkalmazások hibakeresése Apache Hadoop FONALak naplóival](hdinsight-hadoop-access-yarn-app-logs-linux.md)
* [Halom-memóriaképek engedélyezése Apache Hadoop-szolgáltatásokhoz Linux-alapú HDInsight](hdinsight-hadoop-collect-debug-heap-dump-linux.md)
