---
title: "Az Azure Functions az Event Hubs kötések |} Microsoft Docs"
description: "Azure Event Hubs kötések az Azure Functions használatának megismerése."
services: functions
documentationcenter: na
author: wesmc7777
manager: cfowler
editor: 
tags: 
keywords: "Azure functions, Funkciók, Eseményfeldolgozási, dinamikus számítási kiszolgáló nélküli architektúrája"
ms.assetid: daf81798-7acc-419a-bc32-b5a41c6db56b
ms.service: functions
ms.devlang: multiple
ms.topic: reference
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 06/20/2017
ms.author: wesmc
ms.openlocfilehash: 85eb6985ef3579b1b2313db3ce5f91c3471da72f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="azure-functions-event-hubs-bindings"></a>Az Azure Functions az Event Hubs kötések
[!INCLUDE [functions-selector-bindings](../../includes/functions-selector-bindings.md)]

Ez a cikk azt ismerteti, hogyan konfigurálhatja és használhatja [Azure Event Hubs](../event-hubs/event-hubs-what-is-event-hubs.md) Azure Functions kötéseit.
Az Azure Functions támogatja indítható el, és az Event Hubs kötései kimeneti.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

Ha most ismerkedik az Azure Event Hubs, tekintse meg a [Event Hubs – áttekintés](../event-hubs/event-hubs-what-is-event-hubs.md).

<a name="trigger"></a>

## <a name="event-hub-trigger"></a>Hub eseményindító
Az Event Hubs eseményindító segítségével egy event hub eseményfelhasználó küldött esemény válaszolni. Az event hubs az eseményindító beállítása olvasási hozzáféréssel kell rendelkeznie.

Az Event Hubs függvény eseményindító használja a következő JSON-objektum a `bindings` function.json tömbje:

```json
{
    "type": "eventHubTrigger",
    "name": "<Name of trigger parameter in function signature>",
    "direction": "in",
    "path": "<Name of the event hub>",
    "consumerGroup": "Consumer group to use - see below",
    "connection": "<Name of app setting with connection string - see below>"
}
```

