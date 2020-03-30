---
title: Azure Event Grid kimeneti kötés az Azure Functionshez
description: Ismerje meg, hogyan küldhet event grid-eseményt az Azure Functions szolgáltatásban.
author: craigshoemaker
ms.topic: reference
ms.date: 02/14/2020
ms.author: cshoe
ms.custom: fasttrack-edit
ms.openlocfilehash: e7a2611312ffc33703dd5cc9d0a2d7142ddb0532
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77368947"
---
# <a name="azure-event-grid-output-binding-for-azure-functions"></a>Azure Event Grid kimeneti kötés az Azure Functionshez

Az Event Grid kimeneti kötés használatával eseményeket írhat egy egyéni témakörbe. Az egyéni témakörhöz érvényes [hozzáférési kulcsszükséges.](../event-grid/security-authentication.md#custom-topic-publishing)

A beállítással és a konfigurációval kapcsolatos részletekről az [áttekintésben](./functions-bindings-event-grid.md)olvashat.

> [!NOTE]
> Az Event Grid kimeneti kötés nem támogatja a megosztott hozzáférési aláírások (SAS-jogkivonatok). A témakör hozzáférési kulcsát kell használnia.

> [!IMPORTANT]
> Az Event Grid kimeneti kötés csak a Functions 2.x és újabb függvényekhez érhető el.

## <a name="example"></a>Példa

# <a name="c"></a>[C #](#tab/csharp)

A következő példa egy [C# függvényt](functions-dotnet-class-library.md) mutat be, amely üzenetet ír egy Event Grid egyéni témakörbe, a metódus visszatérési értékét használva kimenetként:

```csharp
[FunctionName("EventGridOutput")]
[return: EventGrid(TopicEndpointUri = "MyEventGridTopicUriSetting", TopicKeySetting = "MyEventGridTopicKeySetting")]
public static EventGridEvent Run([TimerTrigger("0 */5 * * * *")] TimerInfo myTimer, ILogger log)
{
    return new EventGridEvent("message-id", "subject-name", "event-data", "event-type", DateTime.UtcNow, "1.0");
}
```

A következő példa bemutatja, hogyan használhatja a `IAsyncCollector` felületet egy köteg üzenet küldésére.

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

# <a name="c-script"></a>[C# parancsfájl](#tab/csharp-script)

A következő példa az Event Grid kimeneti kötési adatait mutatja be a *function.json* fájlban.

```json
{
    "type": "eventGrid",
    "name": "outputEvent",
    "topicEndpointUri": "MyEventGridTopicUriSetting",
    "topicKeySetting": "MyEventGridTopicKeySetting",
    "direction": "out"
}
```

Itt a C# parancsfájlkód, amely létrehoz egy eseményt:

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

Itt a C# parancsfájlkód, amely több eseményt hoz létre:

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

# <a name="javascript"></a>[Javascript](#tab/javascript)

A következő példa az Event Grid kimeneti kötési adatait mutatja be a *function.json* fájlban.

```json
{
    "type": "eventGrid",
    "name": "outputEvent",
    "topicEndpointUri": "MyEventGridTopicUriSetting",
    "topicKeySetting": "MyEventGridTopicKeySetting",
    "direction": "out"
}
```

Itt van a JavaScript-kód, amely egyetlen eseményt hoz létre:

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

Itt van a JavaScript-kód, amely több eseményt hoz létre:

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

Az Event Grid kimeneti kötés nem érhető el a Python.

# <a name="java"></a>[Java](#tab/java)

Az Event Grid kimeneti kötése java esetén nem érhető el.

---

## <a name="attributes-and-annotations"></a>Attribútumok és jegyzetek

# <a name="c"></a>[C #](#tab/csharp)

[C# osztálytárak](functions-dotnet-class-library.md)esetén használja az [EventGridAttribute](https://github.com/Azure/azure-functions-eventgrid-extension/blob/dev/src/EventGridExtension/OutputBinding/EventGridAttribute.cs) attribútumot.

Az attribútum konstruktora az egyéni témakör nevét tartalmazó alkalmazásbeállítás nevét és a témakörkulcsot tartalmazó alkalmazásbeállítás nevét veszi fel. Ezekről a beállításokról további információt a [Kimenet - konfiguráció](#configuration)című témakörben talál. Íme egy `EventGrid` attribútum példa:

```csharp
[FunctionName("EventGridOutput")]
[return: EventGrid(TopicEndpointUri = "MyEventGridTopicUriSetting", TopicKeySetting = "MyEventGridTopicKeySetting")]
public static string Run([TimerTrigger("0 */5 * * * *")] TimerInfo myTimer, ILogger log)
{
    ...
}
```

A teljes példát lásd a [példában.](#example)

# <a name="c-script"></a>[C# parancsfájl](#tab/csharp-script)

Az attribútumokat a C# script nem támogatja.

# <a name="javascript"></a>[Javascript](#tab/javascript)

A JavaScript nem támogatja az attribútumokat.

# <a name="python"></a>[Python](#tab/python)

Az Event Grid kimeneti kötés nem érhető el a Python.

# <a name="java"></a>[Java](#tab/java)

Az Event Grid kimeneti kötése java esetén nem érhető el.

---

## <a name="configuration"></a>Konfiguráció

Az alábbi táblázat a *function.json* fájlban és az `EventGrid` attribútumban beállított kötési konfigurációs tulajdonságokat ismerteti.

|function.json tulajdonság | Attribútum tulajdonság |Leírás|
|---------|---------|----------------------|
|**Típus** | n/a | Az "eventGrid" beállításra kell beállítva. |
|**direction** | n/a | Be kell állítani, hogy "ki". Ez a paraméter automatikusan be van állítva, amikor létrehozza a kötést az Azure Portalon. |
|**név** | n/a | Az eseményt jelképező függvénykódban használt változónév. |
|**topicEndpointUri** |**TopicEndpointUri** | Az egyéni témakör URI-ját tartalmazó alkalmazásbeállítás neve, `MyTopicEndpointUri`például . |
|**topicKeySetting** |**TopicKeySetting (Témakulcsbeállítása)** | Az egyéni témakör hozzáférési kulcsát tartalmazó alkalmazásbeállítás neve. |

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

> [!IMPORTANT]
> Győződjön meg arról, `TopicEndpointUri` hogy a konfigurációs tulajdonság értékét az egyéni témakör URI-ját tartalmazó alkalmazásbeállítás nevére állította be. Ne adja meg közvetlenül ebben a tulajdonságban az egyéni témakör URI-ját.

## <a name="usage"></a>Használat

# <a name="c"></a>[C #](#tab/csharp)

Üzenetek küldése metódusparaméter használatával, `out EventGridEvent paramName`például . Több üzenet írásához használhatja `IAsyncCollector<EventGridEvent>` vagy `out EventGridEvent`helyette a ot `ICollector<EventGridEvent>`

# <a name="c-script"></a>[C# parancsfájl](#tab/csharp-script)

Üzenetek küldése metódusparaméter használatával, `out EventGridEvent paramName`például . A C# `paramName` parancsfájlban a `name` *function.json*tulajdonságában megadott érték. Több üzenet írásához használhatja `IAsyncCollector<EventGridEvent>` vagy `out EventGridEvent`helyette a ot `ICollector<EventGridEvent>`

# <a name="javascript"></a>[Javascript](#tab/javascript)

A kimeneti esemény `context.bindings.<name>` `<name>` elérése a `name` *function.json*tulajdonságában megadott érték használatával.

# <a name="python"></a>[Python](#tab/python)

Az Event Grid kimeneti kötés nem érhető el a Python.

# <a name="java"></a>[Java](#tab/java)

Az Event Grid kimeneti kötése java esetén nem érhető el.

---

## <a name="next-steps"></a>További lépések

* [Eseményrács-esemény feladása](./functions-bindings-event-grid-trigger.md)
