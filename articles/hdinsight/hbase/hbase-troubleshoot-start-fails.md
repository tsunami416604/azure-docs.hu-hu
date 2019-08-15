---
title: Az Apache HBase Master nem indul el az Azure HDInsight
description: Az Apache HBase Master (HMaster) nem indul el az Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.date: 08/06/2019
ms.openlocfilehash: 8368ebfca4cdd72c5c455a04e29b6c0cb44938ea
ms.sourcegitcommit: 13a289ba57cfae728831e6d38b7f82dae165e59d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/09/2019
ms.locfileid: "68935405"
---
# <a name="apache-hbase-master-hmaster-fails-to-start-in-azure-hdinsight"></a>Az Apache HBase Master (HMaster) nem indul el az Azure HDInsight

Ez a cikk az Azure HDInsight-fürtökkel való interakció során felmerülő problémák hibaelhárítási lépéseit és lehetséges megoldásait ismerteti.

## <a name="scenario-atomic-renaming-failure"></a>Forgatókönyv: Atomi átnevezési hiba

### <a name="issue"></a>Probléma

Váratlan fájlok vannak azonosítva az indítási folyamat során.

### <a name="cause"></a>Ok

Az indítási folyamat során a HMaster számos inicializálási lépést hajt végre, beleértve az adatok áthelyezését a semmiből (. tmp) mappából az adatok mappájába. A HMaster a WALs (írás előtt álló naplók) mappában is megtekintheti, hogy vannak-e Holt régió-kiszolgálók. Az összes ilyen helyzetben alapszintű `list` parancsot hajt végre ezeken a mappákon. Ha a mappák bármelyikében váratlan fájlt lát, akkor kivételt jelez, ezért nem indul el.

### <a name="resolution"></a>Megoldás:

Ebben az esetben a hívási veremben ellenőrizze, hogy melyik mappa okozza a problémát (például a WALs mappa vagy a. tmp mappa). Ezután a Cloud Explorer vagy a hdfs parancsok segítségével keresse meg a problémát tartalmazó fájlt. A probléma általában egy `*-renamePending.json` fájl (a WASB-illesztőprogramban található atomi átnevezési művelet megvalósítására szolgáló Journal-fájl). Az ebben a megvalósításban található hibák miatt az ilyen fájlok megmaradhatnak a folyamat összeomlása esetén. A fájl törlésének kényszerítése a Cloud Explorer használatával. Emellett előfordulhat, hogy az adott helyen a Nature $ ideiglenes fájl található. A fájl nem látható a Cloud Explorer használatával, és csak a `ls` hdfs parancson keresztül. A fájl törléséhez használhatja `hdfs dfs -rm //\$\$\$.\$\$\$` a hdfs parancsot.

A probléma eltávolítása után a HMaster azonnal elindul.

---

## <a name="scenario-no-server-address-listed"></a>Forgatókönyv: Nincs felsorolva kiszolgálócím

### <a name="issue"></a>Probléma

A HMaster log egy, a hbase: a (z) xxx régióhoz tartozó meta-címen található "nincs kiszolgálócím" nevű hibaüzenetet jeleníti meg.

### <a name="cause"></a>Ok

A HMaster nem tudott inicializálni a HBase újraindítása után.

### <a name="resolution"></a>Megoldás:

1. Hajtsa végre a következő parancsokat a HBase-rendszerhéjon (módosítsa a tényleges értékeket a megfelelő értékre):

    ```
    scan 'hbase:meta'
    delete 'hbase:meta','hbase:backup <region name>','<column name>' 
    ```

1. Törölje a hbase: Namespace bejegyzését, mert a hiba a hbase: Namespace tábla vizsgálatakor is megjelenik.

1. Indítsa újra az aktív HMaster a Ambari felhasználói felületéről, hogy a HBase futási állapotban legyen.

1. Futtassa az alábbi parancsot a HBase-rendszerhéjon az összes offline tábla létrehozásához:

    ```
    hbase hbck -ignorePreCheckPermission -fixAssignments
    ```

---

## <a name="scenario-javaioioexception-timedout"></a>Forgatókönyv: Java. IO. IOException: Időtúllépés

### <a name="issue"></a>Probléma

A HMaster időtúllépést okoz, mint `java.io.IOException: Timedout 300000ms waiting for namespace table to be assigned`például a végzetes kivétel.

### <a name="cause"></a>Ok

Az időtúllépés a HMaster ismert hibája. Az általános fürt indítási feladatai hosszú időt vehetnek igénybe. A HMaster leállt, ha még nincs hozzárendelve a névtér tábla. A hosszadalmas indítási feladatok akkor fordulnak elő, amikor nagy mennyiségű kiürítetlen adat létezik, és az 5 perces időkorlát nem elegendő.

