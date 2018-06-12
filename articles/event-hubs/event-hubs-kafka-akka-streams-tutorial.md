---
title: Használja az Azure Event Hubs Akka adatfolyamok Kafka ökoszisztéma |} Microsoft Docs
description: Az Event Hubs Akka adatfolyamok csatlakozik egy Kafka engedélyezve van.
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
ms.openlocfilehash: 9db27340a2210ea0be0564b15241952477e592ba
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/11/2018
ms.locfileid: "35303717"
---
# <a name="using-akka-streams-with-event-hubs-for-kafka-ecosystem"></a>Az Event Hubs Akka adatfolyamok Kafka ökoszisztéma használ

A fő előnyei a Apache Kafka egyik keretrendszerekre, hogy kapcsolódni tud az ökoszisztéma. Az Event Hubs Kafka-kompatibilis Kafka rugalmasan ötvözi a méretezhetőséget, konzisztencia- és az Azure-ökoszisztéma-támogatása.

Ez az oktatóanyag bemutatja, hogyan Akka adatfolyamok csatlakozni Kafka engedélyezve van az event hubs módosítása a protokoll-ügyfelek és a saját fürtöket futtató nélkül. A Kafka ökoszisztéma támogatja az Azure Event Hubs [Apache Kafka 1.0-s verziója.](https://kafka.apache.org/10/documentation.html)

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzéséhez, győződjön meg arról, hogy a következő előfeltételek teljesülését:

* Azure-előfizetés. Ha még nincs előfizetése, hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio), mielőtt hozzákezd.
* [Java fejlesztői készlet (JDK) 1.8 +](http://www.oracle.com/technetwork/java/javase/downloads/index.html)
    * Ubuntu rendszeren futtassa az `apt-get install default-jdk` parancsot a JDK telepítéséhez.
    * Ügyeljen arra, hogy a JAVA_HOME környezeti változó arra a mappára mutasson, ahová a JDK telepítve lett.
* [Töltse le](http://maven.apache.org/download.cgi) és [telepítése](http://maven.apache.org/install.html) bináris Maven-archívum létrehozása
    * Ubuntu rendszeren futtathatja az `apt-get install maven` parancsot a Maven telepítéséhez.
* [Git](https://www.git-scm.com/downloads)
    * Ubuntu rendszeren futtathatja a `sudo apt-get install git` parancsot a Git telepítéséhez.

## <a name="create-an-event-hubs-namespace"></a>Az Event Hubs-névtér létrehozása

Az Event Hubs névtér elküldéséhez, vagy a bármely Event Hubs szolgáltatás szükséges. Lásd: [Kafka létrehozása az Event Hubs engedélyezett](event-hubs-create-kafka-enabled.md) további információ az Event Hubs Kafka végpont beolvasásakor. Ügyeljen arra, hogy másolja az Event Hubs kapcsolati karakterláncot későbbi használatra.

## <a name="clone-the-example-project"></a>Klónozza a példaprojekt

Most, hogy rendelkezik a kapcsolati karakterláncot az Event Hubs Kafka engedélyezve van, az Azure Event Hubs tárház klónozása, és keresse meg a `akka` almappa:

```shell
git clone https://github.com/Azure/azure-event-hubs.git
cd azure-event-hubs/samples/kafka/akka
```

## <a name="akka-streams-producer"></a>Akka adatfolyamok készítő

A Akka adatfolyamokat a gyártó például használva üzenetek küldése az Event Hubs szolgáltatás.

### <a name="provide-an-event-hubs-kafka-endpoint"></a>Adja meg az Event Hubs Kafka végpont

#### <a name="producer-applicationconf"></a>Gyártó application.conf

Frissítés a `bootstrap.servers` és `sasl.jaas.config` értékei `producer/src/main/resources/application.conf` át tudja irányítani a megfelelő hitelesítési Event Hubs Kafka végpontjának a termelő.

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

### <a name="run-producer-from-the-command-line"></a>Gyártó futtassa a parancssorból

A gyártó futtatásához a parancssorból, létre a JAR és majd futtatni Maven belül (, vagy a JAR Maven, majd futtassa a Java ad hozzá a szükséges Kafka JAR(s) a classpath használatával):

```shell
mvn clean package
mvn exec:java -Dexec.mainClass="AkkaTestProducer"
```

A gyártó kezdődik események küldése az Kafka-kompatibilis eseményközpontba témakör következő `test`, és kiírja az stdout és eseményeket.

## <a name="akka-streams-consumer"></a>Akka adatfolyamok fogyasztói

A megadott felhasználói példánál üzeneteket fogadjon a Kafka engedélyezve van az event hubs.

### <a name="provide-an-event-hubs-kafka-endpoint"></a>Adja meg az Event Hubs Kafka végpont

#### <a name="consumer-applicationconf"></a>Fogyasztó application.conf

Frissítés a `bootstrap.servers` és `sasl.jaas.config` értékei `consumer/src/main/resources/application.conf` át tudja irányítani a fogyasztó számára a megfelelő hitelesítési Event Hubs Kafka végpontjának.

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

### <a name="run-consumer-from-the-command-line"></a>Fogyasztó futtassa a parancssorból

A fogyasztó futtatásához a parancssorból, létre a JAR és majd futtatni Maven belül (, vagy a JAR Maven, majd futtassa a Java ad hozzá a szükséges Kafka JAR(s) a classpath használatával):

```shell
mvn clean package
mvn exec:java -Dexec.mainClass="AkkaTestConsumer"
```

Ha a Kafka-kompatibilis eseményközpont események (például, ha a gyártó is fut), majd a fogyasztó kezdődik események fogadása témakör `test`. 

Tekintse meg a [Akka adatfolyamok Kafka útmutató](https://doc.akka.io/docs/akka-stream-kafka/current/home.html) részletes Akka adatfolyamok kapcsolatos információkat.

## <a name="next-steps"></a>További lépések

* [Ismerkedés az Event Hubs szolgáltatással](event-hubs-what-is-event-hubs.md)
* [Ismerkedés a Kafkához készült Event Hubs ökoszisztémájával](event-hubs-for-kafka-ecosystem-overview.md)
* Használjon [MirrorMaker](https://cwiki.apache.org/confluence/pages/viewpage.action?pageId=27846330) való [adatfolyam események a helyszíni Kafka Kafka engedélyezve van az Event Hubs felhő.](event-hubs-kafka-mirror-maker-tutorial.md)
* Megtudhatja, hogyan adatfolyamként történő Kafka engedélyezve van az Event Hubs használatával [natív Kafka alkalmazások](event-hubs-quickstart-kafka-enabled-event-hubs.md) vagy [Apache Flink](event-hubs-kafka-flink-tutorial.md)
