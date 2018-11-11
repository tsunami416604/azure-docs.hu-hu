---
title: Az Azure Event Hubs-beli Apache kafka Akka adatfolyamok használata |} A Microsoft Docs
description: Event Hub Akka Streamek csatlakozik egy Kafka engedélyezve van.
services: event-hubs
documentationcenter: ''
author: basilhariri
manager: timlt
editor: ''
ms.assetid: ''
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.custom: mvc
ms.date: 08/06/2018
ms.author: bahariri
ms.openlocfilehash: 973470a6964e04b84b814ecf8732b2b981ced44c
ms.sourcegitcommit: ba4570d778187a975645a45920d1d631139ac36e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/08/2018
ms.locfileid: "51278956"
---
# <a name="using-akka-streams-with-event-hubs-for-apache-kafka"></a>Az Event Hubs-beli Apache kafka Akka adatfolyamok használata
Ez az oktatóanyag bemutatja, hogyan Akka Streamek csatlakozni a Kafka-kompatibilis az event hubs a protokollt használó ügyfelek módosítása, vagy saját fürtök futtatása nélkül. Támogatja az Azure Event hubs szolgáltatás a Kafka [Apache Kafka 1.0-s verzióját.](https://kafka.apache.org/10/documentation.html)

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:
> [!div class="checklist"]
> * Event Hubs-névtér létrehozása
> * A példa-projekt klónozása
> * Akka Streamek előállító Futtatás 
> * Futtatási Akka Streamek fogyasztói

> [!NOTE]
> Ez a minta elérhető a [GitHubon](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/akka/java).

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzéséhez, ellenőrizze, hogy a következő előfeltételek vonatkoznak:

* Olvassa át az [Apache Kafkához készült Event Hubsot](event-hubs-for-kafka-ecosystem-overview.md) ismertető cikket. 
* Azure-előfizetés. Ha még nincs előfizetése, hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio), mielőtt hozzákezd.
* [Java fejlesztői készlet (JDK 1.8-as +)](https://aka.ms/azure-jdks)
    * Ubuntu rendszeren futtassa az `apt-get install default-jdk` parancsot a JDK telepítéséhez.
    * Ügyeljen arra, hogy a JAVA_HOME környezeti változó arra a mappára mutasson, ahová a JDK telepítve lett.
* [Töltse le](http://maven.apache.org/download.cgi) és [telepítése](http://maven.apache.org/install.html) a Maven bináris archívum
    * Ubuntu rendszeren futtathatja az `apt-get install maven` parancsot a Maven telepítéséhez.
* [Git](https://www.git-scm.com/downloads)
    * Ubuntu rendszeren futtathatja a `sudo apt-get install git` parancsot a Git telepítéséhez.

## <a name="create-an-event-hubs-namespace"></a>Event Hubs-névtér létrehozása

Event Hubs-névtér elküldeni vagy fogadni a minden Event Hubs szolgáltatás szükséges. Lásd: [Kafka létrehozása az Event Hubs engedélyezve](event-hubs-create-kafka-enabled.md) Bevezetés az Event Hubs Kafka végpont kapcsolatos információkat. Ellenőrizze, hogy az Event Hubs kapcsolati karakterláncot a későbbi használatra.

## <a name="clone-the-example-project"></a>A példa-projekt klónozása

Most, hogy a Kafka-kompatibilis az Event Hubs kapcsolati karakterláncot, az Azure Event Hubs, Kafka-tárház klónozása, és keresse meg a `akka` almappát:

```shell
git clone https://github.com/Azure/azure-event-hubs-for-kafka.git
cd azure-event-hubs-for-kafka/tutorials/akka/java
```

## <a name="run-akka-streams-producer"></a>Akka Streamek előállító Futtatás

A megadott Akka Streamek előállítói példáját, üzenetek küldése az Event Hubs szolgáltatás.

### <a name="provide-an-event-hubs-kafka-endpoint"></a>Adja meg az Event Hubs Kafka-végpont

#### <a name="producer-applicationconf"></a>Gyártó application.conf

Frissítés a `bootstrap.servers` és `sasl.jaas.config` lévő értékeknek `producer/src/main/resources/application.conf` irányítani az előállítói, a megfelelő hitelesítési az Event Hubs Kafka-végpontra.

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

Az előállítói futtatása a parancssorból, készítése a fájlt, majd futtatni Maven belül (vagy készítése a mavennel, majd futtatása Java környezetben ad hozzá a szükséges Kafka JAR(s) az osztályútvonal JAR):

```shell
mvn clean package
mvn exec:java -Dexec.mainClass="AkkaTestProducer"
```

Az előállítói kezdődik, a Kafka-kompatibilis eseményközpontot, a témakör az események küldése `test`, és kinyomtatja az stdout és eseményeket.

## <a name="run-akka-streams-consumer"></a>Futtatási Akka Streamek fogyasztói

A megadott fogyasztói példához használja, üzenet fogadása a Kafka-kompatibilis az event hubs.

### <a name="provide-an-event-hubs-kafka-endpoint"></a>Adja meg az Event Hubs Kafka-végpont

#### <a name="consumer-applicationconf"></a>Fogyasztói application.conf

Frissítés a `bootstrap.servers` és `sasl.jaas.config` lévő értékeknek `consumer/src/main/resources/application.conf` való közvetlen a felhasználó a megfelelő hitelesítési az Event Hubs Kafka-végpontra.

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

### <a name="run-consumer-from-the-command-line"></a>Fogyasztói futtatása a parancssorból

A fogyasztó futtatása a parancssorból, készítése a fájlt, majd futtatni Maven belül (vagy készítése a mavennel, majd futtatása Java környezetben ad hozzá a szükséges Kafka JAR(s) az osztályútvonal JAR):

```shell
mvn clean package
mvn exec:java -Dexec.mainClass="AkkaTestConsumer"
```

Ha a Kafka-kompatibilis eseményközpont események (például, ha a gyártó is fut), majd a fogyasztó elkezdi a fogadott események témakörből `test`. 

Tekintse meg a [Akka Streamek Kafka útmutató](https://doc.akka.io/docs/akka-stream-kafka/current/home.html) részletes Akka Streamek kapcsolatos információkat.

## <a name="next-steps"></a>További lépések
Ebben az oktatóanyagban megtudhatta, hogyan Akka Streamek csatlakozni a Kafka-kompatibilis az event hubs a protokollt használó ügyfelek módosítása, vagy saját fürtök futtatása nélkül. Támogatja az Azure Event hubs szolgáltatás a Kafka [Apache Kafka 1.0-s verziója.](https://kafka.apache.org/10/documentation.html). Ez az oktatóanyag során is tette a következő műveleteket: 

> [!div class="checklist"]
> * Event Hubs-névtér létrehozása
> * A példa-projekt klónozása
> * Akka Streamek előállító Futtatás 
> * Futtatási Akka Streamek fogyasztói

További információ az Event Hubs és az Event Hubs a Kafka, a következő témakörben talál:  

- [Ismerkedés az Event Hubs szolgáltatással](event-hubs-what-is-event-hubs.md)
- [Az Event Hubs-beli Apache kafka](event-hubs-for-kafka-ecosystem-overview.md)
- [A Kafka létrehozása az Event Hubs engedélyezve](event-hubs-create-kafka-enabled.md)
- [Stream az Event Hubsba, a Kafka-alkalmazásokból](event-hubs-quickstart-kafka-enabled-event-hubs.md)
- [Egy Kafka-közvetítő Kafka-kompatibilis eseményközpont tükrözéséhez.](event-hubs-kafka-mirror-maker-tutorial.md)
- [Csatlakozás az Apache Spark egy Kafka-kompatibilis event hubs](event-hubs-kafka-spark-tutorial.md)
- [Az Apache Flink csatlakozni egy Kafka-kompatibilis eseményközpont](event-hubs-kafka-flink-tutorial.md)
- [Egy Kafka-kompatibilis eseményközpont Kafka csatlakozás integrálása](event-hubs-kafka-connect-tutorial.md)
- [Minták megtekintése a githubon](https://github.com/Azure/azure-event-hubs-for-kafka)
