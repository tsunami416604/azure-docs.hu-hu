---
title: Azure Functions-kötések Azure Service Bus
description: Megtudhatja, hogyan küldhet Azure Service Bus üzeneteket a Azure Functionsból.
author: craigshoemaker
ms.assetid: daedacf0-6546-4355-a65c-50873e74f66b
ms.topic: reference
ms.date: 02/19/2020
ms.author: cshoe
ms.openlocfilehash: 02d9ce87d45c5f1c9a123aae18f7d710b268f03e
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "80582258"
---
# <a name="azure-service-bus-output-binding-for-azure-functions"></a>Azure Functions Azure Service Bus kimeneti kötése

A várólista vagy a témakör üzeneteinek küldéséhez használjon Azure Service Bus kimeneti kötést.

További információ a telepítésről és a konfigurációról: [Áttekintés](functions-bindings-service-bus-output.md).

## <a name="example"></a>Példa

# <a name="c"></a>[C #](#tab/csharp)

Az alábbi példa egy [C#-függvényt](functions-dotnet-class-library.md) mutat be, amely Service Bus üzenetsor-üzenetet küld:

```cs
[FunctionName("ServiceBusOutput")]
[return: ServiceBus("myqueue", Connection = "ServiceBusConnection")]
public static string ServiceBusOutput([HttpTrigger] dynamic input, ILogger log)
{
    log.LogInformation($"C# function processed: {input.Text}");
    return input.Text;
}
```

# <a name="c-script"></a>[C#-parancsfájl](#tab/csharp-script)

Az alábbi példa egy Service Bus kimeneti kötést mutat be egy *function. JSON* fájlban, valamint egy [C# parancsfájl-függvényt](functions-reference-csharp.md) , amely a kötést használja. A függvény egy időzítő trigger használatával küld üzenetsor-üzenetet 15 másodpercenként.

Itt található a *function. JSON* fájlban található kötési adat:

```json
{
    "bindings": [
        {
            "schedule": "0/15 * * * * *",
            "name": "myTimer",
            "runsOnStartup": true,
            "type": "timerTrigger",
            "direction": "in"
        },
        {
            "name": "outputSbQueue",
            "type": "serviceBus",
            "queueName": "testqueue",
            "connection": "MyServiceBusConnection",
            "direction": "out"
        }
    ],
    "disabled": false
}
```

Az alábbi C#-kód egyetlen üzenetet hoz létre:

```cs
public static void Run(TimerInfo myTimer, ILogger log, out string outputSbQueue)
{
    string message = $"Service Bus queue message created at: {DateTime.Now}";
    log.LogInformation(message); 
    outputSbQueue = message;
}
```

A következő C#-kód több üzenetet hoz létre:

```cs
public static async Task Run(TimerInfo myTimer, ILogger log, IAsyncCollector<string> outputSbQueue)
{
    string message = $"Service Bus queue messages created at: {DateTime.Now}";
    log.LogInformation(message); 
    await outputSbQueue.AddAsync("1 " + message);
    await outputSbQueue.AddAsync("2 " + message);
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Az alábbi példa egy Service Bus kimeneti kötést mutat be egy *function. JSON* fájlban, valamint egy [JavaScript-függvényt](functions-reference-node.md) , amely a kötést használja. A függvény egy időzítő trigger használatával küld üzenetsor-üzenetet 15 másodpercenként.

Itt található a *function. JSON* fájlban található kötési adat:

```json
{
    "bindings": [
        {
            "schedule": "0/15 * * * * *",
            "name": "myTimer",
            "runsOnStartup": true,
            "type": "timerTrigger",
            "direction": "in"
        },
        {
            "name": "outputSbQueue",
            "type": "serviceBus",
            "queueName": "testqueue",
            "connection": "MyServiceBusConnection",
            "direction": "out"
        }
    ],
    "disabled": false
}
```

Itt található a JavaScript-szkript kódja, amely egyetlen üzenetet hoz létre:

```javascript
module.exports = function (context, myTimer) {
    var message = 'Service Bus queue message created at ' + timeStamp;
    context.log(message);   
    context.bindings.outputSbQueue = message;
    context.done();
};
```

Az alábbi JavaScript-parancsfájl-kód több üzenetet hoz létre:

```javascript
module.exports = function (context, myTimer) {
    var message = 'Service Bus queue message created at ' + timeStamp;
    context.log(message);   
    context.bindings.outputSbQueue = [];
    context.bindings.outputSbQueue.push("1 " + message);
    context.bindings.outputSbQueue.push("2 " + message);
    context.done();
};
```

# <a name="python"></a>[Python](#tab/python)

Az alábbi példa bemutatja, hogyan írhat egy Service Bus üzenetsor-várólistára a Pythonban.

Egy Service Bus kötés definíciója a *function. JSON* fájlban van megadva, ahol a `serviceBus`Type értéke a *következő* :.

```json
{
  "scriptFile": "__init__.py",
  "bindings": [
    {
      "authLevel": "function",
      "type": "httpTrigger",
      "direction": "in",
      "name": "req",
      "methods": [
        "get",
        "post"
      ]
    },
    {
      "type": "http",
      "direction": "out",
      "name": "$return"
    },
    {
      "type": "serviceBus",
      "direction": "out",
      "connection": "AzureServiceBusConnectionString",
      "name": "msg",
      "queueName": "outqueue"
    }
  ]
}
```

`set` Az * _ \_init_\_.* másolás eszközben a metódus értékének megadásával kiírhat egy üzenetet a várólistába.

```python
import azure.functions as func

def main(req: func.HttpRequest, msg: func.Out[str]) -> func.HttpResponse:

    input_msg = req.params.get('message')

    msg.set(input_msg)

    return 'OK'
```

# <a name="java"></a>[Java](#tab/java)

Az alábbi példa egy Java-függvényt mutat be, amely egy HTTP- `myqueue` kérelem által aktivált üzenetet küld egy Service Bus üzenetsor számára.

```java
@FunctionName("httpToServiceBusQueue")
@ServiceBusQueueOutput(name = "message", queueName = "myqueue", connection = "AzureServiceBusConnection")
public String pushToQueue(
  @HttpTrigger(name = "request", methods = {HttpMethod.POST}, authLevel = AuthorizationLevel.ANONYMOUS)
  final String message,
  @HttpOutput(name = "response") final OutputBinding<T> result ) {
      result.setValue(message + " has been sent.");
      return message;
 }
```

 A [Java functions runtime library](/java/api/overview/azure/functions/runtime)-ben használja `@QueueOutput` a megjegyzések a függvény azon paramétereit, amelyek értékét egy Service Bus üzenetsor számára szeretné írni.  A paraméter típusának olyannak kell lennie `OutputBinding<T>`, ahol a T egy POJO natív Java-típusa.

A Java functions is írhat Service Bus témakörbe. A következő példa a `@ServiceBusTopicOutput` jegyzetet használja a kimeneti kötés konfigurációjának leírásához. 

```java
@FunctionName("sbtopicsend")
    public HttpResponseMessage run(
            @HttpTrigger(name = "req", methods = {HttpMethod.GET, HttpMethod.POST}, authLevel = AuthorizationLevel.ANONYMOUS) HttpRequestMessage<Optional<String>> request,
            @ServiceBusTopicOutput(name = "message", topicName = "mytopicname", subscriptionName = "mysubscription", connection = "ServiceBusConnection") OutputBinding<String> message,
            final ExecutionContext context) {
        
        String name = request.getBody().orElse("Azure Functions");

        message.setValue(name);
        return request.createResponseBuilder(HttpStatus.OK).body("Hello, " + name).build();
        
    }
```

---

## <a name="attributes-and-annotations"></a>Attribútumok és jegyzetek

# <a name="c"></a>[C #](#tab/csharp)

A [C# osztályok könyvtáraiban](functions-dotnet-class-library.md)használja a [ServiceBusAttribute](https://github.com/Azure/azure-functions-servicebus-extension/blob/master/src/Microsoft.Azure.WebJobs.Extensions.ServiceBus/ServiceBusAttribute.cs).

Az attribútum konstruktora a várólista nevét, illetve a témakört és az előfizetést veszi át. Megadhatja a kapcsolat hozzáférési jogosultságait is. A hozzáférési jogosultságok beállításának kiválasztását a [kimenet – konfiguráció](#configuration) szakasz ismerteti. Az alábbi példa bemutatja a függvény visszatérési értékére alkalmazott attribútumot:

```csharp
[FunctionName("ServiceBusOutput")]
[return: ServiceBus("myqueue")]
public static string Run([HttpTrigger] dynamic input, ILogger log)
{
    ...
}
```

A `Connection` tulajdonság beállításával megadhatja a használni kívánt Service Bus-kapcsolódási karakterláncot tartalmazó Alkalmazásbeállítások nevét, ahogy az a következő példában is látható:

```csharp
[FunctionName("ServiceBusOutput")]
[return: ServiceBus("myqueue", Connection = "ServiceBusConnection")]
public static string Run([HttpTrigger] dynamic input, ILogger log)
{
    ...
}
```

Teljes példa: [kimenet – példa](#example).

Az `ServiceBusAccount` attribútum segítségével megadhatja az osztály, a metódus vagy a paraméter szintjén használandó Service Bus fiókot.  További információ: [trigger-attributes](functions-bindings-service-bus-trigger.md#attributes-and-annotations).

# <a name="c-script"></a>[C#-parancsfájl](#tab/csharp-script)

A C# parancsfájl nem támogatja az attribútumokat.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

A JavaScript nem támogatja az attribútumokat.

# <a name="python"></a>[Python](#tab/python)

A Python nem támogatja az attribútumokat.

# <a name="java"></a>[Java](#tab/java)

A `ServiceBusQueueOutput` és `ServiceBusTopicOutput` a jegyzetek elérhetők egy üzenet függvény kimenetként való írásához. Az ezekkel a megjegyzésekkel díszített paramétert az üzenet típusának `T` megfelelő típusként kell deklarálni `OutputBinding<T>` .

---

## <a name="configuration"></a>Configuration

Az alábbi táblázat a *function. JSON* fájlban és az `ServiceBus` attribútumban beállított kötési konfigurációs tulajdonságokat ismerteti.

|function. JSON-tulajdonság | Attribútum tulajdonsága |Leírás|
|---------|---------|----------------------|
|**típusa** | n/a | "ServiceBus" értékre kell állítani. Ez a tulajdonság automatikusan be van állítva, amikor létrehozza az triggert a Azure Portalban.|
|**direction** | n/a | "Out" értékre kell állítani. Ez a tulajdonság automatikusan be van állítva, amikor létrehozza az triggert a Azure Portalban. |
|**név** | n/a | Annak a változónak a neve, amely a függvény kódjában szereplő üzenetsor vagy témakör üzenetét jelöli. A függvény visszatérési értékének hivatkozásához állítsa a "$return" értéket. |
|**queueName**|**QueueName**|A várólista neve.  Csak akkor állítsa be, ha üzenetsor-üzeneteket küld, nem pedig egy témakörhöz.
|**topicName**|**TopicName**|A témakör neve. Csak akkor állítható be, ha nem várólistára küldi a témakör üzeneteit.|
|**kapcsolat**|**Kapcsolat**|A kötéshez használni kívánt Service Bus kapcsolati karakterláncot tartalmazó Alkalmazásbeállítás neve. Ha az Alkalmazásbeállítások neve "AzureWebJobs" előtaggal kezdődik, akkor csak a név hátralévő részét adhatja meg. Ha például a "MyServiceBus" `connection` értékre van állítva, a functions futtatókörnyezet egy "AzureWebJobsMyServiceBus" nevű alkalmazás-beállítást keres. Ha üresen `connection` hagyja, a functions Runtime az alapértelmezett Service Bus a "AzureWebJobsServiceBus" nevű alkalmazás-beállításban található.<br><br>A kapcsolódási karakterlánc beszerzéséhez kövesse a [felügyeleti hitelesítő adatok beolvasása](../service-bus-messaging/service-bus-quickstart-portal.md#get-the-connection-string)című cikkben ismertetett lépéseket. A kapcsolódási karakterláncnak egy Service Bus névtérhez kell tartoznia, nem csak egy adott várólistára vagy témakörre.|
|**accessRights**|**Hozzáférés**|Hozzáférési jogosultságok a kapcsolati karakterlánchoz. Az `manage` elérhető értékek: `listen`és. Az alapértelmezett érték `manage`a, amely azt jelzi `connection` , hogy a rendelkezik a **kezelés** engedéllyel. Ha olyan kapcsolódási karakterláncot használ, amely nem rendelkezik a **kezelés** engedéllyel, `accessRights` állítsa a "Listen" (figyelés) lehetőséget. Ellenkező esetben előfordulhat, hogy a functions futtatókörnyezete nem próbálkozik a kezelési jogosultságokat igénylő műveletekkel. A Azure Functions 2. x vagy újabb verziójában ez a tulajdonság nem érhető el, mert a Service Bus SDK legújabb verziója nem támogatja a kezelési műveleteket.|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="usage"></a>Használat

Azure Functions 1. x esetén a futtatókörnyezet létrehozza a várólistát, ha az nem létezik, és `accessRights` beállította `manage`a következőt:. A functions 2. x vagy újabb verziójában a várólistának vagy a témakörnek már léteznie kell; Ha olyan üzenetsor vagy témakört ad meg, amely nem létezik, a függvény sikertelen lesz. 

# <a name="c"></a>[C #](#tab/csharp)

A kimeneti kötéshez használja a következő paramétereket:

* `out T paramName` - `T`bármely JSON-szerializálható típus lehet. Ha a paraméter értéke null, ha a függvény kilép, a függvény Null objektummal hozza létre az üzenetet.
* `out string`-Ha a paraméter értéke null, ha a függvény kilép, a functions nem hoz létre üzenetet.
* `out byte[]`-Ha a paraméter értéke null, ha a függvény kilép, a functions nem hoz létre üzenetet.
* `out BrokeredMessage`-Ha a paraméter értéke null, ha a függvény kilép, a functions nem hoz létre üzenetet (az 1. x függvények esetében)
* `out Message`– Ha a paraméter értéke null, ha a függvény kilép, a functions nem hoz létre üzenetet (a 2. x és újabb függvények esetében).
* `ICollector<T>`vagy `IAsyncCollector<T>` – több üzenet létrehozásához. A `Add` metódus meghívásakor létrejön egy üzenet.

C#-függvények használata esetén:

* Az aszinkron függvények visszatérési értékének `IAsyncCollector` vagy `out` paraméterének kell lennie.

* A munkamenet-azonosító eléréséhez kötést kell [`Message`](https://docs.microsoft.com/dotnet/api/microsoft.azure.servicebus.message) kötnie egy típushoz, és a `sessionId` tulajdonságot kell használnia.

# <a name="c-script"></a>[C#-parancsfájl](#tab/csharp-script)

A kimeneti kötéshez használja a következő paramétereket:

* `out T paramName` - `T`bármely JSON-szerializálható típus lehet. Ha a paraméter értéke null, ha a függvény kilép, a függvény Null objektummal hozza létre az üzenetet.
* `out string`-Ha a paraméter értéke null, ha a függvény kilép, a functions nem hoz létre üzenetet.
* `out byte[]`-Ha a paraméter értéke null, ha a függvény kilép, a functions nem hoz létre üzenetet.
* `out BrokeredMessage`-Ha a paraméter értéke null, ha a függvény kilép, a functions nem hoz létre üzenetet (az 1. x függvények esetében)
* `out Message`– Ha a paraméter értéke null, ha a függvény kilép, a functions nem hoz létre üzenetet (a 2. x és újabb függvények esetében).
* `ICollector<T>`vagy `IAsyncCollector<T>` – több üzenet létrehozásához. A `Add` metódus meghívásakor létrejön egy üzenet.

C#-függvények használata esetén:

* Az aszinkron függvények visszatérési értékének `IAsyncCollector` vagy `out` paraméterének kell lennie.

* A munkamenet-azonosító eléréséhez kötést kell [`Message`](https://docs.microsoft.com/dotnet/api/microsoft.azure.servicebus.message) kötnie egy típushoz, és a `sessionId` tulajdonságot kell használnia.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Az üzenetsor vagy témakör eléréséhez használja `context.bindings.<name from function.json>`a következőt:. Hozzárendelhet egy karakterláncot, egy bájt tömböt vagy egy JavaScript-objektumot (JSON-ként deszerializált) `context.binding.<name>`a következőhöz:.

# <a name="python"></a>[Python](#tab/python)

A beépített kimeneti kötés helyett használja a [Azure Service Bus SDK](https://docs.microsoft.com/azure/service-bus-messaging) -t.

# <a name="java"></a>[Java](#tab/java)

A beépített kimeneti kötés helyett használja a [Azure Service Bus SDK](https://docs.microsoft.com/azure/service-bus-messaging) -t.

---

## <a name="exceptions-and-return-codes"></a>Kivételek és visszatérési kódok

| Kötés | Referencia |
|---|---|
| Service Bus | [Service Bus hibakódok](https://docs.microsoft.com/azure/service-bus-messaging/service-bus-messaging-exceptions) |
| Service Bus | [Service Bus korlátok](https://docs.microsoft.com/azure/service-bus-messaging/service-bus-quotas) |

<a name="host-json"></a>  

## <a name="hostjson-settings"></a>gazdagép. JSON-beállítások

Ez a szakasz a kötéshez elérhető globális konfigurációs beállításokat ismerteti 2. x vagy újabb verziókban. A következő példában szereplő Host. JSON fájl csak a kötés beállításait tartalmazza. A globális konfigurációs beállításokkal kapcsolatos további információkért lásd: [Host. JSON-dokumentáció Azure functions verzióhoz](functions-host-json.md).

> [!NOTE]
> Az 1. x függvények Host. JSON fájljának hivatkozását lásd: [Host. JSON-dokumentáció Azure functions 1. x-hez](functions-host-json-v1.md).

```json
{
    "version": "2.0",
    "extensions": {
        "serviceBus": {
            "prefetchCount": 100,
            "messageHandlerOptions": {
                "autoComplete": false,
                "maxConcurrentCalls": 32,
                "maxAutoRenewDuration": "00:55:00"
            },
            "sessionHandlerOptions": {
                "autoComplete": false,
                "messageWaitTimeout": "00:00:30",
                "maxAutoRenewDuration": "00:55:00",
                "maxConcurrentSessions": 16
            }
        }
    }
}
```

|Tulajdonság  |Alapértelmezett | Leírás |
|---------|---------|---------|
|prefetchCount|0|Lekérdezi vagy beállítja az üzenet fogadója által egyidejűleg igényelhető üzenetek számát.|
|maxAutoRenewDuration|00:05:00|Az a maximális időtartam, amelyen belül az üzenet zárolása automatikusan meg lesz újítva.|
|Automatikus kiegészítés|igaz|Azt jelzi, hogy a triggernek azonnal meg kell-e jelölnie az üzenetet (automatikus kiegészítés), vagy várjon, amíg a függvény sikeresen kilép a hívás befejezéséhez.|
|maxConcurrentCalls|16|Az üzenet-szivattyú által kezdeményezett visszahívás egyidejű hívásának maximális száma. Alapértelmezés szerint a függvények futtatókörnyezete egyszerre több üzenetet dolgoz fel. Ha úgy szeretné irányítani a futtatókörnyezetet, hogy egyszerre csak egyetlen üzenetsor vagy témakörbeli üzenetet `maxConcurrentCalls` dolgozza fel, állítsa az 1 értékre. |

## <a name="next-steps"></a>További lépések

- [Függvény futtatása Service Bus üzenetsor vagy témakör-üzenet létrehozásakor (trigger)](./functions-bindings-service-bus-trigger.md)
