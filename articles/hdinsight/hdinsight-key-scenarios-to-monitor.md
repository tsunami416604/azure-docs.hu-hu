---
title: Fürtteljesítmény monitorozása – Azure HDInsight
description: Hogyan lehet a kapacitás és teljesítmény érdekében egy HDInsight-fürt monitorozására.
services: hdinsight
author: maxluk
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 09/27/2017
ms.author: maxluk
ms.openlocfilehash: 434b3ecf65aaa5ecea81f5a9773f1bc6e8f6f2be
ms.sourcegitcommit: f6e2a03076679d53b550a24828141c4fb978dcf9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/27/2018
ms.locfileid: "43092327"
---
# <a name="monitor-cluster-performance"></a>Fürtteljesítmény monitorozása

Figyelési állapotát és teljesítményét egy HDInsight-fürt elengedhetetlen fenntartása a maximális teljesítmény- és erőforrás-használatot. Figyelés is segíthet cím lehetséges kódolási vagy a fürt konfigurációs hibái.

A következő szakaszok ismertetik, hogyan optimalizálható a fürt betöltése, a YARN várólista hatékonyság és a storage kisegítő lehetőségek.

## <a name="cluster-loading"></a>Fürt betöltése

Hadoop-fürtök kell terheléselosztást végeznie betöltése a fürt csomópontjai között. A terheléselosztás megakadályozza, hogy feldolgozás feladatokat RAM memória, Processzor vagy lemez-erőforrásainak korlátozza.

A csomópontok a fürt és a betöltés áttekintése kapni, jelentkezzen be a [az Ambari webes felhasználói felület](hdinsight-hadoop-manage-ambari.md), majd válassza ki a **gazdagépek** fülre. A gazdagépek a teljes tartománynevek alapján vannak listázva. Minden gazdagép működési állapota színes állapotjelző szerint jelenik meg:

| Szín | Leírás |
| --- | --- |
| Piros | A gazdagépen legalább egy fő összetevője leállt. Listák összetevőhöz elemleírásokban megtekintéséhez mutasson. |
| Narancssárga | A gazdagépen legalább egy alárendelt kiszolgáló-összetevő nem működik. Listák összetevőhöz elemleírásokban megtekintéséhez mutasson. |
| Sárga | Az Ambari kiszolgáló nem kapott szívverést a gazdagépről a több mint 3 perc alatt. |
| Zöld | A normál futó állapotban. |

Azt is láthatja minden gazdagéphez a magok számát és a RAM mennyisége megjelenítő oszlopokat, és a használat és a terhelés átlagos.

![Lap](./media/hdinsight-key-scenarios-to-monitor/hosts-tab.png)

Válassza ki, sem a gazdagép részletes tekintse meg, hogy a gazdagép és a metrikák futó összetevők számára. Egy választható idővonalának CPU használati, betöltése, használat, memóriahasználat, hálózati használati és folyamatok számát, a metrikák jelennek meg.

![gazdagép részletei](./media/hdinsight-key-scenarios-to-monitor/host-details.png)

Lásd: [kezelése a HDInsight-fürtök az Ambari webes felhasználói felület használatával](hdinsight-hadoop-manage-ambari.md) -riasztások beállítása és mérőszámok megtekintésével.

## <a name="yarn-queue-configuration"></a>YARN üzenetek sorának konfigurációját

Hadoop elosztott platformja futó különböző szolgáltatásokat tartalmaz. YARN (még egy másik Resource Negotiator) koordinálja a ezeket a szolgáltatásokat, fürt erőforrásokat foglal le, és kezeli a hozzáférést egy közös adatkészlet.

YARN osztja fel a két felelősséget a JobTracker, az erőforrás-kezelést és a feladat ütemezése és figyelése, a két démonok: egy globális erőforrás-kezelő és a egy alkalmazásonkénti ApplicationMaster (kor).

Az erőforrás-kezelő rendszer egy *tiszta scheduler*, és minden versengő alkalmazások között elérhető erőforrások kizárólag arbitrates. Az erőforrás-kezelő biztosítja, hogy az összes erőforrás mindig a használatát, SLA-k, például különböző konstansok optimalizálása garantálja a kapacitás, és így tovább. A ApplicationMaster egyezteti az erőforrás-kezelő erőforrásokat, és működik együtt a NodeManager(s) végrehajtásához és a tárolók és az erőforrás-használat figyeléséhez.

Ha több bérlő osztozik egy nagy fürt, nincs a fürt az erőforrásokért való. A CapacityScheduler moduláris ütemező, amely segíti a várólista által végrehajtott kérelmek által megosztás erőforrás. Emellett támogatja a CapacityScheduler *hierarchikus üzenetsorok* annak érdekében, hogy a szervezet, mielőtt más alkalmazások várólisták használata ingyenes erőforrások alárendelt várólisták megosztott erőforrások.

