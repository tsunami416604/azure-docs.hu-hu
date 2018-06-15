---
title: 'Oktatóanyag: Az Apache Kafka Producer és Consumer API-k használata – Azure HDInsight | Microsoft Docs'
description: Ismerkedjen meg az Apache Kafka Producer és Consumer API-k a Kafka on HDInsighttal való használatával. Ez az oktatóanyag ezen API-k használatát mutatja be egy Java-alkalmazásból futtatott Kafka on HDInsighttal.
services: hdinsight
documentationcenter: ''
author: Blackmist
manager: cgronlun
editor: cgronlun
tags: azure-portal
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: tutorial
ms.date: 04/16/2018
ms.author: larryfr
ms.openlocfilehash: b602f8bfe316e9c11dbff18273f37c99407c3da6
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/07/2018
ms.locfileid: "33771150"
---
# <a name="tutorial-use-the-apache-kafka-producer-and-consumer-apis"></a>Oktatóanyag: Az Apache Kafka Producer és Consumer API-k használata

Ismerkedjen meg a Kafka Producer és Consumer API-k a Kafka on HDInsighttal való használatával.

A Kafka Producer API segítségével az alkalmazások adatstreameket küldhetnek a Kafka-fürtnek. A Kafka Consumer API segítségével az alkalmazások adatstreameket olvashatnak be a fürtből.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * A fejlesztési környezet beállítása
> * Az üzembehelyezési környezet beállítása
> * A kód értelmezése
> * Az alkalmazás létrehozása és üzembe helyezése
> * Az alkalmazás futtatása a fürtön

