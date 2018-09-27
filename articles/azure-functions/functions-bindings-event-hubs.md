---
title: Az Azure Event Hubs-kötések az Azure Functions szolgáltatáshoz
description: Megtudhatja, hogyan használhatja az Azure Event Hubs kötéseit az Azure Functions szolgáltatásban.
services: functions
documentationcenter: na
author: ggailey777
manager: jeconnoc
keywords: az Azure functions, függvények, eseményfeldolgozás, dinamikus számítás, kiszolgáló nélküli architektúra
ms.assetid: daf81798-7acc-419a-bc32-b5a41c6db56b
ms.service: azure-functions
ms.devlang: multiple
ms.topic: reference
ms.date: 11/08/2017
ms.author: glenga
ms.openlocfilehash: def921a5378e85caadfaebdd06fc61dba2661756
ms.sourcegitcommit: ad08b2db50d63c8f550575d2e7bb9a0852efb12f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/26/2018
ms.locfileid: "47223087"
---
# <a name="azure-event-hubs-bindings-for-azure-functions"></a>Az Azure Event Hubs-kötések az Azure Functions szolgáltatáshoz

Ez a cikk azt ismerteti, hogyan használható a [Azure Event Hubs](../event-hubs/event-hubs-what-is-event-hubs.md) Azure Functions kötések. Az Azure Functions támogatja a-trigger, és a kimeneti az Event Hubs kötéseit.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="packages---functions-1x"></a>Csomagok – 1.x függvények

