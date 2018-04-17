---
title: Az Apache Kafka készítő és fogyasztói API-k – az Azure HDInsight |} Microsoft Docs
description: Útmutató a Apache Kafka készítő és fogyasztói API-k használata a HDInsight Kafka. Ezen API-k lehetővé teszik, hogy az írható és olvasható az Apache Kafka alkalmazások fejlesztéséhez.
services: hdinsight
documentationcenter: ''
author: Blackmist
manager: cgronlun
editor: cgronlun
tags: azure-portal
ms.service: hdinsight
ms.custom: hdinsightactive
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/10/2018
ms.author: larryfr
ms.openlocfilehash: 01592401c4c88adeed49b11df4e7963e27b1bcee
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2018
---
# <a name="apache-kafka-producer-and-consumer-apis"></a>Apache Kafka termelő és fogyasztói API-k

Megtudhatja, hogyan hozhat létre, amely a Kafka gyártó és a fogyasztói API-kat használ, a HDInsight Kafka alkalmazást.

Az API-jainak dokumentációja, lásd: [készítő API](https://kafka.apache.org/documentation/#producerapi) és [fogyasztói API](https://kafka.apache.org/documentation/#consumerapi).

## <a name="set-up-your-development-environment"></a>A fejlesztési környezet beállítása

A fejlesztési környezetben a következő összetevőket kell telepítenie:

* [Java JDK 8](http://www.oracle.com/technetwork/java/javase/downloads/index.html) vagy azzal egyenértékű, például az OpenJDK.

* [Apache Maven](http://maven.apache.org/)

* Egy SSH-ügyfél és az `scp` parancs. További információ: [SSH használata a HDInsighttal](../hdinsight-hadoop-linux-use-ssh-unix.md).

## <a name="set-up-your-deployment-environment"></a>A telepítési környezet beállítása

Ebben a példában a HDInsight 3.6 Kafka igényel. Megtudhatja, hogyan hozzon létre egy Kafka a HDInsight-fürtöt, tekintse meg a [indítsa el a HDInsight Kafka](apache-kafka-get-started.md) dokumentum.

## <a name="build-and-deploy-the-example"></a>Hozza létre és telepítheti a példa

1. Töltse le az említett példákat követik [ https://github.com/Azure-Samples/hdinsight-kafka-java-get-started ](https://github.com/Azure-Samples/hdinsight-kafka-java-get-started).

2. Módosítsa a könyvtárat, hol található a `Producer-Consumer` könyvtárra, és használja a következő parancsot:

    ```
    mvn clean package
    ```

    A parancs létrehozza a `target` nevű könyvtárat, amely a `kafka-producer-consumer-1.0-SNAPSHOT.jar` nevű fájlt tartalmazza.

3. Az alábbi parancsokkal másolja a `kafka-producer-consumer-1.0-SNAPSHOT.jar` fájlt a HDInsight-fürtbe:
   
    ```bash
    scp ./target/kafka-producer-consumer-1.0-SNAPSHOT.jar SSHUSER@CLUSTERNAME-ssh.azurehdinsight.net:kafka-producer-consumer.jar
    ```
   
    Cserélje le az **SSHUSER** elemet a fürt SSH-felhasználójára, illetve a **CLUSTERNAME** elemet a fürt nevére. Ha a rendszer kéri, adja meg az SSH-felhasználó jelszavát.

## <a id="run"></a> A példa futtatásához

1. Nyissa meg a fürthöz az SSH-kapcsolat, használja a következő parancsot:

    ```bash
    ssh SSHUSER@CLUSTERNAME-ssh.azurehdinsight.net
    ```

    Cserélje le az **SSHUSER** elemet a fürt SSH-felhasználójára, illetve a **CLUSTERNAME** elemet a fürt nevére. Ha a rendszer kéri, adja meg az SSH-felhasználói fiók jelszavát. További információk az `scp` a hdinsight eszközzel, lásd: [az SSH a Hdinsighttal](../hdinsight-hadoop-linux-use-ssh-unix.md).

2. Ez a példa által használt témakörök a Kafka létrehozásához használja a következő parancsokat:

    ```bash
    sudo apt -y install jq

    CLUSTERNAME='your cluster name'

    export KAFKAZKHOSTS=`curl -sS -u admin -G https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/services/ZOOKEEPER/components/ZOOKEEPER_SERVER | jq -r '["\(.host_components[].HostRoles.host_name):2181"] | join(",")' | cut -d',' -f1,2`

    export KAFKABROKERS=`curl -sS -u admin -G https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/services/KAFKA/components/KAFKA_BROKER | jq -r '["\(.host_components[].HostRoles.host_name):9092"] | join(",")' | cut -d',' -f1,2`

    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --create --replication-factor 3 --partitions 8 --topic test --zookeeper $KAFKAZKHOSTS
    ```

    Cserélje le __a fürt neve__ a HDInsight-fürt nevét. Amikor a rendszer kéri, adja meg a HDInsight fürt bejelentkezési fiók jelszavát.

    > [!NOTE]
    > Ha a fürt bejelentkezési eltér az alapértelmezett érték `admin`, cserélje le a `admin` érték az előző parancsot a fürt bejelentkezési névvel.

3. A gyártó futtatásához, és adatokat írni a témakör, a következő paranccsal:

    ```bash
    java -jar kafka-producer-consumer.jar producer $KAFKABROKERS
    ```

4. Miután befejezte a gyártó, használja a következő parancsot a következő témakörben olvasásakor:
   
    ```bash
    java -jar kafka-producer-consumer.jar consumer $KAFKABROKERS
    ```
   
    A rendszer megjeleníti a beolvasott rekordokat a rekordok számával együtt.

5. Használja a __Ctrl + C__ billentyűparancsot a fogyasztóból történő kilépéshez.

### <a name="multiple-consumers"></a>Több fogyasztó

A Kafka-fogyasztók egy fogyasztói csoportot használnak a rekordok olvasásakor. Ugyanazon csoport használata több fogyasztó esetén a terhelés szempontjából kiegyensúlyozott olvasást eredményez a témakörökből történő olvasáskor. A csoport mindegyik fogyasztója a rekordok egy részét kapja meg.

A fogyasztó alkalmazás fogad el egy paraméter, amely a csoport azonosítója. Például a következő paranccsal elindítja a csoport azonosító használatával ügyféllel `mygroup`:
   
```bash
java -jar kafka-producer-consumer.jar consumer $KAFKABROKERS mygroup
```

Ez a folyamat művelet megtekintéséhez használja a következő lépések

1. Ismételje meg az 1 és 2 a [a példa futtatásához](#run) nyisson meg egy új SSH-munkamenetet a szakaszban található.

2. Mindkét SSH-munkamenetekben adja meg a következő parancsot:

    ```bash
    java -jar kafka-producer-consumer.jar consumer $KAFKABROKERS mygroup
    ```
    
    > [!IMPORTANT]
    > Adja meg mindkét parancsok egyidejűleg, így mindkettőt futtatnak egy időben.

    Figyelje meg, hogy az üzenetek száma olvasható nem ugyanaz, mint az előző szakaszban, ha csak egy fogyasztói volt. Ehelyett az üzenetek olvasás a példányai között oszlik meg.

Az ugyanazon csoportban található ügyfelek általi felhasználás kezelése a témakör partícióinak használatával történik. A korábban létrehozott `test` témakör nyolc partícióval rendelkezik. Ha megnyit nyolc SSH-munkamenetet, és mindegyikben elindít egy fogyasztót, az egyes fogyasztók a témakör egyetlen partíciójából fognak rekordokat olvasni.

> [!IMPORTANT]
> A fogyasztói csoportban található fogyasztói példányok száma nem haladhatja meg a partíciók számát. Ebben a példában egy fogyasztói csoport legfeljebb nyolc fogyasztót tartalmazhat, mivel a témakörben ennyi partíció található. Emellett lehet több, legfeljebb nyolc fogyasztóval rendelkező fogyasztói csoportja is.

A Kafkában tárolt rekordok mentése a partíción belüli fogadásuk sorrendje szerint történik. Ha a rekordokat az érkezési sorrendben szeretné kézbesíteni *egy partíción belül*, hozzon létre egy fogyasztói csoportot, amelyben a fogyasztói példányok száma egyezik a partíciók számával. Ha a rekordokat az érkezési sorrendben szeretné kézbesíteni *a témakörön belül*, hozzon létre egy olyan fogyasztói csoportot, amely csak egyetlen fogyasztói példánnyal rendelkezik.

## <a name="next-steps"></a>További lépések

Ebből a dokumentumból megismerte a Kafka gyártó és a fogyasztói API használata a HDInsight Kafka. Az alábbiak további információt biztosítanak a Kafka használatával kapcsolatban:

* [Kafka-naplók elemzése](apache-kafka-log-analytics-operations-management.md)
* [Adatreplikálás Kafka-fürtök között](apache-kafka-mirroring.md)
* [Kafka Streams API a HDInsighttal](apache-kafka-streams-api.md)
* [Az Apache Spark stream (DStream) használata a Kafkával a HDInsighton](../hdinsight-apache-spark-with-kafka.md)
* [Az Apache Spark strukturált stream használata a Kafkával a HDInsighton](../hdinsight-apache-kafka-spark-structured-streaming.md)
* [Az Apache Spark strukturált stream használata adatok áthelyezéséhez a HDInsighton lévő Kafkáról a Cosmos DB-re](../apache-kafka-spark-structured-streaming-cosmosdb.md)
* [Az Apache Storm használata a HDInsighton futó Kafkával](../hdinsight-apache-storm-with-kafka.md)
* [Csatlakozás a Kafkához Azure Virtual Networkön keresztül](apache-kafka-connect-vpn-gateway.md)