### <a name="resolution"></a>Megoldás:

1. A Ambari felhasználói felületének eléréséhez nyissa meg a HBase-> `hbase-site.xml` konfigurációkat az egyéni Hozzáadás a következő beállítással:

    ```
    Key: hbase.master.namespace.init.timeout Value: 2400000  
    ```

1. Indítsa újra a szükséges szolgáltatásokat (főleg HMaster és esetleg más HBase-szolgáltatásokat).

---

## <a name="scenario-frequent-regionserver-restarts"></a>Forgatókönyv: Gyakori regionserver-újraindítások

### <a name="issue"></a>Probléma

A csomópontok rendszeres újraindítása. A regionserver-naplókból a következőhöz hasonló bejegyzések jelenhetnek meg:

```
2017-05-09 17:45:07,683 WARN  [JvmPauseMonitor] util.JvmPauseMonitor: Detected pause in JVM or host machine (eg GC): pause of approximately 31000ms
2017-05-09 17:45:07,683 WARN  [JvmPauseMonitor] util.JvmPauseMonitor: Detected pause in JVM or host machine (eg GC): pause of approximately 31000ms
2017-05-09 17:45:07,683 WARN  [JvmPauseMonitor] util.JvmPauseMonitor: Detected pause in JVM or host machine (eg GC): pause of approximately 31000ms
```

### <a name="cause"></a>Ok

Hosszú regionserver JVM GC szüneteltetése. A Szüneteltetés hatására a regionserver nem válaszol, és nem küldheti el szívverését a HMaster a ZK-munkamenet 40-es időkorlátján belül. A HMaster azt fogja hinni, hogy a regionserver elhalt, és megszakítja a regionserver, és újraindítja azt.

### <a name="resolution"></a>Megoldás:

Módosítsa a Zookeeper-munkamenet időtúllépését, nem csak a hbase `zookeeper.session.timeout` beállítást, hanem a Zookeeper Zoo. `maxSessionTimeout` cfg beállítás módosítását is.

1. A Ambari felhasználói felületének eléréséhez nyissa meg a **HBase-> configs-> beállításokat**, az időtúllépések szakaszban, és módosítsa a Zookeeper-munkamenet időtúllépésének értékét.

1. A Ambari felhasználói felületének eléréséhez nyissa meg a **Zookeeper-> configs-> egyéni** Zoo. cfg fájlt, és adja hozzá a következő beállítást, vagy módosítsa azt. Győződjön meg arról, hogy az érték megegyezik a `zookeeper.session.timeout`hbase.

    ```
    Key: maxSessionTimeout Value: 120000  
    ```

1. Indítsa újra a szükséges szolgáltatásokat.

---

## <a name="scenario-log-splitting-failure"></a>Forgatókönyv: Sikertelen naplózási megszakítás

### <a name="issue"></a>Probléma

A HMasters nem tudott bejutni egy HBase-fürtre.

### <a name="cause"></a>Ok

A másodlagos HDFS és HBase beállításai helytelenül vannak konfigurálva.

### <a name="resolution"></a>Megoldás:

Állítsa be a hbase. wasb://@.blob.core.windows.net/hbase rootdir: programot, és indítsa újra a szolgáltatásokat a Ambari-on.

---

## <a name="next-steps"></a>További lépések

Ha nem látja a problémát, vagy nem tudja megoldani a problémát, további támogatásért látogasson el az alábbi csatornák egyikére:

* Azure-szakértőktől kaphat válaszokat az [Azure közösségi támogatásával](https://azure.microsoft.com/support/community/).

* Kapcsolódjon [@AzureSupport](https://twitter.com/azuresupport) a-a hivatalos Microsoft Azure fiókhoz a felhasználói élmény javítása érdekében. Az Azure-Közösség összekapcsolása a megfelelő erőforrásokkal: válaszok, támogatás és szakértők.

* Ha további segítségre van szüksége, támogatási kérést küldhet a Azure Portaltól [](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Válassza a menüsor **támogatás** elemét, vagy nyissa meg a **Súgó + támogatás** hubot. Részletesebb információkért tekintse át az [Azure-támogatási kérelem létrehozását](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request)ismertető témakört. Az előfizetés-kezeléshez és a számlázási támogatáshoz való hozzáférés a Microsoft Azure-előfizetés része, és a technikai támogatás az egyik [Azure-támogatási csomagon](https://azure.microsoft.com/support/plans/)keresztül érhető el.
