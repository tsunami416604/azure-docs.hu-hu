---
title: 'Oktatóanyag: Az Apache Kafka Streams API használata – Azure HDInsight '
description: Megtudhatja, hogyan használhatja az Apache Kafka Streams API-t a Kafka on HDInsighttal. Az API segítségével streamfeldolgozást végezhet a témakörök között a Kafkában.
services: hdinsight
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.topic: tutorial
ms.date: 11/06/2018
ms.openlocfilehash: 8319376c597f16a5bfe1a357d74c59453b797e51
ms.sourcegitcommit: 345b96d564256bcd3115910e93220c4e4cf827b3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/28/2018
ms.locfileid: "52495124"
---
# <a name="tutorial-apache-kafka-streams-api"></a>Oktatóanyag: Az Apache Kafka Streams API

Ismerje meg, hogyan hozhat létre az Apache Kafka Streams API-t használó alkalmazások, és futtassa a Kafka HDInsight. 

A jelen oktatóanyagban használt alkalmazás egy streamelési szószámláló. Szöveges adatokat olvas be egy Kafka-témakörből, kigyűjti az egyes szavakat, majd eltárolja a szavakat és azok számát egy másik Kafka-témakörben.

> [!NOTE]
> A Kafka adatfolyam-feldolgozás gyakran történik az Apache Spark és Apache Storm használatával. A Kafka Streams API a Kafka 0.10.0-s verziójában (a HDInsight 3.5-ben és 3.6-ban) lett bevezetve. Ez az API lehetővé teszi az adatstreamek a bemeneti és kimeneti témakörök közötti átalakítását. Bizonyos esetekben ez alternatív megoldás lehet a streamelési Storm- vagy Spark-megoldások létrehozása helyett. 
>
> A Kafka Streams megoldással kapcsolatos további információkért tekintse meg [a Streams bevezető](https://kafka.apache.org/10/documentation/streams/) dokumentációját az Apache.org webhelyen.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * A fejlesztési környezet beállítása
> * A kód értelmezése
> * Az alkalmazás létrehozása és üzembe helyezése
> * Kafka-témakörök konfigurálása
> * A kód futtatása

## <a name="prerequisites"></a>Előfeltételek

* Egy Kafka on HDInsight 3.6-fürt. Ismerje meg, hogyan hozhat létre egy Kafka HDInsight-fürtön, tekintse meg a [a HDInsight Apache Kafka használatának első lépései](apache-kafka-get-started.md) dokumentumot.

* A lépések elvégzéséhez a [előállítói API-t és az Apache Kafka-fogyasztók](apache-kafka-producer-consumer-api.md) dokumentumot. A dokumentumban leírt lépések az ebben az oktatóanyagban létrehozott példaalkalmazást és -témaköröket használják.

## <a name="set-up-your-development-environment"></a>A fejlesztési környezet beállítása

A fejlesztési környezetben a következő összetevőket kell telepítenie:

* [Java JDK 8](https://aka.ms/azure-jdks) vagy azzal egyenértékű, például az OpenJDK.

* [Apache Maven](http://maven.apache.org/)

* Egy SSH-ügyfél és az `scp` parancs. További információ: [SSH használata a HDInsighttal](../hdinsight-hadoop-linux-use-ssh-unix.md).

## <a name="understand-the-code"></a>A kód értelmezése

A példaalkalmazás helye: [https://github.com/Azure-Samples/hdinsight-kafka-java-get-started](https://github.com/Azure-Samples/hdinsight-kafka-java-get-started) (`Streaming` alkönyvtár). Az alkalmazás két fájlt tartalmaz:

* `pom.xml`: Ez a fájl határozza meg a projektfüggőségeket, a Java-verziót és a csomagolási módszereket.
* `Stream.java`: Ez a fájl valósítja meg a streamelési logikát.

### <a name="pomxml"></a>Pom.xml

A `pom.xml` fájl fontosabb elemei a következők:

* Függőségek: A projekt a Kafka Streams API-jára épül, amelyet a `kafka-clients` csomag tartalmaz. Ezt a függőséget a következő XML-kód határozza meg:

    ```xml
    <!-- Kafka client for producer/consumer operations -->
    <dependency>
      <groupId>org.apache.kafka</groupId>
      <artifactId>kafka-clients</artifactId>
      <version>${kafka.version}</version>
    </dependency>
    ```

    > [!NOTE]
    > A `${kafka.version}` bejegyzés a `pom.xml` fájl `<properties>..</properties>` szakaszában van meghatározva, és a HDInsight-fürt Kafka-verziójához van konfigurálva.

* Beépülő modulok: A Maven beépülő modulok különböző képességekkel rendelkeznek. Ebben a projektben a következő beépülő modulokat használjuk:

    * `maven-compiler-plugin`: Ezzel állítható a projekt által használt Java-verzió a 8-as verzióra. A HDInsight 3.6-hoz Java 8 szükséges.
    * `maven-shade-plugin`: Ez az alkalmazást és az esetleges függőségeket tartalmazó uber JAR-fájl létrehozására használható. Az alkalmazás belépési pontjának beállítására is használható, így közvetlenül futtathatja a JAR-fájlt anélkül, hogy a fő osztályt meg kellene határoznia.

### <a name="streamjava"></a>Stream.java

A [Stream.java](https://github.com/Azure-Samples/hdinsight-kafka-java-get-started/blob/master/Streaming/src/main/java/com/microsoft/example/Stream.java) fájl a Streams API-val implementálja a szószámláló alkalmazást. Adatokat olvas egy `test` nevű Kafka-témakörből, és a szószámokat egy `wordcounts` nevű témakörbe írja.

A következő kód definiálja a szószámláló alkalmazást:

```java
package com.microsoft.example;

import org.apache.kafka.common.serialization.Serde;
import org.apache.kafka.common.serialization.Serdes;
import org.apache.kafka.streams.KafkaStreams;
import org.apache.kafka.streams.KeyValue;
import org.apache.kafka.streams.StreamsConfig;
import org.apache.kafka.streams.kstream.KStream;
import org.apache.kafka.streams.kstream.KStreamBuilder;

import java.util.Arrays;
import java.util.Properties;

public class Stream
{
    public static void main( String[] args ) {
        Properties streamsConfig = new Properties();
        // The name must be unique on the Kafka cluster
        streamsConfig.put(StreamsConfig.APPLICATION_ID_CONFIG, "wordcount-example");
        // Brokers
        streamsConfig.put(StreamsConfig.BOOTSTRAP_SERVERS_CONFIG, args[0]);
        // SerDes for key and values
        streamsConfig.put(StreamsConfig.KEY_SERDE_CLASS_CONFIG, Serdes.String().getClass().getName());
        streamsConfig.put(StreamsConfig.VALUE_SERDE_CLASS_CONFIG, Serdes.String().getClass().getName());

        // Serdes for the word and count
        Serde<String> stringSerde = Serdes.String();
        Serde<Long> longSerde = Serdes.Long();

        KStreamBuilder builder = new KStreamBuilder();
        KStream<String, String> sentences = builder.stream(stringSerde, stringSerde, "test");
        KStream<String, Long> wordCounts = sentences
                .flatMapValues(value -> Arrays.asList(value.toLowerCase().split("\\W+")))
                .map((key, word) -> new KeyValue<>(word, word))
                .countByKey("Counts")
                .toStream();
        wordCounts.to(stringSerde, longSerde, "wordcounts");

        KafkaStreams streams = new KafkaStreams(builder, streamsConfig);
        streams.start();

        Runtime.getRuntime().addShutdownHook(new Thread(streams::close));
    }
}
```


## <a name="build-and-deploy-the-example"></a>A példa létrehozása és üzembe helyezése

A projekt összeállításához és a Kafka on HDInsight-fürtön való üzembe helyezéséhez hajtsa végre a következő lépéseket:

1. A példákat a [https://github.com/Azure-Samples/hdinsight-kafka-java-get-started](https://github.com/Azure-Samples/hdinsight-kafka-java-get-started) címről töltheti le.

2. Módosítsa a könyvtárakat a `Streaming` könyvtárra, majd a következő parancs használatával hozzon létre egy JAR-csomagot:

    ```bash
    mvn clean package
    ```

    Ez a parancs a `target/kafka-streaming-1.0-SNAPSHOT.jar` helyen hozza létre a csomagot.

3. Az alábbi paranccsal másolja a `kafka-streaming-1.0-SNAPSHOT.jar` fájlt a HDInsight-fürtbe:
   
    ```bash
    scp ./target/kafka-streaming-1.0-SNAPSHOT.jar sshuser@clustername-ssh.azurehdinsight.net:kafka-streaming.jar
    ```
   
    Cserélje le az `sshuser` elemet a fürt SSH-felhasználójára, illetve a `clustername` elemet a fürt nevére. Ha a rendszer kéri, adja meg az SSH-felhasználói fiók jelszavát. Az `scp` HDInsighttal való használatával kapcsolatos további információkat [az SSH a HDInsighttal való használatáról szóló cikkben](../hdinsight-hadoop-linux-use-ssh-unix.md) találhat.

## <a name="create-apache-kafka-topics"></a>Hozzon létre Apache Kafka-témakörökhöz

1. A fürttel létesített SSH-kapcsolat megnyitásához használja a következő parancsot:

    ```bash
    ssh sshuser@clustername-ssh.azurehdinsight.net
    ```

    Cserélje le az `sshuser` elemet a fürt SSH-felhasználójára, illetve a `clustername` elemet a fürt nevére. Ha a rendszer kéri, adja meg az SSH-felhasználói fiók jelszavát. Az `scp` HDInsighttal való használatával kapcsolatos további információkat [az SSH a HDInsighttal való használatáról szóló cikkben](../hdinsight-hadoop-linux-use-ssh-unix.md) találhat.

2. A fürtnév változóként való mentéséhez és egy JSON-elemző segédprogram (`jq`) telepítéséhez használja az alábbi parancsokat. Ha a rendszer kéri, adja meg a Kafka-fürt nevét:

    ```bash
    sudo apt -y install jq
    read -p 'Enter your Kafka cluster name:' CLUSTERNAME
    ```

3. A Kafka-közvetítő gazdagépek a gazdagépek és az Apache Zookeeper lekéréséhez használja a következő parancsokat. Ha a rendszer kéri, adja meg a fürt bejelentkezési (rendszergazdai) fiókjának jelszavát. A rendszer kétszer kéri a jelszó megadására.

    ```bash
    export KAFKAZKHOSTS=`curl -sS -u admin -G https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/services/ZOOKEEPER/components/ZOOKEEPER_SERVER | jq -r '["\(.host_components[].HostRoles.host_name):2181"] | join(",")' | cut -d',' -f1,2`; \
    export KAFKABROKERS=`curl -sS -u admin -G https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/services/KAFKA/components/KAFKA_BROKER | jq -r '["\(.host_components[].HostRoles.host_name):9092"] | join(",")' | cut -d',' -f1,2`; \
    ```

4. A streamelési művelet által használt témakörök létrehozásához használja az alábbi parancsokat:

    > [!NOTE]
    > Hibaüzenetet kaphat, miszerint a `test` témakör már létezik. Ez rendben van, mivel lehetséges, hogy a témakör az előállítói és fogyasztói API-val foglalkozó oktatóanyagban már létre lett hozva.

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --create --replication-factor 3 --partitions 8 --topic test --zookeeper $KAFKAZKHOSTS

    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --create --replication-factor 3 --partitions 8 --topic wordcounts --zookeeper $KAFKAZKHOSTS

    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --create --replication-factor 3 --partitions 8 --topic RekeyedIntermediateTopic --zookeeper $KAFKAZKHOSTS

    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --create --replication-factor 3 --partitions 8 --topic wordcount-example-Counts-changelog --zookeeper $KAFKAZKHOSTS
    ```

    A témaköröket a következő célokra használjuk:

    * `test`: A témakör, ahová a rekordok érkeznek. A streamelési alkalmazás innen olvassa az adatokat.
    * `wordcounts`: A témakör, amelyben a streamelési alkalmazás a kimenetet tárolja.
    * `RekeyedIntermediateTopic`: Ez a témakör szolgál az adatok újraparticionálására, ahogy a `countByKey` operátor frissíti a számlálót.
    * `wordcount-example-Counts-changelog`: Ez a témakör egy, a `countByKey` művelet által használt állapottároló.

    > [!IMPORTANT]
    > A Kafka on HDInsight a témakörök automatikus létrehozására is konfigurálható. További információkért tekintse meg [a témakörök automatikus létrehozásának konfigurálását](apache-kafka-auto-create-topics.md) ismertető dokumentumot.

## <a name="run-the-code"></a>A kód futtatása

1. A streamelési alkalmazás háttérfolyamatként való indításához használja a következő parancsot:

    ```bash
    java -jar kafka-streaming.jar $KAFKABROKERS $KAFKAZKHOSTS &
    ```

    > [!NOTE]
    > Figyelmeztetés jelenhet meg a log4j kapcsán. Ezt figyelmen kívül hagyhatja.

2. A bejegyzések a `test` témakörbe való küldéséhez az alábbi parancs használatával indítsa el az előállító alkalmazást:

    ```bash
    java -jar kafka-producer-consumer.jar producer test $KAFKABROKERS
    ```

3. Miután az előállító végzett, az alábbi parancs használatával tekintheti meg a `wordcounts` témakörben tárolt adatokat:

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-console-consumer.sh --bootstrap-server $KAFKABROKERS --topic wordcounts --formatter kafka.tools.DefaultMessageFormatter --property print.key=true --property key.deserializer=org.apache.kafka.common.serialization.StringDeserializer --property value.deserializer=org.apache.kafka.common.serialization.LongDeserializer --from-beginning
    ```

    > [!NOTE]
    > A `--property` paraméterek utasítják a konzolfogyasztót, hogy a kulcsot (szó) a számlálóval (érték) együtt írja ki. Ez a paraméter emellett az értékek a Kafkából való beolvasásához használandó deszerializálót is konfigurálja.

    A kimenet az alábbi szöveghez hasonló:
   
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
    > A `--from-beginning` paraméter arra konfigurálja a fogyasztót, hogy a témakörben tárolt rekordok elején kezdjen. A számláló minden egyes szó beolvasásával nő, így a témakör az egyes szavakhoz több bejegyzést is tartalmaz, növekvő számlálóval.

7. A __Ctrl + C__ billentyűparanccsal zárhatja be az előállítót. A __Ctrl + C__ billentyűparancs ismételt lenyomásával zárhatja be az alkalmazást és a fogyasztót is.

## <a name="next-steps"></a>További lépések

Ebben a dokumentumban megismerkedett az Apache Kafka Streams API használata a HDInsight alatt futó Kafka. Az alábbiak további információt biztosítanak a Kafka használatával kapcsolatban:

* [Az Apache Kafka-naplók elemzése](apache-kafka-log-analytics-operations-management.md)
* [Adatok replikálása Apache Kafka-fürtök között](apache-kafka-mirroring.md)
