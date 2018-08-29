---
title: Események küldése az Azure Event Hubs Java használatával |} A Microsoft Docs
description: Ismerkedés az Event Hubs Java használatával történő küldése
services: event-hubs
author: ShubhaVijayasarathy
manager: timlt
ms.service: event-hubs
ms.workload: core
ms.topic: article
ms.date: 08/27/2018
ms.author: shvija
ms.openlocfilehash: f67982eda60a8fdfdf0d50785827c513275fd202
ms.sourcegitcommit: 2ad510772e28f5eddd15ba265746c368356244ae
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/28/2018
ms.locfileid: "43124755"
---
# <a name="send-events-to-azure-event-hubs-using-java"></a>Események küldése az Azure Event Hubs Java használatával

Event Hubs szolgáltatás egy kiválóan méretezhető fogadórendszer, amely képes másodpercenként több millió feldolgozására, ezáltal az alkalmazások feldolgozásához, és a csatlakoztatott eszközök és alkalmazások által létrehozott hatalmas adatmennyiségek elemzését. Miután az adatoknak egy eseményközpontba való összegyűjtését, átalakíthatja és adatok bármilyen valós idejű elemzési szolgáltató vagy tárolási fürt használatával tárolhatja.

További információkért lásd: a [Event Hubs – áttekintés][Event Hubs overview].

Ez az oktatóanyag bemutatja, hogyan küldhet eseményeket egy eseményközpontba egy konzolalkalmazást, a Java használatával. A Java Event Processor Host tár használatával események fogadására, lásd: [Ez a cikk](event-hubs-java-get-started-receive-eph.md), vagy kattintson a megfelelő fogadónyelvre a bal oldali tartalomjegyzékben.

## <a name="prerequisites"></a>Előfeltételek

Ez az oktatóanyag elvégzéséhez szüksége van a következő előfeltételek vonatkoznak:

* A Java fejlesztési környezet. Ebben az oktatóanyagban [Eclipse](https://www.eclipse.org/).
* Aktív Azure-fiók. Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot][] a feladatok megkezdése előtt.

