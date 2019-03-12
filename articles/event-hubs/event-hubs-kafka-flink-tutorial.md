---
title: Az Apache Flink használata Apache kafka platformmal – Azure Event Hubs |} A Microsoft Docs
description: Ez a cikk ismerteti, hogyan lehet Apache Flink csatlakozni az Apache Kafka információkat engedélyezve van az Azure event hub
services: event-hubs
documentationcenter: ''
author: basilhariri
manager: timlt
ms.service: event-hubs
ms.topic: article
ms.custom: seodec18
ms.date: 12/06/2018
ms.author: bahariri
ms.openlocfilehash: dc4a982dde62f62eb8f2d91a61fd70ba79fa13d5
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/07/2019
ms.locfileid: "57539977"
---
# <a name="use-apache-flink-with-azure-event-hubs-for-apache-kafka"></a>Az Azure Event Hubs-beli Apache kafka Apache Flink használata
Az oktatóanyag bemutatja, hogyan lehet Apache Flink csatlakozni Kafka-kompatibilis az event hubs nélkül módosítja a protokollt használó ügyfelek vagy a saját fürtök. Támogatja az Azure Event Hubs [Apache Kafka 1.0-s verziója.](https://kafka.apache.org/10/documentation.html).

Apache Kafka használatának legfontosabb előnyei egyike az ökoszisztéma-keretrendszereket, hogy kapcsolódni. A Kafka a rugalmasságot, a kafka engedélyezve az Event Hubs ötvözi a méretezhetőség, a konzisztencia és a támogatása az Azure-rendszerbe.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:
> [!div class="checklist"]
> * Event Hubs-névtér létrehozása
> * A példaprojekt klónozása
> * Flink előállító Futtatás 
> * Futtatási Flink fogyasztói

> [!NOTE]
> Ez a minta elérhető a [GitHubon](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/flink).

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzéséhez, ellenőrizze, hogy a következő előfeltételek vonatkoznak:

* Olvassa át az [Apache Kafkához készült Event Hubsot](event-hubs-for-kafka-ecosystem-overview.md) ismertető cikket. 
* Azure-előfizetés. Ha még nincs előfizetése, hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio), mielőtt hozzákezd.
* [Java fejlesztői készlet (JDK) 1.7+](https://aka.ms/azure-jdks)
    * Ubuntu rendszeren futtassa az `apt-get install default-jdk` parancsot a JDK telepítéséhez.
    * Ügyeljen arra, hogy a JAVA_HOME környezeti változó arra a mappára mutasson, ahová a JDK telepítve lett.
* [Töltse le](https://maven.apache.org/download.cgi) és [telepítése](https://maven.apache.org/install.html) a Maven bináris archívum
    * Ubuntu rendszeren futtathatja az `apt-get install maven` parancsot a Maven telepítéséhez.
* [Git](https://www.git-scm.com/downloads)
    * Ubuntu rendszeren futtathatja a `sudo apt-get install git` parancsot a Git telepítéséhez.

## <a name="create-an-event-hubs-namespace"></a>Event Hubs-névtér létrehozása

Event Hubs-névtér elküldeni vagy fogadni a minden Event Hubs szolgáltatás szükséges. Lásd: [Kafka létrehozása az Event Hubs engedélyezve](event-hubs-create-kafka-enabled.md) Bevezetés az Event Hubs Kafka végpont kapcsolatos információkat. Ellenőrizze, hogy az Event Hubs kapcsolati karakterláncot a későbbi használatra.

## <a name="clone-the-example-project"></a>A példaprojekt klónozása

Most, hogy a Kafka-kompatibilis az Event Hubs kapcsolati karakterláncot, az Azure Event Hubs, Kafka-tárház klónozása, és keresse meg a `flink` almappát:

```shell
git clone https://github.com/Azure/azure-event-hubs-for-kafka.git
cd azure-event-hubs-for-kafka/tutorials/flink
```

## <a name="run-flink-producer"></a>Flink előállító Futtatás

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

## <a name="run-flink-consumer"></a>Futtatási Flink fogyasztói

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
Ebben az oktatóanyagban a megismert Apache Flink Kafka-kompatibilis az event hubs kapcsolódás nélkül módosítja a protokollt használó ügyfelek vagy a saját fürtök. Ez az oktatóanyag részeként végezte a következő lépéseket: 

> [!div class="checklist"]
> * Event Hubs-névtér létrehozása
> * A példaprojekt klónozása
> * Flink előállító Futtatás 
> * Futtatási Flink fogyasztói

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
