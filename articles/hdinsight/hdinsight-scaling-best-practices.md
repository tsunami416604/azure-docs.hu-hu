---
title: Fürtméretek méretezése – Azure HDInsight
description: Az Apache Hadoop-fürt rugalmas méretezése az Azure HDInsight ban lévő munkaterheléshez
author: ashishthaps
ms.author: ashish
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 02/26/2020
ms.openlocfilehash: 96a72541255ad0059abe5ad280f1728518dbf68c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80234731"
---
# <a name="scale-azure-hdinsight-clusters"></a>Azure HDInsight-fürtök méretezése

A HDInsight rugalmasságot biztosít azáltal, hogy lehetővé teszi a fürtökben lévő munkavégző csomópontok számának skálázását és leskálázását. Ez a rugalmasság lehetővé teszi, hogy a fürt órák után vagy hétvégén, és bontsa ki csúcsüzleti igények alatt.

Ha rendszeres kötegelt feldolgozással rendelkezik, a HDInsight-fürt néhány perccel a művelet előtt skálázható, hogy a fürt megfelelő memóriával és processzorteljesítménysel rendelkezik. Később, miután a feldolgozás befejeződött, és a használat ismét leáll, a HDInsight-fürt kevesebb munkavégző csomópontra skálázható.

A fürt manuálisan skálázható az alább ismertetett módszerek egyikével, vagy [automatikus skálázási](hdinsight-autoscale-clusters.md) beállításokkal beállíthatja, hogy a rendszer automatikusan fel- és leskálázható legyen a processzorra, a memóriára és más metrikákra válaszul.

> [!NOTE]  
> Csak a HDInsight 3.1.3-as vagy újabb verziójával rendelkező fürtök támogatottak. Ha nem biztos a fürt verziójában, ellenőrizheti a Tulajdonságok lapot.

## <a name="utilities-to-scale-clusters"></a>Fürtök méretezéséhez hasznos segédprogramok

A Microsoft a fürtök méretezéséhez a következő segédprogramokat biztosítja:

