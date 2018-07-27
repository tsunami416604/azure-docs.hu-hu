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
ms.date: 06/06/2018
ms.author: bahariri
ms.openlocfilehash: f6671e05c53b8be2e06eb969adc8a4d5736aff37
ms.sourcegitcommit: 068fc623c1bb7fb767919c4882280cad8bc33e3a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/27/2018
ms.locfileid: "39284447"
---
# <a name="using-akka-streams-with-event-hubs-for-apache-kafka"></a>Az Event Hubs-beli Apache kafka Akka adatfolyamok használata

Apache Kafka használatának legfontosabb előnyei egyike az ökoszisztéma-keretrendszereket, hogy kapcsolódni. A Kafka-kompatibilis az Event Hubs a méretezhetőséget, a konzisztencia és a támogatás az Azure-rendszerbe, a rugalmasságot, a kafka ötvözi.

Ez az oktatóanyag bemutatja, hogyan Akka Streamek csatlakozni a Kafka-kompatibilis az event hubs a protokollt használó ügyfelek módosítása, vagy saját fürtök futtatása nélkül. Támogatja az Azure Event hubs szolgáltatás a Kafka [Apache Kafka 1.0-s verzióját.](https://kafka.apache.org/10/documentation.html)

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzéséhez, ellenőrizze, hogy a következő előfeltételek vonatkoznak:

* Azure-előfizetés. Ha még nincs előfizetése, hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio), mielőtt hozzákezd.
* [Java fejlesztői készlet (JDK 1.8-as +)](http://www.oracle.com/technetwork/java/javase/downloads/index.html)
    * Ubuntu rendszeren futtassa az `apt-get install default-jdk` parancsot a JDK telepítéséhez.
    * Ügyeljen arra, hogy a JAVA_HOME környezeti változó arra a mappára mutasson, ahová a JDK telepítve lett.
* [Töltse le](http://maven.apache.org/download.cgi) és [telepítése](http://maven.apache.org/install.html) a Maven bináris archívum
    * Ubuntu rendszeren futtathatja az `apt-get install maven` parancsot a Maven telepítéséhez.
* [Git](https://www.git-scm.com/downloads)
    * Ubuntu rendszeren futtathatja a `sudo apt-get install git` parancsot a Git telepítéséhez.

## <a name="create-an-event-hubs-namespace"></a>Event Hubs-névtér létrehozása

Event Hubs-névtér elküldeni vagy fogadni a minden Event Hubs szolgáltatás szükséges. Lásd: [Kafka létrehozása az Event Hubs engedélyezve](event-hubs-create-kafka-enabled.md) Bevezetés az Event Hubs Kafka végpont kapcsolatos információkat. Ellenőrizze, hogy az Event Hubs kapcsolati karakterláncot a későbbi használatra.

## <a name="clone-the-example-project"></a>A példa-projekt klónozása

Most, hogy a Kafka-kompatibilis az Event Hubs kapcsolati karakterláncot, az Azure Event Hubs-tárház klónozásához, és keresse meg a `akka` almappát:

```shell
git clone https://github.com/Azure/azure-event-hubs.git
cd azure-event-hubs/samples/kafka/akka
```

## <a name="akka-streams-producer"></a>Akka Streamek producer

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

## <a name="akka-streams-consumer"></a>Akka Streamek fogyasztói

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

* [Ismerkedés az Event Hubs szolgáltatással](event-hubs-what-is-event-hubs.md)
* [További információ az Event Hubs, Kafka for](event-hubs-for-kafka-ecosystem-overview.md)
* A [MirrorMaker](https://cwiki.apache.org/confluence/pages/viewpage.action?pageId=27846330) használatával [eseményeket streamelhet a helyszíni Kafkából a felhőben található Kafka-kompatibilis Event Hubsba.](event-hubs-kafka-mirror-maker-tutorial.md)
* Ismerje meg, hogyan streamelése Kafka az engedélyezett az Event Hubs használatával [Kafka natív alkalmazások](event-hubs-quickstart-kafka-enabled-event-hubs.md) vagy [Apache Flink](event-hubs-kafka-flink-tutorial.md)
