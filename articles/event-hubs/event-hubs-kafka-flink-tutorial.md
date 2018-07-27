---
title: Apache Flink használata az Azure Event Hubs-beli Apache kafka |} A Microsoft Docs
description: Egy kafka csatlakozó Apache Flink eseményközpont engedélyezve
services: event-hubs
documentationcenter: ''
author: basilhariri
manager: timlt
ms.service: event-hubs
ms.topic: article
ms.custom: mvc
ms.date: 06/06/2018
ms.author: bahariri
ms.openlocfilehash: ce1665c3cfd58d0d5aa8e253b5db317505b1959e
ms.sourcegitcommit: 068fc623c1bb7fb767919c4882280cad8bc33e3a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/27/2018
ms.locfileid: "39284577"
---
# <a name="use-apache-flink-with-azure-event-hubs-for-apache-kafka"></a>Az Azure Event Hubs-beli Apache kafka Apache Flink használata

Apache Kafka használatának legfontosabb előnyei egyike az ökoszisztéma-keretrendszereket, hogy kapcsolódni. A Kafka a rugalmasságot, a kafka engedélyezve az Event Hubs ötvözi a méretezhetőség, a konzisztencia és a támogatása az Azure-rendszerbe.

Az oktatóanyag bemutatja, hogyan lehet Apache Flink csatlakozni Kafka-kompatibilis az event hubs nélkül módosítja a protokollt használó ügyfelek vagy a saját fürtök. Támogatja az Azure Event Hubs [Apache Kafka 1.0-s verzióját.](https://kafka.apache.org/10/documentation.html)

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzéséhez, ellenőrizze, hogy a következő előfeltételek vonatkoznak:

* Azure-előfizetés. Ha még nincs előfizetése, hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio), mielőtt hozzákezd.
* [Java fejlesztői készlet (JDK) 1.7+](http://www.oracle.com/technetwork/java/javase/downloads/index.html)
    * Ubuntu rendszeren futtassa az `apt-get install default-jdk` parancsot a JDK telepítéséhez.
    * Ügyeljen arra, hogy a JAVA_HOME környezeti változó arra a mappára mutasson, ahová a JDK telepítve lett.
* [Töltse le](http://maven.apache.org/download.cgi) és [telepítése](http://maven.apache.org/install.html) a Maven bináris archívum
    * Ubuntu rendszeren futtathatja az `apt-get install maven` parancsot a Maven telepítéséhez.
* [Git](https://www.git-scm.com/downloads)
    * Ubuntu rendszeren futtathatja a `sudo apt-get install git` parancsot a Git telepítéséhez.

## <a name="create-an-event-hubs-namespace"></a>Event Hubs-névtér létrehozása

Event Hubs-névtér elküldeni vagy fogadni a minden Event Hubs szolgáltatás szükséges. Lásd: [Kafka létrehozása az Event Hubs engedélyezve](event-hubs-create-kafka-enabled.md) Bevezetés az Event Hubs Kafka végpont kapcsolatos információkat. Ellenőrizze, hogy az Event Hubs kapcsolati karakterláncot a későbbi használatra.

## <a name="clone-the-example-project"></a>A példa-projekt klónozása

Most, hogy a Kafka-kompatibilis az Event Hubs kapcsolati karakterláncot, az Azure Event Hubs-tárház klónozásához, és keresse meg a `flink` almappát:

```shell
git clone https://github.com/Azure/azure-event-hubs.git
cd azure-event-hubs/samples/kafka/flink
```

## <a name="flink-producer"></a>Flink producer

A megadott Flink előállítói példáját, üzenetek küldése az Event Hubs szolgáltatás.

### <a name="provide-an-event-hubs-kafka-endpoint"></a>Adja meg az Event Hubs Kafka-végpont

#### <a name="producerconfig"></a>Producer.config

Frissítés a `bootstrap.servers` és `sasl.jaas.config` lévő értékeknek `producer/src/main/resources/producer.config` irányítani az előállítói, a megfelelő hitelesítési az Event Hubs Kafka-végpontra.

```xml
bootstrap.servers={YOUR.EVENTHUBS.FQDN}:9093
client.id=FlinkExampleProducer
sasl.mechanism=PLAIN
security.protocol=SASL_SSL
sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required \
   username="$ConnectionString" \
   password="{YOUR.EVENTHUBS.CONNECTION.STRING}";
```

### <a name="run-producer-from-the-command-line"></a>Gyártó futtatása a parancssorból

Az előállítói futtatása a parancssorból, készítése a fájlt, majd futtatni Maven belül (vagy készítése a mavennel, majd futtatása Java környezetben ad hozzá a szükséges Kafka JAR(s) az osztályútvonal JAR):

```shell
mvn clean package
mvn exec:java -Dexec.mainClass="FlinkTestProducer"
```

Az előállítói most elkezdi az Eseményközpont eseményeket küldeni a Kafka engedélyezve a témakör `test` és nyomtatására az stdout és eseményeket.

## <a name="flink-consumer"></a>Flink fogyasztói

A megadott fogyasztói példához használja érkező üzenetek fogadása a Kafka Event hubs szolgáltatás engedélyezve van.

### <a name="provide-an-event-hubs-kafka-endpoint"></a>Adja meg az Event Hubs Kafka-végpont

#### <a name="consumerconfig"></a>Consumer.config

Frissítés a `bootstrap.servers` és `sasl.jaas.config` lévő értékeknek `consumer/src/main/resources/consumer.config` való közvetlen a felhasználó a megfelelő hitelesítési az Event Hubs Kafka-végpontra.

```xml
bootstrap.servers={YOUR.EVENTHUBS.FQDN}:9093
group.id=FlinkExampleConsumer
sasl.mechanism=PLAIN
security.protocol=SASL_SSL
sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required \
   username="$ConnectionString" \
   password="{YOUR.EVENTHUBS.CONNECTION.STRING}";
```

### <a name="run-consumer-from-the-command-line"></a>Fogyasztói futtatása a parancssorból

A fogyasztó futtatása a parancssorból, készítése a fájlt, majd futtatni Maven belül (vagy készítése a mavennel, majd futtatása Java környezetben ad hozzá a szükséges Kafka JAR(s) az osztályútvonal JAR):

```shell
mvn clean package
mvn exec:java -Dexec.mainClass="FlinkTestConsumer"
```

Ha a Kafka-kompatibilis eseményközpont események (például, ha a gyártó is fut), majd a fogyasztó már megkezdi események fogadása a témakör `test`.

Tekintse meg [Flink a Kafka összekötő útmutató](https://ci.apache.org/projects/flink/flink-docs-stable/dev/connectors/kafka.html) részletesebb Flink Kafka való csatlakozással kapcsolatos információkat.

## <a name="next-steps"></a>További lépések

* [Ismerkedés az Event Hubs szolgáltatással](event-hubs-what-is-event-hubs.md)
* [További információ az Event Hubs, Kafka for](event-hubs-for-kafka-ecosystem-overview.md)
* A [MirrorMaker](https://cwiki.apache.org/confluence/pages/viewpage.action?pageId=27846330) használatával [eseményeket streamelhet a helyszíni Kafkából a felhőben található Kafka-kompatibilis Event Hubsba.](event-hubs-kafka-mirror-maker-tutorial.md)
* Ismerje meg, hogyan streamelése Kafka az engedélyezett az Event Hubs használatával [Kafka natív alkalmazások](event-hubs-quickstart-kafka-enabled-event-hubs.md) vagy [Akka Streamek](event-hubs-kafka-akka-streams-tutorial.md).
