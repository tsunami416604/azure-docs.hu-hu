---
title: Az Apache HBase Master nem indul el az Azure HDInsight
description: Az Apache HBase Master (HMaster) nem indul el az Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 08/14/2019
ms.openlocfilehash: 290b541d9b5e86616373d2e426241fca07e780ed
ms.sourcegitcommit: 8e9a6972196c5a752e9a0d021b715ca3b20a928f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/11/2020
ms.locfileid: "75887206"
---
# <a name="apache-hbase-master-hmaster-fails-to-start-in-azure-hdinsight"></a>Az Apache HBase Master (HMaster) nem indul el az Azure HDInsight

Ez a cikk az Azure HDInsight-fürtökkel való interakció során felmerülő problémák hibaelhárítási lépéseit és lehetséges megoldásait ismerteti.

## <a name="scenario-atomic-renaming-failure"></a>Forgatókönyv: atomi átnevezési hiba

### <a name="issue"></a>Probléma

Váratlan fájlok vannak azonosítva az indítási folyamat során.

### <a name="cause"></a>Ok

Az indítási folyamat során a HMaster számos inicializálási lépést hajt végre, beleértve az adatok áthelyezését a semmiből (. tmp) mappából az adatok mappájába. A HMaster azt is megvizsgálja, hogy vannak-e nem válaszoló régió-kiszolgálók.

A HMaster egy alapszintű List parancsot tartalmaz a WAL-mappákon. Ha bármikor, a HMaster egy nem várt fájlt lát ezen mappák valamelyikében, kivételt jelez, és nem indul el.

### <a name="resolution"></a>Felbontás

Ellenőrizze a hívási veremet, és próbálja meg megállapítani, hogy melyik mappa okozza a problémát (például lehet, hogy a WAL-mappa vagy a. tmp mappa). Ezután a Cloud Explorerben vagy a HDFS parancsok használatával próbálja megkeresni a problémát tartalmazó fájlt. Ez általában egy `*-renamePending.json`-fájl. (A `*-renamePending.json` fájl egy olyan naplófájl, amely az atomi átnevezési művelet megvalósítására szolgál a WASB-illesztőprogramban. Az ebben a megvalósításban található hibák miatt ezek a fájlok a folyamat összeomlása után is megmaradhatnak, és így tovább.) Kényszerítse a fájl törlését a Cloud Explorerben vagy a HDFS parancsok használatával.

Időnként előfordulhat, hogy az adott helyen `$$$.$$$` hasonló nevű ideiglenes fájl is található. A fájl megjelenítéséhez a HDFS `ls` parancsot kell használnia; a fájl nem látható a Cloud Explorerben. A fájl törléséhez használja a `hdfs dfs -rm /\<path>\/\$\$\$.\$\$\$`HDFS parancsot.

A parancsok futtatása után a HMaster azonnal el kell indulnia.

---

## <a name="scenario-no-server-address-listed"></a>Forgatókönyv: nincs listázva kiszolgáló címe

### <a name="issue"></a>Probléma

Előfordulhat, hogy megjelenik egy üzenet, amely jelzi, hogy a `hbase: meta` tábla nem online állapotú. A `hbck` futtatva jelenthetik, hogy `hbase: meta table replicaId 0 is not found on any region.` a HMaster-naplókban a következő üzenet jelenhet meg: `No server address listed in hbase: meta for region hbase: backup <region name>`.  

### <a name="cause"></a>Ok

A HMaster nem tudott inicializálni a HBase újraindítása után.

### <a name="resolution"></a>Felbontás

1. A HBase-rendszerhéjban írja be a következő parancsokat (módosítsa a tényleges értékeket, ha vannak ilyenek):

    ```hbase
    scan 'hbase:meta'
    delete 'hbase:meta','hbase:backup <region name>','<column name>'
    ```

1. Törölje a `hbase: namespace` bejegyzést. Előfordulhat, hogy ez a bejegyzés ugyanaz a hiba, amelyet a rendszer a `hbase: namespace` tábla vizsgálata során jelez.

1. Indítsa újra az aktív HMaster a Ambari felhasználói felületéről, hogy a HBase futási állapotban legyen.

1. Az összes offline tábla létrehozásához a HBase-rendszerhéjban futtassa a következő parancsot:

    ```hbase
    hbase hbck -ignorePreCheckPermission -fixAssignments
    ```

---

## <a name="scenario-javaioioexception-timedout"></a>Forgatókönyv: Java. IO. IOException: időtúllépés

### <a name="issue"></a>Probléma

A HMaster időtúllépést okoz a következőhöz hasonló végzetes kivétel miatt: `java.io.IOException: Timedout 300000ms waiting for namespace table to be assigned`.

### <a name="cause"></a>Ok