Az API-król további információkat az Apache dokumentációjának [Producer API](https://kafka.apache.org/documentation/#producerapi)-val és [Consumer API](https://kafka.apache.org/documentation/#consumerapi)-val foglalkozó részeiben talál.

## <a name="set-up-your-development-environment"></a>A fejlesztési környezet beállítása

A fejlesztési környezetben a következő összetevőket kell telepítenie:

* [Java JDK 8](http://www.oracle.com/technetwork/java/javase/downloads/index.html) vagy azzal egyenértékű, például az OpenJDK.

* [Apache Maven](http://maven.apache.org/)

* Egy SSH-ügyfél és az `scp` parancs. További információ: [SSH használata a HDInsighttal](../hdinsight-hadoop-linux-use-ssh-unix.md).

* Szövegszerkesztő vagy Java IDE.

Az alábbi környezeti változók állíthatók be a Java és a JDK fejlesztői munkaállomáson történő telepítésekor. Érdemes azonban ellenőriznie, hogy a fentiek léteznek-e, és a rendszer számára megfelelő értékeket tartalmaznak-e.

* `JAVA_HOME` – arra a könyvtárra mutasson, ahová a JDK telepítve lett.
* `PATH` – a következő elérési utakat kell tartalmaznia:
  
    * `JAVA_HOME` (vagy ezzel egyenértékű elérési út).
    * `JAVA_HOME\bin` (vagy ezzel egyenértékű elérési út).
    * A Maven telepítési könyvtára.

## <a name="set-up-your-deployment-environment"></a>Az üzembehelyezési környezet beállítása

Az oktatóanyag elvégzéséhez a Kafka on HDInsight 3.6-os verziójára van szükség. A Kafka on HDInsight-fürtök létrehozásával kapcsolatban tekintse meg [a Kafka on HDInsight használatának első lépéseit](apache-kafka-get-started.md) ismertető dokumentumot.

## <a name="understand-the-code"></a>A kód értelmezése

A példaalkalmazás helye: [https://github.com/Azure-Samples/hdinsight-kafka-java-get-started](https://github.com/Azure-Samples/hdinsight-kafka-java-get-started) (`Producer-Consumer` alkönyvtár). Az alkalmazás elsődlegesen négy fájlból áll:

* `pom.xml`: Ez a fájl határozza meg a projektfüggőségeket, a Java-verziót és a csomagolási módszereket.
* `Producer.java`: Ez a fájl 1 millió (1 000 000) véletlenszerű mondatot küld a Kafkának a Producer API használatával.
* `Consumer.java`: Ez a fájl a Consumer API-t használatával olvas ki adatokat a Kafkából, és az STDOUT-ba küldi el azokat.
* `Run.java`: A Producer- és Consumer-kód futtatásához a rendszer a parancssori felületet használja.

### <a name="pomxml"></a>Pom.xml

A `pom.xml` fájl fontosabb elemei a következők:

* Függőségek: Ez a projekt a Kafka Producer és Consumer API-jaira támaszkodik, amelyeket a `kafka-clients` csomag tartalmaz. Ezt a függőséget a következő XML-kód határozza meg:

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

    * `maven-compiler-plugin`: Ezzel állítható a projekt által használt Java-verzió a 8-as verzióra. A HDInsight 3.6 ezt a Java-verziót használja.
    * `maven-shade-plugin`: Ez az alkalmazást és az esetleges függőségeket tartalmazó uber JAR-fájl létrehozására használható. Az alkalmazás belépési pontjának beállítására is használható, így közvetlenül futtathatja a JAR-fájlt anélkül, hogy a fő osztályt meg kellene határoznia.

### <a name="producerjava"></a>Producer.java

A Producer a Kafka közvetítő gazdagépeivel (feldolgozó csomópontjaival) kommunikál az adatok Kafka-témakörben történő mentéséhez. A következő kódrészlet a `Producer.java` fájlból származik:

```java
package com.microsoft.example;

import org.apache.kafka.clients.producer.KafkaProducer;
import org.apache.kafka.clients.producer.ProducerRecord;
import java.util.Properties;
import java.util.Random;
import java.io.IOException;

public class Producer
{
    public static void produce(String brokers) throws IOException
    {

        // Set properties used to configure the producer
        Properties properties = new Properties();
        // Set the brokers (bootstrap servers)
        properties.setProperty("bootstrap.servers", brokers);
        // Set how to serialize key/value pairs
        properties.setProperty("key.serializer","org.apache.kafka.common.serialization.StringSerializer");
        properties.setProperty("value.serializer","org.apache.kafka.common.serialization.StringSerializer");
        KafkaProducer<String, String> producer = new KafkaProducer<>(properties);

        // So we can generate random sentences
        Random random = new Random();
        String[] sentences = new String[] {
                "the cow jumped over the moon",
                "an apple a day keeps the doctor away",
                "four score and seven years ago",
                "snow white and the seven dwarfs",
                "i am at two with nature"
        };

        String progressAnimation = "|/-\\";
        // Produce a bunch of records
        for(int i = 0; i < 1000000; i++) {
            // Pick a sentence at random
            String sentence = sentences[random.nextInt(sentences.length)];
            // Send the sentence to the test topic
            producer.send(new ProducerRecord<String, String>("test", sentence));
            String progressBar = "\r" + progressAnimation.charAt(i % progressAnimation.length()) + " " + i;
            System.out.write(progressBar.getBytes());
        }
    }
}
```

Ez a kód kapcsolatot létesít a Kafka közvetítő gazdagépeivel (feldolgozó csomópontjaival), majd a Producer API használatával elküld 1 000 000 mondatot a Kafkának.

### <a name="consumerjava"></a>Consumer.java

A Consumer a Kafka közvetítő gazdagépeivel (feldolgozó csomópontjaival) kommunikál, majd egy ciklusban beolvassa az adatokat. A következő kódrészlet a `Consumer.java` fájlból származik:

```java
package com.microsoft.example;

import org.apache.kafka.clients.consumer.KafkaConsumer;
import org.apache.kafka.clients.consumer.ConsumerRecords;
import org.apache.kafka.clients.consumer.ConsumerRecord;
import java.util.Properties;
import java.util.Arrays;

public class Consumer {
    public static void consume(String brokers, String groupId) {
        // Create a consumer
        KafkaConsumer<String, String> consumer;
        // Configure the consumer
        Properties properties = new Properties();
        // Point it to the brokers
        properties.setProperty("bootstrap.servers", brokers);
        // Set the consumer group (all consumers must belong to a group).
        properties.setProperty("group.id", groupId);
        // Set how to serialize key/value pairs
        properties.setProperty("key.deserializer","org.apache.kafka.common.serialization.StringDeserializer");
        properties.setProperty("value.deserializer","org.apache.kafka.common.serialization.StringDeserializer");
        // When a group is first created, it has no offset stored to start reading from. This tells it to start
        // with the earliest record in the stream.
        properties.setProperty("auto.offset.reset","earliest");
        consumer = new KafkaConsumer<>(properties);

        // Subscribe to the 'test' topic
        consumer.subscribe(Arrays.asList("test"));

        // Loop until ctrl + c
        int count = 0;
        while(true) {
            // Poll for records
            ConsumerRecords<String, String> records = consumer.poll(200);
            // Did we get any?
            if (records.count() == 0) {
                // timeout/nothing to read
            } else {
                // Yes, loop over records
                for(ConsumerRecord<String, String> record: records) {
                    // Display record and count
                    count += 1;
                    System.out.println( count + ": " + record.value());
                }
            }
        }
    }
}
```

Ebben a kódban a Consumer úgy van beállítva, hogy a beolvasást a témakör elejétől kezdje (az `auto.offset.reset` beállítása: `earliest`).

### <a name="runjava"></a>Run.java

A `Run.java` fájl parancssori felületet biztosít a Producer- vagy a Consumer-kód futtatásához. Paraméterként a Kafka közvetítő gazdagépének adatait kell megadni. Opcionálisan megadhat egy csoportazonosító-értéket, amit a fogyasztói folyamat fog használni. Ha több, azonos csoportazonosítóval ellátott fogyasztói példányt hoz létre, azok a terheléselosztáshoz szükséges adatokat a témakörből olvassák ki.

```java
package com.microsoft.example;

import java.io.IOException;
import java.util.UUID;

// Handle starting producer or consumer
public class Run {
    public static void main(String[] args) throws IOException {
        if(args.length < 2) {
            usage();
        }

        // Get the brokers
        String brokers = args[1];
        switch(args[0].toLowerCase()) {
            case "producer":
                Producer.produce(brokers);
                break;
            case "consumer":
                // Either a groupId was passed in, or we need a random one
                String groupId;
                if(args.length == 3) {
                    groupId = args[2];
                } else {
                    groupId = UUID.randomUUID().toString();
                }
                Consumer.consume(brokers, groupId);
                break;
            default:
                usage();
        }
        System.exit(0);
    }
    // Display usage
    public static void usage() {
        System.out.println("Usage:");
        System.out.println("kafka-example.jar <producer|consumer> brokerhosts [groupid]");
        System.exit(1);
    }
}
```

## <a name="build-and-deploy-the-example"></a>A példa létrehozása és üzembe helyezése

1. A példákat a [https://github.com/Azure-Samples/hdinsight-kafka-java-get-started](https://github.com/Azure-Samples/hdinsight-kafka-java-get-started) címről töltheti le.

2. Lépjen a `Producer-Consumer` könyvtár helyére, majd használja az alábbi parancsot:

    ```
    mvn clean package
    ```

    A parancs létrehozza a `target` nevű könyvtárat, amely a `kafka-producer-consumer-1.0-SNAPSHOT.jar` nevű fájlt tartalmazza.

3. Az alábbi parancsokkal másolja a `kafka-producer-consumer-1.0-SNAPSHOT.jar` fájlt a HDInsight-fürtbe:
   
    ```bash
    scp ./target/kafka-producer-consumer-1.0-SNAPSHOT.jar SSHUSER@CLUSTERNAME-ssh.azurehdinsight.net:kafka-producer-consumer.jar
    ```
   
    Cserélje le az **SSHUSER** elemet a fürt SSH-felhasználójára, illetve a **CLUSTERNAME** elemet a fürt nevére. Ha a rendszer kéri, adja meg az SSH-felhasználó jelszavát.

## <a id="run"></a> A példa futtatása

1. A fürttel létesített SSH-kapcsolat megnyitásához használja a következő parancsot:

    ```bash
    ssh SSHUSER@CLUSTERNAME-ssh.azurehdinsight.net
    ```

    Cserélje le az **SSHUSER** elemet a fürt SSH-felhasználójára, illetve a **CLUSTERNAME** elemet a fürt nevére. Ha a rendszer kéri, adja meg az SSH-felhasználói fiók jelszavát. Az `scp` HDInsighttal való használatával kapcsolatos további információkat [az SSH a HDInsighttal való használatáról szóló cikkben](../hdinsight-hadoop-linux-use-ssh-unix.md) találhat.

2. A jelen példa által használt Kafka-témakörök létrehozásához kövesse a következő lépéseket:

    1. A fürtnév változóként való mentéséhez és egy JSON-elemző segédprogram (`jq`) telepítéséhez használja az alábbi parancsokat. Ha a rendszer kéri, adja meg a Kafka-fürt nevét:
    
        ```bash
        sudo apt -y install jq
        read -p 'Enter your Kafka cluster name:' CLUSTERNAME
        ```
    
    2. A Kafka közvetítő gazdagépeinek és a Zookeeper-gazdagépek beolvasását az alábbi parancsokkal végezheti el. Ha a rendszer kéri, adja meg a fürt bejelentkezési (rendszergazdai) fiókjának jelszavát.
    
        ```bash
        export KAFKAZKHOSTS=`curl -sS -u admin -G https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/services/ZOOKEEPER/components/ZOOKEEPER_SERVER | jq -r '["\(.host_components[].HostRoles.host_name):2181"] | join(",")' | cut -d',' -f1,2`; \
        export KAFKABROKERS=`curl -sS -u admin -G https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/services/KAFKA/components/KAFKA_BROKER | jq -r '["\(.host_components[].HostRoles.host_name):9092"] | join(",")' | cut -d',' -f1,2`; \
        ```

    3. A `test` témakör létrehozásához használja az alábbi parancsot:

        ```bash
        /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --create --replication-factor 3 --partitions 8 --topic test --zookeeper $KAFKAZKHOSTS
        ```

3. Az előállító futtatásához és az adatok témakörbe írásához használja az alábbi parancsot:

    ```bash
    java -jar kafka-producer-consumer.jar producer $KAFKABROKERS
    ```

4. Az előállító futásának befejeződését követően használja az alábbi parancsot a témakörből történő olvasáshoz:
   
    ```bash
    java -jar kafka-producer-consumer.jar consumer $KAFKABROKERS
    ```
   
    A rendszer megjeleníti a beolvasott rekordokat a rekordok számával együtt.

5. Használja a __Ctrl + C__ billentyűparancsot a fogyasztóból történő kilépéshez.

### <a name="multiple-consumers"></a>Több fogyasztó

A Kafka-fogyasztók egy fogyasztói csoportot használnak a rekordok olvasásakor. Ugyanazon csoport használata több fogyasztó esetén a terhelés szempontjából kiegyensúlyozott olvasást eredményez a témakörökből történő olvasáskor. A csoport mindegyik fogyasztója a rekordok egy részét kapja meg.

A fogyasztó alkalmazás elfogadja a csoportazonosítóként használt paramétert. Az alábbi parancs például elindít egy fogyasztót a `mygroup` csoportazonosítóval:
   
```bash
java -jar kafka-producer-consumer.jar consumer $KAFKABROKERS mygroup
```

Ha szeretné látni, hogyan zajlik a folyamat, használja az alábbi parancsot:

```bash
tmux new-session 'java -jar kafka-producer-consumer.jar consumer $KAFKABROKERS mygroup' \; split-w
indow -h 'java -jar kafka-producer-consumer.jar consumer $KAFKABROKERS mygroup' \; attach
```

Ez a parancs a `tmux` használatával két oszlopra osztja a terminált. Mindegyik oszlopban elindul egy-egy fogyasztó, amelyekhez azonos csoportazonosító-érték tartozik. Ha a fogyasztók befejezték a beolvasást, figyelje meg, hogy mindegyik csak a rekordok egy részét olvasta be. A `tmux`-ból való kilépéshez nyomja le kétszer a Ctrl + C billentyűkombinációt.

Az ugyanazon csoportban található ügyfelek általi felhasználás kezelése a témakör partícióinak használatával történik. A korábban létrehozott `test` témakör nyolc partícióval rendelkezik. Ha elindít nyolc fogyasztót, az egyes fogyasztók a témakör egyetlen partíciójából fognak rekordokat olvasni.

> [!IMPORTANT]
> A fogyasztói csoportban található fogyasztói példányok száma nem haladhatja meg a partíciók számát. Ebben a példában egy fogyasztói csoport legfeljebb nyolc fogyasztót tartalmazhat, mivel a témakörben ennyi partíció található. Emellett lehet több, legfeljebb nyolc fogyasztóval rendelkező fogyasztói csoportja is.

A Kafkában tárolt rekordok mentése a partíción belüli fogadásuk sorrendje szerint történik. Ha a rekordokat az érkezési sorrendben szeretné kézbesíteni *egy partíción belül*, hozzon létre egy fogyasztói csoportot, amelyben a fogyasztói példányok száma egyezik a partíciók számával. Ha a rekordokat az érkezési sorrendben szeretné kézbesíteni *a témakörön belül*, hozzon létre egy olyan fogyasztói csoportot, amely csak egyetlen fogyasztói példánnyal rendelkezik.

## <a name="next-steps"></a>További lépések

Ebben a dokumentumban megismerkedett a Kafka Producer és Consumer API-k a Kafka on HDInsighttal való használatával. Az alábbiak további információt biztosítanak a Kafka használatával kapcsolatban:

* [Kafka-naplók elemzése](apache-kafka-log-analytics-operations-management.md)
* [Adatreplikálás Kafka-fürtök között](apache-kafka-mirroring.md)
* [Kafka Streams API a HDInsighttal](apache-kafka-streams-api.md)