---
title: Az Azure Service Bus kötései az Azure Functionshez
description: Ismerje meg, hogyan küldhet Azure Service Bus-üzeneteket az Azure Functions szolgáltatásból.
author: craigshoemaker
ms.assetid: daedacf0-6546-4355-a65c-50873e74f66b
ms.topic: reference
ms.date: 02/19/2020
ms.author: cshoe
ms.openlocfilehash: 7e00d03a8b3ec7ef56935ff7714fd932bc343cd3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79277439"
---
# <a name="azure-service-bus-output-binding-for-azure-functions"></a>Az Azure Service Bus kimeneti kötése az Azure Functions-hez

Az Azure Service Bus kimeneti kötéshasználatával várólista- vagy témakörüzeneteket küldhet.

A beállítással és a konfigurációval kapcsolatos részletekről az [áttekintésben](functions-bindings-service-bus-output.md)olvashat.

## <a name="example"></a>Példa

# <a name="c"></a>[C #](#tab/csharp)

A következő példa egy [C# függvényt](functions-dotnet-class-library.md) mutat be, amely egy Service Bus-üzenetüzenetben jelenik meg:

```cs
[FunctionName("ServiceBusOutput")]
[return: ServiceBus("myqueue", Connection = "ServiceBusConnection")]
public static string ServiceBusOutput([HttpTrigger] dynamic input, ILogger log)
{
    log.LogInformation($"C# function processed: {input.Text}");
    return input.Text;
}
```

# <a name="c-script"></a>[C# parancsfájl](#tab/csharp-script)

A következő példa egy Service Bus kimeneti kötést mutat be egy *function.json* fájlban és egy [C# parancsfájl függvényt,](functions-reference-csharp.md) amely a kötést használja. A függvény időzítő-eseményindítót használ a várólista-üzenet 15 másodpercenként küldéséhez.

A *function.json* fájlban a kötési adatok:

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

Itt a C# parancsfájlkód, amely egyetlen üzenetet hoz létre:

```cs
public static void Run(TimerInfo myTimer, ILogger log, out string outputSbQueue)
{
    string message = $"Service Bus queue message created at: {DateTime.Now}";
    log.LogInformation(message); 
    outputSbQueue = message;
}
```

Itt a C# parancsfájlkód, amely több üzenetet hoz létre:

```cs
public static async Task Run(TimerInfo myTimer, ILogger log, IAsyncCollector<string> outputSbQueue)
{
    string message = $"Service Bus queue messages created at: {DateTime.Now}";
    log.LogInformation(message); 
    await outputSbQueue.AddAsync("1 " + message);
    await outputSbQueue.AddAsync("2 " + message);
}
```

# <a name="javascript"></a>[Javascript](#tab/javascript)

A következő példa egy Service Bus kimeneti kötést mutat be egy *function.json* fájlban, és egy [JavaScript függvényt,](functions-reference-node.md) amely a kötést használja. A függvény időzítő-eseményindítót használ a várólista-üzenet 15 másodpercenként küldéséhez.

A *function.json* fájlban a kötési adatok:

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

Itt van a JavaScript parancsfájlkód, amely egyetlen üzenetet hoz létre:

```javascript
module.exports = function (context, myTimer) {
    var message = 'Service Bus queue message created at ' + timeStamp;
    context.log(message);   
    context.bindings.outputSbQueue = message;
    context.done();
};
```

Itt van a JavaScript parancsfájlkód, amely több üzenetet hoz létre:

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

A következő példa bemutatja, hogyan írhat ki egy Service Bus-várólistába a Pythonban.

A Service Bus kötésdefiníciója a *function.json* ban `serviceBus`van definiálva, ahol a *típus* a.

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

Az `set` init *\_.py alkalmazásban egy üzenetet is kiírhat a várólistába, ha átad egy értéket a metódusnak. _ \__*

```python
import azure.functions as func

def main(req: func.HttpRequest, msg: func.Out[str]) -> func.HttpResponse:

    input_msg = req.params.get('message')

    msg.set(input_msg)

    return 'OK'
```

# <a name="java"></a>[Java](#tab/java)

A következő példa egy Java-függvényt mutat be, amely http-kérelem esetén üzenetet küld egy Service Bus-várólistának. `myqueue`

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

 A [Java függvények futásidejű függvénytárában](/java/api/overview/azure/functions/runtime)használja a `@QueueOutput` funkcióparaméterek et, amelyek értékét a Service Bus-várólistába írná.  A paramétertípusnak `OutputBinding<T>`a : -nak kell lennie, ahol T a POJO bármely natív Java típusa.

Java függvények is írhat egy Service Bus témakörben. A következő példa `@ServiceBusTopicOutput` a jegyzet et használja a kimeneti kötés konfigurációjának leírására. 

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

A [C# osztálykönyvtárakban](functions-dotnet-class-library.md)használja a [ServiceBusAttribute attribútumot.](https://github.com/Azure/azure-functions-servicebus-extension/blob/master/src/Microsoft.Azure.WebJobs.Extensions.ServiceBus/ServiceBusAttribute.cs)

Az attribútum konstruktora veszi a nevét a várólista vagy a témakör és az előfizetés. Megadhatja a kapcsolat hozzáférési jogait is. A hozzáférési jogok beállításának kiválasztása a [Kimenet - konfiguráció](#configuration) szakaszban található. Íme egy példa, amely a függvény visszatérési értékére alkalmazott attribútumot mutatja:

```csharp
[FunctionName("ServiceBusOutput")]
[return: ServiceBus("myqueue")]
public static string Run([HttpTrigger] dynamic input, ILogger log)
{
    ...
}
```

Beállíthatja, `Connection` hogy a tulajdonság adja meg a használni kívánt Service Bus-kapcsolati karakterláncot tartalmazó alkalmazásbeállítás nevét, ahogy az a következő példában látható:

```csharp
[FunctionName("ServiceBusOutput")]
[return: ServiceBus("myqueue", Connection = "ServiceBusConnection")]
public static string Run([HttpTrigger] dynamic input, ILogger log)
{
    ...
}
```

Egy teljes példa, lásd: [Kimenet - példa](#example).

Az `ServiceBusAccount` attribútum segítségével megadhatja az osztály, metódus vagy paraméter szintjén használandó Service Bus-fiókot.  További információ: [Trigger - attributes](functions-bindings-service-bus-trigger.md#attributes-and-annotations).

# <a name="c-script"></a>[C# parancsfájl](#tab/csharp-script)

Az attribútumokat a C# script nem támogatja.

# <a name="javascript"></a>[Javascript](#tab/javascript)

A JavaScript nem támogatja az attribútumokat.

# <a name="python"></a>[Python](#tab/python)

Az attribútumokat a Python nem támogatja.

# <a name="java"></a>[Java](#tab/java)

A `ServiceBusQueueOutput` `ServiceBusTopicOutput` és a jegyzetek függvénykimenetként írhatók. Az ezekkel a taggal díszített paramétert `OutputBinding<T>` az `T` üzenet típusának megfelelő helyként kell deklarálni.

---

## <a name="configuration"></a>Konfiguráció

Az alábbi táblázat a *function.json* fájlban és az `ServiceBus` attribútumban beállított kötési konfigurációs tulajdonságokat ismerteti.

|function.json tulajdonság | Attribútum tulajdonság |Leírás|
|---------|---------|----------------------|
|**Típus** | n/a | A "serviceBus" beállításnak kell lennie. Ez a tulajdonság automatikusan be van állítva, amikor létrehozza az eseményindítót az Azure Portalon.|
|**direction** | n/a | Be kell állítani, hogy "ki". Ez a tulajdonság automatikusan be van állítva, amikor létrehozza az eseményindítót az Azure Portalon. |
|**név** | n/a | A várólista- vagy témakörüzenetet jelképező változó neve a függvénykódban. Állítsa a "$return" értéket a függvény visszatérési értékére való hivatkozáshoz. |
|**queueName (sornév)**|**Várólistaneve**|A várólista neve.  Csak akkor állítsa be, ha várólistaüzeneteket küld, nem pedig egy témakörhöz.
|**topicName (témakör neve)**|**Témakör neve**|A téma neve. Csak akkor állítsa be, ha témakörüzeneteket küld, nem pedig várólistához.|
|**Kapcsolat**|**Kapcsolat**|A kötéshez használandó Service Bus-kapcsolati karakterláncot tartalmazó alkalmazásbeállítás neve. Ha az alkalmazásbeállítás neve "AzureWebJobs" programmal kezdődik, csak a név fennmaradó részét adhatja meg. Ha például "MyServiceBus" beállítást állít be, `connection` a Functions futásidejű megkeresi az "AzureWebJobsMyServiceBus" nevű alkalmazásbeállítást. Ha üresen hagyja, `connection` a Functions futásidejű az alapértelmezett Service Bus-kapcsolati karakterláncot használja az "AzureWebJobsServiceBus" nevű alkalmazásbeállításban.<br><br>Kapcsolati karakterlánc beszerzéséhez kövesse [a Felügyeleti hitelesítő adatok beszerzése](../service-bus-messaging/service-bus-quickstart-portal.md#get-the-connection-string)című rész lépéseit. A kapcsolati karakterláncnak egy Service Bus-névtérhez kell tartoznia, nem korlátozódik egy adott várólistára vagy témakörre.|
|**accessRights (hozzáférési jogok)**|**Hozzáférés**|A kapcsolati karakterlánc hozzáférési jogosultságai. A rendelkezésre álló értékek a `manage` és `listen`a. Az alapértelmezett `manage`beállítás azt jelzi, hogy a `connection` rendelkezik a **Manage** engedéllyel. Ha olyan kapcsolati karakterláncot használ, amely `accessRights` nem rendelkezik **Manage** engedéllyel, állítsa a "listen" (figyelés) beállítást. Ellenkező esetben előfordulhat, hogy a Functions futásidejű sikertelen lesz a kezelési jogosultságokat igénylő műveletek megkísérlése. Az Azure Functions 2.x-es és újabb verziójában ez a tulajdonság nem érhető el, mert a Service Bus SDK legújabb verziója nem támogatja a kezelési műveleteket.|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="usage"></a>Használat

Az Azure Functions 1.x-ben a futásidejű létrehozza a várólistát, `manage`ha az nem létezik, és beállította a. `accessRights` A Functions 2.x-es és újabb verziójában a várólistának vagy a témakörnek már léteznie kell; ha olyan várólistát vagy témakört ad meg, amely nem létezik, a függvény sikertelen lesz. 

# <a name="c"></a>[C #](#tab/csharp)

A kimenetkötéshez a következő paramétertípusokat használja:

* `out T paramName` - `T`bármilyen JSON-szerializálható típus lehet. Ha a paraméter értéke null értékű, amikor a függvény kilép, a Functions null objektummal hozza létre az üzenetet.
* `out string`- Ha a paraméter értéke null értékű, amikor a függvény kilép, a Functions nem hoz létre üzenetet.
* `out byte[]`- Ha a paraméter értéke null értékű, amikor a függvény kilép, a Functions nem hoz létre üzenetet.
* `out BrokeredMessage`- Ha a paraméter értéke null értékű, amikor a függvény kilép, a Functions nem hoz létre üzenetet (az 1.x függvényekhez)
* `out Message`- Ha a paraméter értéke null, amikor a függvény kilép, a Functions nem hoz létre üzenetet (a Functions 2.x és újabb verziók esetén)
* `ICollector<T>`vagy `IAsyncCollector<T>` - Több üzenet létrehozásához. A `Add` metódus hívásakor üzenet jön létre.

C# függvényekkel végzett munka során:

* Az Async függvényeknek `IAsyncCollector` visszatérési `out` értékre vagy paraméter helyett kell lenniünk.

* A munkamenet-azonosító eléréséhez kösse meg [`Message`](https://docs.microsoft.com/dotnet/api/microsoft.azure.servicebus.message) `sessionId` egy típust, és használja a tulajdonságot.

# <a name="c-script"></a>[C# parancsfájl](#tab/csharp-script)

A kimenetkötéshez a következő paramétertípusokat használja:

* `out T paramName` - `T`bármilyen JSON-szerializálható típus lehet. Ha a paraméter értéke null értékű, amikor a függvény kilép, a Functions null objektummal hozza létre az üzenetet.
* `out string`- Ha a paraméter értéke null értékű, amikor a függvény kilép, a Functions nem hoz létre üzenetet.
* `out byte[]`- Ha a paraméter értéke null értékű, amikor a függvény kilép, a Functions nem hoz létre üzenetet.
* `out BrokeredMessage`- Ha a paraméter értéke null értékű, amikor a függvény kilép, a Functions nem hoz létre üzenetet (az 1.x függvényekhez)
* `out Message`- Ha a paraméter értéke null, amikor a függvény kilép, a Functions nem hoz létre üzenetet (a Functions 2.x és újabb verziók esetén)
* `ICollector<T>`vagy `IAsyncCollector<T>` - Több üzenet létrehozásához. A `Add` metódus hívásakor üzenet jön létre.

C# függvényekkel végzett munka során:

* Az Async függvényeknek `IAsyncCollector` visszatérési `out` értékre vagy paraméter helyett kell lenniünk.

* A munkamenet-azonosító eléréséhez kösse meg [`Message`](https://docs.microsoft.com/dotnet/api/microsoft.azure.servicebus.message) `sessionId` egy típust, és használja a tulajdonságot.

# <a name="javascript"></a>[Javascript](#tab/javascript)

A várólista vagy `context.bindings.<name from function.json>`a témakör elérése a használatával. A programhoz karakterláncot, bájttömböt vagy JavaScript-objektumot rendelhet (json-ba deszerializálva). `context.binding.<name>`

# <a name="python"></a>[Python](#tab/python)

Használja az [Azure Service Bus SDK](https://docs.microsoft.com/azure/service-bus-messaging) helyett a beépített kimeneti kötés.

# <a name="java"></a>[Java](#tab/java)

Használja az [Azure Service Bus SDK](https://docs.microsoft.com/azure/service-bus-messaging) helyett a beépített kimeneti kötés.

---

## <a name="exceptions-and-return-codes"></a>Kivételek és visszaküldési kódok

| Kötés | Referencia |
|---|---|
| Service Bus | [Service Bus hibakódok](https://docs.microsoft.com/azure/service-bus-messaging/service-bus-messaging-exceptions) |
| Service Bus | [Szolgáltatási busz korlátai](https://docs.microsoft.com/azure/service-bus-messaging/service-bus-quotas) |

<a name="host-json"></a>  

## <a name="hostjson-settings"></a>host.json beállítások

Ez a szakasz a kötéshez a 2.x-es vagy újabb verziókban elérhető globális konfigurációs beállításokat ismerteti. Az alábbi host.json példafájl csak a kötés beállításait tartalmazza. A globális konfigurációs beállításokról további információt az [Azure Functions verziójának host.json-referencia című témaköre tartalmaz.](functions-host-json.md)

> [!NOTE]
> A host.json 1.x függvényében a [host.json témakörben az Azure Functions 1.x host.json hivatkozása](functions-host-json-v1.md)látható.

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
|maxAutoRenewDuration|00:05:00|Az a maximális időtartam, amelyen belül az üzenetzárolás automatikusan megújul.|
|Automatikus|igaz|Azt jelzi, hogy az eseményindító azonnal megjelöli-e az üzenetet teljesként (automatikus kiegészítés), vagy várja meg, amíg a függvény sikeresen kilép a hívás befejezéséhez.|
|maxConcurrentKéri|16|Az üzenetszivattyú által kezdeményezett visszahívásegyidejű hívásainak maximális száma. Alapértelmezés szerint a Függvények futásidejű folyamatok több üzenetet dolgoznak fel egyidejűleg. Ha azt szeretné, hogy a futásidejű egyszerre csak egy `maxConcurrentCalls` várólista- vagy témakörüzenet feldolgozására szolgáljon, állítsa 1-re. |

## <a name="next-steps"></a>További lépések

- [Szolgáltatásbusz-várólista vagy témakörüzenet létrehozásakor egy függvény futtatása (Eseményindító)](./functions-bindings-service-bus-trigger.md)