|Segédprogram | Leírás|
|---|---|
|[PowerShell Az](https://docs.microsoft.com/powershell/azure)|[Set-AzHDInsightClusterSize](https://docs.microsoft.com/powershell/module/az.hdinsight/set-azhdinsightclustersize) -ClusterName \<fürtnév> \<-TargetInstanceCount NewSize>|
|[PowerShell AzureRM](https://docs.microsoft.com/powershell/azure/azurerm) |[Set-AzureRmHDInsightClusterSize](https://docs.microsoft.com/powershell/module/azurerm.hdinsight/set-azurermhdinsightclustersize) -ClusterName \<fürtnév> \<-TargetInstanceCount NewSize>|
|[Azure CLI](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest)| [az az hdinsight átméretezése](https://docs.microsoft.com/cli/azure/hdinsight?view=azure-cli-latest#az-hdinsight-resize) \<--erőforrás-csoport \<Erőforráscsoport> --name \<Cluster Name> --workernode-count NewSize>|
|[Azure Klasszikus CLI](hdinsight-administer-use-command-line.md)|az azure hdinsight \<fürt \<átméretezi a fürtnevet> a célpéldányok számát> |
|[Azure-portál](https://portal.azure.com)|Nyissa meg a HDInsight fürtpanelt, válassza a bal oldali menü **Fürtméret parancsát,** majd a Fürtméret ablaktáblán írja be a munkavégző csomópontok számát, és válassza a Mentés gombot.|  

![Az Azure Portal méretezési fürtbeállítása](./media/hdinsight-scaling-best-practices/azure-portal-settings-nodes.png)

Ezen módszerek bármelyikével perceken belül skálázhatja a HDInsight-fürtöt.

> [!IMPORTANT]  
> * Az Azure klasszikus CLI elavult, és csak a klasszikus üzembe helyezési modell használható. Az összes többi központi telepítéshez használja az [Azure CLI.For](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest)all other deployments, use the Azure CLI .  
> * A PowerShell AzureRM-modul elavult.  Kérjük, amikor csak lehetséges, használja az [Az modult.](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-1.4.0)

## <a name="impact-of-scaling-operations"></a>A skálázási műveletek hatása

Amikor csomópontokat **ad hozzá** a futó HDInsight-fürthöz (felskálázás), a függőben lévő vagy futó feladatokat ez nem érinti. Új feladatok biztonságosan elküldhetők, miközben a skálázási folyamat fut. Ha a skálázási művelet bármilyen okból sikertelen, a hiba lesz kezelve, hogy hagyja a fürt funkcionális állapotban.

Ha **eltávolítja** a csomópontokat (leskálázás), a függőben lévő vagy futó feladatok sikertelenek lesznek, amikor a skálázási művelet befejeződik. Ez a hiba annak köszönhető, hogy néhány szolgáltatás újraindul a skálázási folyamat során. Fennáll annak a veszélye is, hogy a fürt beragadhat csökkentett módban a manuális skálázási művelet során.

Az adatcsomópontok számának módosítása a HDInsight által támogatott fürttípusoktól függően változik:

* Apache Hadoop

    Zökkenőmentesen növelheti a munkavégző csomópontok számát egy hadoop-fürtben, amely anélkül fut, hogy befolyásolna a függőben lévő vagy futó feladatokat. A művelet közben új feladatok is elküldhetők. A skálázási művelet hibáit a program szabályosan kezeli, így a fürt mindig működőképes állapotban marad.

    Ha egy Hadoop-fürt az adatcsomópontok számának csökkentésével csökken, a fürt egyes szolgáltatásai újraindulnak. Ez a viselkedés okozza az összes futó és függőben lévő feladatok sikertelen a skálázási művelet befejezésekor. A művelet befejezése után azonban újra elküldheti a feladatokat.

* Apache HBase

    Zökkenőmentesen hozzáadhat vagy eltávolíthat csomópontokat a HBase-fürthöz futás közben. A regionális kiszolgálók automatikusan kivannak egyensúlyban a skálázási művelet befejezését követő néhány percen belül. A regionális kiszolgálókat azonban manuálisan is kiegyensúlyozhatja, ha bejelentkezik a fürt csomópontjára, és parancssori ablakból futtatja a következő parancsokat:

    ```bash
    pushd %HBASE_HOME%\bin
    hbase shell
    balancer
    ```

    A HBase rendszerhéj használatáról az [Apache HBase használatának lépésea a HDInsightban](hbase/apache-hbase-tutorial-get-started-linux.md)című témakörben talál további információt.

* Apache Storm

    Zökkenőmentesen hozzáadhat vagy eltávolíthat adatcsomópontokat a Storm-fürthöz futás közben. Azonban a skálázási művelet sikeres befejezése után újra egyensúlyba kell hoznia a topológiát.

    Az egyensúly helyreállítása kétféleképpen valósítható meg:

  * Storm web felhasználói felülete
  * Parancssori interfész (CLI) eszköz

    További részleteket az [Apache Storm dokumentációjában](https://storm.apache.org/documentation/Understanding-the-parallelism-of-a-Storm-topology.html) talál.

    A Storm webes felhasználói felülete a HDInsight-fürtön érhető el:

    ![HDInsight Storm-skála egyensúlyának helyreállítása](./media/hdinsight-scaling-best-practices/hdinsight-portal-scale-cluster-storm-rebalance.png)

    Íme egy példa cli parancsot, hogy egyensúlyba hozza a Storm topológia:

    ```console
    ## Reconfigure the topology "mytopology" to use 5 worker processes,
    ## the spout "blue-spout" to use 3 executors, and
    ## the bolt "yellow-bolt" to use 10 executors
    $ storm rebalance mytopology -n 5 -e blue-spout=3 -e yellow-bolt=10
    ```

## <a name="how-to-safely-scale-down-a-cluster"></a>Fürt biztonságos méretezése

### <a name="scale-down-a-cluster-with-running-jobs"></a>Fürt lekicsinylése futó feladatokkal

Annak elkerülése érdekében, hogy a futó feladatok sikertelenek legyenek egy leskálázási művelet során, három dolgot próbálhat meg:

1. Várja meg, amíg a feladatok befejeződnek, mielőtt leskálázhatja a fürtöt.
1. Manuálisan fejezze be a feladatokat.
1. Küldje el újra a feladatokat a skálázási művelet befejezése után.

A függőben lévő és futó feladatok listájának megtekintéséhez használja a YARN **Erőforrás-kezelő felhasználói felületét**az alábbi lépésekkel:

1. Az [Azure Portalon](https://portal.azure.com/)válassza ki a fürtöt.  Az utasításokat a [Fürtök listája és megjelenítése](./hdinsight-administer-use-portal-linux.md#showClusters) című témakörben találja. A fürt egy új portállapon nyílik meg.
2. A fő nézetben keresse meg a **Fürt irányítópultjait** > **Ambari home**. Adja meg a fürt hitelesítő adatait.
3. Az Ambari felhasználói felületén válassza a **YARN** lehetőséget a bal oldali menüszolgáltatások listájában.  
4. A YARN lapon válassza a **Gyorshivatkozások** lehetőséget, és mutasson az aktív főcsomópontra, majd válassza a **ResourceManager felhasználói felületét**.

    ![Apache Ambari gyorskapcsolatok ResourceManager UI](./media/hdinsight-scaling-best-practices/resource-manager-ui1.png)

Közvetlenül elérheti a ResourceManager `https://<HDInsightClusterName>.azurehdinsight.net/yarnui/hn/cluster`felhasználói felületét a segítségével.

Megjelenik a feladatok listája a jelenlegi állapotukkal együtt. A képernyőképen jelenleg egy feladat fut:

![ResourceManager felhasználói felületi alkalmazások](./media/hdinsight-scaling-best-practices/resourcemanager-ui-applications.png)

A futó alkalmazás manuális megöléséhez hajtsa végre a következő parancsot az SSH rendszerhéjból:

```bash
yarn application -kill <application_id>
```

Példa:

```bash
yarn application -kill "application_1499348398273_0003"
```

### <a name="getting-stuck-in-safe-mode"></a>Elakadás csökkentett módban

Fürt leskálázásakor a HDInsight az Apache Ambari felügyeleti felületek segítségével először leszereli az extra munkavégző csomópontokat, amelyek a HDFS-blokkokat más online munkavégző csomópontokra replikálják. Ezt követően a HDInsight biztonságosan skálázhatja a fürtöt. A HDFS csökkentett módban érhető el a skálázási művelet során, és a méretezés befejezése után kell kijönnie. Bizonyos esetekben azonban a HDFS csökkentett módban ragad a méretezési művelet során a fájlblokk alulreplikációja miatt.

Alapértelmezés szerint a HDFS `dfs.replication` 1-es beállítással van konfigurálva, amely azt szabályozza, hogy az egyes fájlfiókokból hány példány érhető el. A fájlblokk minden példánya a fürt egy másik csomópontján tárolódik.

Ha a HDFS azt észleli, hogy a blokkpéldányok várható száma nem érhető el, a HDFS csökkentett módba lép, és az Ambari riasztásokat hoz létre. Ha a HDFS csökkentett módba lép egy skálázási művelethez, de nem tud kilépni a csökkentett módból, mert a replikációhoz nem észlelhető a szükséges számú csomópont, a fürt csökkentett módban ragadhat meg.

### <a name="example-errors-when-safe-mode-is-turned-on"></a>Példa hibák, ha a csökkentett mód be van kapcsolva

```output
org.apache.hadoop.hdfs.server.namenode.SafeModeException: Cannot create directory /tmp/hive/hive/819c215c-6d87-4311-97c8-4f0b9d2adcf0. Name node is in safe mode.
```

```output
org.apache.http.conn.HttpHostConnectException: Connect to active-headnode-name.servername.internal.cloudapp.net:10001 [active-headnode-name.servername. internal.cloudapp.net/1.1.1.1] failed: Connection refused
```

A `/var/log/hadoop/hdfs/` fürt méretezésének idején a mappából származó névcsomópont-naplókat áttekintheti, hogy mikor lépett be csökkentett módba. A naplófájlok `Hadoop-hdfs-namenode-<active-headnode-name>.*`neve .

Az előző hibák kiváltó oka az, hogy a Hive a lekérdezések futtatása közben a HDFS-ben lévő ideiglenes fájloktól függ. Amikor a HDFS csökkentett módba lép, a Hive nem tud lekérdezéseket futtatni, mert nem tud írni a HDFS rendszerbe. A HDFS-ben lévő ideiglenes fájlok az egyes munkavégző csomópont virtuális gépeihez csatlakoztatott helyi meghajtón találhatók, és a többi feldolgozócsomópont között replikálhatók három replikával, legalább három replikával.

### <a name="how-to-prevent-hdinsight-from-getting-stuck-in-safe-mode"></a>Hogyan akadályozható meg, hogy a HDInsight beszoruljon csökkentett módban?

A HDInsight csökkentett módban való elhagyását többféleképpen is megakadályozhatja:

* Állítsa le az összes Hive-feladatok, mielőtt leskálázhatja a HDInsight. Másik lehetőségként ütemezze a leskálázási folyamatot, hogy elkerülje a Hive-feladatok futtatásának elkerülését.
* A leskálázás előtt `tmp` manuálisan tisztítsa meg a Hive névmásként szükséges könyvtárfájljait a HDFS-ben.
* Csak a HDInsight horizontális felskálázása három munkavégző csomópontra, minimum. Ne menjen olyan alacsonyra, mint egy munkavégző csomópont.
* Szükség esetén futtassa a parancsot a csökkentett mód elhagyásához.

A következő szakaszok ezeket a beállításokat ismertetik.

#### <a name="stop-all-hive-jobs"></a>Az összes Hive-feladat leállítása

Állítsa le az összes Hive-feladatok, mielőtt egy munkavégző csomópontra. Ha a számítási feladatok ütemezett, majd hajtsa végre a leskálázás után Hive munka befejeződött.

A Hive-feladatok méretezés előtti leállítása segít minimalizálni a tmp mappában lévő koncos fájlok számát (ha vannak ilyenek).

#### <a name="manually-clean-up-hives-scratch-files"></a>A Hive kaparós sorsjegyfájljainak manuális karbantartása

Ha a Hive ideiglenes fájlokat hagyott hátra, akkor manuálisan is megtisztíthatja ezeket a fájlokat, mielőtt leskálázná őket a csökkentett mód elkerülése érdekében.

1. Ellenőrizze, hogy melyik helyet használja a Hive `hive.exec.scratchdir` ideiglenes fájlokat a konfigurációs tulajdonság ból. Ez a paraméter `/etc/hive/conf/hive-site.xml`a következőkben van beállítva:

    ```xml
    <property>
        <name>hive.exec.scratchdir</name>
        <value>hdfs://mycluster/tmp/hive</value>
    </property>
    ```

1. Állítsa le a Hive-szolgáltatásokat, és győződjön meg arról, hogy minden lekérdezés és feladat befejeződött.
2. Sorolja fel a tartalmát a `hdfs://mycluster/tmp/hive/` semmiből könyvtár fent található, hogy ha tartalmaz fájlokat:

    ```bash
    hadoop fs -ls -R hdfs://mycluster/tmp/hive/hive
    ```

    Itt van egy minta kimenet, ha fájlok léteznek:

    ```output
    sshuser@scalin:~$ hadoop fs -ls -R hdfs://mycluster/tmp/hive/hive
    drwx------   - hive hdfs          0 2017-07-06 13:40 hdfs://mycluster/tmp/hive/hive/4f3f4253-e6d0-42ac-88bc-90f0ea03602c
    drwx------   - hive hdfs          0 2017-07-06 13:40 hdfs://mycluster/tmp/hive/hive/4f3f4253-e6d0-42ac-88bc-90f0ea03602c/_tmp_space.db
    -rw-r--r--   3 hive hdfs         27 2017-07-06 13:40 hdfs://mycluster/tmp/hive/hive/4f3f4253-e6d0-42ac-88bc-90f0ea03602c/inuse.info
    -rw-r--r--   3 hive hdfs          0 2017-07-06 13:40 hdfs://mycluster/tmp/hive/hive/4f3f4253-e6d0-42ac-88bc-90f0ea03602c/inuse.lck
    drwx------   - hive hdfs          0 2017-07-06 20:30 hdfs://mycluster/tmp/hive/hive/c108f1c2-453e-400f-ac3e-e3a9b0d22699
    -rw-r--r--   3 hive hdfs         26 2017-07-06 20:30 hdfs://mycluster/tmp/hive/hive/c108f1c2-453e-400f-ac3e-e3a9b0d22699/inuse.info
    ```

3. Ha tudja, hogy a Hive végzett ezekkel a fájlokkal, eltávolíthatja őket. Győződjön meg arról, hogy a Hive nem fut semmilyen lekérdezést a Yarn ResourceManager felhasználói felületének megnézésével.

    Példa parancssorra a HDFS fájlrendszerből való fájlok eltávolításához:

    ```bash
    hadoop fs -rm -r -skipTrash hdfs://mycluster/tmp/hive/
    ```

#### <a name="scale-hdinsight-to-three-or-more-worker-nodes"></a>A HDInsight méretezése három vagy több munkavégző csomópontra

Ha a fürtök gyakran elakadnak csökkentett módban, amikor háromnál kevesebb munkavégző csomópontra csökkennek le, és az előző lépések nem működnek, akkor elkerülheti, hogy a fürt teljesen biztonságos módban legyen legalább három munkavégző csomópont megtartásával.

Három munkavégző csomópont megtartása költségesebb, mint a leskálázás csak egy munkavégző csomópontra, de megakadályozza, hogy a fürt beszoruljon csökkentett módban.

### <a name="scale-hdinsight-down-to-one-worker-node"></a>A HDInsight méretezése egy munkavégző csomópontra

Még akkor is, ha a fürt lekicsinyített 1 csomópont, a 0 munkavégző csomópont továbbra is fennmarad. A 0 munkavégző csomópontot soha nem lehet leszerelni.

#### <a name="run-the-command-to-leave-safe-mode"></a>A parancs futtatása a csökkentett módból való kilépéshez

Az utolsó lehetőség a leave safe mode parancs végrehajtása. Ha tudja, hogy a HDFS csökkentett módba lépésének oka a Hive-fájl alulreplikációja miatt van, a következő parancsot hajthatja végre a csökkentett mód elhagyásához:

```bash
hdfs dfsadmin -D 'fs.default.name=hdfs://mycluster/' -safemode leave
```

### <a name="scale-down-an-apache-hbase-cluster"></a>Apache HBase-fürt lekicsinylése

A régiókiszolgálók automatikusan kivannak egyensúlyban néhány percen belül a skálázási művelet befejezése után. A régiókiszolgálók manuális kiegyensúlyozásához hajtsa végre az alábbi lépéseket:

1. Csatlakozzon a HDInsight-fürthöz az SSH használatával. További információ: [SSH használata a HDInsight segítségével.](hdinsight-hadoop-linux-use-ssh-unix.md)

2. Indítsa el a HBase rendszerhéjat:

    ```bash
    hbase shell
    ```

3. A következő paranccsal manuálisan egyensúlyozta ki a régiókiszolgálókat:

    ```bash
    balancer
    ```

## <a name="next-steps"></a>További lépések

* [Az Azure HDInsight-fürtök automatikus méretezése](hdinsight-autoscale-clusters.md)
* [Bevezetés az Azure HDInsight ba](hadoop/apache-hadoop-introduction.md)
