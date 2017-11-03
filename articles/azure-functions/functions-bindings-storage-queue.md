---
title: "Az Azure Functions várólista tárolási kötések |} Microsoft Docs"
description: "Azure Storage eseményindítók és kötések az Azure Functions használatának megismerése."
services: functions
documentationcenter: na
author: ggailey777
manager: cfowler
editor: 
tags: 
keywords: "Azure functions, Funkciók, Eseményfeldolgozási, dinamikus számítási kiszolgáló nélküli architektúrája"
ms.assetid: 4e6a837d-e64f-45a0-87b7-aa02688a75f3
ms.service: functions
ms.devlang: multiple
ms.topic: reference
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 05/30/2017
ms.author: glenga
ms.openlocfilehash: b68ce106ceb25d19ee0bbde287891d553a448560
ms.sourcegitcommit: 963e0a2171c32903617d883bb1130c7c9189d730
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/20/2017
---
# <a name="azure-functions-queue-storage-bindings"></a>Az Azure Queue Storage funkciók kötések
[!INCLUDE [functions-selector-bindings](../../includes/functions-selector-bindings.md)]

Ez a cikk ismerteti, konfigurálása és az Azure Functions kód Azure Queue storage kötések. Az Azure Functions támogatja aktiválhatja, és kimeneti Azure várólisták kötései. Elérhető összes kötését lásd [Azure Functions eseményindítók és kötések fogalmak](functions-triggers-bindings.md).

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

<a name="trigger"></a>

## <a name="queue-storage-trigger"></a>Várólista tárolási eseményindító
Az Azure Queue storage eseményindító lehetővé teszi egy új üzenetek a queue storage figyelése, és reagálni azokra. 

Adja meg a várólista eseményindító használatával a **integráció** a Functions portálra a lap. A portál létrehoz egyet a következő definícióját a **kötések** szakasza *function.json*:

```json
{
    "type": "queueTrigger",
    "direction": "in",
    "name": "<The name used to identify the trigger data in your code>",
    "queueName": "<Name of queue to poll>",
    "connection":"<Name of app setting - see below>"
}
```

* A `connection` tulajdonságának tartalmaznia kell egy olyan alkalmazás-beállítást, amely tartalmazza a tárolási kapcsolati karakterlánc nevét. Az Azure portálon, a szokásos szerkesztő a **integráció** lapon konfigurálja az Alkalmazásbeállítás meg, amikor kiválaszt egy tárfiókot.

