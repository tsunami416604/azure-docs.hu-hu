---
title: "Az Azure Functions a Service Bus-eseményindítók és kötések |} Microsoft Docs"
description: "Azure Service Bus-eseményindítók és kötések az Azure Functions használatának megismerése."
services: functions
documentationcenter: na
author: christopheranderson
manager: cfowler
editor: 
tags: 
keywords: "Azure functions, Funkciók, Eseményfeldolgozási, dinamikus számítási kiszolgáló nélküli architektúrája"
ms.assetid: daedacf0-6546-4355-a65c-50873e74f66b
ms.service: functions
ms.devlang: multiple
ms.topic: reference
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 04/01/2017
ms.author: glenga
ms.openlocfilehash: 71149aaacc940a62e085cf1ce103a0214d05bd1c
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="azure-functions-service-bus-bindings"></a>Az Azure Functions a Service Bus kötések
[!INCLUDE [functions-selector-bindings](../../includes/functions-selector-bindings.md)]

Ez a cikk azt ismerteti, hogyan konfigurálását és az Azure Functions Azure Service Bus kötések használatát. 

Az Azure Functions támogatja aktiválhatja és Service Bus-üzenetsorok és témakörök kimeneti.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

<a name="trigger"></a>

## <a name="service-bus-trigger"></a>A Service Bus eseményindító
A Service Bus eseményindító használatával a Service Bus-üzenetsor vagy témakör üzenetek válaszolni. 

A Service Bus-üzenetsor és a témakör eseményindítók határozzák meg a következő JSON-objektumok a `bindings` function.json tömbje:

* *várólista* eseményindító:

    ```json
    {
        "name" : "<Name of input parameter in function signature>",
        "queueName" : "<Name of the queue>",
        "connection" : "<Name of app setting that has your queue's connection string - see below>",
        "accessRights" : "<Access rights for the connection string - see below>",
        "type" : "serviceBusTrigger",
        "direction" : "in"
    }
    ```

* *a témakör* eseményindító:

    ```json
    {
        "name" : "<Name of input parameter in function signature>",
        "topicName" : "<Name of the topic>",
        "subscriptionName" : "<Name of the subscription>",
        "connection" : "<Name of app setting that has your topic's connection string - see below>",
        "accessRights" : "<Access rights for the connection string - see below>",
        "type" : "serviceBusTrigger",
        "direction" : "in"
    }
    ```

Vegye figyelembe a következőket:

