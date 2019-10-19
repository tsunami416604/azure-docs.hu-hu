---
title: Adatstream az Azure Event Hubs a Kafka protokoll használatával | Microsoft Docs
description: Ez a cikk az Azure-Event Hubs a Kafka protokoll és az API-k használatával történő adatfolyam-továbbítással kapcsolatos információkat tartalmazza.
services: event-hubs
author: ShubhaVijayasarathy
ms.author: shvija
ms.service: event-hubs
ms.topic: quickstart
ms.custom: seodec18
ms.date: 05/06/2019
ms.openlocfilehash: ebe3dc39cf135f09c084a8325d380b9bc9ef48dc
ms.sourcegitcommit: ae461c90cada1231f496bf442ee0c4dcdb6396bc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/17/2019
ms.locfileid: "72554964"
---
# <a name="data-streaming-with-event-hubs-using-the-kafka-protocol"></a>Adatfolyamok Event Hubs a Kafka protokoll használatával
Ez a rövid útmutató bemutatja, hogyan streamelhet a Kafka-kompatibilis Event Hubsba anélkül, hogy módosítaná a protokollügyfeleket vagy saját fürtöket futtatna. Megtudhatja, hogyan érheti el egy egyszerű konfigurációmódosítással az alkalmazásokban, hogy az előállítók és a fogyasztók kommunikáljanak a Kafka-kompatibilis Event Hubsszal. Az Azure Event Hubs az [Apache Kafka 1.0-s verzióját](https://kafka.apache.org/10/documentation.html) támogatja.

> [!NOTE]
> Ez a minta elérhető a [GitHubon](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/quickstart/java).

## <a name="prerequisites"></a>Előfeltételek

A rövid útmutató elvégzéséhez győződjön meg arról, hogy teljesülnek az alábbi előfeltételek:

* Olvassa át az [Apache Kafkához készült Event Hubsot](event-hubs-for-kafka-ecosystem-overview.md) ismertető cikket.
* Azure-előfizetés. Ha még nincs előfizetése, hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio), mielőtt hozzákezd.
* [Java fejlesztői készlet (JDK) 1.7+](https://aka.ms/azure-jdks).
* Egy [letöltött](https://maven.apache.org/download.cgi) és [telepített](https://maven.apache.org/install.html) Maven bináris archívum.
* [Git](https://www.git-scm.com/)
* [Kafka-kompatibilis Event Hubs-névtér](event-hubs-create.md)

## <a name="create-a-kafka-enabled-event-hubs-namespace"></a>Kafka-kompatibilis Event Hubs-névtér létrehozása

1. Jelentkezzen be a [Azure Portalba](https://portal.azure.com), és kattintson a képernyő bal felső részén található **erőforrás létrehozása** elemre.

2. Keressen rá az Event Hubsra, és válassza az itt látható lehetőségeket:
    
    ![Event Hubs keresése a portálon](./media/event-hubs-create-kafka-enabled/event-hubs-create-event-hubs.png)
 
3. Adjon meg egy egyedi nevet, és engedélyezze a Kafkát a névtéren. Kattintson a  **Create** (Létrehozás) gombra. Megjegyzés: a Kafka-Event Hubs csak a standard és a dedikált szint Event Hubs támogatja. Az alapszintű Event Hubs az összes Kafka-műveletre adott válaszként egy témakör-engedélyezési hibát ad vissza.
    
    ![Névtér létrehozása](./media/event-hubs-create-kafka-enabled/create-kafka-namespace.jpg)
 
4. A névtér létrehozását követően a **Beállítások** lap **Megosztott elérési szabályzatok** elemére kattintva kérje le a kapcsolati sztringet.

    ![Kattintás a Megosztott elérési szabályzatok elemre](./media/event-hubs-create/create-event-hub7.png)

5. Választhatja az alapértelmezett **RootManageSharedAccessKey** szabályzatot, vagy hozzáadhat egy újat is. Kattintson a szabályzat nevére, és másolja a vágólapra a kapcsolati sztringet. 
    
    ![Szabályzat kiválasztása](./media/event-hubs-create/create-event-hub8.png)
 
6. Adja hozzá ezt a kapcsolati sztringet a Kafka-alkalmazás konfigurációjához.

Így már streamelheti az eseményeket az Event Hubsba a Kafka-protokollt használó alkalmazásaiból.

## <a name="send-and-receive-messages-with-kafka-in-event-hubs"></a>Üzenetek küldése és fogadása a Kafkával az Event Hubsban

1. Klónozza a [Kafkához készült Event Hubs-adattárat](https://github.com/Azure/azure-event-hubs-for-kafka).

2. Nyissa meg a `azure-event-hubs-for-kafka/quickstart/java/producer` címet.

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
    
5. Nyissa meg a `azure-event-hubs-for-kafka/quickstart/java/consumer` címet.

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

## <a name="next-steps"></a>Következő lépések
Ebben a cikkben bemutattuk, hogyan streamelhet Kafka-kompatibilis Event Hubsba anélkül, hogy módosítaná a protokollügyfeleket vagy saját fürtöket futtatna. További információért folytassa az alábbi oktatóanyaggal:

* [Ismerkedés az Event Hubs szolgáltatással](event-hubs-what-is-event-hubs.md)
* [Ismerkedés a Kafkához készült Event Hubs szolgáltatással](event-hubs-for-kafka-ecosystem-overview.md)
* [További minták megismerése a Kafkához készült Event Hubs GitHub-oldalán](https://github.com/Azure/azure-event-hubs-for-kafka)
* A [MirrorMaker](https://cwiki.apache.org/confluence/pages/viewpage.action?pageId=27846330) segítségével [továbbíthatja a helyszíni Kafka-eseményeket a felhőben elérhető Event Hubs Kafka-ra.](event-hubs-kafka-mirror-maker-tutorial.md)
* Ismerje meg, hogyan streamelhet a Kafka-kompatibilis Event Hubsba az [Apache Flink](event-hubs-kafka-flink-tutorial.md) vagy az [Akka Streams](event-hubs-kafka-akka-streams-tutorial.md) használatával
