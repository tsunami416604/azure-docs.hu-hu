---
title: Azure Functions-kötések Azure Service Bus
description: Ismerje meg, hogyan futtathat Azure-függvényeket Azure Service Bus üzenetek létrehozásakor.
author: craigshoemaker
ms.assetid: daedacf0-6546-4355-a65c-50873e74f66b
ms.topic: reference
ms.date: 02/19/2020
ms.author: cshoe
ms.openlocfilehash: 1ead7fcd9d474369e3a62e372a971d88d26f4e9c
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "78273562"
---
# <a name="azure-service-bus-trigger-for-azure-functions"></a>Azure Functions Azure Service Bus trigger

A Service Bus triggerrel válaszolhat Service Bus üzenetsor vagy témakör üzeneteire.

További információ a telepítésről és a konfigurációról: [Áttekintés](functions-bindings-service-bus-output.md).

## <a name="example"></a>Példa

# <a name="c"></a>[C #](#tab/csharp)

Az alábbi példa egy [C#-függvényt](functions-dotnet-class-library.md) mutat be, amely beolvassa az [üzenet metaadatait](#message-metadata) , és naplóz egy Service Bus üzenetsor-üzenetet:

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

# <a name="c-script"></a>[C#-parancsfájl](#tab/csharp-script)

Az alábbi példa egy Service Bus trigger kötést mutat be egy *function. JSON* fájlban, valamint egy [C# parancsfájl-függvényt](functions-reference-csharp.md) , amely a kötést használja. A függvény beolvassa az [üzenet metaadatait](#message-metadata) , és naplóz egy Service Bus üzenetsor-üzenetet.

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

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Az alábbi példa egy Service Bus trigger kötést mutat be egy *function. JSON* fájlban, valamint egy [JavaScript-függvényt](functions-reference-node.md) , amely a kötést használja. A függvény beolvassa az [üzenet metaadatait](#message-metadata) , és naplóz egy Service Bus üzenetsor-üzenetet. 

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

# <a name="python"></a>[Python](#tab/python)

Az alábbi példa bemutatja, hogyan olvashat egy Service Bus üzenetsor-üzenetet egy trigger használatával.

Az Service Bus kötés a *function. JSON* fájlban van definiálva, ahol a `serviceBusTrigger` *Type* értéke.

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

Az `func.ServiceBusMessage` * _ \_init_\_.* a (z). a. a. a. a. a (z) kód deklarál egy paramétert, amely lehetővé teszi, hogy elolvassa az üzenetsor

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

# <a name="java"></a>[Java](#tab/java)

A következő Java-függvény a `@ServiceBusQueueTrigger` [Java functions Runtime könyvtárának](/java/api/overview/azure/functions/runtime) megjegyzését használja egy Service Bus üzenetsor-trigger konfigurációjának leírásához. A függvény megragadja a várólistára helyezett üzenetet, és hozzáadja azt a naplókhoz.

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

---

## <a name="attributes-and-annotations"></a>Attribútumok és jegyzetek

# <a name="c"></a>[C #](#tab/csharp)

A [C# osztályok könyvtáraiban](functions-dotnet-class-library.md)használja a következő attribútumokat egy Service Bus trigger konfigurálásához:

* [ServiceBusTriggerAttribute](https://github.com/Azure/azure-functions-servicebus-extension/blob/master/src/Microsoft.Azure.WebJobs.Extensions.ServiceBus/ServiceBusTriggerAttribute.cs)

  Az attribútum konstruktora a várólista nevét, illetve a témakört és az előfizetést veszi át. A Azure Functions 1. x verziójában megadhatja a kapcsolat hozzáférési jogosultságait is. Ha nem ad meg hozzáférési jogosultságokat, az alapértelmezett `Manage`érték. További információ: [trigger-Configuration](#configuration) szakasz.

  Az alábbi példa egy sztring paraméterrel használt attribútumot mutatja be:

  ```csharp
  [FunctionName("ServiceBusQueueTriggerCSharp")]                    
  public static void Run(
      [ServiceBusTrigger("myqueue")] string myQueueItem, ILogger log)
  {
      ...
  }
  ```

  A `Connection` tulajdonság beállításával megadhatja a használni kívánt Service Bus-kapcsolódási karakterláncot tartalmazó Alkalmazásbeállítások nevét, ahogy az a következő példában is látható:

  ```csharp
  [FunctionName("ServiceBusQueueTriggerCSharp")]                    
  public static void Run(
      [ServiceBusTrigger("myqueue", Connection = "ServiceBusConnection")] 
      string myQueueItem, ILogger log)
  {
      ...
  }
  ```

  A teljes példa: [trigger-example](#example).

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

* Az `ServiceBusTrigger` attribútum `Connection` tulajdonsága.
* A `ServiceBusAccount` `ServiceBusTrigger` attribútummal megegyező paraméterre alkalmazott attribútum.
* A `ServiceBusAccount` függvényre alkalmazott attribútum.
* Az `ServiceBusAccount` osztályra alkalmazott attribútum.
* A "AzureWebJobsServiceBus" alkalmazás beállításai.

# <a name="c-script"></a>[C#-parancsfájl](#tab/csharp-script)

A C# parancsfájl nem támogatja az attribútumokat.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

A JavaScript nem támogatja az attribútumokat.

# <a name="python"></a>[Python](#tab/python)

A Python nem támogatja az attribútumokat.

# <a name="java"></a>[Java](#tab/java)

A `ServiceBusQueueTrigger` jegyzet lehetővé teszi, hogy egy Service Bus üzenetsor létrehozásakor futó függvényt hozzon létre. Az elérhető konfigurációs lehetőségek közé tartozik a várólista neve és a kapcsolatok karakterláncának neve.

A `ServiceBusTopicTrigger` jegyzet lehetővé teszi, hogy egy témakört és egy előfizetést jelöljön ki a függvényt kiváltó adat megcélzásához.

További részletekért tekintse meg az trigger [példáját](#example) .

---

## <a name="configuration"></a>Configuration

Az alábbi táblázat a *function. JSON* fájlban és az `ServiceBusTrigger` attribútumban beállított kötési konfigurációs tulajdonságokat ismerteti.

|function. JSON-tulajdonság | Attribútum tulajdonsága |Leírás|
|---------|---------|----------------------|
|**típusa** | n/a | "ServiceBusTrigger" értékre kell állítani. Ez a tulajdonság automatikusan be van állítva, amikor létrehozza az triggert a Azure Portalban.|
|**direction** | n/a | "In" értékre kell állítani. Ez a tulajdonság automatikusan be van állítva, amikor létrehozza az triggert a Azure Portalban. |
|**név** | n/a | Annak a változónak a neve, amely a függvény kódjában szereplő üzenetsor vagy témakör üzenetét jelöli. |
|**queueName**|**QueueName**|A figyelni kívánt várólista neve.  Csak akkor állítható be, ha egy üzenetsor figyelése nem a témakörhöz szükséges.
|**topicName**|**TopicName**|A figyelni kívánt témakör neve. Csak akkor állítsa be, ha egy témakör figyelése nem várólistára van állítva.|
|**subscriptionName**|**SubscriptionName**|A figyelni kívánt előfizetés neve. Csak akkor állítsa be, ha egy témakör figyelése nem várólistára van állítva.|
|**kapcsolat**|**Kapcsolat**|A kötéshez használni kívánt Service Bus kapcsolati karakterláncot tartalmazó Alkalmazásbeállítás neve. Ha az Alkalmazásbeállítások neve "AzureWebJobs" előtaggal kezdődik, akkor csak a név hátralévő részét adhatja meg. Ha például a "MyServiceBus" `connection` értékre van állítva, a functions futtatókörnyezet egy "AzureWebJobsMyServiceBus" nevű alkalmazás-beállítást keres. Ha üresen `connection` hagyja, a functions Runtime az alapértelmezett Service Bus a "AzureWebJobsServiceBus" nevű alkalmazás-beállításban található.<br><br>A kapcsolódási karakterlánc beszerzéséhez kövesse a [felügyeleti hitelesítő adatok beolvasása](../service-bus-messaging/service-bus-quickstart-portal.md#get-the-connection-string)című cikkben ismertetett lépéseket. A kapcsolódási karakterláncnak egy Service Bus névtérhez kell tartoznia, nem csak egy adott várólistára vagy témakörre. |
|**accessRights**|**Hozzáférés**|Hozzáférési jogosultságok a kapcsolati karakterlánchoz. Az `manage` elérhető értékek: `listen`és. Az alapértelmezett érték `manage`a, amely azt jelzi `connection` , hogy a rendelkezik a **kezelés** engedéllyel. Ha olyan kapcsolódási karakterláncot használ, amely nem rendelkezik a **kezelés** engedéllyel, `accessRights` állítsa a "Listen" (figyelés) lehetőséget. Ellenkező esetben előfordulhat, hogy a functions futtatókörnyezete nem próbálkozik a kezelési jogosultságokat igénylő műveletekkel. A Azure Functions 2. x vagy újabb verziójában ez a tulajdonság nem érhető el, mert a Service Bus SDK legújabb verziója nem támogatja a kezelési műveleteket.|
|**isSessionsEnabled**|**IsSessionsEnabled**|`true`Ha [munkamenet-kompatibilis](../service-bus-messaging/message-sessions.md) várólistához vagy előfizetéshez csatlakozik. `false`Ellenkező esetben ez az alapértelmezett érték.|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="usage"></a>Használat

# <a name="c"></a>[C #](#tab/csharp)

A következő típusparaméter-típusok érhetők el a várólista vagy a témakör üzeneteihez:

* `string`– Ha az üzenet szövege.
* `byte[]`– Hasznos a bináris adattároláshoz.
* Egyéni típus – ha az üzenet JSON-t tartalmaz, Azure Functions megpróbálja deszerializálni a JSON-adattípust.
* `BrokeredMessage`-Megadja a deszerializált üzenetet a [BrokeredMessage. GetBody\<T> ()](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.getbody?view=azure-dotnet#Microsoft_ServiceBus_Messaging_BrokeredMessage_GetBody__1) metódussal.

Ezek a paraméterek a Azure Functions 1. x verzióra vonatkoznak. 2. x és újabb verziók esetén használja [`Message`](https://docs.microsoft.com/dotnet/api/microsoft.azure.servicebus.message) a helyett `BrokeredMessage`a következőt:.

# <a name="c-script"></a>[C#-parancsfájl](#tab/csharp-script)

A következő típusparaméter-típusok érhetők el a várólista vagy a témakör üzeneteihez:

* `string`– Ha az üzenet szövege.
* `byte[]`– Hasznos a bináris adattároláshoz.
* Egyéni típus – ha az üzenet JSON-t tartalmaz, Azure Functions megpróbálja deszerializálni a JSON-adattípust.
* `BrokeredMessage`-Megadja a deszerializált üzenetet a [BrokeredMessage. GetBody\<T> ()](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.getbody?view=azure-dotnet#Microsoft_ServiceBus_Messaging_BrokeredMessage_GetBody__1) metódussal.

Ezek a paraméterek az 1. x Azure Functions-verzióra vonatkoznak. 2. x és újabb verziók esetén használja [`Message`](https://docs.microsoft.com/dotnet/api/microsoft.azure.servicebus.message) a helyett `BrokeredMessage`a következőt:.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

A várólista vagy a témakör üzenetének elérése `context.bindings.<name from function.json>`a használatával. A Service Bus üzenetet karakterlánc vagy JSON-objektumként adja át a függvénynek.

# <a name="python"></a>[Python](#tab/python)

Az üzenetsor-üzenet a következőként beírt paraméterrel érhető el a `func.ServiceBusMessage`függvény számára:. A Service Bus üzenetet karakterlánc vagy JSON-objektumként adja át a függvénynek.

# <a name="java"></a>[Java](#tab/java)

A bejövő Service Bus üzenet a vagy `ServiceBusQueueMessage` `ServiceBusTopicMessage` a paraméter használatával érhető el.

[A részletekért tekintse meg a példát](#example).

---

## <a name="poison-messages"></a>Üzenetek megmérgezve

A megmérgezett üzenetküldés nem szabályozható és nem konfigurálható Azure Functionsban. Service Bus maga kezeli a megmérgezhető üzeneteket.

## <a name="peeklock-behavior"></a>PeekLock viselkedés

A functions futtatókörnyezet [PeekLock módban](../service-bus-messaging/service-bus-performance-improvements.md#receive-mode)fogad üzenetet. `Complete` Ha a függvény sikeresen befejeződik, meghívja az üzenetet, vagy meghívja `Abandon` , ha a függvény meghibásodik. Ha a függvény hosszabb ideig fut az `PeekLock` időtúllépésnél, a rendszer automatikusan megújítja a zárolást, amíg a függvény fut. 

A `maxAutoRenewDuration` a *Host. JSON*fájlban konfigurálható, amely a [OnMessageOptions. MaxAutoRenewDuration](https://docs.microsoft.com/dotnet/api/microsoft.azure.servicebus.messagehandleroptions.maxautorenewduration?view=azure-dotnet)leképezésére szolgál. Az ehhez a beállításhoz engedélyezett maximális érték 5 perc a Service Bus dokumentációjában, míg a functions időkorlát az alapértelmezett 5 perc és 10 perc között is megnövelhető. Olyan Service Bus függvények esetében, amelyeket nem kíván végrehajtani, mert túllépi a Service Bus megújítási korlátot.

## <a name="message-metadata"></a>Üzenet metaadatai

A Service Bus trigger számos [metaadat-tulajdonságot](./functions-bindings-expressions-patterns.md#trigger-metadata)biztosít. Ezek a tulajdonságok a más kötésekben lévő kötési kifejezések vagy a kódban szereplő paraméterek részeként is használhatók. Ezek a tulajdonságok a [BrokeredMessage](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.brokeredmessage) osztály tagjai.

|Tulajdonság|Típus|Leírás|
|--------|----|-----------|
|`DeliveryCount`|`Int32`|A kézbesítések száma.|
|`DeadLetterSource`|`string`|A kézbesítetlen levél forrása.|
|`ExpiresAtUtc`|`DateTime`|A lejárati idő UTC szerint.|
|`EnqueuedTimeUtc`|`DateTime`|A várólistán lévő idő UTC szerint.|
|`MessageId`|`string`|Felhasználó által definiált érték, amelyet a Service Bus az ismétlődő üzenetek azonosítására használhat, ha engedélyezve van.|
|`ContentType`|`string`|A küldő és a fogadó által az alkalmazásspecifikus logikához használt tartalomtípus-azonosító.|
|`ReplyTo`|`string`|A várólista-címnek küldött válasz.|
|`SequenceNumber`|`Int64`|A Service Bus által az üzenethez hozzárendelt egyedi szám.|
|`To`|`string`|A Küldés címe.|
|`Label`|`string`|Az alkalmazásra vonatkozó címke.|
|`CorrelationId`|`string`|A korrelációs azonosító.|

Tekintse meg a jelen cikk korábbi részében említett tulajdonságokat használó [példákat](#example) .

## <a name="next-steps"></a>További lépések

- [Azure Service Bus üzenetek küldése Azure Functionsból (kimeneti kötés)](./functions-bindings-service-bus-output.md)
