---
title: Az Apache Flink for Apache Kafka használata – Azure Event Hubs | Microsoft dokumentumok
description: Ez a cikk az Apache Flink Azure-eseményközponthoz való csatlakoztatásáról nyújt tájékoztatást.
services: event-hubs
documentationcenter: ''
author: ShubhaVijayasarathy
manager: timlt
ms.service: event-hubs
ms.topic: article
ms.custom: seodec18
ms.date: 12/06/2018
ms.author: shvija
ms.openlocfilehash: 6ab542e1328bb986f53d31e2eca75007cf1e0c75
ms.sourcegitcommit: b0ff9c9d760a0426fd1226b909ab943e13ade330
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/01/2020
ms.locfileid: "80521801"
---
# <a name="use-apache-flink-with-azure-event-hubs-for-apache-kafka"></a>Az Apache Flink használata az Apache Kafkához készült Event Hubs szolgáltatással
Ez az oktatóanyag bemutatja, hogyan csatlakoztathatja az Apache Flinket egy eseményközponthoz a protokollügyfelek módosítása vagy a saját fürtök futtatása nélkül. Az Azure Event Hubs támogatja az [Apache Kafka 1.0-s verzióját.](https://kafka.apache.org/10/documentation.html).

Az Apache Kafka használatának egyik legfontosabb előnye a keretrendszerek ökoszisztémája, amelyhez csatlakozni tud. Az Event Hubs egyesíti a Kafka rugalmasságát az Azure-ökoszisztéma méretezhetőségével, konzisztenciájával és támogatásával.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:
> [!div class="checklist"]
> * Event Hubs-névtér létrehozása
> * A példaprojekt klónozása
> * Fuss Flink gyártó 
> * A Flink-fogyasztó futtatása

> [!NOTE]
> Ez a minta elérhető a [GitHubon](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/flink).

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag befejezéséhez győződjön meg arról, hogy a következő előfeltételekkel rendelkezik:

* Olvassa át az [Apache Kafkához készült Event Hubsot](event-hubs-for-kafka-ecosystem-overview.md) ismertető cikket. 
* Azure-előfizetés. Ha még nincs előfizetése, hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio), mielőtt hozzákezd.
* [Java Fejlesztő készlet (JDK) 1.7+](https://aka.ms/azure-jdks)
    * Ubuntu rendszeren futtassa az `apt-get install default-jdk` parancsot a JDK telepítéséhez.
    * Ügyeljen arra, hogy a JAVA_HOME környezeti változó arra a mappára mutasson, ahová a JDK telepítve lett.
* [Maven](https://maven.apache.org/download.cgi) bináris archívum [letöltése](https://maven.apache.org/install.html) és telepítése
    * Ubuntu rendszeren futtathatja az `apt-get install maven` parancsot a Maven telepítéséhez.
* [Git](https://www.git-scm.com/downloads)
    * Ubuntu rendszeren futtathatja a `sudo apt-get install git` parancsot a Git telepítéséhez.

## <a name="create-an-event-hubs-namespace"></a>Event Hubs-névtér létrehozása

Az Event Hubs névtér bármely Event Hubs szolgáltatásból történő küldéséhez vagy fogadásához szükséges. A névtér és az eseményközpont létrehozásáról az [Eseményközpont létrehozása](event-hubs-create.md) című témakörben talál útmutatást. Győződjön meg arról, hogy másolja az Event Hubs kapcsolati karakterláncot későbbi használatra.

## <a name="clone-the-example-project"></a>A példaprojekt klónozása

Most, hogy rendelkezik az Event Hubs kapcsolati karakterláncával, klónozza a Kafka-tárház Azure-eseményközpontokat, és keresse meg az `flink` almappát:

```shell
git clone https://github.com/Azure/azure-event-hubs-for-kafka.git
cd azure-event-hubs-for-kafka/tutorials/flink
```

## <a name="run-flink-producer"></a>Fuss Flink gyártó

A megadott Flink produceri példával üzeneteket küldhet az Event Hubs szolgáltatásnak.

### <a name="provide-an-event-hubs-kafka-endpoint"></a>Eseményközpontok Kafka-végpontjának biztosítása

#### <a name="producerconfig"></a>producer.config

Frissítse `bootstrap.servers` a `sasl.jaas.config` és `producer/src/main/resources/producer.config` az értékeket, hogy a gyártó az Event Hubs Kafka-végponthoz irányítsa a megfelelő hitelesítéssel.

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

A gyártó parancssorból történő futtatásához hozza létre a JAR-t, majd fusson a Maven-en belülről (vagy hozza létre a JAR-t Maven használatával, majd futtassa Java-ban a szükséges Kafka JAR(ok) hozzáadásával az osztályhoz):

```shell
mvn clean package
mvn exec:java -Dexec.mainClass="FlinkTestProducer"
```

A gyártó most megkezdi az események küldését az eseményközpontba a témában, `test` és kinyomtatja az eseményeket az stdout-ra.

## <a name="run-flink-consumer"></a>A Flink-fogyasztó futtatása

A megadott fogyasztói példában fogadhat üzeneteket az eseményközpontból. 

### <a name="provide-an-event-hubs-kafka-endpoint"></a>Eseményközpontok Kafka-végpontjának biztosítása

#### <a name="consumerconfig"></a>consumer.config

Frissítse `bootstrap.servers` a `sasl.jaas.config` és `consumer/src/main/resources/consumer.config` az értékeket, hogy az elődet az Event Hubs Kafka-végponthoz irányítsa a megfelelő hitelesítéssel.

```xml
bootstrap.servers={YOUR.EVENTHUBS.FQDN}:9093
group.id=FlinkExampleConsumer
sasl.mechanism=PLAIN
security.protocol=SASL_SSL
sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required \
   username="$ConnectionString" \
   password="{YOUR.EVENTHUBS.CONNECTION.STRING}";
```

### <a name="run-consumer-from-the-command-line"></a>Fogyasztó futtatása a parancssorból

A fogyasztó parancssorból történő futtatásához hozza létre a JAR-t, majd fusson a Maven-en belülről (vagy hozza létre a JAR-t maven használatával, majd futtassa Java-ban a szükséges Kafka JAR(ok) hozzáadásával a classpath-hoz):

```shell
mvn clean package
mvn exec:java -Dexec.mainClass="FlinkTestConsumer"
```

Ha az eseményközpont eseményeket (például a gyártó is fut), akkor a `test`fogyasztó most megkezdi az események fogadását a témából.

Nézze meg [Flink a Kafka Connector Guide](https://ci.apache.org/projects/flink/flink-docs-stable/dev/connectors/kafka.html) részletesebb információt összekötő Flink a Kafka.

## <a name="next-steps"></a>További lépések
Ebben az oktatóanyagban a megtanulta, hogyan csatlakoztathatja az Apache Flinket az Event Hubs-hoz a protokollügyfelek módosítása vagy a saját fürtök futtatása nélkül. Az oktatóanyag részeként a következő lépéseket hajtotta végre: 

> [!div class="checklist"]
> * Event Hubs-névtér létrehozása
> * A példaprojekt klónozása
> * Fuss Flink gyártó 
> * A Flink-fogyasztó futtatása

Az Event Hubsszal és a Kafkához készült Event Hubsszal kapcsolatos további információkért tekintse át a következő témaköröket:  

- [Ismerkedés az Event Hubs szolgáltatással](event-hubs-what-is-event-hubs.md)
- [Az Apache Kafkához készült Event Hubs](event-hubs-for-kafka-ecosystem-overview.md)
- [Eseményközpont létrehozása](event-hubs-create.md)
- [Streamelés az Event Hubsba a Kafka-alkalmazásokból](event-hubs-quickstart-kafka-enabled-event-hubs.md)
- [Kafka-közvetítő tükrözése egy eseményközpontba](event-hubs-kafka-mirror-maker-tutorial.md)
- [Apache Spark csatlakoztatása egy eseményközponthoz](event-hubs-kafka-spark-tutorial.md)
- [A Kafka Connect integrálása egy eseményközponttal](event-hubs-kafka-connect-tutorial.md)
- [Akka-adatfolyamok csatlakoztatása eseményközponthoz](event-hubs-kafka-akka-streams-tutorial.md)
- [További példák a GitHubon](https://github.com/Azure/azure-event-hubs-for-kafka)
