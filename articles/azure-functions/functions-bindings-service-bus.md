---
title: Az Azure Szolgáltatásbusz-kötések az Azure Functions szolgáltatáshoz
description: Megtudhatja, hogyan használhatja az Azure Service Bus-eseményindítók és kötések az Azure Functions szolgáltatásban.
author: craigshoemaker
ms.assetid: daedacf0-6546-4355-a65c-50873e74f66b
ms.topic: reference
ms.date: 04/01/2017
ms.author: cshoe
ms.openlocfilehash: d27058ed0ff3044d98d8428b3065b02e2c24c451
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/20/2019
ms.locfileid: "74231052"
---
# <a name="azure-service-bus-bindings-for-azure-functions"></a>Az Azure Szolgáltatásbusz-kötések az Azure Functions szolgáltatáshoz

Ez a cikk ismerteti az Azure Service Bus-kötések az Azure Functions használata. Az Azure Functions támogatja a-trigger, és a kimeneti kötések Service Bus-üzenetsorok és üzenettémák esetében.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="packages---functions-1x"></a>Csomagok – 1.x függvények

A Service Bus kötések a [Microsoft. Azure. webjobs. ServiceBus](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.ServiceBus) NuGet csomagban találhatók, 2. x verzióban. 

[!INCLUDE [functions-package](../../includes/functions-package.md)]

## <a name="packages---functions-2x"></a>Csomagok – 2.x függvények

