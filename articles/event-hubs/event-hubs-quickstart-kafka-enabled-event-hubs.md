---
title: 'Rövid útmutató: Adatfolyam-továbbítás az Azure Event Hubs használatával a Kafka protokoll használatával'
description: 'Rövid útmutató: Ez a cikk a Kafka protokoll és API-k használatával az Azure Event Hubs-ba való streamelésről nyújt tájékoztatást.'
services: event-hubs
author: ShubhaVijayasarathy
ms.author: shvija
ms.service: event-hubs
ms.topic: quickstart
ms.custom: seodec18
ms.date: 02/12/2020
ms.openlocfilehash: 4a0d7d30ea02af222ab4a758c18b46d7488e1a8b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80280711"
---
# <a name="quickstart-data-streaming-with-event-hubs-using-the-kafka-protocol"></a>Rövid útmutató: Adatfolyam-továbbítás az Event Hubs használatával a Kafka protokoll használatával
Ez a rövid útmutató bemutatja, hogyan streamelhet az Event Hubs-ba a protokollügyfelek módosítása vagy a saját fürtök futtatása nélkül. Megtudhatja, hogyan használhatja a gyártók és a fogyasztók beszélni Event Hubs csak egy konfigurációs változás az alkalmazásokban. Az Azure Event Hubs az [Apache Kafka 1.0-s verzióját](https://kafka.apache.org/10/documentation.html) támogatja.

> [!NOTE]
> Ez a minta elérhető a [GitHubon](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/quickstart/java).

## <a name="prerequisites"></a>Előfeltételek

A rövid útmutató elvégzéséhez győződjön meg arról, hogy teljesülnek az alábbi előfeltételek:

* Olvassa át az [Apache Kafkához készült Event Hubsot](event-hubs-for-kafka-ecosystem-overview.md) ismertető cikket.
* Azure-előfizetés. Ha még nincs előfizetése, hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio), mielőtt hozzákezd.
* [Java fejlesztői készlet (JDK) 1.7+](https://aka.ms/azure-jdks).
* [Töltsön le](https://maven.apache.org/download.cgi) és [telepítsen](https://maven.apache.org/install.html) egy Maven bináris archívumot.
* [Git](https://www.git-scm.com/)


## <a name="create-a-kafka-enabled-event-hubs-namespace"></a>Kafka-kompatibilis Event Hubs-névtér létrehozása
Szabványos **szintű** Event Hubs névtér létrehozásakor a névtér Kafka végpontja automatikusan engedélyezve lesz. A Kafka protokollt használó alkalmazások eseményeit streamelheti standard szintű Event Hubs-ba. Kövesse a lépésenkénti utasításokat a [Hozzon létre egy eseményközpontot](event-hubs-create.md) az Azure Portal használatával egy **szabványos** szintű Event Hubs névtér létrehozásához. 

> [!NOTE]
> A Kafka eseményközpontjai csak **normál** és **dedikált** szinteken érhetők el. Az **alapszintű** szint nem támogatja a Kafka az Event Hubs.

## <a name="send-and-receive-messages-with-kafka-in-event-hubs"></a>Üzenetek küldése és fogadása a Kafkával az Event Hubsban

1. Klónozza a [Kafkához készült Event Hubs-adattárat](https://github.com/Azure/azure-event-hubs-for-kafka).

2. Nyissa meg a `azure-event-hubs-for-kafka/quickstart/java/producer` címet.

3. Az alábbiakban látható módon módosítsa az előállító konfigurációs adatait az `src/main/resources/producer.config` fájlban:

    **Ssl:**

    ```xml
    bootstrap.servers=NAMESPACENAME.servicebus.windows.net:9093
    security.protocol=SASL_SSL
    sasl.mechanism=PLAIN
    sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required username="$ConnectionString" password="{YOUR.EVENTHUBS.CONNECTION.STRING}";
    ```
    **Oauth:**

    ```xml
    bootstrap.servers=NAMESPACENAME.servicebus.windows.net:9093
    security.protocol=SASL_SSL
    sasl.mechanism=OAUTHBEARER
    sasl.jaas.config=org.apache.kafka.common.security.oauthbearer.OAuthBearerLoginModule required;
    sasl.login.callback.handler.class=CustomAuthenticateCallbackHandler;
    ```    

    A GitHubon található CustomAuthenticateCallbackHandler mintakezelő osztály forráskódját [itt](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/oauth/java/appsecret/producer/src/main/java)találja.
4. Futtassa a termelői kódot, és streamelje az eseményeket az Eseményközpontokba:
   
    ```shell
    mvn clean package
    mvn exec:java -Dexec.mainClass="TestProducer"                                    
    ```
    
5. Nyissa meg a `azure-event-hubs-for-kafka/quickstart/java/consumer` címet.

6. Az alábbiakban látható módon módosítsa a fogyasztó konfigurációs adatait az `src/main/resources/consumer.config` fájlban:
   
    **Ssl:**

    ```xml
    bootstrap.servers=NAMESPACENAME.servicebus.windows.net:9093
    security.protocol=SASL_SSL
    sasl.mechanism=PLAIN
    sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required username="$ConnectionString" password="{YOUR.EVENTHUBS.CONNECTION.STRING}";
    ```

    **Oauth:**

    ```xml
    bootstrap.servers=NAMESPACENAME.servicebus.windows.net:9093
    security.protocol=SASL_SSL
    sasl.mechanism=OAUTHBEARER
    sasl.jaas.config=org.apache.kafka.common.security.oauthbearer.OAuthBearerLoginModule required;
    sasl.login.callback.handler.class=CustomAuthenticateCallbackHandler;
    ``` 

    A GitHubon található CustomAuthenticateCallbackHandler mintakezelő osztály forráskódját [itt](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/oauth/java/appsecret/consumer/src/main/java)találja.

    A Kafka eseményelosztóinak összes OAuth-mintáját [itt](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/oauth)találja.
7. Futtassa a fogyasztó kódját, és végezze el a feldolgozást a Kafka-kompatibilis Event Hubsból a Kafka-ügyfelek használatával:

    ```java
    mvn clean package
    mvn exec:java -Dexec.mainClass="TestConsumer"                                    
    ```

Ha az Event Hubs Kafka-fürtön vannak események, most el kell kezdeniük érkezni a fogyasztóról.

## <a name="next-steps"></a>További lépések
Ebben a cikkben megtanulta, hogyan streamelhet az Event Hubs-ba a protokollügyfelek módosítása vagy a saját fürtök futtatása nélkül. További információ: a következő cikkek és minták:

- [Ismerkedés a Kafkához készült Event Hubs szolgáltatással](event-hubs-for-kafka-ecosystem-overview.md)
- [Rövid útmutatók a Kafka eseményközpontjaihoz a GitHubon](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/quickstart)
- [Oktatóanyagok a Kafka eseményközpontokhoz a GitHubon](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials)
- A [MirrorMaker](https://cwiki.apache.org/confluence/pages/viewpage.action?pageId=27846330) használatával [eseményeket streamelhet a kafkai kafkai eseményekről a Kafka által engedélyezett eseményközpontokba a felhőben.](event-hubs-kafka-mirror-maker-tutorial.md)
- Ismerje meg, hogyan streamelhet a Kafka-kompatibilis Event Hubsba az [Apache Flink](event-hubs-kafka-flink-tutorial.md) vagy az [Akka Streams](event-hubs-kafka-akka-streams-tutorial.md) használatával
