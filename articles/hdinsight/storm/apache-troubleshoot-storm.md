---
title: Az Azure HDInsight Storm hibaelhárítása
description: Az Azure HDInsight alapú Apache Storm használatával kapcsolatos gyakori kérdésekre adott válaszok.
keywords: Az Azure HDInsight, a Storm, gyakori kérdések hibaelhárítási útmutató gyakori problémák
services: hdinsight
ms.service: hdinsight
author: jasonwhowell
ms.author: jasonwhowell
editor: jasonwhowell
ms.topic: conceptual
ms.date: 11/2/2017
ms.openlocfilehash: c90c793949f1c37d18d0227fd9407e65c332ed0c
ms.sourcegitcommit: 387d7edd387a478db181ca639db8a8e43d0d75f7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/10/2018
ms.locfileid: "42057226"
---
# <a name="troubleshoot-storm-by-using-azure-hdinsight"></a>Az Azure HDInsight Storm hibaelhárítása

Ismerje meg a leggyakoribb problémák és azok megoldásait az Apache Ambari az Apache Storm hasznos adatokkal végzett munka.

## <a name="how-do-i-access-the-storm-ui-on-a-cluster"></a>Hogyan férhetek hozzá a Storm felhasználói felülete egy fürtön?
A Storm felhasználói felülete eléréséhez egy böngészőben két lehetősége van:

### <a name="ambari-ui"></a>Az Ambari felhasználói felületén
1. Nyissa meg az Ambari irányítópultot.
2. A szolgáltatások listájában jelölje ki **Storm**.
3. Az a **Gyorshivatkozások** menüjében válassza **Storm felhasználói felülete**.

### <a name="direct-link"></a>Közvetlen hivatkozás
A Storm felhasználói felülete a következő URL-címen érhető el:

https://\<fürt DNS-név\>/stormui

Példa:

 https://stormcluster.azurehdinsight.net/stormui

## <a name="how-do-i-transfer-storm-event-hub-spout-checkpoint-information-from-one-topology-to-another"></a>Hogyan ruházhatom át Storm event hub spout ellenőrzőpont információk adatainak egy másikra?

Olvassa el az Azure Event Hubsból topológiák fejlesztése során segítségével a HDInsight Storm event hub spout a .jar-fájl, telepítenie kell egy-egy új fürtön ugyanazzal a névvel rendelkező topológia. Azonban meg kell őrizni az ellenőrzőpont-adatok, amely az Apache ZooKeeper véglegesítve lett a régi fürtön.

### <a name="where-checkpoint-data-is-stored"></a>Ellenőrzőpont-adatok tárolására
Az event hub spout, ZooKeeper a gyökér elérési utat a tárol az eltolások ellenőrzőpont adatokat:
- Nem tranzakciós spout ellenőrzőpontok /eventhubspout vannak tárolva.
- Tranzakciós spout ellenőrzőpont-adatok be / tranzakciós tárolja.

