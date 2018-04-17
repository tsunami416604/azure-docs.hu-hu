---
title: Fürt méretek – Azure HDInsight méretezése |} Microsoft Docs
description: HDInsight-fürtök a munkaterhelés vertikális.
services: hdinsight
documentationcenter: ''
author: ashishthaps
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: ''
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.date: 02/02/2018
ms.author: ashish
ms.openlocfilehash: 8b76d7d0441a5c1c25ad17b73083ec0e4feef1fe
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2018
---
# <a name="scale-hdinsight-clusters"></a>A HDInsight-fürtök méretezése

HDInsight a rugalmasság biztosít felkínálva a lehetőséget növelheti vagy csökkentheti a adhatja meg a fürt feldolgozó csomópontjainak számát. Ez lehetővé teszi, hogy a fürt zsugorítása óra múlva, vagy a hétvégekre, és bontsa ki a csúcs üzleti igények során.

Például ha néhány kötegfeldolgozási rendelkezik, amely naponta egyszer vagy havonta történik, a HDInsight-fürt akár is méretezhető, hogy az ütemezett esemény előtt néhány percet, memória és CPU számítási teljesítményt. Automatizálható a skálázás a PowerShell-parancsmaggal [ `Set–AzureRmHDInsightClusterSize` ](hdinsight-administer-use-powershell.md#scale-clusters).  Később Miután a feldolgozás történik, és használati újra leáll, méretezheti a HDInsight-fürt kevesebb munkavégző csomópontokhoz le.

* Méretezni a fürtön [PowerShell](hdinsight-administer-use-powershell.md):

    ```powershell
    Set-AzureRmHDInsightClusterSize -ClusterName <Cluster Name> -TargetInstanceCount <NewSize>
    ```
    
* Méretezni a fürtön a [Azure CLI](hdinsight-administer-use-command-line.md):

    ```
    azure hdinsight cluster resize [options] <clusterName> <Target Instance Count>
    ```
    
* Méretezni a fürtön a [Azure-portálon](https://portal.azure.com), nyissa meg a HDInsight-fürt ablaktábla, válassza ki **méretezési fürt** a bal oldali menüben, majd a skála fürt ablak, írja be a feldolgozó csomópontok száma és Válassza ki, mentse.

    ![Fürt méretezése](./media/hdinsight-scaling-best-practices/scale-cluster-blade.png)

Az alábbi módszereket használva úgy méretezheti a HDInsight-fürt felfelé vagy lefelé percen belül.

## <a name="scaling-impacts-on-running-jobs"></a>A futó feladatok méretezési hatások

Ha Ön **hozzáadása** csomópontok futó HDInsight-fürtjét, folyamatban lévő vagy futó feladat nem befolyásolja a. Emellett új feladatok biztonságosan küldheti el a méretezési művelet végrehajtása közben. A méretezési művelet bármilyen okból sikertelen, ha a hiba szabályosan kezeli, a fürt működési állapotban hagyja.

Azonban ha folyamatban van a fürthöz le **eltávolítása** csomópontok, minden folyamatban lévő vagy futó feladat sikertelen lesz a méretezési művelet befejeződése után. Ez a hiba az oka, hogy egyes szolgáltatások újraindítása a folyamat során.

A probléma megoldására megvárhatja a feladatok elvégzése előtt skálázás le a fürtöt, manuálisan állítsa le a feladatok vagy küldje el újra a feladatok, miután a méretezési művelet kötött.

Függőben lévő és a futó feladatok megtekintéséhez használhatja a YARN erőforrás-kezelő felhasználói felületén a következő lépéseket:

1. Jelentkezzen be az [Azure portálra](https://portal.azure.com).
2. A bal oldali menüben válassza **Tallózás**, jelölje be **a HDInsight-fürtök**, majd válassza ki a fürtöt.
3. A HDInsight fürt ablaktáblán válassza ki a **irányítópult** a felső menüben az Ambari felhasználói felületének megnyitásához. Adja meg a fürt bejelentkezési hitelesítő adatait.
4. Kattintson a **YARN** a bal oldali menüből a szolgáltatások listájában. A YARN lapon válassza az **Gyorshivatkozások** és az aktív átjárócsomópont mutat, majd kattintson **erőforrás-kezelő felhasználói felületén**.

    ![Erőforrás-kezelő felhasználói felületén](./media/hdinsight-scaling-best-practices/resourcemanager-ui.png)

Lehetséges közvetlenül hozzáférni az erőforrás-kezelő felhasználói felület `https://<HDInsightClusterName>.azurehdinsight.net/yarnui/hn/cluster`.

Feladatok mellett az aktuális állapot listájának megtekintéséhez. A képernyőfelvételen látható a rendszer jelenleg fut egy feladat:

![Erőforrás-kezelő felhasználói felületén alkalmazások](./media/hdinsight-scaling-best-practices/resourcemanager-ui-applications.png)

Manuálisan leállítani a futó alkalmazást, hajtsa végre a következő parancsot az SSH-rendszerhéj:

```bash
yarn application -kill <application_id>
```

Példa:

```bash
yarn application -kill "application_1499348398273_0003"
```

## <a name="rebalancing-an-hbase-cluster"></a>HBase-fürtöt újraelosztás

A skálázási művelet befejezése után néhány percen belül automatikusan elosztását régió kiszolgálók. Manuálisan egyensúlyba régió kiszolgálók, tegye a következőket:

1. Csatlakozzon a HDInsight-fürthöz SSH használatával. További információ: [Az SSH használata HDInsighttal](hdinsight-hadoop-linux-use-ssh-unix.md).

2. Indítsa el a HBase rendszerhéjból:

        hbase shell

3. Az alábbi parancs segítségével manuálisan elosztása az a régió kiszolgálók:

        balancer

## <a name="scale-down-implications"></a>Csökkentheti a gyakorolt hatása

Ahogy korábban említettük, folyamatban lévő vagy futó feladat leállítása van egy skálázás le művelet befejezésekor. Vannak azonban más lehetséges megvalósítását skálázás le, amely akkor fordulhat elő.

## <a name="hdinsight-name-node-stays-in-safe-mode-after-scaling-down"></a>HDInsight neve csomópont után skálázás csökkentett üzemmódban marad.

![Fürt méretezése](./media/hdinsight-scaling-best-practices/scale-cluster.png)

Ha a fürt legalább egy feldolgozó csomópont le az előző ábrán látható módon zsugorítható, HDFS előfordulhat, hogy állapottal csökkentett módban amikor munkavégző csomópontokhoz újraindítása van javítása miatt, vagy közvetlenül a méretezési művelet után.

Az elsődleges ennek oka az, hogy használ-e a Hive néhány `scratchdir` fájlokat, és alapértelmezés szerint minden adatblokk három replikák vár, de nincs csak egy replika lehetséges, ha a minimális egy munkavégző csomópont csökkentheti. Ennek következtében a található fájlokat a `scratchdir` válnak *under-replikált*. Emiatt a HDFS csökkentett módban marad, ha a szolgáltatás újraindul a méretezési művelet után.

Ha egy vertikális kísérlet történik, HDInsight az Ambari felületek először leszerelése az extra nemkívánatos munkavégző csomópontokhoz, amelyet a rendszer replikál a HDFS blokkok más online munkavégző csomópontokhoz, és majd biztonságosan csökkentheti a fürt alapul. HDFS a karbantartási időszak alatt a csökkentett mód állapotba kerül, és állapotba kerülnek, a méretezés befejeződése után. Ezen a ponton, hogy HDFS állapottal csökkentett módban.

HDFS van konfigurálva egy `dfs.replication` 3 beállítása. Így az ideiglenes fájlok blokkok is under-replikált vannak a 3-nál kevesebb munkavégző csomópontot online, mert nem a várt három másolatot minden blokk állnak rendelkezésre.

Ön is végrehajthatja a HDFS kapcsolja ki a biztonságos mód parancsot. Például ha tudja, hogy az csak oka csökkentett módban nem, mert az ideiglenes fájlokat under-replikált, majd biztonságosan hagyhatja csökkentett módban. Ennek az az oka under-replikált fájlok Hive ideiglenes ideiglenes fájlok.

```bash
hdfs dfsadmin -D 'fs.default.name=hdfs://mycluster/' -safemode leave
```

Miután csökkentett módban, manuálisan eltávolíthatja az ideiglenes fájlokat, vagy várjon, amíg a Hive végül karbantartása őket automatikusan.

### <a name="example-errors-when-safe-mode-is-turned-on"></a>Példa hibák csökkentett módban bekapcsolása

* H070 nem Hive munkamenetet nyit meg. org.apache.hadoop.ipc.RemoteException(org.apache.hadoop.ipc.RetriableException): org.apache.hadoop.hdfs.server.namenode.SafeModeException: **könyvtár nem hozható létre**  /tmp/hive/hive / 819c215c - 6d 87-4311-97c 8-4f0b9d2adcf0. **Csökkentett módban van a csomópont neve**. A jelentett blokkok 75 kell további 12 blokkolja a küszöbérték teljes blokkok 87 0.9900 eléréséhez. Az élő datanodes 10 száma elérte a minimális száma 0. A biztonságos mód ki lesz kapcsolva automatikusan a küszöbérték elérése után.

* Adatbázisok H100 nem lehet elküldeni utasítás megjelenítése: org.apache.thrift.transport.TTransportException: org.apache.http.conn.HttpHostConnectException: hn0-clustername.servername.internal.cloudapp.net:10001 [hn0-clustername.servername kapcsolódni . Internal.cloudapp.NET/1.1.1.1] nem sikerült: **kapcsolat elutasítva**

* H020 nem tudott kapcsolatot hn0-hdisrv.servername.bx.internal.cloudapp .net: 10001: org.apache.thrift.transport.TTransportException: nem hozható létre kapcsolat annak http://hn0-hdisrv.servername.bx.internal.cloudapp.net:10001/. org.apache.http.conn.HttpHostConnectException: hn0-hdisrv.servername.bx.internal.cloudapp.net:10001 [hn0-hdisrv.servername.bx.internal.cloudapp.net/10.0.0.28] nem sikerült kapcsolódni: Kapcsolat elutasítva: org.apache.thrift.transport.TTransportException: nem hozható létre kapcsolat annak http://hn0-hdisrv.servername.bx.internal.cloudapp.net:10001/. org.apache.http.conn.HttpHostConnectException: hn0-hdisrv.servername.bx.internal.cloudapp.net:10001 [hn0-hdisrv.servername.bx.internal.cloudapp.net/10.0.0.28] nem sikerült kapcsolódni: **kapcsolat elutasítva**

* A Hive naplókból: [fő] Figyelmeztetés: kiszolgáló. Hiveserver2-n (HiveServer2.java:startHiveServer2(442)) – hiba történt az hiveserver2-n próbálkozás 21, 60 másodperc java.lang.RuntimeException újra fog próbálkozni: engedélyezési házirend alkalmazása a hive-konfigurációs hiba: org.apache.hadoop.ipc.RemoteException () org.apache.hadoop.ipc.RetriableException): org.apache.hadoop.hdfs.server.namenode.SafeModeException: **könyvtár nem hozható létre** /tmp/hive/hive/70a42b8a-9437-466e-acbe-da90b1614374. **Csökkentett módban van a csomópont neve**.
    A jelentett blokkok 0 a küszöbérték teljes blokkok 9 0.9900 eléréséhez további 9 blokkok kell.
    Az élő datanodes 10 száma elérte a minimális száma 0. **A biztonságos mód ki lesz kapcsolva automatikusan a küszöbérték elérése után**.
    at org.apache.hadoop.hdfs.server.namenode.FSNamesystem.checkNameNodeSafeMode(FSNamesystem.java:1324)

Tekintse át a név csomópont naplók a `/var/log/hadoop/hdfs/` mappa közel az idő, amikor a fürt lett méretezve, hogy mikor került csökkentett módban. A naplófájlok elnevezése `Hadoop-hdfs-namenode-hn0-clustername.*`.

A korábbi hibák okozza-e, hogy Hive függ a HDFS ideiglenes fájlok lekérdezések futtatása során. HDFS adja meg a biztonságos mód, ha a struktúra nem futtatható lekérdezések, mert a HDFS nem írható. HDFS-ben az ideiglenes fájlok az egyes munkavégző csomópont virtuális gépek csatlakozik, és replikálja más feldolgozó csomópontok minimális három replikákat, többek között a helyi meghajtón található.

A `hive.exec.scratchdir` paraméter a Hive konfigurálja `/etc/hive/conf/hive-site.xml`:

```xml
<property>
    <name>hive.exec.scratchdir</name>
    <value>hdfs://mycluster/tmp/hive</value>
</property>
```

### <a name="view-the-health-and-state-of-your-hdfs-file-system"></a>Az állapot- és a HDFS fájlrendszer állapotának megtekintése

Megtekintheti, hogy csomópontok csökkentett módban van-e minden név csomópontjáról állapotjelentést. Megtekinti a jelentést, az SSH-ból minden átjárócsomópont, és futtassa a következő parancsot:

```
hdfs dfsadmin -D 'fs.default.name=hdfs://mycluster/' -safemode get
```

![A biztonságos mód kikapcsolása](./media/hdinsight-scaling-best-practices/safe-mode-off.png)

> [!NOTE]
> A `-D` kapcsoló szükség, mert az alapértelmezett fájlrendszert HDInsight vagy az Azure Storage, vagy az Azure Data Lake Store. `-D` Meghatározza, hogy a parancsok végrehajtása során a helyi HDFS fájlrendszerben.

A következő tekintheti meg egy jelentést, amely a HDFS állapot részleteit jeleníti meg:

```
hdfs dfsadmin -D 'fs.default.name=hdfs://mycluster/' -report
```

Ez a parancs eredménye a következő, ahol a blokkok replikálva vannak a várt mértékben kifogástalan fürtön:

![A biztonságos mód kikapcsolása](./media/hdinsight-scaling-best-practices/report.png)

HDFS támogatja a `fsck` parancs futtatásával ellenőrizze az inkonzisztenciákat különböző fájlok, például hiányzó fájlra vagy under-replikált blokkok blokkolja. Futtatásához a `fsck` parancs a `scratchdir` (ideiglenes ideiglenes lemez) fájlok:

```
hdfs fsck -D 'fs.default.name=hdfs://mycluster/' /tmp/hive/hive
```

HDFS kifogástalan fájlrendszeren nem under-replikált blokkolja a végrehajtásakor a következőhöz hasonló kimenetnek jelenik meg:

```
Connecting to namenode via http://hn0-scalin.name.bx.internal.cloudapp.net:30070/fsck?ugi=sshuser&path=%2Ftmp%2Fhive%2Fhive
FSCK started by sshuser (auth:SIMPLE) from /10.0.0.21 for path /tmp/hive/hive at Thu Jul 06 20:07:01 UTC 2017
..Status: HEALTHY
 Total size:    53 B
 Total dirs:    5
 Total files:   2
 Total symlinks:                0 (Files currently being written: 2)
 Total blocks (validated):      2 (avg. block size 26 B)
 Minimally replicated blocks:   2 (100.0 %)
 Over-replicated blocks:        0 (0.0 %)
 Under-replicated blocks:       0 (0.0 %)
 Mis-replicated blocks:         0 (0.0 %)
 Default replication factor:    3
 Average block replication:     3.0
 Corrupt blocks:                0
 Missing replicas:              0 (0.0 %)
 Number of data-nodes:          4
 Number of racks:               1
FSCK ended at Thu Jul 06 20:07:01 UTC 2017 in 3 milliseconds


The filesystem under path '/tmp/hive/hive' is HEALTHY
```

Ezzel szemben, ha a `fsck` parancs végrehajtása néhány under-replikált blokkok egy HDFS fájlrendszer, a kimenete az alábbihoz hasonló:

```
Connecting to namenode via http://hn0-scalin.name.bx.internal.cloudapp.net:30070/fsck?ugi=sshuser&path=%2Ftmp%2Fhive%2Fhive
FSCK started by sshuser (auth:SIMPLE) from /10.0.0.21 for path /tmp/hive/hive at Thu Jul 06 20:13:58 UTC 2017
.
/tmp/hive/hive/4f3f4253-e6d0-42ac-88bc-90f0ea03602c/inuse.info:  Under replicated BP-1867508080-10.0.0.21-1499348422953:blk_1073741826_1002. Target Replicas is 3 but found 1 live replica(s), 0 decommissioned replica(s) and 0 decommissioning replica(s).
.
/tmp/hive/hive/e7c03964-ff3a-4ee1-aa3c-90637a1f4591/inuse.info: CORRUPT blockpool BP-1867508080-10.0.0.21-1499348422953 block blk_1073741825

/tmp/hive/hive/e7c03964-ff3a-4ee1-aa3c-90637a1f4591/inuse.info: MISSING 1 blocks of total size 26 B.Status: CORRUPT
 Total size:    53 B
 Total dirs:    5
 Total files:   2
 Total symlinks:                0 (Files currently being written: 2)
 Total blocks (validated):      2 (avg. block size 26 B)
  ********************************
  UNDER MIN REPL'D BLOCKS:      1 (50.0 %)
  dfs.namenode.replication.min: 1
  CORRUPT FILES:        1
  MISSING BLOCKS:       1
  MISSING SIZE:         26 B
  CORRUPT BLOCKS:       1
  ********************************
 Minimally replicated blocks:   1 (50.0 %)
 Over-replicated blocks:        0 (0.0 %)
 Under-replicated blocks:       1 (50.0 %)
 Mis-replicated blocks:         0 (0.0 %)
 Default replication factor:    3
 Average block replication:     0.5
 Corrupt blocks:                1
 Missing replicas:              2 (33.333332 %)
 Number of data-nodes:          1
 Number of racks:               1
FSCK ended at Thu Jul 06 20:13:58 UTC 2017 in 28 milliseconds


The filesystem under path '/tmp/hive/hive' is CORRUPT
```

Megtekintheti a HDFS állapot Ambari felhasználói felületén; ehhez válassza a **HDFS** a bal oldalon, vagy a szolgáltatás `https://<HDInsightClusterName>.azurehdinsight.net/#/main/services/HDFS/summary`.

![Ambari HDFS állapota](./media/hdinsight-scaling-best-practices/ambari-hdfs.png)

Az aktív vagy készenléti állapotban lévő NameNodes a is egy vagy több kritikus hiba jelenhet meg. A NameNode blokkok állapotának megtekintéséhez válassza ki a NameNode hivatkozás a riasztás mellett.

![NameNode blokkok állapota](./media/hdinsight-scaling-best-practices/ambari-hdfs-crit.png)

Karbantartása az ideiglenes fájlok, amely a blokk replikációs hibák, az egyes átjárócsomópont SSH eltávolítja, és futtassa a következő parancsot:

```
hadoop fs -rm -r -skipTrash hdfs://mycluster/tmp/hive/
```

> [!NOTE]
> Ez a parancs meghibásodásához vezethet a Hive, ha egyes feladatok továbbra is futnak.

### <a name="how-to-prevent-hdinsight-from-getting-stuck-in-safe-mode-due-to-under-replicated-blocks"></a>HDInsight megakadályozása első akadt miatt under-replikált blokkok csökkentett módban

Megakadályozhatja, hogy a HDInsight a csökkentett módban hagyja számos módja van:

* HDInsight le skálázás előtt állítsa le a Hive-feladatokat. Alternatív megoldásként ütemezni a skála folyamattal, amellyel elkerülhető a Hive-feladatok futó ütköző le.
* Manuálisan tisztítása struktúrájának teljesen új `tmp` directory fájlok HDFS-ben előtt skálázás.
* Csak csökkentheti a HDInsight három munkavégző csomópontokhoz, minimális. Kerülje a minimumérték megegyezik egy feldolgozó csomópont lesz.
* A paranccsal csökkentett módban, hogy szükség esetén.

A következő szakaszok ezeket a lehetőségeket ismerteti.

#### <a name="stop-all-hive-jobs"></a>Állítsa le a Hive-feladatok

Skálázás egy munkavégző csomópont előtt állítsa le a Hive-feladatokat. Ha a számítási feladatok van ütemezve, futtatja a lefelé méretezéshez után Hive munkát.

Ezzel a megoldással tmp mappában (ha van ilyen) ideiglenes fájlok számának minimalizálása érdekében.

#### <a name="manually-clean-up-hives-scratch-files"></a>Manuálisan struktúrájának ideiglenes fájlok törlése

Ha a Hive elhagyta mögött ideiglenes fájlokat, majd manuálisan törölheti is ezeket a fájlokat le a skálázás előtt ne csökkentett módban.

1. Hive szolgáltatások leállítása és minden lekérdezések és feladat befejeződött.

2. Tartalmának megjelenítése a `hdfs://mycluster/tmp/hive/` könyvtárat, ha bármely fájl tartalmaz:

    ```
    hadoop fs -ls -R hdfs://mycluster/tmp/hive/hive
    ```
    
    Íme egy minta kimenet fájlok létezik:

    ```
    sshuser@hn0-scalin:~$ hadoop fs -ls -R hdfs://mycluster/tmp/hive/hive
    drwx------   - hive hdfs          0 2017-07-06 13:40 hdfs://mycluster/tmp/hive/hive/4f3f4253-e6d0-42ac-88bc-90f0ea03602c
    drwx------   - hive hdfs          0 2017-07-06 13:40 hdfs://mycluster/tmp/hive/hive/4f3f4253-e6d0-42ac-88bc-90f0ea03602c/_tmp_space.db
    -rw-r--r--   3 hive hdfs         27 2017-07-06 13:40 hdfs://mycluster/tmp/hive/hive/4f3f4253-e6d0-42ac-88bc-90f0ea03602c/inuse.info
    -rw-r--r--   3 hive hdfs          0 2017-07-06 13:40 hdfs://mycluster/tmp/hive/hive/4f3f4253-e6d0-42ac-88bc-90f0ea03602c/inuse.lck
    drwx------   - hive hdfs          0 2017-07-06 20:30 hdfs://mycluster/tmp/hive/hive/c108f1c2-453e-400f-ac3e-e3a9b0d22699
    -rw-r--r--   3 hive hdfs         26 2017-07-06 20:30 hdfs://mycluster/tmp/hive/hive/c108f1c2-453e-400f-ac3e-e3a9b0d22699/inuse.info
    ```

3. Ha ismeri a Hive ezekkel a fájlokkal történik, akkor távolítsa el őket. Győződjön meg arról, hogy, hogy a struktúra nem rendelkezik a Yarn erőforrás-kezelő felhasználói felületén lap keresésével futó lekérdezéseket.

    A parancssor például fájlok eltávolítása a HDFS:

    ```
    hadoop fs -rm -r -skipTrash hdfs://mycluster/tmp/hive/
    ```
    
#### <a name="scale--hdinsight-to-three-worker-nodes"></a>Skála HDInsight három munkavégző csomópontokhoz

Ha első elakadt a csökkentett üzemmódban állandó probléma, és az előző lépések opció nem, akkor érdemes lehet a probléma elkerüléséhez három munkavégző csomópontokhoz le csak skálázással. Ez nem lehet optimális költség-korlátok, egy csomópont méretezhetők képest miatt. Azonban csak egy feldolgozó csomópont, a HDFS nem garantálja, az adatok három replikáinak érhetők el a fürthöz.

#### <a name="run-the-command-to-leave-safe-mode"></a>Futtassa a parancsot, hogy a biztonságos mód

Az utolsó lehetőség a HDFS megadja a biztonságos mód a ritka esetben figyelendő, akkor hajtsa végre a hagyja a csökkentett mód parancsot. Miután megállapította, hogy a OK HDFS került csökkentett módban az az oka, hogy az éppen under-replikált fájlok, hajtsa végre a következő parancsot, hogy a biztonságos mód:

* HDInsight Linux rendszeren:

    ```bash
    hdfs dfsadmin -D 'fs.default.name=hdfs://mycluster/' -safemode leave
    ```
    
* HDInsight Windows rendszeren:

    ```bash
    hdfs dfsadmin -fs hdfs://headnodehost:9000 -safemode leave
    ```
    
## <a name="next-steps"></a>További lépések

* [Az Azure HDInsight bemutatása](hadoop/apache-hadoop-introduction.md)
* [Skála fürtök](hdinsight-administer-use-portal-linux.md#scale-clusters)
* [A HDInsight-fürtök kezelése az Ambari webes felhasználói felület használatával](hdinsight-hadoop-manage-ambari.md)
