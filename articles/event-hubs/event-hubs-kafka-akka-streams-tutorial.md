---
title: Az Akka Streams használata az Apache Kafka szolgáltatáshoz – Azure Event Hubs| Microsoft dokumentumok
description: Ez a cikk az Akka-adatfolyamok Azure-eseményközponthoz való csatlakoztatásáról nyújt tájékoztatást.
services: event-hubs
documentationcenter: ''
author: ShubhaVijayasarathy
manager: timlt
editor: ''
ms.assetid: ''
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.custom: seodec18
ms.date: 12/06/2018
ms.author: shvija
ms.openlocfilehash: c53fcc7b4b99e77237ba7ea9219aef4182f2af61
ms.sourcegitcommit: b0ff9c9d760a0426fd1226b909ab943e13ade330
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/01/2020
ms.locfileid: "80521826"
---
# <a name="using-akka-streams-with-event-hubs-for-apache-kafka"></a>Az Akka Streams használata az Apache Kafkához készült Event Hubs szolgáltatással
Ez az oktatóanyag bemutatja, hogyan csatlakoztathatja az Akka Streams-t egy eseményközponthoz a protokollügyfelek módosítása vagy a saját fürtök futtatása nélkül. A Kafka Azure Event Hubs támogatja az [Apache Kafka 1.0-s verzióját.](https://kafka.apache.org/10/documentation.html)

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:
> [!div class="checklist"]
> * Event Hubs-névtér létrehozása
> * A példaprojekt klónozása
> * Az Akka Streams gyártójának futtatása 
> * Az Akka Streams fogyasztói futtatása

> [!NOTE]
> Ez a minta elérhető a [GitHubon](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/akka/java).

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag befejezéséhez győződjön meg arról, hogy a következő előfeltételekkel rendelkezik:

* Olvassa át az [Apache Kafkához készült Event Hubsot](event-hubs-for-kafka-ecosystem-overview.md) ismertető cikket. 
* Azure-előfizetés. Ha még nincs előfizetése, hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio), mielőtt hozzákezd.
* [Java fejlesztői készlet (JDK) 1.8+](https://aka.ms/azure-jdks)
    * Ubuntu rendszeren futtassa az `apt-get install default-jdk` parancsot a JDK telepítéséhez.
    * Ügyeljen arra, hogy a JAVA_HOME környezeti változó arra a mappára mutasson, ahová a JDK telepítve lett.
* [Maven](https://maven.apache.org/download.cgi) bináris archívum [letöltése](https://maven.apache.org/install.html) és telepítése
    * Ubuntu rendszeren futtathatja az `apt-get install maven` parancsot a Maven telepítéséhez.
* [Git](https://www.git-scm.com/downloads)
    * Ubuntu rendszeren futtathatja a `sudo apt-get install git` parancsot a Git telepítéséhez.

## <a name="create-an-event-hubs-namespace"></a>Event Hubs-névtér létrehozása

Az Event Hubs névtér bármely Event Hubs szolgáltatásból történő küldéséhez vagy fogadásához szükséges. Részletes információkért [lásd: Eseményközpont létrehozása.](event-hubs-create.md) Győződjön meg arról, hogy másolja az Event Hubs kapcsolati karakterláncot későbbi használatra.

## <a name="clone-the-example-project"></a>A példaprojekt klónozása

Most, hogy rendelkezik egy Event Hubs kapcsolati karakterlánccal, klónozza a Kafka-tárház Azure-eseményközpontokat, és keresse meg az `akka` almappát:

```shell
git clone https://github.com/Azure/azure-event-hubs-for-kafka.git
cd azure-event-hubs-for-kafka/tutorials/akka/java
```

## <a name="run-akka-streams-producer"></a>Az Akka Streams gyártójának futtatása

A megadott Akka Streams produceri példával üzeneteket küldhet az Event Hubs szolgáltatásnak.

### <a name="provide-an-event-hubs-kafka-endpoint"></a>Eseményközpontok Kafka-végpontjának biztosítása

#### <a name="producer-applicationconf"></a>Gyártói alkalmazás.conf

Frissítse `bootstrap.servers` a `sasl.jaas.config` és `producer/src/main/resources/application.conf` az értékeket, hogy a gyártó az Event Hubs Kafka-végponthoz irányítsa a megfelelő hitelesítéssel.

```xml
akka.kafka.producer {
    #Akka Kafka producer properties can be defined here


    # Properties defined by org.apache.kafka.clients.producer.ProducerConfig
    # can be defined in this configuration section.
    kafka-clients {
        bootstrap.servers="{YOUR.EVENTHUBS.FQDN}:9093"
        sasl.mechanism=PLAIN
        security.protocol=SASL_SSL
        sasl.jaas.config="org.apache.kafka.common.security.plain.PlainLoginModule required username=\"$ConnectionString\" password=\"{YOUR.EVENTHUBS.CONNECTION.STRING}\";"
    }
}
```

### <a name="run-producer-from-the-command-line"></a>Gyártó futtatása a parancssorból

A gyártó parancssorból történő futtatásához hozza létre a JAR-t, majd fusson a Maven-en belülről (vagy hozza létre a JAR-t Maven használatával, majd futtassa Java-ban a szükséges Kafka JAR(ok) hozzáadásával az osztályhoz):

```shell
mvn clean package
mvn exec:java -Dexec.mainClass="AkkaTestProducer"
```

A gyártó megkezdi az események küldését az eseményközpontba a témában, `test`és kinyomtatja az eseményeket stdout-ra.

## <a name="run-akka-streams-consumer"></a>Az Akka Streams fogyasztói futtatása

A megadott fogyasztói példában fogadhat üzeneteket az eseményközpontból.

### <a name="provide-an-event-hubs-kafka-endpoint"></a>Eseményközpontok Kafka-végpontjának biztosítása

#### <a name="consumer-applicationconf"></a>Fogyasztói alkalmazás.conf

Frissítse `bootstrap.servers` a `sasl.jaas.config` és `consumer/src/main/resources/application.conf` az értékeket, hogy az elődet az Event Hubs Kafka-végponthoz irányítsa a megfelelő hitelesítéssel.

```xml
akka.kafka.consumer {
    #Akka Kafka consumer properties defined here
    wakeup-timeout=60s

    # Properties defined by org.apache.kafka.clients.consumer.ConsumerConfig
    # defined in this configuration section.
    kafka-clients {
       request.timeout.ms=60000
       group.id=akka-example-consumer

       bootstrap.servers="{YOUR.EVENTHUBS.FQDN}:9093"
       sasl.mechanism=PLAIN
       security.protocol=SASL_SSL
       sasl.jaas.config="org.apache.kafka.common.security.plain.PlainLoginModule required username=\"$ConnectionString\" password=\"{YOUR.EVENTHUBS.CONNECTION.STRING}\";"
    }
}
```

### <a name="run-consumer-from-the-command-line"></a>Fogyasztó futtatása a parancssorból

A fogyasztó parancssorból történő futtatásához hozza létre a JAR-t, majd fusson a Maven-en belülről (vagy hozza létre a JAR-t maven használatával, majd futtassa Java-ban a szükséges Kafka JAR(ok) hozzáadásával a classpath-hoz):

```shell
mvn clean package
mvn exec:java -Dexec.mainClass="AkkaTestConsumer"
```

Ha az eseményközpont eseményeket (például, ha a gyártó is fut), `test`akkor a fogyasztó megkezdi az események fogadását a témából. 

Nézze meg az [Akka Streams Kafka Guide](https://doc.akka.io/docs/akka-stream-kafka/current/home.html) részletesebb információt Akka Patakok.

## <a name="next-steps"></a>További lépések
Ebben az oktatóanyagban megtanulta, hogyan csatlakoztathatja az Akka streameket az eseményközponthoz a protokollügyfelek módosítása vagy a saját fürtök futtatása nélkül. A Kafka Azure Event Hubs szolgáltatása támogatja az [Apache Kafka 1.0-s verzióját.](https://kafka.apache.org/10/documentation.html). Az oktatóanyag részeként a következő műveleteket végezte el: 

> [!div class="checklist"]
> * Event Hubs-névtér létrehozása
> * A példaprojekt klónozása
> * Az Akka Streams gyártójának futtatása 
> * Az Akka Streams fogyasztói futtatása

Az Event Hubsszal és a Kafkához készült Event Hubsszal kapcsolatos további információkért tekintse át a következő témaköröket:  

- [Ismerkedés az Event Hubs szolgáltatással](event-hubs-what-is-event-hubs.md)
- [Az Apache Kafkához készült Event Hubs](event-hubs-for-kafka-ecosystem-overview.md)
- [Eseményközpont létrehozása](event-hubs-create.md)
- [Streamelés az Event Hubsba a Kafka-alkalmazásokból](event-hubs-quickstart-kafka-enabled-event-hubs.md)
- [Kafka-közvetítő tükrözése egy eseményközpontba](event-hubs-kafka-mirror-maker-tutorial.md)
- [Apache Spark csatlakoztatása egy eseményközponthoz](event-hubs-kafka-spark-tutorial.md)
- [Apache Flink csatlakoztatása egy eseményközponthoz](event-hubs-kafka-flink-tutorial.md)
- [A Kafka Connect integrálása egy eseményközponttal](event-hubs-kafka-connect-tutorial.md)
- [További példák a GitHubon](https://github.com/Azure/azure-event-hubs-for-kafka)
