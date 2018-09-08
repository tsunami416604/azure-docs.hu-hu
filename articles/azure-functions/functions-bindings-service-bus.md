---
title: Az Azure Szolgáltatásbusz-kötések az Azure Functions szolgáltatáshoz
description: Megtudhatja, hogyan használhatja az Azure Service Bus-eseményindítók és kötések az Azure Functions szolgáltatásban.
services: functions
documentationcenter: na
author: ggailey777
manager: jeconnoc
keywords: az Azure functions, függvények, eseményfeldolgozás, dinamikus számítás, kiszolgáló nélküli architektúra
ms.assetid: daedacf0-6546-4355-a65c-50873e74f66b
ms.service: azure-functions
ms.devlang: multiple
ms.topic: reference
ms.date: 04/01/2017
ms.author: glenga
ms.openlocfilehash: 2dd8e93aa8da59019af6e04b29da786e78b54a81
ms.sourcegitcommit: af60bd400e18fd4cf4965f90094e2411a22e1e77
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/07/2018
ms.locfileid: "44092495"
---
# <a name="azure-service-bus-bindings-for-azure-functions"></a>Az Azure Szolgáltatásbusz-kötések az Azure Functions szolgáltatáshoz

Ez a cikk ismerteti az Azure Service Bus-kötések az Azure Functions használata. Az Azure Functions támogatja a-trigger, és a kimeneti kötések Service Bus-üzenetsorok és üzenettémák esetében.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="packages---functions-1x"></a>Csomagok – 1.x függvények

