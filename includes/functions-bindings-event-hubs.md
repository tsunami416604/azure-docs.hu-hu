---
author: craigshoemaker
ms.service: azure-functions
ms.topic: include
ms.date: 03/05/2019
ms.author: cshoe
ms.openlocfilehash: ec3a7b6420144278df66f693d9fd9933449b3d80
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/24/2020
ms.locfileid: "76748942"
---
## <a name="trigger"></a>Eseményindító

Az Event hub Event streambe küldött eseményre való reagáláshoz használja a függvény eseményindítóját. Az eseményindító beállításához olvasási hozzáféréssel kell rendelkeznie a mögöttes Event hub-hoz. A függvény elindításakor a függvénynek átadott üzenet karakterláncként van beírva.

## <a name="trigger---scaling"></a>Trigger – skálázás

Egy esemény által aktivált függvény minden példányát egyetlen [EventProcessorHost](https://docs.microsoft.com/dotnet/api/microsoft.azure.eventhubs.processor) -példány támogatja. A trigger (Event Hubs) biztosítja, hogy csak egy [EventProcessorHost](https://docs.microsoft.com/dotnet/api/microsoft.azure.eventhubs.processor) -példány kapjon bérletet egy adott partíción.

Vegyünk például egy Event hub-t az alábbiak szerint:

* 10 partíció
* 1 000 az összes partíción egyenletesen elosztott események, 100-es üzenetekkel az egyes partíciók között

A függvény első engedélyezésekor a függvénynek csak egy példánya van. Hívjuk meg az első függvény példányát `Function_0`. A `Function_0` függvény a [EventProcessorHost](https://docs.microsoft.com/dotnet/api/microsoft.azure.eventhubs.processor) egyetlen példányát tartalmazza, amely minden tíz partíción rendelkezik bérlettel. Ez a példány az 0-9-es partíciókhoz származó eseményeket olvas. Ettől a ponttól kezdve a következők egyike történik:

* Az **új függvények példányai nem szükségesek**: a `Function_0` képes feldolgozni az összes 1 000 eseményt, mielőtt a függvények skálázási logikája érvénybe lép. Ebben az esetben a rendszer az összes 1 000 üzenetet dolgozza fel `Function_0`szerint.

* **További függvény-példány hozzáadása**: Ha a függvények skálázási logikája megállapítja, hogy `Function_0` több üzenettel rendelkezik, mint amennyit feldolgozhat, egy új Function App-példány (`Function_1`) jön létre. Ez az új függvény a [EventProcessorHost](https://docs.microsoft.com/dotnet/api/microsoft.azure.eventhubs.processor)társított példányával is rendelkezik. Mivel az alapul szolgáló Event Hubs azt vizsgálja, hogy egy új gazdagép-példány próbálkozik az olvasási üzenetekkel, a terheléselosztás a gazdagép példányai között osztja el a partíciókat. Előfordulhat például, hogy a 0-4-es partíciók hozzá vannak rendelve a `Function_0`hoz, és a 5-9-es partíciót `Function_1`.

* **N további függvény-példányok lettek hozzáadva**: Ha a függvények skálázási logikája megállapítja, hogy mind a `Function_0`, mind a `Function_1` több üzenettel rendelkezik, mint amennyit feldolgozhatnak, az új `Functions_N` Function app-példányok jönnek létre  Az alkalmazások arra a pontra jönnek létre, ahol a `N` nagyobb, mint az Event hub-partíciók száma. A példánkban Event Hubs újra a partíciók terheléselosztását, ebben az esetben a `Function_0`...`Functions_9`ban szereplő példányok között.

Skálázás esetén `N` példányok száma nagyobb, mint az Event hub-partíciók száma. Ez a minta annak biztosítására szolgál, hogy a [EventProcessorHost](https://docs.microsoft.com/dotnet/api/microsoft.azure.eventhubs.processor) példányok elérhetők legyenek a partíciók zárolásának beszerzéséhez, mivel azok más példányokból elérhetővé válnak Csak a függvény példányának végrehajtásakor használt erőforrásokért kell fizetnie. Más szóval nem számítunk fel díjat a kiépítés során.

Ha az összes függvény végrehajtása befejeződött (hibákkal vagy anélkül), a rendszer ellenőrzőpontokat ad hozzá a társított Storage-fiókhoz. Ha a pipa sikeres, a rendszer az összes 1 000 üzenetet soha nem kéri le.

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

Az alábbi példa egy [ C# függvényt](../articles/azure-functions/functions-dotnet-class-library.md) mutat be, amely az Event hub eseményindítójának üzenet törzsét naplózza.

```csharp
[FunctionName("EventHubTriggerCSharp")]
public static void Run([EventHubTrigger("samples-workitems", Connection = "EventHubConnectionAppSetting")] string myEventHubMessage, ILogger log)
{
    log.LogInformation($"C# function triggered to process a message: {myEventHubMessage}");
}
```

Ahhoz, hogy hozzáférést kapjon az [esemény metaadatainak](#trigger---event-metadata) a [EventData](/dotnet/api/microsoft.servicebus.messaging.eventdata) objektumhoz, kötést kell kötnie a (`Microsoft.Azure.EventHubs`) használatához. Ugyanezeket a tulajdonságokat a metódus aláírása kötési kifejezések használatával is elérheti.  Az alábbi példa mindkét módszert mutatja be, hogy ugyanazokat az eszközöket kapja:

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

Ha egy kötegben szeretne eseményeket fogadni, hajtson végre `string` vagy `EventData` egy tömböt.  

> [!NOTE]
> Egy kötegben való fogadáskor nem lehet olyan metódusokhoz kötni, mint például a fenti példában `DateTime enqueuedTimeUtc`, és minden `EventData` objektumból kell megkapnia ezeket.  

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

# <a name="c-scripttabcsharp-script"></a>[C#Parancsfájl](#tab/csharp-script)

Az alábbi példa egy Event hub eseményindító-kötést mutat be egy *function. JSON* fájlban, valamint egy olyan [ C# parancsfájl-függvényt](../articles/azure-functions/functions-reference-csharp.md) , amely a kötést használja. A függvény naplózza az Event hub eseményindítójának üzenet törzsét.

Az alábbi példák a *function. JSON* fájlban Event Hubs kötési adataikat mutatják be.

#### <a name="version-2x-and-higher"></a>2\. x vagy újabb verzió

```json
{
  "type": "eventHubTrigger",
  "name": "myEventHubMessage",
  "direction": "in",
  "eventHubName": "MyEventHub",
  "connection": "myEventHubReadConnectionAppSetting"
}
```

### <a name="version-1x"></a>1\. x verzió

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

Ahhoz, hogy hozzáférést kapjon az [esemény metaadatainak](#trigger---event-metadata) a [EventData](/dotnet/api/microsoft.servicebus.messaging.eventdata) objektumhoz, kötést kell kötnie a (`Microsoft.Azure.EventHubs`) használatához. Ugyanezeket a tulajdonságokat a metódus aláírása kötési kifejezések használatával is elérheti.  Az alábbi példa mindkét módszert mutatja be, hogy ugyanazokat az eszközöket kapja:

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

Ha egy kötegben szeretne eseményeket fogadni, hajtson végre `string` vagy `EventData` egy tömböt:

```cs
public static void Run(string[] eventHubMessages, TraceWriter log)
{
    foreach (var message in eventHubMessages)
    {
        log.Info($"C# function triggered to process a message: {message}");
    }
}
```

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

Az alábbi példa egy Event hub eseményindító-kötést mutat be egy *function. JSON* fájlban, valamint egy [JavaScript-függvényt](../articles/azure-functions/functions-reference-node.md) , amely a kötést használja. A függvény beolvassa az [esemény metaadatait](#trigger---event-metadata) , és naplózza az üzenetet.

Az alábbi példák a *function. JSON* fájlban Event Hubs kötési adataikat mutatják be.

#### <a name="version-2x-and-higher"></a>2\. x vagy újabb verzió

```json
{
  "type": "eventHubTrigger",
  "name": "myEventHubMessage",
  "direction": "in",
  "eventHubName": "MyEventHub",
  "connection": "myEventHubReadConnectionAppSetting"
}
```

### <a name="version-1x"></a>1\. x verzió

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
module.exports = function (context, myEventHubMessage) {
    context.log('Function triggered to process a message: ', myEventHubMessage);
    context.log('EnqueuedTimeUtc =', context.bindingData.enqueuedTimeUtc);
    context.log('SequenceNumber =', context.bindingData.sequenceNumber);
    context.log('Offset =', context.bindingData.offset);

    context.done();
};
```

Ha egy kötegben szeretne eseményeket fogadni, állítsa a `cardinality` `many`re a *function. JSON* fájlban, ahogy az alábbi példákban is látható.

#### <a name="version-2x-and-higher"></a>2\. x vagy újabb verzió

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

### <a name="version-1x"></a>1\. x verzió

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

# <a name="pythontabpython"></a>[Python](#tab/python)

Az alábbi példa egy Event hub eseményindító-kötést mutat be egy *function. JSON* fájlban, valamint egy olyan [Python-függvényt](../articles/azure-functions/functions-reference-python.md) , amely a kötést használja. A függvény beolvassa az [esemény metaadatait](#trigger---event-metadata) , és naplózza az üzenetet.

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

# <a name="javatabjava"></a>[Java](#tab/java)

Az alábbi példa egy Event hub eseményindító-kötést mutat be egy *function. JSON* fájlban, valamint egy olyan [Java-függvényt](../articles/azure-functions/functions-reference-java.md) , amely a kötést használja. A függvény naplózza az Event hub eseményindítójának üzenet törzsét.

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

 A [Java functions runtime library](/java/api/overview/azure/functions/runtime)-ben használja a `EventHubTrigger` megjegyzéseket azon paramétereknél, amelyek értéke az Event hub-ból származik. Az ezekkel a megjegyzésekkel rendelkező paraméterek a függvény futását okozzák, amikor egy esemény érkezik.  Ezt a jegyzetet natív Java-típusokkal, Szerializálói vagy NULL értékű értékekkel lehet használni `Optional<T>`használatával.

 ---

## <a name="trigger---attributes-and-annotations"></a>Trigger – attribútumok és jegyzetek

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

Az [ C# osztályok könyvtáraiban](../articles/azure-functions/functions-dotnet-class-library.md)használja a [EventHubTriggerAttribute](https://github.com/Azure/azure-functions-eventhubs-extension/blob/master/src/Microsoft.Azure.WebJobs.Extensions.EventHubs/EventHubTriggerAttribute.cs) attribútumot.

Az attribútum konstruktora az Event hub nevét, a fogyasztói csoport nevét és a kapcsolati karakterláncot tartalmazó Alkalmazásbeállítás nevét tartalmazza. További információ ezekről a beállításokról: [trigger konfiguráció szakasz](#trigger---configuration). Példa `EventHubTriggerAttribute` attribútumra:

```csharp
[FunctionName("EventHubTriggerCSharp")]
public static void Run([EventHubTrigger("samples-workitems", Connection = "EventHubConnectionAppSetting")] string myEventHubMessage, ILogger log)
{
    ...
}
```

A teljes példa: [trigger- C# example](#trigger).

# <a name="c-scripttabcsharp-script"></a>[C#Parancsfájl](#tab/csharp-script)

Az C# attribútumokat a parancsfájl nem támogatja.

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

A JavaScript nem támogatja az attribútumokat.

# <a name="pythontabpython"></a>[Python](#tab/python)

A Python nem támogatja az attribútumokat.

# <a name="javatabjava"></a>[Java](#tab/java)

A Java [functions futtatókörnyezet könyvtárában](https://docs.microsoft.com/java/api/overview/azure/functions/runtime)használja a [EventHubTrigger](https://docs.microsoft.com/java/api/com.microsoft.azure.functions.annotation.eventhubtrigger) -jegyzetet olyan paramétereknél, amelyek értéke az Event hub-ból származik. Az ezekkel a megjegyzésekkel rendelkező paraméterek a függvény futását okozzák, amikor egy esemény érkezik. Ezt a jegyzetet natív Java-típusokkal, Szerializálói vagy NULL értékű értékekkel lehet használni `Optional<T>`használatával.

---

## <a name="trigger---configuration"></a>Eseményindító - konfiguráció

Az alábbi táblázat a *function. JSON* fájlban és a `EventHubTrigger` attribútumban beállított kötési konfigurációs tulajdonságokat ismerteti.

|Function.JSON tulajdonság | Attribútum tulajdonsága |Leírás|
|---------|---------|----------------------|
|**type** | n/a | `eventHubTrigger`értékre kell állítani. Ez a tulajdonság beállítása automatikusan történik, ha az eseményindítót fog létrehozni az Azure Portalon.|
|**direction** | n/a | `in`értékre kell állítani. Ez a tulajdonság beállítása automatikusan történik, ha az eseményindítót fog létrehozni az Azure Portalon. |
|**név** | n/a | Annak a változónak a neve, amely a függvény kódjában szereplő Event tételt jelöli. |
|**elérési útja** |**EventHubName** | Csak 1. x függvények. Az Event hub neve. Ha az Event hub neve szerepel a kapcsolati sztringben is, ez az érték felülbírálja ezt a tulajdonságot futásidőben. |
|**eventHubName** |**EventHubName** | A 2. x és újabb függvények. Az Event hub neve. Ha az Event hub neve szerepel a kapcsolati sztringben is, ez az érték felülbírálja ezt a tulajdonságot futásidőben. Hivatkozhatók a (z)% eventHubName% alkalmazás beállításain keresztül |
|**consumerGroup** |**ConsumerGroup** | Egy opcionális tulajdonság, amely a központban lévő eseményekre való előfizetéshez használt [fogyasztói csoportot](../articles/event-hubs/event-hubs-features.md#event-consumers) állítja be. Ha nincs megadva, a rendszer a `$Default` fogyasztói csoportot használja. |
|**számosság** | n/a | A Javascripthez. A kötegelt feldolgozás engedélyezéséhez állítsa `many` értékre.  Ha nincs megadva, vagy a `one`értékre van állítva, a függvény egyetlen üzenetet ad át. |
|**kapcsolat** |**Kapcsolat** | Az Event hub névteréhez tartozó kapcsolati sztringet tartalmazó Alkalmazásbeállítás neve. Másolja ezt a kapcsolati karakterláncot a [névtér](../articles/event-hubs/event-hubs-create.md#create-an-event-hubs-namespace) **kapcsolati adatok** gombjára kattintva, nem az Event hub-t. A kapcsolódási karakterláncnak legalább olvasási engedéllyel kell rendelkeznie az trigger aktiválásához.|

[!INCLUDE [app settings to local.settings.json](../articles/azure-functions/../../includes/functions-app-settings-local.md)]

## <a name="trigger---event-metadata"></a>Eseményindító – esemény metaadatainak

A Event Hubs trigger számos [metaadat-tulajdonságot](../articles/azure-functions/./functions-bindings-expressions-patterns.md)biztosít. A metaadatok tulajdonságai más kötésekben vagy a kódban szereplő paraméterek részeként is használhatók. A tulajdonságok a [EventData](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.eventdata) osztályból származnak.

|Tulajdonság|Típus|Leírás|
|--------|----|-----------|
|`PartitionContext`|[PartitionContext](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.partitioncontext)|A `PartitionContext` példány.|
|`EnqueuedTimeUtc`|`DateTime`|A várólistán lévő időpontja (UTC).|
|`Offset`|`string`|Az adateltolás az Event hub-partíciós adatfolyamhoz viszonyítva. Az eltolás a Event Hubs streamen belüli esemény jelölője vagy azonosítója. Az azonosító egyedi az Event Hubs stream egy partícióján belül.|
|`PartitionKey`|`string`|Az a partíció, amelybe az eseményt el kell juttatni.|
|`Properties`|`IDictionary<String,Object>`|Az eseményhez tartozó adatértékek felhasználói tulajdonságai|
|`SequenceNumber`|`Int64`|Az esemény logikai sorszáma.|
|`SystemProperties`|`IDictionary<String,Object>`|A Rendszertulajdonságok, beleértve az eseményre vonatkozó adattípusokat is.|

Tekintse meg a jelen cikk korábbi részében említett tulajdonságokat használó [példákat](#trigger) .

## <a name="trigger---hostjson-properties"></a>Eseményindító - host.json tulajdonságai

A [Host. JSON](../articles/azure-functions/functions-host-json.md#eventhub) fájl olyan beállításokat tartalmaz, amelyek a Event Hubs trigger viselkedését vezérlik.

[!INCLUDE [functions-host-json-event-hubs](../articles/azure-functions/../../includes/functions-host-json-event-hubs.md)]

## <a name="output"></a>Kimenet

Az események egy esemény-adatfolyamba való írásához használja a Event Hubs kimeneti kötést. Az Event hub-nak Küldés engedéllyel kell rendelkeznie az események írásához.

A kimeneti kötés megvalósítása előtt győződjön meg arról, hogy a szükséges csomagokra vonatkozó hivatkozások vannak érvényben.

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

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

Az alábbi példa azt szemlélteti, hogyan lehet a `IAsyncCollector` felületen elküldeni egy köteg üzenetet. Ez a forgatókönyv gyakori, ha egy adott esemény központból érkező üzeneteket dolgoz fel, és az eredményt egy másik Event hub számára küldi el.

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

# <a name="c-scripttabcsharp-script"></a>[C#Parancsfájl](#tab/csharp-script)

Az alábbi példa egy Event hub eseményindító-kötést mutat be egy *function. JSON* fájlban, valamint egy olyan [ C# parancsfájl-függvényt](../articles/azure-functions/functions-reference-csharp.md) , amely a kötést használja. A függvény egy üzenetet ír az Event hub-ba.

Az alábbi példák a *function. JSON* fájlban Event Hubs kötési adataikat mutatják be. Az első példa a 2. x és újabb függvények esetében, a második pedig az 1. x függvényre mutat. 

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

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

Az alábbi példa egy Event hub eseményindító-kötést mutat be egy *function. JSON* fájlban, valamint egy [JavaScript-függvényt](../articles/azure-functions/functions-reference-node.md) , amely a kötést használja. A függvény egy üzenetet ír az Event hub-ba.

Az alábbi példák a *function. JSON* fájlban Event Hubs kötési adataikat mutatják be. Az első példa a 2. x és újabb függvények esetében, a második pedig az 1. x függvényre mutat. 

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

# <a name="pythontabpython"></a>[Python](#tab/python)

Az alábbi példa egy Event hub eseményindító-kötést mutat be egy *function. JSON* fájlban, valamint egy olyan [Python-függvényt](../articles/azure-functions/functions-reference-python.md) , amely a kötést használja. A függvény egy üzenetet ír az Event hub-ba.

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
    logging.info('Message created at: %s', timestamp)
    return 'Message created at: {}'.format(timestamp)
```

# <a name="javatabjava"></a>[Java](#tab/java)

Az alábbi példa egy Java-függvényt mutat be, amely egy Event hub aktuális időpontját tartalmazó üzenetet ír.

```java
@FunctionName("sendTime")
@EventHubOutput(name = "event", eventHubName = "samples-workitems", connection = "AzureEventHubConnection")
public String sendTime(
   @TimerTrigger(name = "sendTimeTrigger", schedule = "0 */5 * * * *") String timerInfo)  {
     return LocalDateTime.now().toString();
 }
```

A [Java functions runtime library](/java/api/overview/azure/functions/runtime)-ben használja a `@EventHubOutput` Megjegyzés azon paramétereket, amelyek értékét közzé szeretné tenni az Event hub-ban.  A paraméternek `OutputBinding<T>` típusúnak kell lennie, ahol a T egy POJO vagy bármilyen natív Java-típusnak kell lennie.

---

## <a name="output---attributes-and-annotations"></a>Kimenet – attribútumok és jegyzetek

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

Az [EventHubAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.ServiceBus/EventHubs/EventHubAttribute.cs) attribútumot használja az [ C# osztályok könyvtáraihoz](../articles/azure-functions/functions-dotnet-class-library.md).

Az attribútum konstruktora az Event hub nevét, valamint egy, a kapcsolati karakterláncot tartalmazó Alkalmazásbeállítás nevét veszi át. További információ ezekről a beállításokról: [kimeneti konfiguráció](#output---configuration). Példa `EventHub` attribútumra:

```csharp
[FunctionName("EventHubOutput")]
[return: EventHub("outputEventHubMessage", Connection = "EventHubConnectionAppSetting")]
public static string Run([TimerTrigger("0 */5 * * * *")] TimerInfo myTimer, ILogger log)
{
    ...
}
```

Teljes példa: [kimenet – C# példa](#output).

# <a name="c-scripttabcsharp-script"></a>[C#Parancsfájl](#tab/csharp-script)

Az C# attribútumokat a parancsfájl nem támogatja.

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

A JavaScript nem támogatja az attribútumokat.

# <a name="pythontabpython"></a>[Python](#tab/python)

A Python nem támogatja az attribútumokat.

# <a name="javatabjava"></a>[Java](#tab/java)

A [Java functions runtime library](https://docs.microsoft.com/java/api/overview/azure/functions/runtime)-ben használja a [EventHubOutput](https://docs.microsoft.com/java/api/com.microsoft.azure.functions.annotation.eventhuboutput) -jegyzetet azon paramétereknél, amelyek értékét közzé szeretné tenni az Event hub-ban. A paraméternek `OutputBinding<T>` típusúnak kell lennie, ahol a `T` POJO vagy bármely natív Java-típusnak kell lennie.

---

## <a name="output---configuration"></a>Kimenete – konfiguráció

Az alábbi táblázat a *function. JSON* fájlban és a `EventHub` attribútumban beállított kötési konfigurációs tulajdonságokat ismerteti.

|Function.JSON tulajdonság | Attribútum tulajdonsága |Leírás|
|---------|---------|----------------------|
|**type** | n/a | "EventHub" értékre kell állítani. |
|**direction** | n/a | Állítsa "out". Ez a paraméter automatikusan be van állítva, amikor létrehozza a kötést a Azure Portalban. |
|**név** | n/a | Az eseményt jelölő függvény kódjában használt változó neve. |
|**elérési útja** |**EventHubName** | Csak 1. x függvények. Az Event hub neve. Ha az Event hub neve szerepel a kapcsolati sztringben is, ez az érték felülbírálja ezt a tulajdonságot futásidőben. |
|**eventHubName** |**EventHubName** | A 2. x és újabb függvények. Az Event hub neve. Ha az Event hub neve szerepel a kapcsolati sztringben is, ez az érték felülbírálja ezt a tulajdonságot futásidőben. |
|**kapcsolat** |**Kapcsolat** | Az Event hub névteréhez tartozó kapcsolati sztringet tartalmazó Alkalmazásbeállítás neve. Másolja ezt a kapcsolati karakterláncot a *névtér* **kapcsolati adatok** gombjára kattintva, nem az Event hub-t. Ez a kapcsolati karakterláncnak küldési engedéllyel kell rendelkeznie az üzenet küldéséhez az esemény-adatfolyamba.|

[!INCLUDE [app settings to local.settings.json](../articles/azure-functions/../../includes/functions-app-settings-local.md)]

## <a name="output---usage"></a>Kimenet – használat

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

Üzenetek küldése egy metódus-paraméter (például `out string paramName`) használatával. A C# szkriptben `paramName` a *function. JSON*`name` tulajdonságában megadott érték. Több üzenet írásához `ICollector<string>` vagy `IAsyncCollector<string>`t is használhat `out string`helyett.

# <a name="c-scripttabcsharp-script"></a>[C#Parancsfájl](#tab/csharp-script)

Üzenetek küldése egy metódus-paraméter (például `out string paramName`) használatával. A C# szkriptben `paramName` a *function. JSON*`name` tulajdonságában megadott érték. Több üzenet írásához `ICollector<string>` vagy `IAsyncCollector<string>`t is használhat `out string`helyett.

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

A kimeneti eseményt `context.bindings.<name>` használatával érheti el, ahol `<name>` a *function. json*`name` tulajdonságában megadott érték.

# <a name="pythontabpython"></a>[Python](#tab/python)

Az Event hub-üzenetek egy függvényből való üzembe helyezésének két lehetősége van:

- **Visszatérési érték**: állítsa be az `name` tulajdonságot a *function. json* fájlban a `$return`. Ezzel a konfigurációval a függvény visszatérési értéke Event hub-üzenetként is megmarad.

- **Elengedhetetlen**: adjon meg egy értéket a [set](https://docs.microsoft.com/python/api/azure-functions/azure.functions.out?view=azure-python#set-val--t-----none) metódusnak, amely [kimenő](https://docs.microsoft.com/python/api/azure-functions/azure.functions.out?view=azure-python) típusként van deklarálva. Az `set`nek átadott értéket Event hub-üzenetként őrzi meg a rendszer.

# <a name="javatabjava"></a>[Java](#tab/java)

Az Event hub-üzenetek egy függvényből való üzembe helyezésére két lehetőség áll rendelkezésre a [EventHubOutput](https://docs.microsoft.com/java/api/com.microsoft.azure.functions.annotation.eventhuboutput) jegyzet használatával:

- Visszaadott **érték**: Ha a jegyzetet a függvényhez alkalmazza, a függvény visszatérési értéke Event hub-üzenetként marad.

- **Fontos**: Ha explicit módon be szeretné állítani az üzenet értékét, alkalmazza a jegyzetet a [`OutputBinding<T>`](https://docs.microsoft.com/java/api/com.microsoft.azure.functions.OutputBinding)típusú adott paraméterre, ahol a `T` POJO vagy bármely natív Java-típus. Ezzel a konfigurációval a `setValue` metódus értékének átadásával az érték az Event hub üzenete marad.

---

## <a name="exceptions-and-return-codes"></a>Kivételek és a visszatérési kódok

| Kötés | Referencia |
|---|---|
| Eseményközpont | [Üzemeltetési útmutató](https://docs.microsoft.com/rest/api/eventhub/publisher-policy-operations) |

<a name="host-json"></a>  

## <a name="hostjson-settings"></a>Host.JSON-beállítások

Ez a szakasz a kötéshez elérhető globális konfigurációs beállításokat ismerteti 2. x vagy újabb verziókban. A következő példa a Host. JSON fájlt tartalmazza, csak a 2. x + beállításokat a kötéshez. További információ a 2. x verzióban található globális konfigurációs beállításokról: a [Host. JSON dokumentációja Azure functions](../articles/azure-functions/functions-host-json.md).

> [!NOTE]
> Az 1. x függvények Host. JSON fájljának hivatkozását lásd: [Host. JSON-dokumentáció Azure functions 1. x-hez](../articles/azure-functions/functions-host-json-v1.md).

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
|`maxBatchSize`|10|A fogadott események száma fogadási hurokban.|
|`prefetchCount`|300|Az alapul szolgáló `EventProcessorHost`által használt alapértelmezett előzetes lehívási szám.|
|`batchCheckpointFrequency`|1|Az EventHub kurzor ellenőrzőpontjának létrehozása előtt feldolgozandó eseményvezérelt kötegek száma.|