### <a name="how-to-restore"></a>Visszaállítása
A szkriptek és -kódtárak együttese segítségével exportálhatja az adatokat ZooKeeper és majd importálja vissza a ZooKeeper, egy új nevet az adatok lekéréséhez lásd: [HDInsight Storm-példák](https://github.com/hdinsight/hdinsight-storm-examples/tree/master/tools/zkdatatool-1.0).

Lib mappájába rendelkezik a .jar fájlokat, amelyek tartalmazzák az exportálási/importálási művelet végrehajtására. A bash-mappába, amely azt ismerteti, hogyan adatok exportálása a ZooKeeper-kiszolgáló a régi fürtön, és ezután importálja vissza a ZooKeeper-kiszolgáló, az új fürtön példa parancsfájl rendelkezik.

Futtassa a [stormmeta.sh](https://github.com/hdinsight/hdinsight-storm-examples/blob/master/tools/zkdatatool-1.0/bash/stormmeta.sh) parancsfájlt a ZooKeeper-csomópontok exportálhatja és importálhatja az adatokat. Frissítse a parancsfájlt a megfelelő Hortonworks Data Platform (HDP) verzióra. (Is dolgozunk a HDInsight általános így ezeket a parancsfájlokat. Az általános parancsfájlok futtatásához bármely olyan csomópontról módosítások nélkül a fürtön a felhasználó.)

Az exportálási parancs egy Apache Hadoop elosztott fájlrendszer (HDFS) útvonal (az Azure Blob Storage vagy az Azure Data Lake Store áruházban) egy Ön által beállított helyen a metaadatokat ír.

### <a name="examples"></a>Példák

#### <a name="export-offset-metadata"></a>Eltolási metaadatainak exportálásához
1. SSH-val, amelyről az ellenőrzőpont eltolás exportálni kell a fürtön nyissa meg a ZooKeeper-fürtön.
2. Futtassa a következő parancsot (miután frissítette a HDP verzió-karakterlánc) ZooKeeper eltolási adatainak exportálása a /stormmetadta/zkdata HDFS elérési útja:

    ```apache   
    java -cp ./*:/etc/hadoop/conf/*:/usr/hdp/2.5.1.0-56/hadoop/*:/usr/hdp/2.5.1.0-56/hadoop/lib/*:/usr/hdp/2.5.1.0-56/hadoop-hdfs/*:/usr/hdp/2.5.1.0-56/hadoop-hdfs/lib/*:/etc/failover-controller/conf/*:/etc/hadoop/* com.microsoft.storm.zkdatatool.ZkdataImporter export /eventhubspout /stormmetadata/zkdata
    ```

#### <a name="import-offset-metadata"></a>Eltolási metaadatainak importálása
1. SSH-val, amelyről az ellenőrzőpont eltolás importálni kell a fürtön nyissa meg a ZooKeeper-fürtön.
2. Futtassa a következő parancsot (miután frissítette a HDP verzió-karakterlánc) ZooKeeper eltolási adatok a HDFS elérési útja /stormmetadata/zkdata a ZooKeeper-kiszolgáló a célfürtön importálhatja:

    ```apache
    java -cp ./*:/etc/hadoop/conf/*:/usr/hdp/2.5.1.0-56/hadoop/*:/usr/hdp/2.5.1.0-56/hadoop/lib/*:/usr/hdp/2.5.1.0-56/hadoop-hdfs/*:/usr/hdp/2.5.1.0-56/hadoop-hdfs/lib/*:/etc/failover-controller/conf/*:/etc/hadoop/* com.microsoft.storm.zkdatatool.ZkdataImporter import /eventhubspout /home/sshadmin/zkdata
    ```
   
#### <a name="delete-offset-metadata-so-that-topologies-can-start-processing-data-from-the-beginning-or-from-a-timestamp-that-the-user-chooses"></a>Topológiák megkezdheti az adatok feldolgozását, az elejétől, illetve időbélyeg, amely a felhasználó úgy dönt, hogy a eltolási metaadatok törlése
1. SSH-val, amelyről az ellenőrzőpont eltolás törölni kell a fürtön nyissa meg a ZooKeeper-fürtön.
2. Futtassa a következő parancsot (HDP verzió-karakterlánca frissítése) után törli az összes ZooKeeper eltolási adatokat az aktuális fürt:

    ```apache
    java -cp ./*:/etc/hadoop/conf/*:/usr/hdp/2.5.1.0-56/hadoop/*:/usr/hdp/2.5.1.0-56/hadoop/lib/*:/usr/hdp/2.5.1.0-56/hadoop-hdfs/*:/usr/hdp/2.5.1.0-56/hadoop-hdfs/lib/*:/etc/failover-controller/conf/*:/etc/hadoop/* com.microsoft.storm.zkdatatool.ZkdataImporter delete /eventhubspout
    ```

## <a name="how-do-i-locate-storm-binaries-on-a-cluster"></a>Hogyan találom meg a Storm bináris egy fürtön?
A Storm bináris az aktuális HDP verem /usr/hdp/current/storm-client találhatók. A hely az azonos fő csomópontból és feldolgozó csomópontokat.
 
Előfordulhat, hogy több bináris fájljait (például /usr/hdp/2.5.0.1233/storm) /usr/hdp a HDP verzióját. A /usr/hdp/current/storm-client mappa nem a legújabb verzióra a fürtön futó symlinked.

További információkért lásd: [egy HDInsight-fürthöz SSH használatával csatlakozhat](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-linux-use-ssh-unix) és [Storm](http://storm.apache.org/).
 
## <a name="how-do-i-determine-the-deployment-topology-of-a-storm-cluster"></a>Hogyan állapítható meg a telepítési topológia a Storm-fürt?
Először azonosítsa az összes telepített összetevőinek támogatásához a HDInsight Storm. A Storm-fürtök négy csomópont kategória áll:

* Az átjárócsomópontok
* Átjárócsomópontok
* ZooKeeper-csomópontok
* Munkavégző csomópontok
 
### <a name="gateway-nodes"></a>Az átjárócsomópontok
Átjáró csomópontnak számít egy átjárót, és a fordított proxy szolgáltatás, amely lehetővé teszi a nyilvános hozzáférést egy aktív Ambari felügyeleti szolgáltatáshoz. Az Ambari vezetőválasztási is kezeli.
 
### <a name="head-nodes"></a>Átjárócsomópontok
A Storm fő csomópontok futtassa a következő szolgáltatásokat:
* Nimbus
* Az Ambari kiszolgáló
* Ambari-metrikák kiszolgáló
* Az Ambari metrikákat gyűjtő
 
### <a name="zookeeper-nodes"></a>ZooKeeper-csomópontok
HDInsight egy három csomópontos ZooKeeper kvórum tartalmaz. A kvórum mérete rögzített, és nem állítható át.
 
A Storm-szolgáltatásokat a fürt automatikusan a ZooKeeper kvórum használatára vannak konfigurálva.
 
### <a name="worker-nodes"></a>Munkavégző csomópontok
A Storm munkavégző csomópontok futtassa a következő szolgáltatásokat:
* Felügyeleti
* Feldolgozó Java virtuális gépek (JVMs), a futó topológiák
* Ambari-ügynök
 
## <a name="how-do-i-locate-storm-event-hub-spout-binaries-for-development"></a>Hogyan találom meg a Storm event hub spout bináris fejlesztéshez?
 
A topológia a Storm event hub spout .jar fájlokat használatával kapcsolatos további információkért lásd a következőket.
 
### <a name="java-based-topology"></a>Java-alapú topológia
[Az Azure Event Hubs-eseményközpontok eseményeinek feldolgozása a HDInsight alatt futó Stormmal (Java)](https://docs.microsoft.com/azure/hdinsight/hdinsight-storm-develop-java-event-hub-topology)
 
### <a name="c-based-topology-mono-on-hdinsight-34-linux-storm-clusters"></a>C#-alapú topológia (HDInsight 3.4-es + Linux Storm-fürtök a Mono)
[Az Azure Event Hubs-eseményközpontok eseményeinek feldolgozása a HDInsight alatt futó Stormmal (C#)](https://docs.microsoft.com/azure/hdinsight/hdinsight-storm-develop-csharp-event-hub-topology)
 
### <a name="latest-storm-event-hub-spout-binaries-for-hdinsight-35-linux-storm-clusters"></a>Legújabb Storm event hub spout bináris fájljai, a HDInsight 3.5-ös + Linux Storm-fürtök
A legújabb Storm event hub spout, amely együttműködik a HDInsight 3.5-ös + Linux Storm-fürtök használatával kapcsolatban lásd: a mvn-tárház [információs fájl](https://github.com/hdinsight/mvn-repo/blob/master/README.md).
 
### <a name="source-code-examples"></a>Forrás hitelesítésikód-példák
Lásd: [példák](https://github.com/Azure-Samples/hdinsight-java-storm-eventhub) bemutatja, hogyan olvashatja és írhatja az Azure Event Hubs egy Apache Storm-topológia (Java nyelven írt) használatával az Azure HDInsight-fürtön.
 
## <a name="how-do-i-locate-storm-log4j-configuration-files-on-clusters"></a>Hogyan találom meg a Storm Log4J konfigurációs fájlok fürtökön?
 
Apache Log4J konfigurációs fájlok Storm szolgáltatások azonosításához.
 
### <a name="on-head-nodes"></a>A fő csomópontok
A Nimbus Log4J konfigurációjának olvasása az/usr/hdp/\<HDP verzió\>/storm/log4j2/cluster.xml.
 
### <a name="on-worker-nodes"></a>A feldolgozó csomópontok
A felügyelő Log4J konfigurációját olvasni usr/hdp/\<HDP verzió\>/storm/log4j2/cluster.xml.
 
A feldolgozó Log4J konfigurációs fájl olvasása az/usr/hdp/\<HDP verzió\>/storm/log4j2/worker.xml.
 
Példák: /usr/hdp/2.6.0.2-76/storm/log4j2/cluster.xml /usr/hdp/2.6.0.2-76/storm/log4j2/worker.xml

### <a name="see-also"></a>Lásd még:
[Hibaelhárítás az Azure HDInsight segítségével](../../hdinsight/hdinsight-troubleshoot-guide.md)
