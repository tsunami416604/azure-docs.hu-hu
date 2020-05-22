---
title: Azure Functions Azure Event Grid kimeneti kötése
description: Megtudhatja, hogyan küldhet Event Grid eseményt Azure Functions.
author: craigshoemaker
ms.topic: reference
ms.date: 02/14/2020
ms.author: cshoe
ms.custom: fasttrack-edit
ms.openlocfilehash: 4df0faf3f74ef3423dcd42c2c76af8b39a889a92
ms.sourcegitcommit: 318d1bafa70510ea6cdcfa1c3d698b843385c0f6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/21/2020
ms.locfileid: "83773952"
---
# <a name="azure-event-grid-output-binding-for-azure-functions"></a>Azure Functions Azure Event Grid kimeneti kötése

A Event Grid kimeneti kötés használatával írhat eseményeket egyéni témakörbe. [Az egyéni témakörhöz érvényes hozzáférési kulccsal](../event-grid/security-authentication.md#authenticate-publishing-clients-using-sas-or-key)kell rendelkeznie.

További információ a telepítésről és a konfigurációról: [Áttekintés](./functions-bindings-event-grid.md).

> [!NOTE]
> A Event Grid kimeneti kötés nem támogatja a közös hozzáférésű aláírásokat (SAS-tokeneket). A témakör elérési kulcsát kell használnia.

> [!IMPORTANT]
> A Event Grid kimeneti kötés csak a 2. x és újabb függvények esetében érhető el.

## <a name="example"></a>Példa

# <a name="c"></a>[C #](#tab/csharp)

Az alábbi példa egy [C#-függvényt](functions-dotnet-class-library.md) mutat be, amely egy Event Grid egyéni témakörbe ír egy üzenetet, amely a metódus visszatérési értékét használja kimenetként:

```csharp
[FunctionName("EventGridOutput")]
[return: EventGrid(TopicEndpointUri = "MyEventGridTopicUriSetting", TopicKeySetting = "MyEventGridTopicKeySetting")]
public static EventGridEvent Run([TimerTrigger("0 */5 * * * *")] TimerInfo myTimer, ILogger log)
{
    return new EventGridEvent("message-id", "subject-name", "event-data", "event-type", DateTime.UtcNow, "1.0");
}
```

Az alábbi példa azt szemlélteti, hogyan használható az `IAsyncCollector` interfész egy köteg üzenetek küldésére.

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

# <a name="c-script"></a>[C#-parancsfájl](#tab/csharp-script)

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

Az alábbi C#-kód egy eseményt hoz létre:

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

A következő C#-kód több eseményt hoz létre:

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

# <a name="javascript"></a>[JavaScript](#tab/javascript)

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

# <a name="python"></a>[Python](#tab/python)

Az alábbi példa egy trigger-kötést mutat be egy *function. JSON* fájlban, valamint egy olyan [Python-függvényt](functions-reference-python.md) , amely a kötést használja. Ezután egy eseményben küldi el az egyéni Event Grid témakört, amelyet a következő ad meg: `topicEndpointUri` .

Itt található a *function. JSON* fájlban található kötési adat:

```json
{
  "scriptFile": "__init__.py",
  "bindings": [
    {
      "type": "eventGridTrigger",
      "name": "eventGridEvent",
      "direction": "in"
    },
    {
      "type": "eventGrid",
      "name": "outputEvent",
      "topicEndpointUri": "MyEventGridTopicUriSetting",
      "topicKeySetting": "MyEventGridTopicKeySetting",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

Itt látható a Python-minta, amely egy eseményt küld egy egyéni Event Grid témakörre a következő beállításával `EventGridOutputEvent` :

```python
import logging
import azure.functions as func
import datetime


def main(eventGridEvent: func.EventGridEvent, 
         outputEvent: func.Out[func.EventGridOutputEvent]) -> None:

    logging.log("eventGridEvent: ", eventGridEvent)

    outputEvent.set(
        func.EventGridOutputEvent(
            id="test-id",
            data={"tag1": "value1", "tag2": "value2"},
            subject="test-subject",
            event_type="test-event-1",
            event_time=datetime.datetime.utcnow(),
            data_version="1.0"))
```

# <a name="java"></a>[Java](#tab/java)

A Event Grid kimeneti kötés nem érhető el a Javához.

---

## <a name="attributes-and-annotations"></a>Attribútumok és jegyzetek

# <a name="c"></a>[C #](#tab/csharp)

[C# szintű kódtárak](functions-dotnet-class-library.md)esetén használja a [EventGridAttribute](https://github.com/Azure/azure-functions-eventgrid-extension/blob/dev/src/EventGridExtension/OutputBinding/EventGridAttribute.cs) attribútumot.

Az attribútum konstruktora egy olyan Alkalmazásbeállítás nevét adja meg, amely az egyéni témakör nevét és egy, a témakör kulcsát tartalmazó Alkalmazásbeállítás nevét tartalmazza. További információ ezekről a beállításokról: [kimeneti konfiguráció](#configuration). `EventGrid`Példa egy attribútumra:

```csharp
[FunctionName("EventGridOutput")]
[return: EventGrid(TopicEndpointUri = "MyEventGridTopicUriSetting", TopicKeySetting = "MyEventGridTopicKeySetting")]
public static string Run([TimerTrigger("0 */5 * * * *")] TimerInfo myTimer, ILogger log)
{
    ...
}
```

Teljes példa: [példa](#example).

# <a name="c-script"></a>[C#-parancsfájl](#tab/csharp-script)

A C# parancsfájl nem támogatja az attribútumokat.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

A JavaScript nem támogatja az attribútumokat.

# <a name="python"></a>[Python](#tab/python)

A Event Grid kimeneti kötés nem érhető el a Pythonhoz.

# <a name="java"></a>[Java](#tab/java)

A Event Grid kimeneti kötés nem érhető el a Javához.

---

## <a name="configuration"></a>Konfiguráció

Az alábbi táblázat a *function. JSON* fájlban és az attribútumban beállított kötési konfigurációs tulajdonságokat ismerteti `EventGrid` .

|function. JSON-tulajdonság | Attribútum tulajdonsága |Leírás|
|---------|---------|----------------------|
|**típusa** | n.a. | "EventGrid" értékre kell állítani. |
|**direction** | n.a. | "Out" értékre kell állítani. Ez a paraméter automatikusan be van állítva, amikor létrehozza a kötést a Azure Portalban. |
|**name** | n.a. | Az eseményt jelölő függvény kódjában használt változó neve. |
|**topicEndpointUri** |**TopicEndpointUri** | Az egyéni témakör URI azonosítóját tartalmazó Alkalmazásbeállítás neve, például: `MyTopicEndpointUri` . |
|**topicKeySetting** |**TopicKeySetting** | Az egyéni témakör elérési kulcsát tartalmazó Alkalmazásbeállítás neve. |

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

> [!IMPORTANT]
> Győződjön meg arról, hogy a Configuration ( `TopicEndpointUri` konfiguráció) tulajdonság értékét egy olyan Alkalmazásbeállítás nevére állítja be, amely az egyéni témakör URI-ját tartalmazza. Ne határozza meg az egyéni témakör URI-JÁT közvetlenül ebben a tulajdonságban.

## <a name="usage"></a>Használat

# <a name="c"></a>[C #](#tab/csharp)

Üzenetek küldése a metódus paraméterének használatával, például: `out EventGridEvent paramName` . Több üzenet írásához használhatja a `ICollector<EventGridEvent>` vagy `IAsyncCollector<EventGridEvent>` a helyét `out EventGridEvent` .

# <a name="c-script"></a>[C#-parancsfájl](#tab/csharp-script)

Üzenetek küldése a metódus paraméterének használatával, például: `out EventGridEvent paramName` . A C#-szkriptben `paramName` a `name` *function. JSON*tulajdonságban megadott érték. Több üzenet írásához használhatja a `ICollector<EventGridEvent>` vagy `IAsyncCollector<EventGridEvent>` a helyét `out EventGridEvent` .

# <a name="javascript"></a>[JavaScript](#tab/javascript)

A kimeneti eseményt a `context.bindings.<name>` `<name>` `name` *függvény. JSON*tulajdonságában megadott érték használatával érheti el.

# <a name="python"></a>[Python](#tab/python)

A Event Grid kimeneti kötés nem érhető el a Pythonhoz.

# <a name="java"></a>[Java](#tab/java)

A Event Grid kimeneti kötés nem érhető el a Javához.

---

## <a name="next-steps"></a>További lépések

* [Event Grid esemény elküldése](./functions-bindings-event-grid-trigger.md)
