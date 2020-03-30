---
title: OutOfMemoryError kivételek az Apache Sparkhoz az Azure HDInsightban
description: Különböző OutOfMemoryError kivételek az Apache Spark-fürthöz az Azure HDInsightban
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 08/15/2019
ms.openlocfilehash: 31cdef281b1cb26d01a4690c815e3d3621e2c053
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79271966"
---
# <a name="outofmemoryerror-exceptions-for-apache-spark-in-azure-hdinsight"></a>OutOfMemoryError kivételek az Apache Sparkhoz az Azure HDInsightban

Ez a cikk az Apache Spark-összetevők Azure HDInsight-fürtökben való használata során felmerülő problémák hibaelhárítási lépéseit és lehetséges megoldásait ismerteti.

## <a name="scenario-outofmemoryerror-exception-for-apache-spark"></a>Eset: OutOfMemoryError kivétel az Apache Spark számára

### <a name="issue"></a>Probléma

Az Apache Spark-alkalmazás nem sikerült egy OutOfMemoryError kezeletlen kivétellel. A következőhöz hasonló hibaüzenet jelenhet meg:

```error
ERROR Executor: Exception in task 7.0 in stage 6.0 (TID 439)

java.lang.OutOfMemoryError
    at java.io.ByteArrayOutputStream.hugeCapacity(Unknown Source)
    at java.io.ByteArrayOutputStream.grow(Unknown Source)
    at java.io.ByteArrayOutputStream.ensureCapacity(Unknown Source)
    at java.io.ByteArrayOutputStream.write(Unknown Source)
    at java.io.ObjectOutputStream$BlockDataOutputStream.drain(Unknown Source)
    at java.io.ObjectOutputStream$BlockDataOutputStream.setBlockDataMode(Unknown Source)
    at java.io.ObjectOutputStream.writeObject0(Unknown Source)
    at java.io.ObjectOutputStream.writeObject(Unknown Source)
    at org.apache.spark.serializer.JavaSerializationStream.writeObject(JavaSerializer.scala:44)
    at org.apache.spark.serializer.JavaSerializerInstance.serialize(JavaSerializer.scala:101)
    at org.apache.spark.executor.Executor$TaskRunner.run(Executor.scala:239)
    at java.util.concurrent.ThreadPoolExecutor.runWorker(Unknown Source)
    at java.util.concurrent.ThreadPoolExecutor$Worker.run(Unknown Source)
    at java.lang.Thread.run(Unknown Source)
```

```error
ERROR SparkUncaughtExceptionHandler: Uncaught exception in thread Thread[Executor task launch worker-0,5,main]

java.lang.OutOfMemoryError
    at java.io.ByteArrayOutputStream.hugeCapacity(Unknown Source)
    ...
```

### <a name="cause"></a>Ok

A kivétel legvalószínűbb oka az, hogy nincs elég halommemória a Java virtuális gépek (JVM) számára. Ezek a JVM-ek végrehajtóként vagy illesztőprogramként indulnak az Apache Spark alkalmazás részeként.

### <a name="resolution"></a>Megoldás:

1. Határozza meg a Spark-alkalmazás által kezelendő adatok maximális méretét. Becsülje meg a méretet a bemeneti adatok maximális mérete, a bemeneti adatok átalakításával előállított köztes adatok és a köztes adatok további átalakításával előállított kimeneti adatok alapján. Ha a kezdeti becslés nem elegendő, növelje a méretet, és addig itetéz, amíg a memóriahibák el nem múlnak.

1. Győződjön meg arról, hogy a használni kívánt HDInsight-fürtnek elegendő memória-erőforrás áll a rendelkezésére, és elegendő maggal rendelkezik a Spark-alkalmazás elhelyezéséhez. Ez a fürt YARN felhasználói felületének fürtmetrikák szakaszának megtekintésével határozható meg a **felhasznált memória** és a használt **memória összes** **memóriája** és a virtuális magok és a **virtuális magok összesen értékeire vonatkozóan.**

    ![fonal mag memória nézete](./media/apache-spark-ts-outofmemory/yarn-core-memory-view.png)

1. Állítsa be a következő Spark-konfigurációk megfelelő értékeket. Az alkalmazáskövetelményeinek és a fürtben rendelkezésre álló erőforrások közötti egyensúly. Ezek az értékek nem haladhatják meg a YARN által nézett memória és magmagok 90%-át, és meg kell felelniük a Spark-alkalmazás minimális memóriaigényének is:

    ```
    spark.executor.instances (Example: 8 for 8 executor count)
    spark.executor.memory (Example: 4g for 4 GB)
    spark.yarn.executor.memoryOverhead (Example: 384m for 384 MB)
    spark.executor.cores (Example: 2 for 2 cores per executor)
    spark.driver.memory (Example: 8g for 8GB)
    spark.driver.cores (Example: 4 for 4 cores)
    spark.yarn.driver.memoryOverhead (Example: 384m for 384MB)
    ```

    Az összes végrehajtó által használt összes memória =

    ```
    spark.executor.instances * (spark.executor.memory + spark.yarn.executor.memoryOverhead) 
    ```

    Az illesztőprogram által használt teljes memória =

    ```
    spark.driver.memory + spark.yarn.driver.memoryOverhead
    ```

---

## <a name="scenario-java-heap-space-error-when-trying-to-open-apache-spark-history-server"></a>Eset: Java halomterület hiba, amikor megpróbálja megnyitni az Apache Spark előzménykiszolgálóját

### <a name="issue"></a>Probléma

A Spark-előzmények kiszolgálón az események megnyitásakor a következő hibaüzenet jelenik meg:

```
scala.MatchError: java.lang.OutOfMemoryError: Java heap space (of class java.lang.OutOfMemoryError)
```

### <a name="cause"></a>Ok

Ezt a problémát gyakran az erőforrások hiánya okozza a nagy szikraesemény-fájlok megnyitásakor. A Spark halommemória mérete alapértelmezés szerint 1 GB-ra van állítva, de a nagy Spark eseményfájlok ennél többre is szükség lehet.

Ha ellenőrizni szeretné a betölteni kívánt fájlok méretét, hajtsa végre a következő parancsokat:

```bash
hadoop fs -du -s -h wasb:///hdp/spark2-events/application_1503957839788_0274_1/
**576.5 M**  wasb:///hdp/spark2-events/application_1503957839788_0274_1

hadoop fs -du -s -h wasb:///hdp/spark2-events/application_1503957839788_0264_1/
**2.1 G**  wasb:///hdp/spark2-events/application_1503957839788_0264_1
```

### <a name="resolution"></a>Megoldás:

A Spark History Server memóriáját `SPARK_DAEMON_MEMORY` növelheti a tulajdonság szerkesztésével a Spark konfigurációjában, és újraindíthatja az összes szolgáltatást.

Ezt az Ambari böngésző felhasználói felületén belül teheti meg a Spark2/Config/Advanced spark2-env szakasz kiválasztásával.

![Speciális szikra2-env szakasz](./media/apache-spark-ts-outofmemory-heap-space/apache-spark-image01.png)

A Spark History Server memóriájának 1 g-ról `SPARK_DAEMON_MEMORY=4g`4g-re történő módosításához adja hozzá a következő tulajdonságot: .

![Spark tulajdonság](./media/apache-spark-ts-outofmemory-heap-space/apache-spark-image02.png)

Győződjön meg róla, hogy indítsa újra az összes érintett szolgáltatást Ambari.

---

## <a name="scenario-livy-server-fails-to-start-on-apache-spark-cluster"></a>Forgatókönyv: A Livy Server nem indul el az Apache Spark-fürtön

### <a name="issue"></a>Probléma

