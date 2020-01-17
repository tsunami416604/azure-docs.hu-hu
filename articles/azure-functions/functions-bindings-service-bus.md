---
title: Azure Functions-kötések Azure Service Bus
description: Megtudhatja, hogyan használhatók Azure Service Bus eseményindítók és kötések a Azure Functionsban.
author: craigshoemaker
ms.assetid: daedacf0-6546-4355-a65c-50873e74f66b
ms.topic: reference
ms.date: 04/01/2017
ms.author: cshoe
ms.openlocfilehash: ca19aefdd213331214938b2af6c9a77501333fb0
ms.sourcegitcommit: 5bbe87cf121bf99184cc9840c7a07385f0d128ae
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/16/2020
ms.locfileid: "76121216"
---
# <a name="azure-service-bus-bindings-for-azure-functions"></a>Azure Functions-kötések Azure Service Bus

Ez a cikk azt ismerteti, hogyan használhatók Azure Service Bus kötések a Azure Functionsban. Az Azure Functions támogatja a triggerek és kimenetek kötését a Service Bus-üzenetsorok és -témakörök esetében.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="packages---functions-1x"></a>Csomagok – 1. x függvények

A Service Bus kötések a [Microsoft. Azure. webjobs. ServiceBus](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.ServiceBus) NuGet csomagban találhatók, 2. x verzióban. 

[!INCLUDE [functions-package](../../includes/functions-package.md)]

## <a name="packages---functions-2x-and-higher"></a>Csomagok – 2. x és újabb függvények

