---
title: Fürt méretek – Azure HDInsight méretezése
description: Méretezheti a számítási feladat egy HDInsight-fürtön.
services: hdinsight
author: ashishthaps
editor: jasonwhowell
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 02/02/2018
ms.author: ashish
ms.openlocfilehash: 2f1de608a8273d7be50e4ad432ab72052fc0e228
ms.sourcegitcommit: 1f0587f29dc1e5aef1502f4f15d5a2079d7683e9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/07/2018
ms.locfileid: "39596840"
---
# <a name="scale-hdinsight-clusters"></a>HDInsight-fürtök méretezése

HDInsight biztosítja a rugalmasságot felkínálva a lehetőséget az növelheti vagy csökkentheti a feldolgozó csomópontok a fürtben. Ez lehetővé teszi, hogy a fürt óra múlva, vagy a hétvégeken, csökkenthető és felfüggeszthető kibontásához során üzleti megnövekedett igényeket kell kielégíteni.

Például ha van néhány kötegelt feldolgozás naponta egyszer vagy egy hónapban egyszer történik, a HDInsight-fürt is vertikálisan fel néhány percet, hogy az ütemezett esemény előtt, a memória és CPU-számítási teljesítményt. Skálázás a PowerShell-parancsmaggal automatizálható [ `Set–AzureRmHDInsightClusterSize` ](hdinsight-administer-use-powershell.md#scale-clusters).  Később Miután befejeződött a feldolgozás, és a használati újra leáll, vertikális kevesebb munkavégző csomópontot a HDInsight-fürt.

* A fürt méretezése [PowerShell](hdinsight-administer-use-powershell.md):

    ```powershell
    Set-AzureRmHDInsightClusterSize -ClusterName <Cluster Name> -TargetInstanceCount <NewSize>
    ```
    
* A fürt méretezése a [Azure CLI-vel](hdinsight-administer-use-command-line.md):

    ```
    azure hdinsight cluster resize [options] <clusterName> <Target Instance Count>
    ```
    
* Keresztül a fürtök skálázásának a [az Azure portal](https://portal.azure.com), a HDInsight-fürt panel megnyitásához, jelölje ki **fürt méretezése** a bal oldali menüben, majd a fürthöz méretezés panelen írja be a munkavégző csomópontok számát, és Válassza a mentés.

    ![Fürt méretezése](./media/hdinsight-scaling-best-practices/scale-cluster-blade.png)

Ezen módszerek bármelyikével, skálázhatja a HDInsight-fürt felfelé vagy lefelé percen belül.

## <a name="scaling-impacts-on-running-jobs"></a>Hatással van a futó feladatok méretezése

Ha Ön **hozzáadása** csomópontok a futó HDInsight-fürthöz, a folyamatban lévő vagy futó feladatok nem érinti. Emellett új feladatok biztonságosan küldheti a skálázási művelet végrehajtása közben. Ha a méretezési műveletek bármilyen okból nem sikerül, a hiba szabályosan történik, a fürt és a egy működési állapotot.

Azonban ha meg van vertikális leskálázást a fürt a **eltávolítása** csomópontok, a folyamatban lévő vagy futó feladatok meghiúsulnak, a skálázási művelet befejeződése után. Ez a hiba okozza a szolgáltatások újraindítása a folyamat során.

A probléma megoldására, várja meg, a feladat befejeződését, mielőtt a fürt vertikális, manuálisan a feladatok leállítása vagy küldje el újra a feladatok, miután a skálázási művelet lezárult.

Függőben lévő és a futó feladatok megtekintéséhez használhatja a YARN ResourceManager Felületet, a következő lépéseket:

1. Jelentkezzen be az [Azure portálra](https://portal.azure.com).
2. A bal oldali menüben válassza ki a **Tallózás**válassza **HDInsight-fürtök**, majd válassza ki a fürtöt.
3. A HDInsight-fürt panelen válassza ki a **irányítópult** az Ambari felhasználói felületének megnyitásához a felső menüben. Adja meg a fürt bejelentkezési hitelesítő adatait.
4. Kattintson a **YARN** a bal oldali menüben a szolgáltatások listájában. A YARN lapon válassza ki a **Gyorshivatkozások** és az aktív átjárócsomóponthoz fölé, majd kattintson **ResourceManager felhasználói felülete**.

    ![Erőforrás-kezelő felhasználói felületén](./media/hdinsight-scaling-best-practices/resourcemanager-ui.png)

Előfordulhat, hogy közvetlenül hozzáférhet az erőforrás-kezelő felhasználói felület `https://<HDInsightClusterName>.azurehdinsight.net/yarnui/hn/cluster`.

Feladatok, valamint a jelenlegi állapotuk listáját láthatja. A képernyőképen van jelenleg fut egy feladat:

![Erőforrás-kezelő felhasználói felületén alkalmazások](./media/hdinsight-scaling-best-practices/resourcemanager-ui-applications.png)

Manuálisan le a futó alkalmazást, az SSH rendszerhéjból hajtsa végre a következő parancsot:

```bash
yarn application -kill <application_id>
```

Példa:

```bash
yarn application -kill "application_1499348398273_0003"
```

## <a name="rebalancing-an-hbase-cluster"></a>Az újraegyensúlyozás HBase-fürt

Régiókiszolgálók automatikusan kiegyensúlyozott vannak a skálázási művelet befejezése után néhány percen belül. Manuálisan elosztása régióbeli kiszolgálók, használja az alábbi lépéseket:

1. A HDInsight-fürthöz SSH használatával csatlakozhat. További információ: [Az SSH használata HDInsighttal](hdinsight-hadoop-linux-use-ssh-unix.md).

2. Indítsa el a HBase rendszerhéj:

        hbase shell

3. A következő parancs használatával manuálisan elosztása a régióbeli kiszolgálók:

        balancer

## <a name="scale-down-implications"></a>Vertikális leskálázás következmények

Ahogy korábban említettük, a folyamatban lévő vagy futó feladatok egy vertikális leskálázást a művelet befejezése után megszűnik. Vannak azonban egyéb lehetséges következményeiről skálázás lefelé, amely akkor fordulhat elő.

## <a name="hdinsight-name-node-stays-in-safe-mode-after-scaling-down"></a>HDInsight neve csomópont után vertikális leskálázást csökkentett üzemmódban marad.

![Fürt méretezése](./media/hdinsight-scaling-best-practices/scale-cluster.png)

Ha a fürt egy feldolgozó csomópont, a minimális le az előző képen látható módon csökkenti méretét, HDFS előfordulhat, hogy letöltés állapottal csökkentett módban, ha a munkavégző csomópontok javítása miatt, vagy közvetlenül a skálázási művelet után indulnak újra.

Az elsődleges ennek oka az, hogy a Hive néhány használja `scratchdir` fájlokat, és alapértelmezés szerint három replika készül, mindegyik blokk vár, de csak egy replika lehetséges, ha a minimális egy munkavégző csomópont vertikális leskálázás. Ennek következtében a lévő fájlokat a `scratchdir` válnak *under-replikált*. Emiatt a HDFS csökkentett módban marad, a szolgáltatások a skálázási művelet utáni újraindításakor.

Ha egy vertikális leskálázási kísérlet történik, HDInsight az Ambari felügyeleti felületek, először leszerelni a extra nemkívánatos feldolgozó csomópontokat, amely a HDFS-blokkok replikálja más online feldolgozó csomópontokat, utána pedig biztonságosan a fürt megbízhatóak. HDFS csökkentett módban hiányzóra változik a karbantartási időszak alatt, és állapotba kerülnek, a méretezés befejezése után. Ezen a ponton, hogy HDFS csökkentett módban letöltés állapottal.

HDFS van konfigurálva egy `dfs.replication` beállítása a 3-ból. Így az ideiglenes fájlok azokat az adatblokkokat under-replikált, amikor legalább három feldolgozó csomópont online, mert nincsenek elérhető minden blokk nem várt három másolata.

A paranccsal HDFS hozza ki a csökkentett mód hajthat végre. Például ha tudja, hogy csak az az oka csökkentett módban van, mert az ideiglenes fájlok under-replikált, majd biztonságosan hagyhatja csökkentett módban. Ennek az oka under-replikált fájlok Hive ideiglenes ideiglenes fájlok.

```bash
hdfs dfsadmin -D 'fs.default.name=hdfs://mycluster/' -safemode leave
```

A csökkentett mód elhagyása, után manuálisan eltávolíthatja az ideiglenes fájlokat, vagy várja meg, Hive idővel törölni őket automatikusan.

### <a name="example-errors-when-safe-mode-is-turned-on"></a>Példahibák, amikor a csökkentett mód be van kapcsolva

* H070 nem Hive-munkamenetet. org.apache.hadoop.ipc.RemoteException(org.apache.hadoop.ipc.RetriableException): org.apache.hadoop.hdfs.server.namenode.SafeModeException: **könyvtár nem hozható létre**  /tmp/hive/hive / 819c215c - 6d 87-4311 – 97c 8-4f0b9d2adcf0. **Csomópont neve csökkentett módban van**. A jelzett blokkokat 75 kell elérni a küszöbérték teljes blokkok 87 0.9900 további 12 blokkokat. A 10 élő adatcsomópontok száma elérte a minimális száma 0. A csökkentett mód ki lesz kapcsolva automatikusan után a rendszer elérte a küszöbértékeket.

* Adatbázisok H100 beküldése sikertelen utasítás megjelenítése: org.apache.thrift.transport.TTransportException: org.apache.http.conn.HttpHostConnectException: [hn0-clustername.servername hn0-clustername.servername.internal.cloudapp.net:10001 csatlakozni . Internal.cloudapp.NET/1.1.1.1] nem sikerült: **kapcsolat elutasítva**

* H020 nem tudott kapcsolatot hn0-hdisrv.servername.bx.internal.cloudapp .net-re: 10001: org.apache.thrift.transport.TTransportException: nem sikerült létrehozni a http protokollú kapcsolódáshoz http://hn0-hdisrv.servername.bx.internal.cloudapp.net:10001/. org.apache.http.conn.HttpHostConnectException: hn0-hdisrv.servername.bx.internal.cloudapp.net:10001 [hn0-hdisrv.servername.bx.internal.cloudapp.net/10.0.0.28] nem sikerült csatlakozni: Kapcsolat elutasítva: org.apache.thrift.transport.TTransportException: nem sikerült létrehozni a http protokollú kapcsolódáshoz http://hn0-hdisrv.servername.bx.internal.cloudapp.net:10001/. org.apache.http.conn.HttpHostConnectException: hn0-hdisrv.servername.bx.internal.cloudapp.net:10001 [hn0-hdisrv.servername.bx.internal.cloudapp.net/10.0.0.28] nem sikerült csatlakozni: **kapcsolat elutasítva**

* A Hive-naplókban található: [fő] WARN: kiszolgáló. Hiveserver2-n (HiveServer2.java:startHiveServer2(442)) – indítása a hiveserver2-n keresztül kísérlet 21., a hiba újra fog próbálkozni 60 másodperc java.lang.RuntimeException: engedélyezési házirend alkalmazása a hive-konfigurációs hiba: org.apache.hadoop.ipc.RemoteException () org.apache.hadoop.ipc.RetriableException): org.apache.hadoop.hdfs.server.namenode.SafeModeException: **könyvtár nem hozható létre** /tmp/hive/hive/70a42b8a-9437-466e-acbe-da90b1614374. **Csomópont neve csökkentett módban van**.
    A jelzett blokkokat 0 van szüksége további 9 blokkok elérni a teljes blokkok 9 0.9900 küszöbértéket.
    A 10 élő adatcsomópontok száma elérte a minimális száma 0. **A csökkentett mód ki lesz kapcsolva automatikusan után a rendszer elérte a küszöbértékek**.
    at org.apache.hadoop.hdfs.server.namenode.FSNamesystem.checkNameNodeSafeMode(FSNamesystem.java:1324)

A név csomópont naplóinak áttekintheti a `/var/log/hadoop/hdfs/` mappa közel az idő, amikor a fürt méreteztünk, hogy mikor került a csökkentett mód. A naplófájlok elnevezése `Hadoop-hdfs-namenode-hn0-clustername.*`.

A korábbi hibák okozza-e, hogy Hive attól függ, hdfs ideiglenes fájlok lekérdezések futtatásakor. HDFS biztonságos módra vált, ha a Hive nem futtatható lekérdezéseket, mivel a HDFS nem tud írni. HDFS-ben az ideiglenes fájlokat a helyi meghajtó csatlakoztatva van, az egyes munkavégző csomópont azon virtuális gépeit, és más feldolgozó csomópontok minimális három replikapéldány, többek között replikált mappában találhatók.

A `hive.exec.scratchdir` van konfigurálva a Hive paraméter `/etc/hive/conf/hive-site.xml`:

```xml
<property>
    <name>hive.exec.scratchdir</name>
    <value>hdfs://mycluster/tmp/hive</value>
</property>
```

### <a name="view-the-health-and-state-of-your-hdfs-file-system"></a>Állapot és a HDFS-fájlrendszer állapotának megtekintése

A vannak-e csomópontok csökkentett módban neve csomópontokról állapotjelentés tekintheti meg. Tekintse meg a jelentést, az SSH-t minden egyes átjárócsomóponthoz, és futtassa a következő parancsot:

```
hdfs dfsadmin -D 'fs.default.name=hdfs://mycluster/' -safemode get
```

![Biztonságos mód kikapcsolása](./media/hdinsight-scaling-best-practices/safe-mode-off.png)

> [!NOTE]
> A `-D` kapcsoló szükség, mert a rendszer az alapértelmezett fájlrendszert a HDInsight az Azure Storage vagy az Azure Data Lake Store. `-D` Itt adhatja meg, hogy a parancsok végrehajtása a helyi HDFS-fájlrendszer ellen.

Ezután tekintheti meg egy jelentést, amely a HDFS-állapot részleteit jeleníti meg:

```
hdfs dfsadmin -D 'fs.default.name=hdfs://mycluster/' -report
```

Ez a parancs eredménye a következő, ahol minden blokkok replikálja a rendszer a várt párhuzamossági kifogástalan fürtön:

![Biztonságos mód kikapcsolása](./media/hdinsight-scaling-best-practices/report.png)

Támogatja a HDFS a `fsck` paranccsal ellenőrizheti a sikertelenség a különféle fájlok, például hiányzó blokkolja egy fájlhoz vagy under-replikált blokkokat. Futtatásához a `fsck` parancsot a `scratchdir` (ideiglenes ideiglenes lemez) fájlok:

```
hdfs fsck -D 'fs.default.name=hdfs://mycluster/' /tmp/hive/hive
```

Nincs under-replikált blokkokkal kifogástalan HDFS fájlrendszerben végrehajtásakor az alábbihoz hasonló kimenet jelenik meg:

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

Ezzel szemben, ha a `fsck` parancs végrehajtása néhány under-replikált blokkokkal HDFS-fájlrendszer, a kimenete az alábbihoz hasonló lesz:

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

Is megtekintheti a HDFS állapota az Ambari felhasználói felületén válassza a **HDFS** szolgáltatást, a bal oldalon, vagy a `https://<HDInsightClusterName>.azurehdinsight.net/#/main/services/HDFS/summary`.

![Az Ambari HDFS állapota](./media/hdinsight-scaling-best-practices/ambari-hdfs.png)

Az aktív vagy készenléti NameNodes az egy vagy több kritikus hibák is megjelenhetnek. A NameNode blokk állapotának megtekintéséhez válassza ki a NameNode hivatkozás a riasztás melletti.

![NameNode blokkok állapota](./media/hdinsight-scaling-best-practices/ambari-hdfs-crit.png)

Távolítsa el az ideiglenes fájlok, amely eltávolítja a blokk replikációs hibák, SSH-t minden egyes átjárócsomóponthoz, és futtassa a következő parancsot:

```
hadoop fs -rm -r -skipTrash hdfs://mycluster/tmp/hive/
```

> [!NOTE]
> Ez a parancs tönkretehetik a Hive, ha egyes feladatok továbbra is futnak.

### <a name="how-to-prevent-hdinsight-from-getting-stuck-in-safe-mode-due-to-under-replicated-blocks"></a>Csökkentett üzemmódban miatt under-replikált blokkok első elakadt a HDInsight megakadályozása

Megakadályozza, hogy a HDInsight marad, hogy a csökkentett módban számos módja van:

* Hive-feladatok leállítása előtt HDInsight leskálázást. Azt is megteheti ütemezheti a méretezési csoport a folyamat Hive-feladatok futtatása az ütköző elkerülése érdekében.
* Hive a teljesen új saját kezűleg tisztítása `tmp` directory fájlok hdfs előtt vertikális leskálázást.
* Csak vertikális leskálázás HDInsight három munkavégző csomópontokhoz, minimális. Elkerülése érdekében, akár egy munkavégző csomópont is történik.
* Futtassa a parancsot csökkentett módban, hagyja üresen, ha szükséges.

A következő szakaszok ismertetik ezeket a beállításokat.

#### <a name="stop-all-hive-jobs"></a>Az összes Hive-feladatok leállítása

Az összes Hive-feladatok leállítása előtt egy munkavégző csomópont leskálázást. Ha a számítási feladat ütemezve van, futtatja a skálázási után Hive munkát.

Ez segít a tmp mappába (ha vannak) az ideiglenes fájlok számának csökkentése érdekében.

#### <a name="manually-clean-up-hives-scratch-files"></a>Hive a ideiglenes fájlok manuális törlése

Ha a Hive távozott mögött ideiglenes fájlok, majd manuálisan távolíthatja el ezeket a fájlokat előtt való vertikális leskálázást elkerülése érdekében csökkentett módban.

1. Hive-szolgáltatások leállítása és lekérdezések és a feladat befejeződött.

2. Tartalmának a `hdfs://mycluster/tmp/hive/` könyvtárat, ha bármelyik fájl tartalmaz:

    ```
    hadoop fs -ls -R hdfs://mycluster/tmp/hive/hive
    ```
    
    Itt látható egy mintakimenet, ha a fájlok léteznek:

    ```
    sshuser@hn0-scalin:~$ hadoop fs -ls -R hdfs://mycluster/tmp/hive/hive
    drwx------   - hive hdfs          0 2017-07-06 13:40 hdfs://mycluster/tmp/hive/hive/4f3f4253-e6d0-42ac-88bc-90f0ea03602c
    drwx------   - hive hdfs          0 2017-07-06 13:40 hdfs://mycluster/tmp/hive/hive/4f3f4253-e6d0-42ac-88bc-90f0ea03602c/_tmp_space.db
    -rw-r--r--   3 hive hdfs         27 2017-07-06 13:40 hdfs://mycluster/tmp/hive/hive/4f3f4253-e6d0-42ac-88bc-90f0ea03602c/inuse.info
    -rw-r--r--   3 hive hdfs          0 2017-07-06 13:40 hdfs://mycluster/tmp/hive/hive/4f3f4253-e6d0-42ac-88bc-90f0ea03602c/inuse.lck
    drwx------   - hive hdfs          0 2017-07-06 20:30 hdfs://mycluster/tmp/hive/hive/c108f1c2-453e-400f-ac3e-e3a9b0d22699
    -rw-r--r--   3 hive hdfs         26 2017-07-06 20:30 hdfs://mycluster/tmp/hive/hive/c108f1c2-453e-400f-ac3e-e3a9b0d22699/inuse.info
    ```

3. Ha ismeri ezeket a fájlokat a Hive történik, akkor távolítsa el őket. Győződjön meg arról, hogy a Hive nem rendelkezik a Yarn ResourceManager felhasználói felülete lap alapján futó lekérdezéseket.

    Például szolgáló parancssor fájlok eltávolítása a HDFS-ből:

    ```
    hadoop fs -rm -r -skipTrash hdfs://mycluster/tmp/hive/
    ```
    
#### <a name="scale--hdinsight-to-three-worker-nodes"></a>Méretezési csoport HDInsight három munkavégző csomópontokhoz

Ha az első elakadt csökkentett módban egy állandó a probléma, és az előző lépések nem használhatók lehetőségeket, akkor csak három feldolgozó csomópontot lefelé méretezés a probléma elkerülése érdekében érdemes. Ez nem feltétlenül optimális, költség korlátozott, az egyik csomóponton leskálázást képest miatt. Azonban csak egy feldolgozó csomóponttal, HDFS nem garantálja, az adatok három replika érhetők el a fürtöt.

#### <a name="run-the-command-to-leave-safe-mode"></a>Futtassa a parancsot, hogy a csökkentett mód

Az utolsó lehetőség a HDFS megadja a csökkentett mód a ritka esetben kapcsolatos, akkor hagyja üresen a csökkentett mód parancs hajtható végre. Miután eldöntötte, hogy az OK HDFS kategóriájának csökkentett módban miatt under-replikált folyamatban a Hive-fájlokat, hajtsa végre a következő parancsot, hogy a csökkentett mód:

* HDInsight Linux rendszeren:

    ```bash
    hdfs dfsadmin -D 'fs.default.name=hdfs://mycluster/' -safemode leave
    ```
    
* A Windows HDInsight:

    ```bash
    hdfs dfsadmin -fs hdfs://headnodehost:9000 -safemode leave
    ```
    
## <a name="next-steps"></a>További lépések

* [Az Azure HDInsight bemutatása](hadoop/apache-hadoop-introduction.md)
* [Fürtök méretezése](hdinsight-administer-use-portal-linux.md#scale-clusters)
* [HDInsight-fürtök kezelése az Ambari webes felhasználói felület használatával](hdinsight-hadoop-manage-ambari.md)
