---
title: Az Apache HBase Master nem indul el az Azure HDInsightban
description: Az Apache HBase Master (HMaster) nem indul el az Azure HDInsightban
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 08/14/2019
ms.openlocfilehash: 290b541d9b5e86616373d2e426241fca07e780ed
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75887206"
---
# <a name="apache-hbase-master-hmaster-fails-to-start-in-azure-hdinsight"></a>Az Apache HBase Master (HMaster) nem indul el az Azure HDInsightban

Ez a cikk az Azure HDInsight-fürtökkel való kommunikáció során felmerülő problémák hibaelhárítási lépéseit és lehetséges megoldásait ismerteti.

## <a name="scenario-atomic-renaming-failure"></a>Forgatókönyv: Atomi átnevezési hiba

### <a name="issue"></a>Probléma

Az indítási folyamat során azonosított váratlan fájlok.

### <a name="cause"></a>Ok

Az indítási folyamat során a HMaster számos inicializálási lépést hajt végre, beleértve az adatok átmozgatását a semmiből (.tmp) az adatmappába. A HMaster a wali (írási előre írt naplók) mappát is megvizsgálja, hogy vannak-e nem válaszoló régiókiszolgálók.

HMaster csinál egy alap lista követel -on WAL tartók. Ha a HMaster bármikor váratlan fájlt lát a mappák bármelyikében, kivételt okoz, és nem indul el.

### <a name="resolution"></a>Megoldás:

Ellenőrizze a hívási vermet, és próbálja meg megállapítani, hogy melyik mappa okozhatja a problémát (például lehet, hogy a WAL mappa vagy a .tmp mappa). Ezután a Cloud Explorerben vagy a HDFS-parancsok használatával próbálja meg keresni a problémás fájlt. Általában ez egy `*-renamePending.json` fájl. (A `*-renamePending.json` fájl egy naplófájl, amely a WASB illesztőprogram ban az atomi átnevezési művelet megvalósításához használatos. A jelen implementáció hibái miatt ezek a fájlok a folyamat összeomlása után is megmaradnak, és így tovább.) A fájl kényszerítése a Cloud Explorer programban vagy a HDFS-parancsok használatával történő törlésre.

Néha előfordulhat, hogy egy ideiglenes fájl `$$$.$$$` neve valami ilyesmi ezen a helyen. A fájl megtekintéséhez `ls` a HDFS parancsot kell használnia; a fájl nem látható a Cloud Explorer ben. A fájl törléséhez használja a `hdfs dfs -rm /\<path>\/\$\$\$.\$\$\$`HDFS parancsot.

Miután futtatta ezeket a parancsokat, a HMasternek azonnal el kell indulnia.

---

## <a name="scenario-no-server-address-listed"></a>Eset: Nincs felsorolva kiszolgálócím

### <a name="issue"></a>Probléma

Megjelenhet egy üzenet, amely `hbase: meta` azt jelzi, hogy a tábla nincs online állapotban. A `hbck` Futó `hbase: meta table replicaId 0 is not found on any region.` jelentheti, hogy a HMaster-naplókban `No server address listed in hbase: meta for region hbase: backup <region name>`a következő üzenet jelenhet meg: .  

### <a name="cause"></a>Ok

A HMaster nem tudta inicializálni a HBase újraindítása után.

### <a name="resolution"></a>Megoldás:

1. A HBase rendszerhéjban adja meg a következő parancsokat (módosítsa a tényleges értékeket):

    ```hbase
    scan 'hbase:meta'
    delete 'hbase:meta','hbase:backup <region name>','<column name>'
    ```

1. Törölje `hbase: namespace` a bejegyzést. Ez a bejegyzés ugyanaz a hiba lehet, `hbase: namespace` amelyet a tábla beolvasásakor jelentett.

1. Indítsa újra az aktív HMaster-t az Ambari felhasználói felületről a HBase futási állapotának beállításához.

1. A HBase rendszerhéjban az összes kapcsolat nélküli tábla felsorolásához futtassa a következő parancsot:

    ```hbase
    hbase hbck -ignorePreCheckPermission -fixAssignments
    ```

---

## <a name="scenario-javaioioexception-timedout"></a>Forgatókönyv: java.io.IOException: Idődout

### <a name="issue"></a>Probléma

HMaster időkiidővel végzetes `java.io.IOException: Timedout 300000ms waiting for namespace table to be assigned`kivétel hasonló : .

### <a name="cause"></a>Ok

