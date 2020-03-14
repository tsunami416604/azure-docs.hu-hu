---
title: 'Oktatóanyag: Apache Kafka producer & fogyasztói API-k – Azure HDInsight'
description: Ismerkedjen meg az Apache Kafka Producer és Consumer API-k a Kafka on HDInsighttal való használatával. Ez az oktatóanyag ezen API-k használatát mutatja be egy Java-alkalmazásból futtatott Kafka on HDInsighttal.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: tutorial
ms.date: 10/08/2019
ms.openlocfilehash: 5a7d4d1917f65cd3d836db83600937a3e3d89de6
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/13/2020
ms.locfileid: "79239538"
---
# <a name="tutorial-use-the-apache-kafka-producer-and-consumer-apis"></a>Oktatóanyag: Az Apache Kafka Producer és Consumer API-k használata

Ismerkedjen meg az Apache Kafka Producer és Consumer API-k a Kafka on HDInsighttal való használatával.

A Kafka Producer API segítségével az alkalmazások adatstreameket küldhetnek a Kafka-fürtnek. A Kafka Consumer API segítségével az alkalmazások adatstreameket olvashatnak be a fürtből.

Ez az oktatóanyag bemutatja, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> * Előfeltételek
> * A kód értelmezése
> * Az alkalmazás létrehozása és üzembe helyezése
> * Az alkalmazás futtatása a fürtön

