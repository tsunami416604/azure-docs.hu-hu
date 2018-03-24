---
title: Események küldése az Azure Event Hubs Java használatával |} Microsoft Docs
description: Bevezetés az Event Hubs Java használatával küldése
services: event-hubs
documentationcenter: ''
author: sethmanheim
manager: timlt
editor: ''
ms.assetid: ''
ms.service: event-hubs
ms.workload: core
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/21/2018
ms.author: sethm
ms.openlocfilehash: 5dd0c88dab9ff4b7073a9acf6872b4c3ff085586
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/23/2018
---
# <a name="send-events-to-azure-event-hubs-using-java"></a>Események küldése az Azure Event Hubs Java használatával

Az Event Hubs egy kiválóan méretezhető fogadórendszer, amely is több millió eseményt másodpercenként, az alkalmazás engedélyezése feldolgozni, és elemezze a nagy mennyiségű adatot a csatlakoztatott eszközök és alkalmazások által létrehozott. Miután egy eseményközpontba való összegyűjtését, átalakítás és tárolására is használható adatok bármilyen valós idejű elemzési szolgáltató vagy tárolási fürt használatával.

További információkért lásd: a [Event Hubs – áttekintés][Event Hubs overview].

Ez az oktatóanyag bemutatja, hogyan események küldése az eseményközpontba egy Java-Konzolalkalmazás használatával. A Java Event Processor Host szalagtárat használó események fogadásához, lásd: [Ez a cikk](event-hubs-java-get-started-receive-eph.md), vagy kattintson a bal oldali tartalomjegyzék a megfelelő fogadó nyelvet.

Az oktatóanyag teljesítéséhez a következőkre lesz szüksége:

* A Java-fejlesztőkörnyezet. Ebben az oktatóanyagban feltételezzük, hogy [Eclipse](https://www.eclipse.org/).
* Aktív Azure-fiók. Ha még nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot][] megkezdése előtt.

A jelen oktatóanyagban található kód alapján a [küldése GitHub minta](https://github.com/Azure/azure-event-hubs/tree/master/samples/Java/Basic/Send), amely láthatja, hogy a teljes működő alkalmazást.

## <a name="send-events-to-event-hubs"></a>Események küldése az Event Hubs

Az Event Hubs Java ügyfélkódtár a Maven-projektek használható a [Maven központi tárházban](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22azure-eventhubs%22). Ebben a könyvtárban, a következő függőségi deklarációjában használja az Maven project fájl hivatkozhat. A jelenlegi verzió: 1.0.0:    

```xml
<dependency>
    <groupId>com.microsoft.azure</groupId>
    <artifactId>azure-eventhubs</artifactId>
    <version>1.0.0</version>
</dependency>
```

A különböző típusú buildkörnyezeteket, explicit módon beszerezheti a legfrissebb kiadott JAR fájlok a [Maven központi tárházban](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22azure-eventhubs%22).  

Egy egyszerű esemény-közzétevő, importálja a *com.microsoft.azure.eventhubs* csomag az Event Hubs ügyfél osztályok és a *com.microsoft.azure.servicebus* segédprogram osztályok esetében például a csomag közös kivételeket, amelyek az Azure Service Bus üzenetküldési ügyfél vannak megosztva. 

### <a name="declare-the-send-class"></a>A Küldés osztály deklarálható

A következő mintában először hozzon létre egy új Maven-projektet egy konzol/felületalkalmazáshoz a kedvenc Java-fejlesztőkörnyezetében. Az osztály neve `Send`:     

```java
package com.microsoft.azure.eventhubs.samples.send;

import com.google.gson.Gson;
import com.google.gson.GsonBuilder;
import com.microsoft.azure.eventhubs.ConnectionStringBuilder;
import com.microsoft.azure.eventhubs.EventData;
import com.microsoft.azure.eventhubs.EventHubClient;
import com.microsoft.azure.eventhubs.PartitionSender;
import com.microsoft.azure.eventhubs.EventHubException;

import java.io.IOException;
import java.nio.charset.Charset;
import java.time.Instant;
import java.util.Random;
import java.util.concurrent.ExecutionException;
import java.util.concurrent.Executors;
import java.util.concurrent.ExecutorService;

public class Send {

    public static void main(String[] args)
            throws EventHubException, ExecutionException, InterruptedException, IOException {
```

### <a name="construct-connection-string"></a>Összeállíthatja a kapcsolati karakterlánc

A ConnectionStringBuilder osztály használatával hozható létre, hogy az Event Hubs ügyfél példány kapcsolati karakterlánc-érték. A helyőrzőket cserélje le a létrehozása után a névtér és az event hub kapott értékeket:

```java
   final ConnectionStringBuilder connStr = new ConnectionStringBuilder()
      .setNamespaceName("----NamespaceName-----")
      .setEventHubName("----EventHubName-----")
      .setSasKeyName("-----SharedAccessSignatureKeyName-----")
      .setSasKey("---SharedAccessSignatureKey----");
```

### <a name="send-events"></a>Események küldése

Ezután hozzon létre egyes számú esemény által karakterlánc átalakítása azokat a UTF-8 bájtos kódolását. Ezután hozzon létre új Event Hubs-ügyfél példányt a kapcsolati karakterláncból, és elküldeni az üzenetet.   

```java 
byte[] payloadBytes = "Test AMQP message from JMS".getBytes("UTF-8");
EventData sendEvent = new EventData(payloadBytes);

final EventHubClient ehClient = EventHubClient.createSync(connStr.toString(), executorService);
ehClient.sendSync(sendEvent);
    
// close the client at the end of your program
ehClient.closeSync();

``` 

## <a name="next-steps"></a>További lépések

Az alábbi webhelyeken további információt talál az Event Hubsról:

* [Fogadása az EventProcessorHost használatához események](event-hubs-java-get-started-receive-eph.md)
* [Event Hubs – áttekintés][Event Hubs overview]
* [Eseményközpont létrehozása](event-hubs-create.md)
* [Event Hubs – gyakori kérdések](event-hubs-faq.md)

<!-- Links -->
[Event Hubs overview]: event-hubs-overview.md
[ingyenes fiókot]: https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio

