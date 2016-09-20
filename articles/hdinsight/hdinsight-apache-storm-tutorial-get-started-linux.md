<properties
    pageTitle="Apache Storm-oktatóanyag: Megismerkedés a Linux-alapú Storm on HDInsight alkalmazással | Microsoft Azure"
    description="Ismerkedjen meg a big data elemzésre szolgáló Apache Storm alkalmazással, valamint a Linux-alapú HDInsight Storm Starter-mintáival. Ismerkedjen meg a Storm valós idejű adatfeldolgozásra történő használatával."
    keywords="apache storm,apache storm-oktatóanyag,big data-elemzés,storm starter"
    services="hdinsight"
    documentationCenter=""
    authors="Blackmist"
    manager="paulettm"
    editor="cgronlun"/>

<tags
   ms.service="hdinsight"
   ms.devlang="java"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="08/16/2016"
   ms.author="larryfr"/>


# Apache Storm-oktatóanyag: Megismerkedés a big data elemzés HDInsight platformon történő elvégzésére szolgáló Storm Starter-mintákkal

Az Apache Storm egy skálázható, hibatűrő, elosztott, valós idejű számítási rendszer az adatstreamek feldolgozására. A Storm on Azure HDInsight segítségével olyan felhőalapú Storm-fürtöket hozhat létre, amelyek valós időben végeznek big data elemzést.

> [AZURE.NOTE] A cikkben található lépések során Linux-alapú HDInsight-fürtöt hozunk létre. Windows-alapú Storm on HDInsight-fürt létrehozásának lépései: [Apache Storm-oktatóanyag: Megismerkedés a Storm Starter-mintákkal a HDInsight platformon történő adatelemzés során](hdinsight-apache-storm-tutorial-get-started.md)

## Előkészületek

