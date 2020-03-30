---
title: Az Azure Service Bus kötései az Azure Functionshez
description: Ismerje meg, hogy futtassa az Azure-függvényt, amikor az Azure Service Bus-üzenetek jönnek létre.
author: craigshoemaker
ms.assetid: daedacf0-6546-4355-a65c-50873e74f66b
ms.topic: reference
ms.date: 02/19/2020
ms.author: cshoe
ms.openlocfilehash: 1ead7fcd9d474369e3a62e372a971d88d26f4e9c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78273562"
---
# <a name="azure-service-bus-trigger-for-azure-functions"></a>Az Azure Service Bus eseményindítója az Azure Functionshez

A Service Bus eseményindító használatával válaszolhat a Service Bus-várólistából vagy témakörből érkező üzenetekre.

A beállítással és a konfigurációval kapcsolatos részletekről az [áttekintésben](functions-bindings-service-bus-output.md)olvashat.

## <a name="example"></a>Példa

# <a name="c"></a>[C #](#tab/csharp)

A következő példa egy [C# függvényt](functions-dotnet-class-library.md) mutat be, amely beolvassa [az üzenet metaadatait,](#message-metadata) és naplózza a Service Bus várólista üzenetét:

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

# <a name="c-script"></a>[C# parancsfájl](#tab/csharp-script)

A következő példa egy Service Bus-eseményindító tegy *function.json* fájlés egy [C# parancsfájl függvény,](functions-reference-csharp.md) amely a kötést használja. A függvény [beolvassa az üzenetek metaadatait,](#message-metadata) és naplózza a Service Bus várólista-üzenetét.

A *function.json* fájlban a kötési adatok:

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

Itt a C# script kód:

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

# <a name="javascript"></a>[Javascript](#tab/javascript)

A következő példa egy Service Bus eseményindító kötést mutat be egy *function.json* fájlban, és egy [JavaScript függvényt,](functions-reference-node.md) amely a kötést használja. A függvény [beolvassa az üzenetek metaadatait,](#message-metadata) és naplózza a Service Bus várólista-üzenetét. 

A *function.json* fájlban a kötési adatok:

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

Itt a JavaScript script kód:

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

A következő példa bemutatja, hogyan kell olvasni a Service Bus-üzenet egy eseményindítón keresztül.

A Service Bus-kötés a *function.json* ban `serviceBusTrigger`van definiálva, ahol a *típus* a .

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

Az * _ \_init_\_.py* kódja egy `func.ServiceBusMessage`paramétert deklarál , amely lehetővé teszi a sorsorüzenet olvasását a függvényben.

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

A következő Java `@ServiceBusQueueTrigger` függvény a [Java függvény futásidejű függvénytárból](/java/api/overview/azure/functions/runtime) származó jegyzetet használja a Service Bus várólista-eseményindítókonfigurációjának leírására. A függvény megragadja a várólistára helyezett üzenetet, és hozzáadja a naplókhoz.

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

Java-függvények akkor is aktiválhatók, ha egy üzenet hozzáadódik egy Service Bus-témakörhöz. A következő példa `@ServiceBusTopicTrigger` a jegyzet et használja az eseményindító konfigurációjának leírására.

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

A [C# osztálytárakban](functions-dotnet-class-library.md)a következő attribútumokkal konfigurálhatja a Service Bus eseményindítót:

* [ServiceBusTriggerAttribute](https://github.com/Azure/azure-functions-servicebus-extension/blob/master/src/Microsoft.Azure.WebJobs.Extensions.ServiceBus/ServiceBusTriggerAttribute.cs)

  Az attribútum konstruktora veszi a nevét a várólista vagy a témakör és az előfizetés. Az Azure Functions 1.x-es verziójában megadhatja a kapcsolat hozzáférési jogait is. Ha nem ad meg hozzáférési jogokat, az alapértelmezett érték `Manage`a . További információt az [Eseményindító - konfiguráció](#configuration) című szakaszban talál.

  Íme egy példa, amely a karakterlánc-paraméterrel használt attribútumot mutatja be:

  ```csharp
  [FunctionName("ServiceBusQueueTriggerCSharp")]                    
  public static void Run(
      [ServiceBusTrigger("myqueue")] string myQueueItem, ILogger log)
  {
      ...
  }
  ```

  Beállíthatja, `Connection` hogy a tulajdonság adja meg a használni kívánt Service Bus-kapcsolati karakterláncot tartalmazó alkalmazásbeállítás nevét, ahogy az a következő példában látható:

  ```csharp
  [FunctionName("ServiceBusQueueTriggerCSharp")]                    
  public static void Run(
      [ServiceBusTrigger("myqueue", Connection = "ServiceBusConnection")] 
      string myQueueItem, ILogger log)
  {
      ...
  }
  ```

  Egy teljes példa, [lásd: Trigger - példa](#example).

* [ServiceBusAccountAttribute](https://github.com/Azure/azure-functions-servicebus-extension/blob/master/src/Microsoft.Azure.WebJobs.Extensions.ServiceBus/ServiceBusAccountAttribute.cs)

  Egy másik módja a használandó Service Bus-fiók megadásának. A konstruktor egy Service Bus-kapcsolati karakterláncot tartalmazó alkalmazásbeállítás nevét veszi fel. Az attribútum a paraméter, a metódus vagy az osztály szintjén alkalmazható. A következő példa az osztályszintet és a metódusszintet mutatja be:

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

A használandó Service Bus-fiók meghatározása a következő sorrendben történik:

* Az `ServiceBusTrigger` attribútum tulajdona. `Connection`
* Az `ServiceBusAccount` `ServiceBusTrigger` attribútummal azonos paraméterre alkalmazott attribútum.
* A `ServiceBusAccount` függvényre alkalmazott attribútum.
* Az `ServiceBusAccount` osztályra alkalmazott attribútum.
* Az "AzureWebJobsServiceBus" alkalmazás beállítás.

# <a name="c-script"></a>[C# parancsfájl](#tab/csharp-script)

Az attribútumokat a C# script nem támogatja.

# <a name="javascript"></a>[Javascript](#tab/javascript)

A JavaScript nem támogatja az attribútumokat.

# <a name="python"></a>[Python](#tab/python)

Az attribútumokat a Python nem támogatja.

# <a name="java"></a>[Java](#tab/java)

A `ServiceBusQueueTrigger` jegyzet lehetővé teszi, hogy hozzon létre egy függvényt, amely fut, amikor a Service Bus üzenet jön létre. A rendelkezésre álló konfigurációs beállítások közé tartozik a várólista neve és a kapcsolati karakterlánc neve.

A `ServiceBusTopicTrigger` jegyzet lehetővé teszi, hogy jelöljön ki egy témakört és előfizetést, hogy milyen adatok váltják ki a függvényt.

További részletekért tekintse meg az eseményindító [példát.](#example)

---

## <a name="configuration"></a>Konfiguráció

Az alábbi táblázat a *function.json* fájlban és az `ServiceBusTrigger` attribútumban beállított kötési konfigurációs tulajdonságokat ismerteti.

|function.json tulajdonság | Attribútum tulajdonság |Leírás|
|---------|---------|----------------------|
|**Típus** | n/a | A "serviceBusTrigger" beállításnak kell lennie. Ez a tulajdonság automatikusan be van állítva, amikor létrehozza az eseményindítót az Azure Portalon.|
|**direction** | n/a | Be kell állítani, hogy "in". Ez a tulajdonság automatikusan be van állítva, amikor létrehozza az eseményindítót az Azure Portalon. |
|**név** | n/a | A várólista- vagy témakörüzenetet jelképező változó neve a függvénykódban. |
|**queueName (sornév)**|**Várólistaneve**|A figyelni nek ikon neve.  Csak akkor állítsa be, ha egy várólistát figyel, nem egy témakörhöz.
|**topicName (témakör neve)**|**Témakör neve**|A figyelni fogandó témakör neve. Csak akkor állítsa be, ha egy témakört figyel, nem egy várólistához.|
|**subscriptionName**|**Előfizetésneve**|A figyelni rekettel kapcsolatos előfizetés neve. Csak akkor állítsa be, ha egy témakört figyel, nem egy várólistához.|
|**Kapcsolat**|**Kapcsolat**|A kötéshez használandó Service Bus-kapcsolati karakterláncot tartalmazó alkalmazásbeállítás neve. Ha az alkalmazásbeállítás neve "AzureWebJobs" programmal kezdődik, csak a név fennmaradó részét adhatja meg. Ha például "MyServiceBus" beállítást állít be, `connection` a Functions futásidejű megkeresi az "AzureWebJobsMyServiceBus" nevű alkalmazásbeállítást. Ha üresen hagyja, `connection` a Functions futásidejű az alapértelmezett Service Bus-kapcsolati karakterláncot használja az "AzureWebJobsServiceBus" nevű alkalmazásbeállításban.<br><br>Kapcsolati karakterlánc beszerzéséhez kövesse [a Felügyeleti hitelesítő adatok beszerzése](../service-bus-messaging/service-bus-quickstart-portal.md#get-the-connection-string)című rész lépéseit. A kapcsolati karakterláncnak egy Service Bus-névtérhez kell tartoznia, nem korlátozódik egy adott várólistára vagy témakörre. |
|**accessRights (hozzáférési jogok)**|**Hozzáférés**|A kapcsolati karakterlánc hozzáférési jogosultságai. A rendelkezésre álló értékek a `manage` és `listen`a. Az alapértelmezett `manage`beállítás azt jelzi, hogy a `connection` rendelkezik a **Manage** engedéllyel. Ha olyan kapcsolati karakterláncot használ, amely `accessRights` nem rendelkezik **Manage** engedéllyel, állítsa a "listen" (figyelés) beállítást. Ellenkező esetben előfordulhat, hogy a Functions futásidejű sikertelen lesz a kezelési jogosultságokat igénylő műveletek megkísérlése. Az Azure Functions 2.x-es és újabb verziójában ez a tulajdonság nem érhető el, mert a Service Bus SDK legújabb verziója nem támogatja a kezelési műveleteket.|
|**isSessionsEnabled**|**IsSessionsEnabled**|`true`[munkamenet-barát](../service-bus-messaging/message-sessions.md) várólistához vagy előfizetéshez való csatlakozás esetén. `false`ellenkező esetben, amely az alapértelmezett érték.|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="usage"></a>Használat

# <a name="c"></a>[C #](#tab/csharp)

A várólista- vagy témakörüzenethez a következő paramétertípusok érhetők el:

* `string`- Ha az üzenet sms-ben van.
* `byte[]`- Hasznos bináris adatok.
* Egyéni típus – Ha az üzenet JSON-t tartalmaz, az Azure Functions megpróbálja deszerializni a JSON-adatokat.
* `BrokeredMessage`- Adeszerializált üzenetet adja a [BrokeredMessage.GetBody\<T>()](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.getbody?view=azure-dotnet#Microsoft_ServiceBus_Messaging_BrokeredMessage_GetBody__1) metódussal.

Ezek a paramétertípusok az Azure Functions 1.x-es verziójához tartoznak; 2.x és újabb [`Message`](https://docs.microsoft.com/dotnet/api/microsoft.azure.servicebus.message) verzióesetén `BrokeredMessage`használja a helyett.

# <a name="c-script"></a>[C# parancsfájl](#tab/csharp-script)

A várólista- vagy témakörüzenethez a következő paramétertípusok érhetők el:

* `string`- Ha az üzenet sms-ben van.
* `byte[]`- Hasznos bináris adatok.
* Egyéni típus – Ha az üzenet JSON-t tartalmaz, az Azure Functions megpróbálja deszerializni a JSON-adatokat.
* `BrokeredMessage`- Adeszerializált üzenetet adja a [BrokeredMessage.GetBody\<T>()](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.getbody?view=azure-dotnet#Microsoft_ServiceBus_Messaging_BrokeredMessage_GetBody__1) metódussal.

Ezek a paraméterek az Azure Functions 1.x-es verziójához tartoznak; 2.x és újabb [`Message`](https://docs.microsoft.com/dotnet/api/microsoft.azure.servicebus.message) verzióesetén `BrokeredMessage`használja a helyett.

# <a name="javascript"></a>[Javascript](#tab/javascript)

A várólista- vagy `context.bindings.<name from function.json>`témakörüzenet elérése a használatával. A Service Bus üzenet karakterláncként vagy JSON-objektumként kerül át a függvénybe.

# <a name="python"></a>[Python](#tab/python)

A várólista-üzenet a függvény számára `func.ServiceBusMessage`a . A Service Bus üzenet karakterláncként vagy JSON-objektumként kerül át a függvénybe.

# <a name="java"></a>[Java](#tab/java)

A bejövő Service Bus üzenet `ServiceBusQueueMessage` egy `ServiceBusTopicMessage` vagy paraméteren keresztül érhető el.

[A részleteket lásd a példában.](#example)

---

## <a name="poison-messages"></a>Méreg üzenetek

A mérgező üzenetek kezelése nem vezérelhető vagy konfigurálható az Azure Functionsben. A Service Bus kezeli magát a méregüzeneteket.

## <a name="peeklock-behavior"></a>PeekLock viselkedés

A Functions futásidejű üzenet [PeekLock módban](../service-bus-messaging/service-bus-performance-improvements.md#receive-mode)érkezik. Meghívja `Complete` az üzenetet, ha a függvény `Abandon` sikeresen befejeződik, vagy ha a függvény meghibásodik. Ha a függvény hosszabb `PeekLock` ideig fut, mint az időtúllépés, a zárolás automatikusan megújul, amíg a függvény fut. 

Az `maxAutoRenewDuration` konfigurálható a *host.json*nyelven , amely [az OnMessageOptions.MaxAutoRenewDuration](https://docs.microsoft.com/dotnet/api/microsoft.azure.servicebus.messagehandleroptions.maxautorenewduration?view=azure-dotnet)elemre van leképezve. A beállítás maximális engedélyezett 5 perc a Service Bus dokumentációszerint, mivel a Functions időkorlátot az alapértelmezett 5 percről 10 percre növelheti. A Service Bus-funkciók akkor nem szeretné ezt megtenni, mert túllépi a Service Bus megújítási korlátot.

## <a name="message-metadata"></a>Üzenet metaadatai

A Service Bus eseményindító számos [metaadat-tulajdonságot](./functions-bindings-expressions-patterns.md#trigger-metadata)biztosít. Ezek a tulajdonságok más kötésekben lévő kötési kifejezések részeként vagy a kódban paraméterekként használhatók. Ezek a tulajdonságok a [BrokeredMessage](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.brokeredmessage) osztály tagjai.

|Tulajdonság|Típus|Leírás|
|--------|----|-----------|
|`DeliveryCount`|`Int32`|A szállítások száma.|
|`DeadLetterSource`|`string`|A halott levél forrása.|
|`ExpiresAtUtc`|`DateTime`|A lejárati idő UTC-ben.|
|`EnqueuedTimeUtc`|`DateTime`|A várólistán lévő idő utc-ben.|
|`MessageId`|`string`|A felhasználó által definiált érték, amelyet a Service Bus használhat az ismétlődő üzenetek azonosítására, ha engedélyezve van.|
|`ContentType`|`string`|A feladó és a fogadó által az alkalmazásspecifikus logikához használt tartalomtípus-azonosító.|
|`ReplyTo`|`string`|A várólista címére adott válasz.|
|`SequenceNumber`|`Int64`|A Service Bus által egy üzenethez rendelt egyedi szám.|
|`To`|`string`|A küldés a címre.|
|`Label`|`string`|Az alkalmazásspecifikus címke.|
|`CorrelationId`|`string`|A korrelációs azonosító.|

Tekintse meg a cikk korábbi, ezeket a tulajdonságokat használó [példákat.](#example)

## <a name="next-steps"></a>További lépések

- [Azure Service Bus-üzenetek küldése az Azure Functions szolgáltatásból (kimenetkötés)](./functions-bindings-service-bus-output.md)
