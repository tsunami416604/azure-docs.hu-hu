---
title: Streamelés az Azure Event Hubs Kafkához készült ökoszisztémájában | Microsoft Docs
description: Streamelés az Event Hubsba a Kafka-protokoll és a Kafka API-k használatával.
services: event-hubs
documentationcenter: ''
author: basilhariri
manager: timlt
editor: ''
ms.assetid: ''
ms.service: event-hubs
ms.devlang: na
ms.topic: quickstart
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/03/2018
ms.author: bahariri
ms.openlocfilehash: cabbb7ed6157a6c68530ab6b5f405aa67b31a1b2
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/10/2018
---
# <a name="stream-into-event-hubs-for-kafka-ecosystem"></a>Streamelés az Event Hubs Kafkához készült ökoszisztémájában

> [!NOTE]
> Ez a minta elérhető a [GitHubon](https://github.com/Azure/azure-event-hubs).

Ez a rövid útmutató bemutatja, hogyan streamelhet a Kafka-kompatibilis Event Hubsba anélkül, hogy módosítaná a protokollügyfeleket vagy saját fürtöket futtatna. Megtudhatja, hogyan érheti el egy egyszerű konfigurációmódosítással az alkalmazásokban, hogy az előállítók és a fogyasztók kommunikáljanak a Kafka-kompatibilis Event Hubszal. Az Azure Event Hubs Kafkához készült ökoszisztémája az [Apache Kafka 1.0-s verzióját](https://kafka.apache.org/10/documentation.html) támogatja.

## <a name="prerequisites"></a>Előfeltételek

A rövid útmutató elvégzéséhez győződjön meg arról, hogy rendelkezik az alábbiakkal:

* Azure-előfizetés. Ha még nincs előfizetése, hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio), mielőtt hozzákezd.
* [Java fejlesztői készlet (JDK) 1.7+](http://www.oracle.com/technetwork/java/javase/downloads/index.html).
* Egy [letöltött](http://maven.apache.org/download.cgi) és [telepített](http://maven.apache.org/install.html) Maven bináris archívum.
* [Git](https://www.git-scm.com/)
* [Kafka-kompatibilis Event Hubs-névtér](event-hubs-create.md)

## <a name="send-and-receive-messages-with-kafka-in-event-hubs"></a>Üzenetek küldése és fogadása a Kafkával az Event Hubsban

1. Klónozza az [Azure Event Hubs-adattárat](https://github.com/Azure/azure-event-hubs).

2. Nyissa meg a `azure-event-hubs/samples/kafka/quickstart/producer` címet.

3. Az alábbiakban látható módon módosítsa az előállító konfigurációs adatait az src/main/resources/producer.config fájlban.

    ```xml
    bootstrap.servers={YOUR.EVENTHUBS.FQDN}:9093
    security.protocol=SASL_SSL
    sasl.mechanism=PLAIN
    sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required username="$ConnectionString" password="{YOUR.EVENTHUBS.CONNECTION.STRING}";
    ```
4. Futtassa az előállító kódját, és streameljen a Kafka-kompatibilis Event Hubsba.
   
    ```java
    mvn clean package
    mvn exec:java -Dexec.mainClass="TestProducer"                                    
    ```
5. Navigáljon az azure-event-hubs/samples/kafka/quickstart/consumer helyre.

6. Az alábbiakban látható módon módosítsa a fogyasztó konfigurációs adatait az src/main/resources/consumer.config fájlban.
   
    ```xml
    bootstrap.servers={YOUR.EVENTHUBS.FQDN}:9093
    security.protocol=SASL_SSL
    sasl.mechanism=PLAIN
    sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required username="$ConnectionString" password="{YOUR.EVENTHUBS.CONNECTION.STRING}";
    ```

7. Futtassa a fogyasztó kódját, és végezze el a feldolgozást a Kafka-kompatibilis Event Hubsból a Kafka-ügyfelek használatával.

    ```java
    mvn clean package
    mvn exec:java -Dexec.mainClass="TestConsumer"                                    
    ```

Ha az Event Hubs Kafka-fürtön vannak az előállítóról érkezett várakozó események, most el kell kezdeniük érkezni a fogyasztóról.

## <a name="next-steps"></a>További lépések

* [Ismerkedés a Kafkához készült Event Hubs ökoszisztémájával](event-hubs-for-kafka-ecosystem-overview.md)
* [Ismerkedés az Event Hubs szolgáltatással](event-hubs-what-is-event-hubs.md)
* A [MirrorMaker](https://cwiki.apache.org/confluence/pages/viewpage.action?pageId=27846330) használatával eseményeket streamelhet a helyszíni Kafkából a felhőben található Kafka-kompatibilis Event Hubsba.](event-hubs-kafka-mirror-maker-tutorial.md)
