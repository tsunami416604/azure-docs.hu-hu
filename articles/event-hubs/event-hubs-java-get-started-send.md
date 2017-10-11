---
title: "Események küldése az Azure Event Hubs Java használatával |} Microsoft Docs"
description: "Bevezetés az Event Hubs Java használatával küldése"
services: event-hubs
documentationcenter: 
author: sethmanheim
manager: timlt
editor: 
ms.assetid: 
ms.service: event-hubs
ms.workload: core
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/15/2017
ms.author: sethm
ms.openlocfilehash: b31771001989e20b88bc8d7bca1afceb58ec197c
ms.sourcegitcommit: 50e23e8d3b1148ae2d36dad3167936b4e52c8a23
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/18/2017
---
# <a name="send-events-to-azure-event-hubs-using-java"></a>Események küldése az Azure Event Hubs Java használatával

## <a name="introduction"></a>Bevezetés
Az Event Hubs egy kiválóan méretezhető fogadórendszer, amely is több millió eseményt másodpercenként, az alkalmazás engedélyezése feldolgozni, és elemezze a nagy mennyiségű adatot a csatlakoztatott eszközök és alkalmazások által létrehozott. Miután egy eseményközpontba való összegyűjtését, átalakítás és tárolására is használható adatok bármilyen valós idejű elemzési szolgáltató vagy tárolási fürt használatával.

További információkért lásd: a [Event Hubs – áttekintés][Event Hubs overview].

Ez az oktatóanyag bemutatja, hogyan események küldése az eseményközpontba egy Java-Konzolalkalmazás használatával. A Java Event Processor Host szalagtárat használó események fogadásához, lásd: [Ez a cikk](event-hubs-java-get-started-receive-eph.md), vagy kattintson a bal oldali tartalomjegyzék a megfelelő fogadó nyelvet.

Az oktatóanyag teljesítéséhez a következőkre lesz szüksége:

* A Java-fejlesztőkörnyezet. Ebben az oktatóanyagban feltételezzük, hogy [Eclipse](https://www.eclipse.org/).
* Aktív Azure-fiók. <br/>Ha nincs fiókja, néhány perc alatt létrehozhat egy ingyenes fiókot. További információkért lásd: <a href="http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started%2F" target="_blank">Ingyenes Azure-fiók létrehozása</a>.

## <a name="send-messages-to-event-hubs"></a>Üzenetek küldése az Event Hubs szolgáltatásnak
Az Event Hubs Java ügyfélkódtár a Maven-projektek használható a [Maven központi tárházban](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22azure-eventhubs%22). Ebben a könyvtárban, a következő függőségi nyilatkozat az Maven project fájl használatával is hivatkozni:    

```xml
<dependency>
    <groupId>com.microsoft.azure</groupId>
    <artifactId>azure-eventhubs</artifactId>
    <version>{VERSION}</version>
</dependency>
```

A különböző típusú buildkörnyezeteket, explicit módon beszerezheti a legfrissebb kiadott JAR fájlok a [Maven központi tárházban](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22azure-eventhubs%22).  

Egy egyszerű esemény-közzétevő, importálja a *com.microsoft.azure.eventhubs* csomag az Event Hubs ügyfél osztályok és a *com.microsoft.azure.servicebus* segédprogram osztályok esetében például a csomag közös kivételeket, amelyek az Azure Service Bus üzenetküldési ügyfél vannak megosztva. 

A következő mintában először hozzon létre egy új Maven-projektet egy konzol/felületalkalmazáshoz a kedvenc Java-fejlesztőkörnyezetében. Az osztály neve `Send`.     

```java
import java.io.IOException;
import java.nio.charset.*;
import java.util.*;
import java.util.concurrent.ExecutionException;

import com.microsoft.azure.eventhubs.*;
import com.microsoft.azure.servicebus.*;

public class Send
{
    public static void main(String[] args) 
            throws ServiceBusException, ExecutionException, InterruptedException, IOException
    {
```

Cserélje le a névtér és a event hub neve az eseményközpont létrehozásakor használt értékek.

```java
    final String namespaceName = "----ServiceBusNamespaceName-----";
    final String eventHubName = "----EventHubName-----";
    final String sasKeyName = "-----SharedAccessSignatureKeyName-----";
    final String sasKey = "---SharedAccessSignatureKey----";
    ConnectionStringBuilder connStr = new ConnectionStringBuilder(namespaceName, eventHubName, sasKeyName, sasKey);
```

Ezután hozzon létre egyes számú esemény által karakterlánc átalakítása azokat a UTF-8 bájtos kódolását. Ezután hozzon létre új Event Hubs-ügyfél példányt a kapcsolati karakterláncból, és elküldeni az üzenetet.   

```java 

    byte[] payloadBytes = "Test AMQP message from JMS".getBytes("UTF-8");
    EventData sendEvent = new EventData(payloadBytes);

    EventHubClient ehClient = EventHubClient.createFromConnectionStringSync(connStr.toString());
    ehClient.sendSync(sendEvent);
    }
}

``` 

## <a name="next-steps"></a>Következő lépések
Az alábbi webhelyeken további információt talál az Event Hubsról:

* [Fogadása az EventProcessorHost használatához események](event-hubs-java-get-started-receive-eph.md)
* [Event Hubs – áttekintés][Event Hubs overview]
* [Eseményközpont létrehozása](event-hubs-create.md)
* [Event Hubs – gyakori kérdések](event-hubs-faq.md)

<!-- Links -->
[Event Hubs overview]: event-hubs-overview.md