További beállításokat lehet megadni a [host.json fájl](https://github.com/Azure/azure-webjobs-sdk-script/wiki/host.json) tárolási eseményindítók tovább szűkítheti. Például a lekérdezési időköz a host.json várólista módosíthatja.

<a name="triggerusage"></a>

## <a name="using-a-queue-trigger"></a>A várólista eseményindító használatával
Node.js funkciók, nyissa meg a várólista adatok használatával `context.bindings.<name>`.


.NET-es függvényeket, nyissa meg a várólista forgalma, mint egy metódus paraméter használatával `CloudQueueMessage paramName`. Itt `paramName` az Ön által megadott érték a [eseményindító konfigurációs](#trigger). Az üzenetsorban lévő üzenetet is deszerializálható a következő típusok egyikének sem:

* POCO objektum. Ha a várólista-forgalma egy JSON-objektum használatát. A Functions futtatókörnyezete deserializes a tartalom a POCO objektumba. 
* `string`
* `byte[]`
* [`CloudQueueMessage`]

<a name="meta"></a>

### <a name="queue-trigger-metadata"></a>Várólista eseményindító metaadatok
A várólista eseményindító biztosít több metaadat-tulajdonságot. Ezeket a tulajdonságokat meg más kötésekben kötési kifejezés részeként vagy a kód paramétereiben használható. Az értékek rendelkezik a azonos szemantikákkal, [ `CloudQueueMessage` ].

* **QueueTrigger** -várólista forgalma (Ha egy érvényes karakterláncot)
* **DequeueCount** -típus `int`. Ez az üzenet várólistából nincs kivéve. eléréseinek a száma.
* **ExpirationTime** -típus `DateTimeOffset?`. Az üzenet lejárati ideje.
* **Azonosító** -típus `string`. Várólista azonosító.
* **InsertionTime** -típus `DateTimeOffset?`. Az üzenet a várólistához lett adva időpontja.
* **NextVisibleTime** -típus `DateTimeOffset?`. Az üzenet mellett látható lesz ideje.
* **PopReceipt** -típus `string`. A pop visszaigazolás.

A várólista metaadatok használata [eseményindító minta](#triggersample).

<a name="triggersample"></a>

## <a name="trigger-sample"></a>Eseményindító minta
Tegyük fel, a következő function.json, amely meghatározza egy várólista eseményindító:

```json
{
    "disabled": false,
    "bindings": [
        {
            "type": "queueTrigger",
            "direction": "in",
            "name": "myQueueItem",
            "queueName": "myqueue-items",
            "connection":"MyStorageConnectionString"
        }
    ]
}
```

Tekintse meg a nyelvspecifikus mintát, amely kéri le, és a naplók várólistára metaadatok.

* [C#](#triggercsharp)
* [Node.js](#triggernodejs)

<a name="triggercsharp"></a>

### <a name="trigger-sample-in-c"></a>A C# eseményindító minta #
```csharp
#r "Microsoft.WindowsAzure.Storage"

using Microsoft.WindowsAzure.Storage.Queue;
using System;

public static void Run(CloudQueueMessage myQueueItem, 
    DateTimeOffset expirationTime, 
    DateTimeOffset insertionTime, 
    DateTimeOffset nextVisibleTime,
    string queueTrigger,
    string id,
    string popReceipt,
    int dequeueCount,
    TraceWriter log)
{
    log.Info($"C# Queue trigger function processed: {myQueueItem.AsString}\n" +
        $"queueTrigger={queueTrigger}\n" +
        $"expirationTime={expirationTime}\n" +
        $"insertionTime={insertionTime}\n" +
        $"nextVisibleTime={nextVisibleTime}\n" +
        $"id={id}\n" +
        $"popReceipt={popReceipt}\n" + 
        $"dequeueCount={dequeueCount}");
}
```

<!--
<a name="triggerfsharp"></a>
### Trigger sample in F# ## 
```fsharp

```
-->

<a name="triggernodejs"></a>

### <a name="trigger-sample-in-nodejs"></a>A node.js eseményindító minta

```javascript
module.exports = function (context) {
    context.log('Node.js queue trigger function processed work item', context.bindings.myQueueItem);
    context.log('queueTrigger =', context.bindingData.queueTrigger);
    context.log('expirationTime =', context.bindingData.expirationTime);
    context.log('insertionTime =', context.bindingData.insertionTime);
    context.log('nextVisibleTime =', context.bindingData.nextVisibleTime);
    context.log('id=', context.bindingData.id);
    context.log('popReceipt =', context.bindingData.popReceipt);
    context.log('dequeueCount =', context.bindingData.dequeueCount);
    context.done();
};
```

### <a name="handling-poison-queue-messages"></a>Az elhalt üzenetek kezelése
A várólista funkció sikertelensége esetén az Azure Functions újrapróbálkozik a függvény legfeljebb ötször egy adott várólistában üzenet, beleértve az első próbálkozás. Ha bármilyen öt kísérlet sikertelen, a functions futtatókörnyezete ad hozzá egy üzenet nevű várólista tárolási  *&lt;originalqueuename >-poison*. Írhat egy folyamat üzenetek működnek, mint az elhalt üzenetsorból naplózásukhoz, vagy értesítést küld, hogy manuális beavatkozást van szükség. 

Az elhalt üzenetek manuálisan kezeléséhez, ellenőrizze a `dequeueCount` a várólista-üzenet (lásd: [várólista eseményindító metaadatok](#meta)).

<a name="output"></a>

## <a name="queue-storage-output-binding"></a>A Queue storage kimeneti kötése
Az Azure üzenetsor-kezelési tárolási kimeneti kötés lehetővé teszi üzenetek írását annak a várólistára. 

Adja meg a várólista kimeneti kötés segítségével a **integráció** a Functions portálra a lap. A portál létrehoz egyet a következő definícióját a **kötések** szakasza *function.json*:

```json
{
   "type": "queue",
   "direction": "out",
   "name": "<The name used to identify the trigger data in your code>",
   "queueName": "<Name of queue to write to>",
   "connection":"<Name of app setting - see below>"
}
```

* A `connection` tulajdonságának tartalmaznia kell egy olyan alkalmazás-beállítást, amely tartalmazza a tárolási kapcsolati karakterlánc nevét. Az Azure portálon, a szokásos szerkesztő a **integráció** lapon konfigurálja az Alkalmazásbeállítás meg, amikor kiválaszt egy tárfiókot.

<a name="outputusage"></a>

## <a name="using-a-queue-output-binding"></a>Kimeneti várólista alapján kötése
A Node.js funkciók segítségével érhető el a kimeneti várólista `context.bindings.<name>`.

A .NET-es függvényeket a kimenetre küldheti a következő típusok egyikének sem. A típusparaméter esetén `T`, `T` kell lennie a támogatott kimeneti típusú, mint például `string` vagy egy POCO.

* `out T`(JSON-ként szerializált)
* `out string`
* `out byte[]`
* `out` [`CloudQueueMessage`] 
* `ICollector<T>`
* `IAsyncCollector<T>`
* [`CloudQueue`](/dotnet/api/microsoft.windowsazure.storage.queue.cloudqueue)

A metódus visszatérési típusának a kimeneti kötés is használhatja.

<a name="outputsample"></a>

## <a name="queue-output-sample"></a>Várólista kimeneti minta
A következő *function.json* definiál egy HTTP-eseményindítóval üzenetsorokat kimeneti kötése:

```json
{
  "bindings": [
    {
      "type": "httpTrigger",
      "direction": "in",
      "authLevel": "function",
      "name": "input"
    },
    {
      "type": "http",
      "direction": "out",
      "name": "return"
    },
    {
      "type": "queue",
      "direction": "out",
      "name": "$return",
      "queueName": "outqueue",
      "connection": "MyStorageConnectionString",
    }
  ]
}
``` 

Tekintse meg a nyelvspecifikus mintát, amely a bejövő HTTP-forgalma várólista üzenetbe.

* [C#](#outcsharp)
* [Node.js](#outnodejs)

<a name="outcsharp"></a>

### <a name="queue-output-sample-in-c"></a>Várólista kimeneti minta C# #

```cs
// C# example of HTTP trigger binding to a custom POCO, with a queue output binding
public class CustomQueueMessage
{
    public string PersonName { get; set; }
    public string Title { get; set; }
}

public static CustomQueueMessage Run(CustomQueueMessage input, TraceWriter log)
{
    return input;
}
```

Több üzenetet küldeni, használja az `ICollector`:

```cs
public static void Run(CustomQueueMessage input, ICollector<CustomQueueMessage> myQueueItem, TraceWriter log)
{
    myQueueItem.Add(input);
    myQueueItem.Add(new CustomQueueMessage { PersonName = "You", Title = "None" });
}
```

<a name="outnodejs"></a>

### <a name="queue-output-sample-in-nodejs"></a>Kimeneti várólista minta Node.js

```javascript
module.exports = function (context, input) {
    context.done(null, input.body);
};
```

Vagy több üzenetet küldeni.

```javascript
module.exports = function(context) {
    // Define a message array for the myQueueItem output binding. 
    context.bindings.myQueueItem = ["message 1","message 2"];
    context.done();
};
```

## <a name="next-steps"></a>Következő lépések

Egy függvény által használt tárolási eseményindítók és kötések példáért lásd: [hozzon létre egy Azure Queue storage által indított függvényt](functions-create-storage-queue-triggered-function.md).

[!INCLUDE [next steps](../../includes/functions-bindings-next-steps.md)]

<!-- LINKS -->

["CloudQueueMessage"]: /dotnet/api/microsoft.windowsazure.storage.queue.cloudqueuemessage
