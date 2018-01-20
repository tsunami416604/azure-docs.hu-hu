---
title: "Használja az Apache Kafka adatfolyamok API - az Azure HDInsight |} Microsoft Docs"
description: "Ismerje meg az Apache Kafka adatfolyamok API használata a HDInsight Kafka. Ez az API lehetővé teszi a streamfeldolgozási Kafka témakörei között."
services: hdinsight
documentationcenter: 
author: Blackmist
manager: cgronlun
editor: cgronlun
tags: azure-portal
ms.service: hdinsight
ms.custom: hdinsightactive
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/19/2018
ms.author: larryfr
ms.openlocfilehash: 1ea20eceb28fead003c7279632b1e75ae1fd3553
ms.sourcegitcommit: 817c3db817348ad088711494e97fc84c9b32f19d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/20/2018
---
# <a name="apache-kafka-streams-api"></a>Apache Kafka streams API

Megtudhatja, hogyan hozzon létre egy alkalmazást, amely a Kafka adatfolyamok API és a HDInsight Kafka futtassák azt.

Az Apache Kafka használatakor az adatfolyam feldolgozása gyakran történik az Apache Spark on vagy a Storm használatával. Kafka 0.10.0-s verzió (a HDInsight 3.5-ös és 3.6) bevezetett a Kafka adatfolyamok API. Ez az API átalakítási az adatfolyamok között bemeneti és kimeneti témakörök Kafka futó alkalmazás segítségével teszi lehetővé. Néhány esetben ez a Spark és a streamelési megoldások Storm alternatív megoldás is lehet. Kafka adatfolyamok további információkért tekintse meg a [Bevezetés az adatfolyamok](https://kafka.apache.org/10/documentation/streams/) dokumentációjában az Apache.org webhelyen.

## <a name="set-up-your-development-environment"></a>A fejlesztési környezet beállítása

A fejlesztési környezetben a következő összetevőket kell telepítenie:

* [Java JDK 8](http://www.oracle.com/technetwork/java/javase/downloads/index.html) vagy azzal egyenértékű, például az OpenJDK.

* [Apache Maven](http://maven.apache.org/)

* Egy SSH-ügyfél és az `scp` parancs. További információ: [SSH használata a HDInsighttal](../hdinsight-hadoop-linux-use-ssh-unix.md).

## <a name="set-up-your-deployment-environment"></a>A telepítési környezet beállítása

Ebben a példában a HDInsight 3.6 Kafka igényel. Megtudhatja, hogyan hozzon létre egy Kafka a HDInsight-fürtöt, tekintse meg a [indítsa el a HDInsight Kafka](apache-kafka-get-started.md) dokumentum.

## <a name="build-and-deploy-the-example"></a>Hozza létre és telepítheti a példa

Az alábbi lépések segítségével hozza létre, és a HDInsight-fürt Kafka a projekt telepítése.

1. A példákat a [https://github.com/Azure-Samples/hdinsight-kafka-java-get-started](https://github.com/Azure-Samples/hdinsight-kafka-java-get-started) címről töltheti le.

2. Lépjen a `Streaming` könyvtárra, és majd a jar-csomag létrehozása a következő parancsot:

    ```bash
    mvn clean package
    ```

    A parancs létrehozza a `target` nevű könyvtárat, amely a `kafka-streaming-1.0-SNAPSHOT.jar` nevű fájlt tartalmazza.

3. Másolja az alábbi parancs segítségével a `kafka-streaming-1.0-SNAPSHOT.jar` fájl a HDInsight-fürthöz:
   
    ```bash
    scp ./target/kafka-streaming-1.0-SNAPSHOT.jar SSHUSER@CLUSTERNAME-ssh.azurehdinsight.net:kafka-streaming.jar
    ```
   
    Cserélje le az **SSHUSER** elemet a fürt SSH-felhasználójára, illetve a **CLUSTERNAME** elemet a fürt nevére. Ha a rendszer kéri, adja meg az SSH-felhasználói fiók jelszavát. További információk az `scp` a hdinsight eszközzel, lásd: [az SSH a Hdinsighttal](../hdinsight-hadoop-linux-use-ssh-unix.md).

## <a name="run-the-example"></a>A példa futtatásához

1. Nyissa meg a fürthöz az SSH-kapcsolat, használja a következő parancsot:

    ```bash
    ssh SSHUSER@CLUSTERNAME-ssh.azurehdinsight.net
    ```

    Cserélje le az **SSHUSER** elemet a fürt SSH-felhasználójára, illetve a **CLUSTERNAME** elemet a fürt nevére. Ha a rendszer kéri, adja meg az SSH-felhasználói fiók jelszavát. További információk az `scp` a hdinsight eszközzel, lásd: [az SSH a Hdinsighttal](../hdinsight-hadoop-linux-use-ssh-unix.md).

4. Ez a példa által használt témakörök a Kafka létrehozásához használja a következő parancsokat:

    ```bash
    sudo apt -y install jq

    CLUSTERNAME='your cluster name'

    export KAFKAZKHOSTS=`curl -sS -u admin -G https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/services/ZOOKEEPER/components/ZOOKEEPER_SERVER | jq -r '["\(.host_components[].HostRoles.host_name):2181"] | join(",")' | cut -d',' -f1,2`

    export KAFKABROKERS=`curl -sS -u admin -G https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/services/KAFKA/components/KAFKA_BROKER | jq -r '["\(.host_components[].HostRoles.host_name):9092"] | join(",")' | cut -d',' -f1,2`

    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --create --replication-factor 3 --partitions 8 --topic test --zookeeper $KAFKAZKHOSTS

    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --create --replication-factor 3 --partitions 8 --topic wordcounts --zookeeper $KAFKAZKHOSTS
    ```

    Cserélje le __a fürt neve__ a HDInsight-fürt nevét. Amikor a rendszer kéri, adja meg a HDInsight fürt bejelentkezési fiók jelszavát.

    > [!NOTE]
    > Ha a fürt bejelentkezési eltér az alapértelmezett érték `admin`, cserélje le a `admin` érték az előző parancsot a fürt bejelentkezési névvel.

5. Ez a példa futtatásához három feladatot kell elvégeznie:

    * Indítsa el a lévő adatfolyamok megoldás `kafka-streaming.jar`.
    * Indítsa el a gyártó által a `test` témakör.
    * Így megtekintheti a kimeneti írni, indítsa el a fogyasztó a `wordcounts` témakör

    Ezek a műveletek SSH legfeljebb három munkamenet megnyitása sikertelen megvalósításához. De majd kell `$KAFKABROKERS` és `$KAFKAZKHOSTS` futtatásával minden egyes lépést 4 ebben a szakaszban ismertetett minden SSH-munkamenetet. Egy egyszerűbb megoldást a `tmux` segédprogram, amely az aktuális SSH megjelenítésének több szakaszokra fel. A stream, a gyártó és az használatával fogyasztói `tmux`, használja a következő parancsot:

    ```bash
    tmux new-session '/usr/hdp/current/kafka-broker/bin/kafka-console-consumer.sh --bootstrap-server $KAFKABROKERS --topic wordcounts --formatter kafka.tools.DefaultMessageFormatter --property print.key=true --property key.deserializer=org.apache.kafka.common.serialization.StringDeserializer --property value.deserializer=org.apache.kafka.common.serialization.LongDeserializer' \; split-window -h 'java -jar kafka-streaming.jar $KAFKABROKERS $KAFKAZKHOSTS' \; split-window -v '/usr/hdp/current/kafka-broker/bin/kafka-console-producer.sh --broker-list $KAFKABROKERS --topic test' \; attach
    ```

    Ez a parancs felosztja a három részre SSH megjelenítéséhez:

    * A bal oldalon fut a konzol felhasználóinak, mely olvasások érkező üzenetek a `wordcounts` témakör:`/usr/hdp/current/kafka-broker/bin/kafka-console-consumer.sh --bootstrap-server $KAFKABROKERS --topic wordcounts --formatter kafka.tools.DefaultMessageFormatter --property print.key=true --property key.deserializer=org.apache.kafka.common.serialization.StringDeserializer --property value.deserializer=org.apache.kafka.common.serialization.LongDeserializer`

        > [!NOTE]
        > A `--property` paraméterek, kérje meg a konzol fogyasztó nyomtatni a kulcsot (word) együtt a count (érték). Ez a paraméter is konfigurálja a deszerializáló ezeket az értékeket a Kafka olvasásához.

    * A jobb felső részében a futtatja az adatfolyamok API-megoldás:`java -jar kafka-streaming.jar $KAFKABROKERS $KAFKAZKHOSTS`

    * A jobb alsó részén a konzol készítő fut, és megvárja-e a küldendő üzenetek megadását a `test` témakör:`/usr/hdp/current/kafka-broker/bin/kafka-console-producer.sh --broker-list $KAFKABROKERS --topic test`
 
6. Miután a `tmux` parancs felosztja a képernyőt, a kurzor jobb alsó részén. Indítsa el a mondatok megadása. Minden mondat után a bal oldali ablaktáblán frissül jelenjen meg az egyedi szavak számát. A kimenet az alábbi szöveghez hasonló:
   
        dwarfs  13635
        ago     13664
        snow    13636
        dwarfs  13636
        ago     13665
        a       13803
        ago     13666
        a       13804
        ago     13667
        ago     13668
        jumped  13640
        jumped  13641
   
    > [!NOTE]
    > A szám nő minden egyes szó beolvasott szóval.

7. Használja a __Ctrl + C__ való kilépéshez a küldőnek. Továbbra is használni __Ctrl + C__ az alkalmazás és a fogyasztói kilép.

## <a name="next-steps"></a>További lépések

Ebből a dokumentumból megismerte a Kafka adatfolyamok API használata a HDInsight Kafka. Az alábbiak további információt biztosítanak a Kafka használatával kapcsolatban:

* [Kafka-naplók elemzése](apache-kafka-log-analytics-operations-management.md)
* [Adatreplikálás Kafka-fürtök között](apache-kafka-mirroring.md)
* [A hdinsight eszközzel Kafka gyártó és a fogyasztói API](apache-kafka-producer-consumer-api.md)
* [Az Apache Spark stream (DStream) használata a Kafkával a HDInsighton](../hdinsight-apache-spark-with-kafka.md)
* [Az Apache Spark strukturált stream használata a Kafkával a HDInsighton](../hdinsight-apache-kafka-spark-structured-streaming.md)
* [Használja az Apache Spark strukturált Streaming tárolt adatok mozgatása Kafka a HDInsight az Cosmos-Adatbázishoz](../apache-kafka-spark-structured-streaming-cosmosdb.md)
* [Az Apache Storm használata a HDInsighton futó Kafkával](../hdinsight-apache-storm-with-kafka.md)
* [Csatlakozás a Kafkához Azure Virtual Networkön keresztül](apache-kafka-connect-vpn-gateway.md)