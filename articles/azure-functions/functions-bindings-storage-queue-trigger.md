---
title: Az Azure-függvények Azure-várólista-tárolási eseményindítója
description: Ismerje meg, hogyan futtathat egy Azure-függvényt az Azure Queue storage adatainak változásakor.
author: craigshoemaker
ms.topic: reference
ms.date: 02/18/2020
ms.author: cshoe
ms.custom: cc996988-fb4f-47
ms.openlocfilehash: 74ca984232bef979062221a451d0ee10a6965bc6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79277374"
---
# <a name="azure-queue-storage-trigger-for-azure-functions"></a>Az Azure-függvények Azure-várólista-tárolási eseményindítója

A várólista tárolási eseményindító fut egy függvényt, mint üzenetek hozzá adódnak az Azure Queue storage.

## <a name="encoding"></a>Encoding

A függvények *base64* kódolású karakterláncot várnak. A kódolási típus bármilyen módosítását (az adatok *alapként való előkészítése érdekében64* kódolású karakterláncként) a hívó szolgáltatásban kell végrehajtani.

## <a name="example"></a>Példa

A várólista-eseményindító val indíthatja el a függvényt, amikor új elem érkezik egy várólistára. A várakozó üzenetet a függvény bemeneteként adja át.

# <a name="c"></a>[C #](#tab/csharp)

