---
title: 'Oktatóanyag: Az Apache Kafka Producer és Consumer API-k – Azure HDInsight használata '
description: Ismerkedjen meg az Apache Kafka Producer és Consumer API-k a Kafka on HDInsighttal való használatával. Ez az oktatóanyag ezen API-k használatát mutatja be egy Java-alkalmazásból futtatott Kafka on HDInsighttal.
services: hdinsight
author: dhgoelmsft
ms.author: dhgoel
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: tutorial
ms.date: 04/02/2019
ms.openlocfilehash: 6b77cd9939e244fd031788164cdfe391c3e2b9d5
ms.sourcegitcommit: f093430589bfc47721b2dc21a0662f8513c77db1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/04/2019
ms.locfileid: "58916393"
---
# <a name="tutorial-use-the-apache-kafka-producer-and-consumer-apis"></a>Oktatóanyag: Az Apache Kafka Producer és Consumer API-k használata

Ismerkedjen meg az Apache Kafka Producer és Consumer API-k a Kafka on HDInsighttal való használatával.

A Kafka Producer API segítségével az alkalmazások adatstreameket küldhetnek a Kafka-fürtnek. A Kafka Consumer API segítségével az alkalmazások adatstreameket olvashatnak be a fürtből.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Előfeltételek
> * A kód értelmezése
> * Az alkalmazás létrehozása és üzembe helyezése
> * Az alkalmazás futtatása a fürtön

Az API-król további információkat az Apache dokumentációjának [Producer API](https://kafka.apache.org/documentation/#producerapi)-val és [Consumer API](https://kafka.apache.org/documentation/#consumerapi)-val foglalkozó részeiben talál.

## <a name="prerequisites"></a>Előfeltételek

* Apache Kafka on HDInsight 3.6. Ismerje meg, hogyan hozhat létre egy Kafka HDInsight-fürtön, lásd: [a HDInsight Apache Kafka használatának első lépései](apache-kafka-get-started.md).

* [Java fejlesztői készlet (JDK) 8-as verzió](https://aka.ms/azure-jdks) vagy azzal egyenértékű, például az OpenJDK.

* [Az Apache Maven](https://maven.apache.org/download.cgi) megfelelően [telepített](https://maven.apache.org/install.html) Apache megfelelően.  Maven egy projektet a Java-projektek rendszert hozhat létre.

* Egy SSH-ügyfél. További információkért lásd: [HDInsight (az Apache Hadoop) SSH-val csatlakozhat](../hdinsight-hadoop-linux-use-ssh-unix.md).

## <a name="understand-the-code"></a>A kód értelmezése

A példaalkalmazás helye: [https://github.com/Azure-Samples/hdinsight-kafka-java-get-started](https://github.com/Azure-Samples/hdinsight-kafka-java-get-started) (`Producer-Consumer` alkönyvtár). Az alkalmazás elsődlegesen négy fájlból áll:

* `pom.xml`: Ezt a fájlt a projekt függőségeit, Java-verzió és a csomagolási határozza meg.
* `Producer.java`: Ezt a fájlt küld a véletlenszerű mondatokat Kafka az előállítói API-t.
* `Consumer.java`: Ez a fájl gridre bocsáthatja ki az STDOUT és az adatok olvasása a Kafkából a fogyasztói API-t használja.
* `Run.java`: A parancssori felület segítségével az előállítói és fogyasztói kódot futtatta.

### <a name="pomxml"></a>Pom.xml

A `pom.xml` fájl fontosabb elemei a következők:

* Függőségek: Ez a projekt támaszkodik a Kafka producer és consumer API-k által biztosított a `kafka-clients` csomagot. Ezt a függőséget a következő XML-kód határozza meg:

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

* Beépülő modulok: Maven beépülő modulok különféle képességeket biztosítanak. Ebben a projektben a következő beépülő modulokat használjuk:

    * `maven-compiler-plugin`: Használja a projekt 8 által használt Java-verzió beállításához. A HDInsight 3.6 ezt a Java-verziót használja.
    * `maven-shade-plugin`: Az uber jar, amely tartalmazza ezt az alkalmazást, valamint a függőségek létrehozásához használt. Az alkalmazás belépési pontjának beállítására is használható, így közvetlenül futtathatja a JAR-fájlt anélkül, hogy a fő osztályt meg kellene határoznia.

### <a name="producerjava"></a>Producer.java

A Producer a Kafka közvetítő gazdagépeivel (feldolgozó csomópontjaival) kommunikál, és adatok küld egy Kafka-témakörnek. A rendszer a következő kódrészletet a [Producer.java](https://github.com/Azure-Samples/hdinsight-kafka-java-get-started/blob/master/Producer-Consumer/src/main/java/com/microsoft/example/Producer.java) fájlt a [GitHub-adattár](https://github.com/Azure-Samples/hdinsight-kafka-java-get-started) , és bemutatja, hogyan állítsa be a gyártó tulajdonságokat:

```java
Properties properties = new Properties();
// Set the brokers (bootstrap servers)
properties.setProperty("bootstrap.servers", brokers);
// Set how to serialize key/value pairs
properties.setProperty("key.serializer","org.apache.kafka.common.serialization.StringSerializer");
properties.setProperty("value.serializer","org.apache.kafka.common.serialization.StringSerializer");
KafkaProducer<String, String> producer = new KafkaProducer<>(properties);
```

### <a name="consumerjava"></a>Consumer.java

A Consumer a Kafka közvetítő gazdagépeivel (feldolgozó csomópontjaival) kommunikál, majd egy ciklusban beolvassa az adatokat. A [Consumer.java](https://github.com/Azure-Samples/hdinsight-kafka-java-get-started/blob/master/Producer-Consumer/src/main/java/com/microsoft/example/Consumer.java) fájl következő kódrészlete a Consumer tulajdonságait állítja be:

```java
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
```

Ebben a kódban a Consumer úgy van beállítva, hogy a beolvasást a témakör elejétől kezdje (az `auto.offset.reset` beállítása: `earliest`).

### <a name="runjava"></a>Run.java

A [Run.java](https://github.com/Azure-Samples/hdinsight-kafka-java-get-started/blob/master/Producer-Consumer/src/main/java/com/microsoft/example/Run.java) fájlt, amely a gyártó vagy a felhasználói kód parancssori felületet biztosít. Paraméterként a Kafka közvetítő gazdagépének adatait kell megadni. Szükség esetén belefoglalhatja a csoport azonosító érték, a fogyasztói folyamat által használt. Ha több fogyasztói példányok száma a ugyanazon csoport azonosítója alapján hoz létre, azok fog terheléselosztást olvasása a témakörből.

## <a name="build-and-deploy-the-example"></a>A példa létrehozása és üzembe helyezése

1. Töltse le és csomagolja ki a példákat a [ https://github.com/Azure-Samples/hdinsight-kafka-java-get-started ](https://github.com/Azure-Samples/hdinsight-kafka-java-get-started).

2. Állítsa be az aktuális könyvtár helyét a `hdinsight-kafka-java-get-started\Producer-Consumer` könyvtárra, és használja a következő parancsot:

    ```cmd
    mvn clean package
    ```

    A parancs létrehozza a `target` nevű könyvtárat, amely a `kafka-producer-consumer-1.0-SNAPSHOT.jar` nevű fájlt tartalmazza.

3. Cserélje le az `sshuser` elemet a fürt SSH-felhasználójára, illetve a `CLUSTERNAME` elemet a fürt nevére. Másolja a következő parancsot írja be a `kafka-producer-consumer-1.0-SNAPSHOT.jar` fájlt a HDInsight-fürthöz. Ha a rendszer kéri, adja meg az SSH-felhasználó jelszavát.

    ```cmd
    scp ./target/kafka-producer-consumer-1.0-SNAPSHOT.jar sshuser@CLUSTERNAME-ssh.azurehdinsight.net:kafka-producer-consumer.jar
    ```

## <a id="run"></a> A példa futtatása

1. Cserélje le az `sshuser` elemet a fürt SSH-felhasználójára, illetve a `CLUSTERNAME` elemet a fürt nevére. Nyissa meg a fürthöz az SSH-kapcsolatot az alábbi parancs megadásával. Ha a rendszer kéri, adja meg az SSH-felhasználói fiók jelszavát.

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

2. Telepítés [jq](https://stedolan.github.io/jq/), egy parancssori JSON feldolgozó. Nyitott fennálló SSH-kapcsolatból, adja meg az alábbi paranccsal telepíthető `jq`:

    ```bash
    sudo apt -y install jq
    ```

3. Állítsa be a környezeti változók. Cserélje le `PASSWORD` és `CLUSTERNAME` a fürt bejelentkezési jelszava és a fürt neve rendre, majd adja meg a parancsot:

    ```bash
    export password='PASSWORD'
    export clusterNameA='CLUSTERNAME'
    ```

4. Bontsa ki a fürt megfelelően kisbetűsek nevét. A fürt nevét, a tényleges kis-és várható, a fürt létrehozási módjától függően eltérő lehet. Ez a parancs szerezze be a tényleges kis-és nagybetűhasználatot, egy változó tárolja, és majd jelenítse meg a megfelelő cased nevét és a korábban megadott nevét. Írja be a következő parancsot:

    ```bash
    export clusterName=$(curl -u admin:$password -sS -G "https://$clusterNameA.azurehdinsight.net/api/v1/clusters" \
  	| jq -r '.items[].Clusters.cluster_name')
    echo $clusterName, $clusterNameA
    ```

5. A Kafka-közvetítő gazdagépek a gazdagépek és az Apache Zookeeper lekéréséhez használja a következő parancsot:

    ```bash
    export KAFKABROKERS=`curl -sS -u admin:$password -G https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/KAFKA/components/KAFKA_BROKER \
  	| jq -r '["\(.host_components[].HostRoles.host_name):9092"] | join(",")' | cut -d',' -f1,2`
    ```

6. A Kafka-témakört, `myTest`, a következő parancs beírásával:

    ```bash
    java -jar kafka-producer-consumer.jar create myTest $KAFKABROKERS
    ```

7. Az előállító futtatásához és az adatok témakörbe írásához használja az alábbi parancsot:

    ```bash
    java -jar kafka-producer-consumer.jar producer myTest $KAFKABROKERS
    ```

8. Az előállító futásának befejeződését követően használja az alábbi parancsot a témakörből történő olvasáshoz:

    ```bash
    java -jar kafka-producer-consumer.jar consumer myTest $KAFKABROKERS
    ```

    A rendszer megjeleníti a beolvasott rekordokat a rekordok számával együtt.

9. Használja a __Ctrl + C__ billentyűparancsot a fogyasztóból történő kilépéshez.

### <a name="multiple-consumers"></a>Több fogyasztó

A Kafka-fogyasztók egy fogyasztói csoportot használnak a rekordok olvasásakor. Ugyanazon csoport használata több fogyasztó esetén a terhelés szempontjából kiegyensúlyozott olvasást eredményez a témakörökből történő olvasáskor. A csoport mindegyik fogyasztója a rekordok egy részét kapja meg.

A fogyasztó alkalmazás elfogadja a csoportazonosítóként használt paramétert. Az alábbi parancs például elindít egy fogyasztót a `myGroup` csoportazonosítóval:

```bash
java -jar kafka-producer-consumer.jar consumer myTest $KAFKABROKERS myGroup
```

Használja a __Ctrl + C__ billentyűparancsot a fogyasztóból történő kilépéshez.

Ha szeretné látni, hogyan zajlik a folyamat, használja az alábbi parancsot:

```bash
tmux new-session 'java -jar kafka-producer-consumer.jar consumer myTest $KAFKABROKERS myGroup' \
\; split-window -h 'java -jar kafka-producer-consumer.jar consumer myTest $KAFKABROKERS myGroup' \
\; attach
```

Ez a parancs a `tmux` használatával két oszlopra osztja a terminált. Mindegyik oszlopban elindul egy-egy fogyasztó, amelyekhez azonos csoportazonosító-érték tartozik. Ha a fogyasztók befejezték a beolvasást, figyelje meg, hogy mindegyik csak a rekordok egy részét olvasta be. Használat __Ctrl + C__ gombra kétszer a kilépéshez `tmux`.

Az ugyanazon csoportban található ügyfelek általi felhasználás kezelése a témakör partícióinak használatával történik. Ebben a kódmintában a korábban létrehozott `test` témakör nyolc partícióval rendelkezik. Ha elindít nyolc fogyasztót, az egyes fogyasztók a témakör egyetlen partíciójából fognak rekordokat olvasni.

> [!IMPORTANT]  
> A fogyasztói csoportban található fogyasztói példányok száma nem haladhatja meg a partíciók számát. Ebben a példában egy fogyasztói csoport legfeljebb nyolc fogyasztót tartalmazhat, mivel a témakörben ennyi partíció található. Emellett lehet több, legfeljebb nyolc fogyasztóval rendelkező fogyasztói csoportja is.

A Kafkában tárolt rekordok mentése a partíción belüli fogadásuk sorrendje szerint történik. Ha a rekordokat az érkezési sorrendben szeretné kézbesíteni *egy partíción belül*, hozzon létre egy fogyasztói csoportot, amelyben a fogyasztói példányok száma egyezik a partíciók számával. Ha a rekordokat az érkezési sorrendben szeretné kézbesíteni *a témakörön belül*, hozzon létre egy olyan fogyasztói csoportot, amely csak egyetlen fogyasztói példánnyal rendelkezik.

## <a name="next-steps"></a>További lépések

Ebben a dokumentumban megismerkedett az Apache Kafka Producer és Consumer API-k használata a HDInsight alatt futó Kafka. Az alábbiak további információt biztosítanak a Kafka használatával kapcsolatban:

* [Apache Kafka-naplók elemzése](apache-kafka-log-analytics-operations-management.md)
* [Adatok replikálása Apache Kafka-fürtök között](apache-kafka-mirroring.md)
* [Az Apache Kafka Streams API a HDInsight](apache-kafka-streams-api.md)
