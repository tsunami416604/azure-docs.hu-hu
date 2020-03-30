---
author: craigshoemaker
ms.service: azure-functions
ms.topic: include
ms.date: 03/05/2019
ms.author: cshoe
ms.openlocfilehash: 438e3166e27511780dd871b5076a7b28ebade052
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77589713"
---
A függvényesemény-eseményindító segítségével válaszolhat egy eseményközpont eseményfolyamba küldött eseményre. Az eseményindító beállításához olvasási hozzáféréssel kell rendelkeznie az alapul szolgáló eseményközponthoz. A függvény aktiválásakor a függvénynek átadott üzenet karakterláncként lesz beírva.

## <a name="scaling"></a>Méretezés

Az esemény aktivált függvényminden példányát egyetlen [EventProcessorHost](https://docs.microsoft.com/dotnet/api/microsoft.azure.eventhubs.processor) példány támogatja. Az eseményindító (az Event Hubs-on hajtva) biztosítja, hogy csak egy [EventProcessorHost](https://docs.microsoft.com/dotnet/api/microsoft.azure.eventhubs.processor) példány kap egy bérletet egy adott partíción.

Vegyünk például egy eseményközpontot a következőképpen:

* 10 partíció
* 1000 esemény egyenletesen elosztva az összes partícióközött, 100 üzenettel minden partícióban

Amikor a függvény először engedélyezve van, a függvénynek csak egy példánya van. Hívjuk az első függvénypéldányt. `Function_0` A `Function_0` függvény rendelkezik az [EventProcessorHost](https://docs.microsoft.com/dotnet/api/microsoft.azure.eventhubs.processor) egyetlen példányával, amely mind a tíz partíción rendelkezik bérlettel. Ez a példány a 0-9 partíciók eseményeit olvassa. Ettől a ponttól kezdve, az alábbiak egyike történik:

* **Nincs szükség új függvénypéldányokra:** `Function_0` képes feldolgozni az összes 1000 eseményt, mielőtt a Függvények méretezési logika érvénybe lépne. Ebben az esetben az összes 1000 `Function_0`üzenetet a .

* **Egy további függvénypéldány kerül hozzáadásra:** Ha `Function_0` a Függvények méretezési logika azt állapítja`Function_1`meg, hogy több üzenetet tartalmaz, mint amennyit fel tud dolgozni, egy új függvényalkalmazás-példány ( ) jön létre. Ez az új függvény az [EventProcessorHost](https://docs.microsoft.com/dotnet/api/microsoft.azure.eventhubs.processor)társított példányával is rendelkezik. Ahogy az alapul szolgáló Eseményközpontok észlelik, hogy egy új állomáspéldány megpróbálja olvasni az üzeneteket, a terhelés kiegyensúlyozza a partíciókat a gazdapéldányok között. Például a 0-4 partíciók hozzárendelhetők, `Function_0` az 5-9-es partíciók pedig a hoz rendelhetők `Function_1`hozzá.

* **N további függvénypéldányok kerülnek hozzáadásra:** Ha a `Function_0` `Function_1` Függvények méretezési logika azt `Functions_N` állapítja meg, hogy mindkettő, és több üzenetet tartalmaznak, mint amennyit fel tudnak dolgozni, új függvényalkalmazás-példányok jönnek létre.  Az alkalmazások addig a `N` pontig jönnek létre, ahol nagyobb, mint az eseményközpont-partíciók száma. A mi példánkban az Event Hubs ismét terhelési kiegyensúlyozza a partíciókat, ebben az esetben az esetekben `Function_0`... `Functions_9`.

Méretezés esetén a `N` példányok száma nagyobb, mint az eseményközpont-partíciók száma. Ez a minta annak biztosítására szolgál, hogy az [EventProcessorHost](https://docs.microsoft.com/dotnet/api/microsoft.azure.eventhubs.processor) példányok elérhetők legyenek a partíciók zárolásainak megszerzéséhez, amint azok más példányokból válnak elérhetővé. Csak a függvénypéldány végrehajtásakor használt erőforrásokért kell fizetnie. Más szóval, nem kell fizetnie a túlzott kiépítésért.

Ha az összes függvény-végrehajtás befejeződik (hibákkal vagy anélkül), ellenőrzőpontok hozzáadódnak a társított tárfiókhoz. Ha az ellenőrzés sikeres, az összes 1000 üzenet soha nem lesz beolvasva.

<a id="example" name="example"></a>

# <a name="c"></a>[C #](#tab/csharp)

A következő példa egy [C# függvényt](../articles/azure-functions/functions-dotnet-class-library.md) mutat be, amely naplózza az eseményközpont-eseményindító üzenettörzsét.

```csharp
[FunctionName("EventHubTriggerCSharp")]
public static void Run([EventHubTrigger("samples-workitems", Connection = "EventHubConnectionAppSetting")] string myEventHubMessage, ILogger log)
{
    log.LogInformation($"C# function triggered to process a message: {myEventHubMessage}");
}
```

Az esemény [metaadataihoz](#event-metadata) való hozzáféréshez a függvénykódban, kössön `Microsoft.Azure.EventHubs`egy [EventData](/dotnet/api/microsoft.servicebus.messaging.eventdata) objektumhoz (használati utasítás szükséges a rendszerhez). Ugyanezeket a tulajdonságokat kötési kifejezések használatával is elérheti a metódus aláírásában.  A következő példa bemutatja, hogy miként szerezheti be ugyanazokat az adatokat:

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

Események kötegben való fogadásához készítsen `string` vagy `EventData` tömböt.  

> [!NOTE]
> Ha kötegben érkezik, nem köthető a fenti példában szereplőhez hasonló metódusparaméterekhez, `DateTime enqueuedTimeUtc` és ezeket minden objektumból meg kell kapnia, `EventData` és ezeket meg kell kapnia  

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

# <a name="c-script"></a>[C# parancsfájl](#tab/csharp-script)

A következő példa egy eseményközpont-eseményindító tanusítását mutatja be egy *function.json* fájlban, és egy [C# parancsfájlfüggvényt,](../articles/azure-functions/functions-reference-csharp.md) amely a kötést használja. A függvény naplózza az eseményközpont-eseményindító üzenettörzsét.

Az alábbi példák az Event Hubs kötési adatait mutatják be a *function.json* fájlban.

### <a name="version-2x-and-higher"></a>2.x-es vagy újabb verzió

```json
{
  "type": "eventHubTrigger",
  "name": "myEventHubMessage",
  "direction": "in",
  "eventHubName": "MyEventHub",
  "connection": "myEventHubReadConnectionAppSetting"
}
```

### <a name="version-1x"></a>1.x verzió

```json
{
  "type": "eventHubTrigger",
  "name": "myEventHubMessage",
  "direction": "in",
  "path": "MyEventHub",
  "connection": "myEventHubReadConnectionAppSetting"
}
```

Itt a C# script kód:

```cs
using System;

public static void Run(string myEventHubMessage, TraceWriter log)
{
    log.Info($"C# function triggered to process a message: {myEventHubMessage}");
}
```

Az esemény [metaadataihoz](#event-metadata) való hozzáféréshez a függvénykódban, kössön `Microsoft.Azure.EventHubs`egy [EventData](/dotnet/api/microsoft.servicebus.messaging.eventdata) objektumhoz (használati utasítás szükséges a rendszerhez). Ugyanezeket a tulajdonságokat kötési kifejezések használatával is elérheti a metódus aláírásában.  A következő példa bemutatja, hogy miként szerezheti be ugyanazokat az adatokat:

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

Események kötegben való fogadásához készítse el `string` vagy `EventData` tömbje legyen:

```cs
public static void Run(string[] eventHubMessages, TraceWriter log)
{
    foreach (var message in eventHubMessages)
    {
        log.Info($"C# function triggered to process a message: {message}");
    }
}
```

# <a name="javascript"></a>[Javascript](#tab/javascript)

A következő példa egy eseményközpont-eseményindító-kötést mutat be egy *function.json* fájlban, és egy [JavaScript-függvényt,](../articles/azure-functions/functions-reference-node.md) amely a kötést használja. A függvény beolvassa [az esemény metaadatait,](#event-metadata) és naplózza az üzenetet.

Az alábbi példák az Event Hubs kötési adatait mutatják be a *function.json* fájlban.

### <a name="version-2x-and-higher"></a>2.x-es vagy újabb verzió

```json
{
  "type": "eventHubTrigger",
  "name": "myEventHubMessage",
  "direction": "in",
  "eventHubName": "MyEventHub",
  "connection": "myEventHubReadConnectionAppSetting"
}
```

### <a name="version-1x"></a>1.x verzió

```json
{
  "type": "eventHubTrigger",
  "name": "myEventHubMessage",
  "direction": "in",
  "path": "MyEventHub",
  "connection": "myEventHubReadConnectionAppSetting"
}
```

Itt a JavaScript-kód:

```javascript
module.exports = function (context, myEventHubMessage) {
    context.log('Function triggered to process a message: ', myEventHubMessage);
    context.log('EnqueuedTimeUtc =', context.bindingData.enqueuedTimeUtc);
    context.log('SequenceNumber =', context.bindingData.sequenceNumber);
    context.log('Offset =', context.bindingData.offset);

    context.done();
};
```

Események fogadásához egy kötegben, állítsa `cardinality` `many` be a *function.json* fájlban, ahogy az alábbi példákban látható.

### <a name="version-2x-and-higher"></a>2.x-es vagy újabb verzió

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

### <a name="version-1x"></a>1.x verzió

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

Itt a JavaScript-kód:

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

# <a name="python"></a>[Python](#tab/python)

A következő példa egy eseményközpont-eseményindító tincsét mutatja be egy *function.json* fájlban, és egy [Python-függvényt,](../articles/azure-functions/functions-reference-python.md) amely a kötést használja. A függvény beolvassa [az esemény metaadatait,](#event-metadata) és naplózza az üzenetet.

Az alábbi példák az Event Hubs kötési adatait mutatják be a *function.json* fájlban.

```json
{
  "type": "eventHubTrigger",
  "name": "event",
  "direction": "in",
  "eventHubName": "MyEventHub",
  "connection": "myEventHubReadConnectionAppSetting"
}
```

Itt a Python kód:

```python
import logging
import azure.functions as func


def main(event: func.EventHubEvent):
    logging.info('Function triggered to process a message: ', event.get_body())
    logging.info('  EnqueuedTimeUtc =', event.enqueued_time)
    logging.info('  SequenceNumber =', event.sequence_number)
    logging.info('  Offset =', event.offset)
```

# <a name="java"></a>[Java](#tab/java)

A következő példa egy Eseményközpont-eseményindító-kötést mutat be, amely naplózza az Event Hub eseményindító üzenettörzsét.

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

 A [Java függvények futásidejű függvénytárában](/java/api/overview/azure/functions/runtime)használja a jegyzetet azon `EventHubTrigger` paramétereken, amelyek értéke az Event Hubból származna. Az ezekkel a jegyzetekkel rendelkező paraméterek hatására a függvény akkor fog futni, amikor egy esemény megérkezik.  Ez a jegyzet használható natív Java-típusok, POJOs vagy `Optional<T>`nullable értékek segítségével.

 ---

## <a name="attributes-and-annotations"></a>Attribútumok és jegyzetek

# <a name="c"></a>[C #](#tab/csharp)

A [C# osztálytárakban](../articles/azure-functions/functions-dotnet-class-library.md)használja az [EventHubTriggerAttribute](https://github.com/Azure/azure-functions-eventhubs-extension/blob/master/src/Microsoft.Azure.WebJobs.Extensions.EventHubs/EventHubTriggerAttribute.cs) attribútumot.

Az attribútum konstruktora felveszi az eseményközpont nevét, a fogyasztói csoport nevét és a kapcsolati karakterláncot tartalmazó alkalmazásbeállítás nevét. Ezekről a beállításokról további információt az [eseményindító konfigurációs szakaszában talál.](#configuration) Íme egy `EventHubTriggerAttribute` attribútum példa:

```csharp
[FunctionName("EventHubTriggerCSharp")]
public static void Run([EventHubTrigger("samples-workitems", Connection = "EventHubConnectionAppSetting")] string myEventHubMessage, ILogger log)
{
    ...
}
```

Egy teljes példa, [lásd: Trigger - C# példa.](#example)

# <a name="c-script"></a>[C# parancsfájl](#tab/csharp-script)

Az attribútumokat a C# script nem támogatja.

# <a name="javascript"></a>[Javascript](#tab/javascript)

A JavaScript nem támogatja az attribútumokat.

# <a name="python"></a>[Python](#tab/python)

Az attribútumokat a Python nem támogatja.

# <a name="java"></a>[Java](#tab/java)

A [Java-függvények futásidejű függvénytár,](https://docs.microsoft.com/java/api/overview/azure/functions/runtime)használja az [EventHubTrigger](https://docs.microsoft.com/java/api/com.microsoft.azure.functions.annotation.eventhubtrigger) jegyzetelés a paramétereket, amelyek értéke az Event Hub származna. Az ezekkel a jegyzetekkel rendelkező paraméterek hatására a függvény akkor fog futni, amikor egy esemény megérkezik. Ez a jegyzet használható natív Java-típusok, POJOs vagy `Optional<T>`nullable értékek segítségével.

---

## <a name="configuration"></a>Konfiguráció

Az alábbi táblázat a *function.json* fájlban és az `EventHubTrigger` attribútumban beállított kötési konfigurációs tulajdonságokat ismerteti.

|function.json tulajdonság | Attribútum tulajdonság |Leírás|
|---------|---------|----------------------|
|**Típus** | n/a | A beállításnak `eventHubTrigger`a beállítására kell beállítható. Ez a tulajdonság automatikusan be van állítva, amikor létrehozza az eseményindítót az Azure Portalon.|
|**direction** | n/a | A beállításnak `in`a beállítására kell beállítható. Ez a tulajdonság automatikusan be van állítva, amikor létrehozza az eseményindítót az Azure Portalon. |
|**név** | n/a | Annak a változónak a neve, amely az eseményelemet jelöli a függvénykódban. |
|**Elérési út** |**EventHubName** | Csak 1.x függvényt. Az eseményközpont neve. Ha az eseményközpont neve is szerepel a kapcsolati karakterláncban, ez az érték felülbírálja ezt a tulajdonságot futásidőben. |
|**eventHubName** |**EventHubName** | Funkciók 2.x és magasabb. Az eseményközpont neve. Ha az eseményközpont neve is szerepel a kapcsolati karakterláncban, ez az érték felülbírálja ezt a tulajdonságot futásidőben. Az alkalmazásbeállításokon keresztül (%eventHubName% ) hivatkozhat |
|**consumerGroup (fogyasztói csoport)** |**Fogyasztói csoport** | Egy választható tulajdonság, amely beállítja a központi eseményekre való előfizetéshez használt [fogyasztói csoportot.](../articles/event-hubs/event-hubs-features.md#event-consumers) Ha nincs megadva, a `$Default` fogyasztói csoportot használja a használat. |
|**Számossága** | n/a | Javascript esetén. `many` A kötegelés engedélyezéséhez állítsa be.  Ha nincs megadva `one`vagy be van állítva, a függvény egyetlen üzenetet küld a függvénynek. |
|**Kapcsolat** |**Kapcsolat** | Az eseményközpont névteréhez való kapcsolati karakterláncot tartalmazó alkalmazásbeállítás neve. Másolja a kapcsolati karakterláncot úgy, hogy a [névtér](../articles/event-hubs/event-hubs-create.md#create-an-event-hubs-namespace) **Kapcsolatinformáció** gombjára kattint, nem magára az eseményközpontra. Ennek a kapcsolati karakterláncnak legalább olvasási engedéllyel kell rendelkeznie az eseményindító aktiválásához.|

[!INCLUDE [app settings to local.settings.json](../articles/azure-functions/../../includes/functions-app-settings-local.md)]

## <a name="event-metadata"></a>Esemény metaadatai

Az Eseményközpontok eseményindító jatt mindkét [metaadat-tulajdonságot](../articles/azure-functions/./functions-bindings-expressions-patterns.md)tartalmazza. A metaadat-tulajdonságok más kötésekben lévő kötési kifejezések részeként vagy a kódban lévő paraméterekként használhatók. A tulajdonságok az [EventData](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.eventdata) osztályból származnak.

|Tulajdonság|Típus|Leírás|
|--------|----|-----------|
|`PartitionContext`|[PartitionContext](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.partitioncontext)|A `PartitionContext` példány.|
|`EnqueuedTimeUtc`|`DateTime`|A várólistán lévő idő utc-ben.|
|`Offset`|`string`|Az adatok eltolása az Event Hub partícióstreamhez viszonyítva. Az eltolás az Eseményközpontok adatfolyamon belüli esemény jelölője vagy azonosítója. Az azonosító az Eseményközpontok adatfolyamának partícióján belül egyedi.|
|`PartitionKey`|`string`|Az a partíció, amelyre az eseményadatokat el kell küldeni.|
|`Properties`|`IDictionary<String,Object>`|Az eseményadatok felhasználói tulajdonságai.|
|`SequenceNumber`|`Int64`|Az esemény logikai sorszáma.|
|`SystemProperties`|`IDictionary<String,Object>`|A rendszer tulajdonságai, beleértve az eseményadatokat is.|

Tekintse meg a cikk korábbi, ezeket a tulajdonságokat használó [példákat.](#example)

## <a name="hostjson-properties"></a>host.json tulajdonságok

A [host.json](../articles/azure-functions/functions-host-json.md#eventhub) fájl olyan beállításokat tartalmaz, amelyek szabályozzák az Event Hubs eseményindítójának viselkedését.

[!INCLUDE [functions-host-json-event-hubs](../articles/azure-functions/../../includes/functions-host-json-event-hubs.md)]