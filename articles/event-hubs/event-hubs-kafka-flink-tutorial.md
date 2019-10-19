---
title: Az Apache flink használata a Apache Kafkahoz – Azure Event Hubs | Microsoft Docs
description: Ez a cikk azt ismerteti, hogyan csatlakoztatható az Apache flink egy Apache Kafka engedélyezett Azure Event hub-hoz
services: event-hubs
documentationcenter: ''
author: ShubhaVijayasarathy
manager: timlt
ms.service: event-hubs
ms.topic: article
ms.custom: seodec18
ms.date: 12/06/2018
ms.author: shvija
ms.openlocfilehash: 881546a97b01bef993cc24c6b868ec97ddf5ac36
ms.sourcegitcommit: ae461c90cada1231f496bf442ee0c4dcdb6396bc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/17/2019
ms.locfileid: "72555723"
---
# <a name="use-apache-flink-with-azure-event-hubs-for-apache-kafka"></a>Az Apache flink használata az Azure Event Hubs for Apache Kafka
Ebből az oktatóanyagból megtudhatja, hogyan csatlakoztatható az Apache flink a Kafka-kompatibilis Event hubokhoz a protokoll-ügyfelek módosítása vagy a saját fürtök futtatása nélkül. Az Azure Event Hubs támogatja az 1,0-es [Apache Kafka-verziót.](https://kafka.apache.org/10/documentation.html)

A Apache Kafka használatának egyik legfőbb előnye, hogy a keretrendszerek ökoszisztémája, amelyhez csatlakozni tud. A Kafka-kompatibilis Event Hubs ötvözi a Kafka rugalmasságát az Azure-ökoszisztéma skálázhatóságával, konzisztenciájával és támogatásával.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:
> [!div class="checklist"]
> * Event Hubs-névtér létrehozása
> * A példaprojekt klónozása
> * Flink-gyártó futtatása 
> * Flink-fogyasztó futtatása

> [!NOTE]
> Ez a minta elérhető a [GitHubon](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/flink).

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzéséhez győződjön meg arról, hogy rendelkezik a következő előfeltételekkel:

* Olvassa át az [Apache Kafkához készült Event Hubsot](event-hubs-for-kafka-ecosystem-overview.md) ismertető cikket. 
* Azure-előfizetés. Ha még nincs előfizetése, hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio), mielőtt hozzákezd.
* [Java fejlesztői készlet (JDK) 1.7+](https://aka.ms/azure-jdks)
    * Ubuntu rendszeren futtassa az `apt-get install default-jdk` parancsot a JDK telepítéséhez.
    * Ügyeljen arra, hogy a JAVA_HOME környezeti változó arra a mappára mutasson, ahová a JDK telepítve lett.
* Maven bináris Archívum [letöltése](https://maven.apache.org/download.cgi) és [telepítése](https://maven.apache.org/install.html)
    * Ubuntu rendszeren futtathatja az `apt-get install maven` parancsot a Maven telepítéséhez.
* [Git](https://www.git-scm.com/downloads)
    * Ubuntu rendszeren futtathatja a `sudo apt-get install git` parancsot a Git telepítéséhez.

## <a name="create-an-event-hubs-namespace"></a>Event Hubs-névtér létrehozása

Bármely Event Hubs szolgáltatásból történő küldéshez vagy fogadáshoz Event Hubs névtér szükséges. A Event Hubs Kafka-végpont beszerzésével kapcsolatos információkért tekintse meg a [Kafka-kompatibilis Event Hubs létrehozása](event-hubs-create-kafka-enabled.md) című témakört. Ügyeljen arra, hogy későbbi használatra másolja a Event Hubs-kapcsolódási karakterláncot.

## <a name="clone-the-example-project"></a>A példaprojekt klónozása

Most, hogy már van egy Kafka-kompatibilis Event Hubs kapcsolódási karakterlánca, klónozott az Azure Event Hubs a Kafka-tárházhoz, és navigáljon a `flink` almappába:

```shell
git clone https://github.com/Azure/azure-event-hubs-for-kafka.git
cd azure-event-hubs-for-kafka/tutorials/flink
```

## <a name="run-flink-producer"></a>Flink-gyártó futtatása

A megadott flink-előállító példa használatával üzeneteket küldhet a Event Hubs szolgáltatásnak.

### <a name="provide-an-event-hubs-kafka-endpoint"></a>Event Hubs Kafka-végpont megadása

#### <a name="producerconfig"></a>producer. config

Frissítse a `producer/src/main/resources/producer.config` `bootstrap.servers` és `sasl.jaas.config` értékeit, hogy a gyártót az Event Hubs Kafka-végpontra irányítsa a megfelelő hitelesítéssel.

```xml
bootstrap.servers={YOUR.EVENTHUBS.FQDN}:9093
client.id=FlinkExampleProducer
sasl.mechanism=PLAIN
security.protocol=SASL_SSL
sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required \
   username="$ConnectionString" \
   password="{YOUR.EVENTHUBS.CONNECTION.STRING}";
```

### <a name="run-producer-from-the-command-line"></a>A gyártó futtatása a parancssorból

Ha a parancssorból szeretné futtatni a gyártót, a JAR-t, majd a Mavenből futtatva futtassa a jar-t, majd futtassa a-t a Maven használatával, majd futtassa a Java-ban a szükséges Kafka-JAR (ok) hozzáadásával a osztályútvonal):

```shell
mvn clean package
mvn exec:java -Dexec.mainClass="FlinkTestProducer"
```

A gyártó most megkezdi az események küldését a Kafka-kompatibilis Event hubhoz a `test` témakörben, és az eseményeket az stdout-ra nyomtassa.

## <a name="run-flink-consumer"></a>Flink-fogyasztó futtatása

A megadott fogyasztói példa használatával üzeneteket fogadhat a Kafka által engedélyezett Event Hubsról.

### <a name="provide-an-event-hubs-kafka-endpoint"></a>Event Hubs Kafka-végpont megadása

#### <a name="consumerconfig"></a>Consumer. config

Frissítse a `bootstrap.servers` és `sasl.jaas.config` értékeket a `consumer/src/main/resources/consumer.config`, hogy a fogyasztót a Event Hubs Kafka-végpontra irányítsa a megfelelő hitelesítéssel.

```xml
bootstrap.servers={YOUR.EVENTHUBS.FQDN}:9093
group.id=FlinkExampleConsumer
sasl.mechanism=PLAIN
security.protocol=SASL_SSL
sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required \
   username="$ConnectionString" \
   password="{YOUR.EVENTHUBS.CONNECTION.STRING}";
```

### <a name="run-consumer-from-the-command-line"></a>A felhasználó futtatása a parancssorból

Ha a parancssorból szeretné futtatni a fogyasztót, adja ki a JAR-t, majd futtassa a Mavenből (vagy a Maven használatával a JAR-t használva, majd futtassa a Java-ban a szükséges Kafka-JAR (ok) hozzáadásával a osztályútvonal):

```shell
mvn clean package
mvn exec:java -Dexec.mainClass="FlinkTestConsumer"
```

Ha a Kafka-kompatibilis Event hub eseményekkel rendelkezik (például ha a gyártó is fut), akkor a fogyasztó most elkezdi fogadni az eseményeket a következő témakörből: `test`.

Tekintse [meg a flink Kafka-összekötő útmutatóját](https://ci.apache.org/projects/flink/flink-docs-stable/dev/connectors/kafka.html) , amely részletesebb információkat biztosít a flink és a Kafka összekapcsolásáról.

## <a name="next-steps"></a>Következő lépések
Ebben az oktatóanyagban megtanulta, hogyan csatlakoztatható az Apache flink a Kafka-kompatibilis Event hubokhoz a protokoll-ügyfelek módosítása vagy a saját fürtök futtatása nélkül. Az oktatóanyag részeként a következő lépéseket hajtotta végre: 

> [!div class="checklist"]
> * Event Hubs-névtér létrehozása
> * A példaprojekt klónozása
> * Flink-gyártó futtatása 
> * Flink-fogyasztó futtatása

Az Event Hubsszal és a Kafkához készült Event Hubsszal kapcsolatos további információkért tekintse át a következő témaköröket:  

- [Ismerkedés az Event Hubs szolgáltatással](event-hubs-what-is-event-hubs.md)
- [Az Apache Kafkához készült Event Hubs](event-hubs-for-kafka-ecosystem-overview.md)
- [Kafka-kompatibilis eseményközpont létrehozása](event-hubs-create-kafka-enabled.md)
- [Streamelés az Event Hubsba a Kafka-alkalmazásokból](event-hubs-quickstart-kafka-enabled-event-hubs.md)
- [Kafka-közvetítő tükrözése egy Kafka-kompatibilis eseményközpontba](event-hubs-kafka-mirror-maker-tutorial.md)
- [Az Apache Spark csatlakoztatása egy Kafka-kompatibilis eseményközponthoz](event-hubs-kafka-spark-tutorial.md)
- [A Kafka Connect integrálása egy Kafka-kompatibilis eseményközponttal](event-hubs-kafka-connect-tutorial.md)
- [Az Akka Streams csatlakoztatása egy Kafka-kompatibilis eseményközponthoz](event-hubs-kafka-akka-streams-tutorial.md)
- [További példák a GitHubon](https://github.com/Azure/azure-event-hubs-for-kafka)
