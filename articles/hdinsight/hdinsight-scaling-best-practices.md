---
title: Fürtök méretének méretezése – Azure HDInsight
description: Apache Hadoop-fürt rugalmas méretezése az Azure HDInsight-beli munkaterhelésnek megfelelően
author: ashishthaps
ms.author: ashish
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 06/10/2019
ms.openlocfilehash: dad796c8a7a34a782a4f78260ac38bd966eddde9
ms.sourcegitcommit: 1c9858eef5557a864a769c0a386d3c36ffc93ce4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/18/2019
ms.locfileid: "71105399"
---
# <a name="scale-azure-hdinsight-clusters"></a>Azure HDInsight-fürtök méretezése

A HDInsight rugalmasságot biztosít azáltal, hogy lehetővé teszi a fürtökben lévő munkavégző csomópontok számának vertikális felskálázását és méretezését. Ez a rugalmasság lehetővé teszi, hogy a fürtöket órák vagy hétvégék után, az üzleti igényeknek megfelelően bővítse.

Ha rendszeres kötegelt feldolgozást használ, a HDInsight-fürt a művelet előtt néhány percen belül méretezhető, így a fürt rendelkezik a megfelelő memóriával és CPU-teljesítménnyel.  Később, a feldolgozás befejezése után a használat újra leáll, a HDInsight-fürt kevesebb feldolgozó csomópontra is méretezhető.

Manuálisan is méretezheti a fürtöt az alább vázolt módszerek egyikével, vagy az automatikus [méretezési](hdinsight-autoscale-clusters.md) beállítások használatával a rendszer a CPU, a memória és az egyéb mérőszámok alapján automatikusan fel-és leskálázást hajthat végre.

> [!NOTE]  
> Csak a 3.1.3-es vagy újabb verziójú HDInsight-fürtök támogatottak. Ha nem tudja biztosan a fürt verzióját, akkor ellenőrizze a tulajdonságok lapot.

## <a name="utilities-to-scale-clusters"></a>A fürtök méretezését szolgáló segédprogramok

A Microsoft a következő segédprogramokat biztosítja a fürtök méretezéséhez:

