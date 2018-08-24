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
ms.date: 08/06/2018
ms.author: bahariri
ms.openlocfilehash: b724ddfc1214ac17c2138dc9875896cf3353f0c7
ms.sourcegitcommit: b5ac31eeb7c4f9be584bb0f7d55c5654b74404ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/23/2018
ms.locfileid: "42746626"
---
# <a name="use-apache-flink-with-azure-event-hubs-for-apache-kafka"></a>Az Azure Event Hubs-beli Apache kafka Apache Flink használata
Az oktatóanyag bemutatja, hogyan lehet Apache Flink csatlakozni Kafka-kompatibilis az event hubs nélkül módosítja a protokollt használó ügyfelek vagy a saját fürtök. Támogatja az Azure Event Hubs [Apache Kafka 1.0-s verziója.](https://kafka.apache.org/10/documentation.html).

Apache Kafka használatának legfontosabb előnyei egyike az ökoszisztéma-keretrendszereket, hogy kapcsolódni. A Kafka a rugalmasságot, a kafka engedélyezve az Event Hubs ötvözi a méretezhetőség, a konzisztencia és a támogatása az Azure-rendszerbe.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:
> [!div class="checklist"]
> * Event Hubs-névtér létrehozása
> * A példa-projekt klónozása
> * Flink előállító Futtatás 
> * Futtatási Flink fogyasztói

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzéséhez, ellenőrizze, hogy a következő előfeltételek vonatkoznak:

* Olvassa el a [az Event Hubs-beli Apache kafka](event-hubs-for-kafka-ecosystem-overview.md) cikk. 
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
> * A példa-projekt klónozása
> * Flink előállító Futtatás 
> * Futtatási Flink fogyasztói

Folytassa a következő cikk további információ az Event Hubs-beli Apache kafka:

> [!div class="nextstepaction"]
> [Az Azure Event Hubs Akka adatfolyamok a Kafka használata](event-hubs-kafka-akka-streams-tutorial.md)