Ez a probléma akkor jelentkezhet, ha a HMaster-szolgáltatások újraindításakor nem kiürített táblákés régiók sok. Az időhosszabbítás ismert hiba a HMasternél. Az általános fürtindítási feladatok hosszú időt vehet igénybe. A HMaster leáll, ha a névtértábla még nincs hozzárendelve. A hosszadalmas indítási feladatok akkor történnek, ha nagy mennyiségű kiürítetlen adat áll rendelkezésre, és az öt perces időtúllépés nem elegendő.

### <a name="resolution"></a>Megoldás:

1. Az Apache Ambari felhasználói felületéről nyissa meg a **HBase** > **Configs lapot.** Az egyéni `hbase-site.xml` fájlban adja hozzá a következő beállítást:

    ```
    Key: hbase.master.namespace.init.timeout Value: 2400000  
    ```

1. Indítsa újra a szükséges szolgáltatásokat (HMaster és esetleg más HBase-szolgáltatások).

---

## <a name="scenario-frequent-region-server-restarts"></a>Eset: A gyakori régiókiszolgáló újraindul

### <a name="issue"></a>Probléma

A csomópontok rendszeresen újraindulnak. A régiókiszolgálói naplókban a következőhöz hasonló bejegyzések jelenhetnek meg:

```
2017-05-09 17:45:07,683 WARN  [JvmPauseMonitor] util.JvmPauseMonitor: Detected pause in JVM or host machine (eg GC): pause of approximately 31000ms
2017-05-09 17:45:07,683 WARN  [JvmPauseMonitor] util.JvmPauseMonitor: Detected pause in JVM or host machine (eg GC): pause of approximately 31000ms
2017-05-09 17:45:07,683 WARN  [JvmPauseMonitor] util.JvmPauseMonitor: Detected pause in JVM or host machine (eg GC): pause of approximately 31000ms
```

### <a name="cause"></a>Ok

Hosszú `regionserver` JVM GC szünet. A szünet `regionserver` hatására nem reagál, és nem tudja küldeni a szívverést a HMasternek a zk session timeout 40s-en belül. HMaster fogja `regionserver` hinni halott, és `regionserver` megszakítja a és indítsa újra.

### <a name="resolution"></a>Megoldás:

Módosítsa a Zookeeper munkamenet időtúltöltését, `hbase-site` nemcsak a beállítást, `zookeeper.session.timeout` hanem a Zookeeper `zoo.cfg` beállítást `maxSessionTimeout` is módosítani kell.

1. Hozzáférés Ambari UI, ugrás **HBase -> Configs -> Settings**, a Timeouts szakaszban, módosítsa az értékét Zookeeper session timeout.

1. Hozzáférés Ambari UI, go **Zookeeper -> Configs -> Custom** `zoo.cfg`, add/change a következő beállítást. Győződjön meg arról, hogy `zookeeper.session.timeout`az érték megegyezik a HBase értékkel.

    ```
    Key: maxSessionTimeout Value: 120000  
    ```

1. Indítsa újra a szükséges szolgáltatásokat.

---

## <a name="scenario-log-splitting-failure"></a>Eset: Naplófelosztási hiba

### <a name="issue"></a>Probléma

A HMasters nem tudott feljönni egy HBase-fürtön.

### <a name="cause"></a>Ok

Helytelenül konfigurált HDFS- és HBase-beállítások egy másodlagos tárfiókhoz.

### <a name="resolution"></a>Megoldás:

set hbase.rootdir: wasb://@.blob.core.windows.net/hbase és indítsa újra a szolgáltatásokat ambari.

---

## <a name="next-steps"></a>További lépések

Ha nem látta a problémát, vagy nem tudja megoldani a problémát, további támogatásért látogasson el az alábbi csatornák egyikébe:

* Válaszokat kaphat az Azure szakértőitől az [Azure közösségi támogatásán](https://azure.microsoft.com/support/community/)keresztül.

* Lépjen [@AzureSupport](https://twitter.com/azuresupport) kapcsolatba a hivatalos Microsoft Azure-fiókkal az ügyfélélmény javítása érdekében. Az Azure-közösség összekapcsolása a megfelelő erőforrásokkal: válaszok, támogatás és szakértők.

* Ha további segítségre van szüksége, támogatási kérelmet nyújthat be az [Azure Portalról.](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/) Válassza a **menüsor Támogatás parancsát,** vagy nyissa meg a **Súgó + támogatási** központot. További információkért tekintse [át az Azure-támogatási kérelem létrehozása című áttekintést.](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request) Az Előfizetés-kezelés hez és a számlázási támogatáshoz való hozzáférés a Microsoft Azure-előfizetésrészét képezi, a technikai támogatást pedig az [Azure-támogatási csomagok](https://azure.microsoft.com/support/plans/)egyike biztosítja.
