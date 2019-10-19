---
title: A Apache Kafka-Azure Event Hubs-hoz készült beadási streamek használata | Microsoft Docs
description: Ez a cikk azt ismerteti, hogyan csatlakoztathatók a Apache Kafka engedélyezett Azure Event hub szolgáltatáshoz.
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
ms.openlocfilehash: ba81ce88bcdf039d020dcd945e45a11cf603c114
ms.sourcegitcommit: ae461c90cada1231f496bf442ee0c4dcdb6396bc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/17/2019
ms.locfileid: "72555758"
---
# <a name="using-akka-streams-with-event-hubs-for-apache-kafka"></a>A Event Hubs for Apache Kafka
Ebből az oktatóanyagból megtudhatja, hogyan csatlakoztathatók a-beli-adatfolyamok a Kafka-kompatibilis Event hubokhoz a protokoll-ügyfelek módosítása vagy a saját fürtök futtatása nélkül. A Kafka Azure-Event Hubs [Apache Kafka 1,0-es verzióval támogatott.](https://kafka.apache.org/10/documentation.html)

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:
> [!div class="checklist"]
> * Event Hubs-névtér létrehozása
> * A példaprojekt klónozása
> * A kiállítók futtatása 
> * A bevezetési streamek fogyasztójának futtatása

> [!NOTE]
> Ez a minta elérhető a [GitHubon](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/akka/java).

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzéséhez győződjön meg arról, hogy rendelkezik a következő előfeltételekkel:

* Olvassa át az [Apache Kafkához készült Event Hubsot](event-hubs-for-kafka-ecosystem-overview.md) ismertető cikket. 
* Azure-előfizetés. Ha még nincs előfizetése, hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio), mielőtt hozzákezd.
* [Java fejlesztői készlet (JDK) 1.8+](https://aka.ms/azure-jdks)
    * Ubuntu rendszeren futtassa az `apt-get install default-jdk` parancsot a JDK telepítéséhez.
    * Ügyeljen arra, hogy a JAVA_HOME környezeti változó arra a mappára mutasson, ahová a JDK telepítve lett.
* Maven bináris Archívum [letöltése](https://maven.apache.org/download.cgi) és [telepítése](https://maven.apache.org/install.html)
    * Ubuntu rendszeren futtathatja az `apt-get install maven` parancsot a Maven telepítéséhez.
* [Git](https://www.git-scm.com/downloads)
    * Ubuntu rendszeren futtathatja a `sudo apt-get install git` parancsot a Git telepítéséhez.

## <a name="create-an-event-hubs-namespace"></a>Event Hubs-névtér létrehozása

Bármely Event Hubs szolgáltatásból történő küldéshez vagy fogadáshoz Event Hubs névtér szükséges. A Event Hubs Kafka-végpont beszerzésével kapcsolatos információkért tekintse meg a [Kafka-kompatibilis Event Hubs létrehozása](event-hubs-create-kafka-enabled.md) című témakört. Ügyeljen arra, hogy későbbi használatra másolja a Event Hubs-kapcsolódási karakterláncot.

## <a name="clone-the-example-project"></a>A példaprojekt klónozása

Most, hogy már van egy Kafka-kompatibilis Event Hubs kapcsolódási karakterlánca, klónozott az Azure Event Hubs a Kafka-tárházhoz, és navigáljon a `akka` almappába:

```shell
git clone https://github.com/Azure/azure-event-hubs-for-kafka.git
cd azure-event-hubs-for-kafka/tutorials/akka/java
```

## <a name="run-akka-streams-producer"></a>A kiállítók futtatása

A megadott beadási streamek gyártói példájának használatával üzeneteket küldhet a Event Hubs szolgáltatásnak.

### <a name="provide-an-event-hubs-kafka-endpoint"></a>Event Hubs Kafka-végpont megadása

#### <a name="producer-applicationconf"></a>Producer Application. conf

Frissítse a `producer/src/main/resources/application.conf` `bootstrap.servers` és `sasl.jaas.config` értékeit, hogy a gyártót az Event Hubs Kafka-végpontra irányítsa a megfelelő hitelesítéssel.

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

### <a name="run-producer-from-the-command-line"></a>A gyártó futtatása a parancssorból

Ha a parancssorból szeretné futtatni a gyártót, a JAR-t, majd a Mavenből futtatva futtassa a jar-t, majd futtassa a-t a Maven használatával, majd futtassa a Java-ban a szükséges Kafka-JAR (ok) hozzáadásával a osztályútvonal):

```shell
mvn clean package
mvn exec:java -Dexec.mainClass="AkkaTestProducer"
```

A gyártó elkezdi elküldeni az eseményeket a Kafka-kompatibilis Event hubhoz a `test` témakörben, és kinyomtatja az eseményeket az stdout-ra.

## <a name="run-akka-streams-consumer"></a>A bevezetési streamek fogyasztójának futtatása

A megadott fogyasztói példa használatával fogadhat üzeneteket a Kafka-kompatibilis Event hubokból.

### <a name="provide-an-event-hubs-kafka-endpoint"></a>Event Hubs Kafka-végpont megadása

#### <a name="consumer-applicationconf"></a>Consumer Application. conf

Frissítse a `bootstrap.servers` és `sasl.jaas.config` értékeket a `consumer/src/main/resources/application.conf`, hogy a fogyasztót a Event Hubs Kafka-végpontra irányítsa a megfelelő hitelesítéssel.

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

### <a name="run-consumer-from-the-command-line"></a>A felhasználó futtatása a parancssorból

Ha a parancssorból szeretné futtatni a fogyasztót, adja ki a JAR-t, majd futtassa a Mavenből (vagy a Maven használatával a JAR-t használva, majd futtassa a Java-ban a szükséges Kafka-JAR (ok) hozzáadásával a osztályútvonal):

```shell
mvn clean package
mvn exec:java -Dexec.mainClass="AkkaTestConsumer"
```

Ha a Kafka-kompatibilis Event hub eseményeket tartalmaz (például ha a gyártója is fut), akkor a fogyasztó megkezdi az események fogadását a következő témakörből: `test`. 

Tekintse meg a bevezetési [Streams Kafka-útmutatót](https://doc.akka.io/docs/akka-stream-kafka/current/home.html) , ahol részletesebb információkat találhat a bevezető streamekről.

## <a name="next-steps"></a>Következő lépések
Ebben az oktatóanyagban megtanulta, hogyan csatlakoztathatók a-beli-adatfolyamok a Kafka-kompatibilis Event hubokhoz a protokoll-ügyfelek módosítása vagy a saját fürtök futtatása nélkül. A Kafka Azure-Event Hubs [Apache Kafka 1,0-es verziójának támogatása.](https://kafka.apache.org/10/documentation.html) Az oktatóanyag részeként a következő műveleteket végezte el: 

> [!div class="checklist"]
> * Event Hubs-névtér létrehozása
> * A példaprojekt klónozása
> * A kiállítók futtatása 
> * A bevezetési streamek fogyasztójának futtatása

Az Event Hubsszal és a Kafkához készült Event Hubsszal kapcsolatos további információkért tekintse át a következő témaköröket:  

- [Ismerkedés az Event Hubs szolgáltatással](event-hubs-what-is-event-hubs.md)
- [Az Apache Kafkához készült Event Hubs](event-hubs-for-kafka-ecosystem-overview.md)
- [Kafka-kompatibilis eseményközpont létrehozása](event-hubs-create-kafka-enabled.md)
- [Streamelés az Event Hubsba a Kafka-alkalmazásokból](event-hubs-quickstart-kafka-enabled-event-hubs.md)
- [Kafka-közvetítő tükrözése egy Kafka-kompatibilis eseményközpontba](event-hubs-kafka-mirror-maker-tutorial.md)
- [Az Apache Spark csatlakoztatása egy Kafka-kompatibilis eseményközponthoz](event-hubs-kafka-spark-tutorial.md)
- [Az Apache Flink csatlakoztatása egy Kafka-kompatibilis eseményközponthoz](event-hubs-kafka-flink-tutorial.md)
- [A Kafka Connect integrálása egy Kafka-kompatibilis eseményközponttal](event-hubs-kafka-connect-tutorial.md)
- [További példák a GitHubon](https://github.com/Azure/azure-event-hubs-for-kafka)
