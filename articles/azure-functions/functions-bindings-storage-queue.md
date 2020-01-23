---
title: Azure üzenetsor-tárolási kötések Azure Functionshoz
description: Ismerje meg, hogyan használható az Azure üzenetsor-tároló-trigger és a kimeneti kötés a Azure Functionsban.
author: craigshoemaker
ms.topic: reference
ms.date: 09/03/2018
ms.author: cshoe
ms.custom: cc996988-fb4f-47
ms.openlocfilehash: ea213921c736bc3b6bf88c0bdd81a96656ecbe5b
ms.sourcegitcommit: 87781a4207c25c4831421c7309c03fce5fb5793f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/23/2020
ms.locfileid: "76547285"
---
# <a name="azure-queue-storage-bindings-for-azure-functions"></a>Azure üzenetsor-tárolási kötések Azure Functionshoz

Ez a cikk azt ismerteti, hogyan használható az Azure üzenetsor-tárolási kötések Azure Functions. Azure Functions támogatja a várólisták trigger-és kimeneti kötéseit.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="packages---functions-1x"></a>Csomagok – 1. x függvények

A várólista-tárolási kötések a [Microsoft. Azure. webjobs](https://www.nuget.org/packages/Microsoft.Azure.WebJobs) Nuget csomagban, 2. x verzióban találhatók. A csomag forráskódja az [Azure-webjobs-SDK](https://github.com/Azure/azure-webjobs-sdk/tree/v2.x/src/Microsoft.Azure.WebJobs.Storage/Queue) GitHub-tárházban található.

[!INCLUDE [functions-package-auto](../../includes/functions-package-auto.md)]

[!INCLUDE [functions-storage-sdk-version](../../includes/functions-storage-sdk-version.md)]

## <a name="packages---functions-2x-and-higher"></a>Csomagok – 2. x és újabb függvények

A várólista-tárolási kötések a [Microsoft. Azure. webjobs. Extensions. Storage](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.Storage) Nuget csomag 3. x verziójában érhetők el. A csomag forráskódja az [Azure-webjobs-SDK](https://github.com/Azure/azure-webjobs-sdk/tree/dev/src/Microsoft.Azure.WebJobs.Extensions.Storage/Queues) GitHub-tárházban található.

[!INCLUDE [functions-package-v2](../../includes/functions-package-v2.md)]

## <a name="encoding"></a>Encoding
A függvények *Base64* kódolású karakterláncot várnak. A kódolási típus összes módosítását (az adatgyűjtés *Base64* kódolású karakterláncként való előkészítéséhez) a hívó szolgáltatásban kell végrehajtani.

## <a name="trigger"></a>Eseményindító

A várólista-trigger használatával elindíthat egy függvényt, ha új elem érkezik egy várólistán. A várakozó üzenetet a függvény bemeneteként adja át.

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

Az alábbi példa egy olyan [ C# függvényt](functions-dotnet-class-library.md) mutat be, amely lekérdezi a `myqueue-items` várólistát, és minden alkalommal beírja a naplót, amikor a várólista-elemek feldolgozása történik.

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

# <a name="c-scripttabcsharp-script"></a>[C#Parancsfájl](#tab/csharp-script)

Az alábbi példa egy üzenetsor-trigger kötést mutat be egy *function. JSON* fájlban és [ C# a kötést használó script (. CSX)](functions-reference-csharp.md) kódban. A függvény lekérdezi a `myqueue-items` várólistát, és minden alkalommal egy naplót ír a várólista-elemek feldolgozásakor.

Itt látható a *function. JSON* fájl:

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

A [konfigurációs](#trigger---configuration) szakasz ezeket a tulajdonságokat ismerteti.

A C# szkript kódja:

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

A [használat](#trigger---usage) szakasz ismerteti `myQueueItem`, amelyet a function. json fájl `name` tulajdonsága nevez el.  Az [üzenet metaadatainak szakasza](#trigger---message-metadata) a többi megjelenített változót ismerteti.

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

Az alábbi példa egy várólista-trigger kötését mutatja be egy *function. JSON* fájlban, valamint egy [JavaScript-függvényt](functions-reference-node.md) , amely a kötést használja. A függvény lekérdezi a `myqueue-items` várólistát, és minden alkalommal egy naplót ír a várólista-elemek feldolgozásakor.

Itt látható a *function. JSON* fájl:

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

A [konfigurációs](#trigger---configuration) szakasz ezeket a tulajdonságokat ismerteti.

> [!NOTE]
> A name paraméter azt a JavaScript-kódban `context.bindings.<name>`t tükrözi, amely tartalmazza a várólista-elemek hasznos adatait. Ezt a hasznos adatot a függvény második paramétereként is átadja.

Itt látható a JavaScript-kód:

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

A [használat](#trigger---usage) szakasz ismerteti `myQueueItem`, amelyet a function. json fájl `name` tulajdonsága nevez el.  Az [üzenet metaadatainak szakasza](#trigger---message-metadata) a többi megjelenített változót ismerteti.

# <a name="pythontabpython"></a>[Python](#tab/python)

Az alábbi példa azt szemlélteti, hogyan lehet beolvasni egy függvénynek egy trigger használatával átadott üzenetsor-üzenetet.

A Storage-várólista triggere a *function. JSON* fájlban van definiálva, ahol a *Type* értéke `queueTrigger`.

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

A kód  *_\_init_\_. a. a. a. a. a.* a paramétert `func.ServiceBusMessage`ként deklarálja, amely lehetővé teszi a várólista-üzenet olvasását a függvényben.

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

# <a name="javatabjava"></a>[Java](#tab/java)

A következő Java-példa egy Storage üzenetsor-kiváltó függvényt mutat be, amely naplózza az elindított üzenetet az üzenetsor `myqueuename`ba.

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

## <a name="trigger---attributes-and-annotations"></a>Trigger – attribútumok és jegyzetek

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

Az [ C# osztályok könyvtáraiban](functions-dotnet-class-library.md)használja a következő attribútumokat az üzenetsor-trigger konfigurálásához:

* [QueueTriggerAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.Extensions.Storage/Queues/QueueTriggerAttribute.cs)

  Az attribútum konstruktora a figyelni kívánt várólista nevét adja meg, ahogy az az alábbi példában is látható:

  ```csharp
  [FunctionName("QueueTrigger")]
  public static void Run(
      [QueueTrigger("myqueue-items")] string myQueueItem, 
      ILogger log)
  {
      ...
  }
  ```

  A `Connection` tulajdonság beállításával megadhatja a használni kívánt Storage-fiók kapcsolódási karakterláncát tartalmazó Alkalmazásbeállítások értékét az alábbi példában látható módon:

  ```csharp
  [FunctionName("QueueTrigger")]
  public static void Run(
      [QueueTrigger("myqueue-items", Connection = "StorageConnectionAppSetting")] string myQueueItem, 
      ILogger log)
  {
      ....
  }
  ```

  A teljes példa: [trigger- C# example](#trigger).

* [StorageAccountAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/StorageAccountAttribute.cs)

  Egy másik módszert biztosít a használni kívánt Storage-fiók megadására. A konstruktor egy olyan Alkalmazásbeállítás nevét veszi fel, amely egy tárolási kapcsolatot tartalmazó karakterláncot tartalmaz. Az attribútumot a paraméter, a metódus vagy az osztály szintjén lehet alkalmazni. Az alábbi példa az osztály szintjét és a metódus szintjét mutatja be:

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

A használandó Storage-fiók a következő sorrendben van meghatározva:

* A `QueueTrigger` attribútum `Connection` tulajdonsága.
* A `StorageAccount` attribútum a `QueueTrigger` attribútummal megegyező paraméterre lett alkalmazva.
* A függvényre alkalmazott `StorageAccount` attribútum.
* Az osztályra alkalmazott `StorageAccount` attribútum.
* A "AzureWebJobsStorage" alkalmazás beállításai.

# <a name="c-scripttabcsharp-script"></a>[C#Parancsfájl](#tab/csharp-script)

Az C# attribútumokat a parancsfájl nem támogatja.

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

A Java-parancsfájlok nem támogatják az attribútumokat.

# <a name="pythontabpython"></a>[Python](#tab/python)

A Python nem támogatja az attribútumokat.

# <a name="javatabjava"></a>[Java](#tab/java)

A `QueueTrigger` jegyzet hozzáférést biztosít a függvényt kiváltó várólistához. A következő példa az üzenetsor-üzenetet elérhetővé teszi a függvény számára a `message` paraméterrel.

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
|`name`       | Deklarálja a paraméter nevét a függvény aláírásában. A függvény indításakor ennek a paraméternek az értéke az üzenetsor üzenetének tartalmát jeleníti meg. |
|`queueName`  | Deklarálja a várólista nevét a Storage-fiókban. |
|`connection` | A Storage-fiók kapcsolódási karakterláncára mutat. |

---

## <a name="trigger---configuration"></a>Trigger – konfiguráció

Az alábbi táblázat a *function. JSON* fájlban és a `QueueTrigger` attribútumban beállított kötési konfigurációs tulajdonságokat ismerteti.

|function. JSON-tulajdonság | Attribútum tulajdonsága |Leírás|
|---------|---------|----------------------|
|**type** | –| `queueTrigger`értékre kell állítani. Ez a tulajdonság automatikusan be van állítva, amikor létrehozza az triggert a Azure Portalban.|
|**direction**| – | Csak a *function. JSON* fájlban. `in`értékre kell állítani. Ez a tulajdonság automatikusan be van állítva, amikor létrehozza az triggert a Azure Portalban. |
|**név** | – |Annak a változónak a neve, amely a függvény kódjában található üzenetsor-elemek tartalmát tartalmazza.  |
|**queueName** | **QueueName**| A lekérdezni kívánt várólista neve. |
|**kapcsolat** | **Kapcsolat** |Egy olyan Alkalmazásbeállítás neve, amely a kötéshez használandó tárolási kapcsolati karakterláncot tartalmazza. Ha az Alkalmazásbeállítások neve "AzureWebJobs" előtaggal kezdődik, akkor itt csak a nevet adja meg. Ha például a `connection` "MyStorage" értékre állítja, a functions futtatókörnyezet egy "MyStorage" nevű alkalmazás-beállítást keres. Ha üresen hagyja a `connection`, a functions futtatókörnyezet az alapértelmezett Storage-kapcsolatok karakterláncot használja az `AzureWebJobsStorage`nevű alkalmazás-beállításban.|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="trigger---usage"></a>Trigger – használat

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

Az üzenet adatai egy metódus-paraméter (például `string paramName`) használatával érhetők el. A következő típusokhoz köthető:

* Objektum – a függvények futtatókörnyezete deszerializál egy JSON-adattartalmat a kódban definiált tetszőleges osztály egy példányára. 
* `string`
* `byte[]`
* [CloudQueueMessage]

Ha `CloudQueueMessage`hoz próbál kötni, és hibaüzenetet kap, ellenőrizze, hogy rendelkezik-e [a megfelelő Storage SDK-verzióra](#azure-storage-sdk-version-in-functions-1x)mutató hivatkozással.

# <a name="c-scripttabcsharp-script"></a>[C#Parancsfájl](#tab/csharp-script)

Az üzenet adatai egy metódus-paraméter (például `string paramName`) használatával érhetők el. A `paramName` a *function. json*`name` tulajdonságában megadott érték. A következő típusokhoz köthető:

* Objektum – a függvények futtatókörnyezete deszerializál egy JSON-adattartalmat a kódban definiált tetszőleges osztály egy példányára. 
* `string`
* `byte[]`
* [CloudQueueMessage]

Ha `CloudQueueMessage`hoz próbál kötni, és hibaüzenetet kap, ellenőrizze, hogy rendelkezik-e [a megfelelő Storage SDK-verzióra](#azure-storage-sdk-version-in-functions-1x)mutató hivatkozással.

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

A várólista-elem hasznos tartalma `context.bindings.<NAME>`on keresztül érhető el, ahol a `<NAME>` megegyezik a *function. JSON*fájlban megadott névvel. Ha a hasznos adat JSON, az érték deszerializálása egy objektumba történik.

# <a name="pythontabpython"></a>[Python](#tab/python)

Nyissa meg az üzenetsor-üzenetet a [QueueMessage](https://docs.microsoft.com/python/api/azure-functions/azure.functions.queuemessage?view=azure-python)típussal megadott paraméterrel.

# <a name="javatabjava"></a>[Java](#tab/java)

A [QueueTrigger](https://docs.microsoft.com/java/api/com.microsoft.azure.functions.annotation.queuetrigger?view=azure-java-stable) jegyzet hozzáférést biztosít a függvényt kiváltó üzenetsor-üzenethez.

---

## <a name="trigger---message-metadata"></a>Trigger – üzenet metaadatainak

A várólista-trigger számos [metaadat-tulajdonságot](./functions-bindings-expressions-patterns.md#trigger-metadata)biztosít. Ezek a tulajdonságok a más kötésekben lévő kötési kifejezések vagy a kódban szereplő paraméterek részeként is használhatók. Ezek a [CloudQueueMessage](https://docs.microsoft.com/dotnet/api/microsoft.azure.storage.queue.cloudqueuemessage) osztály tulajdonságai.

|Tulajdonság|Type (Típus)|Leírás|
|--------|----|-----------|
|`QueueTrigger`|`string`|Várólista-adattartalom (ha érvényes karakterlánc). Ha az üzenetsor-üzenet hasznos karakterlánc, `QueueTrigger` a *function. JSON*fájl `name` tulajdonsága által megnevezett változóval megegyező értékkel rendelkezik.|
|`DequeueCount`|`int`|Azon alkalmak száma, amikor az üzenet el lett küldve.|
|`ExpirationTime`|`DateTimeOffset`|Az az időpont, ameddig az üzenet lejár.|
|`Id`|`string`|Üzenetsor-üzenet azonosítója.|
|`InsertionTime`|`DateTimeOffset`|Az az idő, ameddig az üzenet hozzá lett adva a várólistához.|
|`NextVisibleTime`|`DateTimeOffset`|Az az időpont, amikor a következő üzenet látható lesz.|
|`PopReceipt`|`string`|Az üzenet pop-nyugtája.|

## <a name="trigger---poison-messages"></a>Trigger – Megmérgező üzenetek

Ha a várólista-aktiválási függvény meghiúsul, Azure Functions újrapróbálkozik a függvényt egy adott üzenetsor-üzenetnél akár ötször is, az első próbálkozást is beleértve. Ha mind az öt kísérlet meghiúsul, a functions Runtime egy *&lt;originalqueuename >-Poison*nevű várólistába helyez egy üzenetet. Írhat egy függvényt, amely az üzenetek törlését végzi a méreg-várólistából úgy, hogy naplózza azokat, vagy értesítést küld, amely manuális beavatkozást igényel.

Ha manuálisan szeretné kezelni a Megmérgező üzeneteket, keresse meg az üzenetsor [dequeueCount](#trigger---message-metadata) .

## <a name="trigger---polling-algorithm"></a>Trigger – lekérdezési algoritmus

A várólista-trigger egy véletlenszerű exponenciális visszakapcsolási algoritmust valósít meg, amely csökkenti az üresjárati üzenetsor lekérdezésének hatását a tárolási tranzakciós költségekre.

Az algoritmus a következő logikát használja:

- Ha a rendszer egy üzenetet talál, a futtatókörnyezet két másodpercet vár, majd egy másik üzenetet keres.
- Ha nem talál üzenetet, a rendszer körülbelül négy másodpercig vár, mielőtt újra próbálkozik.
- A várakozási sor üzenetének későbbi sikertelen próbálkozásai után a várakozási idő továbbra is növekszik, amíg el nem éri a maximális várakozási időt, ami alapértelmezés szerint egy percet mutat.
- A maximális várakozási idő a [Host. JSON fájl](functions-host-json.md#queues)`maxPollingInterval` tulajdonságán keresztül konfigurálható.

Helyi fejlesztés esetén a maximális lekérdezési időköz alapértelmezett értéke két másodperc.

A számlázást illetően a futtatókörnyezet által végzett lekérdezés "ingyenes", és nem számít bele a fiókjába.

## <a name="trigger---concurrency"></a>Trigger – Egyidejűség

Ha a várólista-üzenetek több várólistára várnak, a várólista-trigger lekéri az üzenetek kötegét, és egyidejűleg hívja meg a függvények feldolgozását. Alapértelmezés szerint a köteg mérete 16. Ha a feldolgozás alatt álló szám 8, a futtatókörnyezet lekéri egy másik köteget, és elindítja az üzenetek feldolgozását. Így az egyazon virtuális gépeken (VM-ben) végrehajtott, egyidejű feldolgozás alatt álló üzenetek maximális száma 24. Ez a korlát külön vonatkozik az egyes virtuális gépeken futó minden egyes üzenetsor által aktivált függvényre. Ha a függvény alkalmazása több virtuális gépre is méretezhető, minden virtuális gép megvárja az eseményindítókat, és megkísérli futtatni a függvényeket. Ha például egy függvény alkalmazás 3 virtuális gépre van kibővítve, az egy üzenetsor által aktivált függvény egyidejű példányainak alapértelmezett maximális száma 72.

A köteg mérete és az új köteg beolvasásának küszöbértéke a [Host. JSON fájlban](functions-host-json.md#queues)állítható be. Ha a függvény alkalmazásban a várólista által aktivált függvények párhuzamos végrehajtását szeretné csökkenteni, beállíthatja a Batch méretét 1-re. Ez a beállítás csak akkor teszi feleslegessé a párhuzamosságot, ha a Function alkalmazás egyetlen virtuális gépen (VM) fut. 

A várólista-trigger automatikusan megakadályozza, hogy a függvény többször dolgozza fel a várólista-üzeneteket; a függvények nem írhatók idempotens.

## <a name="trigger---hostjson-properties"></a>Trigger-Host. JSON tulajdonságok

A [Host. JSON](functions-host-json.md#queues) fájl olyan beállításokat tartalmaz, amelyek vezérlik a várólista-trigger működését. A rendelkezésre álló beállításokkal kapcsolatos részletekért tekintse meg a [Host. JSON-beállítások](#hostjson-settings) szakaszt.

## <a name="output"></a>Kimenet

Az Azure üzenetsor-tároló kimeneti kötésével üzeneteket írhat a várólistákba.

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

Az alábbi példa egy [ C# függvényt](functions-dotnet-class-library.md) mutat be, amely létrehoz egy üzenetsor-üzenetet minden fogadott http-kérelemhez.

```csharp
[StorageAccount("MyStorageConnectionAppSetting")]
public static class QueueFunctions
{
    [FunctionName("QueueOutput")]
    [return: Queue("myqueue-items")]
    public static string QueueOutput([HttpTrigger] dynamic input,  ILogger log)
    {
        log.LogInformation($"C# function processed: {input.Text}");
        return input.Text;
    }
}
```

# <a name="c-scripttabcsharp-script"></a>[C#Parancsfájl](#tab/csharp-script)

Az alábbi példa egy http-trigger kötést mutat be egy *function. JSON* fájlban és [ C# parancsfájlban (. CSX)](functions-reference-csharp.md) , amely a kötést használja. A függvény egy **CustomQueueMessage** objektum-adattartalommal rendelkező üzenetsor-objektumot hoz létre minden fogadott http-kérelemhez.

Itt látható a *function. JSON* fájl:

```json
{
  "bindings": [
    {
      "type": "httpTrigger",
      "direction": "in",
      "authLevel": "function",
      "name": "input"
    },
    {
      "type": "http",
      "direction": "out",
      "name": "$return"
    },
    {
      "type": "queue",
      "direction": "out",
      "name": "$return",
      "queueName": "outqueue",
      "connection": "MyStorageConnectionAppSetting"
    }
  ]
}
```

A [konfigurációs](#output---configuration) szakasz ezeket a tulajdonságokat ismerteti.

Az alábbi C# parancsfájl-kód létrehoz egy üzenetsor-üzenetet:

```cs
public class CustomQueueMessage
{
    public string PersonName { get; set; }
    public string Title { get; set; }
}

public static CustomQueueMessage Run(CustomQueueMessage input, ILogger log)
{
    return input;
}
```

`ICollector` vagy `IAsyncCollector` paraméter használatával egyszerre több üzenetet is küldhet. Itt van C# egy parancsfájl-kód, amely több üzenetet küld, egyet a HTTP-kérelem adatait, egy pedig egy rögzített értéket:

```cs
public static void Run(
    CustomQueueMessage input, 
    ICollector<CustomQueueMessage> myQueueItems, 
    ILogger log)
{
    myQueueItems.Add(input);
    myQueueItems.Add(new CustomQueueMessage { PersonName = "You", Title = "None" });
}
```

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

Az alábbi példa egy HTTP-trigger kötést mutat be egy *function. JSON* fájlban, valamint egy [JavaScript-függvényt](functions-reference-node.md) , amely a kötést használja. A függvény minden fogadott HTTP-kérelemhez létrehoz egy üzenetsor-tételt.

Itt látható a *function. JSON* fájl:

```json
{
  "bindings": [
    {
      "type": "httpTrigger",
      "direction": "in",
      "authLevel": "function",
      "name": "input"
    },
    {
      "type": "http",
      "direction": "out",
      "name": "$return"
    },
    {
      "type": "queue",
      "direction": "out",
      "name": "$return",
      "queueName": "outqueue",
      "connection": "MyStorageConnectionAppSetting"
    }
  ]
}
```

A [konfigurációs](#output---configuration) szakasz ezeket a tulajdonságokat ismerteti.

Itt látható a JavaScript-kód:

```javascript
module.exports = function (context, input) {
    context.done(null, input.body);
};
```

Egyszerre több üzenetet is küldhet, ha az `myQueueItem` kimeneti kötéshez definiál egy üzenet tömböt. A következő JavaScript-kód két üzenetsor-üzenetet küld az egyes fogadott HTTP-kérelmekhez rögzített értékekkel.

```javascript
module.exports = function(context) {
    context.bindings.myQueueItem = ["message 1","message 2"];
    context.done();
};
```

# <a name="pythontabpython"></a>[Python](#tab/python)

Az alábbi példa bemutatja, hogyan lehet egy és több értéket kiadni a tárolási várólistáknak. A *function. JSON* fájlhoz szükséges konfiguráció ugyanaz, mint az egyik módja.

A Storage-várólista kötése a *function. JSON* fájlban van definiálva, ahol a *Type* értéke `queue`.

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
      "type": "queue",
      "direction": "out",
      "name": "msg",
      "queueName": "outqueue",
      "connection": "AzureStorageQueuesConnectionString"
    }
  ]
}
```

Ha egyéni üzenetet szeretne beállítani a várólistán, egyetlen értéket kell átadnia a `set` metódusnak.

```python
import azure.functions as func

def main(req: func.HttpRequest, msg: func.Out[str]) -> func.HttpResponse:

    input_msg = req.params.get('message')

    msg.set(input_msg)

    return 'OK'
```

Ha több üzenetet szeretne létrehozni a várólistán, deklaráljon egy paramétert a megfelelő lista típusaként, és adjon át egy értékeket tartalmazó tömböt (amely megfelel a lista típusának) a `set` metódusnak.

```python
import azure.functions as func
import typing

def main(req: func.HttpRequest, msg: func.Out[typing.List[str]]) -> func.HttpResponse:

    msg.set(['one', 'two'])

    return 'OK'
```

# <a name="javatabjava"></a>[Java](#tab/java)

 Az alábbi példa egy Java-függvényt mutat be, amely üzenetsor-üzenetet hoz létre a HTTP-kérések indításakor.

```java
@FunctionName("httpToQueue")
@QueueOutput(name = "item", queueName = "myqueue-items", connection = "MyStorageConnectionAppSetting")
 public String pushToQueue(
     @HttpTrigger(name = "request", methods = {HttpMethod.POST}, authLevel = AuthorizationLevel.ANONYMOUS)
     final String message,
     @HttpOutput(name = "response") final OutputBinding<String> result) {
       result.setValue(message + " has been added.");
       return message;
 }
```

A [Java functions runtime library](/java/api/overview/azure/functions/runtime)-ben használja a `@QueueOutput` megjegyzéseket azon paramétereknél, amelyek értékét a rendszer a várólista-tárolóba írja.  A paraméter típusának `OutputBinding<T>`nak kell lennie, ahol a `T` egy POJO natív Java-típusa.

---

## <a name="output---attributes-and-annotations"></a>Kimenet – attribútumok és jegyzetek

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

Az [ C# osztályok könyvtáraiban](functions-dotnet-class-library.md)használja a [QueueAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/QueueAttribute.cs).

Az attribútum egy `out` paraméterre vagy a függvény visszatérési értékére vonatkozik. Az attribútum konstruktora a várólista nevét veszi át, ahogy az az alábbi példában látható:

```csharp
[FunctionName("QueueOutput")]
[return: Queue("myqueue-items")]
public static string Run([HttpTrigger] dynamic input,  ILogger log)
{
    ...
}
```

A `Connection` tulajdonság beállításával megadhatja a használni kívánt Storage-fiókot, ahogy az az alábbi példában is látható:

```csharp
[FunctionName("QueueOutput")]
[return: Queue("myqueue-items", Connection = "StorageConnectionAppSetting")]
public static string Run([HttpTrigger] dynamic input,  ILogger log)
{
    ...
}
```

Teljes példa: [kimenet – C# példa](#output).

Az `StorageAccount` attribútummal megadhatja a Storage-fiókot osztály, metódus vagy paraméter szintjén. További információ: trigger-attributes.

# <a name="c-scripttabcsharp-script"></a>[C#Parancsfájl](#tab/csharp-script)

Az C# attribútumokat a parancsfájl nem támogatja.

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

A Java-parancsfájlok nem támogatják az attribútumokat.

# <a name="pythontabpython"></a>[Python](#tab/python)

A Python nem támogatja az attribútumokat.

# <a name="javatabjava"></a>[Java](#tab/java)

A `QueueOutput` jegyzet lehetővé teszi, hogy egy függvény kimenetének megírásával üzenetet írjon. Az alábbi példa egy HTTP által aktivált függvényt mutat be, amely üzenetsor-üzenetet hoz létre.

```java
package com.function;
import java.util.*;
import com.microsoft.azure.functions.annotation.*;
import com.microsoft.azure.functions.*;

public class HttpTriggerQueueOutput {
    @FunctionName("HttpTriggerQueueOutput")
    public HttpResponseMessage run(
            @HttpTrigger(name = "req", methods = {HttpMethod.GET, HttpMethod.POST}, authLevel = AuthorizationLevel.FUNCTION) HttpRequestMessage<Optional<String>> request,
            @QueueOutput(name = "message", queueName = "messages", connection = "MyStorageConnectionAppSetting") OutputBinding<String> message,
            final ExecutionContext context) {

        message.setValue(request.getQueryParameters().get("name"));
        return request.createResponseBuilder(HttpStatus.OK).body("Done").build();
    }
}
```

| Tulajdonság    | Leírás |
|-------------|-----------------------------|
|`name`       | Deklarálja a paraméter nevét a függvény aláírásában. A függvény indításakor ennek a paraméternek az értéke az üzenetsor üzenetének tartalmát jeleníti meg. |
|`queueName`  | Deklarálja a várólista nevét a Storage-fiókban. |
|`connection` | A Storage-fiók kapcsolódási karakterláncára mutat. |

A `QueueOutput` jegyzethez társított paraméter a [OutputBinding\<t\>](https://github.com/Azure/azure-functions-java-library/blob/master/src/main/java/com/microsoft/azure/functions/OutputBinding.java) példányként van beírva.

---

## <a name="output---configuration"></a>Kimenet – konfiguráció

Az alábbi táblázat a *function. JSON* fájlban és a `Queue` attribútumban beállított kötési konfigurációs tulajdonságokat ismerteti.

|function. JSON-tulajdonság | Attribútum tulajdonsága |Leírás|
|---------|---------|----------------------|
|**type** | – | `queue`értékre kell állítani. Ez a tulajdonság automatikusan be van állítva, amikor létrehozza az triggert a Azure Portalban.|
|**direction** | – | `out`értékre kell állítani. Ez a tulajdonság automatikusan be van állítva, amikor létrehozza az triggert a Azure Portalban. |
|**név** | – | Annak a változónak a neve, amely a függvény kódjában a várólistát jelképezi. A függvény visszatérési értékének hivatkozásához állítsa a `$return` értéket.|
|**queueName** |**QueueName** | A várólista neve. |
|**kapcsolat** | **Kapcsolat** |Egy olyan Alkalmazásbeállítás neve, amely a kötéshez használandó tárolási kapcsolati karakterláncot tartalmazza. Ha az Alkalmazásbeállítások neve "AzureWebJobs" előtaggal kezdődik, akkor itt csak a nevet adja meg. Ha például a `connection` "MyStorage" értékre állítja, a functions futtatókörnyezet egy "MyStorage" nevű alkalmazás-beállítást keres. Ha üresen hagyja a `connection`, a functions futtatókörnyezet az alapértelmezett Storage-kapcsolatok karakterláncot használja az `AzureWebJobsStorage`nevű alkalmazás-beállításban.|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="output---usage"></a>Kimenet – használat

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

Egyetlen üzenetsor-üzenetet írhat egy metódus-paraméter (például `out T paramName`) használatával. `out` paraméter helyett a metódus visszatérési típusát is használhatja, és a `T` a következő típusok bármelyike lehet:

* Egy, JSON-ként szerializálható objektum
* `string`
* `byte[]`
* [CloudQueueMessage] 

Ha `CloudQueueMessage`hoz próbál kötni, és hibaüzenetet kap, ellenőrizze, hogy rendelkezik-e [a megfelelő Storage SDK-verzióra](#azure-storage-sdk-version-in-functions-1x)mutató hivatkozással.

A C# és C# a parancsfájlban több üzenetsor-üzenetet is írhat a következő típusok egyikének használatával: 

* `ICollector<T>` vagy `IAsyncCollector<T>`
* [CloudQueue](/dotnet/api/microsoft.azure.storage.queue.cloudqueue)

# <a name="c-scripttabcsharp-script"></a>[C#Parancsfájl](#tab/csharp-script)

Egyetlen üzenetsor-üzenetet írhat egy metódus-paraméter (például `out T paramName`) használatával. A `paramName` a *function. json*`name` tulajdonságában megadott érték. `out` paraméter helyett a metódus visszatérési típusát is használhatja, és a `T` a következő típusok bármelyike lehet:

* Egy, JSON-ként szerializálható objektum
* `string`
* `byte[]`
* [CloudQueueMessage] 

Ha `CloudQueueMessage`hoz próbál kötni, és hibaüzenetet kap, ellenőrizze, hogy rendelkezik-e [a megfelelő Storage SDK-verzióra](#azure-storage-sdk-version-in-functions-1x)mutató hivatkozással.

A C# és C# a parancsfájlban több üzenetsor-üzenetet is írhat a következő típusok egyikének használatával: 

* `ICollector<T>` vagy `IAsyncCollector<T>`
* [CloudQueue](/dotnet/api/microsoft.azure.storage.queue.cloudqueue)

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

A kimeneti várólista elem `context.bindings.<NAME>`on keresztül érhető el, ahol a `<NAME>` megegyezik a *function. JSON*fájlban megadott névvel. A várólista-elem hasznos adatainak karakterláncot vagy JSON-szerializálható objektumot is használhat.

# <a name="pythontabpython"></a>[Python](#tab/python)

Az Event hub-üzenetek egy függvényből való üzembe helyezésének két lehetősége van:

- **Visszatérési érték**: állítsa be az `name` tulajdonságot a *function. json* fájlban a `$return`. Ezzel a konfigurációval a függvény visszatérési értéke üzenetsor-tárolási üzenetként is megmarad.

- **Elengedhetetlen**: adjon meg egy értéket a [set](https://docs.microsoft.com/python/api/azure-functions/azure.functions.out?view=azure-python#set-val--t-----none) metódusnak, amely [kimenő](https://docs.microsoft.com/python/api/azure-functions/azure.functions.out?view=azure-python) típusként van deklarálva. Az `set`nek átadott értéket üzenetsor-tárolóként megőrzi a rendszer.

# <a name="javatabjava"></a>[Java](#tab/java)

Az Event hub-üzenetek egy függvényből való üzembe helyezésére két lehetőség áll rendelkezésre a [QueueOutput](https://docs.microsoft.com/java/api/com.microsoft.azure.functions.annotation.queueoutput) jegyzet használatával:

- Visszaadott **érték**: Ha a jegyzetet a függvényhez alkalmazza, a függvény visszatérési értéke Event hub-üzenetként marad.

- **Fontos**: Ha explicit módon be szeretné állítani az üzenet értékét, alkalmazza a jegyzetet a [`OutputBinding<T>`](https://docs.microsoft.com/java/api/com.microsoft.azure.functions.OutputBinding)típusú adott paraméterre, ahol a `T` POJO vagy bármely natív Java-típus. Ezzel a konfigurációval a `setValue` metódus értékének átadásával az érték az Event hub üzenete marad.

---

## <a name="exceptions-and-return-codes"></a>Kivételek és visszatérési kódok

| Kötés |  Leírások |
|---|---|
| Queue | [Üzenetsor-hibakódok](https://docs.microsoft.com/rest/api/storageservices/queue-service-error-codes) |
| BLOB, tábla, üzenetsor | [Tárolási hibakódok](https://docs.microsoft.com/rest/api/storageservices/fileservices/common-rest-api-error-codes) |
| BLOB, tábla, üzenetsor |  [hibaelhárítással](https://docs.microsoft.com/rest/api/storageservices/fileservices/troubleshooting-api-operations) |

<a name="host-json"></a>  

## <a name="hostjson-settings"></a>gazdagép. JSON-beállítások

Ez a szakasz a kötéshez elérhető globális konfigurációs beállításokat ismerteti 2. x vagy újabb verziókban. A következő példa a Host. JSON fájlt tartalmazza, csak a 2. x + beállításokat a kötéshez. További információ a 2. x verzióban található globális konfigurációs beállításokról: a [Host. JSON dokumentációja Azure functions](functions-host-json.md).

> [!NOTE]
> Az 1. x függvények Host. JSON fájljának hivatkozását lásd: [Host. JSON-dokumentáció Azure functions 1. x-hez](functions-host-json-v1.md).

```json
{
    "version": "2.0",
    "extensions": {
        "queues": {
            "maxPollingInterval": "00:00:02",
            "visibilityTimeout" : "00:00:30",
            "batchSize": 16,
            "maxDequeueCount": 5,
            "newBatchThreshold": 8
        }
    }
}
```

|Tulajdonság  |Alapértelmezett | Leírás |
|---------|---------|---------|
|maxPollingInterval|00:00:01|A várólista-lekérdezések közötti maximális időköz. Minimum 00:00:00.100 (100 MS), és legfeljebb 00:01:00 (1 perc) értékkel növekszik.  Az 1. x adattípusa ezredmásodperc, a 2. x és a magasabb érték pedig egy TimeSpan.|
|visibilityTimeout|00:00:00|Az újrapróbálkozások között eltelt idő az üzenet feldolgozásakor. |
|batchSize|16|Azoknak a üzenetsor-üzeneteknek a száma, amelyeket a függvények futtatókörnyezete egyszerre kér le, és párhuzamosan dolgozza fel a folyamatokat. Amikor a feldolgozott szám lezajlik a `newBatchThreshold`, a futtatókörnyezet egy másik köteget kap, és elindítja az üzenetek feldolgozását. Így a függvények által feldolgozott egyidejű üzenetek maximális száma `batchSize` plusz `newBatchThreshold`. Ez a korlát külön vonatkozik az egyes üzenetsor-vezérelt függvényekre. <br><br>Ha el szeretné kerülni a párhuzamos végrehajtást az egyik várólistán fogadott üzenetek esetében, beállíthatja a `batchSize` 1 értékre. Ez a beállítás azonban csak akkor teszi feleslegessé a párhuzamosságot, ha a Function alkalmazás egyetlen virtuális gépen fut (VM). Ha a Function alkalmazás több virtuális gépre is kiterjed, minden egyes virtuális gép futtathatja az egyes üzenetsor által aktivált függvények egy példányát.<br><br>A maximális `batchSize` 32. |
|maxDequeueCount|5|Azon alkalmak száma, amelyekkel az üzenetek feldolgozására kerül sor, mielőtt a rendszer áthelyezi azt a Megmérgező várólistára.|
|newBatchThreshold|batchSize/2|Ha az egyidejűleg feldolgozható üzenetek száma leállítja ezt a számot, a futtatókörnyezet egy másik köteget kérdez le.|

## <a name="next-steps"></a>Következő lépések

* [További információ az Azure functions-eseményindítók és-kötésekről](functions-triggers-bindings.md)

<!--
> [!div class="nextstepaction"]
> [Go to a quickstart that uses a Queue storage trigger](functions-create-storage-queue-triggered-function.md)
-->

> [!div class="nextstepaction"]
> [Ugrás egy üzenetsor-tároló kimeneti kötést használó oktatóanyagra](functions-integrate-storage-queue-output-binding.md)

<!-- LINKS -->

[CloudQueueMessage]: /dotnet/api/microsoft.azure.storage.queue.cloudqueuemessage
