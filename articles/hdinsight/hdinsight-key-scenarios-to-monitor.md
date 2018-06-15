---
title: Fürt teljesítmény - Azure HDInsight figyelése |} Microsoft Docs
description: HDInsight-fürtök a kapacitást és teljesítményt figyelésének módjáról.
services: hdinsight
documentationcenter: ''
tags: azure-portal
author: maxluk
manager: jhubbard
editor: cgronlun
ms.assetid: ''
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.date: 09/27/2017
ms.author: maxluk
ms.openlocfilehash: 9bf49631da58de86ffa1881bca976cab86677805
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2018
ms.locfileid: "31403746"
---
# <a name="monitor-cluster-performance"></a>Fürtteljesítmény monitorozása

A figyelés állapotának és teljesítményének a HDInsight-fürtök létfontosságú megőrzéséhez a maximális teljesítmény- és erőforrás-használat. Figyelés segítségével cím lehetséges kódolási vagy a fürt konfigurációs hibák.

A következő szakaszok ismertetik, hogyan optimalizálható a fürt betöltése, a YARN várólista hatékonyság és a tárolási kisegítő.

## <a name="cluster-loading"></a>Fürt betöltése

Hadoop-fürtök kell terheléselosztást betöltése a fürt csomópontjai között. A terheléselosztás megakadályozza, hogy feladatokat RAM, a CPU és a lemezerőforrásokat korlátozzák.

Ahhoz, hogy a csomópontok a fürt és a betöltés áttekintése, jelentkezzen be a [Ambari webes felhasználói felületén](hdinsight-hadoop-manage-ambari.md), majd jelölje be a **állomások** fülre. A gazdagépek a teljes tartománynevek alapján vannak listázva. Minden állomás működési állapota színes állapotát jelző jelenik meg:

| Szín | Leírás |
| --- | --- |
| Piros | A gazdagépen legalább egy fő összetevő nem működik. Vigye listák összetevőhöz elemleírás megjelenítéséhez. |
| Narancssárga | A gazdagépen legalább egy alárendelt összetevő nem működik. Vigye listák összetevőhöz elemleírás megjelenítéséhez. |
| Sárga | Ambari kiszolgálót nem konfigurálták a szívverést a gazdagépről a több mint 3 perc alatt. |
| Zöld | A normál futó állapotban. |

Oszlopok magok száma és a RAM mennyisége megjelenítő minden állomás számára is láthatók, és a lemezek használata terén, és a betöltés átlagos.

![Lap](./media/hdinsight-key-scenarios-to-monitor/hosts-tab.png)

Válassza ki bármelyik fut az adott gazdagépen és a metrikák összetevők részletes tekintse meg az állomás nevét. A metrikák használati, load, lemezek használata terén, memória használata, hálózati használata, és folyamatok száma a választható ütemterv szerint jelennek meg.

![állomás részletei](./media/hdinsight-key-scenarios-to-monitor/host-details.png)

Lásd: [kezelése HDInsight-fürtök az Ambari webes felhasználói felület használatával](hdinsight-hadoop-manage-ambari.md) -riasztások beállítása és metrikákat megtekintése.

## <a name="yarn-queue-configuration"></a>YARN várósor beállítása

Hadoop van az elosztott platformon futó különböző szolgáltatások. (Még egy másik erőforrás egyeztető) koordinálja az ezeket a szolgáltatásokat, fürt-erőforrásokat foglal le, és felügyeli a közös adatkészletet való hozzáférést.

YARN két démonok felosztja a JobTracker, az erőforrás-kezelés és a feladat ütemezés/figyelése, a két feladat: a globális erőforrás-kezelő, és egy alkalmazás ApplicationMaster (óra).

Az erőforrás-kezelő rendszer egy *tiszta Feladatütemező*, és kizárólag arbitrates rendelkezésre álló erőforrások minden versengő alkalmazások között. Az erőforrás-kezelő biztosítja, hogy minden erőforrás mindig a használatát, optimalizálja a szolgáltatásiszint-szerződések, például különböző állandók kapacitás biztosítja, és így tovább. A ApplicationMaster egyezteti a erőforrás-kezelő erőforrásokat, és együttműködik a végrehajtáshoz, és figyelheti a tárolók és a hálózatierőforrás-fogyasztás NodeManager(s).

Ha több bérlő egy nagy fürtön osztozik, akkor erőforrásokért való versengés a fürt erőforrásait. A CapacityScheduler egy moduláris Feladatütemező, amely segít a kérelmek várólistázást megosztás erőforrás. A CapacityScheduler is támogatja a *hierarchikus várólisták* annak érdekében, hogy erőforrásokat egy szervezet, mielőtt más alkalmazások várólisták számára megengedett szabad erőforrást alárendelt várólisták között vannak megosztva.

