---
title: Események küldése az Azure Event Hubs Java használatával |} A Microsoft Docs
description: Ismerkedés az Event Hubs Java használatával történő küldése
services: event-hubs
author: ShubhaVijayasarathy
manager: timlt
ms.service: event-hubs
ms.workload: core
ms.topic: article
ms.date: 10/18/2018
ms.author: shvija
ms.openlocfilehash: 87d3261d5d9604b004c949e384e9d48e957229d7
ms.sourcegitcommit: 668b486f3d07562b614de91451e50296be3c2e1f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/19/2018
ms.locfileid: "49455725"
---
# <a name="send-events-to-azure-event-hubs-using-java"></a>Események küldése az Azure Event Hubs Java használatával

Az Azure Event Hubs egy Big Data streamplatform és eseményfeldolgozó szolgáltatás, amely másodpercenként több millió esemény fogadására és feldolgozására képes. Az Event Hubs képes az elosztott szoftverek és eszközök által generált események, adatok vagy telemetria feldolgozására és tárolására. Az eseményközpontokba elküldött adatok bármilyen valós idejű elemzési szolgáltató vagy kötegelési/tárolóadapter segítségével átalakíthatók és tárolhatók. Az Event hubs részletes ismertetőt talál [Event Hubs – áttekintés](event-hubs-about.md) és [Event Hubs-szolgáltatások](event-hubs-features.md).

Ez az oktatóanyag bemutatja, hogyan küldhet eseményeket egy eseményközpontba egy Java nyelven írt konzolalkalmazással használatával. 

> [!NOTE]
> Ebben a rövid útmutatóban egy mintát, letöltheti a [GitHub](https://github.com/Azure/azure-event-hubs/tree/master/samples/Java/Basic/SimpleSend), cserélje le `EventHubConnectionString` és `EventHubName` karakterláncot az eseményközpontja értékeire, és futtassa azt. A lépéseket követheti azt is megteheti, ebben az oktatóanyagban hozhat létre saját.

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag teljesítéséhez a következő előfeltételekre lesz szüksége:

* A Java fejlesztési környezet. Ebben az oktatóanyagban [Eclipse](https://www.eclipse.org/).

## <a name="create-an-event-hubs-namespace-and-an-event-hub"></a>Event Hubs-névtér és eseményközpont létrehozása
Első lépésként az [Azure Portalon](https://portal.azure.com) hozzon létre egy Event Hubs típusú névteret, és szerezze be az alkalmazása és az eseményközpont közötti kommunikációhoz szükséges felügyeleti hitelesítő adatokat. A névtér és eseményközpont létrehozásához hajtsa végre az eljárást a [Ez a cikk](event-hubs-create.md), majd folytassa a következő lépéseket ebben az oktatóanyagban.

## <a name="add-reference-to-azure-event-hubs-library"></a>Az Azure Event Hubs-erőforrástárhoz hivatkozás hozzáadása

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

## <a name="write-code-to-send-messages-to-the-event-hub"></a>Kód írása az üzenetek küldése az event hubs való

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

Hozhat létre és futtassa a programot, és győződjön meg arról, hogy nincsenek-e hibák.

Gratulálunk! Üzeneteket küldött egy eseményközpontba.

### <a name="appendix-how-messages-are-routed-to-eventhub-partitions"></a>A függelék: Hogyan üzenetek legyenek átirányítva az EventHub-partíciók

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

Ebben a rövid útmutatóban elküldött üzeneteket az eseményközpontba Java használatával. Események fogadása az event hubs .NET-keretrendszer használatával kapcsolatban lásd: [események fogadása az event hubs - Java](event-hubs-java-get-started-receive-eph.md).

<!-- Links -->
[Event Hubs overview]: event-hubs-overview.md
[free account]: https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio

