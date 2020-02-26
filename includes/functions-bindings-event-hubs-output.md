---
author: craigshoemaker
ms.service: azure-functions
ms.topic: include
ms.date: 02/21/2020
ms.author: cshoe
ms.openlocfilehash: 1e25656b58fe675cfbe87fef75af4fcb174b7f55
ms.sourcegitcommit: 7f929a025ba0b26bf64a367eb6b1ada4042e72ed
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/25/2020
ms.locfileid: "77589739"
---
Az események egy esemény-adatfolyamba való írásához használja a Event Hubs kimeneti kötést. Az Event hub-nak Küldés engedéllyel kell rendelkeznie az események írásához.

A kimeneti kötés megvalósítása előtt győződjön meg arról, hogy a szükséges csomagokra vonatkozó hivatkozások vannak érvényben.

<a id="example" name="example"></a>

# <a name="c"></a>[C#](#tab/csharp)

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

# <a name="c-script"></a>[C#Parancsfájl](#tab/csharp-script)

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

# <a name="javascript"></a>[JavaScript](#tab/javascript)

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

# <a name="python"></a>[Python](#tab/python)

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

# <a name="java"></a>[Java](#tab/java)

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

## <a name="attributes-and-annotations"></a>Attribútumok és jegyzetek

# <a name="c"></a>[C#](#tab/csharp)

Az [EventHubAttribute](https://github.com/Azure/azure-functions-eventhubs-extension/blob/master/src/Microsoft.Azure.WebJobs.Extensions.EventHubs/EventHubAttribute.cs) attribútumot használja az [ C# osztályok könyvtáraihoz](../articles/azure-functions/functions-dotnet-class-library.md).

Az attribútum konstruktora az Event hub nevét, valamint egy, a kapcsolati karakterláncot tartalmazó Alkalmazásbeállítás nevét veszi át. További információ ezekről a beállításokról: [kimeneti konfiguráció](#configuration). Példa `EventHub` attribútumra:

```csharp
[FunctionName("EventHubOutput")]
[return: EventHub("outputEventHubMessage", Connection = "EventHubConnectionAppSetting")]
public static string Run([TimerTrigger("0 */5 * * * *")] TimerInfo myTimer, ILogger log)
{
    ...
}
```

Teljes példa: [kimenet – C# példa](#example).

# <a name="c-script"></a>[C#Parancsfájl](#tab/csharp-script)

Az C# attribútumokat a parancsfájl nem támogatja.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

A JavaScript nem támogatja az attribútumokat.

# <a name="python"></a>[Python](#tab/python)

A Python nem támogatja az attribútumokat.

# <a name="java"></a>[Java](#tab/java)

A [Java functions runtime library](https://docs.microsoft.com/java/api/overview/azure/functions/runtime)-ben használja a [EventHubOutput](https://docs.microsoft.com/java/api/com.microsoft.azure.functions.annotation.eventhuboutput) -jegyzetet azon paramétereknél, amelyek értékét közzé szeretné tenni az Event hub-ban. A paraméternek `OutputBinding<T>` típusúnak kell lennie, ahol a `T` POJO vagy bármely natív Java-típusnak kell lennie.

---

## <a name="configuration"></a>Konfiguráció

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

## <a name="usage"></a>Használat

# <a name="c"></a>[C#](#tab/csharp)

Üzenetek küldése egy metódus-paraméter (például `out string paramName`) használatával. A C# szkriptben `paramName` a *function. JSON*`name` tulajdonságában megadott érték. Több üzenet írásához `ICollector<string>` vagy `IAsyncCollector<string>`t is használhat `out string`helyett.

# <a name="c-script"></a>[C#Parancsfájl](#tab/csharp-script)

Üzenetek küldése egy metódus-paraméter (például `out string paramName`) használatával. A C# szkriptben `paramName` a *function. JSON*`name` tulajdonságában megadott érték. Több üzenet írásához `ICollector<string>` vagy `IAsyncCollector<string>`t is használhat `out string`helyett.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

A kimeneti eseményt `context.bindings.<name>` használatával érheti el, ahol `<name>` a *function. json*`name` tulajdonságában megadott érték.

# <a name="python"></a>[Python](#tab/python)

Az Event hub-üzenetek egy függvényből való üzembe helyezésének két lehetősége van:

- **Visszatérési érték**: állítsa be az `name` tulajdonságot a *function. json* fájlban a `$return`. Ezzel a konfigurációval a függvény visszatérési értéke Event hub-üzenetként is megmarad.

- **Elengedhetetlen**: adjon meg egy értéket a [set](https://docs.microsoft.com/python/api/azure-functions/azure.functions.out?view=azure-python#set-val--t-----none) metódusnak, amely [kimenő](https://docs.microsoft.com/python/api/azure-functions/azure.functions.out?view=azure-python) típusként van deklarálva. Az `set`nek átadott értéket Event hub-üzenetként őrzi meg a rendszer.

# <a name="java"></a>[Java](#tab/java)

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