YARN lehetővé teszi számunkra, hogy ezek a várólisták erőforrásokat, és bemutatja, hogy az összes rendelkezésre álló erőforrás rendelt. Az üzenetsorokkal kapcsolatos információk megtekintéséhez jelentkezzen be az Ambari webes felhasználói Felületet, majd jelölje ki **YARN üzenetsor-kezelő** a felső menüben.

![YARN üzenetsor-kezelő](./media/hdinsight-key-scenarios-to-monitor/yarn-queue-manager.png)

A YARN üzenetsor-kezelő lap az üzenetsorok listája látható, a bal oldalon, a hozzárendelt kapacitás százalékos együtt.

![YARN üzenetsor-kezelő Részletek lap](./media/hdinsight-key-scenarios-to-monitor/yarn-queue-manager-details.png)

Válassza ki a várólisták, az Ambari irányítópultról részletesebb tekintse meg a **YARN** szolgáltatást, a bal oldali listából. Majd a **Gyorshivatkozások** legördülő menüjében válassza **ResourceManager felhasználói felülete** az aktív csomópont alá.

![Erőforrás-kezelő felhasználói felületén menü-hivatkozás](./media/hdinsight-key-scenarios-to-monitor/resource-manager-ui-menu.png)

Az erőforrás-kezelő felhasználói felületén, válassza ki a **Scheduler** elemet a bal oldali menüben. Láthatja, hogy az alatta lévő üzenetsorok listája *alkalmazás üzenetsorok*. Itt láthatja a kapacitást, az üzenetsorok, arról, hogy a feladatok oszlanak meg közöttük, minden egyes használt, és e feladatok bármelyik erőforrás korlátozott.

![Erőforrás-kezelő felhasználói felületén menü-hivatkozás](./media/hdinsight-key-scenarios-to-monitor/resource-manager-ui.png)

## <a name="storage-throttling"></a>Storage-szabályozás

Egy fürt teljesítménybeli szűk keresztmetszetet fordulhat elő, a tárolási szinten. Ez a szűk keresztmetszetet típus leggyakoribb oka az, hogy *blokkolja* bemeneti/kimeneti (I/O) műveleteket, amelyeket fordulhat elő, ha a futó feladatok küldése további IO, mint a storage szolgáltatás képes kezelni. Ez a blokkolás létrehoz egy üzenetsort, az IOS-es feldolgozása után amíg feldolgozásra váró i/o-kérések. A blokkok miatt vannak *tárolási szabályozás*, amely nem fizikai korlátozva, hanem inkább egy által előírt határérték a storage szolgáltatás egy szolgáltatásiszint-szerződés (SLA). Ez a korlátozás biztosítja, hogy nincs egyetlen ügyfél vagy a bérlő is kisajátítja a szolgáltatást. Az SLA korlátozza az IOS-es másodpercenként (IOPS) az Azure Storage - részletekért, lásd: [Azure Storage méretezhetőségi és Teljesítménycéljai](https://docs.microsoft.com/azure/storage/storage-scalability-targets).

Ha Azure Storage, a storage szolgáltatással kapcsolatos problémák figyelési információkat használ, beleértve a szabályozás, lásd [figyelése, diagnosztizálása és hibaelhárítása a Microsoft Azure Storage](https://docs.microsoft.com/azure/storage/storage-monitoring-diagnosing-troubleshooting).

Ha a fürt háttértárban Azure Data Lake Store (ADLS), a szabályozás oka valószínűleg sávszélesség korlátozásai miatt. Szabályozás ebben az esetben sikerült azonosítható a feladat naplókban szabályozási hibákat észlelt problémát. ADLS tekintse meg a sávszélesség-szabályozási szakaszban a megfelelő szolgáltatás a következő cikkeket:

* [Teljesítmény-finomhangolási útmutató a Hive on HDInsighthoz és az Azure Data Lake Store-hoz](../data-lake-store/data-lake-store-performance-tuning-hive.md)
* [Teljesítmény-finomhangolási útmutató a MapReduce on HDInsighthoz és az Azure Data Lake Store-hoz](../data-lake-store/data-lake-store-performance-tuning-mapreduce.md)
* [Teljesítmény-finomhangolási útmutató a Storm on HDInsighthoz és az Azure Data Lake Store-hoz](../data-lake-store/data-lake-store-performance-tuning-storm.md)

## <a name="next-steps"></a>További lépések

Látogasson el az alábbi hivatkozások további információ a hibaelhárítás és a fürtök figyelése:

* [HDInsight-naplók elemzése](hdinsight-debug-jobs.md)
* [Alkalmazások hibakeresése YARN-naplók használatával](hdinsight-hadoop-access-yarn-app-logs-linux.md)
* [Halomürítések engedélyezése a Linux-alapú HDInsight a Hadoop-szolgáltatásokhoz](hdinsight-hadoop-collect-debug-heap-dump-linux.md)