Ez a probléma akkor fordulhat elő, ha sok olyan táblája és régiója van, amely nem lett kiürítve a HMaster-szolgáltatások újraindításakor. Az időtúllépés a HMaster ismert hibája. Az általános fürt indítási feladatai hosszú időt vehetnek igénybe. A HMaster leállt, ha még nincs hozzárendelve a névtér tábla. A hosszadalmas indítási feladatok akkor fordulnak elő, amikor nagy mennyiségű kiürítetlen adat létezik, és az 5 perces időkorlát nem elegendő.

### <a name="resolution"></a>Felbontás

1. Az Apache Ambari felhasználói felületén nyissa meg a **HBase** > **konfigurációit**. Az egyéni `hbase-site.xml` fájlban adja hozzá a következő beállítást:

    ```
    Key: hbase.master.namespace.init.timeout Value: 2400000  
    ```

1. Indítsa újra a szükséges szolgáltatásokat (HMaster és esetleg más HBase-szolgáltatásokat).

---

## <a name="scenario-frequent-region-server-restarts"></a>Forgatókönyv: gyakori régió-kiszolgáló újraindítása

### <a name="issue"></a>Probléma

A csomópontok rendszeres újraindítása. A régió-kiszolgáló naplóiban a következőhöz hasonló bejegyzések jelenhetnek meg:

```
2017-05-09 17:45:07,683 WARN  [JvmPauseMonitor] util.JvmPauseMonitor: Detected pause in JVM or host machine (eg GC): pause of approximately 31000ms
2017-05-09 17:45:07,683 WARN  [JvmPauseMonitor] util.JvmPauseMonitor: Detected pause in JVM or host machine (eg GC): pause of approximately 31000ms
2017-05-09 17:45:07,683 WARN  [JvmPauseMonitor] util.JvmPauseMonitor: Detected pause in JVM or host machine (eg GC): pause of approximately 31000ms
```

### <a name="cause"></a>Ok

Hosszú `regionserver` JVM GC szüneteltetése. A Szüneteltetés hatására a `regionserver` nem válaszol, és nem küldhet szívverést a HMaster a ZK-munkamenet 40-es időkorlátján belül. A HMaster úgy fogja hinni, `regionserver` elhalt, és megszakítja a `regionserver`, és újraindul.

### <a name="resolution"></a>Felbontás

Módosítsa a Zookeeper-munkamenet időkorlátját, ne csak `hbase-site` beállítást, `zookeeper.session.timeout` de Zookeeper `zoo.cfg` beállítást is, `maxSessionTimeout` módosítani kell.

1. A Ambari felhasználói felületének eléréséhez nyissa meg a **HBase-> configs-> beállításokat**, az időtúllépések szakaszban, és módosítsa a Zookeeper-munkamenet időtúllépésének értékét.

1. Hozzáférés Ambari felhasználói felületéhez lépjen a **Zookeeper-> configs-> egyéni** `zoo.cfg`lehetőségre, és adja hozzá a következő beállítást. Győződjön meg arról, hogy az érték megegyezik a HBase `zookeeper.session.timeout`.

    ```
    Key: maxSessionTimeout Value: 120000  
    ```

1. Indítsa újra a szükséges szolgáltatásokat.

---

## <a name="scenario-log-splitting-failure"></a>Forgatókönyv: a naplózási megszakítás sikertelen

### <a name="issue"></a>Probléma

A HMasters nem tudott bejutni egy HBase-fürtre.

### <a name="cause"></a>Ok

A másodlagos HDFS és HBase beállításai helytelenül vannak konfigurálva.

### <a name="resolution"></a>Felbontás

Állítsa be a hbase. rootdir: wasb://@.blob.core.windows.net/hbase, majd indítsa újra a szolgáltatásokat a Ambari-on.

---

## <a name="next-steps"></a>Következő lépések

Ha nem látja a problémát, vagy nem tudja megoldani a problémát, további támogatásért látogasson el az alábbi csatornák egyikére:

* Azure-szakértőktől kaphat válaszokat az [Azure közösségi támogatásával](https://azure.microsoft.com/support/community/).

* Kapcsolódjon a [@AzureSupporthoz](https://twitter.com/azuresupport) – a hivatalos Microsoft Azure fiókot a felhasználói élmény javításához. Az Azure-Közösség összekapcsolása a megfelelő erőforrásokkal: válaszok, támogatás és szakértők.

* Ha további segítségre van szüksége, támogatási kérést küldhet a [Azure Portaltól](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Válassza a menüsor **támogatás** elemét, vagy nyissa meg a **Súgó + támogatás** hubot. Részletesebb információkért tekintse át az [Azure-támogatási kérelem létrehozását](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request)ismertető témakört. Az előfizetés-kezeléshez és a számlázási támogatáshoz való hozzáférés a Microsoft Azure-előfizetés része, és a technikai támogatás az egyik [Azure-támogatási csomagon](https://azure.microsoft.com/support/plans/)keresztül érhető el.
