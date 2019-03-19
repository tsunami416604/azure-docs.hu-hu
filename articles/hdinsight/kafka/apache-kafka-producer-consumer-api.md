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
ms.date: 11/06/2018
ms.openlocfilehash: 556fc1f04cc6a1d1b594bdd3787ed43d30f607c6
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/18/2019
ms.locfileid: "58091171"
---
# <a name="tutorial-use-the-apache-kafka-producer-and-consumer-apis"></a>Oktatóanyag: Az Apache Kafka Producer és Consumer API-k használata

Ismerkedjen meg az Apache Kafka Producer és Consumer API-k a Kafka on HDInsighttal való használatával.

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

* [Java JDK 8](https://aka.ms/azure-jdks) vagy azzal egyenértékű, például az OpenJDK.

* [Apache Maven](https://maven.apache.org/)

* Egy SSH-ügyfél és az `scp` parancs. További információ: [SSH használata a HDInsighttal](../hdinsight-hadoop-linux-use-ssh-unix.md).

* Szövegszerkesztő vagy Java IDE.

Az alábbi környezeti változók állíthatók be a Java és a JDK fejlesztői munkaállomáson történő telepítésekor. Érdemes azonban ellenőriznie, hogy a fentiek léteznek-e, és a rendszer számára megfelelő értékeket tartalmaznak-e.

* `JAVA_HOME` – arra a könyvtárra mutasson, ahová a JDK telepítve lett.
* `PATH` – a következő elérési utakat kell tartalmaznia:

    * `JAVA_HOME` (vagy ezzel egyenértékű elérési út).
    * `JAVA_HOME\bin` (vagy ezzel egyenértékű elérési út).
    * A Maven telepítési könyvtára.

## <a name="set-up-your-deployment-environment"></a>Az üzembehelyezési környezet beállítása

Az oktatóanyag elvégzéséhez az Apache Kafka on HDInsight 3.6-os verziójára van szükség. Ismerje meg, hogyan hozhat létre egy Kafka HDInsight-fürtön, tekintse meg a [a HDInsight Apache Kafka használatának első lépései](apache-kafka-get-started.md) dokumentumot.

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

A Producer a Kafka közvetítő gazdagépeivel (feldolgozó csomópontjaival) kommunikál, és adatok küld egy Kafka-témakörnek. A rendszer a következő kódrészletet a a [Producer.java](https://github.com/Azure-Samples/hdinsight-kafka-java-get-started/blob/master/Producer-Consumer/src/main/java/com/microsoft/example/Producer.java) fájlt a [GitHub-adattár](https://github.com/Azure-Samples/hdinsight-kafka-java-get-started) , és bemutatja, hogyan állítsa be a gyártó tulajdonságokat:

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

A [Run.java](https://github.com/Azure-Samples/hdinsight-kafka-java-get-started/blob/master/Producer-Consumer/src/main/java/com/microsoft/example/Run.java) fájl parancssori felületet biztosít a Producer- vagy a Consumer-kód futtatásához. Paraméterként a Kafka közvetítő gazdagépének adatait kell megadni. Opcionálisan megadhat egy csoportazonosító-értéket, amit a fogyasztói folyamat fog használni. Ha több, azonos csoportazonosítóval ellátott fogyasztói példányt hoz létre, azok a terheléselosztáshoz szükséges adatokat a témakörből olvassák ki.

## <a name="build-and-deploy-the-example"></a>A példa létrehozása és üzembe helyezése

1. és 2 build lépéseit kihagyhatja, és töltse le az előre elkészített jars(kafka-producer-consumer.jar) a [ https://github.com/Azure-Samples/hdinsight-kafka-java-get-started/tree/master/Prebuilt-Jars ](https://github.com/Azure-Samples/hdinsight-kafka-java-get-started/tree/master/Prebuilt-Jars). A HDInsight-fürt is másolja a JAR-fájl.

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
    
    2. A Kafka-közvetítő gazdagépek a gazdagépek és az Apache Zookeeper lekéréséhez használja a következő parancsokat. Ha a rendszer kéri, adja meg a fürt bejelentkezési (rendszergazdai) fiókjának jelszavát.
    
        ```bash
        export KAFKABROKERS=`curl -sS -u admin -G https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/services/KAFKA/components/KAFKA_BROKER | jq -r '["\(.host_components[].HostRoles.host_name):9092"] | join(",")' | cut -d',' -f1,2`; \
        ```

    3. A `test` témakör létrehozásához használja az alábbi parancsot:

        ```bash
        java -jar kafka-producer-consumer.jar create test $KAFKABROKERS
        ```

3. Az előállító futtatásához és az adatok témakörbe írásához használja az alábbi parancsot:

    ```bash
    java -jar kafka-producer-consumer.jar producer test $KAFKABROKERS
    ```

4. Az előállító futásának befejeződését követően használja az alábbi parancsot a témakörből történő olvasáshoz:

    ```bash
    java -jar kafka-producer-consumer.jar consumer test $KAFKABROKERS
    ```

    A rendszer megjeleníti a beolvasott rekordokat a rekordok számával együtt.

5. Használja a __Ctrl + C__ billentyűparancsot a fogyasztóból történő kilépéshez.

### <a name="multiple-consumers"></a>Több fogyasztó

A Kafka-fogyasztók egy fogyasztói csoportot használnak a rekordok olvasásakor. Ugyanazon csoport használata több fogyasztó esetén a terhelés szempontjából kiegyensúlyozott olvasást eredményez a témakörökből történő olvasáskor. A csoport mindegyik fogyasztója a rekordok egy részét kapja meg.

A fogyasztó alkalmazás elfogadja a csoportazonosítóként használt paramétert. Az alábbi parancs például elindít egy fogyasztót a `mygroup` csoportazonosítóval:

```bash
java -jar kafka-producer-consumer.jar consumer test $KAFKABROKERS mygroup
```

Ha szeretné látni, hogyan zajlik a folyamat, használja az alábbi parancsot:

```bash
tmux new-session 'java -jar kafka-producer-consumer.jar consumer test $KAFKABROKERS mygroup' \; split-w
indow -h 'java -jar kafka-producer-consumer.jar consumer test $KAFKABROKERS mygroup' \; attach
```

Ez a parancs a `tmux` használatával két oszlopra osztja a terminált. Mindegyik oszlopban elindul egy-egy fogyasztó, amelyekhez azonos csoportazonosító-érték tartozik. Ha a fogyasztók befejezték a beolvasást, figyelje meg, hogy mindegyik csak a rekordok egy részét olvasta be. Használat __Ctrl + C__ gombra kétszer a kilépéshez `tmux`.

Az ugyanazon csoportban található ügyfelek általi felhasználás kezelése a témakör partícióinak használatával történik. Ebben a kódmintában a korábban létrehozott `test` témakör nyolc partícióval rendelkezik. Ha elindít nyolc fogyasztót, az egyes fogyasztók a témakör egyetlen partíciójából fognak rekordokat olvasni.

> [!IMPORTANT]  
> A fogyasztói csoportban található fogyasztói példányok száma nem haladhatja meg a partíciók számát. Ebben a példában egy fogyasztói csoport legfeljebb nyolc fogyasztót tartalmazhat, mivel a témakörben ennyi partíció található. Emellett lehet több, legfeljebb nyolc fogyasztóval rendelkező fogyasztói csoportja is.

A Kafkában tárolt rekordok mentése a partíción belüli fogadásuk sorrendje szerint történik. Ha a rekordokat az érkezési sorrendben szeretné kézbesíteni *egy partíción belül*, hozzon létre egy fogyasztói csoportot, amelyben a fogyasztói példányok száma egyezik a partíciók számával. Ha a rekordokat az érkezési sorrendben szeretné kézbesíteni *a témakörön belül*, hozzon létre egy olyan fogyasztói csoportot, amely csak egyetlen fogyasztói példánnyal rendelkezik.

## <a name="next-steps"></a>További lépések

Ebben a dokumentumban megismerkedett az Apache Kafka Producer és Consumer API-k használata a HDInsight alatt futó Kafka. Az alábbiak további információt biztosítanak a Kafka használatával kapcsolatban:

* [Az Apache Kafka-naplók elemzése](apache-kafka-log-analytics-operations-management.md)
* [Adatok replikálása Apache Kafka-fürtök között](apache-kafka-mirroring.md)
* [Az Apache Kafka Streams API a HDInsight](apache-kafka-streams-api.md)
