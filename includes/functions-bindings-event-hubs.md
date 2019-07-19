---
author: craigshoemaker
ms.service: azure-functions
ms.topic: include
ms.date: 03/05/2019
ms.author: cshoe
ms.openlocfilehash: 3ce3d61bf6b5fb44fa13527bc5a93295784fa66b
ms.sourcegitcommit: a6873b710ca07eb956d45596d4ec2c1d5dc57353
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/16/2019
ms.locfileid: "68286251"
---
## <a name="trigger"></a>Eseményindító

Az Event hub Event streambe küldött eseményre való reagáláshoz használja a függvény eseményindítóját. Az eseményindító beállításához olvasási hozzáféréssel kell rendelkeznie a mögöttes Event hub-hoz. A függvény elindításakor a függvénynek átadott üzenet karakterláncként van beírva.

## <a name="trigger---scaling"></a>Trigger – skálázás

Egy esemény által aktivált függvény minden példányát egyetlen [EventProcessorHost](https://docs.microsoft.com/dotnet/api/microsoft.azure.eventhubs.processor) -példány támogatja. A trigger (Event Hubs) biztosítja, hogy csak egy [EventProcessorHost](https://docs.microsoft.com/dotnet/api/microsoft.azure.eventhubs.processor) -példány kapjon bérletet egy adott partíción.

Vegyünk például egy Event hub-t az alábbiak szerint:

* 10 partíció
* 1 000 az összes partíción egyenletesen elosztott események, 100-es üzenetekkel az egyes partíciók között

A függvény első engedélyezésekor a függvénynek csak egy példánya van. Hívjuk meg az első függvény példányát `Function_0`. A `Function_0` függvény egyetlen [EventProcessorHost](https://docs.microsoft.com/dotnet/api/microsoft.azure.eventhubs.processor) -példánnyal rendelkezik, amely minden tíz partíción rendelkezik bérlettel. Ez a példány az 0-9-es partíciókhoz származó eseményeket olvas. Ettől a ponttól kezdve a következők egyike történik:

* Az **új függvények példányai nem**szükségesek `Function_0` : az összes 1 000 eseményt feldolgozhatja, mielőtt a függvények skálázási logikája érvénybe lép. Ebben az esetben a rendszer az összes 1 000 üzenetet dolgozza `Function_0`fel.

* **További függvény-példány hozzáadása**: Ha a függvények skálázási logikája meghatározza `Function_0` , hogy több üzenet van, mint amennyit feldolgozhat, egy új Function`Function_1`App-példány () jön létre. Ez az új függvény a [EventProcessorHost](https://docs.microsoft.com/dotnet/api/microsoft.azure.eventhubs.processor)társított példányával is rendelkezik. Mivel az alapul szolgáló Event Hubs azt vizsgálja, hogy egy új gazdagép-példány próbálkozik az olvasási üzenetekkel, a terheléselosztás a gazdagép példányain tárolja a partíciókat. Előfordulhat például, hogy a 0-4-es partíciók hozzá vannak rendelve `Function_1`a `Function_0` 5-9-es partícióhoz.

* **N további függvény**-példányok lettek hozzáadva: Ha a függvények skálázási logikája meghatározza, `Function_0` hogy `Function_1` a és a rendszer több üzenetet is feldolgozzon, akkor az új `Functions_N` Function app-példányok jönnek létre.  Az alkalmazások arra a pontra jönnek `N` létre, ahol a nagyobb, mint az Event hub-partíciók száma. A példánkban Event Hubs újra a partíciók elosztása, ebben az esetben a példányok `Function_0`között... `Functions_9`.

A függvények skálázásakor `N` a példányok száma nagyobb, mint az Event hub-partíciók száma. Ezzel biztosítható, hogy a [EventProcessorHost](https://docs.microsoft.com/dotnet/api/microsoft.azure.eventhubs.processor) -példányok elérhetők legyenek a partíciók zárolásának beszerzéséhez, mivel azok más példányokból elérhetővé válnak. Csak a függvény példányának végrehajtásakor használt erőforrásokért kell fizetnie. Más szóval nem számítunk fel díjat a kiépítés során.

Ha az összes függvény végrehajtása befejeződött (hibákkal vagy anélkül), a rendszer ellenőrzőpontokat ad hozzá a társított Storage-fiókhoz. Ha a pipa sikeres, a rendszer az összes 1 000 üzenetet soha nem kéri le.

## <a name="trigger---example"></a>Az eseményindító – példa

Tekintse meg az adott nyelvű példa:

* [C#](#trigger---c-example)
* [C# script (.csx)](#trigger---c-script-example)
* [F#](#trigger---f-example)
* [Java](#trigger---java-example)
* [JavaScript](#trigger---javascript-example)
* [Python](#trigger---python-example)

### <a name="trigger---c-example"></a>Eseményindító - C#-példa

Az alábbi példa egy [ C# függvényt](../articles/azure-functions/functions-dotnet-class-library.md) mutat be, amely az Event hub eseményindítójának üzenet törzsét naplózza.

```csharp
[FunctionName("EventHubTriggerCSharp")]
public static void Run([EventHubTrigger("samples-workitems", Connection = "EventHubConnectionAppSetting")] string myEventHubMessage, ILogger log)
{
    log.LogInformation($"C# function triggered to process a message: {myEventHubMessage}");
}
```

Ahhoz, hogy hozzáférést kapjon az [esemény metaadataihoz](#trigger---event-metadata) a Function Code-ban, kötést kell kötnie egy `Microsoft.Azure.EventHubs` [EventData](/dotnet/api/microsoft.servicebus.messaging.eventdata) -objektumhoz (a használatához a using utasítás szükséges). Ugyanezeket a tulajdonságokat a metódus aláírása kötési kifejezések használatával is elérheti.  Az alábbi példa mindkét módszert mutatja be, hogy ugyanazokat az eszközöket kapja:

```csharp
[FunctionName("EventHubTriggerCSharp")]
public static void Run(
    [EventHubTrigger("samples-workitems", Connection = "EventHubConnectionAppSetting")] EventData myEventHubMessage,
    DateTime enqueuedTimeUtc,
    Int64 sequenceNumber,
    string offset,
    ILogger log)
{
    log.LogInformation($"Event: {Encoding.UTF8.GetString(myEventHubMessage.Body)}");
    // Metadata accessed by binding to EventData
    log.LogInformation($"EnqueuedTimeUtc={myEventHubMessage.SystemProperties.EnqueuedTimeUtc}");
    log.LogInformation($"SequenceNumber={myEventHubMessage.SystemProperties.SequenceNumber}");
    log.LogInformation($"Offset={myEventHubMessage.SystemProperties.Offset}");
    // Metadata accessed by using binding expressions in method parameters
    log.LogInformation($"EnqueuedTimeUtc={enqueuedTimeUtc}");
    log.LogInformation($"SequenceNumber={sequenceNumber}");
    log.LogInformation($"Offset={offset}");
}
```

Ha egy kötegben szeretne eseményeket fogadni, `string` végezze el vagy `EventData` egy tömböt.  

> [!NOTE]
> Egy kötegben való fogadáskor nem lehet olyan metódusokhoz kötni, mint például a fenti `DateTime enqueuedTimeUtc` példában, és meg kell kapnia ezeket az egyes `EventData` objektumokból.  

```cs
[FunctionName("EventHubTriggerCSharp")]
public static void Run([EventHubTrigger("samples-workitems", Connection = "EventHubConnectionAppSetting")] EventData[] eventHubMessages, ILogger log)
{
    foreach (var message in eventHubMessages)
    {
        log.LogInformation($"C# function triggered to process a message: {Encoding.UTF8.GetString(message.Body)}");
        log.LogInformation($"EnqueuedTimeUtc={message.SystemProperties.EnqueuedTimeUtc}");
    }
}
```

### <a name="trigger---c-script-example"></a>Eseményindító - C#-szkript példa

Az alábbi példa egy Event hub eseményindító-kötést mutat be egy *function. JSON* fájlban, valamint egy olyan [ C# parancsfájl](../articles/azure-functions/functions-reference-csharp.md) -függvényt, amely a kötést használja. A függvény naplózza az Event hub eseményindítójának üzenet törzsét.

Az alábbi példák a *function. JSON* fájlban Event Hubs kötési adataikat mutatják be.

#### <a name="version-2x"></a>2-es verzió. x

```json
{
  "type": "eventHubTrigger",
  "name": "myEventHubMessage",
  "direction": "in",
  "eventHubName": "MyEventHub",
  "connection": "myEventHubReadConnectionAppSetting"
}
```

#### <a name="version-1x"></a>1\. x verzió

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
    log.Info($"C# function triggered to process a message: {myEventHubMessage}");
}
```

Ahhoz, hogy hozzáférést kapjon az [esemény metaadataihoz](#trigger---event-metadata) a Function Code-ban, kötést kell kötnie egy `Microsoft.Azure.EventHubs` [EventData](/dotnet/api/microsoft.servicebus.messaging.eventdata) -objektumhoz (a használatához a using utasítás szükséges). Ugyanezeket a tulajdonságokat a metódus aláírása kötési kifejezések használatával is elérheti.  Az alábbi példa mindkét módszert mutatja be, hogy ugyanazokat az eszközöket kapja:

```cs
#r "Microsoft.Azure.EventHubs"

using System.Text;
using System;
using Microsoft.ServiceBus.Messaging;
using Microsoft.Azure.EventHubs;

public static void Run(EventData myEventHubMessage,
    DateTime enqueuedTimeUtc,
    Int64 sequenceNumber,
    string offset,
    TraceWriter log)
{
    log.Info($"Event: {Encoding.UTF8.GetString(myEventHubMessage.Body)}");
    log.Info($"EnqueuedTimeUtc={myEventHubMessage.SystemProperties.EnqueuedTimeUtc}");
    log.Info($"SequenceNumber={myEventHubMessage.SystemProperties.SequenceNumber}");
    log.Info($"Offset={myEventHubMessage.SystemProperties.Offset}");

    // Metadata accessed by using binding expressions
    log.Info($"EnqueuedTimeUtc={enqueuedTimeUtc}");
    log.Info($"SequenceNumber={sequenceNumber}");
    log.Info($"Offset={offset}");
}
```

Ha egy kötegben szeretne eseményeket fogadni, `string` hozzon vagy `EventData` egy tömböt:

```cs
public static void Run(string[] eventHubMessages, TraceWriter log)
{
    foreach (var message in eventHubMessages)
    {
        log.Info($"C# function triggered to process a message: {message}");
    }
}
```

### <a name="trigger---f-example"></a>Eseményindító - F# példa

Az alábbi példa egy Event hub eseményindító-kötést mutat be egy *function. JSON* fájlban [ F# ](../articles/azure-functions/functions-reference-fsharp.md) , valamint egy olyan függvényt, amely a kötést használja. A függvény naplózza az Event hub eseményindítójának üzenet törzsét.

Az alábbi példák a *function. JSON* fájlban Event Hubs kötési adataikat mutatják be. 

#### <a name="version-2x"></a>2-es verzió. x

```json
{
  "type": "eventHubTrigger",
  "name": "myEventHubMessage",
  "direction": "in",
  "eventHubName": "MyEventHub",
  "connection": "myEventHubReadConnectionAppSetting"
}
```

#### <a name="version-1x"></a>1\. x verzió

```json
{
  "type": "eventHubTrigger",
  "name": "myEventHubMessage",
  "direction": "in",
  "path": "MyEventHub",
  "connection": "myEventHubReadConnectionAppSetting"
}
```

Íme a F# kódot:

```fsharp
let Run(myEventHubMessage: string, log: TraceWriter) =
    log.Log(sprintf "F# eventhub trigger function processed work item: %s" myEventHubMessage)
```

### <a name="trigger---javascript-example"></a>Eseményindító - JavaScript-példa

Az alábbi példa egy Event hub eseményindító-kötést mutat be egy *function. JSON* fájlban, valamint egy [JavaScript](../articles/azure-functions/functions-reference-node.md) -függvényt, amely a kötést használja. A függvény beolvassa az [esemény metaadatait](#trigger---event-metadata) , és naplózza az üzenetet.

Az alábbi példák a *function. JSON* fájlban Event Hubs kötési adataikat mutatják be.

#### <a name="version-2x"></a>2-es verzió. x

```json
{
  "type": "eventHubTrigger",
  "name": "myEventHubMessage",
  "direction": "in",
  "eventHubName": "MyEventHub",
  "connection": "myEventHubReadConnectionAppSetting"
}
```

#### <a name="version-1x"></a>1\. x verzió

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
    context.log('Function triggered to process a message: ', myEventHubMessage);
    context.log('EnqueuedTimeUtc =', context.bindingData.enqueuedTimeUtc);
    context.log('SequenceNumber =', context.bindingData.sequenceNumber);
    context.log('Offset =', context.bindingData.offset);

    context.done();
};
```

Ha egy kötegben szeretne eseményeket fogadni, `cardinality` állítsa `many` a *függvény. JSON* fájlba az alábbi példákban látható módon.

#### <a name="version-2x"></a>2-es verzió. x

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

#### <a name="version-1x"></a>1\. x verzió

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

### <a name="trigger---python-example"></a>Trigger – Python-példa

Az alábbi példa egy Event hub eseményindító-kötést mutat be egy *function. JSON* fájlban, valamint egy olyan [Python](../articles/azure-functions/functions-reference-python.md) -függvényt, amely a kötést használja. A függvény beolvassa az [esemény metaadatait](#trigger---event-metadata) , és naplózza az üzenetet.

Az alábbi példák a *function. JSON* fájlban Event Hubs kötési adataikat mutatják be.

```json
{
  "type": "eventHubTrigger",
  "name": "event",
  "direction": "in",
  "eventHubName": "MyEventHub",
  "connection": "myEventHubReadConnectionAppSetting"
}
```

Itt látható a Python-kód:

```python
import logging
import azure.functions as func

def main(event: func.EventHubEvent):
    logging.info('Function triggered to process a message: ', event.get_body())
    logging.info('  EnqueuedTimeUtc =', event.enqueued_time)
    logging.info('  SequenceNumber =', event.sequence_number)
    logging.info('  Offset =', event.offset)
```

### <a name="trigger---java-example"></a>Eseményindító - Java-példában

Az alábbi példa egy Event hub eseményindító-kötést mutat be egy *function. JSON* fájlban és egy olyan [Java](../articles/azure-functions/functions-reference-java.md) -függvényben, amely a kötést használja. A függvény naplózza az Event hub eseményindítójának üzenet törzsét.

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

 A [Java functions runtime library](/java/api/overview/azure/functions/runtime)-ben használja `EventHubTrigger` a jegyzeteket azon paramétereknél, amelyek értéke az Event hubből származik. Az ezekkel a megjegyzésekkel rendelkező paraméterek a függvény futását okozzák, amikor egy esemény érkezik.  Ezt a jegyzetet natív Java-típusokkal, szerializálói vagy NULL értékű értékekkel lehet használni opcionális\<T > használatával.

## <a name="trigger---attributes"></a>Eseményindító - attribútumok

Az [ C# osztályok könyvtáraiban](../articles/azure-functions/functions-dotnet-class-library.md)használja a [EventHubTriggerAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.Extensions.EventHubs/EventHubTriggerAttribute.cs) attribútumot.

Az attribútum konstruktora az Event hub nevét, a fogyasztói csoport nevét és a kapcsolati karakterláncot tartalmazó Alkalmazásbeállítás nevét tartalmazza. További információ ezekről a beállításokról: [trigger konfiguráció szakasz](#trigger---configuration). Példa egy `EventHubTriggerAttribute` attribútumra:

```csharp
[FunctionName("EventHubTriggerCSharp")]
public static void Run([EventHubTrigger("samples-workitems", Connection = "EventHubConnectionAppSetting")] string myEventHubMessage, ILogger log)
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
|**name** | n/a | Annak a változónak a neve, amely a függvény kódjában szereplő Event tételt jelöli. |
|**path** |**EventHubName** | Csak 1. x függvények. Az Event hub neve. Ha az Event hub neve szerepel a kapcsolati sztringben is, ez az érték felülbírálja ezt a tulajdonságot futásidőben. |
|**eventHubName** |**EventHubName** | Csak 2. x funkció. Az Event hub neve. Ha az Event hub neve szerepel a kapcsolati sztringben is, ez az érték felülbírálja ezt a tulajdonságot futásidőben. |
|**consumerGroup** |**ConsumerGroup** | Nem kötelező tulajdonság, amely a központban lévő eseményekre való előfizetéshez használt [fogyasztói csoportot](../articles/event-hubs/event-hubs-features.md)állítja #event – fogyasztók számára). Ha nincs megadva, a `$Default` rendszer a fogyasztói csoportot használja. |
|**számosság** | n/a | A Javascripthez. A kötegelt feldolgozás engedélyezéséhez állítsa a következőre:.`many`  Ha nincs megadva, vagy a `one`értékre van állítva, a rendszer egyetlen üzenetet kapott a függvénynek. |
|**kapcsolat** |**kapcsolat** | Az Event hub névteréhez tartozó kapcsolati sztringet tartalmazó Alkalmazásbeállítás neve. Másolja a kapcsolati karakterláncot a [névtér](../articles/event-hubs/event-hubs-create.md)#create-a-Event-hubok-névtér **kapcsolati adatok** gombjára kattintva, nem az Event hub-t. A kapcsolódási karakterláncnak legalább olvasási engedéllyel kell rendelkeznie az trigger aktiválásához.|
|**path**|**EventHubName**|Az Event hub neve. Az alkalmazás beállításain keresztül lehet hivatkozni`%eventHubName%`|

[!INCLUDE [app settings to local.settings.json](../articles/azure-functions/../../includes/functions-app-settings-local.md)]

## <a name="trigger---event-metadata"></a>Eseményindító – esemény metaadatainak

A Event Hubs trigger számos [metaadat](../articles/azure-functions/./functions-bindings-expressions-patterns.md)-tulajdonságot biztosít. Ezek a tulajdonságok a kötési kifejezésekben való használata más kötések részeként vagy a kód paramétereiben használható. Ezek a [EventData](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.eventdata) osztály tulajdonságai.

|Tulajdonság|Típus|Leírás|
|--------|----|-----------|
|`PartitionContext`|[PartitionContext](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.partitioncontext)|A `PartitionContext` példány.|
|`EnqueuedTimeUtc`|`DateTime`|A várólistán lévő időpontja (UTC).|
|`Offset`|`string`|Az adateltolás az Event hub-partíciós adatfolyamhoz viszonyítva. Az eltolás a Event Hubs streamen belüli esemény jelölője vagy azonosítója. Az azonosító egyedi az Event Hubs stream egy partícióján belül.|
|`PartitionKey`|`string`|Az a partíció, amelybe az eseményt el kell juttatni.|
|`Properties`|`IDictionary<String,Object>`|Az eseményhez tartozó adatértékek felhasználói tulajdonságai|
|`SequenceNumber`|`Int64`|Az esemény logikai sorszáma.|
|`SystemProperties`|`IDictionary<String,Object>`|A Rendszertulajdonságok, beleértve az eseményre vonatkozó adattípusokat is.|

Lásd: [kódpéldák](#trigger---example) , használja ezeket a tulajdonságokat a cikk korábbi részében.

## <a name="trigger---hostjson-properties"></a>Eseményindító - host.json tulajdonságai

A [Host. JSON](../articles/azure-functions/functions-host-json.md#eventhub) fájl olyan beállításokat tartalmaz, amelyek a Event Hubs trigger viselkedését vezérlik.

[!INCLUDE [functions-host-json-event-hubs](../articles/azure-functions/../../includes/functions-host-json-event-hubs.md)]

## <a name="output"></a>Output

Az események egy esemény-adatfolyamba való írásához használja a Event Hubs kimeneti kötést. Az Event hub-nak Küldés engedéllyel kell rendelkeznie az események írásához.

Győződjön meg arról, hogy a szükséges csomagokra vonatkozó hivatkozások vannak érvényben: Functions 1. x vagy functions 2. x

## <a name="output---example"></a>Kimenete – példa

Tekintse meg az adott nyelvű példa:

* [C#](#output---c-example)
* [C# script (.csx)](#output---c-script-example)
* [F#](#output---f-example)
* [Java](#output---java-example)
* [JavaScript](#output---javascript-example)
* [Python](#output---python-example)

### <a name="output---c-example"></a>Kimenet – C#-példa

Az alábbi példa egy olyan [ C# függvényt](../articles/azure-functions/functions-dotnet-class-library.md) mutat be, amely üzenetet ír egy Event hub-ba, a metódus visszatérési értékeként a kimenetként:

```csharp
[FunctionName("EventHubOutput")]
[return: EventHub("outputEventHubMessage", Connection = "EventHubConnectionAppSetting")]
public static string Run([TimerTrigger("0 */5 * * * *")] TimerInfo myTimer, ILogger log)
{
    log.LogInformation($"C# Timer trigger function executed at: {DateTime.Now}");
    return $"{DateTime.Now}";
}
```

Az alábbi minta azt mutatja be, hogyan `IAsyncCollector` használható az interfész egy köteg üzenetek küldésére. Ez a forgatókönyv gyakori, ha egy adott esemény központból érkező üzeneteket dolgoz fel, és az eredményt egy másik Event hub számára küldi el.

```csharp
[FunctionName("EH2EH")]
public static async Task Run(
    [EventHubTrigger("source", Connection = "EventHubConnectionAppSetting")] EventData[] events,
    [EventHub("dest", Connection = "EventHubConnectionAppSetting")]IAsyncCollector<string> outputEvents,
    ILogger log)
{
    foreach (EventData eventData in events)
    {
        // do some processing:
        var myProcessedEvent = DoSomething(eventData);

        // then send the message
        await outputEvents.AddAsync(JsonConvert.SerializeObject(myProcessedEvent));
    }
}
```

### <a name="output---c-script-example"></a>Kimenet – C#-szkript példa

Az alábbi példa egy Event hub eseményindító-kötést mutat be egy *function. JSON* fájlban, valamint egy olyan [ C# parancsfájl](../articles/azure-functions/functions-reference-csharp.md) -függvényt, amely a kötést használja. A függvény egy üzenetet ír az Event hub-ba.

Az alábbi példák a *function. JSON* fájlban Event Hubs kötési adataikat mutatják be. Az első példa a 2. x függvényre, a második pedig az 1. x függvényre mutat. 

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

Az alábbi C# szkript kód egy üzenetet hoz létre:

```cs
using System;
using Microsoft.Extensions.Logging;

public static void Run(TimerInfo myTimer, out string outputEventHubMessage, ILogger log)
{
    String msg = $"TimerTriggerCSharp1 executed at: {DateTime.Now}";
    log.LogInformation(msg);   
    outputEventHubMessage = msg;
}
```

Az alábbiakban C#-szkriptkódot, amely több üzenetet hoz létre:

```cs
public static void Run(TimerInfo myTimer, ICollector<string> outputEventHubMessage, ILogger log)
{
    string message = $"Message created at: {DateTime.Now}";
    log.LogInformation(message);
    outputEventHubMessage.Add("1 " + message);
    outputEventHubMessage.Add("2 " + message);
}
```

### <a name="output---f-example"></a>Kimenete – F# példa

Az alábbi példa egy Event hub eseményindító-kötést mutat be egy *function. JSON* fájlban [ F# ](../articles/azure-functions/functions-reference-fsharp.md) , valamint egy olyan függvényt, amely a kötést használja. A függvény egy üzenetet ír az Event hub-ba.

Az alábbi példák a *function. JSON* fájlban Event Hubs kötési adataikat mutatják be. Az első példa a 2. x függvényre, a második pedig az 1. x függvényre mutat. 

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

Íme a F# kódot:

```fsharp
let Run(myTimer: TimerInfo, outputEventHubMessage: byref<string>, log: ILogger) =
    let msg = sprintf "TimerTriggerFSharp1 executed at: %s" DateTime.Now.ToString()
    log.LogInformation(msg);
    outputEventHubMessage <- msg;
```

### <a name="output---javascript-example"></a>Kimenet – JavaScript-példa

Az alábbi példa egy Event hub eseményindító-kötést mutat be egy *function. JSON* fájlban, valamint egy [JavaScript](../articles/azure-functions/functions-reference-node.md) -függvényt, amely a kötést használja. A függvény egy üzenetet ír az Event hub-ba.

Az alábbi példák a *function. JSON* fájlban Event Hubs kötési adataikat mutatják be. Az első példa a 2. x függvényre, a második pedig az 1. x függvényre mutat. 

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

Az alábbi JavaScript-kód egyetlen üzenetet küld:

```javascript
module.exports = function (context, myTimer) {
    var timeStamp = new Date().toISOString();
    context.log('Message created at: ', timeStamp);   
    context.bindings.outputEventHubMessage = "Message created at: " + timeStamp;
    context.done();
};
```

Az alábbi JavaScript-kód több üzenetet küld:

```javascript
module.exports = function(context) {
    var timeStamp = new Date().toISOString();
    var message = 'Message created at: ' + timeStamp;

    context.bindings.outputEventHubMessage = [];

    context.bindings.outputEventHubMessage.push("1 " + message);
    context.bindings.outputEventHubMessage.push("2 " + message);
    context.done();
};
```

### <a name="output---python-example"></a>Kimenet – Python-példa

Az alábbi példa egy Event hub eseményindító-kötést mutat be egy *function. JSON* fájlban, valamint egy olyan [Python](../articles/azure-functions/functions-reference-python.md) -függvényt, amely a kötést használja. A függvény egy üzenetet ír az Event hub-ba.

Az alábbi példák a *function. JSON* fájlban Event Hubs kötési adataikat mutatják be.

```json
{
    "type": "eventHub",
    "name": "$return",
    "eventHubName": "myeventhub",
    "connection": "MyEventHubSendAppSetting",
    "direction": "out"
}
```

A következő Python-kód egyetlen üzenetet küld:

```python
import datetime
import logging
import azure.functions as func

def main(timer: func.TimerRequest) -> str:
    timestamp = datetime.datetime.utcnow()
    logging.info('Message created at: %s', timestamp);   
    return 'Message created at: {}'.format(timestamp)
```

### <a name="output---java-example"></a>Kimenet – Java-példában

Az alábbi példa egy Java-függvényt mutat be, amely az aktuális időponthoz tartozó üzenetet ír egy Event hub-ba.

```java
@}FunctionName("sendTime")
@EventHubOutput(name = "event", eventHubName = "samples-workitems", connection = "AzureEventHubConnection")
public String sendTime(
   @TimerTrigger(name = "sendTimeTrigger", schedule = "0 *&#47;5 * * * *") String timerInfo)  {
     return LocalDateTime.now().toString();
 }
```

A [Java functions runtime library](/java/api/overview/azure/functions/runtime)-ben használja `@EventHubOutput` a Megjegyzés azon paramétereket, amelyek értékét közzé szeretné tenni az Event hub-ban.  A paraméternek olyan típusúnak `OutputBinding<T>` kell lennie, ahol a T egy POJO vagy bármely natív Java-típus.

## <a name="output---attributes"></a>Kimenet – attribútumok

Az [EventHubAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.ServiceBus/EventHubs/EventHubAttribute.cs) attribútumot használja az [ C# osztályok könyvtáraihoz](../articles/azure-functions/functions-dotnet-class-library.md).

Az attribútum konstruktora az Event hub nevét, valamint egy, a kapcsolati karakterláncot tartalmazó Alkalmazásbeállítás nevét veszi át. További információ ezekről a beállításokról: [kimeneti konfiguráció](#output---configuration). Példa egy `EventHub` attribútumra:

```csharp
[FunctionName("EventHubOutput")]
[return: EventHub("outputEventHubMessage", Connection = "EventHubConnectionAppSetting")]
public static string Run([TimerTrigger("0 */5 * * * *")] TimerInfo myTimer, ILogger log)
{
    ...
}
```

Egy teljes példa: [kimenet – C#-példa](#output---c-example).

## <a name="output---configuration"></a>Kimenete – konfiguráció

A következő táblázat ismerteti a megadott kötés konfigurációs tulajdonságaiban a *function.json* fájlt, és a `EventHub` attribútum.

|Function.JSON tulajdonság | Attribútum tulajdonsága |Leírás|
|---------|---------|----------------------|
|**type** | n/a | "EventHub" értékre kell állítani. |
|**direction** | n/a | Állítsa "out". Ez a paraméter automatikusan be van állítva, amikor létrehozza a kötést a Azure Portalban. |
|**name** | n/a | Az eseményt jelölő függvény kódjában használt változó neve. |
|**path** |**EventHubName** | Csak 1. x függvények. Az Event hub neve. Ha az Event hub neve szerepel a kapcsolati sztringben is, ez az érték felülbírálja ezt a tulajdonságot futásidőben. |
|**eventHubName** |**EventHubName** | Csak 2. x funkció. Az Event hub neve. Ha az Event hub neve szerepel a kapcsolati sztringben is, ez az érték felülbírálja ezt a tulajdonságot futásidőben. |
|**kapcsolat** |**kapcsolat** | Az Event hub névteréhez tartozó kapcsolati sztringet tartalmazó Alkalmazásbeállítás neve. Másolja ezt a kapcsolati karakterláncot a *névtér* **kapcsolati adatok** gombjára kattintva, nem az Event hub-t. Ez a kapcsolati karakterláncnak küldési engedéllyel kell rendelkeznie az üzenet küldéséhez az esemény-adatfolyamba.|

[!INCLUDE [app settings to local.settings.json](../articles/azure-functions/../../includes/functions-app-settings-local.md)]

## <a name="output---usage"></a>Kimenet – használat

A C# és C# a szkriptben a (z `out string paramName`) metódus-paraméter (például) használatával küldhet üzeneteket. A C# szkriptben `paramName` a *függvény. JSON* `name` tulajdonságában megadott érték. Több üzenet `ICollector<string>` írásához használhatja a vagy `IAsyncCollector<string>` a helyét `out string`.

A JavaScriptben a használatával `context.bindings.<name>`érheti el a kimeneti eseményt. `<name>`a `name` *függvény. JSON*tulajdonságában megadott érték.

## <a name="exceptions-and-return-codes"></a>Kivételek és a visszatérési kódok

| Kötés | Hivatkozás |
|---|---|
| Eseményközpont | [Üzemeltetési útmutató](https://docs.microsoft.com/rest/api/eventhub/publisher-policy-operations) |

<a name="host-json"></a>  

## <a name="hostjson-settings"></a>Host.JSON-beállítások

Ez a szakasz ismerteti a globális konfigurációs beállításoknak a kötéshez verziójában elérhető 2.x. Az alábbi példa host.json-fájl csak a verzió 2.x beállításait tartalmazza ezt a kötést. További információ a globális konfigurációs beállításoknak verzióban 2.x verzióját, lásd: [verzióját az Azure Functions – host.json referencia 2.x](../articles/azure-functions/functions-host-json.md).

> [!NOTE]
> Az a funkciók host.json odkaz 1.x, lásd: [Azure Functions – host.json referencia 1.x](../articles/azure-functions/functions-host-json-v1.md).

```json
{
    "version": "2.0",
    "extensions": {
        "eventHubs": {
            "batchCheckpointFrequency": 5,
            "eventProcessorOptions": {
                "maxBatchSize": 256,
                "prefetchCount": 512
            }
        }
    }
}  
```

|Tulajdonság  |Alapértelmezett | Leírás |
|---------|---------|---------|
|maxBatchSize|64|A fogadott események száma fogadási hurokban.|
|prefetchCount|n/a|Az alapul szolgáló EventProcessorHost által használt alapértelmezett PrefetchCount.|
|batchCheckpointFrequency|1|Az EventHub kurzor ellenőrzőpontjának létrehozása előtt feldolgozandó eseményvezérelt kötegek száma.|
