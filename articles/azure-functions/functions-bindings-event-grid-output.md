---
title: Azure Functions Azure Event Grid kimeneti kötése
description: Megtudhatja, hogyan küldhet Event Grid eseményt Azure Functions.
author: craigshoemaker
ms.topic: reference
ms.date: 02/14/2020
ms.author: cshoe
ms.custom: fasttrack-edit
ms.openlocfilehash: e7a2611312ffc33703dd5cc9d0a2d7142ddb0532
ms.sourcegitcommit: f97f086936f2c53f439e12ccace066fca53e8dc3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/15/2020
ms.locfileid: "77368947"
---
# <a name="azure-event-grid-output-binding-for-azure-functions"></a>Azure Functions Azure Event Grid kimeneti kötése

A Event Grid kimeneti kötés használatával írhat eseményeket egyéni témakörbe. [Az egyéni témakörhöz érvényes hozzáférési kulccsal](../event-grid/security-authentication.md#custom-topic-publishing)kell rendelkeznie.

További információ a telepítésről és a konfigurációról: [Áttekintés](./functions-bindings-event-grid.md).

> [!NOTE]
> A Event Grid kimeneti kötés nem támogatja a közös hozzáférésű aláírásokat (SAS-tokeneket). A témakör elérési kulcsát kell használnia.

> [!IMPORTANT]
> A Event Grid kimeneti kötés csak a 2. x és újabb függvények esetében érhető el.

## <a name="example"></a>Példa

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

Az alábbi példa egy olyan [ C# függvényt](functions-dotnet-class-library.md) mutat be, amely egy Event Grid egyéni témakörbe ír egy üzenetet, amely a metódus visszatérési értékét használja kimenetként:

```csharp
[FunctionName("EventGridOutput")]
[return: EventGrid(TopicEndpointUri = "MyEventGridTopicUriSetting", TopicKeySetting = "MyEventGridTopicKeySetting")]
public static EventGridEvent Run([TimerTrigger("0 */5 * * * *")] TimerInfo myTimer, ILogger log)
{
    return new EventGridEvent("message-id", "subject-name", "event-data", "event-type", DateTime.UtcNow, "1.0");
}
```

Az alábbi példa azt szemlélteti, hogyan lehet a `IAsyncCollector` felületen elküldeni egy köteg üzenetet.

```csharp
[FunctionName("EventGridAsyncOutput")]
public static async Task Run(
    [TimerTrigger("0 */5 * * * *")] TimerInfo myTimer,
    [EventGrid(TopicEndpointUri = "MyEventGridTopicUriSetting", TopicKeySetting = "MyEventGridTopicKeySetting")]IAsyncCollector<EventGridEvent> outputEvents,
    ILogger log)
{
    for (var i = 0; i < 3; i++)
    {
        var myEvent = new EventGridEvent("message-id-" + i, "subject-name", "event-data", "event-type", DateTime.UtcNow, "1.0");
        await outputEvents.AddAsync(myEvent);
    }
}
```

# <a name="c-scripttabcsharp-script"></a>[C#Parancsfájl](#tab/csharp-script)

Az alábbi példa a *function. JSON* fájlban lévő Event Grid kimeneti kötési adatokat mutatja be.

```json
{
    "type": "eventGrid",
    "name": "outputEvent",
    "topicEndpointUri": "MyEventGridTopicUriSetting",
    "topicKeySetting": "MyEventGridTopicKeySetting",
    "direction": "out"
}
```

Az alábbi C# szkript kód egy eseményt hoz létre:

```cs
#r "Microsoft.Azure.EventGrid"
using System;
using Microsoft.Azure.EventGrid.Models;
using Microsoft.Extensions.Logging;

public static void Run(TimerInfo myTimer, out EventGridEvent outputEvent, ILogger log)
{
    outputEvent = new EventGridEvent("message-id", "subject-name", "event-data", "event-type", DateTime.UtcNow, "1.0");
}
```

Az alábbi C# parancsfájl-kód több eseményt hoz létre:

```cs
#r "Microsoft.Azure.EventGrid"
using System;
using Microsoft.Azure.EventGrid.Models;
using Microsoft.Extensions.Logging;

public static void Run(TimerInfo myTimer, ICollector<EventGridEvent> outputEvent, ILogger log)
{
    outputEvent.Add(new EventGridEvent("message-id-1", "subject-name", "event-data", "event-type", DateTime.UtcNow, "1.0"));
    outputEvent.Add(new EventGridEvent("message-id-2", "subject-name", "event-data", "event-type", DateTime.UtcNow, "1.0"));
}
```

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

Az alábbi példa a *function. JSON* fájlban lévő Event Grid kimeneti kötési adatokat mutatja be.

```json
{
    "type": "eventGrid",
    "name": "outputEvent",
    "topicEndpointUri": "MyEventGridTopicUriSetting",
    "topicKeySetting": "MyEventGridTopicKeySetting",
    "direction": "out"
}
```

Az alábbi JavaScript-kód egyetlen eseményt hoz létre:

```javascript
module.exports = async function (context, myTimer) {
    var timeStamp = new Date().toISOString();

    context.bindings.outputEvent = {
        id: 'message-id',
        subject: 'subject-name',
        dataVersion: '1.0',
        eventType: 'event-type',
        data: "event-data",
        eventTime: timeStamp
    };
    context.done();
};
```

Az alábbi JavaScript-kód több eseményt hoz létre:

```javascript
module.exports = function(context) {
    var timeStamp = new Date().toISOString();

    context.bindings.outputEvent = [];

    context.bindings.outputEvent.push({
        id: 'message-id-1',
        subject: 'subject-name',
        dataVersion: '1.0',
        eventType: 'event-type',
        data: "event-data",
        eventTime: timeStamp
    });
    context.bindings.outputEvent.push({
        id: 'message-id-2',
        subject: 'subject-name',
        dataVersion: '1.0',
        eventType: 'event-type',
        data: "event-data",
        eventTime: timeStamp
    });
    context.done();
};
```

# <a name="pythontabpython"></a>[Python](#tab/python)

A Event Grid kimeneti kötés nem érhető el a Pythonhoz.

# <a name="javatabjava"></a>[Java](#tab/java)

A Event Grid kimeneti kötés nem érhető el a Javához.

---

## <a name="attributes-and-annotations"></a>Attribútumok és jegyzetek

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

Az [EventGridAttribute](https://github.com/Azure/azure-functions-eventgrid-extension/blob/dev/src/EventGridExtension/OutputBinding/EventGridAttribute.cs) attribútumot használja az [ C# osztályok könyvtáraihoz](functions-dotnet-class-library.md).

Az attribútum konstruktora egy olyan Alkalmazásbeállítás nevét adja meg, amely az egyéni témakör nevét és egy, a témakör kulcsát tartalmazó Alkalmazásbeállítás nevét tartalmazza. További információ ezekről a beállításokról: [kimeneti konfiguráció](#configuration). Példa `EventGrid` attribútumra:

```csharp
[FunctionName("EventGridOutput")]
[return: EventGrid(TopicEndpointUri = "MyEventGridTopicUriSetting", TopicKeySetting = "MyEventGridTopicKeySetting")]
public static string Run([TimerTrigger("0 */5 * * * *")] TimerInfo myTimer, ILogger log)
{
    ...
}
```

Teljes példa: [példa](#example).

# <a name="c-scripttabcsharp-script"></a>[C#Parancsfájl](#tab/csharp-script)

Az C# attribútumokat a parancsfájl nem támogatja.

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

A JavaScript nem támogatja az attribútumokat.

# <a name="pythontabpython"></a>[Python](#tab/python)

A Event Grid kimeneti kötés nem érhető el a Pythonhoz.

# <a name="javatabjava"></a>[Java](#tab/java)

A Event Grid kimeneti kötés nem érhető el a Javához.

---

## <a name="configuration"></a>Konfiguráció

Az alábbi táblázat a *function. JSON* fájlban és a `EventGrid` attribútumban beállított kötési konfigurációs tulajdonságokat ismerteti.

|Function.JSON tulajdonság | Attribútum tulajdonsága |Leírás|
|---------|---------|----------------------|
|**type** | n/a | "EventGrid" értékre kell állítani. |
|**direction** | n/a | Állítsa "out". Ez a paraméter automatikusan be van állítva, amikor létrehozza a kötést a Azure Portalban. |
|**név** | n/a | Az eseményt jelölő függvény kódjában használt változó neve. |
|**topicEndpointUri** |**TopicEndpointUri** | Az egyéni témakör URI azonosítóját tartalmazó Alkalmazásbeállítás neve, például `MyTopicEndpointUri`. |
|**topicKeySetting** |**TopicKeySetting** | Az egyéni témakör elérési kulcsát tartalmazó Alkalmazásbeállítás neve. |

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

> [!IMPORTANT]
> Győződjön meg arról, hogy a `TopicEndpointUri` konfiguráció tulajdonság értékét az egyéni témakör URI-JÁT tartalmazó Alkalmazásbeállítás nevére állítja be. Ne határozza meg az egyéni témakör URI-JÁT közvetlenül ebben a tulajdonságban.

## <a name="usage"></a>Használat

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

Üzenetek küldése egy metódus-paraméter (például `out EventGridEvent paramName`) használatával. Több üzenet írásához `ICollector<EventGridEvent>` vagy `IAsyncCollector<EventGridEvent>`t is használhat `out EventGridEvent`helyett.

# <a name="c-scripttabcsharp-script"></a>[C#Parancsfájl](#tab/csharp-script)

Üzenetek küldése egy metódus-paraméter (például `out EventGridEvent paramName`) használatával. A C# szkriptben `paramName` a *function. JSON*`name` tulajdonságában megadott érték. Több üzenet írásához `ICollector<EventGridEvent>` vagy `IAsyncCollector<EventGridEvent>`t is használhat `out EventGridEvent`helyett.

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

A kimeneti eseményt `context.bindings.<name>` használatával érheti el, ahol `<name>` a *function. json*`name` tulajdonságában megadott érték.

# <a name="pythontabpython"></a>[Python](#tab/python)

A Event Grid kimeneti kötés nem érhető el a Pythonhoz.

# <a name="javatabjava"></a>[Java](#tab/java)

A Event Grid kimeneti kötés nem érhető el a Javához.

---

## <a name="next-steps"></a>Következő lépések

* [Event Grid esemény elküldése](./functions-bindings-event-grid-trigger.md)
