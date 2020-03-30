---
title: Fürtteljesítmény figyelése – Azure HDInsight
description: Az Apache Hadoop-fürtök állapotának és teljesítményének figyelése az Azure HDInsightban.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 03/09/2020
ms.openlocfilehash: 75ac5a7fc352f877573d79a004d8da761c6f1cef
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79082880"
---
# <a name="monitor-cluster-performance-in-azure-hdinsight"></a>Fürtteljesítmény figyelése az Azure HDInsightban

A HDInsight-fürtök állapotának és teljesítményének figyelése elengedhetetlen az optimális teljesítmény és az erőforrás-használat fenntartásához. A figyelés segítségével észlelheti és orvosolhatja a fürtkonfigurációs hibákat és a felhasználói kódokkal kapcsolatos problémákat.

A következő szakaszok ismertetik, hogyan figyelheti és optimalizálhatja a terhelést a fürtök, Apache Hadoop YARN várólisták és a tárolási szabályozás problémák észlelése.

## <a name="monitor-cluster-load"></a>Fürtterhelés figyelése

A Hadoop-fürtök a legoptimálisabb teljesítményt nyújtják, ha a fürt terhelése egyenletesen oszlik el az összes csomópont között. Ez lehetővé teszi, hogy a feldolgozási feladatok futtatásához anélkül, hogy a RAM, a CPU vagy a lemez erőforrásait egyes csomópontokon korlátozná.

A fürt csomópontjainak és betöltésének magas szintű megismeréséhez jelentkezzen be az [Ambari Web felhasználói felületére,](hdinsight-hadoop-manage-ambari.md)majd válassza a **Hosts (Állomások)** lapot. A házigazdák teljesen minősített tartománynevek szerint vannak felsorolva. Minden állomás működési állapotát egy színes állapotjelző mutatja:

| Color | Leírás |
| --- | --- |
| Vörös | Az állomás legalább egy fő összetevője nem. Az érintett összetevőket felsoroló elemleírás megtekintéséhez vigye az egérmutatót. |
| Narancssárga | Az állomás legalább egy másodlagos összetevője nem érhető el. Az érintett összetevőket felsoroló elemleírás megtekintéséhez vigye az egérmutatót. |
| Sárga | Az Ambari Server több mint 3 perce nem kapott szívverést az állomástól. |
| Zöld | Normál működési állapot. |

Oszlopokat is látni fog, amelyek az egyes gazdagép magok számát és a RAM mennyiségét, valamint a lemezhasználat és a terhelési átlagot mutatják.

![Az Apache Ambari állomások lap áttekintése](./media/hdinsight-key-scenarios-to-monitor/apache-ambari-hosts-tab.png)

Válassza ki az állomásnevek bármelyikét az adott gazdagépen futó összetevők és azok metrikák részletes vizsgálata. A mérőszámok a CPU-használat, a terhelés, a lemezhasználat, a memóriahasználat, a hálózati használat és a folyamatok számának választható idővonalaként jelennek meg.

![Apache Ambari host részletek – áttekintés](./media/hdinsight-key-scenarios-to-monitor/apache-ambari-host-details.png)

A [HDInsight-fürtök kezelése az Apache Ambari webfelhasználói felület használatával](hdinsight-hadoop-manage-ambari.md) című témakörben talál részleteket a riasztások beállításával és a mérőszámok megtekintésével.

## <a name="yarn-queue-configuration"></a>YARN várólista konfigurációja

A Hadoop különböző szolgáltatásokat kínál az elosztott platformon keresztül. A YARN (Még egy másik erőforrás-tárgyaló) koordinálja ezeket a szolgáltatásokat, és fürterőforrásokat foglal le annak érdekében, hogy a terhelések egyenletesen legyenek elosztva a fürtön.

A YARN a JobTracker két felelősségi körét, az erőforrás-kezelést és a feladatütemezést/figyelést két démonra osztja: egy globális erőforrás-kezelőre és egy alkalmazásonkénti ApplicationMasterre (AM).