A következő példa egy [C# függvényt](functions-dotnet-class-library.md) mutat be, amely lekérdezi a `myqueue-items` várólistát, és naplót ír minden alkalommal, amikor egy várólistaelem feldolgozásra kerül.

```csharp
public static class QueueFunctions
{
    [FunctionName("QueueTrigger")]
    public static void QueueTrigger(
        [QueueTrigger("myqueue-items")] string myQueueItem, 
        ILogger log)
    {
        log.LogInformation($"C# function processed: {myQueueItem}");
    }
}
```

# <a name="c-script"></a>[C# parancsfájl](#tab/csharp-script)

A következő példa egy *function.json* fájlban és a [c# parancsfájl (.csx)](functions-reference-csharp.md) kódban lévő várólista-eseményindító-kötést mutatja be, amely a kötést használja. A függvény `myqueue-items` lekérdezi a várólistát, és naplót ír minden alkalommal, amikor egy várólistaelemet feldolgoznak.

Itt a *function.json* fájl:

```json
{
    "disabled": false,
    "bindings": [
        {
            "type": "queueTrigger",
            "direction": "in",
            "name": "myQueueItem",
            "queueName": "myqueue-items",
            "connection":"MyStorageConnectionAppSetting"
        }
    ]
}
```

A [konfigurációs](#configuration) szakasz ismerteti ezeket a tulajdonságokat.

Itt a C# script kód:

```csharp
#r "Microsoft.WindowsAzure.Storage"

using Microsoft.Extensions.Logging;
using Microsoft.WindowsAzure.Storage.Queue;
using System;

public static void Run(CloudQueueMessage myQueueItem, 
    DateTimeOffset expirationTime, 
    DateTimeOffset insertionTime, 
    DateTimeOffset nextVisibleTime,
    string queueTrigger,
    string id,
    string popReceipt,
    int dequeueCount,
    ILogger log)
{
    log.LogInformation($"C# Queue trigger function processed: {myQueueItem.AsString}\n" +
        $"queueTrigger={queueTrigger}\n" +
        $"expirationTime={expirationTime}\n" +
        $"insertionTime={insertionTime}\n" +
        $"nextVisibleTime={nextVisibleTime}\n" +
        $"id={id}\n" +
        $"popReceipt={popReceipt}\n" + 
        $"dequeueCount={dequeueCount}");
}
```

A [használati](#usage) `myQueueItem`szakasz ismerteti , `name` amelyet a function.json tulajdonság a névadója.  Az [üzenet metaadatai szakasz](#message-metadata) ismerteti az összes többi változót.

# <a name="javascript"></a>[Javascript](#tab/javascript)

A következő példa egy várólista-eseményindító-kötést mutat be egy *function.json* fájlban, és egy [JavaScript-függvényt,](functions-reference-node.md) amely a kötést használja. A függvény `myqueue-items` lekérdezi a várólistát, és naplót ír minden alkalommal, amikor egy várólistaelemet feldolgoznak.

Itt a *function.json* fájl:

```json
{
    "disabled": false,
    "bindings": [
        {
            "type": "queueTrigger",
            "direction": "in",
            "name": "myQueueItem",
            "queueName": "myqueue-items",
            "connection":"MyStorageConnectionAppSetting"
        }
    ]
}
```

A [konfigurációs](#configuration) szakasz ismerteti ezeket a tulajdonságokat.

> [!NOTE]
> A név paraméter `context.bindings.<name>` tükrözi, mint a JavaScript kódot, amely tartalmazza a várólista elem hasznos tartalmat. Ez a hasznos adat is átkerül a függvény második paramétereként.

Itt a JavaScript-kód:

```javascript
module.exports = async function (context, message) {
    context.log('Node.js queue trigger function processed work item', message);
    // OR access using context.bindings.<name>
    // context.log('Node.js queue trigger function processed work item', context.bindings.myQueueItem);
    context.log('expirationTime =', context.bindingData.expirationTime);
    context.log('insertionTime =', context.bindingData.insertionTime);
    context.log('nextVisibleTime =', context.bindingData.nextVisibleTime);
    context.log('id =', context.bindingData.id);
    context.log('popReceipt =', context.bindingData.popReceipt);
    context.log('dequeueCount =', context.bindingData.dequeueCount);
    context.done();
};
```

A [használati](#usage) `myQueueItem`szakasz ismerteti , `name` amelyet a function.json tulajdonság a névadója.  Az [üzenet metaadatai szakasz](#message-metadata) ismerteti az összes többi változót.

# <a name="python"></a>[Python](#tab/python)

A következő példa bemutatja, hogyan kell olvasni egy üzenetsor-üzenet átadott egy függvény egy eseményindítón keresztül.

A storage-várólista-eseményindító a *function.json* ban van definiálva, ahol a *típus* a. `queueTrigger`

```json
{
  "scriptFile": "__init__.py",
  "bindings": [
    {
      "name": "msg",
      "type": "queueTrigger",
      "direction": "in",
      "queueName": "messages",
      "connection": "AzureStorageQueuesConnectionString"
    }
  ]
}
```

Az * _ \_init_\_.py* kód egy `func.ServiceBusMessage`paramétert deklarál , amely lehetővé teszi a sorsorüzenet olvasását a függvényben.

```python
import logging
import json

import azure.functions as func

def main(msg: func.QueueMessage):
    logging.info('Python queue trigger function processed a queue item.')

    result = json.dumps({
        'id': msg.id,
        'body': msg.get_body().decode('utf-8'),
        'expiration_time': (msg.expiration_time.isoformat()
                            if msg.expiration_time else None),
        'insertion_time': (msg.insertion_time.isoformat()
                           if msg.insertion_time else None),
        'time_next_visible': (msg.time_next_visible.isoformat()
                              if msg.time_next_visible else None),
        'pop_receipt': msg.pop_receipt,
        'dequeue_count': msg.dequeue_count
    })

    logging.info(result)
```

# <a name="java"></a>[Java](#tab/java)

A következő Java-példa egy tárolóvárólista-eseményindító funkciót mutat `myqueuename`be, amely naplózza a várólistába helyezett aktivált üzenetet.

 ```java
 @FunctionName("queueprocessor")
 public void run(
    @QueueTrigger(name = "msg",
                   queueName = "myqueuename",
                   connection = "myconnvarname") String message,
     final ExecutionContext context
 ) {
     context.getLogger().info(message);
 }
 ```

 ---

## <a name="attributes-and-annotations"></a>Attribútumok és jegyzetek

# <a name="c"></a>[C #](#tab/csharp)

A [C# osztálytárakban](functions-dotnet-class-library.md)a következő attribútumokkal konfigurálhat egy várólista-eseményindítót:

* [QueueTriggerAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.Extensions.Storage/Queues/QueueTriggerAttribute.cs)

  Az attribútum konstruktora felveszi a figyelni rekedváró nevét, ahogy az a következő példában látható:

  ```csharp
  [FunctionName("QueueTrigger")]
  public static void Run(
      [QueueTrigger("myqueue-items")] string myQueueItem, 
      ILogger log)
  {
      ...
  }
  ```

  Beállíthatja, `Connection` hogy a tulajdonság adja meg a storage-fiók kapcsolati karakterláncát tartalmazó alkalmazásbeállítást, ahogy az a következő példában látható:

  ```csharp
  [FunctionName("QueueTrigger")]
  public static void Run(
      [QueueTrigger("myqueue-items", Connection = "StorageConnectionAppSetting")] string myQueueItem, 
      ILogger log)
  {
      ....
  }
  ```

  A teljes példát lásd a [példában.](#example)

* [StorageAccountAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/StorageAccountAttribute.cs)

  Egy másik módja a használandó tárfiók megadásának. A konstruktor egy tárolási kapcsolati karakterláncot tartalmazó alkalmazásbeállítás nevét veszi fel. Az attribútum a paraméter, a metódus vagy az osztály szintjén alkalmazható. A következő példa az osztályszintet és a metódusszintet mutatja be:

  ```csharp
  [StorageAccount("ClassLevelStorageAppSetting")]
  public static class AzureFunctions
  {
      [FunctionName("QueueTrigger")]
      [StorageAccount("FunctionLevelStorageAppSetting")]
      public static void Run( //...
  {
      ...
  }
  ```

A felhasználandó tárfiókot a következő sorrendben határozzuk meg:

* Az `QueueTrigger` attribútum tulajdona. `Connection`
* Az `StorageAccount` `QueueTrigger` attribútummal azonos paraméterre alkalmazott attribútum.
* A `StorageAccount` függvényre alkalmazott attribútum.
* Az `StorageAccount` osztályra alkalmazott attribútum.
* Az "AzureWebJobsStorage" alkalmazás beállítás.

# <a name="c-script"></a>[C# parancsfájl](#tab/csharp-script)

Az attribútumokat a C# script nem támogatja.

# <a name="javascript"></a>[Javascript](#tab/javascript)

A JavaScript nem támogatja az attribútumokat.

# <a name="python"></a>[Python](#tab/python)

Az attribútumokat a Python nem támogatja.

# <a name="java"></a>[Java](#tab/java)

A `QueueTrigger` jegyzet hozzáférést biztosít a függvényt kiváltó várólistához. A következő példa elérhetővé teszi a `message` várólista-üzenetet a függvény számára a paraméteren keresztül.

```java
package com.function;
import com.microsoft.azure.functions.annotation.*;
import java.util.Queue;
import com.microsoft.azure.functions.*;

public class QueueTriggerDemo {
    @FunctionName("QueueTriggerDemo")
    public void run(
        @QueueTrigger(name = "message", queueName = "messages", connection = "MyStorageConnectionAppSetting") String message,
        final ExecutionContext context
    ) {
        context.getLogger().info("Queue message: " + message);
    }
}
```

| Tulajdonság    | Leírás |
|-------------|-----------------------------|
|`name`       | Deklarálja a paraméter nevét a függvényaláírásban. A függvény aktiválásakor a paraméter értéke a várólistaüzenet tartalmával rendelkezik. |
|`queueName`  | Deklarálja a várólista nevét a tárfiókban. |
|`connection` | A tárfiók kapcsolati karakterláncára mutat. |

---

## <a name="configuration"></a>Konfiguráció

Az alábbi táblázat a *function.json* fájlban és az `QueueTrigger` attribútumban beállított kötési konfigurációs tulajdonságokat ismerteti.

|function.json tulajdonság | Attribútum tulajdonság |Leírás|
|---------|---------|----------------------|
|**Típus** | n/a| A beállításnak `queueTrigger`a beállítására kell beállítható. Ez a tulajdonság automatikusan be van állítva, amikor létrehozza az eseményindítót az Azure Portalon.|
|**direction**| n/a | Csak a *function.json* fájlban. A beállításnak `in`a beállítására kell beállítható. Ez a tulajdonság automatikusan be van állítva, amikor létrehozza az eseményindítót az Azure Portalon. |
|**név** | n/a |Annak a változónak a neve, amely a várólistacikk hasznos adatát tartalmazza a függvénykódban.  |
|**queueName (sornév)** | **Várólistaneve**| A lehívandó várólista neve. |
|**Kapcsolat** | **Kapcsolat** |A kötéshez használandó Storage-kapcsolati karakterláncot tartalmazó alkalmazásbeállítás neve. Ha az alkalmazásbeállítás neve "AzureWebJobs" programmal kezdődik, itt csak a név fennmaradó részét adhatja meg. Ha például a `connection` "MyStorage" beállítást állítja be, a Functions futásidejű megkeresi a "MyStorage" nevű alkalmazásbeállítást. Ha üresen hagyja, `connection` a Functions futásidejű az alapértelmezett Storage-kapcsolati karakterláncot használja a neve súgás `AzureWebJobsStorage`alkalmazásbeállításban.|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="usage"></a>Használat

# <a name="c"></a>[C #](#tab/csharp)

Az üzenetadatok elérése metódusparaméter használatával, például `string paramName`. A következő típusok bármelyikéhez kötődhet:

* Objektum – A függvények futásidejű deszerializálja a JSON hasznos adat egy példánya egy tetszőleges osztály a kódban meghatározott. 
* `string`
* `byte[]`
* [CloudQueueMessage üzenet]

Ha megpróbál kötődni, `CloudQueueMessage` és hibaüzenetet kap, győződjön meg arról, hogy [rendelkezik a megfelelő Storage SDK-verzióra](functions-bindings-storage-queue.md#azure-storage-sdk-version-in-functions-1x)mutató hivatkozással.

# <a name="c-script"></a>[C# parancsfájl](#tab/csharp-script)

Az üzenetadatok elérése metódusparaméter használatával, például `string paramName`. A `paramName` a `name` *function.json*tulajdonságában megadott érték. A következő típusok bármelyikéhez kötődhet:

* Objektum – A függvények futásidejű deszerializálja a JSON hasznos adat egy példánya egy tetszőleges osztály a kódban meghatározott. 
* `string`
* `byte[]`
* [CloudQueueMessage üzenet]

Ha megpróbál kötődni, `CloudQueueMessage` és hibaüzenetet kap, győződjön meg arról, hogy [rendelkezik a megfelelő Storage SDK-verzióra](functions-bindings-storage-queue.md#azure-storage-sdk-version-in-functions-1x)mutató hivatkozással.

# <a name="javascript"></a>[Javascript](#tab/javascript)

A várólistacikk hasznos `context.bindings.<NAME>` `<NAME>` adata elérhető, ahol megegyezik a *function.json*ban megadott névvel. Ha a hasznos adat JSON, az érték objektummá lesz deszerializálva.

# <a name="python"></a>[Python](#tab/python)

A várólista-üzenet elérése a [QueueMessage](https://docs.microsoft.com/python/api/azure-functions/azure.functions.queuemessage?view=azure-python)paraméterrel.

# <a name="java"></a>[Java](#tab/java)

A [QueueTrigger](https://docs.microsoft.com/java/api/com.microsoft.azure.functions.annotation.queuetrigger?view=azure-java-stable) jegyzet hozzáférést biztosít a függvényt aktiváló várólista-üzenethez.

---

## <a name="message-metadata"></a>Üzenet metaadatai

A várólista-eseményindító számos [metaadat-tulajdonságot](./functions-bindings-expressions-patterns.md#trigger-metadata)biztosít. Ezek a tulajdonságok más kötésekben lévő kötési kifejezések részeként vagy a kódban paraméterekként használhatók. A tulajdonságok a [CloudQueueMessage](https://docs.microsoft.com/dotnet/api/microsoft.azure.storage.queue.cloudqueuemessage) osztály tagjai.

|Tulajdonság|Típus|Leírás|
|--------|----|-----------|
|`QueueTrigger`|`string`|Várólista hasznos adata (ha érvényes karakterlánc). Ha a várólista-üzenet `QueueTrigger` hasznos adata karakterlánc, akkor `name` a *function.json*tulajdonság által elnevezett változó értéke megegyezik.|
|`DequeueCount`|`int`|Az üzenet várólistán stormiusának száma.|
|`ExpirationTime`|`DateTimeOffset`|Az üzenet lejáratának időpontja.|
|`Id`|`string`|Várólista-üzenet azonosítója.|
|`InsertionTime`|`DateTimeOffset`|Az üzenet várólistához való hozzáadásának időpontja.|
|`NextVisibleTime`|`DateTimeOffset`|Az az időpont, amikor az üzenet legközelebb látható lesz.|
|`PopReceipt`|`string`|Az üzenet pop nyugtája.|

## <a name="poison-messages"></a>Méreg üzenetek

Ha egy várólista-eseményindító függvény sikertelen, az Azure Functions újrapróbálkozik a függvény akár ötször egy adott várólista-üzenet, beleértve az első próbálkozást. Ha mind az öt kísérlet sikertelen, a függvények futásidejű hozzáad egy üzenetet egy sorban nevezett * &lt;originalqueuename>-méreg*. Írhat egy függvényt a méregvárólistából érkező üzenetek feldolgozásához, ha naplózza őket, vagy értesítést küld arról, hogy manuális figyelemre van szükség.

A mérgezett üzenetek manuális kezeléséhez ellenőrizze a [várólista-üzenet dequeueCount.](#message-metadata)

## <a name="polling-algorithm"></a>Lekérdezési algoritmus

A várólista-eseményindító egy véletlenszerű, exponenciális visszalépési algoritmust valósít meg, hogy csökkentse az automatikus várólista-lekérdezés hatását a tárolási tranzakciós költségekre.

Az algoritmus a következő logikát használja:

- Ha egy üzenet található, a futásidő két másodpercet vár, majd egy másik üzenetet keres
- Ha nem található üzenet, körülbelül négy másodpercet vár, mielőtt újra próbálkozna.
- A várólista-üzenet lehívására tett későbbi sikertelen próbálkozások után a várakozási idő tovább növekszik, amíg el nem éri a maximális várakozási időt, amely alapértelmezés szerint egy perc.
- A maximális várakozási idő `maxPollingInterval` a [host.json fájlban](functions-host-json.md#queues)található tulajdonságon keresztül konfigurálható.

Helyi fejlesztés esetén a maximális lekérdezési időköz alapértelmezés szerint két másodperc.

A számlázás tekintetében a futásidejű lekérdezéssel töltött idő "ingyenes", és nem számít bele a fiókjába.

## <a name="concurrency"></a>Egyidejűség

Ha több vár vár vár várakozó vár, a várólista-eseményindító lekéri az üzenetek kötegét, és egyidejűleg hívja meg a függvénypéldányokat azok feldolgozásához. Alapértelmezés szerint a köteg mérete 16. Amikor a feldolgozott szám 8-ra csökken, a futásidejű egy másik köteget kap, és megkezdi az üzenetek feldolgozását. Így egy virtuális gépen (VM) függvényenként feldolgozott egyidejű üzenetek maximális száma 24. Ez a korlát külön-külön vonatkozik az egyes virtuális gépeken minden várólista által aktivált függvényre. Ha a függvényalkalmazás több virtuális gépre skálázódik, minden virtuális gép megvárja az eseményindítókat, és megkísérli a függvények futtatását. Ha például egy függvényalkalmazás 3 virtuális gépre skálázódik, egy várólista által aktivált függvény egyidejű példányainak alapértelmezett maximális száma 72.

A kötegmérete és az új köteg megszerzésének küszöbértéke konfigurálható a [host.json fájlban.](functions-host-json.md#queues) Ha azt szeretné, hogy minimalizálja a párhuzamos végrehajtás a várólista által aktivált függvények egy függvényalkalmazásban, beállíthatja a kötegméretet 1. Ez a beállítás csak addig szünteti meg az egyidejűséget, amíg a függvényalkalmazás egyetlen virtuális gépen (VM) fut. 

A várólista-eseményindító automatikusan megakadályozza, hogy egy függvény többször is feldolgozzon egy üzenetsor-üzenetet; funkciókat nem kell megírni ahhoz, hogy idempotensek legyenek.

## <a name="hostjson-properties"></a>host.json tulajdonságok

A [host.json](functions-host-json.md#queues) fájl olyan beállításokat tartalmaz, amelyek szabályozzák a várólista-eseményindító viselkedését. Az elérhető beállításokkal kapcsolatos részleteket a [host.json beállítások](functions-bindings-storage-queue-output.md#hostjson-settings) részben találja.

## <a name="next-steps"></a>További lépések

- [Várólista-tárolási üzenetek írása (Kimenetkötés)](./functions-bindings-storage-blob-output.md)

<!-- LINKS -->

[CloudQueueMessage üzenet]: /dotnet/api/microsoft.azure.storage.queue.cloudqueuemessage
