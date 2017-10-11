---
title: "Azure HDInsight alatt futó Storm hibaelhárításáról |} Microsoft Docs"
description: "Az Azure HDInsight alatt futó Apache Storm használatával kapcsolatos gyakori kérdésekre adott válaszok."
keywords: "Az Azure HDInsight alatt futó Storm, gyakori kérdések hibaelhárítási útmutatója, gyakori problémák"
services: Azure HDInsight
documentationcenter: na
author: raviperi
manager: 
editor: 
ms.assetid: 74E51183-3EF4-4C67-AA60-6E12FAC999B5
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 7/7/2017
ms.author: raviperi
ms.openlocfilehash: 70a3d762431d90acdd6ed2a432a569f34d0ce447
ms.sourcegitcommit: 18ad9bc049589c8e44ed277f8f43dcaa483f3339
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/29/2017
---
# <a name="troubleshoot-storm-by-using-azure-hdinsight"></a>Azure HDInsight alatt futó Storm hibaelhárításáról

További tudnivalók a legfőbb problémákat és azok megoldásait Apache Ambari az Apache Storm hasznos adatot való munkához.

## <a name="how-do-i-access-the-storm-ui-on-a-cluster"></a>Hogyan érhetem el az egy fürtön a Storm felhasználói felülete
A Storm felhasználói felülete eléréséhez a böngészőben két lehetőség közül választhat:

### <a name="ambari-ui"></a>Ambari felhasználói felület
1. Nyissa meg az Ambari irányítópultot.
2. A szolgáltatások listájában jelölje ki **Storm**.
3. Az a **Gyorshivatkozások** menü **Storm felhasználói felülete**.

### <a name="direct-link"></a>A közvetlen hivatkozás
A Storm felhasználói felülete a következő URL-címen érhető el:

https://\<fürt DNS-név\>/stormui

Példa:

 https://stormcluster.azurehdinsight.NET/stormui

## <a name="how-do-i-transfer-storm-event-hub-spout-checkpoint-information-from-one-topology-to-another"></a>Hogyan tegye I átvitele Storm hub spout ellenőrzőpont eseményadatok egy topológia között

Amikor olvassa el az Azure Event Hubs topológiák fejlesztése a HDInsight alatt futó Storm eseményközpont használatával spout található .jar fájl, telepítenie kell egy új fürt meg a névvel rendelkező topológia. Azonban meg kell őrizni, hogy a régi fürtön Apache ZooKeeper lett véglegesített ellenőrzőpont-adatok.

### <a name="where-checkpoint-data-is-stored"></a>Ellenőrzőpont-adatok tárolására
Az eltolások ellenőrzőpont adatait a event hub spout, ZooKeeper a két legfelső szintű elérési útjában tárolja:
- Nem tranzakciós spout ellenőrzőpontokat /eventhubspout vannak tárolva.
- Be- / tranzakciós tranzakciós spout ellenőrzőpont adatait tárolja.

### <a name="how-to-restore"></a>Visszaállítása
Ahhoz, hogy a parancsfájlok és a szalagtárak, amelyekkel ZooKeeper kívüli adatok exportálása és majd importálja vissza az adatokat egy új nevű ZooKeeper, lásd: [HDInsight alatt futó Storm példák](https://github.com/hdinsight/hdinsight-storm-examples/tree/master/tools/zkdatatool-1.0).

A lib mappa rendelkezik .jar fájlok, amelyek tartalmazzák az exportálási/importálási művelet végrehajtására. A bash mappa rendelkezik egy példa a parancsfájl azt mutatja be, és adatok exportálása a ZooKeeper-kiszolgáló a régi fürtön, majd importálja vissza a ZooKeeper server az új fürtön.

Futtassa a [stormmeta.sh](https://github.com/hdinsight/hdinsight-storm-examples/blob/master/tools/zkdatatool-1.0/bash/stormmeta.sh) exportálása, majd az adatok importálása a ZooKeeper csomópontok parancsfájlt. Frissítse a parancsfájlt a megfelelő Hortonworks Data Platform (HDP) verzióra. (Jelenleg is dolgozunk a parancsfájlokat a Hdinsightban általános elvégzése. Az általános parancsfájlok bármely olyan csomópontról módosítások nélkül a fürtön a felhasználó futtathatja.)

A parancs a metaadatok ír egy Apache Hadoop elosztott fájlrendszerrel (HDFS) útvonal (az Azure Blob Storage vagy az Azure Data Lake Store áruházban), amely egy helyen.

### <a name="examples"></a>Példák

#### <a name="export-offset-metadata"></a>Az eltolási metaadatok exportálása
1. SSH segítségével nyissa meg a fürt, amelyből az ellenőrzőpont eltolás exportálni kell a ZooKeeper fürt.
2. A következő parancsot (miután frissítette a HDP verzió-karakterlánca) ZooKeeper eltolási adatainak exportálása a /stormmetadta/zkdata HDFS elérési útja:

    ```apache   
    java -cp ./*:/etc/hadoop/conf/*:/usr/hdp/2.5.1.0-56/hadoop/*:/usr/hdp/2.5.1.0-56/hadoop/lib/*:/usr/hdp/2.5.1.0-56/hadoop-hdfs/*:/usr/hdp/2.5.1.0-56/hadoop-hdfs/lib/*:/etc/failover-controller/conf/*:/etc/hadoop/* com.microsoft.storm.zkdatatool.ZkdataImporter export /eventhubspout /stormmetadata/zkdata
    ```

#### <a name="import-offset-metadata"></a>Az eltolási metaadatok importálása
1. SSH segítségével nyissa meg a fürt, amelyből az ellenőrzőpont eltolás exportálni kell a ZooKeeper fürt.
2. A következő parancsot (miután frissítette a HDP verzió-karakterlánca) ZooKeeper eltolási adatok importálása a HDFS elérési /stormmetadata/zkdata a ZooKeeper-kiszolgáló a célfürtön:

    ```apache
    java -cp ./*:/etc/hadoop/conf/*:/usr/hdp/2.5.1.0-56/hadoop/*:/usr/hdp/2.5.1.0-56/hadoop/lib/*:/usr/hdp/2.5.1.0-56/hadoop-hdfs/*:/usr/hdp/2.5.1.0-56/hadoop-hdfs/lib/*:/etc/failover-controller/conf/*:/etc/hadoop/* com.microsoft.storm.zkdatatool.ZkdataImporter import /eventhubspout /home/sshadmin/zkdata
    ```
   
#### <a name="delete-offset-metadata-so-that-topologies-can-start-processing-data-from-the-beginning-or-from-a-timestamp-that-the-user-chooses"></a>Így a topológiák adatainak feldolgozása elindíthatja az elejéről, vagy a felhasználó által kiválasztott időbélyeg eltolási metaadatok törlése
1. SSH segítségével nyissa meg a fürt, amelyből az ellenőrzőpont eltolás exportálni kell a ZooKeeper fürt.
2. A következő parancsot (HDP verzió-karakterlánca frissítése) után törli az összes ZooKeeper eltolási adatokat az aktuális fürt:

    ```apache
    java -cp ./*:/etc/hadoop/conf/*:/usr/hdp/2.5.1.0-56/hadoop/*:/usr/hdp/2.5.1.0-56/hadoop/lib/*:/usr/hdp/2.5.1.0-56/hadoop-hdfs/*:/usr/hdp/2.5.1.0-56/hadoop-hdfs/lib/*:/etc/failover-controller/conf/*:/etc/hadoop/* com.microsoft.storm.zkdatatool.ZkdataImporter delete /eventhubspout
    ```

## <a name="how-do-i-locate-storm-binaries-on-a-cluster"></a>Hogyan keresse meg a Storm bináris fürt
Az aktuális HDP verem Storm bináris /usr/hdp/current/storm-client szerepelnek. A hely az azonos átjárócsomópontokkal és munkavégző csomópontokhoz.
 
Előfordulhat, hogy több bináris fájljait (például /usr/hdp/2.5.0.1233/storm) /usr/hdp adott HDP verzióihoz. A /usr/hdp/current/storm-client mappa nem symlinked a legújabb verzióra, hogy fut a fürtön.

További információkért lásd: [egy HDInsight-fürthöz SSH használatával csatlakozhat](https://docs.microsoft.com/en-us/azure/hdinsight/hdinsight-hadoop-linux-use-ssh-unix) és [Storm](http://storm.apache.org/).
 
## <a name="how-do-i-determine-the-deployment-topology-of-a-storm-cluster"></a>Hogyan állapítható meg a telepítési topológia a Storm-fürt
Először azonosítsa a HDInsight alatt futó Storm, telepített összetevők. A Storm-fürt négy csomópont kategóriák áll:

* Átjárócsomópontok
* HEAD csomópontok
* ZooKeeper csomópontok
* Munkavégző csomópontokhoz
 
### <a name="gateway-nodes"></a>Átjárócsomópontok
Átjáró csomópont egy átjáró és a fordított proxy szolgáltatás, amely lehetővé teszi egy aktív Ambari felügyeleti szolgáltatás való nyilvános hozzáféréshez. Azt is kezeli az Ambari vezető választás.
 
### <a name="head-nodes"></a>HEAD csomópontok
A Storm átjárócsomópontokkal futtassa a következő szolgáltatásokat:
* Nimbus
* Ambari kiszolgáló
* Ambari metrikák kiszolgáló
* Ambari metrikákat gyűjtő
 
### <a name="zookeeper-nodes"></a>ZooKeeper csomópontok
HDInsight egy három csomópontos ZooKeeper kvórum tartalmaz. A kvórum mérete rögzített, és nem kell újrakonfigurálni.
 
Storm szolgáltatás a fürt automatikusan a ZooKeeper kvórum használatára van konfigurálva.
 
### <a name="worker-nodes"></a>Munkavégző csomópontokhoz
A Storm munkavégző csomópontokhoz futtassa a következő szolgáltatásokat:
* Felügyeleti
* Munkavégző Java virtuális gépek (JVMs), a futó topológiák
* Ambari ügynök
 
## <a name="how-do-i-locate-storm-event-hub-spout-binaries-for-development"></a>Hogyan keresse meg a Storm event hub spout bináris fejlesztési
 
A topológia a Storm event hub spout .jar fájlok használatával kapcsolatos további információkért lásd a következőket.
 
### <a name="java-based-topology"></a>Java-alapú topológia
[Az Azure Event Hubs (Java) futó Storm eseményeinek](https://docs.microsoft.com/en-us/azure/hdinsight/hdinsight-storm-develop-java-event-hub-topology)
 
### <a name="c-based-topology-mono-on-hdinsight-34-linux-storm-clusters"></a>C#-alapú topológia (a HDInsight 3.4 + Linux Storm-fürtök monó)
[Az Azure Event Hubs-eseményközpontok eseményeinek feldolgozása a HDInsight alatt futó Stormmal (C#)](https://docs.microsoft.com/en-us/azure/hdinsight/hdinsight-storm-develop-csharp-event-hub-topology)
 
### <a name="latest-storm-event-hub-spout-binaries-for-hdinsight-35-linux-storm-clusters"></a>Legújabb Storm event hub spout binárist 3.5 + HDInsight Linux Storm-fürtök
A legújabb Storm event hub spout, amely kompatibilis a HDInsight 3.5 + Linux Storm-fürtök használatával kapcsolatban lásd: a mvn-tárház [információs fájl](https://github.com/hdinsight/mvn-repo/blob/master/README.md).
 
### <a name="source-code-examples"></a>Forrás-kódpéldák
Lásd: [példák](https://github.com/Azure-Samples/hdinsight-java-storm-eventhub) bemutatja, hogyan olvashatja és írhatja az Azure Event Hubs egy Apache Storm-topológia (Java nyelven írt) használata az Azure HDInsight-fürtöt.
 
## <a name="how-do-i-locate-storm-log4j-configuration-files-on-clusters"></a>Hogyan keresse meg a Storm Log4J konfigurációs fájlok fürtökön
 
Apache Log4J konfigurációs fájlok Storm szolgáltatások azonosításához.
 
### <a name="on-head-nodes"></a>Az átjárócsomópontokkal
A Nimbus Log4J konfiguráció olvasása az/usr/hdp/\<HDP verzió\>/storm/log4j2/cluster.xml.
 
### <a name="on-worker-nodes"></a>A feldolgozó csomópontok
A felügyelő Log4J konfiguráció olvasása az/usr/hdp/\<HDP verzió\>/storm/log4j2/cluster.xml.
 
A munkavégző Log4J konfigurációs fájl olvasása az/usr/hdp/\<HDP verzió\>/storm/log4j2/worker.xml.
 
Példák: /usr/hdp/2.6.0.2-76/storm/log4j2/cluster.xml /usr/hdp/2.6.0.2-76/storm/log4j2/worker.xml