A Livy Server nem indítható apache sparkon [(Spark 2.1 Linuxon (HDI 3.6)]. Az újraindítás megkísérlése a Livy-naplókból a következő hibavermet eredményezi:

```log
17/07/27 17:52:50 INFO CuratorFrameworkImpl: Starting
17/07/27 17:52:50 INFO ZooKeeper: Client environment:zookeeper.version=3.4.6-29--1, built on 05/15/2017 17:55 GMT
17/07/27 17:52:50 INFO ZooKeeper: Client environment:host.name=10.0.0.66
17/07/27 17:52:50 INFO ZooKeeper: Client environment:java.version=1.8.0_131
17/07/27 17:52:50 INFO ZooKeeper: Client environment:java.vendor=Oracle Corporation
17/07/27 17:52:50 INFO ZooKeeper: Client environment:java.home=/usr/lib/jvm/java-8-openjdk-amd64/jre
17/07/27 17:52:50 INFO ZooKeeper: Client environment:java.class.path= <DELETED>
17/07/27 17:52:50 INFO ZooKeeper: Client environment:java.library.path= <DELETED>
17/07/27 17:52:50 INFO ZooKeeper: Client environment:java.io.tmpdir=/tmp
17/07/27 17:52:50 INFO ZooKeeper: Client environment:java.compiler=<NA>
17/07/27 17:52:50 INFO ZooKeeper: Client environment:os.name=Linux
17/07/27 17:52:50 INFO ZooKeeper: Client environment:os.arch=amd64
17/07/27 17:52:50 INFO ZooKeeper: Client environment:os.version=4.4.0-81-generic
17/07/27 17:52:50 INFO ZooKeeper: Client environment:user.name=livy
17/07/27 17:52:50 INFO ZooKeeper: Client environment:user.home=/home/livy
17/07/27 17:52:50 INFO ZooKeeper: Client environment:user.dir=/home/livy
17/07/27 17:52:50 INFO ZooKeeper: Initiating client connection, connectString=zk2-kcspark.cxtzifsbseee1genzixf44zzga.gx.internal.cloudapp.net:2181,zk3-kcspark.cxtzifsbseee1genzixf44zzga.gx.internal.cloudapp.net:2181,zk6-kcspark.cxtzifsbseee1genzixf44zzga.gx.internal.cloudapp.net:2181 sessionTimeout=60000 watcher=org.apache.curator.ConnectionState@25fb8912
17/07/27 17:52:50 INFO StateStore$: Using ZooKeeperStateStore for recovery.
17/07/27 17:52:50 INFO ClientCnxn: Opening socket connection to server 10.0.0.61/10.0.0.61:2181. Will not attempt to authenticate using SASL (unknown error)
17/07/27 17:52:50 INFO ClientCnxn: Socket connection established to 10.0.0.61/10.0.0.61:2181, initiating session
17/07/27 17:52:50 INFO ClientCnxn: Session establishment complete on server 10.0.0.61/10.0.0.61:2181, sessionid = 0x25d666f311d00b3, negotiated timeout = 60000
17/07/27 17:52:50 INFO ConnectionStateManager: State change: CONNECTED
17/07/27 17:52:50 WARN NativeCodeLoader: Unable to load native-hadoop library for your platform... using builtin-java classes where applicable
17/07/27 17:52:50 INFO AHSProxy: Connecting to Application History server at headnodehost/10.0.0.67:10200
Exception in thread "main" java.lang.OutOfMemoryError: unable to create new native thread
  at java.lang.Thread.start0(Native Method)
  at java.lang.Thread.start(Thread.java:717)
  at com.cloudera.livy.Utils$.startDaemonThread(Utils.scala:98)
  at com.cloudera.livy.utils.SparkYarnApp.<init>(SparkYarnApp.scala:232)
  at com.cloudera.livy.utils.SparkApp$.create(SparkApp.scala:93)
  at com.cloudera.livy.server.batch.BatchSession$$anonfun$recover$2$$anonfun$apply$4.apply(BatchSession.scala:117)
  at com.cloudera.livy.server.batch.BatchSession$$anonfun$recover$2$$anonfun$apply$4.apply(BatchSession.scala:116)
  at com.cloudera.livy.server.batch.BatchSession.<init>(BatchSession.scala:137)
  at com.cloudera.livy.server.batch.BatchSession$.recover(BatchSession.scala:108)
  at com.cloudera.livy.sessions.BatchSessionManager$$anonfun$$init$$1.apply(SessionManager.scala:47)
  at com.cloudera.livy.sessions.BatchSessionManager$$anonfun$$init$$1.apply(SessionManager.scala:47)
  at scala.collection.TraversableLike$$anonfun$map$1.apply(TraversableLike.scala:244)
  at scala.collection.TraversableLike$$anonfun$map$1.apply(TraversableLike.scala:244)
  at scala.collection.mutable.ResizableArray$class.foreach(ResizableArray.scala:59)
  at scala.collection.mutable.ArrayBuffer.foreach(ArrayBuffer.scala:47)
  at scala.collection.TraversableLike$class.map(TraversableLike.scala:244)
  at scala.collection.AbstractTraversable.map(Traversable.scala:105)
  at com.cloudera.livy.sessions.SessionManager.com$cloudera$livy$sessions$SessionManager$$recover(SessionManager.scala:150)
  at com.cloudera.livy.sessions.SessionManager$$anonfun$1.apply(SessionManager.scala:82)
  at com.cloudera.livy.sessions.SessionManager$$anonfun$1.apply(SessionManager.scala:82)
  at scala.Option.getOrElse(Option.scala:120)
  at com.cloudera.livy.sessions.SessionManager.<init>(SessionManager.scala:82)
  at com.cloudera.livy.sessions.BatchSessionManager.<init>(SessionManager.scala:42)
  at com.cloudera.livy.server.LivyServer.start(LivyServer.scala:99)
  at com.cloudera.livy.server.LivyServer$.main(LivyServer.scala:302)
  at com.cloudera.livy.server.LivyServer.main(LivyServer.scala)
  
  ## using "vmstat" found  we had enough free memory
```

### <a name="cause"></a>Ok

`java.lang.OutOfMemoryError: unable to create new native thread`kiemeli az operációs rendszert, amely nem tud több natív szálat hozzárendelni a JVM-ekhez. Megerősítést nyert, hogy ezt a kivételt a folyamatonkénti szálszám-korlát megsértése okozza.

Amikor a Livy Server váratlanul leáll, a Spark-fürtökkel létesített összes kapcsolat is megszakad, ami azt jelenti, hogy az összes feladat és a kapcsolódó adatok elvesznek. A HDP 2.6 munkamenet-helyreállítási mechanizmus bevezetése korlett, livy tárolja a munkamenet részleteit Zookeeper vissza kell állítani, miután a Livy Server visszatért.

Ha a Livy-n keresztül nagy számú feladat kerül elküldésre, a Livy Server magas rendelkezésre állásának részeként ezeket a munkamenet-állapotokat ZK-ban tárolja (HDInsight-fürtökön), és helyreállítja ezeket a munkameneteket a Livy szolgáltatás újraindításakor. A váratlan megszüntetés t követő újraindításkor livy munkamenetenként egy szálat hoz létre, és ez bizonyos számú helyreállított munkamenetet halmoz fel, ami túl sok szálat hoz létre.

### <a name="resolution"></a>Megoldás:

Törölje az összes bejegyzést az alábbi lépések segítségével.

1. Az állattartó csomópontok IP-címének beszereznie

    ```bash
    grep -R zk /etc/hadoop/conf  
    ```

1. Fent parancs felsorolt a zookeepers az én klaszter

    ```bash
    /etc/hadoop/conf/core-site.xml:      <value>zk1-hwxspa.lnuwp5akw5ie1j2gi2amtuuimc.dx.internal.cloudapp.net:2181,zk2-      hwxspa.lnuwp5akw5ie1j2gi2amtuuimc.dx.internal.cloudapp.net:2181,zk4-hwxspa.lnuwp5akw5ie1j2gi2amtuuimc.dx.internal.cloudapp.net:2181</value>
    ```

1. Szerezd meg az összes IP-címét az állattartó csomópontok segítségével ping Vagy akkor is csatlakozhat zookeeper a headnode segítségével zk név

    ```bash
    /usr/hdp/current/zookeeper-client/bin/zkCli.sh -server zk2-hwxspa:2181
    ```

1. Miután csatlakozott zookeeper végrehajtja a következő parancsot, hogy sorolja fel az összes munkamenetek, amelyek megpróbálták újraindítani.

    1. A legtöbb esetben ez lehet egy lista több mint 8000 ülés ####

        ```bash
        ls /livy/v1/batch
        ```

    1. A következő parancs az összes helyreaállított munkamenet eltávolítása. #####

        ```bash
        rmr /livy/v1/batch
        ```

1. Várja meg, amíg a fenti parancs befejeződik, és a kurzor visszaadja a kérdést, majd indítsa újra a Livy szolgáltatást az Ambari-tól, amelynek sikeresnek kell lennie.

> [!NOTE]
> `DELETE`a livy ülést, amint az befejezi a végrehajtását. A Livy kötegelt munkamenetek nem törlődnek automatikusan, amint a Spark alkalmazás befejeződik, amely a tervezés szerint. A Livy-munkamenet egy POST-kérelem által a Livy Rest kiszolgáló ellen létrehozott entitás. Az `DELETE` entitás törléséhez hívásra van szükség. Vagy meg kéne várnunk, amíg a GC hatni kezd.

---

## <a name="next-steps"></a>További lépések

Ha nem látta a problémát, vagy nem tudja megoldani a problémát, további támogatásért látogasson el az alábbi csatornák egyikébe:

* [Spark memóriakezelés – áttekintés](https://spark.apache.org/docs/latest/tuning.html#memory-management-overview).

* [A Spark-alkalmazás hibakeresése HDInsight-fürtökön.](https://blogs.msdn.microsoft.com/azuredatalake/2016/12/19/spark-debugging-101/)

* Válaszokat kaphat az Azure szakértőitől az [Azure közösségi támogatásán](https://azure.microsoft.com/support/community/)keresztül.

* Lépjen [@AzureSupport](https://twitter.com/azuresupport) kapcsolatba a hivatalos Microsoft Azure-fiókkal az ügyfélélmény javítása érdekében. Az Azure-közösség összekapcsolása a megfelelő erőforrásokkal: válaszok, támogatás és szakértők.

* Ha további segítségre van szüksége, támogatási kérelmet nyújthat be az [Azure Portalról.](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/) Válassza a **menüsor Támogatás parancsát,** vagy nyissa meg a **Súgó + támogatási** központot. További információkért tekintse [át az Azure-támogatási kérelem létrehozása című áttekintést.](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request) Az Előfizetés-kezelés hez és a számlázási támogatáshoz való hozzáférés a Microsoft Azure-előfizetésrészét képezi, a technikai támogatást pedig az [Azure-támogatási csomagok](https://azure.microsoft.com/support/plans/)egyike biztosítja.
