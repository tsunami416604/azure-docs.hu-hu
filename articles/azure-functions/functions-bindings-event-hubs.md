---
title: "Az Azure Functions az Azure Event Hubs kötései"
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
ms.date: 11/08/2017
ms.author: wesmc
ms.openlocfilehash: 87a7d25e1095fe1511c86dc56375c02f06f51b73
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/16/2018
---
# <a name="azure-event-hubs-bindings-for-azure-functions"></a>Az Azure Functions az Azure Event Hubs kötései

Ez a cikk azt ismerteti, hogyan használható [Azure Event Hubs](../event-hubs/event-hubs-what-is-event-hubs.md) Azure Functions kötéseit. Az Azure Functions támogatja indítható el, és az Event Hubs kötései kimeneti.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="packages"></a>Csomagok

Az Event Hubs kötések szerepelnek a [Microsoft.Azure.WebJobs.ServiceBus](http://www.nuget.org/packages/Microsoft.Azure.WebJobs.ServiceBus) NuGet-csomagot. A csomag forráskódja van a [azure-webjobs-sdk](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.ServiceBus/EventHubs/) GitHub-tárházban.

[!INCLUDE [functions-package](../../includes/functions-package.md)]

## <a name="trigger"></a>Eseményindító

Az Event Hubs eseményindító segítségével egy event hub eseményfelhasználó küldött esemény válaszolni. Az event hubs az eseményindító beállítása olvasási hozzáféréssel kell rendelkeznie.

Az Event Hubs funkció aktiválása esetén az üzenet, amely elindítja az átad a függvény egy karakterlánc.

## <a name="trigger---scaling"></a>Indítás - skálázás

Minden egyes példánya egy Event Hub-Triggered függvény csak 1 EventProcessorHost (EPH) példány alapját. Az Event Hubs biztosítja, hogy csak az 1 EPH beszerezheti a címbérlet egy adott partíción.

Tegyük fel, hogy az első lépések a következő telepítésével és az Eseményközpontok az Előfeltételek:

1. 10 partíciókat.
1. egyenlően elosztott 1000 események összes partíciójára = > 100 üzenetek az egyes partíciók.

Ha a függvény első engedélyezve van, nincs csak 1 példánya a függvényt. Ez a függvény példány Function_0 most hívja. Function_0 fog rendelkezni, amely kezeli a címbérlet beolvasni minden 10 partíción 1 EPH. Események olvasása partíciók 0-9 fog elindulni. Ettől kezdve a következő történik:

* **A függvény csak 1-példány szükséges** -Function_0 sikerült feldolgozni az összes 1000, mielőtt az Azure Functions méretezési logika lép működésbe,. Emiatt minden 1000 üzenetek Function_0 dolgoznak fel.

* **Adja hozzá a 1 további függvény példány** -Azure Functions méretezési programot határozza meg, hogy Function_0 rendelkezik-e a további üzeneteket, mint amennyi feldolgozására, így létrejön egy új példányát, Function_1,. Az Event Hubs észleli, hogy új EPH példány üzenet olvasása közben. Az Event Hubs fogják terheléselosztásának a partíciókat a EPH példányok, például partíciók 0-4 Function_0 vannak rendelve, és partíciók 5 – 9 Function_1 vannak rendelve. 

* **Adja hozzá N több működéséhez példányok** -Azure Functions méretezési logika határozza meg, hogy az Function_0, és Function_1 is rendelkezik, mint azok képes több üzenetet. Akkor lesz skálázva újra Function_2... N, ahol N nagyobb, mint az Event Hubs partíciók számára. Az Event Hubs betölti a partíciók egyensúlyba Function_0... 9 példányok.

Az Azure Functions jelenleg egyedi méretezés logika arra, hogy N nagyobb, mint a partíciók száma. Erre azért van szükség, győződjön meg arról, hogy mindig példánya van EPH rendelkezésére gyorsan zárolni a partíciót tartalmaz, a többi példánytól elérhetővé válnak. Felhasználók csak van szó, az erőforrások használható, ha a függvény példány végrehajtása során, és nem a túlzott kiosztása felszámított.

Minden függvény végrehajtások sikeres, nem jelenik meg hibaüzenet, ha a kapcsolódó tárfiók ellenőrzőpontokat kerülnek. Mutató ellenőrzése sikeres, ha minden 1000 üzenetek kell soha nem olvasható be újra.

## <a name="trigger---example"></a>Eseményindító – példa

Tekintse meg a nyelvspecifikus példát:

* [C#](#trigger---c-example)
* [C# script (.csx)](#trigger---c-script-example)
* [F#](#trigger---f-example)
* [JavaScript](#trigger---javascript-example)

### <a name="trigger---c-example"></a>Eseményindító - C# – példa

Az alábbi példa mutatja egy [C# függvény](functions-dotnet-class-library.md) , naplózza, hogy az eseményindító hub üzenet törzsét.

```csharp
[FunctionName("EventHubTriggerCSharp")]
public static void Run([EventHubTrigger("samples-workitems", Connection = "EventHubConnectionAppSetting")] string myEventHubMessage, TraceWriter log)
{
    log.Info($"C# Event Hub trigger function processed a message: {myEventHubMessage}");
}
```

Férhetnek hozzá az esemény metaadatok, kötést létrehozni egy [EventData](/dotnet/api/microsoft.servicebus.messaging.eventdata) objektum (igényel a `using` nyilatkozata `Microsoft.ServiceBus.Messaging`).

```csharp
[FunctionName("EventHubTriggerCSharp")]
public static void Run([EventHubTrigger("samples-workitems", Connection = "EventHubConnectionAppSetting")] EventData myEventHubMessage, TraceWriter log)
{
    log.Info($"{Encoding.UTF8.GetString(myEventHubMessage.GetBytes())}");
}
```
Események fogadásához egy kötegben, hogy `string` vagy `EventData` tömb:

```cs
[FunctionName("EventHubTriggerCSharp")]
public static void Run([EventHubTrigger("samples-workitems", Connection = "EventHubConnectionAppSetting")] string[] eventHubMessages, TraceWriter log)
{
    foreach (var message in eventHubMessages)
    {
        log.Info($"C# Event Hub trigger function processed a message: {message}");
    }
}
```

### <a name="trigger---c-script-example"></a>Eseményindító - C# parancsfájl – példa

A következő példa bemutatja a kötelező központi eseményindító egy *function.json* fájlt és egy [C# parancsfájl függvény](functions-reference-csharp.md) , amely a kötés használja. A függvény az üzenettörzs az event hub eseményindító naplózza.

Itt az kötés adatai a *function.json* fájlt:

```json
{
  "type": "eventHubTrigger",
  "name": "myEventHubMessage",
  "direction": "in",
  "path": "MyEventHub",
  "connection": "myEventHubReadConnectionAppSetting"
}
```
A C# parancsfájl kód itt látható:

```cs
using System;

public static void Run(string myEventHubMessage, TraceWriter log)
{
    log.Info($"C# Event Hub trigger function processed a message: {myEventHubMessage}");
}
```

Az esemény-metaadatok eléréséhez kötést létrehozni egy [EventData](/dotnet/api/microsoft.servicebus.messaging.eventdata) objektum (kell egy használni utasítást `Microsoft.ServiceBus.Messaging`).

```cs
#r "Microsoft.ServiceBus"
using System.Text;
using Microsoft.ServiceBus.Messaging;

public static void Run(EventData myEventHubMessage, TraceWriter log)
{
    log.Info($"{Encoding.UTF8.GetString(myEventHubMessage.GetBytes())}");
}
```

Események fogadásához egy kötegben, hogy `string` vagy `EventData` tömb:

```cs
public static void Run(string[] eventHubMessages, TraceWriter log)
{
    foreach (var message in eventHubMessages)
    {
        log.Info($"C# Event Hub trigger function processed a message: {message}");
    }
}
```

### <a name="trigger---f-example"></a>Eseményindító - F # – példa

A következő példa bemutatja a kötelező központi eseményindító egy *function.json* fájlt és egy [F # függvény](functions-reference-fsharp.md) , amely a kötés használja. A függvény az üzenettörzs az event hub eseményindító naplózza.

Itt az kötés adatai a *function.json* fájlt:

```json
{
  "type": "eventHubTrigger",
  "name": "myEventHubMessage",
  "direction": "in",
  "path": "MyEventHub",
  "connection": "myEventHubReadConnectionAppSetting"
}
```

A F # kód itt látható:

```fsharp
let Run(myEventHubMessage: string, log: TraceWriter) =
    log.Info(sprintf "F# eventhub trigger function processed work item: %s" myEventHubMessage)
```

### <a name="trigger---javascript-example"></a>Eseményindító - JavaScript – példa

A következő példa bemutatja a kötelező központi eseményindító egy *function.json* fájlt és egy [JavaScript függvény](functions-reference-node.md) , amely a kötés használja. A függvény az üzenettörzs az event hub eseményindító naplózza.

Itt az kötés adatai a *function.json* fájlt:

```json
{
  "type": "eventHubTrigger",
  "name": "myEventHubMessage",
  "direction": "in",
  "path": "MyEventHub",
  "connection": "myEventHubReadConnectionAppSetting"
}
```

A JavaScript-kód itt látható:

```javascript
module.exports = function (context, myEventHubMessage) {
    context.log('Node.js eventhub trigger function processed work item', myEventHubMessage);    
    context.done();
};
```

## <a name="trigger---attributes"></a>Eseményindító - attribútumok

A [C# osztálykönyvtárakhoz](functions-dotnet-class-library.md), használja a [EventHubTriggerAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.ServiceBus/EventHubs/EventHubTriggerAttribute.cs) attribútum.

Az attribútum konstruktora időt vesz igénybe, az eseményközpont nevét, a felhasználói csoport nevét és a kapcsolati karakterláncot tartalmazó alkalmazásbeállítás neve. A beállításokkal kapcsolatban további információkért lásd: a [indul el, a konfigurációs szakasz](#trigger---configuration). Íme egy `EventHubTriggerAttribute` attribútum példa:

```csharp
[FunctionName("EventHubTriggerCSharp")]
public static void Run([EventHubTrigger("samples-workitems", Connection = "EventHubConnectionAppSetting")] string myEventHubMessage, TraceWriter log)
{
    ...
}
```

Tekintse meg a teljes például [eseményindító - C# példa](#trigger---c-example).

## <a name="trigger---configuration"></a>Eseményindító - konfiguráció

Az alábbi táblázat ismerteti a beállított kötés konfigurációs tulajdonságok a *function.json* fájl és a `EventHubTrigger` attribútum.

|Function.JSON tulajdonság | Attribútum tulajdonsága |Leírás|
|---------|---------|----------------------|
|**Típusa** | n/a | meg kell `eventHubTrigger`. Ez a tulajdonság rendszer automatikusan beállítja az eseményindítót hoz létre az Azure portálon.|
|**direction** | n/a | meg kell `in`. Ez a tulajdonság rendszer automatikusan beállítja az eseményindítót hoz létre az Azure portálon. |
|**name** | n/a | Esemény-elem funkciókódot jelölő neve. | 
|**path** |**EventHubName** | Az event hubs neve. | 
|**ConsumerGroup** |**ConsumerGroup** | Egy nem kötelező tulajdonság, amely beállítja a [fogyasztói csoportot](../event-hubs/event-hubs-features.md#event-consumers) használt események központban előfizetni. Ha nincs megadva, a `$Default` fogyasztói csoportot használja. | 
|**connection** |**Kapcsolat** | A kapcsolati karakterlánc az event hubs névtérhez tartalmazó alkalmazásbeállítás neve. Másolja a kapcsolati karakterláncot kattintva a **kapcsolatadatok** gombra kattint, az a [névtér](../event-hubs/event-hubs-create.md#create-an-event-hubs-namespace), nem magát az eseményközpontba. Ez a kapcsolati karakterlánc kell rendelkeznie legalább olvasási engedéllyel az eseményindítót.|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="trigger---hostjson-properties"></a>Eseményindító - host.json tulajdonságai

A [host.json](functions-host-json.md#eventhub) fájl az Event Hubs eseményindító viselkedését vezérlő beállításokat tartalmaz.

[!INCLUDE [functions-host-json-event-hubs](../../includes/functions-host-json-event-hubs.md)]

## <a name="output"></a>Kimenet

Az Event Hubs kimeneti kötése beírni az eseményeket az eseménystream használja. Események írhat az eseményközpontba a küldési engedéllyel kell rendelkeznie.

## <a name="output---example"></a>Kimeneti – példa

Tekintse meg a nyelvspecifikus példát:

* [C#](#output---c-example)
* [C# script (.csx)](#output---c-script-example)
* [F#](#output---f-example)
* [JavaScript](#output---javascript-example)

### <a name="output---c-example"></a>Kimeneti - C# – példa

Az alábbi példa mutatja egy [C# függvény](functions-dotnet-class-library.md) , amely egy üzenetet ír egy eseményközpontot, a metódus visszatérési értéket használja, mint a kimeneti:

```csharp
[FunctionName("EventHubOutput")]
[return: EventHub("outputEventHubMessage", Connection = "EventHubConnectionAppSetting")]
public static string Run([TimerTrigger("0 */5 * * * *")] TimerInfo myTimer, TraceWriter log)
{
    log.Info($"C# Timer trigger function executed at: {DateTime.Now}");
    return $"{DateTime.Now}";
}
```

### <a name="output---c-script-example"></a>Kimeneti - C# parancsfájl – példa

A következő példa bemutatja a kötelező központi eseményindító egy *function.json* fájlt és egy [C# parancsfájl függvény](functions-reference-csharp.md) , amely a kötés használja. A függvény üzenetet ír egy eseményközpontba.

Itt az kötés adatai a *function.json* fájlt:

```json
{
    "type": "eventHub",
    "name": "outputEventHubMessage",
    "path": "myeventhub",
    "connection": "MyEventHubSendAppSetting",
    "direction": "out"
}
```

C# parancsfájlkód, amely létrehoz egy üzenetet a következő:

```cs
using System;

public static void Run(TimerInfo myTimer, out string outputEventHubMessage, TraceWriter log)
{
    String msg = $"TimerTriggerCSharp1 executed at: {DateTime.Now}";
    log.Verbose(msg);   
    outputEventHubMessage = msg;
}
```

Az alábbiakban C# parancsfájl létrehozó kód mellől több üzenetet:

```cs
public static void Run(TimerInfo myTimer, ICollector<string> outputEventHubMessage, TraceWriter log)
{
    string message = $"Event Hub message created at: {DateTime.Now}";
    log.Info(message);
    outputEventHubMessage.Add("1 " + message);
    outputEventHubMessage.Add("2 " + message);
}
```

### <a name="output---f-example"></a>Kimeneti - F # – példa

A következő példa bemutatja a kötelező központi eseményindító egy *function.json* fájlt és egy [F # függvény](functions-reference-fsharp.md) , amely a kötés használja. A függvény üzenetet ír egy eseményközpontba.

Itt az kötés adatai a *function.json* fájlt:

```json
{
    "type": "eventHub",
    "name": "outputEventHubMessage",
    "path": "myeventhub",
    "connection": "MyEventHubSendAppSetting",
    "direction": "out"
}
```

A F # kód itt látható:

```fsharp
let Run(myTimer: TimerInfo, outputEventHubMessage: byref<string>, log: TraceWriter) =
    let msg = sprintf "TimerTriggerFSharp1 executed at: %s" DateTime.Now.ToString()
    log.Verbose(msg);
    outputEventHubMessage <- msg;
```

### <a name="output---javascript-example"></a>Kimeneti - JavaScript – példa

A következő példa bemutatja a kötelező központi eseményindító egy *function.json* fájlt és egy [JavaScript függvény](functions-reference-node.md) , amely a kötés használja. A függvény üzenetet ír egy eseményközpontba.

Itt az kötés adatai a *function.json* fájlt:

```json
{
    "type": "eventHub",
    "name": "outputEventHubMessage",
    "path": "myeventhub",
    "connection": "MyEventHubSendAppSetting",
    "direction": "out"
}
```

Az alábbiakban JavaScript-kódot, amely egy üzenetet küld:

```javascript
module.exports = function (context, myTimer) {
    var timeStamp = new Date().toISOString();
    context.log('Event Hub message created at: ', timeStamp);   
    context.bindings.outputEventHubMessage = "Event Hub message created at: " + timeStamp;
    context.done();
};
```

Az alábbiakban JavaScript-kódot, amelyet több üzenetet küld:

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

## <a name="output---attributes"></a>Kimeneti - attribútumok

A [C# osztálykönyvtárakhoz](functions-dotnet-class-library.md), használja a [EventHubAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.ServiceBus/EventHubs/EventHubAttribute.cs) attribútum.

Az attribútum konstruktora időt vesz igénybe, az eseményközpont nevét és a kapcsolati karakterláncot tartalmazó Alkalmazásbeállítás nevét. A beállításokkal kapcsolatban további információkért lásd: [kimeneti - konfigurációs](#output---configuration). Íme egy `EventHub` attribútum példa:

```csharp
[FunctionName("EventHubOutput")]
[return: EventHub("outputEventHubMessage", Connection = "EventHubConnectionAppSetting")]
public static string Run([TimerTrigger("0 */5 * * * *")] TimerInfo myTimer, TraceWriter log)
{
    ...
}
```

Tekintse meg a teljes például [kimeneti - C# példa](#output---c-example).

## <a name="output---configuration"></a>Kimeneti - konfiguráció

Az alábbi táblázat ismerteti a beállított kötés konfigurációs tulajdonságok a *function.json* fájl és a `EventHub` attribútum.

|Function.JSON tulajdonság | Attribútum tulajdonsága |Leírás|
|---------|---------|----------------------|
|**Típusa** | n/a | "Az eventHub" értékre kell állítani. |
|**direction** | n/a | "Ki" értékre kell állítani. Ez a paraméter értéke automatikusan kötésének létrehozásakor az Azure portálon. |
|**name** | n/a | A változó nevét, amely jelöli az esemény függvény kódban használt. | 
|**path** |**EventHubName** | Az event hubs neve. | 
|**connection** |**Kapcsolat** | A kapcsolati karakterlánc az event hubs névtérhez tartalmazó alkalmazásbeállítás neve. Másolja a kapcsolati karakterláncot kattintva a **kapcsolatadatok** gombra kattint, az a *névtér*, nem magát az eseményközpontba. Ez a kapcsolati karakterlánc az üzenetet küldeni az eseménystream küldési engedéllyel kell rendelkeznie.|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="output---usage"></a>Kimeneti - használat

A C# és C# a parancsfájlt, használatával üzeneteket metódusparaméter például `out string paramName`. A C# parancsfájl `paramName` érték szerepel a `name` tulajdonsága *function.json*. Több üzenetet írni használható `ICollector<string>` vagy `IAsyncCollector<string>` helyett `out string`.

A JavaScript, hozzáférhet a kimeneti esemény a `context.bindings.<name>`. `<name>` az érték szerepel a `name` tulajdonsága *function.json*.

## <a name="exceptions-and-return-codes"></a>Kivételeket és a visszatérési kódok

| Kötelező | Leírások |
|---|---|
| Eseményközpont | [Üzemeltetési útmutató](https://docs.microsoft.com/rest/api/eventhub/publisher-policy-operations) |

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [További tudnivalók az Azure functions eseményindítók és kötések](functions-triggers-bindings.md)
