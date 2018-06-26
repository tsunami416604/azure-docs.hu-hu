---
title: Az Azure Functions az Azure Service Bus kötései
description: Azure Service Bus-eseményindítók és kötések az Azure Functions használatának megismerése.
services: functions
documentationcenter: na
author: tdykstra
manager: cfowler
editor: ''
tags: ''
keywords: Azure functions, Funkciók, Eseményfeldolgozási, dinamikus számítási kiszolgáló nélküli architektúrája
ms.assetid: daedacf0-6546-4355-a65c-50873e74f66b
ms.service: functions
ms.devlang: multiple
ms.topic: reference
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 04/01/2017
ms.author: tdykstra
ms.openlocfilehash: 0e9e7dcab208d1ffd8410a02a7c1cd713d11b277
ms.sourcegitcommit: 6eb14a2c7ffb1afa4d502f5162f7283d4aceb9e2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/25/2018
ms.locfileid: "36753489"
---
# <a name="azure-service-bus-bindings-for-azure-functions"></a>Az Azure Functions az Azure Service Bus kötései

Ez a cikk ismerteti az Azure Functions kötések Azure Service Bus használata. Az Azure Functions támogatja aktiválhatja és Service Bus-üzenetsorok és témakörök kimeneti.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="packages---functions-1x"></a>Csomagok - 1.x működik