Lehetővé teszi, hogy ezek a várólisták-erőforrásokat, és bemutatja, hogy minden, a rendelkezésre álló erőforrásokat rendelt. Tekintse meg a várólisták, jelentkezzen be az Ambari webes felhasználói felületén, majd jelölje ki **YARN várólista-kezelő** a felső menüben.

![YARN várólista-kezelő](./media/hdinsight-key-scenarios-to-monitor/yarn-queue-manager.png)

A YARN várólista-kezelő lap bal oldalán rendelt egyes kapacitás százalékos együtt a várólisták listáját tartalmazza.

![YARN várólista-kezelő Részletek lap](./media/hdinsight-key-scenarios-to-monitor/yarn-queue-manager-details.png)

Válassza ki a várólisták, az Ambari irányítópultról részletesebb tekintse meg a **YARN** szolgáltatás a bal oldali listában. Majd alatt a **Gyorshivatkozások** legördülő menüben válassza **erőforrás-kezelő felhasználói felületén** az aktív csomópont alatt.

![Erőforrás-kezelő felhasználói felületén menü-hivatkozás](./media/hdinsight-key-scenarios-to-monitor/resource-manager-ui-menu.png)

Válassza ki az erőforrás-kezelő felhasználói felületén, **Feladatütemező** a bal oldali menüből. A várólisták alatt listájának megtekintéséhez *alkalmazás várólisták*. Itt láthatja a kapacitási minden, a várólisták, mennyire osztják meg a feladatok között, és hogy minden feladat erőforrás-korlátozott.

![Erőforrás-kezelő felhasználói felületén menü-hivatkozás](./media/hdinsight-key-scenarios-to-monitor/resource-manager-ui.png)

## <a name="storage-throttling"></a>Tárolási sávszélesség-szabályozás

A fürt teljesítménybeli szűk keresztmetszetek akkor fordulhat elő, a tároló szintjén. Ez a szűk keresztmetszetek típus leggyakrabban miatt *blokkolja* bemeneti/kimeneti (I/O) műveletek végrehajtását, amely fordulhat elő, ha a futó feladatok további IO, mint a társzolgáltatás kezelni tud küldeni. A blokkolás létrehoz egy üzenetsort, amíg aktuális IOs feldolgozása után feldolgozásra váró I/O kérelmek. A blokkok vannak miatt *tárolási sávszélesség-szabályozás*, amely nem egy fizikai határérték, de ahelyett, hogy egy által előírt határérték a tároló szolgáltatás által a szolgáltatásiszint-szerződéssel (SLA). Ezt a korlátozást biztosítja, hogy nincs egyetlen ügyfél vagy a bérlői is se sajátíthassa ki a szolgáltatást. A szolgáltatásiszint-szerződést korlátozza az IOs másodpercenként (IOPS) az Azure Storage - részletek című [Azure Storage méretezhetőségi és teljesítménycéloknak](https://docs.microsoft.com/azure/storage/storage-scalability-targets).

Használatakor Azure Storage tárolással kapcsolatos problémák figyelésére vonatkozó információkat, beleértve a sávszélesség-szabályozás, lásd [figyelése, diagnosztizálása és elhárítása a Microsoft Azure Storage](https://docs.microsoft.com/azure/storage/storage-monitoring-diagnosing-troubleshooting).

Ha a fürt biztonsági tároló Azure Data Lake tárolja (ADLS-), valószínűleg azért, mert a sávszélesség korlátja a sávszélesség-szabályozás. Sávszélesség-szabályozás ebben az esetben azonosítható betartásával szabályozási hibák feladat naplókban által. ADLS című rész a sávszélesség-szabályozási ezek a cikkek megfelelő szolgáltatása esetében:

* [Teljesítmény-finomhangolási útmutató a Hive on HDInsighthoz és az Azure Data Lake Store-hoz](../data-lake-store/data-lake-store-performance-tuning-hive.md)
* [Teljesítmény-finomhangolási útmutató a MapReduce on HDInsighthoz és az Azure Data Lake Store-hoz](../data-lake-store/data-lake-store-performance-tuning-mapreduce.md)
* [Teljesítmény-finomhangolási útmutató a Storm on HDInsighthoz és az Azure Data Lake Store-hoz](../data-lake-store/data-lake-store-performance-tuning-storm.md)

## <a name="next-steps"></a>További lépések

Látogasson el az alábbi hivatkozások további információt a hibaelhárítás és a fürtök figyelése:

* [HDInsight-naplók elemzése](hdinsight-debug-jobs.md)
* [Alkalmazások hibakeresése YARN-naplók használatával](hdinsight-hadoop-access-yarn-app-logs-linux.md)
* [Halommemória memóriaképek a Linux-alapú HDInsight Hadoop-szolgáltatások engedélyezése](hdinsight-hadoop-collect-debug-heap-dump-linux.md)
