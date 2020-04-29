---
title: Az Apache flink használata a Apache Kafkahoz – Azure Event Hubs | Microsoft Docs
description: Ez a cikk azt ismerteti, hogyan csatlakoztatható az Apache flink egy Azure Event hub szolgáltatáshoz
services: event-hubs
documentationcenter: ''
author: ShubhaVijayasarathy
manager: timlt
ms.service: event-hubs
ms.topic: how-to
ms.date: 04/02/2020
ms.author: shvija
ms.openlocfilehash: 2e5a2924cdc00c1cc057d71c40645085df4bae6a
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "80632812"
---
# <a name="use-apache-flink-with-azure-event-hubs-for-apache-kafka"></a>Az Apache Flink használata az Apache Kafkához készült Event Hubs szolgáltatással
Ebből az oktatóanyagból megtudhatja, hogyan csatlakoztathatja az Apache flink egy Event hubhoz a protokoll-ügyfelek módosítása vagy a saját fürtök futtatása nélkül. Az Azure Event Hubs támogatja az 1,0-es [Apache Kafka-verziót.](https://kafka.apache.org/10/documentation.html)

A Apache Kafka használatának egyik legfőbb előnye, hogy a keretrendszerek ökoszisztémája, amelyhez csatlakozni tud. Event Hubs ötvözi a Kafka rugalmasságát az Azure-ökoszisztéma skálázhatóságával, konzisztenciájával és támogatásával.

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
* [Java Development Kit (JDK) 1.7 +](https://aka.ms/azure-jdks)
    * Ubuntu rendszeren futtassa az `apt-get install default-jdk` parancsot a JDK telepítéséhez.
    * Ügyeljen arra, hogy a JAVA_HOME környezeti változó arra a mappára mutasson, ahová a JDK telepítve lett.
* [Maven](https://maven.apache.org/download.cgi) bináris archívum [letöltése](https://maven.apache.org/install.html) és telepítése
    * Ubuntu rendszeren futtathatja az `apt-get install maven` parancsot a Maven telepítéséhez.
* [Git](https://www.git-scm.com/downloads)
    * Ubuntu rendszeren futtathatja a `sudo apt-get install git` parancsot a Git telepítéséhez.

## <a name="create-an-event-hubs-namespace"></a>Event Hubs-névtér létrehozása

Bármely Event Hubs szolgáltatásból történő küldéshez vagy fogadáshoz Event Hubs névtér szükséges. A névtér és az Event hub létrehozásával kapcsolatos utasításokért tekintse meg az [Event hub létrehozása](event-hubs-create.md) című témakört. Ügyeljen arra, hogy későbbi használatra másolja a Event Hubs-kapcsolódási karakterláncot.

## <a name="clone-the-example-project"></a>A példaprojekt klónozása

Most, hogy elvégezte a Event Hubs a kapcsolódási karakterláncot, klónozott Azure-Event Hubs a Kafka `flink` -tárházhoz, és navigáljon az almappába:

```shell
git clone https://github.com/Azure/azure-event-hubs-for-kafka.git
cd azure-event-hubs-for-kafka/tutorials/flink
```

## <a name="run-flink-producer"></a>Flink-gyártó futtatása

A megadott flink-előállító példa használatával üzeneteket küldhet a Event Hubs szolgáltatásnak.

### <a name="provide-an-event-hubs-kafka-endpoint"></a>Event Hubs Kafka-végpont megadása

#### <a name="producerconfig"></a>producer. config

A `bootstrap.servers` és `sasl.jaas.config` a értékének `producer/src/main/resources/producer.config` frissítésével irányítsa a gyártót a Event Hubs Kafka-végpontra a megfelelő hitelesítéssel.

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

A gyártó most megkezdi az események küldését az Event hub `test` -ba a témakörben, és kinyomtatja az eseményeket az stdout-ba.

## <a name="run-flink-consumer"></a>Flink-fogyasztó futtatása

A megadott fogyasztói példa használatával fogadhat üzeneteket az Event hub-ból. 

### <a name="provide-an-event-hubs-kafka-endpoint"></a>Event Hubs Kafka-végpont megadása

#### <a name="consumerconfig"></a>Consumer. config

A `bootstrap.servers` és `sasl.jaas.config` a értékének `consumer/src/main/resources/consumer.config` frissítésével irányítsa a fogyasztót a Event Hubs Kafka-végpontra a megfelelő hitelesítéssel.

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

Ha az Event hub eseményeivel rendelkezik (például ha a gyártó is fut), akkor a fogyasztó most elkezdi az események fogadását a témakörből `test`.

Tekintse [meg a flink Kafka-összekötő útmutatóját](https://ci.apache.org/projects/flink/flink-docs-stable/dev/connectors/kafka.html) , amely részletesebb információkat biztosít a flink és a Kafka összekapcsolásáról.

## <a name="next-steps"></a>További lépések
Ha többet szeretne megtudni a Kafka-Event Hubsről, tekintse meg a következő cikkeket:  

- [Kafka-közvetítő tükrözése egy eseményközpontba](event-hubs-kafka-mirror-maker-tutorial.md)
- [Apache Spark csatlakoztatása egy eseményközponthoz](event-hubs-kafka-spark-tutorial.md)
- [A Kafka-kapcsolat integrálása az Event hubhoz](event-hubs-kafka-connect-tutorial.md)
- [További példák a GitHubon](https://github.com/Azure/azure-event-hubs-for-kafka)
- [Az Akka Streams csatlakoztatása eseményközponthoz](event-hubs-kafka-akka-streams-tutorial.md)
- [Apache Kafka fejlesztői útmutató az Azure-hoz Event Hubs](apache-kafka-developer-guide.md)