A jelen oktatóanyagban szereplő kód alapján a [SimpleSend GitHub-minta](https://github.com/Azure/azure-event-hubs/tree/master/samples/Java/Basic/SimpleSend), amely a teljes megtekintéséhez tekintse meg működő alkalmazást.

## <a name="send-events-to-event-hubs"></a>Események küldése az Event Hubsba

A Java ügyféloldali kódtár, az Event Hubs szolgáltatás a Maven-projektekhez érhető el a [Maven központi tárházból](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22azure-eventhubs%22). Ebben a könyvtárban, a Maven-projektfájlból belül a következő függőségi nyilatkozat használatával hivatkozhat. A jelenlegi verzió: 1.0.2-es:    

```xml
<dependency>
    <groupId>com.microsoft.azure</groupId>
    <artifactId>azure-eventhubs</artifactId>
    <version>1.0.2</version>
</dependency>
```

A különböző típusú összeállítási környezetekhez explicit módon szerezheti be a legutóbb kiadott JAR-fájlokat a [Maven központi tárházból](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22azure-eventhubs%22).  

Egy egyszerű esemény-közzétevő importálja a *com.microsoft.azure.eventhubs* az Event Hubs ügyfélosztályok csomag és a *com.microsoft.azure.servicebus* például szolgáló segédeszközosztályok csomag gyakori kivételek, amelyek az Azure Service Bus üzenetkezelési ügyfél vannak megosztva. 

### <a name="declare-the-send-class"></a>Deklarálja a küldési osztály

A következő mintában először hozzon létre egy új Maven-projektet egy konzol/felületalkalmazáshoz a kedvenc Java-fejlesztőkörnyezetében. Az osztály neve `SimpleSend`:     

```java
package com.microsoft.azure.eventhubs.samples.send;

import com.google.gson.Gson;
import com.google.gson.GsonBuilder;
import com.microsoft.azure.eventhubs.ConnectionStringBuilder;
import com.microsoft.azure.eventhubs.EventData;
import com.microsoft.azure.eventhubs.EventHubClient;
import com.microsoft.azure.eventhubs.EventHubException;

import java.io.IOException;
import java.nio.charset.Charset;
import java.time.Instant;
import java.util.concurrent.ExecutionException;
import java.util.concurrent.Executors;
import java.util.concurrent.ExecutorService;

public class SimpleSend {

    public static void main(String[] args)
            throws EventHubException, ExecutionException, InterruptedException, IOException {
            
            
    }
 }
```

### <a name="construct-connection-string"></a>Kapcsolati karakterlánc létrehozása

A ConnectionStringBuilder osztály használatával hozhat létre egy kapcsolati karakterlánc értékét az Event Hubs-ügyfél példány számára. A helyőrzőket cserélje le a névtér és az eseményközpont létrehozásakor beszerzett:

```java
final ConnectionStringBuilder connStr = new ConnectionStringBuilder()
        .setNamespaceName("Your Event Hubs namespace name")
        .setEventHubName("Your event hub")
        .setSasKeyName("Your policy name")
        .setSasKey("Your primary SAS key");
```

### <a name="send-events"></a>Események küldése

Hozzon létre egy egyes számú esemény átalakításával keletkező egy karakterlánc, az UTF-8 bájtos kódolást. Ezután hozzon létre új Event Hubs-ügyfél példány a kapcsolati karakterláncból, és az üzenet elküldéséhez:   

```java 
String payload = "Message " + Integer.toString(i);
byte[] payloadBytes = gson.toJson(payload).getBytes(Charset.defaultCharset());
EventData sendEvent = EventData.create(payloadBytes);

final EventHubClient ehClient = EventHubClient.createSync(connStr.toString(), executorService);
ehClient.sendSync(sendEvent);
    
// close the client at the end of your program
ehClient.closeSync();

``` 

### <a name="how-messages-are-routed-to-eventhub-partitions"></a>Hogyan legyenek átirányítva az üzeneteket az EventHub-partíciók

Üzenetek fogyasztó megkaptuk, mielőtt közzétenni a partíciók először a kiadók által rendelkeznek. Üzeneteket az eseményközpontba szinkron módon történik a metódussal sendSync() a com.microsoft.azure.eventhubs.EventHubClient objektum közzétételét követően sikerült-e az összes rendelkezésre álló partíciókra Ciklikus időszeleteléses módon elosztott vagy egy adott partícióra küldött üzenet attól függően, hogy a partíciós kulcs van megadva, vagy nem.

Ha egy karakterlánc, amely a partíciókulcs meg van adva, a kulcs lesz kivonatolása határozza meg az eseményt, hogy melyik partíciót.

Ha nincs beállítva a partíciókulcsot, ciklikus-robined az összes rendelkezésre álló partíció lesz-e majd üzenetek

```java
// Serialize the event into bytes
byte[] payloadBytes = gson.toJson(messagePayload).getBytes(Charset.defaultCharset());

// Use the bytes to construct an {@link EventData} object
EventData sendEvent = EventData.create(payloadBytes);

// Transmits the event to event hub without a partition key
// If a partition key is not set, then we will round-robin to all topic partitions
eventHubClient.sendSync(sendEvent);

//  the partitionKey will be hash'ed to determine the partitionId to send the eventData to.
eventHubClient.sendSync(sendEvent, partitionKey);

// close the client at the end of your program
eventHubClient.closeSync();

```

## <a name="next-steps"></a>További lépések

Az alábbi webhelyeken további információt talál az Event Hubsról:

* [Események fogadása az EventProcessorHost használatával](event-hubs-java-get-started-receive-eph.md)
* [Event Hubs – áttekintés][Event Hubs overview]
* [Eseményközpont létrehozása](event-hubs-create.md)
* [Event Hubs – gyakori kérdések](event-hubs-faq.md)

<!-- Links -->
[Event Hubs overview]: event-hubs-overview.md
[ingyenes fiókot]: https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio

