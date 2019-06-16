---
title: Fürt méretek – Azure HDInsight méretezése
description: Egy Azure HDInsight-fürtön rugalmasan a számítási feladathoz megfelelő méretezhető.
author: ashishthaps
ms.author: ashish
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 06/10/2019
ms.openlocfilehash: b85277a4238351b6448c2cf29676ae3d8c118385
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "67077203"
---
# <a name="scale-hdinsight-clusters"></a>HDInsight-fürtök méretezése

HDInsight biztosítja a rugalmasságot felkínálva a lehetőséget az növelheti vagy csökkentheti a feldolgozó csomópontok a fürtben. Ez rugalmasságot lehetővé teszi egy fürt óra múlva, vagy a hétvégeken, csökkenthető és felfüggeszthető kibontásához során üzleti megnövekedett igényeket kell kielégíteni.

Ha időszakos kötegelt feldolgozás, a HDInsight-fürt is vertikálisan fel néhány percet, hogy a művelet előtt, hogy a fürt elegendő memória- és CPU-teljesítmény.  Később Miután befejeződött a feldolgozás, és a használati újra leáll, vertikális kevesebb munkavégző csomópontot a HDInsight-fürt.

Fürt horizontális fel-manuálisan az alábbi módszerek egyikének használatával, vagy használjon [automatikus skálázási](hdinsight-autoscale-clusters.md) beállítások, a rendszer automatikusan méretezése felfelé és lefelé a CPU, memória és más metrikákkal.

> [!NOTE]  
> Csak 3.1.3 verziójú HDInsight-fürtök vagy újabb verziója támogatott. Ha biztos benne, hogy a fürt verziója, a Tulajdonságok lapon ellenőrizheti.

## <a name="utilities-to-scale-clusters"></a>Fürtök méretezése segédprogramok

A Microsoft skálázható fürtök az alábbi segédprogramokat biztosít:

|Segédprogram | Leírás|
|---|---|
|[PowerShell Az](https://docs.microsoft.com/powershell/azure)|[Set-AzHDInsightClusterSize](https://docs.microsoft.com/powershell/module/az.hdinsight/set-azhdinsightclustersize) - ClusterName \<fürt neve > - TargetInstanceCount \<NewSize >|
|[PowerShell AzureRM](https://docs.microsoft.com/powershell/azure/azurerm) |[Set-AzureRmHDInsightClusterSize](https://docs.microsoft.com/powershell/module/azurerm.hdinsight/set-azurermhdinsightclustersize) - ClusterName \<fürt neve > - TargetInstanceCount \<NewSize >|
|[Azure CLI](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest)| [az hdinsight átméretezése](https://docs.microsoft.com/cli/azure/hdinsight?view=azure-cli-latest#az-hdinsight-resize) --erőforráscsoport \<erőforráscsoport >--neve \<fürt neve > – cél példányszám \<NewSize >|
|[Azure CLI](hdinsight-administer-use-command-line.md)|az Azure hdinsight-fürt átméretezése \<clusterName > \<cél példányok száma > |
|[Azure Portal](https://portal.azure.com)|A HDInsight-fürt panel megnyitásához, jelölje be **fürtméret** a bal oldali menüben, majd a fürt méretének panelen írja be a munkavégző csomópontok számát, és válassza a mentés.|  

![Fürt méretezése](./media/hdinsight-scaling-best-practices/scale-cluster-blade.png)

Ezen módszerek bármelyikével, skálázhatja a HDInsight-fürt felfelé vagy lefelé percen belül.

> [!IMPORTANT]  
> * Az Azure klasszikus parancssori felület elavult, és csak a klasszikus üzemi modell használható. Minden más üzemelő példánya esetében használja a [Azure CLI-vel](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest).  
> * Az PowerShell AzureRM-modul elavult.  Használja a [Az modul](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-1.4.0) , amikor csak lehetséges.

## <a name="impact-of-scaling-operations"></a>Műveletek hatásainak

Ha Ön **hozzáadása** nem lesz hatással a futó HDInsight-fürthöz (vertikális felskálázási), a folyamatban lévő vagy futó feladatok csomópontok. Új feladatok biztonságosan küldheti a skálázási művelet végrehajtása közben. A skálázási művelet bármilyen okból meghiúsul, ha a hibát hagyja a egy működési állapotot a fürt automatikusan elvégezhető.

Ha Ön **eltávolítása** csomópont (vertikális leskálázási), minden folyamatban lévő vagy futó feladatok lesz meghibásodik, a skálázási művelet befejeződése után. Ez a hiba okozza a szolgáltatások újraindítása a méretezés során. Is annak a kockázata, hogy a fürt beszerezheti a elakadt a csökkentett mód a Manuális méretezés művelet során.

Az adatcsomópontok száma módosításának hatása az egyes támogatott a HDInsight-fürt eltérő:

* Apache Hadoop

    Zökkenőmentesen lehet növelni egy Hadoop-fürtöt, amely a folyamatban lévő vagy futó feladatok befolyásolása nélkül fut-e a munkavégző csomópontok számát. Új feladatok is lehet beküldeni, amíg a művelet folyamatban van. A skálázási művelet hibák szabályosan kezeli, úgy, hogy a fürt minden esetben működőképes állapotban marad.

    Ha egy Hadoop-fürtöt az adatcsomópontok száma csökkentésével vertikálisan leskálázni, a fürtben a szolgáltatások újra lesz indítva. Ez a viselkedés hatására összes futó és a függőben lévő feladatok meghiúsulhatnak, a skálázási művelet befejezése után. A feladatok újból elküldheti, azonban a művelet befejeződése után.

* Apache HBase

    Zökkenőmentesen adja hozzá vagy távolíthat el csomópontokat a HBase-fürt futás közben. Regionális kiszolgáló automatikusan kiegyensúlyozott vannak a skálázási művelet befejezése néhány percen belül. Azonban, manuálisan is eloszthatja a regionális kiszolgálók jelentkezik be a fürt átjárócsomópontjával, és a egy parancssori ablakot a következő parancsokat futtatni:

    ```bash
    pushd %HBASE_HOME%\bin
    hbase shell
    balancer
    ```

    A HBase-rendszerhéj használata további információkért lásd: [Ismerkedés a HDInsight Apache HBase-példát](hbase/apache-hbase-tutorial-get-started-linux.md).

* Apache Storm

    Zökkenőmentesen adja hozzá vagy távolít el a Storm-fürt adatcsomópontok futás közben. Azonban a skálázási művelet a sikeres telepítést követően szüksége lesz a topológia újraegyensúlyozására.

    Az újraegyensúlyozás két módon is elvégezhető:

  * A Storm webes felhasználói felületen
  * Parancssori felület (CLI) eszköz

    Tekintse meg a [Apache Storm-dokumentáció](https://storm.apache.org/documentation/Understanding-the-parallelism-of-a-Storm-topology.html) további részletekért.

    A Storm webes felhasználói felületen a HDInsight-fürtön érhető el:

    ![HDInsight Storm méretezési újraegyensúlyozási](./media/hdinsight-scaling-best-practices/hdinsight-portal-scale-cluster-storm-rebalance.png)

    Íme egy példa a Storm-topológia újraegyensúlyozására CLI-parancsot:

    ```cli
    ## Reconfigure the topology "mytopology" to use 5 worker processes,
    ## the spout "blue-spout" to use 3 executors, and
    ## the bolt "yellow-bolt" to use 10 executors
    $ storm rebalance mytopology -n 5 -e blue-spout=3 -e yellow-bolt=10
    ```

## <a name="how-to-safely-scale-down-a-cluster"></a>Biztonságosan le egy fürt méretezése

### <a name="scale-down-a-cluster-with-running-jobs"></a>Vertikális leskálázás egy fürtbe, a futó feladatok

Ne kelljen a futó feladatok során egy vertikális leskálázási művelet sikertelen, próbálja meg három dolgot:

1. Várjon, amíg a feladatok befejezését, mielőtt a fürt vertikális.
1. A feladatok manuálisan végén.
1. Küldje el újra a feladatok, miután a skálázási művelet lezárult.

Függőben lévő és a futó feladatok megtekintéséhez használhatja a YARN **erőforrás-kezelő felhasználói felületén**, ezeket a lépéseket követve:

1. Az a [az Azure portal](https://portal.azure.com/), válassza ki a fürtöt.  Lásd: [fürtök listázása és megjelenítése](./hdinsight-administer-use-portal-linux.md#showClusters) vonatkozó utasításokat. A fürt egy portál új lapon nyílik meg.
2. Lépjen a fő nézetből **fürt irányítópultjai** > **otthoni Ambari**. Adja meg a fürt hitelesítő adatait.
3. Az Ambari felhasználói felületén, válassza ki **YARN** a bal oldali menüben a szolgáltatások listájában.  
4. A YARN lapon válassza ki a **Gyorshivatkozások** és az aktív átjárócsomóponthoz fölé, majd válassza ki **ResourceManager felhasználói felülete**.

    ![ResourceManager UI](./media/hdinsight-scaling-best-practices/resourcemanager-ui.png)

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

### <a name="getting-stuck-in-safe-mode"></a>Első elakadt csökkentett módban

Egy fürt leskálázás, amikor HDInsight kezelőfelületén Apache Ambari először leszerelése a további feldolgozó csomópontokat, amely a HDFS-blokkok replikálni a többi online munkavégző csomópontokhoz. Ezt követően HDInsight biztonságosan arányosan növekszik a fürt csökken. HDFS csökkentett üzemmódban a méretezési művelet során kerül, és állapotba kerülnek, a méretezés befejezése után. Bizonyos esetekben azonban HDFS elakad csökkentett üzemmódban a méretezési művelet során fájl korrigáljuk blokkreplikációt miatt.

Alapértelmezés szerint a HDFS van konfigurálva egy `dfs.replication` beállítása a 3-ból, amely azt vezérli, hogy hány példányban minden blokk érhetők el. Egy fájl letiltása minden példányát tárolja a fürt egy másik csomóponton.

Amikor HDFS észleli, hogy a várt blokk példányszámot nem érhetők el, HDFS biztonságos módra vált, és Ambari riasztásokat állít elő. Ha a HDFS egy skálázási művelet biztonságos módra vált, de nem zárja be a csökkentett mód, mert a replikáció nem észleli a szükséges csomópontok számát, a fürt letöltés állapottal csökkentett módban.

### <a name="example-errors-when-safe-mode-is-turned-on"></a>Példahibák, amikor a csökkentett mód be van kapcsolva

```
org.apache.hadoop.hdfs.server.namenode.SafeModeException: Cannot create directory /tmp/hive/hive/819c215c-6d87-4311-97c8-4f0b9d2adcf0. Name node is in safe mode.
```

```
org.apache.http.conn.HttpHostConnectException: Connect to hn0-clustername.servername.internal.cloudapp.net:10001 [hn0-clustername.servername. internal.cloudapp.net/1.1.1.1] failed: Connection refused
```

A név csomópont naplóinak áttekintheti a `/var/log/hadoop/hdfs/` mappa közel az idő, amikor a fürt méreteztünk, hogy mikor került a csökkentett mód. A naplófájlok elnevezése `Hadoop-hdfs-namenode-hn0-clustername.*`.

A korábbi hibák okozza-e, hogy Hive attól függ, hdfs ideiglenes fájlok lekérdezések futtatásakor. HDFS biztonságos módra vált, ha a Hive nem futtatható lekérdezéseket, mivel a HDFS nem tud írni. HDFS-ben az ideiglenes fájlokat a helyi meghajtó csatlakoztatva van, az egyes munkavégző csomópont azon virtuális gépeit, és más feldolgozó csomópontok minimális három replikapéldány, többek között replikált mappában találhatók.

### <a name="how-to-prevent-hdinsight-from-getting-stuck-in-safe-mode"></a>Csökkentett üzemmódban első elakadt a HDInsight megakadályozása

Megakadályozza, hogy a HDInsight marad, hogy a csökkentett módban számos módja van:

* Hive-feladatok leállítása előtt HDInsight leskálázást. Azt is megteheti ütemezheti a méretezési csoport a folyamat Hive-feladatok futtatása az ütköző elkerülése érdekében.
* Hive a teljesen új saját kezűleg tisztítása `tmp` directory fájlok hdfs előtt vertikális leskálázást.
* Csak vertikális leskálázás HDInsight három munkavégző csomópontokhoz, minimális. Elkerülése érdekében, akár egy munkavégző csomópont is történik.
* Futtassa a parancsot csökkentett módban, hagyja üresen, ha szükséges.

A következő szakaszok ismertetik ezeket a beállításokat.

#### <a name="stop-all-hive-jobs"></a>Az összes Hive-feladatok leállítása

Az összes Hive-feladatok leállítása előtt egy munkavégző csomópont leskálázást. Ha a számítási feladat ütemezve van, futtatja a skálázási után Hive munkát.

A Hive-feladatok leállítása előtt, lehető legkisebbre csökkenti a tmp mappába ideiglenes fájlok száma (ha van).

#### <a name="manually-clean-up-hives-scratch-files"></a>Hive a ideiglenes fájlok manuális törlése

Ha a Hive távozott mögött ideiglenes fájlok, majd manuálisan távolíthatja el ezeket a fájlokat előtt való vertikális leskálázást elkerülése érdekében csökkentett módban.

1. Ellenőrizze, hogy melyik hely használja az ideiglenes fájlok Hive megnézzük a `hive.exec.scratchdir` konfigurációs tulajdonság. Ez a paraméter értéke belül `/etc/hive/conf/hive-site.xml`:

    ```xml
    <property>
        <name>hive.exec.scratchdir</name>
        <value>hdfs://mycluster/tmp/hive</value>
    </property>
    ```

1. Hive-szolgáltatások leállítása és lekérdezések és a feladat befejeződött.
2. A fent található az ideiglenes könyvtár tartalmának `hdfs://mycluster/tmp/hive/` , ha bármelyik fájl tartalmaz:

    ```bash
    hadoop fs -ls -R hdfs://mycluster/tmp/hive/hive
    ```

    Itt látható egy mintakimenet, ha a fájlok léteznek:

    ```output
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

    ```bash
    hadoop fs -rm -r -skipTrash hdfs://mycluster/tmp/hive/
    ```

#### <a name="scale-hdinsight-to-three-or-more-worker-nodes"></a>Méretezési csoport HDInsight három vagy több munkavégző csomópontokhoz

Ha a fürtök gyakran akkor, ha kevesebb mint három feldolgozó csomópontot lefelé méretezés elakadnak csökkentett módban, és az előző lépések nem működnek, majd elkerülheti a fog csökkentett mód érvényesítette tartja, hogy legalább három feldolgozó csomópontot a fürthöz.

Három feldolgozó csomópontot fenntartása költségesebb, mint a vertikális leskálázást csak egy munkavégző csomópont, de, megakadályozza a fürt első elakadt csökkentett módban.

#### <a name="run-the-command-to-leave-safe-mode"></a>Futtassa a parancsot, hogy a csökkentett mód

Az utolsó lehetőség, hagyja üresen a csökkentett mód parancs végrehajtása. Ha tudja, hogy HDFS megadása a csökkentett mód oka Hive korrigáljuk fájlreplikáció miatt, csökkentett üzemmódban hagyja a következő parancsot futtathatja:

```bash
hdfs dfsadmin -D 'fs.default.name=hdfs://mycluster/' -safemode leave
```

### <a name="scale-down-an-apache-hbase-cluster"></a>Vertikális leskálázás Apache HBase-fürt

Régiókiszolgálók automatikusan kiegyensúlyozott van egy skálázási művelet befejezése után néhány percen belül. Manuálisan kiegyenlítése régióbeli kiszolgálók, a következő lépéseket:

1. A HDInsight-fürthöz SSH használatával csatlakozhat. További információ: [Az SSH használata HDInsighttal](hdinsight-hadoop-linux-use-ssh-unix.md).

2. Indítsa el a HBase rendszerhéj:

    ```bash
    hbase shell
    ```

3. A következő parancs használatával manuálisan elosztása a régióbeli kiszolgálók:

    ```bash
    balancer
    ```

## <a name="next-steps"></a>További lépések

* [Az Azure HDInsight-fürtök automatikus méretezése](hdinsight-autoscale-clusters.md)
* [Az Azure HDInsight bemutatása](hadoop/apache-hadoop-introduction.md)
