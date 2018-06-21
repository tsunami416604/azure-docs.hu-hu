---
title: Streamelés az Azure Event Hubs Kafkához készült ökoszisztémájában | Microsoft Docs
description: Streamelés az Event Hubsba a Kafka-protokoll és a Kafka API-k használatával.
services: event-hubs
documentationcenter: ''
author: basilhariri
manager: timlt
ms.service: event-hubs
ms.devlang: na
ms.topic: quickstart
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/08/2018
ms.author: bahariri
ms.openlocfilehash: 8ef6240d19ce1ac1b891c95ce525a8bd211a2900
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/11/2018
ms.locfileid: "35297223"
---
# <a name="stream-into-event-hubs-for-the-kafka-ecosystem"></a>Streamelés az Event Hubs Kafkához készült ökoszisztémájában

> [!NOTE]
> Ez a minta elérhető a [GitHubon](https://github.com/Azure/azure-event-hubs).

Ez a rövid útmutató bemutatja, hogyan streamelhet a Kafka-kompatibilis Event Hubsba anélkül, hogy módosítaná a protokollügyfeleket vagy saját fürtöket futtatna. Megtudhatja, hogyan érheti el egy egyszerű konfigurációmódosítással az alkalmazásokban, hogy az előállítók és a fogyasztók kommunikáljanak a Kafka-kompatibilis Event Hubsszal. Az Azure Event Hubs Kafkához készült ökoszisztémája az [Apache Kafka 1.0-s verzióját](https://kafka.apache.org/10/documentation.html) támogatja.

## <a name="prerequisites"></a>Előfeltételek

A rövid útmutató elvégzéséhez győződjön meg arról, hogy teljesülnek az alábbi előfeltételek:

* Azure-előfizetés. Ha még nincs előfizetése, hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio), mielőtt hozzákezd.
* [Java fejlesztői készlet (JDK) 1.7+](http://www.oracle.com/technetwork/java/javase/downloads/index.html).
* Egy [letöltött](http://maven.apache.org/download.cgi) és [telepített](http://maven.apache.org/install.html) Maven bináris archívum.
* [Git](https://www.git-scm.com/)
* [Kafka-kompatibilis Event Hubs-névtér](event-hubs-create.md)

## <a name="send-and-receive-messages-with-kafka-in-event-hubs"></a>Üzenetek küldése és fogadása a Kafkával az Event Hubsban

1. Klónozza az [Azure Event Hubs-adattárat](https://github.com/Azure/azure-event-hubs).

2. Nyissa meg a `azure-event-hubs/samples/kafka/quickstart/producer` címet.

3. Az alábbiakban látható módon módosítsa az előállító konfigurációs adatait az `src/main/resources/producer.config` fájlban:

    ```xml
    bootstrap.servers={YOUR.EVENTHUBS.FQDN}:9093
    security.protocol=SASL_SSL
    sasl.mechanism=PLAIN
    sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required username="$ConnectionString" password="{YOUR.EVENTHUBS.CONNECTION.STRING}";
    ```
4. Futtassa az előállító kódját, és streameljen a Kafka-kompatibilis Event Hubsba:
   
    ```shell
    mvn clean package
    mvn exec:java -Dexec.mainClass="TestProducer"                                    
    ```
5. Nyissa meg a `azure-event-hubs/samples/kafka/quickstart/consumer` címet.

6. Az alábbiakban látható módon módosítsa a fogyasztó konfigurációs adatait az `src/main/resources/consumer.config` fájlban:
   
    ```xml
    bootstrap.servers={YOUR.EVENTHUBS.FQDN}:9093
    security.protocol=SASL_SSL
    sasl.mechanism=PLAIN
    sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required username="$ConnectionString" password="{YOUR.EVENTHUBS.CONNECTION.STRING}";
    ```

7. Futtassa a fogyasztó kódját, és végezze el a feldolgozást a Kafka-kompatibilis Event Hubsból a Kafka-ügyfelek használatával:

    ```java
    mvn clean package
    mvn exec:java -Dexec.mainClass="TestConsumer"                                    
    ```

Ha az Event Hubs Kafka-fürtön vannak események, most el kell kezdeniük érkezni a fogyasztóról.

## <a name="next-steps"></a>További lépések

* [Ismerkedés az Event Hubs szolgáltatással](event-hubs-what-is-event-hubs.md)
* [Ismerkedés a Kafkához készült Event Hubs ökoszisztémájával](event-hubs-for-kafka-ecosystem-overview.md)
* A [MirrorMaker](https://cwiki.apache.org/confluence/pages/viewpage.action?pageId=27846330) használatával [eseményeket streamelhet a helyszíni Kafkából a felhőben található Kafka-kompatibilis Event Hubsba.](event-hubs-kafka-mirror-maker-tutorial.md)
* Ismerje meg, hogyan streamelhet a Kafka-kompatibilis Event Hubsba az [Apache Flink](event-hubs-kafka-flink-tutorial.md) vagy az [Akka Streams](event-hubs-kafka-akka-streams-tutorial.md) használatával.