Az Erőforrás-kezelő *egy tiszta ütemező*, és kizárólag az összes versengő alkalmazás között kerül ő a rendelkezésre álló erőforrások között. Az erőforrás-kezelő biztosítja, hogy minden erőforrás mindig használatban van, optimalizálva a különböző állandók, például SLA-k, kapacitásgaranciák, és így tovább. Az ApplicationMaster egyezteti az erőforrásokat az Erőforrás-kezelőtől, és együttműködik a NodeManager(ek)kel a tárolók és azok erőforrás-felhasználásának végrehajtásával és figyelésével.

Ha több bérlő osztozik egy nagy fürtön, verseny van a fürt erőforrásaiért. A CapacityScheduler egy dugaszolható ütemező, amely a kérelmek várólistára állításával segíti az erőforrás-megosztást. A CapacityScheduler támogatja a *hierarchikus várólistákat* is annak érdekében, hogy az erőforrások meg legyenek osztva a szervezet alvárólistái között, mielőtt más alkalmazások várólistái szabad erőforrásokat használhatnának.

A YARN lehetővé teszi számunkra, hogy erőforrásokat osszunk le ezekhez a várólistákhoz, és megmutatja, hogy az összes rendelkezésre álló erőforrás hozzá van-e rendelve. A várólistákkal kapcsolatos információk megtekintéséhez jelentkezzen be az Ambari Web felhasználói felületére, majd válassza a **yarn várólista-kezelő** t a felső menüből.

![Apache Ambari YARN várólista-kezelő](./media/hdinsight-key-scenarios-to-monitor/apache-yarn-queue-manager.png)

A YARN várólista-kezelő lapon a bal oldali várólisták listája, valamint az egyes kapacitások százalékos aránya látható.

![YARN várólistakezelő részletei lap](./media/hdinsight-key-scenarios-to-monitor/yarn-queue-manager-details.png)

A várólisták részletesebb megolvasásához az Ambari irányítópultról válassza ki a **YARN** szolgáltatást a bal oldali listából. Ezután a **Gyorshivatkozások** legördülő menüben válassza az **Erőforrás-kezelő felhasználói felületét** az aktív csomópont alatt.

![Az Erőforrás-kezelő felhasználói felületének hivatkozásai](./media/hdinsight-key-scenarios-to-monitor/resource-manager-ui-menu-link.png)

Az Erőforrás-kezelő felhasználói felületén válassza a bal oldali menü **Ütemező** parancsát. Az alkalmazásvárólisták alatt megjelenik a *várólisták*listája. Itt láthatja az egyes várólistákhoz használt kapacitást, a feladatok közötti elosztás t, valamint azt, hogy a feladatok erőforrás-korlátozott-e.

![Apache HAdoop Resource Manager felhasználói felületmenü](./media/hdinsight-key-scenarios-to-monitor/resource-manager-ui-menu.png)

## <a name="storage-throttling"></a>Tárolási szabályozás

A fürt teljesítménybeli szűk keresztmetszete a tárolási szinten fordulhat elő. Ez a fajta szűk keresztmetszet leggyakrabban a bemeneti/kimeneti (IO) műveletek *blokkolása* miatt történik, amelyek akkor következnek be, amikor a futó feladatok több I/o-t küldenek, mint amennyit a tárolási szolgáltatás kezelni tud. Ez a blokkolás létrehoz egy várólistát az Io-kérelmekre, amelyek feldolgozásra várnak az aktuális IOs feldolgozása utánig. A blokkok a *tárolási szabályozás*miatt, amely nem egy fizikai korlátot, hanem egy korlát által előírt tárolási szolgáltatás egy szolgáltatásiszint-szerződés (SLA). Ez a korlát biztosítja, hogy egyetlen ügyfél vagy bérlő sem sajátíthatja ki a szolgáltatást. Az SLA korlátozza az IOs másodpercenkénti (IOPS) az Azure Storage - a részletekért lásd: [Méretezhetőségi és teljesítménycélok standard tárfiókok.](../storage/common/scalability-targets-standard-account.md)