A Service Bus-kötések szerepelnek a [Microsoft.Azure.WebJobs.ServiceBus](http://www.nuget.org/packages/Microsoft.Azure.WebJobs.ServiceBus) NuGet-csomag verziója 2.x. A csomag forráskódja van a [azure-webjobs-sdk](https://github.com/Azure/azure-webjobs-sdk/blob/v2.x/src/Microsoft.Azure.WebJobs.ServiceBus/) GitHub-tárházban.

[!INCLUDE [functions-package](../../includes/functions-package.md)]

## <a name="packages---functions-2x"></a>Csomagok - 2.x működik

A Service Bus-kötések szerepelnek a [Microsoft.Azure.WebJobs.ServiceBus](http://www.nuget.org/packages/Microsoft.Azure.WebJobs.ServiceBus) NuGet-csomag verziója 3.x. A csomag forráskódja van a [azure-webjobs-sdk](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.ServiceBus/) GitHub-tárházban.

[!INCLUDE [functions-package-v2](../../includes/functions-package-v2.md)]

## <a name="trigger"></a>Eseményindító

A Service Bus eseményindító használatával a Service Bus-üzenetsor vagy témakör üzenetek válaszolni. 

## <a name="trigger---example"></a>Eseményindító – példa

Tekintse meg a nyelvspecifikus példát:

* [C#](#trigger---c-example)
* [C# script (.csx)](#trigger---c-script-example)
* [F#](#trigger---f-example)
* [JavaScript](#trigger---javascript-example)

### <a name="trigger---c-example"></a>Eseményindító - C# – példa

A következő példa azt mutatja be egy [C# függvény](functions-dotnet-class-library.md) , amely beolvassa [üzenet metaadatok](#trigger---message-metadata) és naplózza a Service Bus-üzenetsor:

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

Ebben a példában az Azure Functions verziójához 1.x; a 2.x [a hozzáférési jogok paraméter nincs megadva](#trigger---configuration).
 
### <a name="trigger---c-script-example"></a>Eseményindító - C# parancsfájl – példa

A következő példa bemutatja egy Service Bus eseményindító kötelező egy *function.json* fájlt és egy [C# parancsfájl függvény](functions-reference-csharp.md) , amely a kötés használja. A függvény beolvassa [üzenet metaadatok](#trigger---message-metadata) és egy Service Bus-üzenetsor naplózza.

Itt az kötés adatai a *function.json* fájlt:

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

A C# parancsfájl kód itt látható:

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

### <a name="trigger---f-example"></a>Eseményindító - F # – példa

A következő példa bemutatja egy Service Bus eseményindító kötelező egy *function.json* fájlt és egy [F # függvény](functions-reference-fsharp.md) , amely a kötés használja. A függvény egy Service Bus-üzenetsor naplózza. 

Itt az kötés adatai a *function.json* fájlt:

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

A F # parancsfájl kód itt látható:

```fsharp
let Run(myQueueItem: string, log: TraceWriter) =
    log.Info(sprintf "F# ServiceBus queue trigger function processed message: %s" myQueueItem)
```

### <a name="trigger---javascript-example"></a>Eseményindító - JavaScript – példa

A következő példa bemutatja egy Service Bus eseményindító kötelező egy *function.json* fájlt és egy [JavaScript függvény](functions-reference-node.md) , amely a kötés használja. A függvény beolvassa [üzenet metaadatok](#trigger---message-metadata) és egy Service Bus-üzenetsor naplózza. 

Itt az kötés adatai a *function.json* fájlt:

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

A JavaScript parancsfájl kód itt látható:

```javascript
module.exports = function(context, myQueueItem) {
    context.log('Node.js ServiceBus queue trigger function processed message', myQueueItem);
    context.log('EnqueuedTimeUtc =', context.bindingData.enqueuedTimeUtc);
    context.log('DeliveryCount =', context.bindingData.deliveryCount);
    context.log('MessageId =', context.bindingData.messageId);
    context.done();
};
```

## <a name="trigger---attributes"></a>Eseményindító - attribútumok

A [C# osztálykönyvtárakhoz](functions-dotnet-class-library.md), ha egy Service Bus eseményindítót a következő attribútumokat használhatja:

* [ServiceBusTriggerAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.ServiceBus/ServiceBusTriggerAttribute.cs)

  Az attribútum konstruktora a várólista vagy a témakör és előfizetés neve vesz igénybe. Az Azure Functions verziójában 1.x, azt is megadhatja a kapcsolat hozzáférési jogosultsága. Ha nem ad meg hozzáférési jogosultságokat, az alapértelmezett érték `Manage`. További információkért lásd: a [eseményindító - konfigurációs](#trigger---configuration) szakasz.

  Íme egy példa, amely megjeleníti a karakterlánc-paraméterrel használt attribútum:

  ```csharp
  [FunctionName("ServiceBusQueueTriggerCSharp")]                    
  public static void Run(
      [ServiceBusTrigger("myqueue")] string myQueueItem, TraceWriter log)
  {
      ...
  }
  ```

  Beállíthatja a `Connection` tulajdonság fiókot szeretne megadni a Service Bus használatához a következő példában látható módon:

  ```csharp
  [FunctionName("ServiceBusQueueTriggerCSharp")]                    
  public static void Run(
      [ServiceBusTrigger("myqueue", Connection = "ServiceBusConnection")] 
      string myQueueItem, TraceWriter log)
  {
      ...
  }
  ```

  Tekintse meg a teljes például [eseményindító - C# példa](#trigger---c-example).

* [ServiceBusAccountAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.ServiceBus/ServiceBusAccountAttribute.cs)

  Adja meg a Service Bus-fiókot használni más megoldást kínál. A konstruktornak, amely egy Service Bus kapcsolati karakterláncot tartalmaz alkalmazásbeállítás neve vesz igénybe. Az attribútum a paraméter, módszer vagy osztály szintjén is alkalmazható. A következő példa bemutatja az osztály és módszer:

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

A Service Bus használandó fiókot határozza meg a következő sorrendben:

* A `ServiceBusTrigger` attribútum `Connection` tulajdonság.
* A `ServiceBusAccount` attribútuma ugyanezt a paramétert, mint a `ServiceBusTrigger` attribútum.
* A `ServiceBusAccount` függvény attribútuma.
* A `ServiceBusAccount` osztály attribútuma.
* Az "AzureWebJobsServiceBus" alkalmazásbeállítás.

## <a name="trigger---configuration"></a>Eseményindító - konfiguráció

Az alábbi táblázat ismerteti a beállított kötés konfigurációs tulajdonságok a *function.json* fájl és a `ServiceBusTrigger` attribútum.

|Function.JSON tulajdonság | Attribútum tulajdonsága |Leírás|
|---------|---------|----------------------|
|**type** | n/a | "ServiceBusTrigger" értékre kell állítani. Ez a tulajdonság rendszer automatikusan beállítja az eseményindítót hoz létre az Azure portálon.|
|**direction** | n/a | "A" értékre kell állítani. Ez a tulajdonság rendszer automatikusan beállítja az eseményindítót hoz létre az Azure portálon. |
|**name** | n/a | Az üzenetsor vagy témakör üzenet funkciókódot jelölő neve. "$Return" értékre való hivatkozáshoz függvény visszatérési értéke. | 
|**queueName**|**QueueName**|A várólista figyelése neve.  Csak akkor, ha a várólista nem a témakör a figyelés beállítása.
|**topicName**|**TopicName**|A témakör a figyelheti neve. Állítsa be, csak akkor, ha a témakör a várólista nem figyelni.|
|**subscriptionName**|**SubscriptionName**|Az előfizetés figyelése neve. Állítsa be, csak akkor, ha a témakör a várólista nem figyelni.|
|**kapcsolat**|**Kapcsolat**|A Service Bus kapcsolati karakterlánc az ehhez a kötéshez használandó tartalmazó alkalmazásbeállítás neve. Ha az alkalmazás neve "AzureWebJobs" kezdődik, megadhatja a név csak a maradékot. Ha például `connection` "MyServiceBus", hogy a Functions futtatókörnyezete keresi, hogy az alkalmazás neve "AzureWebJobsMyServiceBus." Ha nem adja meg `connection` üres, a Functions futtatókörnyezete használja az alapértelmezett Service Bus kapcsolati karakterlánc a "AzureWebJobsServiceBus" nevű Alkalmazásbeállítás.<br><br>Szerezzen be egy kapcsolati karakterláncot, kövesse a lépéseket látható [a felügyeleti hitelesítő adatok beszerzése](../service-bus-messaging/service-bus-dotnet-get-started-with-queues.md#obtain-the-management-credentials). A kapcsolati karakterláncnak kell lennie, a Service Bus-névtér, nem kizárólagosan az adott üzenetsor vagy témakör. |
|**accessRights**|**Access (Hozzáférés)**|Hozzáférési jogosultsága a kapcsolódási karakterláncban. Lehetséges értékek a következők `manage` és `listen`. Az alapértelmezett érték `manage`, amely azt jelzi, hogy a `connection` rendelkezik a **kezelése** engedéllyel. Ha használja a kapcsolati karakterláncot, amely nem rendelkezik a **kezelése** engedély, `accessRights` "figyelésére". Ellenkező esetben a futásidejű meghiúsulhat igénylő műveletek végrehajtását megkísérlő funkciók jogosultságaik kezelését. Az Azure Functions verziójában 2.x, ez a tulajdonság nem érhető el, mert a Storage szolgáltatás SDK legújabb verziója nem támogatja a működés felügyelete.|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="trigger---usage"></a>Eseményindító - használat

A C# és C# a parancsfájlt a következő paraméter esetében is használhatja, az üzenetsor vagy témakör üzenet:

* `string` -Ha az üzenet szövegét.
* `byte[]` -Bináris adatoknál lehet hasznos.
* Egyéni típusa: Ha az üzenet tartalmaz JSON, az Azure Functions próbálja meg a JSON-adatok deszerializálása.
* `BrokeredMessage` -Lehetővé teszi a deszerializált üzenetet a [BrokeredMessage.GetBody<T>()](https://docs.microsoft.com/en-us/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.getbody?view=azure-dotnet#Microsoft_ServiceBus_Messaging_BrokeredMessage_GetBody__1) metódust.

Ezek a paraméterek vannak az Azure Functions verziójához 1.x; a 2.x, használjon [ `Message` ](https://docs.microsoft.com/dotnet/api/microsoft.azure.servicebus.message) helyett `BrokeredMessage`.

A JavaScript, hozzáférhetnek az üzenetsor vagy témakör üzenet használatával `context.bindings.<name from function.json>`. A Service Bus message átad egy karakterlánc- vagy JSON-objektum a funkciót a.

## <a name="trigger---poison-messages"></a>Eseményindító - elhalt üzenetek

Elhalt üzenetek kezelésének nem lehet konfigurálni az Azure Functions vagy alatt áll. A Service Bus kezeli az elhalt üzenetek magát.

## <a name="trigger---peeklock-behavior"></a>Eseményindító - PeekLock viselkedése

A Functions futtatókörnyezete az üzenetet kap [PeekLock mód](../service-bus-messaging/service-bus-performance-improvements.md#receive-mode). Meghívja `Complete` az üzenetet, ha a függvény futtatása sikeresen befejeződött, vagy a hívások `Abandon` Ha a parancs nem működik. Ha a függvény futásakor hosszabb, mint a `PeekLock` időtúllépés, a zárolás automatikusan megújítják mindaddig, amíg a függvény fut-e. 

Funkciók 1.x konfigurálását teszi `autoRenewTimeout` a *host.json*, amely leképezhető [OnMessageOptions.AutoRenewTimeout](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.onmessageoptions.autorenewtimeout?view=azure-dotnet#Microsoft_ServiceBus_Messaging_OnMessageOptions_AutoRenewTimeout). A megengedett maximális ehhez a beállításhoz: 5 perc, a Service Bus dokumentációjának megfelelően, mivel növelheti a funkciók időkorlátot az alapértelmezett, 5 perces 10 perc. A Service Bus-funkciók nem szeretné, majd, mert Ön meghaladná a Service Bus megújítási korlátot.

## <a name="trigger---message-metadata"></a>Eseményindító - üzenet metaadatok

A Service Bus eseményindító biztosít több [metaadat-tulajdonságainak](functions-triggers-bindings.md#binding-expressions---trigger-metadata). Ezeket a tulajdonságokat meg más kötésekben kötési kifejezés részeként vagy a kód paramétereiben használható. Ezek a tulajdonságait a [BrokeredMessage](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.brokeredmessage) osztály.

|Tulajdonság|Típus|Leírás|
|--------|----|-----------|
|`DeliveryCount`|`Int32`|A kézbesítések száma.|
|`DeadLetterSource`|`string`|Kézbesítetlen levelek forrása.|
|`ExpiresAtUtc`|`DateTime`|A lejárati idő (UTC).|
|`EnqueuedTimeUtc`|`DateTime`|A várólistában levő ideje UTC Formátumban.|
|`MessageId`|`string`|A felhasználó által definiált értéket, a Service Bus segítségével azonosíthatja a duplikált üzenetek, ha engedélyezve van.|
|`ContentType`|`string`|A küldő és fogadó alkalmazás-specifikus logika a tartalomtípus-azonosító.|
|`ReplyTo`|`string`|A várósor címe választ.|
|`SequenceNumber`|`Int64`|A Service Bus által egy üzenet rendelt egyedi számot.|
|`To`|`string`|A küldési címre.|
|`Label`|`string`|Az alkalmazás adott címkéje.|
|`CorrelationId`|`string`|A korrelációs azonosítója.|
|`Properties`|`IDictionary<String,Object>`|Az adott üzenet tulajdonságaiban.|

Lásd: [kódpéldák](#trigger---example) ezeket a tulajdonságokat, amelyek használják az ebben a cikkben.

## <a name="trigger---hostjson-properties"></a>Eseményindító - host.json tulajdonságai

A [host.json](functions-host-json.md#servicebus) fájl Service Bus eseményindító viselkedését vezérlő beállításokat tartalmaz.

[!INCLUDE [functions-host-json-event-hubs](../../includes/functions-host-json-service-bus.md)]

## <a name="output"></a>Kimenet

Azure Service Bus kimeneti kötés segítségével üzenetsor vagy témakör üzeneteket küldeni.

## <a name="output---example"></a>Kimeneti – példa

Tekintse meg a nyelvspecifikus példát:

* [C#](#output---c-example)
* [C# script (.csx)](#output---c-script-example)
* [F#](#output---f-example)
* [JavaScript](#output---javascript-example)

### <a name="output---c-example"></a>Kimeneti - C# – példa

Az alábbi példa mutatja egy [C# függvény](functions-dotnet-class-library.md) , küld egy Service Bus-üzenetsor:

```cs
[FunctionName("ServiceBusOutput")]
[return: ServiceBus("myqueue", Connection = "ServiceBusConnection")]
public static string ServiceBusOutput([HttpTrigger] dynamic input, TraceWriter log)
{
    log.Info($"C# function processed: {input.Text}");
    return input.Text;
}
```

### <a name="output---c-script-example"></a>Kimeneti - C# parancsfájl – példa

A következő példa bemutatja egy Service Bus kimeneti kötelező egy *function.json* fájlt és egy [C# parancsfájl függvény](functions-reference-csharp.md) , amely a kötés használja. A függvény egy időzítő indítófeltételt használó 15 másodpercenként várólista üzenet küldése.

Itt az kötés adatai a *function.json* fájlt:

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

C# parancsfájlkód, amely létrehoz egy üzenetet a következő:

```cs
public static void Run(TimerInfo myTimer, TraceWriter log, out string outputSbQueue)
{
    string message = $"Service Bus queue message created at: {DateTime.Now}";
    log.Info(message); 
    outputSbQueue = message;
}
```

Az alábbiakban C# parancsfájl létrehozó kód mellől több üzenetet:

```cs
public static void Run(TimerInfo myTimer, TraceWriter log, ICollector<string> outputSbQueue)
{
    string message = $"Service Bus queue messages created at: {DateTime.Now}";
    log.Info(message); 
    outputSbQueue.Add("1 " + message);
    outputSbQueue.Add("2 " + message);
}
```

### <a name="output---f-example"></a>Kimeneti - F # – példa

A következő példa bemutatja egy Service Bus kimeneti kötelező egy *function.json* fájlt és egy [F # parancsfájl függvény](functions-reference-fsharp.md) , amely a kötés használja. A függvény egy időzítő indítófeltételt használó 15 másodpercenként várólista üzenet küldése.

Itt az kötés adatai a *function.json* fájlt:

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

Itt az F # parancsfájlkód, amely létrehoz egy üzenetet:

```fsharp
let Run(myTimer: TimerInfo, log: TraceWriter, outputSbQueue: byref<string>) =
    let message = sprintf "Service Bus queue message created at: %s" (DateTime.Now.ToString())
    log.Info(message)
    outputSbQueue = message
```

### <a name="output---javascript-example"></a>Kimeneti - JavaScript – példa

A következő példa bemutatja egy Service Bus kimeneti kötelező egy *function.json* fájlt és egy [JavaScript függvény](functions-reference-node.md) , amely a kötés használja. A függvény egy időzítő indítófeltételt használó 15 másodpercenként várólista üzenet küldése.

Itt az kötés adatai a *function.json* fájlt:

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

Létrehoz egy üzenetet JavaScript parancsfájlkódot itt található:

```javascript
module.exports = function (context, myTimer) {
    var message = 'Service Bus queue message created at ' + timeStamp;
    context.log(message);   
    context.bindings.outputSbQueue = message;
    context.done();
};
```

JavaScript parancsfájl létrehozó kód mellől több üzenet a következő:

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

## <a name="output---attributes"></a>Kimeneti - attribútumok

A [C# osztálykönyvtárakhoz](functions-dotnet-class-library.md), használja a [ServiceBusAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.ServiceBus/ServiceBusAttribute.cs).

Az attribútum konstruktora a várólista vagy a témakör és előfizetés neve vesz igénybe. A kapcsolat hozzáférési jogosultságokat is megadható. A hozzáférési jogok beállítás kiválasztása esetén, tekintse meg a a [kimeneti - konfigurációs](#output---configuration) szakasz. Íme egy példa bemutatja, a függvény visszatérési értéke a attribútuma:

```csharp
[FunctionName("ServiceBusOutput")]
[return: ServiceBus("myqueue")]
public static string Run([HttpTrigger] dynamic input, TraceWriter log)
{
    ...
}
```

Beállíthatja a `Connection` tulajdonság fiókot szeretne megadni a Service Bus használatához a következő példában látható módon:

```csharp
[FunctionName("ServiceBusOutput")]
[return: ServiceBus("myqueue", Connection = "ServiceBusConnection")]
public static string Run([HttpTrigger] dynamic input, TraceWriter log)
{
    ...
}
```

Tekintse meg a teljes például [kimeneti - C# példa](#output---c-example).

Használhatja a `ServiceBusAccount` attribútum segítségével adhatja meg a Service Bus osztály, módszer vagy paraméter szinten használandó fiókot.  További információkért lásd: [eseményindító - attribútumok](#trigger---attributes).

## <a name="output---configuration"></a>Kimeneti - konfiguráció

Az alábbi táblázat ismerteti a beállított kötés konfigurációs tulajdonságok a *function.json* fájl és a `ServiceBus` attribútum.

|Function.JSON tulajdonság | Attribútum tulajdonsága |Leírás|
|---------|---------|----------------------|
|**type** | n/a | "A Szolgáltatásbusz" értékre kell állítani. Ez a tulajdonság rendszer automatikusan beállítja az eseményindítót hoz létre az Azure portálon.|
|**direction** | n/a | "Ki" értékre kell állítani. Ez a tulajdonság rendszer automatikusan beállítja az eseményindítót hoz létre az Azure portálon. |
|**name** | n/a | A várólista vagy funkciókódot témakörében jelölő neve. "$Return" értékre való hivatkozáshoz függvény visszatérési értéke. | 
|**queueName**|**QueueName**|A várólista nevét.  Állítsa be, csak akkor, ha üzenetküldésre várólista, a témakör a nem.
|**topicName**|**TopicName**|A témakör a figyelheti neve. Csak akkor, ha üzenetküldésre témakör, a várólista nem beállítása.|
|**kapcsolat**|**Kapcsolat**|A Service Bus kapcsolati karakterlánc az ehhez a kötéshez használandó tartalmazó alkalmazásbeállítás neve. Ha az alkalmazás neve "AzureWebJobs" kezdődik, megadhatja a név csak a maradékot. Ha például `connection` "MyServiceBus", hogy a Functions futtatókörnyezete keresi, hogy az alkalmazás neve "AzureWebJobsMyServiceBus." Ha nem adja meg `connection` üres, a Functions futtatókörnyezete használja az alapértelmezett Service Bus kapcsolati karakterlánc a "AzureWebJobsServiceBus" nevű Alkalmazásbeállítás.<br><br>Szerezzen be egy kapcsolati karakterláncot, kövesse a lépéseket látható [a felügyeleti hitelesítő adatok beszerzése](../service-bus-messaging/service-bus-dotnet-get-started-with-queues.md#obtain-the-management-credentials). A kapcsolati karakterláncnak kell lennie, a Service Bus-névtér, nem kizárólagosan az adott üzenetsor vagy témakör.|
|**accessRights**|**Access (Hozzáférés)**|Hozzáférési jogosultsága a kapcsolódási karakterláncban. Lehetséges értékek a következők `manage` és `listen`. Az alapértelmezett érték `manage`, amely azt jelzi, hogy a `connection` rendelkezik a **kezelése** engedéllyel. Ha használja a kapcsolati karakterláncot, amely nem rendelkezik a **kezelése** engedély, `accessRights` "figyelésére". Ellenkező esetben a futásidejű meghiúsulhat igénylő műveletek végrehajtását megkísérlő funkciók jogosultságaik kezelését. Az Azure Functions verziójában 2.x, ez a tulajdonság nem érhető el, mert a Storage szolgáltatás SDK legújabb verziója nem támogatja a működés felügyelete.|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="output---usage"></a>Kimeneti - használat

Az Azure Functions 1.x, a futtatókörnyezet a várólista létrehozása, ha még nem létezik, és meg van `accessRights` való `manage`. Funkciók verziójában 2.x, az üzenetsor vagy témakör már léteznie kell; Ha megadja az üzenetsor vagy témakör, amely nem létezik, a szolgáltatás sikertelen lesz. 

A C# és C# a parancsfájlt a következő paraméter típusok használhatók a kimeneti kötés:

* `out T paramName` - `T` bármely JSON-szerializálható típusú lehet. Ha a paraméter értéke nem null, ha kilép, a függvény, funkciók az üzenet null objektumot hoz létre.
* `out string` -Ha a paraméter értéke nem null, amikor a függvény kilép, a funkciók nem hoz létre egy üzenetet.
* `out byte[]` -Ha a paraméter értéke nem null, amikor a függvény kilép, a funkciók nem hoz létre egy üzenetet.
* `out BrokeredMessage` -Ha a paraméter értéke nem null, amikor a függvény kilép, a funkciók nem hoz létre egy üzenetet.
* `ICollector<T>` vagy `IAsyncCollector<T>` – több üzenetet létrehozásához. Egy üzenet jön létre, ha meghívja a `Add` metódust.

Aszinkron funkciók, használja a visszatérési értéket vagy `IAsyncCollector` ahelyett, hogy egy `out` paraméter.

Ezek a paraméterek vannak az Azure Functions verziójához 1.x; a 2.x, használjon [ `Message` ](https://docs.microsoft.com/dotnet/api/microsoft.azure.servicebus.message) helyett `BrokeredMessage`.

JavaScript, nyissa meg az üzenetsor vagy témakör segítségével `context.bindings.<name from function.json>`. Rendelhet egy karakterlánc, egy bájttömböt vagy egy Javascript-objektum (deszerializálni a JSON-ba) `context.binding.<name>`.

## <a name="exceptions-and-return-codes"></a>Kivételeket és a visszatérési kódok

| Kötelező | Leírások |
|---|---|
| Service Bus | [Service Bus hibakódok](https://docs.microsoft.com/azure/service-bus-messaging/service-bus-messaging-exceptions) |
| Service Bus | [Service Bus korlátok](https://docs.microsoft.com/azure/service-bus-messaging/service-bus-quotas) |

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [További tudnivalók az Azure functions eseményindítók és kötések](functions-triggers-bindings.md)
