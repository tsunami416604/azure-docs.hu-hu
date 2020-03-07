---
title: Az Azure HDInsight Storm hibaelhárítása
description: Az Azure HDInsight alapú Apache Storm használatával kapcsolatos gyakori kérdésekre adott válaszok.
keywords: Az Azure HDInsight, a Storm, gyakori kérdések hibaelhárítási útmutató gyakori problémák
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 11/08/2019
ms.custom: seodec18
ms.openlocfilehash: b51b2c21fd9256c93f6947386a48336af2b75d88
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/05/2020
ms.locfileid: "78395187"
---
# <a name="troubleshoot-apache-storm-by-using-azure-hdinsight"></a>Az Azure HDInsight az Apache Storm hibaelhárítása

Ismerje meg a leggyakoribb problémákat és azok megoldásait [Apache Storm](https://storm.apache.org/) hasznos adatok [Apache Ambari](https://ambari.apache.org/)való használatához.

## <a name="how-do-i-access-the-storm-ui-on-a-cluster"></a>Hogyan férhetek hozzá a Storm felhasználói felülete egy fürtön?

A Storm felhasználói felülete eléréséhez egy böngészőben két lehetősége van:

### <a name="apache-ambari-ui"></a>Apache Ambari felhasználói felület

1. Nyissa meg az Ambari irányítópultot.
2. A szolgáltatások listájában válassza a **Storm**elemet.
3. A **gyors hivatkozások** menüben válassza a **Storm UI**lehetőséget.

### <a name="direct-link"></a>Közvetlen hivatkozás

A Storm felhasználói felülete a következő URL-címen érhető el:

`https://<cluster DNS name>/stormui`

Például: `https://stormcluster.azurehdinsight.net/stormui`

## <a name="how-do-i-transfer-storm-event-hub-spout-checkpoint-information-from-one-topology-to-another"></a>Hogyan ruházhatom át Storm event hub spout ellenőrzőpont információk adatainak egy másikra?

Olvassa el az Azure Event Hubsból topológiák fejlesztése során segítségével a HDInsight Storm event hub spout a .jar-fájl, telepítenie kell egy-egy új fürtön ugyanazzal a névvel rendelkező topológia. Azonban meg kell őriznie a régi fürtön [Apache ZooKeeperhoz](https://zookeeper.apache.org/) véglegesített ellenőrzőpont-adatellenőrzéseket.

### <a name="where-checkpoint-data-is-stored"></a>Ellenőrzőpont-adatok tárolására

Az event hub spout, ZooKeeper a gyökér elérési utat a tárol az eltolások ellenőrzőpont adatokat:

- A nem tranzakciós kiöntő ellenőrzőpontok tárolása `/eventhubspout`történik.

- A tranzakciós kiöntő ellenőrzőpont-adatkészletek tárolása `/transactional`történik.

### <a name="how-to-restore"></a>Visszaállítása

A ZooKeeper-ből történő adatexportáláshoz használt parancsfájlok és kódtárak beszerzéséhez, majd az ZooKeeper új névvel történő importálásához tekintse meg a [HDInsight Storm-példák](https://github.com/hdinsight/hdinsight-storm-examples/tree/master/tools/zkdatatool-1.0)című témakört.

Lib mappájába rendelkezik a .jar fájlokat, amelyek tartalmazzák az exportálási/importálási művelet végrehajtására. A bash-mappába, amely azt ismerteti, hogyan adatok exportálása a ZooKeeper-kiszolgáló a régi fürtön, és ezután importálja vissza a ZooKeeper-kiszolgáló, az új fürtön példa parancsfájl rendelkezik.

Futtassa a [stormmeta.sh](https://github.com/hdinsight/hdinsight-storm-examples/blob/master/tools/zkdatatool-1.0/bash/stormmeta.sh) parancsfájlt a ZooKeeper-csomópontokból az exportáláshoz, majd az adatok importálásához. Frissítse a parancsfájlt a megfelelő Hortonworks Data Platform (HDP) verzióra. (Is dolgozunk a HDInsight általános így ezeket a parancsfájlokat. Az általános parancsfájlok futtatásához bármely olyan csomópontról módosítások nélkül a fürtön a felhasználó.)

Az export parancs a metaadatokat egy Apache Hadoop elosztott fájlrendszer (HDFS) elérési útra írja (az Azure Blob Storage vagy a Azure Data Lake Storage) egy megadott helyen.

### <a name="examples"></a>Példák

#### <a name="export-offset-metadata"></a>Eltolási metaadatainak exportálásához

1. SSH-val, amelyről az ellenőrzőpont eltolás exportálni kell a fürtön nyissa meg a ZooKeeper-fürtön.
2. Futtassa a következő parancsot (miután frissítette a HDP-verzió sztringjét), hogy exportálja a ZooKeeper eltolási értékeit a `/stormmetadta/zkdata` HDFS útvonalra:

    ```apache
    java -cp ./*:/etc/hadoop/conf/*:/usr/hdp/2.5.1.0-56/hadoop/*:/usr/hdp/2.5.1.0-56/hadoop/lib/*:/usr/hdp/2.5.1.0-56/hadoop-hdfs/*:/usr/hdp/2.5.1.0-56/hadoop-hdfs/lib/*:/etc/failover-controller/conf/*:/etc/hadoop/* com.microsoft.storm.zkdatatool.ZkdataImporter export /eventhubspout /stormmetadata/zkdata
    ```

#### <a name="import-offset-metadata"></a>Eltolási metaadatainak importálása

1. SSH-val, amelyről az ellenőrzőpont eltolás importálni kell a fürtön nyissa meg a ZooKeeper-fürtön.
2. Futtassa a következő parancsot (miután frissítette a HDP-verzió sztringjét), hogy az ZooKeeper-eltolási adatok importálása a HDFS elérési útjáról `/stormmetadata/zkdata` a ZooKeeper-kiszolgálóra a cél fürtön:

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

Az aktuális HDP-verem Storm bináris fájljai `/usr/hdp/current/storm-client`találhatók. A hely az azonos fő csomópontból és feldolgozó csomópontokat.

Több bináris fájl is lehet a/usr/HDP adott HDP-verzióihoz (például `/usr/hdp/2.5.0.1233/storm`). A `/usr/hdp/current/storm-client` mappát a fürtön futó legújabb verzióra kell összekapcsolni.

További információ: [Kapcsolódás HDInsight-fürthöz SSH](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-linux-use-ssh-unix) és [Apache Storm](https://storm.apache.org/)használatával.

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

[Események feldolgozása az Azure Event Hubs és az Apache Storm on HDInsight (Java) szolgáltatással](https://github.com/Azure-Samples/hdinsight-java-storm-eventhub)

### <a name="c-based-topology-mono-on-hdinsight-34-linux-storm-clusters"></a>C#-alapú topológia (HDInsight 3.4-es + Linux Storm-fürtök a Mono)

[Események feldolgozása az Azure Event Hubs és az Apache Storm on HDInsightC#()](https://docs.microsoft.com/azure/hdinsight/hdinsight-storm-develop-csharp-event-hub-topology)

### <a name="latest-apache-storm-event-hub-spout-binaries-for-hdinsight-35-linux-storm-clusters"></a>Legújabb Apache Storm event hub spout bináris fájljai, a HDInsight 3.5-ös + Linux Storm-fürtök

Ha szeretné megtudni, hogyan használhatja a HDInsight 3.5 + Linux Storm-fürtöket használó legújabb Storm Event hub kiöntőt, tekintse meg a [MVN-tárház információs fájlját](https://github.com/hdinsight/mvn-repo/blob/master/README.md).

### <a name="source-code-examples"></a>Forrás hitelesítésikód-példák

Tekintse át a [példákat](https://github.com/Azure-Samples/hdinsight-java-storm-eventhub) az Azure Event hub beolvasására és írására egy Azure HDInsight-fürtön található Apache Storm-topológia használatával (Java-ban írt).

## <a name="how-do-i-locate-storm-log4j-2-configuration-files-on-clusters"></a>Hogyan találom meg a Storm Log4J 2 konfigurációs fájlok fürtökön?

A Storm Services [Apache Log4j 2](https://logging.apache.org/log4j/2.x/) konfigurációs fájljainak azonosításához.

### <a name="on-head-nodes"></a>A fő csomópontok

A Nimbus Log4J-konfigurációjának olvasása `/usr/hdp/\<HDP version>/storm/log4j2/cluster.xml`.

### <a name="on-worker-nodes"></a>A feldolgozó csomópontok

A felügyelő Log4J-konfigurációjának olvasása `/usr/hdp/\<HDP version>/storm/log4j2/cluster.xml`.

A Worker Log4J konfigurációs fájljának olvasása `/usr/hdp/\<HDP version>/storm/log4j2/worker.xml`.

Példák: `/usr/hdp/2.6.0.2-76/storm/log4j2/cluster.xml`
`/usr/hdp/2.6.0.2-76/storm/log4j2/worker.xml`

---

## <a name="not-a-leader-exception"></a>Nem vezető kivétel

A topológia elküldésekor a felhasználó a következőhöz hasonló hibaüzenetet kaphat: `Topology submission exception, cause not a leader, the current leader is NimbusInfo`.

A megoldáshoz előfordulhat, hogy a felhasználónak be kell állítania egy jegyet, hogy a csomópontok újraindulnak vagy újraindulnak. További információ: [https://community.hortonworks.com/content/supportkb/150287/error-ignoring-exception-while-trying-to-get-leade.html](https://community.hortonworks.com/content/supportkb/150287/error-ignoring-exception-while-trying-to-get-leade.html).

---

## <a name="next-steps"></a>Következő lépések

Ha nem látja a problémát, vagy nem tudja megoldani a problémát, további támogatásért látogasson el az alábbi csatornák egyikére:

- Azure-szakértőktől kaphat válaszokat az [Azure közösségi támogatásával](https://azure.microsoft.com/support/community/).

- Kapcsolódjon a [@AzureSupporthoz](https://twitter.com/azuresupport) – a hivatalos Microsoft Azure fiókot a felhasználói élmény javításához. Az Azure-Közösség összekapcsolása a megfelelő erőforrásokkal: válaszok, támogatás és szakértők.

- Ha további segítségre van szüksége, támogatási kérést küldhet a [Azure Portaltól](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Válassza a menüsor **támogatás** elemét, vagy nyissa meg a **Súgó + támogatás** hubot. Részletesebb információkért tekintse át az [Azure-támogatási kérelem létrehozását](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request)ismertető témakört. Az előfizetés-kezeléshez és a számlázási támogatáshoz való hozzáférés a Microsoft Azure-előfizetés része, és a technikai támogatás az egyik [Azure-támogatási csomagon](https://azure.microsoft.com/support/plans/)keresztül érhető el.
