---
title: Az Azure Szolgáltatásbusz-kötések az Azure Functions szolgáltatáshoz
description: Ismerje meg, hogyan futtathat Azure-függvényeket Azure Service Bus üzenetek létrehozásakor.
author: craigshoemaker
ms.assetid: daedacf0-6546-4355-a65c-50873e74f66b
ms.topic: reference
ms.date: 02/19/2020
ms.author: cshoe
ms.openlocfilehash: 1ead7fcd9d474369e3a62e372a971d88d26f4e9c
ms.sourcegitcommit: d45fd299815ee29ce65fd68fd5e0ecf774546a47
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/04/2020
ms.locfileid: "78273562"
---
# <a name="azure-service-bus-trigger-for-azure-functions"></a>Azure Functions Azure Service Bus trigger

A Service Bus-trigger használatával üzeneteket egy Service Bus-üzenetsor vagy témakör válaszol.

További információ a telepítésről és a konfigurációról: [Áttekintés](functions-bindings-service-bus-output.md).

## <a name="example"></a>Példa

# <a name="c"></a>[C#](#tab/csharp)

Az alábbi példa egy olyan [ C# függvényt](functions-dotnet-class-library.md) mutat be, amely beolvassa az [üzenet metaadatait](#message-metadata) , és naplóz egy Service Bus üzenetsor-üzenetet:

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

# <a name="c-script"></a>[C#Parancsfájl](#tab/csharp-script)

Az alábbi példa egy Service Bus trigger kötést mutat be egy *function. JSON* fájlban, valamint egy olyan [ C# parancsfájl-függvényt](functions-reference-csharp.md) , amely a kötést használja. A függvény beolvassa az [üzenet metaadatait](#message-metadata) , és naplóz egy Service Bus üzenetsor-üzenetet.

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

# <a name="python"></a>[Python](#tab/python)

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

A (z `func.ServiceBusMessage` *) _\_init_\_. a. a. a.* a (z)

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

---

## <a name="attributes-and-annotations"></a>Attribútumok és jegyzetek

# <a name="c"></a>[C#](#tab/csharp)

Az [ C# osztályok könyvtáraiban](functions-dotnet-class-library.md)használja a következő attribútumokat egy Service Bus trigger konfigurálásához:

* [ServiceBusTriggerAttribute](https://github.com/Azure/azure-functions-servicebus-extension/blob/master/src/Microsoft.Azure.WebJobs.Extensions.ServiceBus/ServiceBusTriggerAttribute.cs)

  Az attribútum konstruktorának paramétereként az üzenetsor vagy témakör és előfizetés nevét. Az Azure Functions verzió 1.x, azt is megadhatja a kapcsolat hozzáférési jogosultságokat. Ha nem ad meg hozzáférési jogosultságokat, az alapértelmezett érték `Manage`. További információ: [trigger-Configuration](#configuration) szakasz.

  A következő példa bemutatja, az attribútum egy karakterlánc-paraméterrel használja:

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

  A teljes példa: [trigger-example](#example).

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

# <a name="c-script"></a>[C#Parancsfájl](#tab/csharp-script)

Az C# attribútumokat a parancsfájl nem támogatja.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

A JavaScript nem támogatja az attribútumokat.

# <a name="python"></a>[Python](#tab/python)

A Python nem támogatja az attribútumokat.

# <a name="java"></a>[Java](#tab/java)

A `ServiceBusQueueTrigger` Megjegyzés lehetővé teszi, hogy olyan függvényt hozzon létre, amely egy Service Bus üzenetsor létrehozásakor fut. Az elérhető konfigurációs lehetőségek közé tartozik a várólista neve és a kapcsolatok karakterláncának neve.

A `ServiceBusTopicTrigger` jegyzet lehetővé teszi egy témakör és előfizetés kijelölését a függvényt kiváltó adat megcélzásához.

További részletekért tekintse meg az trigger [példáját](#example) .

---

## <a name="configuration"></a>Konfiguráció

Az alábbi táblázat a *function. JSON* fájlban és a `ServiceBusTrigger` attribútumban beállított kötési konfigurációs tulajdonságokat ismerteti.

|Function.JSON tulajdonság | Attribútum tulajdonsága |Leírás|
|---------|---------|----------------------|
|**type** | n/a | Állítsa "serviceBusTrigger". Ez a tulajdonság beállítása automatikusan történik, ha az eseményindítót fog létrehozni az Azure Portalon.|
|**direction** | n/a | Meg kell "a". Ez a tulajdonság beállítása automatikusan történik, ha az eseményindítót fog létrehozni az Azure Portalon. |
|**név** | n/a | A függvénykódot az üzenetsor vagy témakör üzenet képviselő változó neve. |
|**queueName**|**QueueName**|Figyelni kívánt üzenetsor neve.  Csak akkor, ha egy üzenetsort, nem a témakör a figyelés beállítása.
|**topicName**|**TopicName**|Figyelendő üzenettéma nevére. Csak akkor, ha a figyelést egy üzenetsort, nem egy témakör beállítása.|
|**subscriptionName**|**SubscriptionName**|Neve a figyelni kívánt előfizetést. Csak akkor, ha a figyelést egy üzenetsort, nem egy témakör beállítása.|
|**kapcsolat**|**Kapcsolat**|A Service Bus kapcsolati karakterlánc használata ehhez a kötéshez tartalmazó alkalmazásbeállítás neve. Azon alkalmazásbeállítás neve "AzureWebJobs" kezdődik, ha csak a név része is megadhat. Ha például a `connection` "MyServiceBus" értékre állítja, a functions futtatókörnyezet egy "AzureWebJobsMyServiceBus" nevű alkalmazás-beállítást keres. Ha üresen hagyja a `connection`, a functions futtatókörnyezet az alapértelmezett Service Bus a "AzureWebJobsServiceBus" nevű alkalmazás-beállítási karakterláncot használja.<br><br>A kapcsolódási karakterlánc beszerzéséhez kövesse a [felügyeleti hitelesítő adatok beolvasása](../service-bus-messaging/service-bus-quickstart-portal.md#get-the-connection-string)című cikkben ismertetett lépéseket. A kapcsolati karakterláncot a Service Bus-névtér, nem kizárólagosan az adott üzenetsor vagy témakör kell lennie. |
|**accessRights**|**Access (Hozzáférés)**|A kapcsolati karakterlánc hozzáférési jogosultságokat. Az elérhető értékek `manage` és `listen`. Az alapértelmezett érték `manage`, amely azt jelzi, hogy a `connection` rendelkezik a **kezelés** engedéllyel. Ha olyan kapcsolódási karakterláncot használ, amely nem rendelkezik a **kezelés** engedéllyel, állítsa a `accessRights` "Listen" (figyelés) értékre. Ellenkező esetben a Functions runtime meghiúsulhat igénylő műveletek végrehajtását kezelésére. A Azure Functions 2. x vagy újabb verziójában ez a tulajdonság nem érhető el, mert a Service Bus SDK legújabb verziója nem támogatja a kezelési műveleteket.|
|**isSessionsEnabled**|**IsSessionsEnabled**|`true`, ha [munkamenet-kompatibilis](../service-bus-messaging/message-sessions.md) várólistához vagy előfizetéshez csatlakozik. `false`, amely az alapértelmezett érték.|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="usage"></a>Használat

# <a name="c"></a>[C#](#tab/csharp)

A következő típusparaméter-típusok érhetők el a várólista vagy a témakör üzeneteihez:

* `string` – ha az üzenet szövege.
* `byte[]` – hasznos a bináris adattároláshoz.
* Egy egyéni típusa – Ha az üzenet tartalmazza JSON-t, az Azure Functions próbálja meg a JSON-adatok deszerializálása.
* `BrokeredMessage` – a deszerializált üzenetet adja a [BrokeredMessage. GetBody\<t > ()](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.getbody?view=azure-dotnet#Microsoft_ServiceBus_Messaging_BrokeredMessage_GetBody__1) metódussal.

Ezek a paraméterek a Azure Functions 1. x verzióra vonatkoznak. a 2. x és újabb verziók esetében a [`Message`](https://docs.microsoft.com/dotnet/api/microsoft.azure.servicebus.message) használata `BrokeredMessage`helyett.

# <a name="c-script"></a>[C#Parancsfájl](#tab/csharp-script)

A következő típusparaméter-típusok érhetők el a várólista vagy a témakör üzeneteihez:

* `string` – ha az üzenet szövege.
* `byte[]` – hasznos a bináris adattároláshoz.
* Egy egyéni típusa – Ha az üzenet tartalmazza JSON-t, az Azure Functions próbálja meg a JSON-adatok deszerializálása.
* `BrokeredMessage` – a deszerializált üzenetet adja a [BrokeredMessage. GetBody\<t > ()](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.getbody?view=azure-dotnet#Microsoft_ServiceBus_Messaging_BrokeredMessage_GetBody__1) metódussal.

Ezek a paraméterek az 1. x Azure Functions-verzióra vonatkoznak. a 2. x és újabb verziók esetében a [`Message`](https://docs.microsoft.com/dotnet/api/microsoft.azure.servicebus.message) használata `BrokeredMessage`helyett.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

A várólista vagy a témakör üzenete `context.bindings.<name from function.json>`használatával érhető el. A Service Bus-üzenet, a függvény egy karakterlánc vagy a JSON-objektum lett átadva.

# <a name="python"></a>[Python](#tab/python)

Az üzenetsor-üzenet `func.ServiceBusMessage`ként beírt paraméterrel érhető el a függvény számára. A Service Bus-üzenet, a függvény egy karakterlánc vagy a JSON-objektum lett átadva.

# <a name="java"></a>[Java](#tab/java)

A bejövő Service Bus üzenet `ServiceBusQueueMessage` vagy `ServiceBusTopicMessage` paraméter használatával érhető el.

[A részletekért tekintse meg a példát](#example).

---

## <a name="poison-messages"></a>Üzenetek megmérgezve

Ártalmas üzenetek kezelése nem szabályozza és nem konfigurált az Azure Functions szolgáltatásban. A Service Bus kezeli az ártalmas üzenetek magát.

## <a name="peeklock-behavior"></a>PeekLock viselkedés

A functions futtatókörnyezet [PeekLock módban](../service-bus-messaging/service-bus-performance-improvements.md#receive-mode)fogad üzenetet. Ha a függvény sikeresen befejeződik, meghívja a `Complete`t az üzenetben, vagy meghívja a `Abandon`, ha a függvény meghibásodik. Ha a függvény a `PeekLock` időtúllépésnél hosszabb ideig fut, akkor a rendszer automatikusan megújítja a zárolást, amíg a függvény fut. 

A `maxAutoRenewDuration` a *Host. JSON*fájlban konfigurálható, amely a [OnMessageOptions. MaxAutoRenewDuration](https://docs.microsoft.com/dotnet/api/microsoft.azure.servicebus.messagehandleroptions.maxautorenewduration?view=azure-dotnet)leképezésére szolgál. Ez a beállítás számára engedélyezett maximális érték megfelelően a Service Bus dokumentációja, 5 perc, míg 10 perc az alapértelmezett 5 perces időbeli függvények korlát növeléséhez. A Service Bus-funkciók nem szeretné, amely majd, mivel a Service Bus-megújítási korlát túllépését eredményezné.

## <a name="message-metadata"></a>Üzenet metaadatai

A Service Bus trigger számos [metaadat-tulajdonságot](./functions-bindings-expressions-patterns.md#trigger-metadata)biztosít. Ezek a tulajdonságok a kötési kifejezésekben való használata más kötések részeként vagy a kód paramétereiben használható. Ezek a tulajdonságok a [BrokeredMessage](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.brokeredmessage) osztály tagjai.

|Tulajdonság|Típus|Leírás|
|--------|----|-----------|
|`DeliveryCount`|`Int32`|A kézbesítések száma.|
|`DeadLetterSource`|`string`|Kézbesítetlen levelek forrása.|
|`ExpiresAtUtc`|`DateTime`|A lejárat időpontja (UTC).|
|`EnqueuedTimeUtc`|`DateTime`|A várólistán lévő időpontja (UTC).|
|`MessageId`|`string`|Egy felhasználó által definiált érték, amely a Service Bus duplikált üzenetek azonosítására használható, ha engedélyezve van.|
|`ContentType`|`string`|A küldő és a fogadó által az alkalmazásspecifikus logikához használt tartalomtípus-azonosító.|
|`ReplyTo`|`string`|A válasz várólista-címre.|
|`SequenceNumber`|`Int64`|Egy üzenetet a Service Bus által hozzárendelt egyedi szám.|
|`To`|`string`|A küldési címre.|
|`Label`|`string`|Az alkalmazásra vonatkozó címke.|
|`CorrelationId`|`string`|A korrelációs azonosítót.|

Tekintse meg a jelen cikk korábbi részében említett tulajdonságokat használó [példákat](#example) .

## <a name="next-steps"></a>Következő lépések

- [Azure Service Bus üzenetek küldése Azure Functionsból (kimeneti kötés)](./functions-bindings-service-bus-output.md)