Az Azure Functions-verzió 1.x, az Event Hubs kötéseit találhatók a [Microsoft.Azure.WebJobs.ServiceBus](http://www.nuget.org/packages/Microsoft.Azure.WebJobs.ServiceBus) NuGet-csomag verziója 2.x.
A csomag forráskódja a [azure-webjobs-sdk](https://github.com/Azure/azure-webjobs-sdk/tree/v2.x/src/Microsoft.Azure.WebJobs.ServiceBus/EventHubs) GitHub-adattárban.


[!INCLUDE [functions-package](../../includes/functions-package.md)]

## <a name="packages---functions-2x"></a>Csomagok – 2.x függvények

A Functions 2.x, használja a [Microsoft.Azure.WebJobs.Extensions.EventHubs](http://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.EventHubs) csomag, a verzió 3.x.
A csomag forráskódja a [azure-webjobs-sdk](https://github.com/Azure/azure-webjobs-sdk/tree/master/src/Microsoft.Azure.WebJobs.Extensions.EventHubs) GitHub-adattárban.

[!INCLUDE [functions-package-v2](../../includes/functions-package-v2.md)]

## <a name="trigger"></a>Eseményindító

Az Event Hubs-eseményindító használatával egy event hub eseménystream küldött esemény válaszolni. Az event hubs, az eseményindító beállítása olvasási hozzáféréssel kell rendelkeznie.

Egy Event Hubs által aktivált függvény aktiválódik, ha az üzenet, amely az aktiválásakor átad a függvény karakterláncként.

## <a name="trigger---scaling"></a>Trigger - méretezés

Minden példánya egy event hub által aktivált függvény csak egy biztonsági [EventProcessorHost](https://docs.microsoft.com/dotnet/api/microsoft.azure.eventhubs.processor) példány. Az Event Hubs biztosítja, hogy csak egy [EventProcessorHost](https://docs.microsoft.com/dotnet/api/microsoft.azure.eventhubs.processor) példány beszerezheti a címbérletet egy adott partíción.

Vegyük példaként egy Eseményközpontba módon:

* 10 partíciókat.
* 1000 esemény oszlanak el minden, az egyes partíciók 100 üzenetekkel.

Ha a függvény első engedélyezve van, nincs a függvény csak egy példánya. Adjuk a függvény példány `Function_0`. `Function_0` rendelkezik egy [EventProcessorHost](https://docs.microsoft.com/dotnet/api/microsoft.azure.eventhubs.processor) -példányt, amelynél a címbérlet tíz partíciókon. Ez a példány események olvasásakor a partíciók 0-9. Ettől kezdve a következők történnek:

* **Nincs szükség új függvény példányok**: `Function_0` a függvények skálázási logika lép működésbe, mielőtt az összes 1000 esemény feldolgozására képes. Ebben az esetben az összes 1000 üzenetek dolgozza fel `Function_0`.

* **Egy további függvény példány kerül hozzáadásra**: logikai méretezés a Functions határozza meg, amely `Function_0` rendelkezik a további üzeneteket, mint amennyit fel tud dolgozni. Az ebben az esetben egy új funkció-példányt (`Function_1`) jön létre, valamint egy új [EventProcessorHost](https://docs.microsoft.com/dotnet/api/microsoft.azure.eventhubs.processor) példány. Az Event Hubs észleli, hogy új gazdagép-példány megpróbálja az üzenetek olvasásakor. Event Hubs elosztja a partíciók között a saját példányait üzemeltetni. Ha például a partíciók 0 és 4 közötti rendelhetők hozzá `Function_0` és 5 – 9, particionálja `Function_1`. 

* **N példányt. a funkció további. Adja hozzá**: logikai méretezés a Functions határozza meg, amely mindkét `Function_0` és `Function_1` további üzenetek feldolgozására képes, mint. Új függvény alkalmazáspéldány `Function_2`... `Functions_N` jönnek létre, ahol `N` nagyobb, mint az event hub-partíciók száma. Ebben a példában az Event Hubs újra elosztja a terhelést a partíciók ebben az esetben a példányok között `Function_0`... `Functions_9`. 

Vegye figyelembe, hogy amikor funkciók méretezhető `N` példányok, ami egy szám nagyobb, mint az event hub-partíciók száma. Ez azért történt, győződjön meg róla, hogy az mindig [EventProcessorHost](https://docs.microsoft.com/dotnet/api/microsoft.azure.eventhubs.processor) példányig beszerzése a partíciók zárolását, amint a többi példány elérhetővé válnak. Ön csak az erőforrások díjkötelesek használatos, ha végrehajtja a függvényt példány; nem terheli a fölösleges üzembe.

Minden függvény végrehajtása befejeződött (a vagy hibák nélkül), ellenőrzőpontok bővül a társított storage-fiókban. Ha sikeres ellenőrzőpontos, 1000 üzenetek soha nem újra beolvasása.

## <a name="trigger---example"></a>Az eseményindító – példa

Tekintse meg az adott nyelvű példa:

* [C#](#trigger---c-example)
* [C# script (.csx)](#trigger---c-script-example)
* [F#](#trigger---f-example)
* [JavaScript](#trigger---javascript-example)
* [Java](#trigger---java-example)

### <a name="trigger---c-example"></a>Eseményindító - C#-példa

A következő példa bemutatja egy [C#-függvény](functions-dotnet-class-library.md) , amely naplózza az eseményközpont-eseményindító üzenet törzse.

```csharp
[FunctionName("EventHubTriggerCSharp")]
public static void Run([EventHubTrigger("samples-workitems", Connection = "EventHubConnectionAppSetting")] string myEventHubMessage, TraceWriter log)
{
    log.Info($"C# Event Hub trigger function processed a message: {myEventHubMessage}");
}
```

Hozzáférhet a [esemény-metaadatok](#trigger---event-metadata) függvénykód, a kötést létrehozni egy [EventData](/dotnet/api/microsoft.servicebus.messaging.eventdata) objektum (egy használata szükséges a utasítás `Microsoft.ServiceBus.Messaging`). Az azonos tulajdonságokkal a podpis metody kötés kifejezések használatával is elérheti.  Az alábbi példában is lekérdezheti ugyanezen adatokat módszert mutat be:

```csharp
[FunctionName("EventHubTriggerCSharp")]
public static void Run(
    [EventHubTrigger("samples-workitems", Connection = "EventHubConnectionAppSetting")] EventData myEventHubMessage, 
    DateTime enqueuedTimeUtc, 
    Int64 sequenceNumber,
    string offset,
    TraceWriter log)
{
    log.Info($"Event: {Encoding.UTF8.GetString(myEventHubMessage.GetBytes())}");
    // Metadata accessed by binding to EventData
    log.Info($"EnqueuedTimeUtc={myEventHubMessage.EnqueuedTimeUtc}");
    log.Info($"SequenceNumber={myEventHubMessage.SequenceNumber}");
    log.Info($"Offset={myEventHubMessage.Offset}");
    // Metadata accessed by using binding expressions
    log.Info($"EnqueuedTimeUtc={enqueuedTimeUtc}");
    log.Info($"SequenceNumber={sequenceNumber}");
    log.Info($"Offset={offset}");
}
```

Események fogadása a kötegben, győződjön meg arról, `string` vagy `EventData` tömböt:

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

### <a name="trigger---c-script-example"></a>Eseményindító - C#-szkript példa

Az alábbi példa bemutatja egy eseményközpont-eseményindító a kötés egy *function.json* fájl és a egy [C#-szkriptfüggvény](functions-reference-csharp.md) , amely a kötés használja. A függvény naplózza az eseményközpont-eseményindító üzenet törzse.

Az alábbi példák bemutatják az Event Hubs-kötés adatok a *function.json* fájlt. Az első példa 2.x működik, és a másikat az funkciók 1.x. 


```json
{
  "type": "eventHubTrigger",
  "name": "myEventHubMessage",
  "direction": "in",
  "eventHubName": "MyEventHub",
  "connection": "myEventHubReadConnectionAppSetting"
}
```
```json
{
  "type": "eventHubTrigger",
  "name": "myEventHubMessage",
  "direction": "in",
  "path": "MyEventHub",
  "connection": "myEventHubReadConnectionAppSetting"
}
```

Íme a C#-szkriptkódot:

```cs
using System;

public static void Run(string myEventHubMessage, TraceWriter log)
{
    log.Info($"C# Event Hub trigger function processed a message: {myEventHubMessage}");
}
```

Hozzáférhet a [esemény-metaadatok](#trigger---event-metadata) függvénykód, a kötést létrehozni egy [EventData](/dotnet/api/microsoft.servicebus.messaging.eventdata) objektum (egy használata szükséges a utasítás `Microsoft.ServiceBus.Messaging`). Az azonos tulajdonságokkal a podpis metody kötés kifejezések használatával is elérheti.  Az alábbi példában is lekérdezheti ugyanezen adatokat módszert mutat be:

```cs
#r "Microsoft.ServiceBus"
using System.Text;
using System;
using Microsoft.ServiceBus.Messaging;

public static void Run(EventData myEventHubMessage,
    DateTime enqueuedTimeUtc, 
    Int64 sequenceNumber,
    string offset,
    TraceWriter log)
{
    log.Info($"Event: {Encoding.UTF8.GetString(myEventHubMessage.GetBytes())}");
    // Metadata accessed by binding to EventData
    log.Info($"EnqueuedTimeUtc={myEventHubMessage.EnqueuedTimeUtc}");
    log.Info($"SequenceNumber={myEventHubMessage.SequenceNumber}");
    log.Info($"Offset={myEventHubMessage.Offset}");
    // Metadata accessed by using binding expressions
    log.Info($"EnqueuedTimeUtc={enqueuedTimeUtc}");
    log.Info($"SequenceNumber={sequenceNumber}");
    log.Info($"Offset={offset}");
}
```

Események fogadása a kötegben, győződjön meg arról, `string` vagy `EventData` tömböt:

```cs
public static void Run(string[] eventHubMessages, TraceWriter log)
{
    foreach (var message in eventHubMessages)
    {
        log.Info($"C# Event Hub trigger function processed a message: {message}");
    }
}
```

### <a name="trigger---f-example"></a>Eseményindító - F #-példa

Az alábbi példa bemutatja egy eseményközpont-eseményindító a kötés egy *function.json* fájl és a egy [F #-függvény](functions-reference-fsharp.md) , amely a kötés használja. A függvény naplózza az eseményközpont-eseményindító üzenet törzse.

Az alábbi példák bemutatják az Event Hubs-kötés adatok a *function.json* fájlt. Az első példa 2.x működik, és a másikat az funkciók 1.x. 


```json
{
  "type": "eventHubTrigger",
  "name": "myEventHubMessage",
  "direction": "in",
  "eventHubName": "MyEventHub",
  "connection": "myEventHubReadConnectionAppSetting"
}
```
```json
{
  "type": "eventHubTrigger",
  "name": "myEventHubMessage",
  "direction": "in",
  "path": "MyEventHub",
  "connection": "myEventHubReadConnectionAppSetting"
}
```

Az F #-kód itt látható:

```fsharp
let Run(myEventHubMessage: string, log: TraceWriter) =
    log.Info(sprintf "F# eventhub trigger function processed work item: %s" myEventHubMessage)
```

### <a name="trigger---javascript-example"></a>Eseményindító - JavaScript-példa

Az alábbi példa bemutatja egy eseményközpont-eseményindító a kötés egy *function.json* fájl és a egy [JavaScript-függvény](functions-reference-node.md) , amely a kötés használja. A függvény beolvassa [esemény-metaadatok](#trigger---event-metadata) és naplózza az üzenetet.

Az alábbi példák bemutatják az Event Hubs-kötés adatok a *function.json* fájlt. Az első példa 2.x működik, és a másikat az funkciók 1.x. 


```json
{
  "type": "eventHubTrigger",
  "name": "myEventHubMessage",
  "direction": "in",
  "eventHubName": "MyEventHub",
  "connection": "myEventHubReadConnectionAppSetting"
}
```
```json
{
  "type": "eventHubTrigger",
  "name": "myEventHubMessage",
  "direction": "in",
  "path": "MyEventHub",
  "connection": "myEventHubReadConnectionAppSetting"
}
```

A következő JavaScript-kódot:

```javascript
module.exports = function (context, eventHubMessage) {
    context.log('Event Hubs trigger function processed message: ', myEventHubMessage);
    context.log('EnqueuedTimeUtc =', context.bindingData.enqueuedTimeUtc);
    context.log('SequenceNumber =', context.bindingData.sequenceNumber);
    context.log('Offset =', context.bindingData.offset);
     
    context.done();
};
```

Egy kötegben események fogadásához állítsa `cardinality` való `many` a a *function.json* fájljához a következő példákban látható módon. Az első példa 2.x működik, és a másikat az funkciók 1.x. 

```json
{
  "type": "eventHubTrigger",
  "name": "eventHubMessages",
  "direction": "in",
  "eventHubName": "MyEventHub",
  "cardinality": "many",
  "connection": "myEventHubReadConnectionAppSetting"
}
```
```json
{
  "type": "eventHubTrigger",
  "name": "eventHubMessages",
  "direction": "in",
  "path": "MyEventHub",
  "cardinality": "many",
  "connection": "myEventHubReadConnectionAppSetting"
}
```

A következő JavaScript-kódot:

```javascript
module.exports = function (context, eventHubMessages) {
    context.log(`JavaScript eventhub trigger function called for message array ${eventHubMessages}`);
    
    eventHubMessages.forEach((message, index) => {
        context.log(`Processed message ${message}`);
        context.log(`EnqueuedTimeUtc = ${context.bindingData.enqueuedTimeUtcArray[index]}`);
        context.log(`SequenceNumber = ${context.bindingData.sequenceNumberArray[index]}`);
        context.log(`Offset = ${context.bindingData.offsetArray[index]}`);
    });

    context.done();
};
```

### <a name="trigger---java-example"></a>Eseményindító - Java-példában

Az alábbi példa bemutatja egy Eseményközpont-eseményindító a kötés egy *function.json* fájl és a egy [Java függvény](functions-reference-java.md) , amely a kötés használja. A függvény naplózza az Eseményközpont-eseményindító üzenet törzse.

```json
{
  "type": "eventHubTrigger",
  "name": "msg",
  "direction": "in",
  "eventHubName": "myeventhubname",
  "connection": "myEventHubReadConnectionAppSetting"
}
```

```java
@FunctionName("ehprocessor")
public void eventHubProcessor(
  @EventHubTrigger(name = "msg",
                  eventHubName = "myeventhubname",
                  connection = "myconnvarname") String message,
       final ExecutionContext context ) 
       {
          context.getLogger().info(message);
 }
 ```

 Az a [Java-függvények futásidejű kódtár](/java/api/overview/azure/functions/runtime), használja a `EventHubTrigger` jegyzet paraméterekkel, amelynek az értéke az Event Hubs lenne származnak. Ezek a jegyzetek a paraméterek miatt a funkció futtatását, amikor az esemény érkezik.  A jegyzet használható natív Java-típusokat, POJOs vagy nullázható értékek használata nem kötelező<T>. 

## <a name="trigger---attributes"></a>Eseményindító - attribútumok

A [C#-osztálykódtárakat](functions-dotnet-class-library.md), használja a [EventHubTriggerAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.ServiceBus/EventHubs/EventHubTriggerAttribute.cs) attribútum.

Az attribútum konstruktorának paramétereként meg az eseményközpont nevével, a fogyasztói csoport neve és a kapcsolati karakterláncot tartalmazó alkalmazásbeállítás neve. Ezekkel a beállításokkal kapcsolatos további információkért lásd: a [konfigurációs szakasz aktiválása](#trigger---configuration). Íme egy `EventHubTriggerAttribute` attribútum példa:

```csharp
[FunctionName("EventHubTriggerCSharp")]
public static void Run([EventHubTrigger("samples-workitems", Connection = "EventHubConnectionAppSetting")] string myEventHubMessage, TraceWriter log)
{
    ...
}
```

Egy teljes példa: [eseményindító – C#-példa](#trigger---c-example).

## <a name="trigger---configuration"></a>Eseményindító - konfiguráció

A következő táblázat ismerteti a megadott kötés konfigurációs tulajdonságaiban a *function.json* fájlt, és a `EventHubTrigger` attribútum.

|Function.JSON tulajdonság | Attribútum tulajdonsága |Leírás|
|---------|---------|----------------------|
|**type** | n/a | Meg kell `eventHubTrigger`. Ez a tulajdonság beállítása automatikusan történik, ha az eseményindítót fog létrehozni az Azure Portalon.|
|**direction** | n/a | Meg kell `in`. Ez a tulajdonság beállítása automatikusan történik, ha az eseményindítót fog létrehozni az Azure Portalon. |
|**name** | n/a | A függvénykód esemény elemet képviselő változó neve. | 
|**path** |**EventHubName** | Csak 1.x működik. Az eseményközpont neve. Ha az eseményközpont neve is szerepel a kapcsolati karakterláncot, ezt az értéket felülbírálja ezt a tulajdonságot, futásidőben. | 
|**eventHubName** |**EventHubName** | Csak a 2.x működik. Az eseményközpont neve. Ha az eseményközpont neve is szerepel a kapcsolati karakterláncot, ezt az értéket felülbírálja ezt a tulajdonságot, futásidőben. |
|**a fogyasztói csoporttól** |**a fogyasztói csoporttól** | Egy nem kötelező tulajdonsággal, amely beállítja a [fogyasztói csoportot](../event-hubs/event-hubs-features.md#event-consumers) fizethet elő eseményekre az agyban használt. Ha nincs megadva, a `$Default` fogyasztói csoportot használja. | 
|**számossága** | n/a | A Javascripthez. Állítsa be `many` , és engedélyezze a kötegelés.  Ha nincs megadva vagy `one`, függvénynek átadott egyetlen üzenetben. | 
|**kapcsolat** |**kapcsolat** | Az event hubs-névtér a kapcsolati karakterláncot tartalmazó alkalmazásbeállítás neve. Ez a kapcsolati karakterlánc másolása kattintva a **kapcsolatadatok** gombot a [névtér](../event-hubs/event-hubs-create.md#create-an-event-hubs-namespace), nem pedig maga az event hubs. Ez a kapcsolati karakterlánc legalább olvasás engedéllyel rendelkezik az eseményindító aktiválása.|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="trigger---event-metadata"></a>Eseményindító - esemény-metaadatok

Az Event Hubs-eseményindító biztosít több [metaadat-tulajdonságot](functions-triggers-bindings.md#binding-expressions---trigger-metadata). Ezek a tulajdonságok a kötési kifejezésekben való használata más kötések részeként vagy a kód paramétereiben használható. Ezek a tulajdonságai a [EventData](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.eventdata) osztály.

|Tulajdonság|Típus|Leírás|
|--------|----|-----------|
|`PartitionContext`|[PartitionContext](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.partitioncontext)|A `PartitionContext` példány.|
|`EnqueuedTimeUtc`|`DateTime`|A várólistán lévő időpontja (UTC).|
|`Offset`|`string`|Az adatokat az Event Hub-partíció adatfolyam képest eltolva. Az eltolás egy nyílhegy vagy az Event Hubs streamen belül esemény azonosítója. Az azonosító, amely egyedi az Event Hubs-adatfolyam egy partíción belül.|
|`PartitionKey`|`string`|A partíció, amely esetben adatok rendszer.|
|`Properties`|`IDictionary<String,Object>`|A felhasználó tulajdonságai az eseményadatokat.|
|`SequenceNumber`|`Int64`|Az esemény logikai sorrendben száma.|
|`SystemProperties`|`IDictionary<String,Object>`|A rendszer-tulajdonságokat, beleértve az eseményadatokat.|

Lásd: [kódpéldák](#trigger---example) , használja ezeket a tulajdonságokat a cikk korábbi részében.

## <a name="trigger---hostjson-properties"></a>Eseményindító - host.json tulajdonságai

A [host.json](functions-host-json.md#eventhub) fájl az Event Hubs eseményindító viselkedését vezérlő beállításokat tartalmaz.

[!INCLUDE [functions-host-json-event-hubs](../../includes/functions-host-json-event-hubs.md)]

## <a name="output"></a>Kimenet

Az Event Hubs-kimeneti kötését beírni az eseményeket az eseménystream használata. Eseményeket írni egy eseményközpontba küldési engedéllyel kell rendelkeznie.

Győződjön meg arról, a szükséges csomag hivatkozások vannak érvényben: [1.x függvények](#packages---functions-1.x) vagy [2.x függvények](#packages---functions-2.x) 

## <a name="output---example"></a>Kimenete – példa

Tekintse meg az adott nyelvű példa:

* [C#](#output---c-example)
* [C# script (.csx)](#output---c-script-example)
* [F#](#output---f-example)
* [JavaScript](#output---javascript-example)
* [Java](#output---java-example)

### <a name="output---c-example"></a>Kimenet – C#-példa

A következő példa bemutatja egy [C#-függvény](functions-dotnet-class-library.md) , amely üzenetet ír egy eseményközpontba, a metódus visszatérési érték a kimeneteként:

```csharp
[FunctionName("EventHubOutput")]
[return: EventHub("outputEventHubMessage", Connection = "EventHubConnectionAppSetting")]
public static string Run([TimerTrigger("0 */5 * * * *")] TimerInfo myTimer, TraceWriter log)
{
    log.Info($"C# Timer trigger function executed at: {DateTime.Now}");
    return $"{DateTime.Now}";
}
```

### <a name="output---c-script-example"></a>Kimenet – C#-szkript példa

Az alábbi példa bemutatja egy eseményközpont-eseményindító a kötés egy *function.json* fájl és a egy [C#-szkriptfüggvény](functions-reference-csharp.md) , amely a kötés használja. A függvény üzenetet ír egy eseményközpontba.

Az alábbi példák bemutatják az Event Hubs-kötés adatok a *function.json* fájlt. Az első példa 2.x működik, és a másikat az funkciók 1.x. 

```json
{
    "type": "eventHub",
    "name": "outputEventHubMessage",
    "eventHubName": "myeventhub",
    "connection": "MyEventHubSendAppSetting",
    "direction": "out"
}
```
```json
{
    "type": "eventHub",
    "name": "outputEventHubMessage",
    "path": "myeventhub",
    "connection": "MyEventHubSendAppSetting",
    "direction": "out"
}
```

Itt látható C#-szkriptkódot, amely egy üzenetet hoz létre:

```cs
using System;

public static void Run(TimerInfo myTimer, out string outputEventHubMessage, TraceWriter log)
{
    String msg = $"TimerTriggerCSharp1 executed at: {DateTime.Now}";
    log.Verbose(msg);   
    outputEventHubMessage = msg;
}
```

Az alábbiakban C#-szkriptkódot, amely több üzenetet hoz létre:

```cs
public static void Run(TimerInfo myTimer, ICollector<string> outputEventHubMessage, TraceWriter log)
{
    string message = $"Event Hub message created at: {DateTime.Now}";
    log.Info(message);
    outputEventHubMessage.Add("1 " + message);
    outputEventHubMessage.Add("2 " + message);
}
```

### <a name="output---f-example"></a>Kimenet – F #-példa

Az alábbi példa bemutatja egy eseményközpont-eseményindító a kötés egy *function.json* fájl és a egy [F #-függvény](functions-reference-fsharp.md) , amely a kötés használja. A függvény üzenetet ír egy eseményközpontba.

Az alábbi példák bemutatják az Event Hubs-kötés adatok a *function.json* fájlt. Az első példa 2.x működik, és a másikat az funkciók 1.x. 

```json
{
    "type": "eventHub",
    "name": "outputEventHubMessage",
    "eventHubName": "myeventhub",
    "connection": "MyEventHubSendAppSetting",
    "direction": "out"
}
```
```json
{
    "type": "eventHub",
    "name": "outputEventHubMessage",
    "path": "myeventhub",
    "connection": "MyEventHubSendAppSetting",
    "direction": "out"
}
```

Az F #-kód itt látható:

```fsharp
let Run(myTimer: TimerInfo, outputEventHubMessage: byref<string>, log: TraceWriter) =
    let msg = sprintf "TimerTriggerFSharp1 executed at: %s" DateTime.Now.ToString()
    log.Verbose(msg);
    outputEventHubMessage <- msg;
```

### <a name="output---javascript-example"></a>Kimenet – JavaScript-példa

Az alábbi példa bemutatja egy eseményközpont-eseményindító a kötés egy *function.json* fájl és a egy [JavaScript-függvény](functions-reference-node.md) , amely a kötés használja. A függvény üzenetet ír egy eseményközpontba.

Az alábbi példák bemutatják az Event Hubs-kötés adatok a *function.json* fájlt. Az első példa 2.x működik, és a másikat az funkciók 1.x. 

```json
{
    "type": "eventHub",
    "name": "outputEventHubMessage",
    "eventHubName": "myeventhub",
    "connection": "MyEventHubSendAppSetting",
    "direction": "out"
}
```
```json
{
    "type": "eventHub",
    "name": "outputEventHubMessage",
    "path": "myeventhub",
    "connection": "MyEventHubSendAppSetting",
    "direction": "out"
}
```

Az alábbiakban JavaScript-kódot, amely egy üzenet küld:

```javascript
module.exports = function (context, myTimer) {
    var timeStamp = new Date().toISOString();
    context.log('Event Hub message created at: ', timeStamp);   
    context.bindings.outputEventHubMessage = "Event Hub message created at: " + timeStamp;
    context.done();
};
```

Az alábbiakban JavaScript-kódot, amely több üzenetet küld:

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

### <a name="output---java-example"></a>Kimenet – Java-példában

Az alábbi példa bemutatja egy Java-függvény, amely az aktuális időt egy üzenet csipesz ír egy eseményközpontba.

```java
@}FunctionName("sendTime")
@EventHubOutput(name = "event", eventHubName = "samples-workitems", connection = "AzureEventHubConnection")
public String sendTime(
   @TimerTrigger(name = "sendTimeTrigger", schedule = "0 *&#47;5 * * * *") String timerInfo)  {
     return LocalDateTime.now().toString();
 }
 ```

Az a [Java-függvények futásidejű kódtár](/java/api/overview/azure/functions/runtime), használja a `@EventHubOutput` jegyzet paraméterekkel, amelynek az értéke lenne poublished az Eseményközpontba.  A paraméter típusúnak kell lennie `OutputBinding<T>` , ahol a T egy pojo-vá vagy bármilyen natív Java-típus. 

## <a name="output---attributes"></a>Kimenet – attribútumok

A [C#-osztálykódtárakat](functions-dotnet-class-library.md), használja a [EventHubAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.ServiceBus/EventHubs/EventHubAttribute.cs) attribútum.

Az attribútum konstruktorának paramétereként meg az eseményközpont nevét és a egy kapcsolati karakterláncot tartalmazó alkalmazásbeállítás neve. Ezekkel a beállításokkal kapcsolatos további információkért lásd: [kimenete – konfigurációs](#output---configuration). Íme egy `EventHub` attribútum példa:

```csharp
[FunctionName("EventHubOutput")]
[return: EventHub("outputEventHubMessage", Connection = "EventHubConnectionAppSetting")]
public static string Run([TimerTrigger("0 */5 * * * *")] TimerInfo myTimer, TraceWriter log)
{
    ...
}
```

Egy teljes példa: [kimenet – C#-példa](#output---c-example).

## <a name="output---configuration"></a>Kimenete – konfiguráció

A következő táblázat ismerteti a megadott kötés konfigurációs tulajdonságaiban a *function.json* fájlt, és a `EventHub` attribútum.

|Function.JSON tulajdonság | Attribútum tulajdonsága |Leírás|
|---------|---------|----------------------|
|**type** | n/a | "Az eventHub" kell állítani. |
|**direction** | n/a | Állítsa "out". Ez a paraméter értéke automatikusan a kötés létrehozásakor az Azure Portalon. |
|**name** | n/a | A függvény kódját a esemény a használt változó neve. | 
|**path** |**EventHubName** | Csak 1.x működik. Az eseményközpont neve. Ha az eseményközpont neve is szerepel a kapcsolati karakterláncot, ezt az értéket felülbírálja ezt a tulajdonságot, futásidőben. | 
|**eventHubName** |**EventHubName** | Csak a 2.x működik. Az eseményközpont neve. Ha az eseményközpont neve is szerepel a kapcsolati karakterláncot, ezt az értéket felülbírálja ezt a tulajdonságot, futásidőben. |
|**kapcsolat** |**kapcsolat** | Az event hubs-névtér a kapcsolati karakterláncot tartalmazó alkalmazásbeállítás neve. Ez a kapcsolati karakterlánc másolása kattintva a **kapcsolatadatok** gombot a *névtér*, nem pedig maga az event hubs. Ez a kapcsolati karakterlánc az üzenet küldéséhez az eseménystream küldési jogosultsággal kell rendelkeznie.|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="output---usage"></a>Kimenet – használat

C# és a C#-szkript, üzeneteket küldhet egy metódus-paramétert használatával `out string paramName`. A C#-szkript `paramName` az értéket a megadott a `name` tulajdonsága *function.json*. Több üzenetet ír, használható `ICollector<string>` vagy `IAsyncCollector<string>` helyén `out string`.

A JavaScript, elérni a kimeneti esemény `context.bindings.<name>`. `<name>` az érték szerepel a `name` tulajdonsága *function.json*.

## <a name="exceptions-and-return-codes"></a>Kivételek és a visszatérési kódok

| Kötés | Leírások |
|---|---|
| Eseményközpont | [Üzemeltetési útmutató](https://docs.microsoft.com/rest/api/eventhub/publisher-policy-operations) |

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Tudjon meg többet az Azure functions eseményindítók és kötések](functions-triggers-bindings.md)