A Service Bus kötéseit szerepelnek a [Microsoft.Azure.WebJobs.ServiceBus](http://www.nuget.org/packages/Microsoft.Azure.WebJobs.ServiceBus) NuGet-csomag verziója 2.x. A csomag forráskódja a [azure-webjobs-sdk](https://github.com/Azure/azure-webjobs-sdk/blob/v2.x/src/Microsoft.Azure.WebJobs.ServiceBus/) GitHub-adattárban.

[!INCLUDE [functions-package](../../includes/functions-package.md)]

## <a name="packages---functions-2x"></a>Csomagok – 2.x függvények

A Service Bus kötéseit szerepelnek a [Microsoft.Azure.WebJobs.ServiceBus](http://www.nuget.org/packages/Microsoft.Azure.WebJobs.ServiceBus) NuGet-csomag verziója 3.x. A csomag forráskódja a [azure-webjobs-sdk](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.ServiceBus/) GitHub-adattárban.

[!INCLUDE [functions-package-v2](../../includes/functions-package-v2.md)]

## <a name="trigger"></a>Eseményindító

A Service Bus-trigger használatával üzeneteket egy Service Bus-üzenetsor vagy témakör válaszol. 

## <a name="trigger---example"></a>Az eseményindító – példa

Tekintse meg az adott nyelvű példa:

* [C#](#trigger---c-example)
* [C# script (.csx)](#trigger---c-script-example)
* [F#](#trigger---f-example)
* [JavaScript](#trigger---javascript-example)
* [Java](#trigger---java-example)

### <a name="trigger---c-example"></a>Eseményindító - C#-példa

A következő példa bemutatja egy [C#-függvény](functions-dotnet-class-library.md) , amely beolvassa [üzenet metaadatok](#trigger---message-metadata) és a egy Service Bus üzenetsor üzenetet:

```cs
[FunctionName("ServiceBusQueueTriggerCSharp")]                    
public static void Run(
    [ServiceBusTrigger("myqueue", AccessRights.Manage, Connection = "ServiceBusConnection")] 
    string myQueueItem,
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

Ebben a példában az Azure Functions-verzióhoz tartozó 1.x; a 2.x [a hozzáférési jogok paraméter nincs megadva](#trigger---configuration).
 
### <a name="trigger---c-script-example"></a>Eseményindító - C#-szkript példa

Az alábbi példa bemutatja egy kötelező a Service Bus-trigger egy *function.json* fájl és a egy [C#-szkriptfüggvény](functions-reference-csharp.md) , amely a kötés használja. A függvény beolvassa [üzenet metaadatok](#trigger---message-metadata) és a egy Service Bus üzenetsor üzenetet.

Itt van a kötési adatait a *function.json* fájlt:

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

### <a name="trigger---f-example"></a>Eseményindító - F #-példa

Az alábbi példa bemutatja egy kötelező a Service Bus-trigger egy *function.json* fájl és a egy [F #-függvény](functions-reference-fsharp.md) , amely a kötés használja. A függvény egy Service Bus üzenetsor-üzenetet naplózza. 

Itt van a kötési adatait a *function.json* fájlt:

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

Az F #-szkriptkódot itt látható:

```fsharp
let Run(myQueueItem: string, log: TraceWriter) =
    log.Info(sprintf "F# ServiceBus queue trigger function processed message: %s" myQueueItem)
```

### <a name="trigger---javascript-example"></a>Eseményindító - JavaScript-példa

Az alábbi példa bemutatja egy kötelező a Service Bus-trigger egy *function.json* fájl és a egy [JavaScript-függvény](functions-reference-node.md) , amely a kötés használja. A függvény beolvassa [üzenet metaadatok](#trigger---message-metadata) és a egy Service Bus üzenetsor üzenetet. 

Itt van a kötési adatait a *function.json* fájlt:

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

### <a name="trigger---java-example"></a>Eseményindító - Java-példában

Az alábbi példa bemutatja egy kötelező a Service Bus-trigger egy *function.json* fájl és a egy [Java függvény](functions-reference-java.md) , amely a kötés használja. Egy üzenetet a Service Bus-üzenetsorban helyez által aktivált függvény, és a függvény naplózza az üzenetsorban található üzenet.

Itt van a kötési adatait a *function.json* fájlt:

```json
{
"bindings": [
    {
    "queueName": "myqueuename",
    "connection": "MyServiceBusConnection",
    "name": "msg",
    "type": "ServiceBusQueueTrigger",
    "direction": "in"
    }
],
"disabled": false
}
```

A Java-kód itt látható:

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

## <a name="trigger---attributes"></a>Eseményindító - attribútumok

A [C#-osztálykódtárakat](functions-dotnet-class-library.md), használja a következő attribútumok egy Service Bus-trigger konfigurálásához:

* [ServiceBusTriggerAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.ServiceBus/ServiceBusTriggerAttribute.cs)

  Az attribútum konstruktorának paramétereként az üzenetsor vagy témakör és előfizetés nevét. Az Azure Functions verzió 1.x, azt is megadhatja a kapcsolat hozzáférési jogosultságokat. Ha nem ad meg hozzáférési jogosultságokat, az alapértelmezett érték `Manage`. További információkért lásd: a [eseményindító - konfiguráció](#trigger---configuration) szakaszban.

  A következő példa bemutatja, az attribútum egy karakterlánc-paraméterrel használja:

  ```csharp
  [FunctionName("ServiceBusQueueTriggerCSharp")]                    
  public static void Run(
      [ServiceBusTrigger("myqueue")] string myQueueItem, TraceWriter log)
  {
      ...
  }
  ```

  Beállíthatja a `Connection` tulajdonság megadása a Service Bus-fiókot szeretne használni, az alábbi példában látható módon:

  ```csharp
  [FunctionName("ServiceBusQueueTriggerCSharp")]                    
  public static void Run(
      [ServiceBusTrigger("myqueue", Connection = "ServiceBusConnection")] 
      string myQueueItem, TraceWriter log)
  {
      ...
  }
  ```

  Egy teljes példa: [eseményindító – C#-példa](#trigger---c-example).

* [ServiceBusAccountAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.ServiceBus/ServiceBusAccountAttribute.cs)

  Adja meg a Service Bus használni kívánt fiók egy másik megoldást kínál. A konstruktorban vesz igénybe egy Service Bus kapcsolati karakterláncot tartalmazó alkalmazásbeállítás neve. Az attribútum a paramétert, a metódus vagy az osztály szintjén alkalmazhatók. Az alábbi példa bemutatja az osztály és metódust:

  ```csharp
  [ServiceBusAccount("ClassLevelServiceBusAppSetting")]
  public static class AzureFunctions
  {
      [ServiceBusAccount("MethodLevelServiceBusAppSetting")]
      [FunctionName("ServiceBusQueueTriggerCSharp")]
      public static void Run(
          [ServiceBusTrigger("myqueue", AccessRights.Manage)] 
          string myQueueItem, TraceWriter log)
  {
      ...
  }
  ```

A Service Bus használni kívánt fiók határozza meg a következő sorrendben:

* A `ServiceBusTrigger` attribútum `Connection` tulajdonság.
* A `ServiceBusAccount` a paramétert, amelyek azonos, alkalmazott attribútum a `ServiceBusTrigger` attribútum.
* A `ServiceBusAccount` attribútum a függvény a alkalmazni.
* A `ServiceBusAccount` attribútum az osztály a alkalmazni.
* Az "AzureWebJobsServiceBus" alkalmazásbeállítást.

## <a name="trigger---configuration"></a>Eseményindító - konfiguráció

A következő táblázat ismerteti a megadott kötés konfigurációs tulajdonságaiban a *function.json* fájlt, és a `ServiceBusTrigger` attribútum.

|Function.JSON tulajdonság | Attribútum tulajdonsága |Leírás|
|---------|---------|----------------------|
|**type** | n/a | Állítsa "serviceBusTrigger". Ez a tulajdonság beállítása automatikusan történik, ha az eseményindítót fog létrehozni az Azure Portalon.|
|**direction** | n/a | Meg kell "a". Ez a tulajdonság beállítása automatikusan történik, ha az eseményindítót fog létrehozni az Azure Portalon. |
|**name** | n/a | A függvénykódot az üzenetsor vagy témakör üzenet képviselő változó neve. "$Return" hivatkozik a függvény visszaadott értékének beállításával. | 
|**queueName**|**queueName**|Figyelni kívánt üzenetsor neve.  Csak akkor, ha egy üzenetsort, nem a témakör a figyelés beállítása.
|**topicName**|**topicName**|Figyelendő üzenettéma nevére. Csak akkor, ha a figyelést egy üzenetsort, nem egy témakör beállítása.|
|**subscriptionName**|**subscriptionName**|Neve a figyelni kívánt előfizetést. Csak akkor, ha a figyelést egy üzenetsort, nem egy témakör beállítása.|
|**kapcsolat**|**kapcsolat**|A Service Bus kapcsolati karakterlánc használata ehhez a kötéshez tartalmazó alkalmazásbeállítás neve. Azon alkalmazásbeállítás neve "AzureWebJobs" kezdődik, ha csak a név része is megadhat. Például, ha a beállított `connection` "MyServiceBus", hogy a Functions futtatókörnyezete úgy tűnik, a beállítás, amely alkalmazás neve "AzureWebJobsMyServiceBus." Ha meghagyja a `connection` üres, a Functions futtatókörnyezete alapértelmezett a Service Bus kapcsolati karakterláncát használja a "AzureWebJobsServiceBus" nevű Alkalmazásbeállítás.<br><br>Szerezzen be egy kapcsolati karakterláncot, kövesse a lépéseket, látható [a felügyeleti hitelesítő adatok beszerzése](../service-bus-messaging/service-bus-dotnet-get-started-with-queues.md#obtain-the-management-credentials). A kapcsolati karakterláncot a Service Bus-névtér, nem kizárólagosan az adott üzenetsor vagy témakör kell lennie. |
|**accessRights**|**Access (Hozzáférés)**|A kapcsolati karakterlánc hozzáférési jogosultságokat. Elérhető értékek a következők `manage` és `listen`. Az alapértelmezett érték `manage`, amely azt jelzi, hogy a `connection` rendelkezik a **kezelés** engedéllyel. Használhat, ha egy kapcsolati karakterláncot, amely nem rendelkezik a **kezelés** , engedélyének `accessRights` "figyelésére". Ellenkező esetben a Functions runtime meghiúsulhat igénylő műveletek végrehajtását kezelésére. Az Azure Functions-verzió 2.x, ez a tulajdonság nem érhető el, mert a Storage SDK legújabb verziója nem támogatja a műveletek kezelésére.|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="trigger---usage"></a>Eseményindító - használat

Az üzenetsor vagy témakör üzenet a C# és a C#-szkript, használhatja a következő paraméter típusa:

* `string` – Ha az üzenet szövege.
* `byte[]` -A bináris adatok hasznos.
* Egy egyéni típusa – Ha az üzenet tartalmazza JSON-t, az Azure Functions próbálja meg a JSON-adatok deszerializálása.
* `BrokeredMessage` -Lehetővé teszi a deszerializált üzenetet a [BrokeredMessage.GetBody<T>()](https://docs.microsoft.com/en-us/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.getbody?view=azure-dotnet#Microsoft_ServiceBus_Messaging_BrokeredMessage_GetBody__1) metódust.

Az Azure Functions verziójára vonatkoznak ezek a paraméterek 1.x; a 2.x használja [ `Message` ](https://docs.microsoft.com/dotnet/api/microsoft.azure.servicebus.message) helyett `BrokeredMessage`.

A JavaScript, elérése az üzenetsor vagy témakör üzenet `context.bindings.<name from function.json>`. A Service Bus-üzenet, a függvény egy karakterlánc vagy a JSON-objektum lett átadva.

## <a name="trigger---poison-messages"></a>Eseményindító - ártalmas üzenetek

Ártalmas üzenetek kezelése nem szabályozza és nem konfigurált az Azure Functions szolgáltatásban. A Service Bus kezeli az ártalmas üzenetek magát.

## <a name="trigger---peeklock-behavior"></a>Eseményindító - PeekLock viselkedés

A Functions futtatókörnyezete egy üzenet fogadja [PeekLock mód](../service-bus-messaging/service-bus-performance-improvements.md#receive-mode). Meghívja `Complete` az üzenetet, ha a függvény sikeresen befejeződik, vagy hívások `Abandon` , ha a parancs nem működik. Ha a függvény futásakor hosszabb, mint a `PeekLock` időkorlát, a zárolás automatikus megújítása mindaddig, amíg a függvény fut-e. 

Függvények 1.x lehetővé teszi, hogy konfigurálása `autoRenewTimeout` a *host.json*, amely leképezhető [OnMessageOptions.AutoRenewTimeout](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.onmessageoptions.autorenewtimeout?view=azure-dotnet#Microsoft_ServiceBus_Messaging_OnMessageOptions_AutoRenewTimeout). Ez a beállítás számára engedélyezett maximális érték megfelelően a Service Bus dokumentációja, 5 perc, míg 10 perc az alapértelmezett 5 perces időbeli függvények korlát növeléséhez. A Service Bus-funkciók nem szeretné, amely majd, mivel a Service Bus-megújítási korlát túllépését eredményezné.

## <a name="trigger---message-metadata"></a>Eseményindító - üzenet metaadatok

A Service Bus-trigger biztosít több [metaadat-tulajdonságot](functions-triggers-bindings.md#binding-expressions---trigger-metadata). Ezek a tulajdonságok a kötési kifejezésekben való használata más kötések részeként vagy a kód paramétereiben használható. Ezek a tulajdonságai a [BrokeredMessage](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.brokeredmessage) osztály.

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
|`Properties`|`IDictionary<String,Object>`|Az alkalmazás adott üzenet tulajdonságai.|

Lásd: [kódpéldák](#trigger---example) , használja ezeket a tulajdonságokat a cikk korábbi részében.

## <a name="trigger---hostjson-properties"></a>Eseményindító - host.json tulajdonságai

A [host.json](functions-host-json.md#servicebus) fájl tartalmazza a Service Bus-eseményindító viselkedését vezérlő beállításokat.

[!INCLUDE [functions-host-json-event-hubs](../../includes/functions-host-json-service-bus.md)]

## <a name="output"></a>Kimenet

Azure Service Bus kimeneti kötés használatával üzenetsor vagy témakör üzeneteket küldeni.

## <a name="output---example"></a>Kimenete – példa

Tekintse meg az adott nyelvű példa:

* [C#](#output---c-example)
* [C# script (.csx)](#output---c-script-example)
* [F#](#output---f-example)
* [JavaScript](#output---javascript-example)
* [Java](#output--java-example)

### <a name="output---c-example"></a>Kimenet – C#-példa

A következő példa bemutatja egy [C#-függvény](functions-dotnet-class-library.md) , amely egy Service Bus üzenetsor-üzenetet küld:

```cs
[FunctionName("ServiceBusOutput")]
[return: ServiceBus("myqueue", Connection = "ServiceBusConnection")]
public static string ServiceBusOutput([HttpTrigger] dynamic input, TraceWriter log)
{
    log.Info($"C# function processed: {input.Text}");
    return input.Text;
}
```

### <a name="output---c-script-example"></a>Kimenet – C#-szkript példa

Az alábbi példa bemutatja egy kötelező a Service Bus-kimenet egy *function.json* fájl és a egy [C#-szkriptfüggvény](functions-reference-csharp.md) , amely a kötés használja. A függvény egy időzítő eseményindító 15 másodpercenként üzenetsori üzenetek küldéséhez használja.

Itt van a kötési adatait a *function.json* fájlt:

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
public static void Run(TimerInfo myTimer, TraceWriter log, out string outputSbQueue)
{
    string message = $"Service Bus queue message created at: {DateTime.Now}";
    log.Info(message); 
    outputSbQueue = message;
}
```

Az alábbiakban C#-szkriptkódot, amely több üzenetet hoz létre:

```cs
public static void Run(TimerInfo myTimer, TraceWriter log, ICollector<string> outputSbQueue)
{
    string message = $"Service Bus queue messages created at: {DateTime.Now}";
    log.Info(message); 
    outputSbQueue.Add("1 " + message);
    outputSbQueue.Add("2 " + message);
}
```

### <a name="output---f-example"></a>Kimenet – F #-példa

Az alábbi példa bemutatja egy kötelező a Service Bus-kimenet egy *function.json* fájl és a egy [F #-szkriptfüggvény](functions-reference-fsharp.md) , amely a kötés használja. A függvény egy időzítő eseményindító 15 másodpercenként üzenetsori üzenetek küldéséhez használja.

Itt van a kötési adatait a *function.json* fájlt:

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

F #-szkriptkódot, amely létrehoz egy üzenet a következő:

```fsharp
let Run(myTimer: TimerInfo, log: TraceWriter, outputSbQueue: byref<string>) =
    let message = sprintf "Service Bus queue message created at: %s" (DateTime.Now.ToString())
    log.Info(message)
    outputSbQueue = message
```

### <a name="output---javascript-example"></a>Kimenet – JavaScript-példa

Az alábbi példa bemutatja egy kötelező a Service Bus-kimenet egy *function.json* fájl és a egy [JavaScript-függvény](functions-reference-node.md) , amely a kötés használja. A függvény egy időzítő eseményindító 15 másodpercenként üzenetsori üzenetek küldéséhez használja.

Itt van a kötési adatait a *function.json* fájlt:

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


### <a name="output---java-example"></a>Kimenet – Java-példában

Az alábbi példa bemutatja egy Java-függvény, amely egy üzenetet küld egy Service Bus-üzenetsorba `myqueue` egy HTTP-kérelem elindításakor.

```java
@FunctionName("httpToServiceBusQueue")
@ServiceBusQueueOutput(name = "message", queueName = "myqueue", connection = "AzureServiceBusConnection")
public String pushToQueue(
  @HttpTrigger(name = "request", methods = {HttpMethod.POST}, authLevel = AuthorizationLevel.ANONYMOUS)
  final String message,
  @HttpOutput(name = "response") final OutputBinding<T>; result ) {
      result.setValue(message + " has been sent.");
      return message;
 }
 ```

 Az a [Java-függvények futásidejű kódtár](/java/api/overview/azure/functions/runtime), használja a `@QueueOutput` függvény paraméterei, amelynek az értéke egy Service Bus-üzenetsorba tartalmazná a jegyzet.  A paraméter típusa legyen `OutputBinding<T>`, ahol a T natív Java bármilyen egy pojo-vá.

## <a name="output---attributes"></a>Kimenet – attribútumok

A [C#-osztálykódtárakat](functions-dotnet-class-library.md), használja a [ServiceBusAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.ServiceBus/ServiceBusAttribute.cs).

Az attribútum konstruktorának paramétereként az üzenetsor vagy témakör és előfizetés nevét. A kapcsolat hozzáférési jogosultságokat is megadhatja. A hozzáférési jogosultságokat beállítás kiválasztása ismertetése a [kimenete – konfigurációs](#output---configuration) szakaszban. A következő példa bemutatja, az attribútum a függvény visszatérési értéke a alkalmazni:

```csharp
[FunctionName("ServiceBusOutput")]
[return: ServiceBus("myqueue")]
public static string Run([HttpTrigger] dynamic input, TraceWriter log)
{
    ...
}
```

Beállíthatja a `Connection` tulajdonság megadása a Service Bus-fiókot szeretne használni, az alábbi példában látható módon:

```csharp
[FunctionName("ServiceBusOutput")]
[return: ServiceBus("myqueue", Connection = "ServiceBusConnection")]
public static string Run([HttpTrigger] dynamic input, TraceWriter log)
{
    ...
}
```

Egy teljes példa: [kimenet – C#-példa](#output---c-example).

Használhatja a `ServiceBusAccount` attribútum adja meg a Service Bus-fiókhoz használandó osztály, módszer vagy paraméter szintjén.  További információkért lásd: [eseményindító - attribútumok](#trigger---attributes).

## <a name="output---configuration"></a>Kimenete – konfiguráció

A következő táblázat ismerteti a megadott kötés konfigurációs tulajdonságaiban a *function.json* fájlt, és a `ServiceBus` attribútum.

|Function.JSON tulajdonság | Attribútum tulajdonsága |Leírás|
|---------|---------|----------------------|
|**type** | n/a | "A serviceBus" kell állítani. Ez a tulajdonság beállítása automatikusan történik, ha az eseményindítót fog létrehozni az Azure Portalon.|
|**direction** | n/a | Állítsa "out". Ez a tulajdonság beállítása automatikusan történik, ha az eseményindítót fog létrehozni az Azure Portalon. |
|**name** | n/a | Az üzenetsor vagy témakör, a függvény kódját képviselő változó neve. "$Return" hivatkozik a függvény visszaadott értékének beállításával. | 
|**queueName**|**queueName**|Az üzenetsor neve.  Állítsa be, csak akkor, ha üzenetküldésre üzenetsor, témakör esetében nem.
|**topicName**|**topicName**|Figyelendő üzenettéma nevére. Csak akkor, ha üzenetküldésre témakör, nem a várólista állítsa be.|
|**kapcsolat**|**kapcsolat**|A Service Bus kapcsolati karakterlánc használata ehhez a kötéshez tartalmazó alkalmazásbeállítás neve. Azon alkalmazásbeállítás neve "AzureWebJobs" kezdődik, ha csak a név része is megadhat. Például, ha a beállított `connection` "MyServiceBus", hogy a Functions futtatókörnyezete úgy tűnik, a beállítás, amely alkalmazás neve "AzureWebJobsMyServiceBus." Ha meghagyja a `connection` üres, a Functions futtatókörnyezete alapértelmezett a Service Bus kapcsolati karakterláncát használja a "AzureWebJobsServiceBus" nevű Alkalmazásbeállítás.<br><br>Szerezzen be egy kapcsolati karakterláncot, kövesse a lépéseket, látható [a felügyeleti hitelesítő adatok beszerzése](../service-bus-messaging/service-bus-dotnet-get-started-with-queues.md#obtain-the-management-credentials). A kapcsolati karakterláncot a Service Bus-névtér, nem kizárólagosan az adott üzenetsor vagy témakör kell lennie.|
|**accessRights**|**Access (Hozzáférés)**|A kapcsolati karakterlánc hozzáférési jogosultságokat. Elérhető értékek a következők `manage` és `listen`. Az alapértelmezett érték `manage`, amely azt jelzi, hogy a `connection` rendelkezik a **kezelés** engedéllyel. Használhat, ha egy kapcsolati karakterláncot, amely nem rendelkezik a **kezelés** , engedélyének `accessRights` "figyelésére". Ellenkező esetben a Functions runtime meghiúsulhat igénylő műveletek végrehajtását kezelésére. Az Azure Functions-verzió 2.x, ez a tulajdonság nem érhető el, mert a Storage SDK legújabb verziója nem támogatja a műveletek kezelésére.|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="output---usage"></a>Kimenet – használat

Az Azure Functions 1.x, a futtatókörnyezet hoz létre az üzenetsor még nem létezik, és beállította `accessRights` való `manage`. A Functions verzió 2.x, az üzenetsor vagy témakör már léteznie kell; Ha megad egy üzenetsorba vagy témakörbe, amely nem létezik, a függvény sikertelen lesz. 

A kimeneti kötési C# és C#-szkript, használhatja a következő paraméter típusa:

* `out T paramName` - `T` bármely JSON-szerializálható típusa lehet. Ha a paraméter értéke null értékű, ha a függvény kilép, Funkciók hoz létre az üzenet Objekt s hodnotou null.
* `out string` – Ha a paraméter értéke null értékű, ha a függvény kilép, függvények nem hoz létre egy üzenetet.
* `out byte[]` – Ha a paraméter értéke null értékű, ha a függvény kilép, függvények nem hoz létre egy üzenetet.
* `out BrokeredMessage` – Ha a paraméter értéke null értékű, ha a függvény kilép, függvények nem hoz létre egy üzenetet.
* `ICollector<T>` vagy `IAsyncCollector<T>` – több üzenetet létrehozásához. Egy üzenet jön létre, amikor hívja a `Add` metódust.

Az aszinkron funkciók, a visszaadott értékének használata vagy `IAsyncCollector` helyett egy `out` paraméter.

Az Azure Functions verziójára vonatkoznak ezek a paraméterek 1.x; a 2.x használja [ `Message` ](https://docs.microsoft.com/dotnet/api/microsoft.azure.servicebus.message) helyett `BrokeredMessage`.

A JavaScript, az üzenetsor vagy témakör segítségével hozzáférni `context.bindings.<name from function.json>`. Rendelhet egy karakterlánc, egy bájttömböt vagy egy Javascript-objektumot (deszerializálni JSON formátumba) `context.binding.<name>`.

## <a name="exceptions-and-return-codes"></a>Kivételek és a visszatérési kódok

| Kötés | Leírások |
|---|---|
| Service Bus | [Service Bus-hibakódok](https://docs.microsoft.com/azure/service-bus-messaging/service-bus-messaging-exceptions) |
| Service Bus | [Service Bus-korlátok](https://docs.microsoft.com/azure/service-bus-messaging/service-bus-quotas) |

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Tudjon meg többet az Azure functions eseményindítók és kötések](functions-triggers-bindings.md)
