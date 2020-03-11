---
title: Az Azure Szolgáltatásbusz-kötések az Azure Functions szolgáltatáshoz
description: Megtudhatja, hogyan küldhet Azure Service Bus üzeneteket a Azure Functionsból.
author: craigshoemaker
ms.assetid: daedacf0-6546-4355-a65c-50873e74f66b
ms.topic: reference
ms.date: 02/19/2020
ms.author: cshoe
ms.openlocfilehash: 7e00d03a8b3ec7ef56935ff7714fd932bc343cd3
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/05/2020
ms.locfileid: "78357013"
---
# <a name="azure-service-bus-output-binding-for-azure-functions"></a>Azure Functions Azure Service Bus kimeneti kötése

Azure Service Bus kimeneti kötés használatával üzenetsor vagy témakör üzeneteket küldeni.

További információ a telepítésről és a konfigurációról: [Áttekintés](functions-bindings-service-bus-output.md).

## <a name="example"></a>Példa

# <a name="c"></a>[C#](#tab/csharp)

Az alábbi példa egy olyan [ C# függvényt](functions-dotnet-class-library.md) mutat be, amely Service Bus üzenetsor-üzenetet küld:

```cs
[FunctionName("ServiceBusOutput")]
[return: ServiceBus("myqueue", Connection = "ServiceBusConnection")]
public static string ServiceBusOutput([HttpTrigger] dynamic input, ILogger log)
{
    log.LogInformation($"C# function processed: {input.Text}");
    return input.Text;
}
```

# <a name="c-script"></a>[C#Parancsfájl](#tab/csharp-script)

Az alábbi példa egy Service Bus kimeneti kötést mutat be egy *function. JSON* fájlban, valamint egy olyan [ C# parancsfájl-függvényt](functions-reference-csharp.md) , amely a kötést használja. A függvény egy időzítő eseményindító 15 másodpercenként üzenetsori üzenetek küldéséhez használja.

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

Itt látható C#-szkriptkódot, amely létrehozza egy adott üzenet:

```cs
public static void Run(TimerInfo myTimer, ILogger log, out string outputSbQueue)
{
    string message = $"Service Bus queue message created at: {DateTime.Now}";
    log.LogInformation(message); 
    outputSbQueue = message;
}
```

Az alábbiakban C#-szkriptkódot, amely több üzenetet hoz létre:

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

Az alábbi példa egy Service Bus kimeneti kötést mutat be egy *function. JSON* fájlban, valamint egy [JavaScript-függvényt](functions-reference-node.md) , amely a kötést használja. A függvény egy időzítő eseményindító 15 másodpercenként üzenetsori üzenetek küldéséhez használja.

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

A következő JavaScript-szkriptkódot, amely létrehozza egy adott üzenet:

```javascript
module.exports = function (context, myTimer) {
    var message = 'Service Bus queue message created at ' + timeStamp;
    context.log(message);   
    context.bindings.outputSbQueue = message;
    context.done();
};
```

A következő JavaScript-szkriptkódot, amely több üzenetet hoz létre:

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

Service Bus kötés definíciója a *function. JSON* fájlban van megadva, ahol a *Type* értéke `serviceBus`.

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

A  *_\_init_\_. a. a.* a `set` metódus értékének átadásával egy üzenetet is írhat a várólistára.

```python
import azure.functions as func

def main(req: func.HttpRequest, msg: func.Out[str]) -> func.HttpResponse:

    input_msg = req.params.get('message')

    msg.set(input_msg)

    return 'OK'
```

# <a name="java"></a>[Java](#tab/java)

Az alábbi példa egy Java-függvényt mutat be, amely egy üzenetet küld egy Service Bus üzenetsor `myqueue`, ha HTTP-kérést indít el.

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

 A [Java functions runtime library](/java/api/overview/azure/functions/runtime)-ben használja a `@QueueOutput` megjegyzése a függvény paramétereit, amelyek értékét egy Service Bus-várólistába kívánja írni.  A paraméter típusának `OutputBinding<T>`nak kell lennie, ahol a T egy POJO natív Java-típusa.

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

# <a name="c"></a>[C#](#tab/csharp)

Az [ C# osztályok könyvtáraiban](functions-dotnet-class-library.md)használja a [ServiceBusAttribute](https://github.com/Azure/azure-functions-servicebus-extension/blob/master/src/Microsoft.Azure.WebJobs.Extensions.ServiceBus/ServiceBusAttribute.cs).

Az attribútum konstruktorának paramétereként az üzenetsor vagy témakör és előfizetés nevét. A kapcsolat hozzáférési jogosultságokat is megadhatja. A hozzáférési jogosultságok beállításának kiválasztását a [kimenet – konfiguráció](#configuration) szakasz ismerteti. A következő példa bemutatja, az attribútum a függvény visszatérési értéke a alkalmazni:

```csharp
[FunctionName("ServiceBusOutput")]
[return: ServiceBus("myqueue")]
public static string Run([HttpTrigger] dynamic input, ILogger log)
{
    ...
}
```

A `Connection` tulajdonság beállításával megadhatja a használni kívánt Service Bus-kapcsolódási karakterláncot tartalmazó Alkalmazásbeállítások nevét, ahogy az alábbi példában is látható:

```csharp
[FunctionName("ServiceBusOutput")]
[return: ServiceBus("myqueue", Connection = "ServiceBusConnection")]
public static string Run([HttpTrigger] dynamic input, ILogger log)
{
    ...
}
```

Teljes példa: [kimenet – példa](#example).

A `ServiceBusAccount` attribútum segítségével megadhatja az osztály, metódus vagy paraméter szintjén használandó Service Bus fiókot.  További információ: [trigger-attributes](functions-bindings-service-bus-trigger.md#attributes-and-annotations).

# <a name="c-script"></a>[C#Parancsfájl](#tab/csharp-script)

Az C# attribútumokat a parancsfájl nem támogatja.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

A JavaScript nem támogatja az attribútumokat.

# <a name="python"></a>[Python](#tab/python)

A Python nem támogatja az attribútumokat.

# <a name="java"></a>[Java](#tab/java)

A `ServiceBusQueueOutput` és `ServiceBusTopicOutput` jegyzetek is elérhetők egy üzenet függvény kimenetként való írásához. Az ezekkel a megjegyzésekkel díszített paramétert olyan `OutputBinding<T>` kell deklarálni, amelyben a `T` az üzenet típusának megfelelő típus.

---

## <a name="configuration"></a>Konfiguráció

Az alábbi táblázat a *function. JSON* fájlban és a `ServiceBus` attribútumban beállított kötési konfigurációs tulajdonságokat ismerteti.

|Function.JSON tulajdonság | Attribútum tulajdonsága |Leírás|
|---------|---------|----------------------|
|**type** | n/a | "A serviceBus" kell állítani. Ez a tulajdonság beállítása automatikusan történik, ha az eseményindítót fog létrehozni az Azure Portalon.|
|**direction** | n/a | Állítsa "out". Ez a tulajdonság beállítása automatikusan történik, ha az eseményindítót fog létrehozni az Azure Portalon. |
|**név** | n/a | A függvénykódot az üzenetsor vagy témakör üzenet képviselő változó neve. "$Return" hivatkozik a függvény visszaadott értékének beállításával. |
|**queueName**|**QueueName**|Az üzenetsor neve.  Állítsa be, csak akkor, ha üzenetküldésre üzenetsor, témakör esetében nem.
|**topicName**|**TopicName**|A témakör neve. Csak akkor, ha üzenetküldésre témakör, nem a várólista állítsa be.|
|**kapcsolat**|**Kapcsolat**|A Service Bus kapcsolati karakterlánc használata ehhez a kötéshez tartalmazó alkalmazásbeállítás neve. Azon alkalmazásbeállítás neve "AzureWebJobs" kezdődik, ha csak a név része is megadhat. Ha például a `connection` "MyServiceBus" értékre állítja, a functions futtatókörnyezet egy "AzureWebJobsMyServiceBus" nevű alkalmazás-beállítást keres. Ha üresen hagyja a `connection`, a functions futtatókörnyezet az alapértelmezett Service Bus a "AzureWebJobsServiceBus" nevű alkalmazás-beállítási karakterláncot használja.<br><br>A kapcsolódási karakterlánc beszerzéséhez kövesse a [felügyeleti hitelesítő adatok beolvasása](../service-bus-messaging/service-bus-quickstart-portal.md#get-the-connection-string)című cikkben ismertetett lépéseket. A kapcsolati karakterláncot a Service Bus-névtér, nem kizárólagosan az adott üzenetsor vagy témakör kell lennie.|
|**accessRights**|**Access (Hozzáférés)**|A kapcsolati karakterlánc hozzáférési jogosultságokat. Az elérhető értékek `manage` és `listen`. Az alapértelmezett érték `manage`, amely azt jelzi, hogy a `connection` rendelkezik a **kezelés** engedéllyel. Ha olyan kapcsolódási karakterláncot használ, amely nem rendelkezik a **kezelés** engedéllyel, állítsa a `accessRights` "Listen" (figyelés) értékre. Ellenkező esetben a Functions runtime meghiúsulhat igénylő műveletek végrehajtását kezelésére. A Azure Functions 2. x vagy újabb verziójában ez a tulajdonság nem érhető el, mert a Service Bus SDK legújabb verziója nem támogatja a kezelési műveleteket.|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="usage"></a>Használat

Azure Functions 1. x esetén a futtatókörnyezet létrehozza a várólistát, ha az nem létezik, és beállította a `accessRights` a `manage`. A functions 2. x vagy újabb verziójában a várólistának vagy a témakörnek már léteznie kell; Ha olyan üzenetsor vagy témakört ad meg, amely nem létezik, a függvény sikertelen lesz. 

# <a name="c"></a>[C#](#tab/csharp)

A kimeneti kötéshez használja a következő paramétereket:

* `out T paramName` - `T` bármely JSON-szerializálható típus lehet. Ha a paraméter értéke null értékű, ha a függvény kilép, Funkciók hoz létre az üzenet Objekt s hodnotou null.
* `out string` – ha a paraméter értéke null, ha a függvény kilép, a functions nem hoz létre üzenetet.
* `out byte[]` – ha a paraméter értéke null, ha a függvény kilép, a functions nem hoz létre üzenetet.
* `out BrokeredMessage` – ha a paraméter értéke null, ha a függvény kilép, a függvények nem hoznak létre üzenetet (az 1. x függvények esetében).
* `out Message` – ha a paraméter értéke null, ha a függvény kilép, a functions nem hoz létre üzenetet (a 2. x és újabb függvények esetében).
* `ICollector<T>` vagy `IAsyncCollector<T>` – több üzenet létrehozásához. A `Add` metódus meghívásakor létrejön egy üzenet.

A C# függvények használatakor:

* Az aszinkron függvények visszatérési értéknek vagy `IAsyncCollector`nak kell lennie `out` paraméter helyett.

* A munkamenet-azonosító eléréséhez kötést [`Message`](https://docs.microsoft.com/dotnet/api/microsoft.azure.servicebus.message) típushoz kell kötni, és a `sessionId` tulajdonságot kell használnia.

# <a name="c-script"></a>[C#Parancsfájl](#tab/csharp-script)

A kimeneti kötéshez használja a következő paramétereket:

* `out T paramName` - `T` bármely JSON-szerializálható típus lehet. Ha a paraméter értéke null értékű, ha a függvény kilép, Funkciók hoz létre az üzenet Objekt s hodnotou null.
* `out string` – ha a paraméter értéke null, ha a függvény kilép, a functions nem hoz létre üzenetet.
* `out byte[]` – ha a paraméter értéke null, ha a függvény kilép, a functions nem hoz létre üzenetet.
* `out BrokeredMessage` – ha a paraméter értéke null, ha a függvény kilép, a függvények nem hoznak létre üzenetet (az 1. x függvények esetében).
* `out Message` – ha a paraméter értéke null, ha a függvény kilép, a functions nem hoz létre üzenetet (a 2. x és újabb függvények esetében).
* `ICollector<T>` vagy `IAsyncCollector<T>` – több üzenet létrehozásához. A `Add` metódus meghívásakor létrejön egy üzenet.

A C# függvények használatakor:

* Az aszinkron függvények visszatérési értéknek vagy `IAsyncCollector`nak kell lennie `out` paraméter helyett.

* A munkamenet-azonosító eléréséhez kötést [`Message`](https://docs.microsoft.com/dotnet/api/microsoft.azure.servicebus.message) típushoz kell kötni, és a `sessionId` tulajdonságot kell használnia.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Az üzenetsor vagy témakör elérése `context.bindings.<name from function.json>`használatával. Hozzárendelhet egy karakterláncot, egy bájt tömböt vagy egy, a JSON-ba szerializált JavaScript-objektumot `context.binding.<name>`.

# <a name="python"></a>[Python](#tab/python)

A beépített kimeneti kötés helyett használja a [Azure Service Bus SDK](https://docs.microsoft.com/azure/service-bus-messaging) -t.

# <a name="java"></a>[Java](#tab/java)

A beépített kimeneti kötés helyett használja a [Azure Service Bus SDK](https://docs.microsoft.com/azure/service-bus-messaging) -t.

---

## <a name="exceptions-and-return-codes"></a>Kivételek és a visszatérési kódok

| Kötés | Referencia |
|---|---|
| Service Bus | [Service Bus hibakódok](https://docs.microsoft.com/azure/service-bus-messaging/service-bus-messaging-exceptions) |
| Service Bus | [Service Bus korlátok](https://docs.microsoft.com/azure/service-bus-messaging/service-bus-quotas) |

<a name="host-json"></a>  

## <a name="hostjson-settings"></a>Host.JSON-beállítások

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
|maxAutoRenewDuration|00:05:00|A maximális időtartamot, amelyen belül az üzenet zárolási újul meg automatikusan.|
|az automatikus kiegészítés|true|Azt jelzi, hogy a triggernek azonnal meg kell-e jelölnie az üzenetet (automatikus kiegészítés), vagy várjon, amíg a függvény sikeresen kilép a hívás befejezéséhez.|
|maxConcurrentCalls|16|Egyidejű hívás, amely az üzenet szivattyú kell kezdeményezni a visszahívás maximális számát. Alapértelmezés szerint a Functions futtatókörnyezete dolgozza fel a több üzenetet egy időben. Ha a futtatókörnyezetet csak egyetlen üzenetsor vagy témakörbeli üzenet feldolgozására szeretné irányítani egyszerre, állítsa a `maxConcurrentCalls` 1 értékre. |

## <a name="next-steps"></a>Következő lépések

- [Függvény futtatása Service Bus üzenetsor vagy témakör-üzenet létrehozásakor (trigger)](./functions-bindings-service-bus-trigger.md)