`consumerGroup`egy nem kötelező tulajdonság beállításához használja a [fogyasztói csoportot](../event-hubs/event-hubs-features.md#event-consumers) használt események központban előfizetni. Ha nincs megadva, a `$Default` fogyasztói csoportot használja.  
`connection`egy Alkalmazásbeállítás, amely tartalmazza a kapcsolati karakterláncot az event hubs névtér nevének kell lennie.
Másolja a kapcsolati karakterláncot kattintva a **kapcsolatadatok** gombra kattint, az a *névtér*, nem magát az eseményközpontba. Ez a kapcsolati karakterlánc kell rendelkeznie legalább olvasási engedéllyel az eseményindítót.

[További beállítások](https://github.com/Azure/azure-webjobs-sdk-script/wiki/host.json) egy host.json fájlban, és további Event Hubs eseményindítók megadható.  

<a name="triggerusage"></a>

## <a name="trigger-usage"></a>Eseményindító kihasználtsága
Az Event Hubs funkció aktiválása esetén az üzenet, amely elindítja az átad a függvény egy karakterlánc.

<a name="triggersample"></a>

## <a name="trigger-sample"></a>Eseményindító minta
Tegyük fel, hogy a következő Event Hubs trigger a `bindings` function.json tömbje:

```json
{
  "type": "eventHubTrigger",
  "name": "myEventHubMessage",
  "direction": "in",
  "path": "MyEventHub",
  "connection": "myEventHubReadConnectionString"
}
```

Tekintse meg a nyelvspecifikus mintát, amelyre bejelentkezik az eseményindító hub üzenet törzsét.

* [C#](#triggercsharp)
* [F#](#triggerfsharp)
* [Node.js](#triggernodejs)

<a name="triggercsharp"></a>

### <a name="trigger-sample-in-c"></a>A C# eseményindító minta #

```cs
using System;

public static void Run(string myEventHubMessage, TraceWriter log)
{
    log.Info($"C# Event Hub trigger function processed a message: {myEventHubMessage}");
}
```

Az esemény akkor is jelentkezhet egy [EventData](/dotnet/api/microsoft.servicebus.messaging.eventdata) objektum, amely hozzáférést biztosít az esemény-metaadatok.

```cs
#r "Microsoft.ServiceBus"
using System.Text;
using Microsoft.ServiceBus.Messaging;

public static void Run(EventData myEventHubMessage, TraceWriter log)
{
    log.Info($"{Encoding.UTF8.GetString(myEventHubMessage.GetBytes())}");
}
```

Események fogadásához egy kötegben, módosítsa a metódus-aláírás `string[]` vagy `EventData[]`.

```cs
public static void Run(string[] eventHubMessages, TraceWriter log)
{
    foreach (var message in eventHubMessages)
    {
        log.Info($"C# Event Hub trigger function processed a message: {message}");
    }
}
```

<a name="triggerfsharp"></a>

### <a name="trigger-sample-in-f"></a>Az F # eseményindító minta #

```fsharp
let Run(myEventHubMessage: string, log: TraceWriter) =
    log.Info(sprintf "F# eventhub trigger function processed work item: %s" myEventHubMessage)
```

<a name="triggernodejs"></a>

### <a name="trigger-sample-in-nodejs"></a>A node.js eseményindító minta

```javascript
module.exports = function (context, myEventHubMessage) {
    context.log('Node.js eventhub trigger function processed work item', myEventHubMessage);    
    context.done();
};
```

<a name="output"></a>

## <a name="event-hubs-output-binding"></a>Az Event Hubs kimeneti kötése
Az Event Hubs kimeneti kötése beírni az eseményeket az event hub eseményfelhasználó használja. Események írhat az eseményközpontba a küldési engedéllyel kell rendelkeznie.

A kimeneti kötés használja a következő JSON-objektum a `bindings` function.json tömbje:

```json
{
    "type": "eventHub",
    "name": "<Name of output parameter in function signature>",
    "path": "<Name of event hub>",
    "connection": "<Name of app setting with connection string - see below>"
    "direction": "out"
}
```

`connection`egy Alkalmazásbeállítás, amely tartalmazza a kapcsolati karakterláncot az event hubs névtér nevének kell lennie.
Másolja a kapcsolati karakterláncot kattintva a **kapcsolatadatok** gombra kattint, az a *névtér*, nem magát az eseményközpontba. Ez a kapcsolati karakterlánc az üzenetet küldeni az eseménystream küldési engedéllyel kell rendelkeznie.

## <a name="output-usage"></a>Kimeneti használata
Ez a szakasz bemutatja, hogyan használható az Event Hubs kimeneti a funkciókódot kötelező.

A következő paraméter típusú kimenetre küldheti a konfigurált eseményközpontba üzenetek:

* `out string`
* `ICollector<string>`(a kimeneti több üzenetek)
* `IAsyncCollector<string>`(aszinkron verzióját `ICollector<T>`)

<a name="outputsample"></a>

## <a name="output-sample"></a>Minta kimenet
Tegyük fel, hogy a következő Event Hubs kimeneti kötések a `bindings` function.json tömbje:

```json
{
    "type": "eventHub",
    "name": "outputEventHubMessage",
    "path": "myeventhub",
    "connection": "MyEventHubSend",
    "direction": "out"
}
```

Tekintse meg a nyelvspecifikus mintát, amely egy eseményt ír a még akkor is, az adatfolyam.

* [C#](#outcsharp)
* [F#](#outfsharp)
* [Node.js](#outnodejs)

<a name="outcsharp"></a>

### <a name="output-sample-in-c"></a>A C# kimeneti minta #

```cs
using System;

public static void Run(TimerInfo myTimer, out string outputEventHubMessage, TraceWriter log)
{
    String msg = $"TimerTriggerCSharp1 executed at: {DateTime.Now}";
    log.Verbose(msg);   
    outputEventHubMessage = msg;
}
```

Vagy, hozzon létre több üzenetet:

```cs
public static void Run(TimerInfo myTimer, ICollector<string> outputEventHubMessage, TraceWriter log)
{
    string message = $"Event Hub message created at: {DateTime.Now}";
    log.Info(message);
    outputEventHubMessage.Add("1 " + message);
    outputEventHubMessage.Add("2 " + message);
}
```

<a name="outfsharp"></a>

### <a name="output-sample-in-f"></a>Az F # kimeneti minta #

```fsharp
let Run(myTimer: TimerInfo, outputEventHubMessage: byref<string>, log: TraceWriter) =
    let msg = sprintf "TimerTriggerFSharp1 executed at: %s" DateTime.Now.ToString()
    log.Verbose(msg);
    outputEventHubMessage <- msg;
```

<a name="outnodejs"></a>

### <a name="output-sample-for-nodejs"></a>A Node.js kimeneti minta

```javascript
module.exports = function (context, myTimer) {
    var timeStamp = new Date().toISOString();
    context.log('Event Hub message created at: ', timeStamp);   
    context.bindings.outputEventHubMessage = "Event Hub message created at: " + timeStamp;
    context.done();
};
```

Vagy több üzenetet küldeni.

```javascript
module.exports = function(context) {
    var timeStamp = new Date().toISOString();
    var message = 'Event Hub message created at: ' + timeStamp;

    context.bindings.outputEventHubMessage = [];

    context.bindings.outputEventHubMessage.push("1 " + message);
    context.bindings.outputEventHubMessage.push("2 " + message);
    context.done();
};
```

## <a name="next-steps"></a>Következő lépések
[!INCLUDE [next steps](../../includes/functions-bindings-next-steps.md)]