Az API-król további információkat az Apache dokumentációjának [Producer API](https://kafka.apache.org/documentation/#producerapi)-val és [Consumer API](https://kafka.apache.org/documentation/#consumerapi)-val foglalkozó részeiben talál.

## <a name="prerequisites"></a>Előfeltételek

* Apache Kafka a HDInsight-fürtön. A fürt létrehozásával kapcsolatos további információkért tekintse [meg a Start with apache Kafka on HDInsight](apache-kafka-get-started.md)című témakört.
* A [Java Developer Kit (JDK) 8-as verziója](https://aka.ms/azure-jdks) vagy azzal egyenértékű, például OpenJDK.
* Az [Apache Maven](https://maven.apache.org/download.cgi) megfelelően [van telepítve](https://maven.apache.org/install.html) az Apache-ban.  A Maven egy projekt-összeállítási rendszer Java-projektekhez.
* Egy SSH-ügyfél, például a Putty. További információ: [Kapcsolódás HDInsight (Apache Hadoop) SSH használatával](../hdinsight-hadoop-linux-use-ssh-unix.md).

## <a name="understand-the-code"></a>A kód értelmezése

A példaalkalmazás helye: [https://github.com/Azure-Samples/hdinsight-kafka-java-get-started](https://github.com/Azure-Samples/hdinsight-kafka-java-get-started) (`Producer-Consumer` alkönyvtár). Ha **Enterprise Security Package (ESP)** engedélyezett Kafka-fürtöt használ, a `DomainJoined-Producer-Consumer` alkönyvtárban található alkalmazás verzióját kell használnia.

Az alkalmazás elsődlegesen négy fájlból áll:
* `pom.xml`: Ez a fájl határozza meg a projektfüggőségeket, a Java-verziót és a csomagolási módszereket.
* `Producer.java`: Ez a fájl véletlenszerű mondatokat küld a Kafkának a Producer API használatával.
* `Consumer.java`: Ez a fájl a Consumer API-t használatával olvas ki adatokat a Kafkából, és az STDOUT-ba küldi el azokat.
* `AdminClientWrapper.java`: Ez a fájl a rendszergazdai API-t használja a Kafka-témakörök létrehozására, leírására és törlésére.
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

    A `${kafka.version}` bejegyzés a `<properties>..</properties>` fájl `pom.xml` szakaszában van meghatározva, és a HDInsight-fürt Kafka-verziójához van konfigurálva.

* Beépülő modulok: A Maven beépülő modulok különböző képességekkel rendelkeznek. Ebben a projektben a következő beépülő modulokat használjuk:

    * `maven-compiler-plugin`: Ezzel állítható a projekt által használt Java-verzió a 8-as verzióra. A HDInsight 3.6 ezt a Java-verziót használja.
    * `maven-shade-plugin`: Ez az alkalmazást és az esetleges függőségeket tartalmazó uber JAR-fájl létrehozására használható. Az alkalmazás belépési pontjának beállítására is használható, így közvetlenül futtathatja a JAR-fájlt anélkül, hogy a fő osztályt meg kellene határoznia.

### <a name="producerjava"></a>Producer.java

A Producer a Kafka közvetítő gazdagépeivel (feldolgozó csomópontjaival) kommunikál, és adatok küld egy Kafka-témakörnek. A következő kódrészlet a [GitHub-tárházból](https://github.com/Azure-Samples/hdinsight-kafka-java-get-started) származó [producer. Java](https://github.com/Azure-Samples/hdinsight-kafka-java-get-started/blob/master/Producer-Consumer/src/main/java/com/microsoft/example/Producer.java) fájlból származik, és bemutatja, hogyan állíthatja be a termelő tulajdonságait:

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

A [Run. Java](https://github.com/Azure-Samples/hdinsight-kafka-java-get-started/blob/master/Producer-Consumer/src/main/java/com/microsoft/example/Run.java) fájl olyan parancssori felületet biztosít, amely a gyártó vagy a fogyasztó kódját futtatja. Paraméterként a Kafka közvetítő gazdagépének adatait kell megadni. Igény szerint a felhasználói folyamat által használt csoportazonosító értéket is megadhatja. Ha ugyanazzal a csoport-AZONOSÍTÓval hoz létre több fogyasztói példányt, a rendszer a témakörből olvassa be a terheléselosztási egyenleget.

## <a name="build-and-deploy-the-example"></a>A példa létrehozása és üzembe helyezése

Ha ki szeretné hagyni ezt a lépést, az előre összeépített tégelyek a `Prebuilt-Jars` alkönyvtárból tölthetők le. Töltse le a Kafka-producer-Consumer. jar fájlt. Ha a fürt **Enterprise Security Package (ESP)** engedélyezve van, használja a Kafka-producer-Consumer-ESP. jar fájlt. Futtassa a 3. lépést a jar a HDInsight-fürtre történő másolásához.

1. Töltse le és csomagolja ki a példákat [https://github.com/Azure-Samples/hdinsight-kafka-java-get-started](https://github.com/Azure-Samples/hdinsight-kafka-java-get-started).

2. Állítsa be az aktuális könyvtárat a `hdinsight-kafka-java-get-started\Producer-Consumer` könyvtárának helyére. Ha **Enterprise Security Package (ESP)** engedélyezett Kafka-fürtöt használ, a helyet `DomainJoined-Producer-Consumer`alkönyvtárra kell beállítania. Az alkalmazás létrehozásához használja a következő parancsot:

    ```cmd
    mvn clean package
    ```

    A parancs létrehozza a `target` nevű könyvtárat, amely a `kafka-producer-consumer-1.0-SNAPSHOT.jar` nevű fájlt tartalmazza.

3. Cserélje le az `sshuser` elemet a fürt SSH-felhasználójára, illetve a `CLUSTERNAME` elemet a fürt nevére. A következő parancs beírásával másolja a `kafka-producer-consumer-1.0-SNAPSHOT.jar`-fájlt a HDInsight-fürtre. Ha a rendszer kéri, adja meg az SSH-felhasználó jelszavát.

    ```cmd
    scp ./target/kafka-producer-consumer-1.0-SNAPSHOT.jar sshuser@CLUSTERNAME-ssh.azurehdinsight.net:kafka-producer-consumer.jar
    ```

## <a id="run"></a> A példa futtatása

1. Cserélje le az `sshuser` elemet a fürt SSH-felhasználójára, illetve a `CLUSTERNAME` elemet a fürt nevére. Nyisson meg egy SSH-kapcsolatokat a fürttel a következő parancs beírásával. Ha a rendszer kéri, adja meg az SSH-felhasználói fiók jelszavát.

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. A Kafka-átvitelszervező gazdagépek beszerzéséhez helyettesítse `<clustername>` és `<password>` értékeit a következő parancsban, majd hajtsa végre. Használja ugyanazt a burkolatot `<clustername>` a Azure Portalban látható módon. Cserélje le a `<password>`t a fürt bejelentkezési jelszavára, majd hajtsa végre a következőket:

    ```bash
    sudo apt -y install jq
    export clusterName='<clustername>'
    export password='<password>'
    export KAFKABROKERS=$(curl -sS -u admin:$password -G https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/KAFKA/components/KAFKA_BROKER | jq -r '["\(.host_components[].HostRoles.host_name):9092"] | join(",")' | cut -d',' -f1,2);
    ```

    > [!Note]  
    > Ehhez a parancshoz Ambari-hozzáférés szükséges. Ha a fürt egy NSG mögött található, futtassa ezt a parancsot egy olyan gépről, amely hozzáférhet a Ambari.

1. Hozzon létre egy Kafka-témakört `myTest`a következő parancs beírásával:

    ```bash
    java -jar kafka-producer-consumer.jar create myTest $KAFKABROKERS
    ```

1. Az előállító futtatásához és az adatok témakörbe írásához használja az alábbi parancsot:

    ```bash
    java -jar kafka-producer-consumer.jar producer myTest $KAFKABROKERS
    ```

1. Az előállító futásának befejeződését követően használja az alábbi parancsot a témakörből történő olvasáshoz:

    ```bash
    java -jar kafka-producer-consumer.jar consumer myTest $KAFKABROKERS
    ```

    A rendszer megjeleníti a beolvasott rekordokat a rekordok számával együtt.

1. Használja a __Ctrl + C__ billentyűparancsot a fogyasztóból történő kilépéshez.

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

Ez a parancs a `tmux` használatával két oszlopra osztja a terminált. Mindegyik oszlopban elindul egy-egy fogyasztó, amelyekhez azonos csoportazonosító-érték tartozik. Ha a fogyasztók befejezték a beolvasást, figyelje meg, hogy mindegyik csak a rekordok egy részét olvasta be. A __CTRL + C billentyűkombinációt__ kétszer is használhatja a `tmux`kilépéséhez.

Az ugyanazon csoportban található ügyfelek általi felhasználás kezelése a témakör partícióinak használatával történik. Ebben a kódmintában a korábban létrehozott `test` témakör nyolc partícióval rendelkezik. Ha elindít nyolc fogyasztót, az egyes fogyasztók a témakör egyetlen partíciójából fognak rekordokat olvasni.

> [!IMPORTANT]  
> A fogyasztói csoportban található fogyasztói példányok száma nem haladhatja meg a partíciók számát. Ebben a példában egy fogyasztói csoport legfeljebb nyolc fogyasztót tartalmazhat, mivel a témakörben ennyi partíció található. Emellett lehet több, legfeljebb nyolc fogyasztóval rendelkező fogyasztói csoportja is.

A Kafka-ben tárolt rekordok tárolása a partíción belül kapott sorrendben történik. Ha a rekordokat az érkezési sorrendben szeretné kézbesíteni *egy partíción belül*, hozzon létre egy fogyasztói csoportot, amelyben a fogyasztói példányok száma egyezik a partíciók számával. Ha a rekordokat az érkezési sorrendben szeretné kézbesíteni *a témakörön belül*, hozzon létre egy olyan fogyasztói csoportot, amely csak egyetlen fogyasztói példánnyal rendelkezik.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha törölni szeretné a jelen oktatóanyag által létrehozott erőforrásokat, akkor törölje az erőforráscsoportot. Az erőforráscsoport törlésekor a kapcsolódó HDInsight-fürt, valamint az esetlegesen az erőforráscsoporthoz társított egyéb erőforrások is törlődnek.

Az erőforráscsoport eltávolítása az Azure Portallal:

1. Az Azure Portalon bontsa ki a bal oldalon a szolgáltatásmenüt, és válassza az __Erőforráscsoportok__ lehetőséget az erőforráscsoportok listájának megjelenítéséhez.
2. Keresse meg a törölni kívánt erőforráscsoportot, és kattintson a jobb gombbal a lista jobb oldalán lévő __Továbbiak__ gombra (...).
3. Válassza az __Erőforráscsoport törlése__ elemet, és erősítse meg a választását.

## <a name="next-steps"></a>Következő lépések

Ebből a dokumentumból megtanulta, hogyan használhatja a Apache Kafka producer és fogyasztói API-t a Kafka on HDInsight. Az alábbiak további információt biztosítanak a Kafka használatával kapcsolatban:

* [A Kafka REST proxy használata](rest-proxy.md)
* [Apache Kafka naplók elemzése](apache-kafka-log-analytics-operations-management.md)
