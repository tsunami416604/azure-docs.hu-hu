---
title: Apache Flink használni az Azure Event Hubs a Kafka ökoszisztéma |} Microsoft Docs
description: Egy Kafka való kapcsolódás során Apache Flink engedélyezve van az event hubs
services: event-hubs
documentationcenter: ''
author: basilhariri
manager: timlt
ms.service: event-hubs
ms.topic: article
ms.custom: mvc
ms.date: 06/06/2018
ms.author: bahariri
ms.openlocfilehash: cb7ef0e9b6a612e3f4116cb626903770e4035368
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/11/2018
ms.locfileid: "35303718"
---
# <a name="apache-flink-with-event-hubs-for-the-kafka-ecosystem"></a>Az Event Hubs a Kafka ökoszisztéma az Apache Flink

A fő előnyei a Apache Kafka egyik keretrendszerekre, hogy kapcsolódni tud az ökoszisztéma. Kafka Kafka rugalmasan engedélyezve az Event Hubs hatékonyan ötvözi a méretezhetőséget, a konzisztencia és a támogatás az Azure ökoszisztéma.

Ez az oktatóanyag bemutatja, hogyan Apache Flink csatlakozni Kafka engedélyezve van az event hubs módosítása a protokoll-ügyfelek és a saját fürtöket futtató nélkül. Kafka ökoszisztéma támogatja az Azure Event Hubs [Apache Kafka 1.0-s verziója.](https://kafka.apache.org/10/documentation.html)

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzéséhez, győződjön meg arról, hogy a következő előfeltételek teljesülését:

* Azure-előfizetés. Ha még nincs előfizetése, hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio), mielőtt hozzákezd.
* [Java fejlesztői készlet (JDK) 1.7+](http://www.oracle.com/technetwork/java/javase/downloads/index.html)
    * Ubuntu rendszeren futtassa az `apt-get install default-jdk` parancsot a JDK telepítéséhez.
    * Ügyeljen arra, hogy a JAVA_HOME környezeti változó arra a mappára mutasson, ahová a JDK telepítve lett.
* [Töltse le](http://maven.apache.org/download.cgi) és [telepítése](http://maven.apache.org/install.html) bináris Maven-archívum létrehozása
    * Ubuntu rendszeren futtathatja az `apt-get install maven` parancsot a Maven telepítéséhez.
* [Git](https://www.git-scm.com/downloads)
    * Ubuntu rendszeren futtathatja a `sudo apt-get install git` parancsot a Git telepítéséhez.

## <a name="create-an-event-hubs-namespace"></a>Az Event Hubs-névtér létrehozása

Az Event Hubs névtér elküldéséhez, vagy a bármely Event Hubs szolgáltatás szükséges. Lásd: [Kafka létrehozása az Event Hubs engedélyezett](event-hubs-create-kafka-enabled.md) további információ az Event Hubs Kafka végpont beolvasásakor. Ügyeljen arra, hogy másolja az Event Hubs kapcsolati karakterláncot későbbi használatra.

## <a name="clone-the-example-project"></a>Klónozza a példaprojekt

Most, hogy rendelkezik a kapcsolati karakterláncot az Event Hubs Kafka engedélyezve van, az Azure Event Hubs tárház klónozása, és keresse meg a `flink` almappa:

```shell
git clone https://github.com/Azure/azure-event-hubs.git
cd azure-event-hubs/samples/kafka/flink
```

## <a name="flink-producer"></a>Flink készítő

A Flink a gyártó például használva üzenetek küldése az Event Hubs szolgáltatás.

### <a name="provide-an-event-hubs-kafka-endpoint"></a>Adja meg az Event Hubs Kafka végpont

#### <a name="producerconfig"></a>Producer.config

Frissítés a `bootstrap.servers` és `sasl.jaas.config` értékei `producer/src/main/resources/producer.config` át tudja irányítani a megfelelő hitelesítési Event Hubs Kafka végpontjának a termelő.

```xml
bootstrap.servers={YOUR.EVENTHUBS.FQDN}:9093
client.id=FlinkExampleProducer
sasl.mechanism=PLAIN
security.protocol=SASL_SSL
sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required \
   username="$ConnectionString" \
   password="{YOUR.EVENTHUBS.CONNECTION.STRING}";
```

### <a name="run-producer-from-the-command-line"></a>Gyártó futtassa a parancssorból

A gyártó futtatásához a parancssorból, létre a JAR és majd futtatni Maven belül (, vagy a JAR Maven, majd futtassa a Java ad hozzá a szükséges Kafka JAR(s) a classpath használatával):

```shell
mvn clean package
mvn exec:java -Dexec.mainClass="FlinkTestProducer"
```

A gyártó most elkezdi az Event Hubs események küldése az Kafka érhető el a témakör `test` és a nyomtatási az stdout és eseményeket.

## <a name="flink-consumer"></a>Flink fogyasztói

A megadott felhasználói példa az üzeneteket fogadni a Kafka Event Hubs engedélyezve van.

### <a name="provide-an-event-hubs-kafka-endpoint"></a>Adja meg az Event Hubs Kafka végpont

#### <a name="consumerconfig"></a>Consumer.config

Frissítés a `bootstrap.servers` és `sasl.jaas.config` értékei `consumer/src/main/resources/consumer.config` át tudja irányítani a fogyasztó számára a megfelelő hitelesítési Event Hubs Kafka végpontjának.

```xml
bootstrap.servers={YOUR.EVENTHUBS.FQDN}:9093
group.id=FlinkExampleConsumer
sasl.mechanism=PLAIN
security.protocol=SASL_SSL
sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required \
   username="$ConnectionString" \
   password="{YOUR.EVENTHUBS.CONNECTION.STRING}";
```

### <a name="run-consumer-from-the-command-line"></a>Fogyasztó futtassa a parancssorból

A fogyasztó futtatásához a parancssorból, létre a JAR és majd futtatni Maven belül (, vagy a JAR Maven, majd futtassa a Java ad hozzá a szükséges Kafka JAR(s) a classpath használatával):

```shell
mvn clean package
mvn exec:java -Dexec.mainClass="FlinkTestConsumer"
```

Ha a Kafka-kompatibilis eseményközpont események (például, ha a gyártó is fut), majd a fogyasztó megkezdi események fogadása a témakör `test`.

Tekintse meg [Flink tartozó Kafka összekötő útmutató](https://ci.apache.org/projects/flink/flink-docs-stable/dev/connectors/kafka.html) Flink csatlakozás Kafka részletes.

## <a name="next-steps"></a>További lépések

* [Ismerkedés az Event Hubs szolgáltatással](event-hubs-what-is-event-hubs.md)
* [Ismerkedés a Kafkához készült Event Hubs ökoszisztémájával](event-hubs-for-kafka-ecosystem-overview.md)
* Használjon [MirrorMaker](https://cwiki.apache.org/confluence/pages/viewpage.action?pageId=27846330) való [adatfolyam események a helyszíni Kafka Kafka engedélyezve van az Event Hubs felhő.](event-hubs-kafka-mirror-maker-tutorial.md)
* Megtudhatja, hogyan adatfolyamként történő Kafka engedélyezve van az Event Hubs használatával [natív Kafka alkalmazások](event-hubs-quickstart-kafka-enabled-event-hubs.md) vagy [Akka adatfolyamok](event-hubs-kafka-akka-streams-tutorial.md).