A Service Bus kötések a [Microsoft. Azure. webjobs. Extensions. ServiceBus](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.ServiceBus) NuGet csomag 3. x verziójában érhetők el. A csomag forráskódja az [Azure-functions-servicebus-Extension](https://github.com/Azure/azure-functions-servicebus-extension) GitHub-tárházban található.

> [!NOTE]
> A 2. x és újabb verziók nem hozza létre a `ServiceBusTrigger`-példányban konfigurált témakört vagy előfizetést. Ezek a verziók a [Microsoft. Azure. ServiceBus](https://www.nuget.org/packages/Microsoft.Azure.ServiceBus) alapulnak, amely nem kezeli a várólista-kezelést.

[!INCLUDE [functions-package-v2](../../includes/functions-package-v2.md)]

## <a name="trigger"></a>Eseményindító

A Service Bus triggerrel válaszolhat Service Bus üzenetsor vagy témakör üzeneteire. 

## <a name="trigger---example"></a>Trigger – példa

Tekintse meg a nyelvspecifikus példát:

* [C#](#trigger---c-example)
* [C#parancsfájl (. CSX)](#trigger---c-script-example)
* [F#](#trigger---f-example)
* [Java](#trigger---java-example)
* [JavaScript](#trigger---javascript-example)
* [Python](#trigger---python-example)

### <a name="trigger---c-example"></a>Trigger – C# példa

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

### <a name="trigger---c-script-example"></a>Trigger – C# parancsfájl-példa

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

A C# szkript kódja:

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

### <a name="trigger---f-example"></a>Trigger – F# példa

Az alábbi példa egy Service Bus trigger kötést mutat be egy *function. JSON* fájlban, valamint egy olyan [ F# függvényt](functions-reference-fsharp.md) , amely a kötést használja. A függvény naplóz egy Service Bus üzenetsor-üzenetet. 

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

A F# szkript kódja:

```fsharp
let Run(myQueueItem: string, log: ILogger) =
    log.LogInformation(sprintf "F# ServiceBus queue trigger function processed message: %s" myQueueItem)
```

### <a name="trigger---java-example"></a>Trigger – Java-példa

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

### <a name="trigger---javascript-example"></a>Trigger – JavaScript-példa

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

Itt látható a JavaScript-szkript kódja:

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

Az alábbi példa bemutatja, hogyan olvashat egy Service Bus üzenetsor-üzenetet egy trigger használatával.

Service Bus kötés van definiálva a *function. JSON* fájlban, ahol a *Type* értéke `serviceBusTrigger`.

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

## <a name="trigger---attributes"></a>Trigger – attribútumok

Az [ C# osztályok könyvtáraiban](functions-dotnet-class-library.md)használja a következő attribútumokat egy Service Bus trigger konfigurálásához:

* [ServiceBusTriggerAttribute](https://github.com/Azure/azure-functions-servicebus-extension/blob/master/src/Microsoft.Azure.WebJobs.Extensions.ServiceBus/ServiceBusTriggerAttribute.cs)

  Az attribútum konstruktora a várólista nevét, illetve a témakört és az előfizetést veszi át. A Azure Functions 1. x verziójában megadhatja a kapcsolat hozzáférési jogosultságait is. Ha nem ad meg hozzáférési jogosultságokat, az alapértelmezett érték `Manage`. További információ: [trigger-Configuration](#trigger---configuration) szakasz.

  Az alábbi példa egy sztring paraméterrel használt attribútumot mutatja be:

  ```csharp
  [FunctionName("ServiceBusQueueTriggerCSharp")]                    
  public static void Run(
      [ServiceBusTrigger("myqueue")] string myQueueItem, ILogger log)
  {
      ...
  }
  ```

  A `Connection` tulajdonság beállításával megadhatja a használni kívánt Service Bus-kapcsolódási karakterláncot tartalmazó Alkalmazásbeállítások nevét, ahogy az alábbi példában is látható:

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

  Egy másik módszert biztosít a használni kívánt Service Bus-fiók megadásához. A konstruktor egy Service Bus kapcsolatot megadó karakterláncot tartalmazó Alkalmazásbeállítás nevét veszi fel. Az attribútumot a paraméter, a metódus vagy az osztály szintjén lehet alkalmazni. Az alábbi példa az osztály szintjét és a metódus szintjét mutatja be:

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

A használni kívánt Service Bus fiók a következő sorrendben van meghatározva:

* A `ServiceBusTrigger` attribútum `Connection` tulajdonsága.
* A `ServiceBusAccount` attribútum a `ServiceBusTrigger` attribútummal megegyező paraméterre lett alkalmazva.
* A függvényre alkalmazott `ServiceBusAccount` attribútum.
* Az osztályra alkalmazott `ServiceBusAccount` attribútum.
* A "AzureWebJobsServiceBus" alkalmazás beállításai.

## <a name="trigger---configuration"></a>Trigger – konfiguráció

Az alábbi táblázat a *function. JSON* fájlban és a `ServiceBusTrigger` attribútumban beállított kötési konfigurációs tulajdonságokat ismerteti.

|function. JSON-tulajdonság | Attribútum tulajdonsága |Leírás|
|---------|---------|----------------------|
|**type** | – | "ServiceBusTrigger" értékre kell állítani. Ez a tulajdonság automatikusan be van állítva, amikor létrehozza az triggert a Azure Portalban.|
|**direction** | – | "In" értékre kell állítani. Ez a tulajdonság automatikusan be van állítva, amikor létrehozza az triggert a Azure Portalban. |
|**név** | – | Annak a változónak a neve, amely a függvény kódjában szereplő üzenetsor vagy témakör üzenetét jelöli. |
|**queueName**|**QueueName**|A figyelni kívánt várólista neve.  Csak akkor állítható be, ha egy üzenetsor figyelése nem a témakörhöz szükséges.
|**topicName**|**TopicName**|A figyelni kívánt témakör neve. Csak akkor állítsa be, ha egy témakör figyelése nem várólistára van állítva.|
|**subscriptionName**|**SubscriptionName**|A figyelni kívánt előfizetés neve. Csak akkor állítsa be, ha egy témakör figyelése nem várólistára van állítva.|
|**kapcsolat**|**Kapcsolat**|A kötéshez használni kívánt Service Bus kapcsolati karakterláncot tartalmazó Alkalmazásbeállítás neve. Ha az Alkalmazásbeállítások neve "AzureWebJobs" előtaggal kezdődik, akkor csak a név hátralévő részét adhatja meg. Ha például a `connection` "MyServiceBus" értékre állítja, a functions futtatókörnyezet egy "AzureWebJobsMyServiceBus" nevű alkalmazás-beállítást keres. Ha üresen hagyja a `connection`, a functions futtatókörnyezet az alapértelmezett Service Bus a "AzureWebJobsServiceBus" nevű alkalmazás-beállítási karakterláncot használja.<br><br>A kapcsolódási karakterlánc beszerzéséhez kövesse a [felügyeleti hitelesítő adatok beolvasása](../service-bus-messaging/service-bus-quickstart-portal.md#get-the-connection-string)című cikkben ismertetett lépéseket. A kapcsolódási karakterláncnak egy Service Bus névtérhez kell tartoznia, nem csak egy adott várólistára vagy témakörre. |
|**accessRights**|**Access (Hozzáférés)**|Hozzáférési jogosultságok a kapcsolati karakterlánchoz. Az elérhető értékek `manage` és `listen`. Az alapértelmezett érték `manage`, amely azt jelzi, hogy a `connection` rendelkezik a **kezelés** engedéllyel. Ha olyan kapcsolódási karakterláncot használ, amely nem rendelkezik a **kezelés** engedéllyel, állítsa a `accessRights` "Listen" (figyelés) értékre. Ellenkező esetben előfordulhat, hogy a functions futtatókörnyezete nem próbálkozik a kezelési jogosultságokat igénylő műveletekkel. A Azure Functions 2. x vagy újabb verziójában ez a tulajdonság nem érhető el, mert a Service Bus SDK legújabb verziója nem támogatja a kezelési műveleteket.|
|**isSessionsEnabled**|**IsSessionsEnabled**|`true`, ha [munkamenet-kompatibilis](../service-bus-messaging/message-sessions.md) várólistához vagy előfizetéshez csatlakozik. `false`, amely az alapértelmezett érték.|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="trigger---usage"></a>Trigger – használat

A C# és C# a szkriptben a következő paramétereket használhatja a várólista vagy a témakör üzenetéhez:

* `string` – ha az üzenet szövege.
* `byte[]` – hasznos a bináris adattároláshoz.
* Egyéni típus – ha az üzenet JSON-t tartalmaz, Azure Functions megpróbálja deszerializálni a JSON-adattípust.
* `BrokeredMessage` – a deszerializált üzenetet adja a [BrokeredMessage. GetBody\<t > ()](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.getbody?view=azure-dotnet#Microsoft_ServiceBus_Messaging_BrokeredMessage_GetBody__1) metódussal.

Ezek a paraméterek a Azure Functions 1. x verzióra vonatkoznak. a 2. x és újabb verziók esetében a [`Message`](https://docs.microsoft.com/dotnet/api/microsoft.azure.servicebus.message) használata `BrokeredMessage`helyett.

A JavaScriptben a `context.bindings.<name from function.json>`használatával férhet hozzá az üzenetsor vagy a témakörhöz. A Service Bus üzenetet karakterlánc vagy JSON-objektumként adja át a függvénynek.

## <a name="trigger---poison-messages"></a>Trigger – Megmérgező üzenetek

A megmérgezett üzenetküldés nem szabályozható és nem konfigurálható Azure Functionsban. Service Bus maga kezeli a megmérgezhető üzeneteket.

## <a name="trigger---peeklock-behavior"></a>Trigger – PeekLock viselkedés

A functions futtatókörnyezet [PeekLock módban](../service-bus-messaging/service-bus-performance-improvements.md#receive-mode)fogad üzenetet. Ha a függvény sikeresen befejeződik, meghívja a `Complete`t az üzenetben, vagy meghívja a `Abandon`, ha a függvény meghibásodik. Ha a függvény a `PeekLock` időtúllépésnél hosszabb ideig fut, akkor a rendszer automatikusan megújítja a zárolást, amíg a függvény fut. 

A `maxAutoRenewDuration` a *Host. JSON*fájlban konfigurálható, amely a [OnMessageOptions. MaxAutoRenewDuration](https://docs.microsoft.com/dotnet/api/microsoft.azure.servicebus.messagehandleroptions.maxautorenewduration?view=azure-dotnet)leképezésére szolgál. Az ehhez a beállításhoz engedélyezett maximális érték 5 perc a Service Bus dokumentációjában, míg a functions időkorlát az alapértelmezett 5 perc és 10 perc között is megnövelhető. Olyan Service Bus függvények esetében, amelyeket nem kíván végrehajtani, mert túllépi a Service Bus megújítási korlátot.

## <a name="trigger---message-metadata"></a>Trigger – üzenet metaadatainak

A Service Bus trigger számos [metaadat-tulajdonságot](./functions-bindings-expressions-patterns.md#trigger-metadata)biztosít. Ezek a tulajdonságok a más kötésekben lévő kötési kifejezések vagy a kódban szereplő paraméterek részeként is használhatók. Ezek a [BrokeredMessage](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.brokeredmessage) osztály tulajdonságai.

|Tulajdonság|Type (Típus)|Leírás|
|--------|----|-----------|
|`DeliveryCount`|`Int32`|A kézbesítések száma.|
|`DeadLetterSource`|`string`|A kézbesítetlen levél forrása.|
|`ExpiresAtUtc`|`DateTime`|A lejárati idő UTC szerint.|
|`EnqueuedTimeUtc`|`DateTime`|A várólistán lévő idő UTC szerint.|
|`MessageId`|`string`|Felhasználó által definiált érték, amelyet a Service Bus az ismétlődő üzenetek azonosítására használhat, ha engedélyezve van.|
|`ContentType`|`string`|A küldő és a fogadó által az alkalmazás-specifikus logikához használt tartalomtípus-azonosító.|
|`ReplyTo`|`string`|A várólista-címnek küldött válasz.|
|`SequenceNumber`|`Int64`|A Service Bus által az üzenethez hozzárendelt egyedi szám.|
|`To`|`string`|A Küldés címe.|
|`Label`|`string`|Az alkalmazásra vonatkozó címke.|
|`CorrelationId`|`string`|A korrelációs azonosító.|

> [!NOTE]
> Jelenleg a munkamenet-kompatibilis várólistákkal és előfizetésekkel használható Service Bus-trigger előzetes verzióban érhető el. Ezt az [adott tételt](https://github.com/Azure/azure-webjobs-sdk/issues/529#issuecomment-491113458) a további frissítésekkel kapcsolatban nyomon követheti. 

Tekintse meg a jelen cikk korábbi részében említett tulajdonságokat használó [példákat](#trigger---example) .

## <a name="output"></a>Kimenet

A várólista vagy a témakör üzeneteinek küldéséhez használjon Azure Service Bus kimeneti kötést.

## <a name="output---example"></a>Kimenet – példa

Tekintse meg a nyelvspecifikus példát:

* [C#](#output---c-example)
* [C#parancsfájl (. CSX)](#output---c-script-example)
* [F#](#output---f-example)
* [Java](#output---java-example)
* [JavaScript](#output---javascript-example)
* [Python](#output---python-example)

### <a name="output---c-example"></a>Kimenet – C# példa

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

### <a name="output---c-script-example"></a>Kimenet – C# parancsfájl – példa

Az alábbi példa egy Service Bus kimeneti kötést mutat be egy *function. JSON* fájlban, valamint egy olyan [ C# parancsfájl-függvényt](functions-reference-csharp.md) , amely a kötést használja. A függvény egy időzítő trigger használatával küld üzenetsor-üzenetet 15 másodpercenként.

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

Az alábbi C# parancsfájl-kód egyetlen üzenetet hoz létre:

```cs
public static void Run(TimerInfo myTimer, ILogger log, out string outputSbQueue)
{
    string message = $"Service Bus queue message created at: {DateTime.Now}";
    log.LogInformation(message); 
    outputSbQueue = message;
}
```

Az alábbi C# szkript-kód több üzenetet hoz létre:

```cs
public static async Task Run(TimerInfo myTimer, ILogger log, IAsyncCollector<string> outputSbQueue)
{
    string message = $"Service Bus queue messages created at: {DateTime.Now}";
    log.LogInformation(message); 
    await outputSbQueue.AddAsync("1 " + message);
    await outputSbQueue.AddAsync("2 " + message);
}
```

### <a name="output---f-example"></a>Kimenet – F# példa

Az alábbi példa egy Service Bus kimeneti kötést mutat be egy *function. JSON* fájlban, valamint egy olyan [ F# parancsfájl-függvényt](functions-reference-fsharp.md) , amely a kötést használja. A függvény egy időzítő trigger használatával küld üzenetsor-üzenetet 15 másodpercenként.

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

Az alábbi F# parancsfájl-kód egyetlen üzenetet hoz létre:

```fsharp
let Run(myTimer: TimerInfo, log: ILogger, outputSbQueue: byref<string>) =
    let message = sprintf "Service Bus queue message created at: %s" (DateTime.Now.ToString())
    log.LogInformation(message)
    outputSbQueue = message
```

### <a name="output---java-example"></a>Kimenet – Java-példa

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

 A [Java functions runtime library](/java/api/overview/azure/functions/runtime)-ben használja a `@ServiceBusQueueOutput` megjegyzése a függvény paramétereit, amelyek értékét egy Service Bus-várólistába kívánja írni.  A paraméter típusának `OutputBinding<T>`nak kell lennie, ahol a T egy POJO natív Java-típusa.

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

### <a name="output---python-example"></a>Kimenet – Python-példa

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

## <a name="output---attributes"></a>Kimenet – attribútumok

Az [ C# osztályok könyvtáraiban](functions-dotnet-class-library.md)használja a [ServiceBusAttribute](https://github.com/Azure/azure-functions-servicebus-extension/blob/master/src/Microsoft.Azure.WebJobs.Extensions.ServiceBus/ServiceBusAttribute.cs).

Az attribútum konstruktora a várólista nevét, illetve a témakört és az előfizetést veszi át. Megadhatja a kapcsolat hozzáférési jogosultságait is. A hozzáférési jogosultságok beállításának kiválasztását a [kimenet – konfiguráció](#output---configuration) szakasz ismerteti. Az alábbi példa bemutatja a függvény visszatérési értékére alkalmazott attribútumot:

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

Teljes példa: [kimenet – C# példa](#output---c-example).

A `ServiceBusAccount` attribútum segítségével megadhatja az osztály, a metódus vagy a paraméter szintjén használandó Service Bus fiókot.  További információ: [trigger-attributes](#trigger---attributes).

## <a name="output---configuration"></a>Kimenet – konfiguráció

Az alábbi táblázat a *function. JSON* fájlban és a `ServiceBus` attribútumban beállított kötési konfigurációs tulajdonságokat ismerteti.

|function. JSON-tulajdonság | Attribútum tulajdonsága |Leírás|
|---------|---------|----------------------|
|**type** | – | "ServiceBus" értékre kell állítani. Ez a tulajdonság automatikusan be van állítva, amikor létrehozza az triggert a Azure Portalban.|
|**direction** | – | "Out" értékre kell állítani. Ez a tulajdonság automatikusan be van állítva, amikor létrehozza az triggert a Azure Portalban. |
|**név** | – | Annak a változónak a neve, amely a függvény kódjában szereplő üzenetsor vagy témakör üzenetét jelöli. A függvény visszatérési értékének hivatkozásához állítsa a "$return" értéket. |
|**queueName**|**QueueName**|A várólista neve.  Csak akkor állítsa be, ha üzenetsor-üzeneteket küld, nem pedig egy témakörhöz.
|**topicName**|**TopicName**|A témakör neve. Csak akkor állítható be, ha nem várólistára küldi a témakör üzeneteit.|
|**kapcsolat**|**Kapcsolat**|A kötéshez használni kívánt Service Bus kapcsolati karakterláncot tartalmazó Alkalmazásbeállítás neve. Ha az Alkalmazásbeállítások neve "AzureWebJobs" előtaggal kezdődik, akkor csak a név hátralévő részét adhatja meg. Ha például a `connection` "MyServiceBus" értékre állítja, a functions futtatókörnyezet egy "AzureWebJobsMyServiceBus" nevű alkalmazás-beállítást keres. Ha üresen hagyja a `connection`, a functions futtatókörnyezet az alapértelmezett Service Bus a "AzureWebJobsServiceBus" nevű alkalmazás-beállítási karakterláncot használja.<br><br>A kapcsolódási karakterlánc beszerzéséhez kövesse a [felügyeleti hitelesítő adatok beolvasása](../service-bus-messaging/service-bus-quickstart-portal.md#get-the-connection-string)című cikkben ismertetett lépéseket. A kapcsolódási karakterláncnak egy Service Bus névtérhez kell tartoznia, nem csak egy adott várólistára vagy témakörre.|
|**accessRights**|**Access (Hozzáférés)**|Hozzáférési jogosultságok a kapcsolati karakterlánchoz. Az elérhető értékek `manage` és `listen`. Az alapértelmezett érték `manage`, amely azt jelzi, hogy a `connection` rendelkezik a **kezelés** engedéllyel. Ha olyan kapcsolódási karakterláncot használ, amely nem rendelkezik a **kezelés** engedéllyel, állítsa a `accessRights` "Listen" (figyelés) értékre. Ellenkező esetben előfordulhat, hogy a functions futtatókörnyezete nem próbálkozik a kezelési jogosultságokat igénylő műveletekkel. A Azure Functions 2. x vagy újabb verziójában ez a tulajdonság nem érhető el, mert a Service Bus SDK legújabb verziója nem támogatja a kezelési műveleteket.|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="output---usage"></a>Kimenet – használat

Azure Functions 1. x esetén a futtatókörnyezet létrehozza a várólistát, ha az nem létezik, és beállította a `accessRights` a `manage`. A functions 2. x vagy újabb verziójában a várólistának vagy a témakörnek már léteznie kell; Ha olyan üzenetsor vagy témakört ad meg, amely nem létezik, a függvény sikertelen lesz. 

A C# és C# a szkriptben a következő paramétereket használhatja a kimeneti kötéshez:

* `out T paramName` - `T` bármely JSON-szerializálható típus lehet. Ha a paraméter értéke null, ha a függvény kilép, a függvény Null objektummal hozza létre az üzenetet.
* `out string` – ha a paraméter értéke null, ha a függvény kilép, a functions nem hoz létre üzenetet.
* `out byte[]` – ha a paraméter értéke null, ha a függvény kilép, a functions nem hoz létre üzenetet.
* `out BrokeredMessage` – ha a paraméter értéke null, ha a függvény kilép, a függvények nem hoznak létre üzenetet (az 1. x függvények esetében).
* `out Message` – ha a paraméter értéke null, ha a függvény kilép, a functions nem hoz létre üzenetet (a 2. x és újabb függvények esetében).
* `ICollector<T>` vagy `IAsyncCollector<T>` – több üzenet létrehozásához. A `Add` metódus meghívásakor létrejön egy üzenet.

A C# függvények használatakor:

* Az aszinkron függvények visszatérési értéknek vagy `IAsyncCollector`nak kell lennie `out` paraméter helyett.

* A munkamenet-azonosító eléréséhez kötést [`Message`](https://docs.microsoft.com/dotnet/api/microsoft.azure.servicebus.message) típushoz kell kötni, és a `sessionId` tulajdonságot kell használnia.

A JavaScriptben a `context.bindings.<name from function.json>`használatával férhet hozzá az üzenetsor vagy a témakörhöz. Hozzárendelhet egy karakterláncot, egy bájt tömböt vagy egy, a JSON-ba szerializált JavaScript-objektumot `context.binding.<name>`.

Ha nemC# nyelven szeretne üzenetet küldeni egy munkamenet-kompatibilis várólistához, használja a [Azure Service Bus SDK](https://docs.microsoft.com/azure/service-bus-messaging) -t a beépített kimeneti kötés helyett.

## <a name="exceptions-and-return-codes"></a>Kivételek és visszatérési kódok

| Kötés | Leírások |
|---|---|
| Szolgáltatásbusz | [Service Bus hibakódok](https://docs.microsoft.com/azure/service-bus-messaging/service-bus-messaging-exceptions) |
| Szolgáltatásbusz | [Service Bus korlátok](https://docs.microsoft.com/azure/service-bus-messaging/service-bus-quotas) |

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
|maxAutoRenewDuration|00:05:00|Az a maximális időtartam, amelyen belül az üzenet zárolása automatikusan meg lesz újítva.|
|Automatikus kiegészítés|igaz|Azt jelzi, hogy a triggernek azonnal meg kell-e jelölnie az üzenetet (automatikus kiegészítés), vagy várjon, amíg a függvény sikeresen kilép a hívás befejezéséhez.|
|maxConcurrentCalls|16|Az üzenet-szivattyú által kezdeményezett visszahívás egyidejű hívásának maximális száma. Alapértelmezés szerint a függvények futtatókörnyezete egyszerre több üzenetet dolgoz fel. Ha a futtatókörnyezetet csak egyetlen üzenetsor vagy témakörbeli üzenet feldolgozására szeretné irányítani egyszerre, állítsa a `maxConcurrentCalls` 1 értékre. |

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [További információ az Azure functions-eseményindítók és-kötésekről](functions-triggers-bindings.md)