A Service Bus kötések a [Microsoft. Azure. webjobs. Extensions. ServiceBus](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.ServiceBus) NuGet csomag 3. x verziójában érhetők el. A csomag forráskódja az [Azure-functions-servicebus-Extension](https://github.com/Azure/azure-functions-servicebus-extension) GitHub-tárházban található.

> [!NOTE]
> A 2. x verzió nem hozza létre a `ServiceBusTrigger`-példányban konfigurált témakört vagy előfizetést. A 2. x verzió a [Microsoft. Azure. ServiceBus](https://www.nuget.org/packages/Microsoft.Azure.ServiceBus) alapul, és nem kezeli a várólista-kezelést.

[!INCLUDE [functions-package-v2](../../includes/functions-package-v2.md)]

## <a name="trigger"></a>Eseményindító

A Service Bus-trigger használatával üzeneteket egy Service Bus-üzenetsor vagy témakör válaszol. 

## <a name="trigger---example"></a>Az eseményindító – példa

Tekintse meg az adott nyelvű példa:

* [C#](#trigger---c-example)
* [C#parancsfájl (. CSX)](#trigger---c-script-example)
* [F#](#trigger---f-example)
* [Java](#trigger---java-example)
* [JavaScript](#trigger---javascript-example)
* [Python](#trigger---python-example)

### <a name="trigger---c-example"></a>Eseményindító - C#-példa

Az alábbi példa egy olyan [ C# függvényt](functions-dotnet-class-library.md) mutat be, amely beolvassa az [üzenet metaadatait](#trigger---message-metadata) , és naplóz egy Service Bus üzenetsor-üzenetet:

```cs
[FunctionName("ServiceBusQueueTriggerCSharp")]                    
public static void Run(
    [ServiceBusTrigger("myqueue", Connection = "ServiceBusConnection")] 
    string myQueueItem,
    Int32 deliveryCount,
    DateTime enqueuedTimeUtc,
    string messageId,
    ILogger log)
{
    log.LogInformation($"C# ServiceBus queue trigger function processed message: {myQueueItem}");
    log.LogInformation($"EnqueuedTimeUtc={enqueuedTimeUtc}");
    log.LogInformation($"DeliveryCount={deliveryCount}");
    log.LogInformation($"MessageId={messageId}");
}
```

### <a name="trigger---c-script-example"></a>Eseményindító - C#-szkript példa

Az alábbi példa egy Service Bus trigger kötést mutat be egy *function. JSON* fájlban, valamint egy olyan [ C# parancsfájl-függvényt](functions-reference-csharp.md) , amely a kötést használja. A függvény beolvassa az [üzenet metaadatait](#trigger---message-metadata) , és naplóz egy Service Bus üzenetsor-üzenetet.

Itt található a *function. JSON* fájlban található kötési adat:

```json
{
"bindings": [
    {
    "queueName": "testqueue",
    "connection": "MyServiceBusConnection",
    "name": "myQueueItem",
    "type": "serviceBusTrigger",
    "direction": "in"
    }
],
"disabled": false
}
```

Íme a C#-szkriptkódot:

```cs
using System;

public static void Run(string myQueueItem,
    Int32 deliveryCount,
    DateTime enqueuedTimeUtc,
    string messageId,
    TraceWriter log)
{
    log.Info($"C# ServiceBus queue trigger function processed message: {myQueueItem}");

    log.Info($"EnqueuedTimeUtc={enqueuedTimeUtc}");
    log.Info($"DeliveryCount={deliveryCount}");
    log.Info($"MessageId={messageId}");
}
```

### <a name="trigger---f-example"></a>Eseményindító - F# példa

Az alábbi példa egy Service Bus trigger kötést mutat be egy *function. JSON* fájlban, valamint egy olyan [ F# függvényt](functions-reference-fsharp.md) , amely a kötést használja. A függvény egy Service Bus üzenetsor-üzenetet naplózza. 

Itt található a *function. JSON* fájlban található kötési adat:

```json
{
"bindings": [
    {
    "queueName": "testqueue",
    "connection": "MyServiceBusConnection",
    "name": "myQueueItem",
    "type": "serviceBusTrigger",
    "direction": "in"
    }
],
"disabled": false
}
```

Íme a F# parancsfájl-kódot:

```fsharp
let Run(myQueueItem: string, log: ILogger) =
    log.LogInformation(sprintf "F# ServiceBus queue trigger function processed message: %s" myQueueItem)
```

### <a name="trigger---java-example"></a>Eseményindító - Java-példában

A következő Java-függvény a [Java functions runtime library](/java/api/overview/azure/functions/runtime) `@ServiceBusQueueTrigger` megjegyzését használja egy Service Bus üzenetsor-trigger konfigurációjának leírásához. A függvény megragadja a várólistára helyezett üzenetet, és hozzáadja azt a naplókhoz.

```java
@FunctionName("sbprocessor")
 public void serviceBusProcess(
    @ServiceBusQueueTrigger(name = "msg",
                             queueName = "myqueuename",
                             connection = "myconnvarname") String message,
   final ExecutionContext context
 ) {
     context.getLogger().info(message);
 }
```

A Java-függvények akkor is elindíthatók, amikor egy üzenet bekerül egy Service Bus témakörbe. Az alábbi példa a `@ServiceBusTopicTrigger` jegyzetet használja az trigger konfigurációjának leírásához.

```java
@FunctionName("sbtopicprocessor")
    public void run(
        @ServiceBusTopicTrigger(
            name = "message",
            topicName = "mytopicname",
            subscriptionName = "mysubscription",
            connection = "ServiceBusConnection"
        ) String message,
        final ExecutionContext context
    ) {
        context.getLogger().info(message);
    }
```

### <a name="trigger---javascript-example"></a>Eseményindító - JavaScript-példa

Az alábbi példa egy Service Bus trigger kötést mutat be egy *function. JSON* fájlban, valamint egy [JavaScript-függvényt](functions-reference-node.md) , amely a kötést használja. A függvény beolvassa az [üzenet metaadatait](#trigger---message-metadata) , és naplóz egy Service Bus üzenetsor-üzenetet. 

Itt található a *function. JSON* fájlban található kötési adat:

```json
{
"bindings": [
    {
    "queueName": "testqueue",
    "connection": "MyServiceBusConnection",
    "name": "myQueueItem",
    "type": "serviceBusTrigger",
    "direction": "in"
    }
],
"disabled": false
}
```

Íme a szkript JavaScript-kódot:

```javascript
module.exports = function(context, myQueueItem) {
    context.log('Node.js ServiceBus queue trigger function processed message', myQueueItem);
    context.log('EnqueuedTimeUtc =', context.bindingData.enqueuedTimeUtc);
    context.log('DeliveryCount =', context.bindingData.deliveryCount);
    context.log('MessageId =', context.bindingData.messageId);
    context.done();
};
```

### <a name="trigger---python-example"></a>Trigger – Python-példa

Az alábbi példa bemutatja, hogyan olvashat egy ServiceBus üzenetsor-üzenetet egy trigger használatával.

ServiceBus kötés van definiálva a *function. JSON* fájlban, ahol a *Type* értéke `serviceBusTrigger`.

```json
{
  "scriptFile": "__init__.py",
  "bindings": [
    {
      "name": "msg",
      "type": "serviceBusTrigger",
      "direction": "in",
      "queueName": "inputqueue",
      "connection": "AzureServiceBusConnectionString"
    }
  ]
}
```

*_\_init_\_. a. a. a. a.* a (z `func.ServiceBusMessage`)

```python
import azure.functions as func

import logging
import json

def main(msg: func.ServiceBusMessage):
    logging.info('Python ServiceBus queue trigger processed message.')

    result = json.dumps({
        'message_id': msg.message_id,
        'body': msg.get_body().decode('utf-8'),
        'content_type': msg.content_type,
        'expiration_time': msg.expiration_time,
        'label': msg.label,
        'partition_key': msg.partition_key,
        'reply_to': msg.reply_to,
        'reply_to_session_id': msg.reply_to_session_id,
        'scheduled_enqueue_time': msg.scheduled_enqueue_time,
        'session_id': msg.session_id,
        'time_to_live': msg.time_to_live,
        'to': msg.to,
        'user_properties': msg.user_properties,
    })

    logging.info(result)
```

## <a name="trigger---attributes"></a>Eseményindító - attribútumok

Az [ C# osztályok könyvtáraiban](functions-dotnet-class-library.md)használja a következő attribútumokat egy Service Bus trigger konfigurálásához:

* [ServiceBusTriggerAttribute](https://github.com/Azure/azure-functions-servicebus-extension/blob/master/src/Microsoft.Azure.WebJobs.Extensions.ServiceBus/ServiceBusTriggerAttribute.cs)

  Az attribútum konstruktorának paramétereként az üzenetsor vagy témakör és előfizetés nevét. Az Azure Functions verzió 1.x, azt is megadhatja a kapcsolat hozzáférési jogosultságokat. Ha nem ad meg hozzáférési jogosultságokat, az alapértelmezett érték `Manage`. További információ: [trigger-Configuration](#trigger---configuration) szakasz.

  A következő példa bemutatja, az attribútum egy karakterlánc-paraméterrel használja:

  ```csharp
  [FunctionName("ServiceBusQueueTriggerCSharp")]                    
  public static void Run(
      [ServiceBusTrigger("myqueue")] string myQueueItem, ILogger log)
  {
      ...
  }
  ```

  A `Connection` tulajdonság beállításával megadhatja a használni kívánt Service Bus fiókot, ahogy az az alábbi példában is látható:

  ```csharp
  [FunctionName("ServiceBusQueueTriggerCSharp")]                    
  public static void Run(
      [ServiceBusTrigger("myqueue", Connection = "ServiceBusConnection")] 
      string myQueueItem, ILogger log)
  {
      ...
  }
  ```

  A teljes példa: [trigger- C# example](#trigger---c-example).

* [ServiceBusAccountAttribute](https://github.com/Azure/azure-functions-servicebus-extension/blob/master/src/Microsoft.Azure.WebJobs.Extensions.ServiceBus/ServiceBusAccountAttribute.cs)

  Adja meg a Service Bus használni kívánt fiók egy másik megoldást kínál. A konstruktorban vesz igénybe egy Service Bus kapcsolati karakterláncot tartalmazó alkalmazásbeállítás neve. Az attribútum a paramétert, a metódus vagy az osztály szintjén alkalmazhatók. Az alábbi példa bemutatja az osztály és metódust:

  ```csharp
  [ServiceBusAccount("ClassLevelServiceBusAppSetting")]
  public static class AzureFunctions
  {
      [ServiceBusAccount("MethodLevelServiceBusAppSetting")]
      [FunctionName("ServiceBusQueueTriggerCSharp")]
      public static void Run(
          [ServiceBusTrigger("myqueue", AccessRights.Manage)] 
          string myQueueItem, ILogger log)
  {
      ...
  }
  ```

A Service Bus használni kívánt fiók határozza meg a következő sorrendben:

* A `ServiceBusTrigger` attribútum `Connection` tulajdonsága.
* A `ServiceBusAccount` attribútum a `ServiceBusTrigger` attribútummal megegyező paraméterre lett alkalmazva.
* A függvényre alkalmazott `ServiceBusAccount` attribútum.
* Az osztályra alkalmazott `ServiceBusAccount` attribútum.
* Az "AzureWebJobsServiceBus" alkalmazásbeállítást.

## <a name="trigger---configuration"></a>Eseményindító - konfiguráció

Az alábbi táblázat a *function. JSON* fájlban és a `ServiceBusTrigger` attribútumban beállított kötési konfigurációs tulajdonságokat ismerteti.

|Function.JSON tulajdonság | Attribútum tulajdonsága |Leírás|
|---------|---------|----------------------|
|**type** | n/a | Állítsa "serviceBusTrigger". Ez a tulajdonság beállítása automatikusan történik, ha az eseményindítót fog létrehozni az Azure Portalon.|
|**direction** | n/a | Meg kell "a". Ez a tulajdonság beállítása automatikusan történik, ha az eseményindítót fog létrehozni az Azure Portalon. |
|**név** | n/a | A függvénykódot az üzenetsor vagy témakör üzenet képviselő változó neve. "$Return" hivatkozik a függvény visszaadott értékének beállításával. |
|**queueName**|**QueueName**|Figyelni kívánt üzenetsor neve.  Csak akkor, ha egy üzenetsort, nem a témakör a figyelés beállítása.
|**topicName**|**TopicName**|Figyelendő üzenettéma nevére. Csak akkor, ha a figyelést egy üzenetsort, nem egy témakör beállítása.|
|**subscriptionName**|**SubscriptionName**|Neve a figyelni kívánt előfizetést. Csak akkor, ha a figyelést egy üzenetsort, nem egy témakör beállítása.|
|**kapcsolat**|**Kapcsolat**|A Service Bus kapcsolati karakterlánc használata ehhez a kötéshez tartalmazó alkalmazásbeállítás neve. Azon alkalmazásbeállítás neve "AzureWebJobs" kezdődik, ha csak a név része is megadhat. Ha például a `connection` "MyServiceBus" értékre állítja, a functions futtatókörnyezet egy "AzureWebJobsMyServiceBus" nevű alkalmazás-beállítást keres. Ha üresen hagyja a `connection`, a functions futtatókörnyezet az alapértelmezett Service Bus a "AzureWebJobsServiceBus" nevű alkalmazás-beállítási karakterláncot használja.<br><br>A kapcsolódási karakterlánc beszerzéséhez kövesse a [felügyeleti hitelesítő adatok beolvasása](../service-bus-messaging/service-bus-quickstart-portal.md#get-the-connection-string)című cikkben ismertetett lépéseket. A kapcsolati karakterláncot a Service Bus-névtér, nem kizárólagosan az adott üzenetsor vagy témakör kell lennie. |
|**accessRights**|**Access (Hozzáférés)**|A kapcsolati karakterlánc hozzáférési jogosultságokat. Az elérhető értékek `manage` és `listen`. Az alapértelmezett érték `manage`, amely azt jelzi, hogy a `connection` rendelkezik a **kezelés** engedéllyel. Ha olyan kapcsolódási karakterláncot használ, amely nem rendelkezik a **kezelés** engedéllyel, állítsa a `accessRights` "Listen" (figyelés) értékre. Ellenkező esetben a Functions runtime meghiúsulhat igénylő műveletek végrehajtását kezelésére. Az Azure Functions-verzió 2.x, ez a tulajdonság nem érhető el, mert a Storage SDK legújabb verziója nem támogatja a műveletek kezelésére.|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="trigger---usage"></a>Eseményindító - használat

Az üzenetsor vagy témakör üzenet a C# és a C#-szkript, használhatja a következő paraméter típusa:

* `string` – ha az üzenet szövege.
* `byte[]` – hasznos a bináris adattároláshoz.
* Egy egyéni típusa – Ha az üzenet tartalmazza JSON-t, az Azure Functions próbálja meg a JSON-adatok deszerializálása.
* `BrokeredMessage` – a deszerializált üzenetet adja a [BrokeredMessage. GetBody\<t > ()](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.getbody?view=azure-dotnet#Microsoft_ServiceBus_Messaging_BrokeredMessage_GetBody__1) metódussal.

Ezek a paraméterek az 1. x Azure Functions-verzióra vonatkoznak. a 2. x esetében `BrokeredMessage`helyett használja a [`Message`](https://docs.microsoft.com/dotnet/api/microsoft.azure.servicebus.message) .

A JavaScriptben a `context.bindings.<name from function.json>`használatával férhet hozzá az üzenetsor vagy a témakörhöz. A Service Bus-üzenet, a függvény egy karakterlánc vagy a JSON-objektum lett átadva.

## <a name="trigger---poison-messages"></a>Eseményindító - ártalmas üzenetek

Ártalmas üzenetek kezelése nem szabályozza és nem konfigurált az Azure Functions szolgáltatásban. A Service Bus kezeli az ártalmas üzenetek magát.

## <a name="trigger---peeklock-behavior"></a>Eseményindító - PeekLock viselkedés

A functions futtatókörnyezet [PeekLock módban](../service-bus-messaging/service-bus-performance-improvements.md#receive-mode)fogad üzenetet. Ha a függvény sikeresen befejeződik, meghívja a `Complete`t az üzenetben, vagy meghívja a `Abandon`, ha a függvény meghibásodik. Ha a függvény a `PeekLock` időtúllépésnél hosszabb ideig fut, akkor a rendszer automatikusan megújítja a zárolást, amíg a függvény fut. 

A `maxAutoRenewDuration` a *Host. JSON*fájlban konfigurálható, amely a [OnMessageOptions. MaxAutoRenewDuration](https://docs.microsoft.com/dotnet/api/microsoft.azure.servicebus.messagehandleroptions.maxautorenewduration?view=azure-dotnet)leképezésére szolgál. Ez a beállítás számára engedélyezett maximális érték megfelelően a Service Bus dokumentációja, 5 perc, míg 10 perc az alapértelmezett 5 perces időbeli függvények korlát növeléséhez. A Service Bus-funkciók nem szeretné, amely majd, mivel a Service Bus-megújítási korlát túllépését eredményezné.

## <a name="trigger---message-metadata"></a>Eseményindító - üzenet metaadatok

A Service Bus trigger számos [metaadat-tulajdonságot](./functions-bindings-expressions-patterns.md#trigger-metadata)biztosít. Ezek a tulajdonságok a kötési kifejezésekben való használata más kötések részeként vagy a kód paramétereiben használható. Ezek a [BrokeredMessage](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.brokeredmessage) osztály tulajdonságai.

|Tulajdonság|Típus|Leírás|
|--------|----|-----------|
|`DeliveryCount`|`Int32`|A kézbesítések száma.|
|`DeadLetterSource`|`string`|Kézbesítetlen levelek forrása.|
|`ExpiresAtUtc`|`DateTime`|A lejárat időpontja (UTC).|
|`EnqueuedTimeUtc`|`DateTime`|A várólistán lévő időpontja (UTC).|
|`MessageId`|`string`|Egy felhasználó által definiált érték, amely a Service Bus duplikált üzenetek azonosítására használható, ha engedélyezve van.|
|`ContentType`|`string`|A tartalomtípus a küldő és fogadó az adott logikai alkalmazás által használt azonosítója|
|`ReplyTo`|`string`|A válasz várólista-címre.|
|`SequenceNumber`|`Int64`|Egy üzenetet a Service Bus által hozzárendelt egyedi szám.|
|`To`|`string`|A küldési címre.|
|`Label`|`string`|Az alkalmazásspecifikus címke.|
|`CorrelationId`|`string`|A korrelációs azonosítót.|

> [!NOTE]
> Jelenleg a Service Bus-trigger, amely a munkamenet-kompatibilis várólistákkal és előfizetésekkel működik, előzetes verzióban érhető el. Ezt az [adott tételt](https://github.com/Azure/azure-webjobs-sdk/issues/529#issuecomment-491113458) a további frissítésekkel kapcsolatban nyomon követheti. 

Tekintse meg a jelen cikk korábbi részében említett tulajdonságokat használó [példákat](#trigger---example) .

## <a name="output"></a>Kimenet

Azure Service Bus kimeneti kötés használatával üzenetsor vagy témakör üzeneteket küldeni.

## <a name="output---example"></a>Kimenete – példa

Tekintse meg az adott nyelvű példa:

* [C#](#output---c-example)
* [C#parancsfájl (. CSX)](#output---c-script-example)
* [F#](#output---f-example)
* [Java](#output---java-example)
* [JavaScript](#output---javascript-example)
* [Python](#output---python-example)

### <a name="output---c-example"></a>Kimenet – C#-példa

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

### <a name="output---c-script-example"></a>Kimenet – C#-szkript példa

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

### <a name="output---f-example"></a>Kimenete – F# példa

Az alábbi példa egy Service Bus kimeneti kötést mutat be egy *function. JSON* fájlban, valamint egy olyan [ F# parancsfájl-függvényt](functions-reference-fsharp.md) , amely a kötést használja. A függvény egy időzítő eseményindító 15 másodpercenként üzenetsori üzenetek küldéséhez használja.

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

Íme F# parancsfájl-kódot, amely létrehozza egy adott üzenet:

```fsharp
let Run(myTimer: TimerInfo, log: ILogger, outputSbQueue: byref<string>) =
    let message = sprintf "Service Bus queue message created at: %s" (DateTime.Now.ToString())
    log.LogInformation(message)
    outputSbQueue = message
```

### <a name="output---java-example"></a>Kimenet – Java-példában

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

### <a name="output---javascript-example"></a>Kimenet – JavaScript-példa

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

### <a name="output---python-example"></a>Kimenet – Python-példa

Az alábbi példa bemutatja, hogyan írhat a Pythonban egy ServiceBus-várólistára.

A *function. JSON* fájlban egy ServiceBue-kötési definíció van megadva, ahol a *Type* értéke `serviceBus`.

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

## <a name="output---attributes"></a>Kimenet – attribútumok

Az [ C# osztályok könyvtáraiban](functions-dotnet-class-library.md)használja a [ServiceBusAttribute](https://github.com/Azure/azure-functions-servicebus-extension/blob/master/src/Microsoft.Azure.WebJobs.Extensions.ServiceBus/ServiceBusAttribute.cs).

Az attribútum konstruktorának paramétereként az üzenetsor vagy témakör és előfizetés nevét. A kapcsolat hozzáférési jogosultságokat is megadhatja. A hozzáférési jogosultságok beállításának kiválasztását a [kimenet – konfiguráció](#output---configuration) szakasz ismerteti. A következő példa bemutatja, az attribútum a függvény visszatérési értéke a alkalmazni:

```csharp
[FunctionName("ServiceBusOutput")]
[return: ServiceBus("myqueue")]
public static string Run([HttpTrigger] dynamic input, ILogger log)
{
    ...
}
```

A `Connection` tulajdonság beállításával megadhatja a használni kívánt Service Bus fiókot, ahogy az az alábbi példában is látható:

```csharp
[FunctionName("ServiceBusOutput")]
[return: ServiceBus("myqueue", Connection = "ServiceBusConnection")]
public static string Run([HttpTrigger] dynamic input, ILogger log)
{
    ...
}
```

Teljes példa: [kimenet – C# példa](#output---c-example).

A `ServiceBusAccount` attribútum segítségével megadhatja az osztály, metódus vagy paraméter szintjén használandó Service Bus fiókot.  További információ: [trigger-attributes](#trigger---attributes).

## <a name="output---configuration"></a>Kimenete – konfiguráció

Az alábbi táblázat a *function. JSON* fájlban és a `ServiceBus` attribútumban beállított kötési konfigurációs tulajdonságokat ismerteti.

|Function.JSON tulajdonság | Attribútum tulajdonsága |Leírás|
|---------|---------|----------------------|
|**type** | n/a | "A serviceBus" kell állítani. Ez a tulajdonság beállítása automatikusan történik, ha az eseményindítót fog létrehozni az Azure Portalon.|
|**direction** | n/a | Állítsa "out". Ez a tulajdonság beállítása automatikusan történik, ha az eseményindítót fog létrehozni az Azure Portalon. |
|**név** | n/a | Az üzenetsor vagy témakör, a függvény kódját képviselő változó neve. "$Return" hivatkozik a függvény visszaadott értékének beállításával. |
|**queueName**|**QueueName**|Az üzenetsor neve.  Állítsa be, csak akkor, ha üzenetküldésre üzenetsor, témakör esetében nem.
|**topicName**|**TopicName**|Figyelendő üzenettéma nevére. Csak akkor, ha üzenetküldésre témakör, nem a várólista állítsa be.|
|**kapcsolat**|**Kapcsolat**|A Service Bus kapcsolati karakterlánc használata ehhez a kötéshez tartalmazó alkalmazásbeállítás neve. Azon alkalmazásbeállítás neve "AzureWebJobs" kezdődik, ha csak a név része is megadhat. Ha például a `connection` "MyServiceBus" értékre állítja, a functions futtatókörnyezet egy "AzureWebJobsMyServiceBus" nevű alkalmazás-beállítást keres. Ha üresen hagyja a `connection`, a functions futtatókörnyezet az alapértelmezett Service Bus a "AzureWebJobsServiceBus" nevű alkalmazás-beállítási karakterláncot használja.<br><br>A kapcsolódási karakterlánc beszerzéséhez kövesse a [felügyeleti hitelesítő adatok beolvasása](../service-bus-messaging/service-bus-quickstart-portal.md#get-the-connection-string)című cikkben ismertetett lépéseket. A kapcsolati karakterláncot a Service Bus-névtér, nem kizárólagosan az adott üzenetsor vagy témakör kell lennie.|
|**accessRights**|**Access (Hozzáférés)**|A kapcsolati karakterlánc hozzáférési jogosultságokat. Az elérhető értékek `manage` és `listen`. Az alapértelmezett érték `manage`, amely azt jelzi, hogy a `connection` rendelkezik a **kezelés** engedéllyel. Ha olyan kapcsolódási karakterláncot használ, amely nem rendelkezik a **kezelés** engedéllyel, állítsa a `accessRights` "Listen" (figyelés) értékre. Ellenkező esetben a Functions runtime meghiúsulhat igénylő műveletek végrehajtását kezelésére. Az Azure Functions-verzió 2.x, ez a tulajdonság nem érhető el, mert a Storage SDK legújabb verziója nem támogatja a műveletek kezelésére.|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="output---usage"></a>Kimenet – használat

Azure Functions 1. x esetén a futtatókörnyezet létrehozza a várólistát, ha az nem létezik, és beállította a `accessRights` a `manage`. A Functions verzió 2.x, az üzenetsor vagy témakör már léteznie kell; Ha megad egy üzenetsorba vagy témakörbe, amely nem létezik, a függvény sikertelen lesz. 

A kimeneti kötési C# és C#-szkript, használhatja a következő paraméter típusa:

* `out T paramName` - `T` bármely JSON-szerializálható típus lehet. Ha a paraméter értéke null értékű, ha a függvény kilép, Funkciók hoz létre az üzenet Objekt s hodnotou null.
* `out string` – ha a paraméter értéke null, ha a függvény kilép, a functions nem hoz létre üzenetet.
* `out byte[]` – ha a paraméter értéke null, ha a függvény kilép, a functions nem hoz létre üzenetet.
* `out BrokeredMessage` – ha a paraméter értéke null, ha a függvény kilép, a függvények nem hoznak létre üzenetet (az 1. x függvények esetében).
* `out Message` – ha a paraméter értéke null, ha a függvény kilép, a functions nem hoz létre üzenetet (a 2. x függvényhez)
* `ICollector<T>` vagy `IAsyncCollector<T>` – több üzenet létrehozásához. A `Add` metódus meghívásakor létrejön egy üzenet.

A C# függvények használatakor:

* Az aszinkron függvények visszatérési értéknek vagy `IAsyncCollector`nak kell lennie `out` paraméter helyett.

* A munkamenet-azonosító eléréséhez kötést [`Message`](https://docs.microsoft.com/dotnet/api/microsoft.azure.servicebus.message) típushoz kell kötni, és a `sessionId` tulajdonságot kell használnia.

A JavaScriptben a `context.bindings.<name from function.json>`használatával férhet hozzá az üzenetsor vagy a témakörhöz. Hozzárendelhet egy karakterláncot, egy bájt tömböt vagy egy, a JSON-ba szerializált JavaScript-objektumot `context.binding.<name>`.

Ha nemC# nyelven szeretne üzenetet küldeni egy munkamenet-kompatibilis várólistához, használja a [Azure Service Bus SDK](https://docs.microsoft.com/azure/service-bus-messaging) -t a beépített kimeneti kötés helyett.

## <a name="exceptions-and-return-codes"></a>Kivételek és a visszatérési kódok

| Kötés | Referencia |
|---|---|
| Service Bus | [Service Bus hibakódok](https://docs.microsoft.com/azure/service-bus-messaging/service-bus-messaging-exceptions) |
| Service Bus | [Service Bus korlátok](https://docs.microsoft.com/azure/service-bus-messaging/service-bus-quotas) |

<a name="host-json"></a>  

## <a name="hostjson-settings"></a>Host.JSON-beállítások

Ez a szakasz ismerteti a globális konfigurációs beállításoknak a kötéshez verziójában elérhető 2.x. Az alábbi példa host.json-fájl csak a verzió 2.x beállításait tartalmazza ezt a kötést. A 2. x verziójú globális konfigurációs beállításokkal kapcsolatos további információkért lásd: [Host. JSON-dokumentáció Azure functions 2. x verzióhoz](functions-host-json.md).

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
            }
        }
    }
}
```

|Tulajdonság  |Alapértelmezett | Leírás |
|---------|---------|---------|
|maxAutoRenewDuration|00:05:00|A maximális időtartamot, amelyen belül az üzenet zárolási újul meg automatikusan.|
|az automatikus kiegészítés|true|E az eseményindító kell azonnal minősítés befejezése (automatikus kiegészítés), vagy várjon, amíg meghívásához teljes feldolgozása.|
|maxConcurrentCalls|16|Egyidejű hívás, amely az üzenet szivattyú kell kezdeményezni a visszahívás maximális számát. Alapértelmezés szerint a Functions futtatókörnyezete dolgozza fel a több üzenetet egy időben. Ha a futtatókörnyezetet csak egyetlen üzenetsor vagy témakörbeli üzenet feldolgozására szeretné irányítani egyszerre, állítsa a `maxConcurrentCalls` 1 értékre. |
|prefetchCount|n/a|Az alapértelmezett PrefetchCount, amely az alapul szolgáló MessageReceiver használni fog.|


## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [További információ az Azure functions-eseményindítók és-kötésekről](functions-triggers-bindings.md)