Ha az Azure Storage-t használja, a tárolással kapcsolatos problémák figyelésével kapcsolatos információkért, például a szabályozásról, olvassa el a [Figyel, diagnosztizálja és elhárítja a Microsoft Azure Storage-t.](https://docs.microsoft.com/azure/storage/storage-monitoring-diagnosing-troubleshooting)

Ha a fürt háttértárolója az Azure Data Lake Storage (ADLS), a sávszélesség-korlátozások valószínűleg a szabályozás miatt. Szabályozás ebben az esetben lehet azonosítani a feladatnaplókban a szabályozási hibák megfigyelésével. Az ADLS-t lásd a megfelelő szolgáltatás szabályozásával című szakaszban az alábbi cikkekben:

* [Teljesítményhangolási útmutató az Apache Hive-hoz a HDInsight és az Azure Data Lake Storage szolgáltatásban](../data-lake-store/data-lake-store-performance-tuning-hive.md)
* [Teljesítményhangolási útmutató a MapReduce szolgáltatáshoz a HDInsight és az Azure Data Lake Storage szolgáltatásban](../data-lake-store/data-lake-store-performance-tuning-mapreduce.md)
* [Teljesítményhangolási útmutató az Apache Storm számára a HDInsight és az Azure Data Lake Storage szolgáltatásban](../data-lake-store/data-lake-store-performance-tuning-storm.md)

## <a name="troubleshoot-sluggish-node-performance"></a>Lassú csomópontteljesítmény – problémamegoldás

Bizonyos esetekben lassúság léphet fel a fürt alacsony lemezterülete miatt. Vizsgálja meg az alábbi lépésekkel:

1. Az [ssh paranccsal](./hdinsight-hadoop-linux-use-ssh-unix.md) csatlakozhat az egyes csomópontokhoz.

1. Ellenőrizze a lemezhasználatot az alábbi parancsok futtatásával:

    ```bash
    df -h
    du -h --max-depth=1 / | sort -h
    ```

1. Tekintse át a kimenetet, és ellenőrizze, `mnt` hogy vannak-e nagy méretű fájlok a mappában vagy más mappákban. A és `usercache`a `appcache` ( mnt/resource/hadoop/yarn/local/usercache/hive/appcache/) mappák általában nagy fájlokat tartalmaznak.

1. Ha nagy fájlok vannak, vagy egy aktuális feladat okozza a fájl növekedését, vagy egy sikertelen korábbi feladat hozzájárulhatott ehhez a problémához. Annak ellenőrzéséhez, hogy ezt a viselkedést egy aktuális feladat okozza-e, futtassa a következő parancsot:

    ```bash
    sudo du -h --max-depth=1 /mnt/resource/hadoop/yarn/local/usercache/hive/appcache/
    ```

1. Ha ez a parancs egy adott feladatot jelez, a következőhöz hasonló paranccsal leszakíthatja a feladatot:

    ```bash
    yarn application -kill -applicationId <application_id>
    ```

    Cserélje `application_id` le az alkalmazásazonosítóra. Ha nincs konkrét feladat megjelölt, folytassa a következő lépéssel.

1. Miután a fenti parancs befejeződött, vagy ha nincsenek konkrét feladatok, törölje a parancs futtatásával azonosított nagyméretű fájlokat, amelyek a következőkre hasonlítanak:

    ```bash
    rm -rf filecache usercache
    ```

A lemezterülettel kapcsolatos problémákról a [Lemezterület hiánya](./hadoop/hdinsight-troubleshoot-out-disk-space.md)című témakörben talál további információt.

> [!NOTE]  
> Ha nagy fájlokat szeretne megtartani, de hozzájárulnak az alacsony lemezterület-problémához, fel kell skáláznia a HDInsight-fürtöt, és újra kell indítania a szolgáltatásokat. Miután befejezte ezt az eljárást, és várjon néhány percet, észre fogja venni, hogy a tárterület felszabadul, és a csomópont szokásos teljesítménye helyreáll.

## <a name="next-steps"></a>További lépések

A fürtök hibaelhárításával és figyelésével kapcsolatos további információkért látogasson el az alábbi hivatkozásokra:

* [HDInsight-naplók elemzése](hdinsight-debug-jobs.md)
* [Hibakeresési alkalmazások Apache Hadoop YARN naplókkal](hdinsight-hadoop-access-yarn-app-logs-linux.md)
* [Halommemória-memóriaképek engedélyezése Apache Hadoop-szolgáltatásokhoz Linux-alapú HDInsight-alapú](hdinsight-hadoop-collect-debug-heap-dump-linux.md)
