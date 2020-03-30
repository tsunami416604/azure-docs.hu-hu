---
title: Storm hibaelhárítása az Azure HDInsight használatával
description: Válaszok az Apache Storm Azure HDInsight használatával kapcsolatos gyakori kérdésekre.
keywords: Azure HDInsight, Storm, GYAKORI KÉRDÉSEK, hibaelhárítási útmutató, gyakori problémák
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 11/08/2019
ms.custom: seodec18
ms.openlocfilehash: b51b2c21fd9256c93f6947386a48336af2b75d88
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79271927"
---
# <a name="troubleshoot-apache-storm-by-using-azure-hdinsight"></a>Az Apache Storm hibáinak elhárítása az Azure HDInsight használatával

Ismerje meg a legfontosabb problémákat és azok megoldásait az [Apache Storm](https://storm.apache.org/) rakományokkal való munkához az [Apache Ambari-ban.](https://ambari.apache.org/)

## <a name="how-do-i-access-the-storm-ui-on-a-cluster"></a>Hogyan érhetem el a Storm felhasználói felületét egy fürtön?

A Storm felhasználói felületét két lehetőség közül választhat:

### <a name="apache-ambari-ui"></a>Apache Ambari felhasználói felület

1. Nyissa meg az Ambari irányítópultot.
2. A szolgáltatások listájában válassza a **Storm**lehetőséget.
3. A **Gyorshivatkozások** menüben válassza a **Storm UI parancsot.**

### <a name="direct-link"></a>Közvetlen kapcsolat

A Storm felhasználói felületét a következő URL-címen érheti el:

`https://<cluster DNS name>/stormui`

Például: `https://stormcluster.azurehdinsight.net/stormui`

## <a name="how-do-i-transfer-storm-event-hub-spout-checkpoint-information-from-one-topology-to-another"></a>Hogyan vihetem át a Storm eseményközpont kifolyó ellenőrzőpont-adatait az egyik topológiából a másikba?

Ha olyan topológiákat fejleszt ki, amelyek az Azure Event Hubs-ból a HDInsight Storm eseményközpont-kimondó .jar fájl használatával olvasnak, telepítenie kell egy olyan topológiát, amelynek ugyanaz a neve egy új fürtön. Azonban meg kell őriznie az ellenőrzőpont-adatokat, amelyek véglegesítették az [Apache ZooKeeper](https://zookeeper.apache.org/) a régi fürtön.

### <a name="where-checkpoint-data-is-stored"></a>Az ellenőrzőpont-adatok tárolásának helye

Az eltolások ellenőrzőpont-adatait a ZooKeeper eseményközpont-kifolyója két gyökérútvonalon tárolja:

- A nem tranzakciós kifolyó ellenőrzőpontok a ban `/eventhubspout`tárolódnak.

- A tranzakciós kifolyó ellenőrzőpont-adatait a rendszer tárolja a ban. `/transactional`

### <a name="how-to-restore"></a>Hogyan lehet visszaállítani

Ha le szeretné venni azokat a parancsfájlokat és kódtárakat, amelyek segítségével adatokat exportálanak a ZooKeeper alkalmazásból, majd új néven importálja az adatokat a ZooKeeper alkalmazásba, olvassa el a [HDInsight Storm példáit.](https://github.com/hdinsight/hdinsight-storm-examples/tree/master/tools/zkdatatool-1.0)

A lib mappában .jar fájlok találhatók, amelyek tartalmazzák az exportálási/importálási művelet megvalósítását. A bash mappa rendelkezik egy példa parancsfájlt, amely bemutatja, hogyan kell exportálni az adatokat a ZooKeeper kiszolgálóról a régi fürtön, majd importálja vissza az új fürt ZooKeeper kiszolgálójára.

Futtassa a [stormmeta.sh](https://github.com/hdinsight/hdinsight-storm-examples/blob/master/tools/zkdatatool-1.0/bash/stormmeta.sh) parancsfájlt a ZooKeeper csomópontokból az exportáláshoz, majd az adatok importálásához. Frissítse a parancsfájlt a megfelelő Hortonworks Data Platform (HDP) verzióra. (Azon dolgozunk, hogy ezeket a parancsfájlokat általánossá tegyék a HDInsightban. Az általános parancsfájlok a fürt bármely csomópontjáról futtathatók a felhasználó módosításai nélkül.)

Az exportálási parancs a metaadatokat egy Apache Hadoop distributed File System (HDFS) elérési útjára írja (az Azure Blob Storage vagy az Azure Data Lake Storage szolgáltatásban) a beállított helyen.

### <a name="examples"></a>Példák

#### <a name="export-offset-metadata"></a>Eltolás metaadatainak exportálása

1. Az SSH használatával nyissa meg a ZooKeeper fürtet azon a fürtön, amelyből az ellenőrzőpont-eltolást exportálni kell.
2. Futtassa a következő parancsot (a HDP-verziókarakterlánc frissítése `/stormmetadta/zkdata` után) a ZooKeeper eltolási adatok exportálásához a HDFS elérési útjára:

    ```apache
    java -cp ./*:/etc/hadoop/conf/*:/usr/hdp/2.5.1.0-56/hadoop/*:/usr/hdp/2.5.1.0-56/hadoop/lib/*:/usr/hdp/2.5.1.0-56/hadoop-hdfs/*:/usr/hdp/2.5.1.0-56/hadoop-hdfs/lib/*:/etc/failover-controller/conf/*:/etc/hadoop/* com.microsoft.storm.zkdatatool.ZkdataImporter export /eventhubspout /stormmetadata/zkdata
    ```

#### <a name="import-offset-metadata"></a>Eltolásmetaadatainak importálása

1. Az SSH használatával nyissa meg a ZooKeeper fürta a fürtön, amelyből az ellenőrzőpont-eltolást importálni kell.
2. Futtassa a következő parancsot (a HDP-verziókarakterlánc frissítése után) a `/stormmetadata/zkdata` ZooKeeper eltolási adatok importálásához a HDFS elérési útjáról a célfürt ZooKeeper kiszolgálójára:

    ```apache
    java -cp ./*:/etc/hadoop/conf/*:/usr/hdp/2.5.1.0-56/hadoop/*:/usr/hdp/2.5.1.0-56/hadoop/lib/*:/usr/hdp/2.5.1.0-56/hadoop-hdfs/*:/usr/hdp/2.5.1.0-56/hadoop-hdfs/lib/*:/etc/failover-controller/conf/*:/etc/hadoop/* com.microsoft.storm.zkdatatool.ZkdataImporter import /eventhubspout /home/sshadmin/zkdata
    ```

#### <a name="delete-offset-metadata-so-that-topologies-can-start-processing-data-from-the-beginning-or-from-a-timestamp-that-the-user-chooses"></a>Az eltolási metaadatok törlése, hogy a topológiák az elejétől vagy a felhasználó által választott időbélyegből kezdhessék meg az adatok feldolgozását

1. Az SSH használatával nyissa meg a ZooKeeper fürta a fürtön, amelyből az ellenőrzőpont eltolását törölni kell.
2. Futtassa a következő parancsot (a HDP verziókarakterlánc frissítése után) az aktuális fürt összes ZooKeeper eltolási adatának törléséhez:

    ```apache
    java -cp ./*:/etc/hadoop/conf/*:/usr/hdp/2.5.1.0-56/hadoop/*:/usr/hdp/2.5.1.0-56/hadoop/lib/*:/usr/hdp/2.5.1.0-56/hadoop-hdfs/*:/usr/hdp/2.5.1.0-56/hadoop-hdfs/lib/*:/etc/failover-controller/conf/*:/etc/hadoop/* com.microsoft.storm.zkdatatool.ZkdataImporter delete /eventhubspout
    ```

## <a name="how-do-i-locate-storm-binaries-on-a-cluster"></a>Hogyan találhatom meg a Storm bináris fájlokat egy fürtön?

Az aktuális HDP-verem storm `/usr/hdp/current/storm-client`bináris fájljai a rendszerben vannak. A hely ugyanaz mind a fő csomópontok, mind a feldolgozó csomópontok esetében.

A /usr/hdp -ben több bináris fájl is lehet `/usr/hdp/2.5.0.1233/storm`adott HDP-verziókhoz (például ). A `/usr/hdp/current/storm-client` mappa a fürtön futó legújabb verzióval van csatolva.

További információ: [Csatlakozás HDInsight-fürthöz SSH](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-linux-use-ssh-unix) és [Apache Storm](https://storm.apache.org/)használatával.

## <a name="how-do-i-determine-the-deployment-topology-of-a-storm-cluster"></a>Hogyan állapítható meg a Storm-fürt központi telepítési topológiája?

Először azonosítsa a HDInsight Storm alkalmazással telepített összes összetevőt. A Storm-fürt négy csomópontkategóriából áll:

* Átjárócsomópontok
* Fő csomópontok
* ZooKeeper csomópontok
* Dolgozó csomópontok

### <a name="gateway-nodes"></a>Átjárócsomópontok

Az átjárócsomópont egy átjáró és fordított proxyszolgáltatás, amely nyilvános hozzáférést biztosít egy aktív Ambari felügyeleti szolgáltatáshoz. Azt is kezeli Ambari vezető választás.

### <a name="head-nodes"></a>Fő csomópontok

A viharfőcsomópontok a következő szolgáltatásokat futtatják:
* Nimbus
* Ambari szerver
* Ambari metrikák kiszolgáló
* Ambari mérőszámok gyűjtő
 
### <a name="zookeeper-nodes"></a>ZooKeeper csomópontok

A HDInsight háromcsomópontos ZooKeeper kvórummal érkezik. A kvórum mérete rögzített, és nem konfigurálható újra.

A fürt ben lévő storm szolgáltatások úgy vannak konfigurálva, hogy automatikusan használják a ZooKeeper kvórumot.

### <a name="worker-nodes"></a>Dolgozó csomópontok

A storm-feldolgozó csomópontok a következő szolgáltatásokat futtatják:
* Felügyelő
* Feldolgozó Java virtuális gépek (JVM), topológiák futtatásához
* Ambari ügynök

## <a name="how-do-i-locate-storm-event-hub-spout-binaries-for-development"></a>Hogyan találhatom meg a Storm eseményközpont-kifolyó bináris fájljait a fejlesztéshez?

A Storm eseményközpont-kifolyó .jar fájlok topológiával való használatáról az alábbi forrásokban talál további információt.

### <a name="java-based-topology"></a>Java-alapú topológia

[Események feldolgozása az Azure Event Hubs-ból az Apache Storm segítségével a HDInsight (Java) szolgáltatással](https://github.com/Azure-Samples/hdinsight-java-storm-eventhub)

### <a name="c-based-topology-mono-on-hdinsight-34-linux-storm-clusters"></a>C#-alapú topológia (Monó a HDInsight 3.4+ Linux Storm fürtökön)

[Események feldolgozása az Azure Event Hubs-ból az Apache Storm segítségével a HDInsighton (C#)](https://docs.microsoft.com/azure/hdinsight/hdinsight-storm-develop-csharp-event-hub-topology)

### <a name="latest-apache-storm-event-hub-spout-binaries-for-hdinsight-35-linux-storm-clusters"></a>Legújabb Apache Storm eseményközpont bináris fájlok HDInsight 3.5+ Linux Storm fürtökhöz

A HDInsight 3.5+ Linux Storm-fürtökkel működő legújabb Storm eseményközpont-kifolyó használatáról az [mvn-repo readme fájlban olvashat.](https://github.com/hdinsight/mvn-repo/blob/master/README.md)

### <a name="source-code-examples"></a>Példák forráskódra

Példák [az](https://github.com/Azure-Samples/hdinsight-java-storm-eventhub) Azure Event Hubról való olvasásra és írásra egy Apache Storm-topológia használatával (Java nyelven írva) egy Azure HDInsight-fürtön.

## <a name="how-do-i-locate-storm-log4j-2-configuration-files-on-clusters"></a>Hogyan találhatom meg a Storm Log4J 2 konfigurációs fájljait a fürtökön?

Az [Apache Log4j 2](https://logging.apache.org/log4j/2.x/) konfigurációs fájljainak azonosítása a Storm szolgáltatásokhoz.

### <a name="on-head-nodes"></a>A főcsomópontokon

A Nimbus Log4J konfigurációja a rendszerből származik. `/usr/hdp/\<HDP version>/storm/log4j2/cluster.xml`

### <a name="on-worker-nodes"></a>Munkavégző csomópontokon

A felügyelő Log4J konfigurációját a rendszer olvassa be a rendszerből. `/usr/hdp/\<HDP version>/storm/log4j2/cluster.xml`

A munkavégző Log4J konfigurációs fájlja a programból származik. `/usr/hdp/\<HDP version>/storm/log4j2/worker.xml`

Példák:`/usr/hdp/2.6.0.2-76/storm/log4j2/cluster.xml`
`/usr/hdp/2.6.0.2-76/storm/log4j2/worker.xml`

---

## <a name="not-a-leader-exception"></a>Nincs vezető kivétel

A topológia elküldésekor a felhasználó a következőhöz `Topology submission exception, cause not a leader, the current leader is NimbusInfo`hasonló hibaüzenetet kaphat: .

A feloldáshoz előfordulhat, hogy a felhasználónak be kell nyújtania egy jegyet a csomópontok újraindításához/újraindításához. További információ: [https://community.hortonworks.com/content/supportkb/150287/error-ignoring-exception-while-trying-to-get-leade.html](https://community.hortonworks.com/content/supportkb/150287/error-ignoring-exception-while-trying-to-get-leade.html).

---

## <a name="next-steps"></a>További lépések

Ha nem látta a problémát, vagy nem tudja megoldani a problémát, további támogatásért látogasson el az alábbi csatornák egyikébe:

- Válaszokat kaphat az Azure szakértőitől az [Azure közösségi támogatásán](https://azure.microsoft.com/support/community/)keresztül.

- Lépjen [@AzureSupport](https://twitter.com/azuresupport) kapcsolatba a hivatalos Microsoft Azure-fiókkal az ügyfélélmény javítása érdekében. Az Azure-közösség összekapcsolása a megfelelő erőforrásokkal: válaszok, támogatás és szakértők.

- Ha további segítségre van szüksége, támogatási kérelmet nyújthat be az [Azure Portalról.](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/) Válassza a **menüsor Támogatás parancsát,** vagy nyissa meg a **Súgó + támogatási** központot. További információkért tekintse [át az Azure-támogatási kérelem létrehozása című áttekintést.](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request) Az Előfizetés-kezelés hez és a számlázási támogatáshoz való hozzáférés a Microsoft Azure-előfizetésrészét képezi, a technikai támogatást pedig az [Azure-támogatási csomagok](https://azure.microsoft.com/support/plans/)egyike biztosítja.