[AZURE.INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

Ennek az Apache Storm-oktatóanyagnak a sikeres befejezéséhez az alábbiakra van szükség:

- **Azure-előfizetés**. Lásd: [Ingyenes Azure-fiók létrehozása](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

- **SSH- és SCP-ismeretek**. Az SSH és az SCP a HDInsighttal történő használatáról további információk:

    - **Linux-, Unix- vagy OS X-ügyfelek esetén**: Lásd: [Use SSH with Linux-based Hadoop on HDInsight from Linux, OS X or Unix](hdinsight-hadoop-linux-use-ssh-unix.md) (Az SSH használata a HDInsightra épülő Linux-alapú Hadooppal Linux, Unix vagy OS X rendszerben)

    - **Windows-ügyfelek számára**: Lásd: [Use SSH with Linux-based Hadoop on HDInsight from Windows](hdinsight-hadoop-linux-use-ssh-windows.md) (Az SSH használata a HDInsightra épülő Linux-alapú Hadooppal Windows rendszerben)

## Storm-fürt létrehozása

Ebben a szakaszban 3.2-es verziójú (0.9.3-as Storm-verziójú) HDInsight-fürtöt hoz létre egy Azure Resource Manager-sablon használatával. Információk a HDInsight-verziókról és azok SLA-iról: [HDInsight-összetevők verziószámozása](hdinsight-component-versioning.md). Egyéb fürtlétrehozási módszerek: [HDInsight-fürtök létrehozása](hdinsight-hadoop-provision-linux-clusters.md).

1. Az alábbi képre kattintva megnyithatja a sablont az Azure Portalon.         

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fhditutorialdata.blob.core.windows.net%2Farmtemplates%2Fcreate-linux-based-storm-cluster-in-hdinsight.json" target="_blank"><img src="https://acom.azurecomcdn.net/80C57D/cdn/mediahandler/docarticles/dpsmedia-prod/azure.microsoft.com/en-us/documentation/articles/hdinsight-hbase-tutorial-get-started-linux/20160201111850/deploy-to-azure.png" alt="Deploy to Azure"></a>
    
    A sablon a következő nyilvános blobtárolóban található: *https://hditutorialdata.blob.core.windows.net/armtemplates/create-linux-based-storm-cluster-in-hdinsight.json*. 
   
2. A Parameters (Paraméterek) panelen adja meg a következőket:

    - **ClusterName**: Adjon nevet a létrehozandó Hadoop-fürtnek.
    - **A fürt bejelentkezési neve és jelszava**: Az alapértelmezett bejelentkezési név az admin.
    - **SSH-felhasználónév és -jelszó**.
    
    Jegyezze fel ezeket az értékeket.  Ezekre az oktatóanyag későbbi részében szüksége lesz.

    > [AZURE.NOTE] Az SSH segítségével – parancssorból – távolról is hozzáférhet a HDInsight-fürthöz. Az itt használt felhasználónévvel és jelszóval csatlakozhat a fürthöz az SSH-n keresztül. Emellett az SSH-felhasználónévnek egyedinek kell lennie, mivel az hozza létre a felhasználói fiókokat az összes HDInsight-fürtcsomóponton. A következő felhasználóneveket a rendszer a fürt szolgáltatásai számára tartja fent, ezért SSH-felhasználónévként nem használhatók:
    >
    > root, hdiuser, storm, hbase, ubuntu, zookeeper, hdfs, yarn, mapred, hbase, hive, oozie, falcon, sqoop, admin, tez, hcat, hdinsight-zookeeper.

    > Az SSH a HDInsighttal történő használatáról további információkat a következő cikkekben talál:

    > * [Az SSH használata a HDInsight-ra épülő Linux-alapú Hadooppal Linux, Unix vagy OS X rendszerben](hdinsight-hadoop-linux-use-ssh-unix.md)
    > * [Az SSH használata a HDInsight-ra épülő Linux-alapú Hadooppal Windows rendszerben](hdinsight-hadoop-linux-use-ssh-windows.md)

    
3. Kattintson az **OK** gombra a paraméterek mentéséhez.

4. A **Custom deployment** (Egyéni üzembe helyezés) panelen kattintson a **Resource group** (Erőforráscsoport) legördülő listára, majd a **New** (Új) lehetőségre egy új erőforráscsoport létrehozásához. Az erőforráscsoport egy olyan tároló, amely csoportosítja a fürtöt, a függő tárfiókot és egyéb kapcsolt erőforrásokat.

5. Kattintson a **Legal terms** (Jogi feltételek), majd a **Create** (Létrehozás) gombra.

6. Kattintson a ** Create** (Létrehozás) gombra. Ekkor egy új csempe jelenik meg Submitting deployment for Template deployment (Üzemelő példány elküldése sablon üzemelő példányhoz) A fürt és az SQL-adatbázis létrehozása nagyjából 20 percet vesz igénybe.


##Storm Starter-minta futtatása HDInsight platformon

A [storm-starter](https://github.com/apache/storm/tree/master/examples/storm-starter)-minták a HDInsight-fürt részét képezik. A következő lépésekben a WordCount-példát fogja futtatni.

1. Csatlakozzon SSH-val a HDInsight-fürthöz:

        ssh USERNAME@CLUSTERNAME-ssh.azurehdinsight.net
        
    Ha az SSH-felhasználói fiókhoz jelszót használt, a rendszer felkéri annak megadására. Nyilvános kulcs használatakor lehetséges, hogy az `-i` paraméter használatára van szükség a megfelelő titkos kulcs megadásához. Például: `ssh -i ~/.ssh/id_rsa USERNAME@CLUSTERNAME-ssh.azurehdinsight.net`.
        
    Az SSH Linux-alapú HDInsighttal történő használatáról további információkat a következő cikkekben talál:
    
    * [Az SSH használata a HDInsight-ra épülő Linux-alapú Hadooppal Linux, Unix vagy OS X rendszerben](hdinsight-hadoop-linux-use-ssh-unix.md)

    * [Az SSH használata a HDInsight-ra épülő Linux-alapú Hadooppal Windows rendszerben](hdinsight-hadoop-linux-use-ssh-windows)

2. Használja az alábbi parancsot példatopológia indításához:

        storm jar /usr/hdp/current/storm-client/contrib/storm-starter/storm-starter-topologies-0.10.0.2.4.2.4-5.jar storm.starter.WordCountTopology wordcount
        
    > [AZURE.NOTE] A HDInsight a Storm újabb verzióival történő frissítésekor lehetséges, hogy a fájlnév `0.10.0.2.4.2.4-5` része megváltozik.

    Ekkor a fürtön a már ismert „wordcount” néven elindul a WordCount-példatopológia. Ez véletlenszerűen állít elő mondatokat, majd az egyes szavak előfordulását számolja meg a mondatokban.

    > [AZURE.NOTE] A topológia a fürtre történő elküldésekor a fürtöt tartalmazó jar-fájlt a `storm` parancs használata előtt kell másolnia. Ezt úgy hajthatja végre, hogy a `scp` parancsot futtatja a fájlt tartalmazó ügyfélről. Például: `scp FILENAME.jar USERNAME@CLUSTERNAME-ssh.azurehdinsight.net:FILENAME.jar`
    >
    > A WordCount-példa és egyéb Storm Starter-példák megtalálhatóak a fürtön a következő helyen: `/usr/hdp/current/storm-client/contrib/storm-starter/`.

##A topológia figyelése

A HDInsight-fürtön elérhető Storm webes felhasználói felületet biztosít a futó topológiákkal való munkavégzéshez.

Kövesse az alábbi lépéseket a topológia a Storm felhasználói felületével történő figyeléséhez:

1. Egy webböngészőben nyissa meg a https://CLUSTERNAME.azurehdinsight.net/stormui címet, ahol a __CLUSTERNAME__ kifejezés helyére a saját fürtjének nevét írja. Ekkor megnyílik a Storm felhasználói felülete.

    > [AZURE.NOTE] Ha a rendszer felkéri a felhasználónév és a jelszó megadására, a fürt létrehozásakor használt fürtrendszergazda (rendszergazda) nevét és jelszavát adja meg.

2. A **Topology summary** (Topológia összegzése) területen válassza ki a **wordcount** bejegyzést a **Name** (Név) oszlopban. Ekkor további információk jelennek meg a topológiával kapcsolatban.

    ![A Storm irányítópultja a Storm Starter WordCount-topológiával kapcsolatos információkkal.](./media/hdinsight-apache-storm-tutorial-get-started-linux/topology-summary.png)

    Ezen a lapon az alábbi információk találhatóak meg:

    * **Topológiastatisztikák** – Alapszintű információkat tartalmaz a topológia teljesítményével kapcsolatban, időtartományokba rendezve.

        > [AZURE.NOTE] Egy adott időtartományt kiválasztva a lap más szakaszaiban található információk időtartománya megváltozik.

    * **Spoutok** – Alapszintű információkat tartalmaz a spoutokkal kapcsolatban, beleértve az egyes spoutok által visszaadott legutóbbi hibaüzenetet is.

    * **Boltok** – Alapszintű információkat tartalmaz a boltokkal kapcsolatban.

    * **Topológiakonfiguráció** – Részletes információkat tartalmaz a topológia konfigurációjával kapcsolatban.

    Ezen a lapon a következő topológiaműveletek szerepelnek:

    * **Aktiválás** – Folytatja az inaktivált topológia feldolgozását.

    * **Inaktiválás** – Megszakítja a futó topológiát.

    * **Visszaegyensúlyozás** – Beállítja a topológia párhuzamosságát. A fürtben található csomópontok számának megváltoztatását követően újra ki kell egyensúlyozni a futó topológiákat. Ez lehetővé teszi a topológia párhuzamosságának beállítását a fürtben található csomópontok számának növekedése/csökkenése kiegyensúlyozása érdekében. További információ: [Understanding the parallelism of a Storm topology](http://storm.apache.org/documentation/Understanding-the-parallelism-of-a-Storm-topology.html) (A Storm-topológia párhuzamosságának ismertetése).

    * **Törlés** – A Storm-topológia leállítása bizonyos időtúllépést követően.

3. Válassza ki a jelen lapon található **Spoutok** vagy **Boltok** szakaszok bejegyzéseinek egyikét. Ekkor információk jelennek meg a kiválasztott összetevővel kapcsolatban.

    ![A Storm irányítópultja a kiválasztott összetevőkkel kapcsolatos információkkal.](./media/hdinsight-apache-storm-tutorial-get-started-linux/component-summary.png)

    Ezen a lapon az alábbi információk találhatóak meg:

    * **Spout-/Bolt-statisztikák** – Alapszintű információkat tartalmaz az egyes összetevők teljesítményével kapcsolatban, időtartományokba rendezve.

        > [AZURE.NOTE] Egy adott időtartományt kiválasztva a lap más szakaszaiban található információk időtartománya megváltozik.

    * **Beviteli statisztikák** (csak boltok esetében) – Információkat tartalmaz a bolt által feldolgozott adatokat előállító összetevőkről.

    * **Kimeneti statisztikák** – Információkat tartalmaz a bolt által kibocsátott adatokról.

    * **Végrehajtók** – Információkat tartalmaz a jelen összetevő példányairól.

    * **Hibák** – A jelen összetevő által visszaadott hibaüzenetek.

4. Egy adott spout vagy bolt részletes adatainak megtekintésekor az összetevők adott példánya részletes adatainak megtekintéséhez jelöljön ki egy bejegyzést a **Port** oszlopból az **Executors** (Végrehajtók) szakaszban.

        2015-01-27 14:18:02 b.s.d.task [INFO] Emitting: split default ["with"]
        2015-01-27 14:18:02 b.s.d.task [INFO] Emitting: split default ["nature"]
        2015-01-27 14:18:02 b.s.d.executor [INFO] Processing received message source: split:21, stream: default, id: {}, [snow]
        2015-01-27 14:18:02 b.s.d.task [INFO] Emitting: count default [snow, 747293]
        2015-01-27 14:18:02 b.s.d.executor [INFO] Processing received message source: split:21, stream: default, id: {}, [white]
        2015-01-27 14:18:02 b.s.d.task [INFO] Emitting: count default [white, 747293]
        2015-01-27 14:18:02 b.s.d.executor [INFO] Processing received message source: split:21, stream: default, id: {}, [seven]
        2015-01-27 14:18:02 b.s.d.task [INFO] Emitting: count default [seven, 1493957]

    Ebből láthatja, hogy a szövegben a **hét** szó 1 493 957 alkalommal fordul elő. Az előfordulások számlálása a topológia indításától kezdve történik.

##A topológia leállítása

Lépjen vissza a **Topology summary** (Topológia összegzése) lapra a word-count topológiához, majd válassza a **Kill** (Törlés) gombot a **Topology actions** (Topológiaműveletek) szakaszban. Amikor a rendszer kéri, adjon meg 10 másodperces értéket a topológia leállítása előtti várakozási időként. Az időtúllépés lejáratát követően az adott topológia már nem jelenik meg az irányítópult **Storm UI** (Storm felhasználói felülete) szakaszában.

##A fürt törlése

[AZURE.INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

##<a id="next"></a>Következő lépések

Ebben az Apache Storm-oktatóanyagban a Storm Starter használatával sajátította el Storm on HDInsight-fürt létrehozását, valamint a Storm irányítópultjának kezelését a Storm-topológiák üzembe helyezéséhez, figyeléséhez és felügyeletéhez. A következő szakaszban a [Java-alapú topológiák fejlesztését ismertetjük Maven használatával](hdinsight-storm-develop-java-topology.md).

Ha már ismeri a Java-alapú topológiák fejlesztését, és egy meglévő topológiát kíván üzembe helyezni a HDInsighton, tekintse meg a következő cikket: [HDInsighton futó Apache Storm-topológiák üzembe helyezése és kezelése](hdinsight-storm-deploy-monitor-topology-linux.md).

[apachestorm]: https://storm.incubator.apache.org
[stormdocs]: http://storm.incubator.apache.org/documentation/Documentation.html
[stormstarter]: https://github.com/apache/storm/tree/master/examples/storm-starter
[stormjavadocs]: https://storm.incubator.apache.org/apidocs/
[azureportal]: https://manage.windowsazure.com/
[hdinsight-provision]: hdinsight-provision-clusters.md
[preview-portal]: https://portal.azure.com/


<!--HONumber=sep16_HO1-->