|Segédprogram | Leírás|
|---|---|
|[PowerShell az](https://docs.microsoft.com/powershell/azure)|[Set-AzHDInsightClusterSize](https://docs.microsoft.com/powershell/module/az.hdinsight/set-azhdinsightclustersize) -ClusterName \<-fürt neve >- \<TargetInstanceCount NewSize >|
|[PowerShell-AzureRM](https://docs.microsoft.com/powershell/azure/azurerm) |[Set-AzureRmHDInsightClusterSize](https://docs.microsoft.com/powershell/module/azurerm.hdinsight/set-azurermhdinsightclustersize) -ClusterName \<-fürt neve >- \<TargetInstanceCount NewSize >|
|[Azure CLI](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest)| [az hdinsight Resize](https://docs.microsoft.com/cli/azure/hdinsight?view=azure-cli-latest#az-hdinsight-resize) --Resource- \<Group erőforráscsoport > – név \<fürt neve >--Target-instances-Count \<NewSize >|
|[Azure CLI](hdinsight-administer-use-command-line.md)|Az Azure hdinsight- \<fürt átméretezi a clusterName > \<a célként megadott példányszámot > |
|[Azure Portal](https://portal.azure.com)|Nyissa meg a HDInsight-fürt panelt, válassza ki a **fürt méretét** a bal oldali menüben, majd a fürt mérete panelen írja be a munkavégző csomópontok számát, majd kattintson a Mentés gombra.|  

![Azure Portal méretezési fürt beállítása](./media/hdinsight-scaling-best-practices/scale-cluster-blade1.png)

A módszerek bármelyikével akár percek alatt is méretezheti a HDInsight-fürtöt.

> [!IMPORTANT]  
> * A aure klasszikus CLI elavult, és csak a klasszikus üzembehelyezési modellel használható. Az összes többi központi telepítéshez használja az [Azure CLI](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest)-t.  
> * A PowerShell-AzureRM modul elavult.  Ha lehetséges, használja az az [modult](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-1.4.0) .

## <a name="impact-of-scaling-operations"></a>A skálázási műveletek hatása

Ha csomópontokat **ad hozzá** a futó HDInsight-fürthöz (vertikális felskálázás), a függőben lévő vagy futó feladatok nem lesznek hatással. Az új feladatok biztonságosan elhelyezhetők a skálázási folyamat futása közben. Ha a skálázási művelet valamilyen okból meghiúsul, a rendszer a hibát fogja kezelni, hogy a fürt működőképes állapotban maradjon.

Ha **eltávolítja** a csomópontokat (leskálázás), a függőben lévő vagy futó feladatok sikertelenek lesznek, amikor a skálázási művelet befejeződik. Ezt a hibát a skálázási folyamat során újrainduló szolgáltatások némelyike okozza. Fennáll annak a kockázata is, hogy a fürt a manuális skálázási művelet során biztonságos módban is elakad.

Az adatcsomópontok számának módosításának következményei a HDInsight által támogatott egyes típusú fürtök esetében változnak:

* Apache Hadoop

    A Hadoop-fürtben futó munkavégző csomópontok száma zökkenőmentesen megnövelhető a függőben lévő vagy futó feladatok hatása nélkül. A művelet végrehajtása közben új feladatokat is el lehet küldeni. A skálázási művelet során fellépő hibák szabályosan kezelhetők, így a fürt mindig működőképes állapotban marad.

    Ha a Hadoop-fürtöket az adatcsomópontok számának csökkentésével csökkentették, a fürt egyes szolgáltatásai újraindulnak. Ez a viselkedés azt eredményezi, hogy a futó és a függőben lévő feladatok sikertelenek lesznek a skálázási művelet befejezésekor. A művelet befejezését követően azonban újra elküldheti a feladatokat.

* Apache HBase

    A futása közben zökkenőmentesen hozzáadhat vagy eltávolíthat csomópontokat a HBase-fürthöz. A regionális kiszolgálók a skálázási művelet befejezését követően néhány percen belül automatikusan egyensúlyban vannak. A regionális kiszolgálókat azonban manuálisan is kiegyensúlyozhatja, ha bejelentkezik a fürt átjárócsomóponthoz, és a következő parancsokat futtatja egy parancssori ablakból:

    ```bash
    pushd %HBASE_HOME%\bin
    hbase shell
    balancer
    ```

    A HBase-rendszerhéj használatával kapcsolatos további információkért tekintse meg az [Apache HBase-példa a HDInsight-ben való használatának első lépéseit](hbase/apache-hbase-tutorial-get-started-linux.md)ismertető témakört.

* Apache Storm

    A futása közben zökkenőmentesen hozzáadhat vagy eltávolíthat adatcsomópontokat a Storm-fürthöz. A skálázási művelet sikeres befejezése után azonban újra kell osztania a topológiát.

    Az újraelosztás kétféleképpen végezhető el:

  * Storm webes felhasználói felület
  * Parancssori felület (CLI) eszköz

    További részletekért tekintse meg az [Apache Storm dokumentációját](https://storm.apache.org/documentation/Understanding-the-parallelism-of-a-Storm-topology.html) .

    A Storm webes felhasználói felülete elérhető a HDInsight-fürtön:

    ![HDInsight Storm skálázási egyensúly](./media/hdinsight-scaling-best-practices/hdinsight-portal-scale-cluster-storm-rebalance.png)

    Íme egy példa CLI-parancs a Storm-topológia újraelosztásához:

    ```cli
    ## Reconfigure the topology "mytopology" to use 5 worker processes,
    ## the spout "blue-spout" to use 3 executors, and
    ## the bolt "yellow-bolt" to use 10 executors
    $ storm rebalance mytopology -n 5 -e blue-spout=3 -e yellow-bolt=10
    ```

## <a name="how-to-safely-scale-down-a-cluster"></a>Fürt biztonságos méretezése

### <a name="scale-down-a-cluster-with-running-jobs"></a>Fürt leskálázása futó feladatokkal

Ha el szeretné kerülni, hogy a futó feladatok leskálázási művelet közben meghiúsulnak, három dolgot is kipróbálhat:

1. Várjon, amíg a feladatok befejeződik a fürt skálázása előtt.
1. Manuálisan fejezze be a feladatokat.
1. A skálázási művelet megkötése után küldje el újra a feladatokat.

A függőben lévő és futó feladatok listájának megtekintéséhez a következő lépéseket követve használhatja a fonal **Resource Manager felhasználói felületét**:

1. A [Azure Portal](https://portal.azure.com/)válassza ki a fürtöt.  Lásd: [fürtök listázása és megjelenítése](./hdinsight-administer-use-portal-linux.md#showClusters) az utasításokhoz. A fürt megnyílik egy új portál oldalon.
2. A fő nézetből navigáljon a **fürt irányítópultok** > **Ambari kezdőlapra**. Adja meg a fürt hitelesítő adatait.
3. A Ambari felhasználói felületén válassza a **fonal** elemet a szolgáltatások listájában a bal oldali menüben.  
4. A fonal lapon válassza a **gyors hivatkozások** lehetőséget, majd vigye az egérmutatót az aktív fő csomópont fölé, majd válassza a **erőforráskezelő felhasználói felület**lehetőséget.

    ![Apache Ambari – gyors hivatkozások erőforráskezelő felhasználói felülete](./media/hdinsight-scaling-best-practices/resource-manager-ui1.png)

A erőforráskezelő felhasználói felületét `https://<HDInsightClusterName>.azurehdinsight.net/yarnui/hn/cluster`közvetlenül is elérheti.

Ekkor megjelenik a feladatok listája, valamint a jelenlegi állapotuk. A képernyőképen egy jelenleg futó feladatot futtatunk:

![Erőforráskezelő felhasználói felületi alkalmazások](./media/hdinsight-scaling-best-practices/resourcemanager-ui-applications.png)

Ha manuálisan szeretné megölni a futó alkalmazást, hajtsa végre a következő parancsot az SSH-rendszerhéjból:

```bash
yarn application -kill <application_id>
```

Példa:

```bash
yarn application -kill "application_1499348398273_0003"
```

### <a name="getting-stuck-in-safe-mode"></a>Biztonságos üzemmódba való beragadás

Amikor lekicsinyít egy fürtöt, a HDInsight az Apache Ambari felügyeleti felületeit használja a további feldolgozó csomópontok leszereléséhez, amelyek replikálják a HDFS-blokkokat más online feldolgozó csomópontokra. Ezt követően a HDInsight biztonságosan méretezi a fürtöt. A skálázási művelet során a HDFS csökkentett módba kerül, és a skálázás befejeződése után kikerül. Bizonyos esetekben azonban a HDFS a replikálás alatt álló blokkolás miatt egy méretezési művelet során csökkentett módban ragadja meg.

Alapértelmezés szerint a HDFS 3 `dfs.replication` értékkel van konfigurálva, amely azt szabályozza, hogy az egyes fájlok hány példánya legyen elérhető. A rendszer a fájl minden példányát a fürt egy másik csomópontján tárolja.

Ha a HDFS észleli, hogy a blokkolt példányok várt száma nem érhető el, a HDFS biztonságos módba lép, és a Ambari riasztásokat hoz létre. Ha a HDFS biztonságos üzemmódba kerül a skálázási művelethez, de nem tud kilépni a biztonságos módból, mert a replikációhoz nem észlelhetők a szükséges csomópontok, a fürt biztonságos módban is elakad.

### <a name="example-errors-when-safe-mode-is-turned-on"></a>Hibák például a biztonságos mód bekapcsolásakor

```
org.apache.hadoop.hdfs.server.namenode.SafeModeException: Cannot create directory /tmp/hive/hive/819c215c-6d87-4311-97c8-4f0b9d2adcf0. Name node is in safe mode.
```

```
org.apache.http.conn.HttpHostConnectException: Connect to hn0-clustername.servername.internal.cloudapp.net:10001 [hn0-clustername.servername. internal.cloudapp.net/1.1.1.1] failed: Connection refused
```

Tekintse át a csomópontok nevét a `/var/log/hadoop/hdfs/` mappából, a fürt méretezésének időpontja közelében, hogy a rendszer mikor adta meg a biztonságos üzemmódot. A naplófájlok neve `Hadoop-hdfs-namenode-hn0-clustername.*`.

Az előző hibák kiváltó oka, hogy a struktúra a HDFS ideiglenes fájljaitól függ a lekérdezések futtatása közben. Ha a HDFS biztonságos módba lép, a struktúra nem tud lekérdezéseket futtatni, mert nem tud írni a HDFS. A HDFS lévő ideiglenes fájlok az egyes munkavégző csomópontok virtuális gépekhez csatlakoztatott helyi meghajtón találhatók, és az egyéb feldolgozó csomópontok között a minimum három replikán replikálva vannak.

### <a name="how-to-prevent-hdinsight-from-getting-stuck-in-safe-mode"></a>A HDInsight letiltása csökkentett módban

A HDInsight csökkentett módban való használatának megakadályozása többféle módon is elvégezhető:

* A HDInsight skálázása előtt állítsa le az összes struktúra-feladatot. Másik lehetőségként ütemezze a skálázási folyamatot, hogy elkerülje a kaptár-feladatok futtatásának ütközését.
* A skálázás előtt törölje manuálisan a `tmp` kaptárt a HDFS.
* Csak három munkavégző csomópontra, minimumra HDInsight le. Ne legyen alacsonyabb, mint egy feldolgozói csomópont.
* Ha szükséges, futtassa a parancsot a biztonságos mód elhagyása érdekében.

A következő szakaszok ezeket a beállításokat ismertetik.

#### <a name="stop-all-hive-jobs"></a>Az összes kaptár-feladat leállítása

Állítsa le az összes kaptár-feladatot, mielőtt egy feldolgozói csomópontra kellene méretezni. Ha a számítási feladat ütemezve van, akkor hajtsa végre a méretezést a struktúra befejezése után.

A struktúra-feladatok skálázása előtt állítsa le a kaptár-feladatokat, így minimálisra csökkenthető a lapozófájlok száma a tmp mappában (ha van ilyen).

#### <a name="manually-clean-up-hives-scratch-files"></a>A kaptár üres fájljainak manuális törlése

Ha a struktúra az ideiglenes fájlok mögött maradt, akkor manuálisan is törölheti ezeket a fájlokat a csökkentett mód elkerülése érdekében.

1. Annak ellenőrzéséhez, hogy a rendszer melyik helyet használja a struktúra ideiglenes fájljaihoz, tekintse meg a `hive.exec.scratchdir` konfigurációs tulajdonságot. Ez a paraméter a következőn belül `/etc/hive/conf/hive-site.xml`van beállítva:

    ```xml
    <property>
        <name>hive.exec.scratchdir</name>
        <value>hdfs://mycluster/tmp/hive</value>
    </property>
    ```

1. Állítsa le a kaptár-szolgáltatásokat, és ellenőrizze, hogy az összes lekérdezés és feladat befejeződött-e.
2. Sorolja fel a fenti, a fentiekben ismertetett `hdfs://mycluster/tmp/hive/` Scratch könyvtár tartalmát, és ellenőrizze, hogy a fájl tartalmazza-e a következő fájlokat:

    ```bash
    hadoop fs -ls -R hdfs://mycluster/tmp/hive/hive
    ```

    Íme egy példa kimenet, ha a fájlok léteznek:

    ```output
    sshuser@hn0-scalin:~$ hadoop fs -ls -R hdfs://mycluster/tmp/hive/hive
    drwx------   - hive hdfs          0 2017-07-06 13:40 hdfs://mycluster/tmp/hive/hive/4f3f4253-e6d0-42ac-88bc-90f0ea03602c
    drwx------   - hive hdfs          0 2017-07-06 13:40 hdfs://mycluster/tmp/hive/hive/4f3f4253-e6d0-42ac-88bc-90f0ea03602c/_tmp_space.db
    -rw-r--r--   3 hive hdfs         27 2017-07-06 13:40 hdfs://mycluster/tmp/hive/hive/4f3f4253-e6d0-42ac-88bc-90f0ea03602c/inuse.info
    -rw-r--r--   3 hive hdfs          0 2017-07-06 13:40 hdfs://mycluster/tmp/hive/hive/4f3f4253-e6d0-42ac-88bc-90f0ea03602c/inuse.lck
    drwx------   - hive hdfs          0 2017-07-06 20:30 hdfs://mycluster/tmp/hive/hive/c108f1c2-453e-400f-ac3e-e3a9b0d22699
    -rw-r--r--   3 hive hdfs         26 2017-07-06 20:30 hdfs://mycluster/tmp/hive/hive/c108f1c2-453e-400f-ac3e-e3a9b0d22699/inuse.info
    ```

3. Ha ismeri a kaptárt ezekkel a fájlokkal, akkor eltávolíthatja őket. Győződjön meg arról, hogy a kaptár nem futtatja a erőforráskezelő felhasználói felületének megtekintése lapot.

    Példa parancssorból a HDFS fájlok eltávolításához:

    ```bash
    hadoop fs -rm -r -skipTrash hdfs://mycluster/tmp/hive/
    ```

#### <a name="scale-hdinsight-to-three-or-more-worker-nodes"></a>HDInsight skálázása három vagy több munkavégző csomópontra

Ha a fürtök a három munkavégző csomópontnál kevesebbre skálázáskor csökkentett módban vannak, és az előző lépések nem működnek, akkor elkerülhető, hogy a fürt a biztonságos módba kerüljön, így legalább három feldolgozó csomópontot kell tartania.

A három feldolgozó csomópont megtartása drágább, mint a csak egy feldolgozó csomópontra való méretezés, azonban megakadályozza, hogy a fürt biztonságos módban ragadjon.

#### <a name="run-the-command-to-leave-safe-mode"></a>Futtassa a parancsot a biztonságos üzemmód elhagyásához

Az utolsó lehetőség az, hogy végrehajtja a biztonságos mód kihagyása parancsot. Ha tudja, hogy a biztonságos mód beírásának oka a HDFS, a replikálás alatt álló struktúra miatt a következő parancs futtatásával hagyhatja el a biztonságos üzemmódot:

```bash
hdfs dfsadmin -D 'fs.default.name=hdfs://mycluster/' -safemode leave
```

### <a name="scale-down-an-apache-hbase-cluster"></a>Apache HBase-fürt méretezése

A skálázási művelet elvégzése után néhány percen belül automatikusan kiegyenlítettük a régió-kiszolgálókat. A régió-kiszolgálók manuális kiegyensúlyozásához hajtsa végre a következő lépéseket:

1. Kapcsolódjon a HDInsight-fürthöz az SSH használatával. További információ: [Az SSH használata HDInsighttal](hdinsight-hadoop-linux-use-ssh-unix.md).

2. A HBase-rendszerhéj elindítása:

    ```bash
    hbase shell
    ```

3. Használja a következő parancsot a régió-kiszolgálók manuális elosztásához:

    ```bash
    balancer
    ```

## <a name="next-steps"></a>További lépések

* [Azure HDInsight-fürtök automatikus méretezése](hdinsight-autoscale-clusters.md)
* [Az Azure HDInsight bemutatása](hadoop/apache-hadoop-introduction.md)