* A `connection`, [alkalmazásbeállítás létrehozása az függvény alkalmazásban](functions-how-to-use-azure-function-app-settings.md) , amely tartalmazza a Service Bus-névtér a kapcsolati karakterláncot, majd adja meg a az Alkalmazásbeállítás nevét a `connection` az eseményindító tulajdonságait. Szerezze be a kapcsolati karakterláncot a jelenik meg a lépéseket követve [a felügyeleti hitelesítő adatok beszerzése](../service-bus-messaging/service-bus-dotnet-get-started-with-queues.md#obtain-the-management-credentials).
  A kapcsolati karakterláncnak kell lennie, a Service Bus-névtér, nem kizárólagosan az adott üzenetsor vagy témakör.
  Ha nem adja meg `connection` üres, az eseményindító azt feltételezi, hogy egy alapértelmezett Service Bus kapcsolati karakterlánc van megadva egy alkalmazás nevű beállításával `AzureWebJobsServiceBus`.
* A `accessRights`, elérhető értékek a következők `manage` és `listen`. Az alapértelmezett érték `manage`, amely azt jelzi, hogy a `connection` rendelkezik a **kezelése** engedéllyel. Ha használja a kapcsolati karakterláncot, amely nem rendelkezik a **kezelése** engedély, `accessRights` való `listen`. Ellenkező esetben a futásidejű meghiúsulhat igénylő műveletek végrehajtását megkísérlő funkciók jogosultságaik kezelését.

## <a name="trigger-behavior"></a>Eseményindító viselkedése
* **Single-threading** - alapértelmezés szerint a funkciók futásidejű folyamatok több üzenetet párhuzamosan. Állítsa át tudja irányítani a futtatókörnyezet egyszerre csak egyetlen üzenetsor vagy témakör üzenet feldolgozásához, `serviceBus.maxConcurrentCalls` 1 *host.json*. 
  További információ *host.json*, lásd: [mappaszerkezet](functions-reference.md#folder-structure) és [host.json](https://github.com/Azure/azure-webjobs-sdk-script/wiki/host.json).
* **Elhalt üzenetek kezelésének** -Service Bus does saját elhalt üzenetek kezelésének, nem szabályozott, vagy az Azure Functions konfiguráció vagy a kód konfigurálva. 
* **PeekLock viselkedés** -a Functions futtatókörnyezete az üzenetet kap [ `PeekLock` mód](../service-bus-messaging/service-bus-performance-improvements.md#receive-mode) és hívások `Complete` az üzenetet, ha a függvény futtatása sikeresen befejeződött, vagy a hívások `Abandon` Ha a parancs nem működik. 
  Ha a függvény futásakor hosszabb, mint a `PeekLock` automatikusan megújítják időtúllépés, a zárolás.

<a name="triggerusage"></a>

## <a name="trigger-usage"></a>Eseményindító kihasználtsága
Ez a szakasz bemutatja, hogyan használható a Service Bus eseményindító a funkciókódot. 

A C# és F # a Service Bus eseményindító üzenet a következő bemeneti típusok bármelyikéhez deszerializálható:

* `string`-hasznosak lehetnek a karakterlánc-üzenetek
* `byte[]`-hasznosak lehetnek a bináris adatok
* Bármely [objektum](https://msdn.microsoft.com/library/system.object.aspx) - hasznosak lehetnek a JSON-adatokból.
  Egy egyéni bemeneti típus, például a deklarálható `CustomType`, az Azure Functions megkísérli a JSON-adatok deszerializálása be a megadott típus.
* `BrokeredMessage`-a deszerializált üzenet lehetővé teszi a [BrokeredMessage.GetBody<T>()](https://msdn.microsoft.com/library/hh144211.aspx) metódust.

A node.js a Service Bus eseményindító üzenet át a funkciót egy karakterlánc- vagy JSON-objektumból.

<a name="triggersample"></a>

## <a name="trigger-sample"></a>Eseményindító minta
Tegyük fel, a következő function.json:

```json
{
"bindings": [
    {
    "queueName": "testqueue",
    "connection": "MyServiceBusConnection",
    "name": "myQueueItem",
    "type": "serviceBusTrigger",
    "direction": "in"
    }
],
"disabled": false
}
```

Tekintse meg a nyelvspecifikus mintát, amely egy Service Bus üzenetsor-üzenetet feldolgozza.

* [C#](#triggercsharp)
* [F#](#triggerfsharp)
* [Node.js](#triggernodejs)

<a name="triggercsharp"></a>

### <a name="trigger-sample-in-c"></a>A C# eseményindító minta #

```cs
public static void Run(string myQueueItem, TraceWriter log)
{
    log.Info($"C# ServiceBus queue trigger function processed message: {myQueueItem}");
}
```

<a name="triggerfsharp"></a>

### <a name="trigger-sample-in-f"></a>Az F # eseményindító minta #

```fsharp
let Run(myQueueItem: string, log: TraceWriter) =
    log.Info(sprintf "F# ServiceBus queue trigger function processed message: %s" myQueueItem)
```

<a name="triggernodejs"></a>

### <a name="trigger-sample-in-nodejs"></a>A node.js eseményindító minta

```javascript
module.exports = function(context, myQueueItem) {
    context.log('Node.js ServiceBus queue trigger function processed message', myQueueItem);
    context.done();
};
```

<a name="output"></a>

## <a name="service-bus-output-binding"></a>A Service Bus kimeneti kötése
A Service Bus várólista és ez a témakör kimeneti függvény használata a következő JSON-objektumok a `bindings` function.json tömbje:

* *várólista* kimenete:

    ```json
    {
        "name" : "<Name of output parameter in function signature>",
        "queueName" : "<Name of the queue>",
        "connection" : "<Name of app setting that has your queue's connection string - see below>",
        "accessRights" : "<Access rights for the connection string - see below>",
        "type" : "serviceBus",
        "direction" : "out"
    }
    ```
* *a témakör* kimenete:

    ```json
    {
        "name" : "<Name of output parameter in function signature>",
        "topicName" : "<Name of the topic>",
        "subscriptionName" : "<Name of the subscription>",
        "connection" : "<Name of app setting that has your topic's connection string - see below>",
        "accessRights" : "<Access rights for the connection string - see below>",
        "type" : "serviceBus",
        "direction" : "out"
    }
    ```

Vegye figyelembe a következőket:

* A `connection`, [alkalmazásbeállítás létrehozása az függvény alkalmazásban](functions-how-to-use-azure-function-app-settings.md) , amely tartalmazza a Service Bus-névtér a kapcsolati karakterláncot, majd adja meg a az Alkalmazásbeállítás nevét a `connection` tulajdonság a kimeneti kötés. Szerezze be a kapcsolati karakterláncot a jelenik meg a lépéseket követve [a felügyeleti hitelesítő adatok beszerzése](../service-bus-messaging/service-bus-dotnet-get-started-with-queues.md#obtain-the-management-credentials).
  A kapcsolati karakterláncnak kell lennie, a Service Bus-névtér, nem kizárólagosan az adott üzenetsor vagy témakör.
  Ha nem adja meg `connection` üres, a kimeneti kötés azt feltételezi, hogy egy alapértelmezett Service Bus kapcsolati karakterlánc van megadva egy alkalmazás nevű beállításával `AzureWebJobsServiceBus`.
* A `accessRights`, elérhető értékek a következők `manage` és `listen`. Az alapértelmezett érték `manage`, amely azt jelzi, hogy a `connection` rendelkezik a **kezelése** engedéllyel. Ha használja a kapcsolati karakterláncot, amely nem rendelkezik a **kezelése** engedély, `accessRights` való `listen`. Ellenkező esetben a futásidejű meghiúsulhat igénylő műveletek végrehajtását megkísérlő funkciók jogosultságaik kezelését.

<a name="outputusage"></a>

## <a name="output-usage"></a>Kimeneti használata
C# és F #, az Azure Functions létrehozhat egy Service Bus-üzenetsor a következő típusok:

* Bármely [objektum](https://msdn.microsoft.com/library/system.object.aspx) -paraméterek definícióját a következőképpen néz `out T paramName` (C#).
  Funkciók deserializes az objektum JSON üzenetbe. Ha a kimeneti érték értéke null, ha a függvény kilép, funkciók az üzenet null objektumot hoz létre.
* `string`-Paraméterek definícióját a következőképpen néz `out string paraName` (C#). Ha a paraméter értéke nem null értékű, amikor a függvény kilép, a funkciók létrehoz egy üzenetet.
* `byte[]`-Paraméterek definícióját a következőképpen néz `out byte[] paraName` (C#). Ha a paraméter értéke nem null értékű, amikor a függvény kilép, a funkciók létrehoz egy üzenetet.
* `BrokeredMessage`Paraméterek definícióját a következőképpen néz `out BrokeredMessage paraName` (C#). Ha a paraméter értéke nem null értékű, amikor a függvény kilép, a funkciók létrehoz egy üzenetet.

A több üzenet létrehozása a C# függvényben, használhat `ICollector<T>` vagy `IAsyncCollector<T>`. Egy üzenet jön létre, ha meghívja a `Add` metódust.

A node.js, rendelhet egy karakterlánc, egy bájttömböt vagy egy Javascript-objektum (deszerializálni a JSON-ba) `context.binding.<paramName>`.

<a name="outputsample"></a>

## <a name="output-sample"></a>Minta kimenet
Tegyük fel, amely meghatározza egy Service Bus várólista kimenete a következő function.json:

```json
{
    "bindings": [
        {
            "schedule": "0/15 * * * * *",
            "name": "myTimer",
            "runsOnStartup": true,
            "type": "timerTrigger",
            "direction": "in"
        },
        {
            "name": "outputSbQueue",
            "type": "serviceBus",
            "queueName": "testqueue",
            "connection": "MyServiceBusConnection",
            "direction": "out"
        }
    ],
    "disabled": false
}
```

Tekintse meg a nyelvspecifikus mintát, amely egy üzenetet küld a service bus-üzenetsorba.

* [C#](#outcsharp)
* [F#](#outfsharp)
* [Node.js](#outnodejs)

<a name="outcsharp"></a>

### <a name="output-sample-in-c"></a>A C# kimeneti minta #

```cs
public static void Run(TimerInfo myTimer, TraceWriter log, out string outputSbQueue)
{
    string message = $"Service Bus queue message created at: {DateTime.Now}";
    log.Info(message); 
    outputSbQueue = message;
}
```

Vagy, hozzon létre több üzenetet:

```cs
public static void Run(TimerInfo myTimer, TraceWriter log, ICollector<string> outputSbQueue)
{
    string message = $"Service Bus queue message created at: {DateTime.Now}";
    log.Info(message); 
    outputSbQueue.Add("1 " + message);
    outputSbQueue.Add("2 " + message);
}
```

<a name="outfsharp"></a>

### <a name="output-sample-in-f"></a>Az F # kimeneti minta #

```fsharp
let Run(myTimer: TimerInfo, log: TraceWriter, outputSbQueue: byref<string>) =
    let message = sprintf "Service Bus queue message created at: %s" (DateTime.Now.ToString())
    log.Info(message)
    outputSbQueue = message
```

<a name="outnodejs"></a>

### <a name="output-sample-in-nodejs"></a>Kimeneti minta node.js

```javascript
module.exports = function (context, myTimer) {
    var message = 'Service Bus queue message created at ' + timeStamp;
    context.log(message);   
    context.bindings.outputSbQueueMsg = message;
    context.done();
};
```

Vagy, hozzon létre több üzenetet:

```javascript
module.exports = function (context, myTimer) {
    var message = 'Service Bus queue message created at ' + timeStamp;
    context.log(message);   
    context.bindings.outputSbQueueMsg = [];
    context.bindings.outputSbQueueMsg.push("1 " + message);
    context.bindings.outputSbQueueMsg.push("2 " + message);
    context.done();
};
```

## <a name="next-steps"></a>Következő lépések
[!INCLUDE [next steps](../../includes/functions-bindings-next-steps.md)]

