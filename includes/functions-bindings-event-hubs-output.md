---
author: craigshoemaker
ms.service: azure-functions
ms.topic: include
ms.date: 02/21/2020
ms.author: cshoe
ms.openlocfilehash: 1e25656b58fe675cfbe87fef75af4fcb174b7f55
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77589739"
---
Az Event Hubs kimeneti kötés használatával eseményeket írhat egy eseményfolyamba. Küldési engedéllyel kell rendelkeznie ahhoz, hogy eseményeket írhasson egy eseményközpontba.

Mielőtt megpróbálna kimeneti kötést végrehajtani, győződjön meg arról, hogy a szükséges csomaghivatkozások a helyükön vannak.

<a id="example" name="example"></a>

# <a name="c"></a>[C #](#tab/csharp)

A következő példa egy [C# függvényt](../articles/azure-functions/functions-dotnet-class-library.md) mutat be, amely üzenetet ír egy eseményközpontba, a metódus visszatérési értékét használva kimenetként:

```csharp
[FunctionName("EventHubOutput")]
[return: EventHub("outputEventHubMessage", Connection = "EventHubConnectionAppSetting")]
public static string Run([TimerTrigger("0 */5 * * * *")] TimerInfo myTimer, ILogger log)
{
    log.LogInformation($"C# Timer trigger function executed at: {DateTime.Now}");
    return $"{DateTime.Now}";
}
```

A következő példa bemutatja, hogyan használhatja a `IAsyncCollector` felületet egy köteg üzenet küldésére. Ez a forgatókönyv gyakori, ha az egyik Event Hubról érkező üzeneteket dolgozza fel, és az eredményt egy másik Eseményközpontnak küldi.

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

# <a name="c-script"></a>[C# parancsfájl](#tab/csharp-script)

A következő példa egy eseményközpont-eseményindító tanusítását mutatja be egy *function.json* fájlban, és egy [C# parancsfájlfüggvényt,](../articles/azure-functions/functions-reference-csharp.md) amely a kötést használja. A függvény üzenetet ír egy eseményközpontba.

Az alábbi példák az Event Hubs kötési adatait mutatják be a *function.json* fájlban. Az első példa a 2.x vagy újabb függvények, a második pedig az 1.x függvények. 

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

Itt a C# parancsfájlkód, amely létrehoz egy üzenetet:

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

Itt a C# parancsfájlkód, amely több üzenetet hoz létre:

```cs
public static void Run(TimerInfo myTimer, ICollector<string> outputEventHubMessage, ILogger log)
{
    string message = $"Message created at: {DateTime.Now}";
    log.LogInformation(message);
    outputEventHubMessage.Add("1 " + message);
    outputEventHubMessage.Add("2 " + message);
}
```

# <a name="javascript"></a>[Javascript](#tab/javascript)

A következő példa egy eseményközpont-eseményindító-kötést mutat be egy *function.json* fájlban, és egy [JavaScript-függvényt,](../articles/azure-functions/functions-reference-node.md) amely a kötést használja. A függvény üzenetet ír egy eseményközpontba.

Az alábbi példák az Event Hubs kötési adatait mutatják be a *function.json* fájlban. Az első példa a 2.x vagy újabb függvények, a második pedig az 1.x függvények. 

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

Itt van a JavaScript-kód, amely egyetlen üzenetet küld:

```javascript
module.exports = function (context, myTimer) {
    var timeStamp = new Date().toISOString();
    context.log('Message created at: ', timeStamp);   
    context.bindings.outputEventHubMessage = "Message created at: " + timeStamp;
    context.done();
};
```

Itt van a JavaScript-kód, amely több üzenetet küld:

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

A következő példa egy eseményközpont-eseményindító tincsét mutatja be egy *function.json* fájlban, és egy [Python-függvényt,](../articles/azure-functions/functions-reference-python.md) amely a kötést használja. A függvény üzenetet ír egy eseményközpontba.

Az alábbi példák az Event Hubs kötési adatait mutatják be a *function.json* fájlban.

```json
{
    "type": "eventHub",
    "name": "$return",
    "eventHubName": "myeventhub",
    "connection": "MyEventHubSendAppSetting",
    "direction": "out"
}
```

Az alábbiakban python-kódot küld, amely egyetlen üzenetet küld:

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

A következő példa egy Java-függvényt mutat be, amely az aktuális időt tartalmazó üzenetet ír egy eseményközpontba.

```java
@FunctionName("sendTime")
@EventHubOutput(name = "event", eventHubName = "samples-workitems", connection = "AzureEventHubConnection")
public String sendTime(
   @TimerTrigger(name = "sendTimeTrigger", schedule = "0 */5 * * * *") String timerInfo)  {
     return LocalDateTime.now().toString();
 }
```

A [Java függvények futásidejű függvénytárában](/java/api/overview/azure/functions/runtime)használja a jegyzetet azon `@EventHubOutput` paramétereken, amelyek értéke közzé lenne téve az Event Hub on.  A paraméter nek `OutputBinding<T>` típusúnak kell lennie, ahol T POJO vagy bármilyen natív Java típus.

---

## <a name="attributes-and-annotations"></a>Attribútumok és jegyzetek

# <a name="c"></a>[C #](#tab/csharp)

C# [osztálytárak esetén](../articles/azure-functions/functions-dotnet-class-library.md)használja az [EventHubAttribute](https://github.com/Azure/azure-functions-eventhubs-extension/blob/master/src/Microsoft.Azure.WebJobs.Extensions.EventHubs/EventHubAttribute.cs) attribútumot.

Az attribútum konstruktora felveszi az eseményközpont nevét és a kapcsolati karakterláncot tartalmazó alkalmazásbeállítás nevét. Ezekről a beállításokról további információt a [Kimenet - konfiguráció](#configuration)című témakörben talál. Íme egy `EventHub` attribútum példa:

```csharp
[FunctionName("EventHubOutput")]
[return: EventHub("outputEventHubMessage", Connection = "EventHubConnectionAppSetting")]
public static string Run([TimerTrigger("0 */5 * * * *")] TimerInfo myTimer, ILogger log)
{
    ...
}
```

Egy teljes példa, lásd: [Kimenet - C# példa](#example).

# <a name="c-script"></a>[C# parancsfájl](#tab/csharp-script)

Az attribútumokat a C# script nem támogatja.

# <a name="javascript"></a>[Javascript](#tab/javascript)

A JavaScript nem támogatja az attribútumokat.

# <a name="python"></a>[Python](#tab/python)

Az attribútumokat a Python nem támogatja.

# <a name="java"></a>[Java](#tab/java)

A [Java függvények futásidejű függvénytárban](https://docs.microsoft.com/java/api/overview/azure/functions/runtime)használja az [EventHubOutput-annotációt](https://docs.microsoft.com/java/api/com.microsoft.azure.functions.annotation.eventhuboutput) azon paramétereken, amelyek értéke közzé lenne téve az Event Hubban. A paraméter nek `OutputBinding<T>` típusúnak `T` kell lennie, ahol POJO vagy bármely natív Java típus.

---

## <a name="configuration"></a>Konfiguráció

Az alábbi táblázat a *function.json* fájlban és az `EventHub` attribútumban beállított kötési konfigurációs tulajdonságokat ismerteti.

|function.json tulajdonság | Attribútum tulajdonság |Leírás|
|---------|---------|----------------------|
|**Típus** | n/a | "eventHub" (eventHub) beállításra kell beállítva. |
|**direction** | n/a | Be kell állítani, hogy "ki". Ez a paraméter automatikusan be van állítva, amikor létrehozza a kötést az Azure Portalon. |
|**név** | n/a | Az eseményt jelképező függvénykódban használt változónév. |
|**Elérési út** |**EventHubName** | Csak 1.x függvényt. Az eseményközpont neve. Ha az eseményközpont neve is szerepel a kapcsolati karakterláncban, ez az érték felülbírálja ezt a tulajdonságot futásidőben. |
|**eventHubName** |**EventHubName** | Funkciók 2.x és magasabb. Az eseményközpont neve. Ha az eseményközpont neve is szerepel a kapcsolati karakterláncban, ez az érték felülbírálja ezt a tulajdonságot futásidőben. |
|**Kapcsolat** |**Kapcsolat** | Az eseményközpont névteréhez való kapcsolati karakterláncot tartalmazó alkalmazásbeállítás neve. Másolja a kapcsolati karakterláncot úgy, hogy a *névtér* **Kapcsolatinformáció** gombjára kattint, nem magára az eseményközpontra. Ennek a kapcsolati karakterláncnak el kell küldenie az üzenetet az eseményfolyamba.|

[!INCLUDE [app settings to local.settings.json](../articles/azure-functions/../../includes/functions-app-settings-local.md)]

## <a name="usage"></a>Használat

# <a name="c"></a>[C #](#tab/csharp)

Üzenetek küldése metódusparaméter használatával, `out string paramName`például . A C# `paramName` parancsfájlban a `name` *function.json*tulajdonságában megadott érték. Több üzenet írásához használhatja `IAsyncCollector<string>` vagy `out string`helyette a ot `ICollector<string>`

# <a name="c-script"></a>[C# parancsfájl](#tab/csharp-script)

Üzenetek küldése metódusparaméter használatával, `out string paramName`például . A C# `paramName` parancsfájlban a `name` *function.json*tulajdonságában megadott érték. Több üzenet írásához használhatja `IAsyncCollector<string>` vagy `out string`helyette a ot `ICollector<string>`

# <a name="javascript"></a>[Javascript](#tab/javascript)

A kimeneti esemény `context.bindings.<name>` `<name>` elérése a `name` *function.json*tulajdonságában megadott érték használatával.

# <a name="python"></a>[Python](#tab/python)

Az Event Hub-üzenetek függvényből való kihirdetésére két lehetőség van:

- **Visszatérési érték** `name` : Állítsa a *function.json* tulajdonságát a értékre. `$return` Ezzel a konfigurációval a függvény visszatérési értéke megmarad egy Event Hub üzenetként.

- **Elengedhetetlen:** Adja át [az](https://docs.microsoft.com/python/api/azure-functions/azure.functions.out?view=azure-python#set-val--t-----none) értéket a megadott metódusa a paraméter deklarált [out](https://docs.microsoft.com/python/api/azure-functions/azure.functions.out?view=azure-python) típus. Az átadott `set` érték eseményközpont-üzenetként marad meg.

# <a name="java"></a>[Java](#tab/java)

Az Event Hub-üzenetek et az [EventHubOutput-megjegyzés](https://docs.microsoft.com/java/api/com.microsoft.azure.functions.annotation.eventhuboutput) használatával kétféleképpen lehet kiküldeni egy függvényből:

- **Visszatérési érték**: Ha a notációt magára a függvényre alkalmazza, a függvény visszatérési értéke Eseményközpont-üzenetként marad meg.

- **Elengedhetetlen**: Az üzenet értékének explicit beállításához alkalmazza a megjegyzéseket [`OutputBinding<T>`](https://docs.microsoft.com/java/api/com.microsoft.azure.functions.OutputBinding)a `T` típus egy adott paraméterére, ahol POJO vagy bármely natív Java típus. Ezzel a konfigurációval egy `setValue` érték átadása a metódus nak megőrzi az értéket, mint egy Event Hub-üzenet.

---

## <a name="exceptions-and-return-codes"></a>Kivételek és visszaküldési kódok

| Kötés | Referencia |
|---|---|
| Eseményközpont | [Üzemeltetési útmutató](https://docs.microsoft.com/rest/api/eventhub/publisher-policy-operations) |

<a name="host-json"></a>  

## <a name="hostjson-settings"></a>host.json beállítások

Ez a szakasz a kötéshez a 2.x-es vagy újabb verziókban elérhető globális konfigurációs beállításokat ismerteti. Az alábbi host.json példafájl csak a kötés 2.x+ verzióját tartalmazza. A 2.x-es és az azt meghaladó verziók globális konfigurációs beállításairól az [Azure Functions host.json referencia című témakörében](../articles/azure-functions/functions-host-json.md)olvashat bővebben.

> [!NOTE]
> A host.json 1.x függvényében a [host.json témakörben az Azure Functions 1.x host.json hivatkozása](../articles/azure-functions/functions-host-json-v1.md)látható.

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
|`maxBatchSize`|10|A fogadási ciklusonként fogadott maximális eseményszám.|
|`prefetchCount`|300|Az alapul szolgáló `EventProcessorHost`eszköz által használt alapértelmezett lehívás előtti számláló .|
|`batchCheckpointFrequency`|1|Az EventHub kurzor-ellenőrzőpont létrehozása előtt feldolgozandó eseménykötegek száma.|
