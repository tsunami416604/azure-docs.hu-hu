---
title: Azure üzenetsor-tárolási kötések Azure Functionshoz
description: Ismerje meg, hogyan használható az Azure üzenetsor-tároló-trigger és a kimeneti kötés a Azure Functionsban.
services: functions
documentationcenter: na
author: craigshoemaker
manager: gwallace
keywords: az Azure functions, függvények, eseményfeldolgozás, dinamikus számítás, kiszolgáló nélküli architektúra
ms.service: azure-functions
ms.devlang: multiple
ms.topic: reference
ms.date: 09/03/2018
ms.author: cshoe
ms.custom: cc996988-fb4f-47
ms.openlocfilehash: bf5219f8e147baba0e89a8c0e1fa6cb7b371473c
ms.sourcegitcommit: 4b5dcdcd80860764e291f18de081a41753946ec9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/03/2019
ms.locfileid: "68774749"
---
# <a name="azure-queue-storage-bindings-for-azure-functions"></a>Azure üzenetsor-tárolási kötések Azure Functionshoz

Ez a cikk azt ismerteti, hogyan használható az Azure üzenetsor-tárolási kötések Azure Functions. Azure Functions támogatja a várólisták trigger-és kimeneti kötéseit.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="packages---functions-1x"></a>Csomagok – 1.x függvények

A várólista-tárolási kötések a [Microsoft. Azure. webjobs](https://www.nuget.org/packages/Microsoft.Azure.WebJobs) NuGet csomagban, 2. x verzióban találhatók. A csomag forráskódja a [azure-webjobs-sdk](https://github.com/Azure/azure-webjobs-sdk/tree/v2.x/src/Microsoft.Azure.WebJobs.Storage/Queue) GitHub-adattárban.

[!INCLUDE [functions-package-auto](../../includes/functions-package-auto.md)]

[!INCLUDE [functions-storage-sdk-version](../../includes/functions-storage-sdk-version.md)]

## <a name="packages---functions-2x"></a>Csomagok – 2.x függvények

A várólista-tárolási kötések a [Microsoft. Azure. webjobs. Extensions. Storage](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.Storage) NuGet csomag 3. x verziójában érhetők el. A csomag forráskódja a [azure-webjobs-sdk](https://github.com/Azure/azure-webjobs-sdk/tree/dev/src/Microsoft.Azure.WebJobs.Extensions.Storage/Queues) GitHub-adattárban.

[!INCLUDE [functions-package-v2](../../includes/functions-package-v2.md)]

## <a name="encoding"></a>Encoding
A függvények *Base64* kódolású karakterláncot várnak. A kódolási típus összes módosítását (az adatgyűjtés *Base64* kódolású karakterláncként való előkészítéséhez) a hívó szolgáltatásban kell végrehajtani.

## <a name="trigger"></a>Eseményindító

A várólista-trigger használatával elindíthat egy függvényt, ha új elem érkezik egy várólistán. A várólista-üzenet bemenetként van megadva a függvénynek.

## <a name="trigger---example"></a>Az eseményindító – példa

Tekintse meg az adott nyelvű példa:

* [C#](#trigger---c-example)
* [C# script (.csx)](#trigger---c-script-example)
* [JavaScript](#trigger---javascript-example)
* [Java](#trigger---java-example)
* [Python](#trigger---python-example)

### <a name="trigger---c-example"></a>Eseményindító - C#-példa

Az alábbi példa egy olyan [ C# függvényt](functions-dotnet-class-library.md) mutat be, `myqueue-items` amely lekérdezi a várólistát, és minden alkalommal beírja a naplót, amikor a várólista-elemek feldolgozása történik

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

### <a name="trigger---c-script-example"></a>Eseményindító - C#-szkript példa

Az alábbi példa egy üzenetsor-trigger kötést mutat be egy *function. JSON* fájlban és [ C# a kötést használó script (. CSX)](functions-reference-csharp.md) kódban. A függvény lekérdezi `myqueue-items` a várólistát, és minden alkalommal beírja a naplót, amikor feldolgozzák a várólista-elemeket.

Íme a *function.json* fájlt:

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

A [konfigurációs](#trigger---configuration) szakasz mutatja be ezeket a tulajdonságokat.

Íme a C#-szkriptkódot:

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

A [használat](#trigger---usage) szakasz ismerteti `myQueueItem`, amelyet a `name` function. JSON nevű tulajdonsága nevez el.  Az [üzenet metaadatainak szakasza](#trigger---message-metadata) a többi megjelenített változót ismerteti.

### <a name="trigger---javascript-example"></a>Eseményindító - JavaScript-példa

Az alábbi példa egy várólista-trigger kötését mutatja be egy *function. JSON* fájlban, valamint egy [JavaScript](functions-reference-node.md) -függvényt, amely a kötést használja. A függvény lekérdezi `myqueue-items` a várólistát, és minden alkalommal beírja a naplót, amikor feldolgozzák a várólista-elemeket.

Íme a *function.json* fájlt:

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

A [konfigurációs](#trigger---configuration) szakasz mutatja be ezeket a tulajdonságokat.

> [!NOTE]
> A name paraméter azt a `context.bindings.<name>` JavaScript-kódot tükrözi, amely tartalmazza a várólista-elemek hasznos adatait. Ezt a hasznos adatot a függvény második paramétereként is átadja.

A következő JavaScript-kódot:

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

A [használat](#trigger---usage) szakasz ismerteti `myQueueItem`, amelyet a `name` function. JSON nevű tulajdonsága nevez el.  Az [üzenet metaadatainak szakasza](#trigger---message-metadata) a többi megjelenített változót ismerteti.

### <a name="trigger---java-example"></a>Eseményindító - Java-példában

A következő Java-példa egy Storage üzenetsor-kiváltó függvényt mutat be, amely naplózza az `myqueuename`aktivált üzenetet a várólistára helyezve.

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

### <a name="trigger---python-example"></a>Trigger – Python-példa

Az alábbi példa azt szemlélteti, hogyan lehet beolvasni egy függvénynek egy trigger használatával átadott üzenetsor-üzenetet.

A Storage-várólista triggere a *function. JSON* fájlban van definiálva, `queueTrigger`ahol a Type érték van beállítva.

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

Az `func.ServiceBusMessage`  *_init \__ .a.aCodeegyparamétertdeklarál,amelylehetővéteszi,hogybeolvassaazüzenetsor-üzenetet\_* a függvényben.

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

## <a name="trigger---attributes"></a>Eseményindító - attribútumok

Az [ C# osztályok könyvtáraiban](functions-dotnet-class-library.md)használja a következő attribútumokat az üzenetsor-trigger konfigurálásához:

* [QueueTriggerAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/QueueTriggerAttribute.cs)

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

  A `Connection` tulajdonság beállításával megadhatja a használni kívánt Storage-fiókot, ahogy az az alábbi példában is látható:

  ```csharp
  [FunctionName("QueueTrigger")]
  public static void Run(
      [QueueTrigger("myqueue-items", Connection = "StorageConnectionAppSetting")] string myQueueItem, 
      ILogger log)
  {
      ....
  }
  ```

  Egy teljes példa: [eseményindító – C#-példa](#trigger---c-example).

* [StorageAccountAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/StorageAccountAttribute.cs)

  Egy másik módszert biztosít a használni kívánt Storage-fiók megadására. A konstruktor egy olyan Alkalmazásbeállítás nevét veszi fel, amely egy tárolási kapcsolatot tartalmazó karakterláncot tartalmaz. Az attribútum a paramétert, a metódus vagy az osztály szintjén alkalmazhatók. Az alábbi példa bemutatja az osztály és metódust:

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

* A `QueueTrigger` attribútum `Connection` tulajdonság.
* A `StorageAccount` a paramétert, amelyek azonos, alkalmazott attribútum a `QueueTrigger` attribútum.
* A `StorageAccount` attribútum a függvény a alkalmazni.
* A `StorageAccount` attribútum az osztály a alkalmazni.
* A "AzureWebJobsStorage" alkalmazás beállításai.

## <a name="trigger---configuration"></a>Eseményindító - konfiguráció

A következő táblázat ismerteti a megadott kötés konfigurációs tulajdonságaiban a *function.json* fájlt, és a `QueueTrigger` attribútum.

|Function.JSON tulajdonság | Attribútum tulajdonsága |Leírás|
|---------|---------|----------------------|
|**type** | n/a| Meg kell `queueTrigger`. Ez a tulajdonság beállítása automatikusan történik, ha az eseményindítót fog létrehozni az Azure Portalon.|
|**direction**| n/a | Csak a *function. JSON* fájlban. Meg kell `in`. Ez a tulajdonság beállítása automatikusan történik, ha az eseményindítót fog létrehozni az Azure Portalon. |
|**name** | n/a |Annak a változónak a neve, amely a függvény kódjában található üzenetsor-elemek tartalmát tartalmazza.  |
|**queueName** | **queueName**| A lekérdezni kívánt várólista neve. |
|**kapcsolat** | **kapcsolat** |Egy olyan Alkalmazásbeállítás neve, amely a kötéshez használandó tárolási kapcsolati karakterláncot tartalmazza. Ha az Alkalmazásbeállítások neve "AzureWebJobs" előtaggal kezdődik, akkor itt csak a nevet adja meg. Ha például a "MyStorage" `connection` értékre van állítva, a functions futtatókörnyezet egy "AzureWebJobsMyStorage" nevű alkalmazás-beállítást keres. Ha üresen `connection` hagyja, a functions futtatókörnyezet az alapértelmezett tárolási kapcsolatok karakterláncát használja a nevű `AzureWebJobsStorage`alkalmazás-beállításban.|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="trigger---usage"></a>Eseményindító - használat

A C# ( C# z `string paramName`) és a (z) parancsfájlban a (z) metódus paraméterének használatával férhet hozzá az üzenet adataihoz. A C# szkriptben `paramName` a *függvény. JSON* `name` tulajdonságában megadott érték. A következő típusokhoz köthető:

* Objektum – a függvények futtatókörnyezete deszerializál egy JSON-adattartalmat a kódban definiált tetszőleges osztály egy példányára. 
* `string`
* `byte[]`
* [CloudQueueMessage]

Ha egy hibaüzenetet próbál meg `CloudQueueMessage` kötni, és hibaüzenetet kap, ellenőrizze, hogy rendelkezik-e [a megfelelő Storage SDK](#azure-storage-sdk-version-in-functions-1x)-verzióra mutató hivatkozással.

A JavaScriptben a `context.bindings.<name>` használatával érheti el az üzenetsor-elem hasznos adatait. Ha a hasznos adat JSON, deszerializálja egy objektumba.

## <a name="trigger---message-metadata"></a>Eseményindító - üzenet metaadatok

A várólista-trigger számos [metaadat](./functions-bindings-expressions-patterns.md#trigger-metadata)-tulajdonságot biztosít. Ezek a tulajdonságok a kötési kifejezésekben való használata más kötések részeként vagy a kód paramétereiben használható. Ezek a [CloudQueueMessage](https://docs.microsoft.com/dotnet/api/microsoft.azure.storage.queue.cloudqueuemessage) osztály tulajdonságai.

|Tulajdonság|Típus|Leírás|
|--------|----|-----------|
|`QueueTrigger`|`string`|Várólista-adattartalom (ha érvényes karakterlánc). Ha az üzenetsor üzenete sztringként van feldolgozva, `QueueTrigger` ugyanazzal az értékkel rendelkezik, mint a *function. JSON*fájlban található `name` tulajdonság által megnevezett változó.|
|`DequeueCount`|`int`|Azon alkalmak száma, amikor az üzenet el lett küldve.|
|`ExpirationTime`|`DateTimeOffset`|Az az időpont, ameddig az üzenet lejár.|
|`Id`|`string`|Üzenetsor-üzenet azonosítója.|
|`InsertionTime`|`DateTimeOffset`|Az az idő, ameddig az üzenet hozzá lett adva a várólistához.|
|`NextVisibleTime`|`DateTimeOffset`|Az az időpont, amikor a következő üzenet látható lesz.|
|`PopReceipt`|`string`|Az üzenet pop-nyugtája.|

## <a name="trigger---poison-messages"></a>Eseményindító - ártalmas üzenetek

Ha a várólista-aktiválási függvény meghiúsul, Azure Functions újrapróbálkozik a függvényt egy adott üzenetsor-üzenetnél akár ötször is, az első próbálkozást is beleértve. Ha mind az öt kísérlet meghiúsul, a functions Runtime egy  *&lt;originalqueuename >-Poison*nevű várólistába helyez egy üzenetet. Írhat egy függvényt, amely az üzenetek törlését végzi a méreg-várólistából úgy, hogy naplózza azokat, vagy értesítést küld, amely manuális beavatkozást igényel.

Ha manuálisan szeretné kezelni a Megmérgező üzeneteket, keresse meg az üzenetsor [dequeueCount](#trigger---message-metadata) .

## <a name="trigger---polling-algorithm"></a>Trigger – lekérdezési algoritmus

A várólista-trigger egy véletlenszerű exponenciális visszakapcsolási algoritmust valósít meg, amely csökkenti az üresjárati üzenetsor lekérdezésének hatását a tárolási tranzakciós költségekre.  Ha a rendszer egy üzenetet talál, a futtatókörnyezet két másodpercet vár, majd egy másik üzenetet keres. Ha nem talál üzenetet, a rendszer körülbelül négy másodpercig vár, mielőtt újra próbálkozik. A várakozási sor üzenetének későbbi sikertelen próbálkozásai után a várakozási idő továbbra is növekszik, amíg el nem éri a maximális várakozási időt, ami alapértelmezés szerint egy percet mutat. A maximális várakozási idő a `maxPollingInterval` [Host. JSON fájlban](functions-host-json.md#queues)található tulajdonságon keresztül konfigurálható.

## <a name="trigger---concurrency"></a>Trigger – Egyidejűség

Ha a várólista-üzenetek több várólistára várnak, a várólista-trigger lekéri az üzenetek kötegét, és egyidejűleg hívja meg a függvények feldolgozását. Alapértelmezés szerint a köteg mérete 16. Ha a feldolgozás alatt álló szám 8, a futtatókörnyezet lekéri egy másik köteget, és elindítja az üzenetek feldolgozását. Így az egyazon virtuális gépeken (VM-ben) végrehajtott, egyidejű feldolgozás alatt álló üzenetek maximális száma 24. Ez a korlát külön vonatkozik az egyes virtuális gépeken futó minden egyes üzenetsor által aktivált függvényre. Ha a függvény alkalmazása több virtuális gépre is méretezhető, minden virtuális gép megvárja az eseményindítókat, és megkísérli futtatni a függvényeket. Ha például egy függvény alkalmazás 3 virtuális gépre van kibővítve, az egy üzenetsor által aktivált függvény egyidejű példányainak alapértelmezett maximális száma 72.

A köteg mérete és az új köteg beolvasásának küszöbértéke a [Host. JSON fájlban](functions-host-json.md#queues)állítható be. Ha a függvény alkalmazásban a várólista által aktivált függvények párhuzamos végrehajtását szeretné csökkenteni, beállíthatja a Batch méretét 1-re. Ez a beállítás csak akkor teszi feleslegessé a párhuzamosságot, ha a Function alkalmazás egyetlen virtuális gépen (VM) fut. 

A várólista-trigger automatikusan megakadályozza, hogy a függvény többször dolgozza fel a várólista-üzeneteket; a függvények nem írhatók idempotens.

## <a name="trigger---hostjson-properties"></a>Eseményindító - host.json tulajdonságai

A [Host. JSON](functions-host-json.md#queues) fájl olyan beállításokat tartalmaz, amelyek vezérlik a várólista-trigger működését. A rendelkezésre álló beállításokkal kapcsolatos részletekért tekintse meg a [Host. JSON-beállítások](#hostjson-settings) szakaszt.

## <a name="output"></a>Output

Az Azure üzenetsor-tároló kimeneti kötésével üzeneteket írhat a várólistákba.

## <a name="output---example"></a>Kimenete – példa

Tekintse meg az adott nyelvű példa:

* [C#](#output---c-example)
* [C# script (.csx)](#output---c-script-example)
* [JavaScript](#output---javascript-example)
* [Java](#output---java-example)
* [Python](#output---python-example)

### <a name="output---c-example"></a>Kimenet – C#-példa

Az alábbi példa egy [ C# függvényt](functions-dotnet-class-library.md) mutat be, amely létrehoz egy üzenetsor-üzenetet minden fogadott http-kérelemhez.

```csharp
[StorageAccount("AzureWebJobsStorage")]
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

### <a name="output---c-script-example"></a>Kimenet – C#-szkript példa

Az alábbi példa egy http-trigger kötést mutat be egy *function. JSON* fájlban és [ C# parancsfájlban (. CSX)](functions-reference-csharp.md) , amely a kötést használja. A függvény egy **CustomQueueMessage** objektum-adattartalommal rendelkező üzenetsor-objektumot hoz létre minden fogadott http-kérelemhez.

Íme a *function.json* fájlt:

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
      "name": "return"
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

A [konfigurációs](#output---configuration) szakasz mutatja be ezeket a tulajdonságokat.

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

Egy `ICollector` vagy`IAsyncCollector` paraméter használatával egyszerre több üzenetet is küldhet. Itt van C# egy parancsfájl-kód, amely több üzenetet küld, egyet a HTTP-kérelem adatait, egy pedig egy rögzített értéket:

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

### <a name="output---javascript-example"></a>Kimenet – JavaScript-példa

Az alábbi példa egy HTTP-trigger kötést mutat be egy *function. JSON* fájlban, valamint egy [JavaScript](functions-reference-node.md) -függvényt, amely a kötést használja. A függvény minden fogadott HTTP-kérelemhez létrehoz egy üzenetsor-tételt.

Íme a *function.json* fájlt:

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
      "name": "return"
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

A [konfigurációs](#output---configuration) szakasz mutatja be ezeket a tulajdonságokat.

A következő JavaScript-kódot:

```javascript
module.exports = function (context, input) {
    context.done(null, input.body);
};
```

Egyszerre több üzenetet is küldhet, ha a `myQueueItem` kimeneti kötéshez definiál egy üzenet tömböt. A következő JavaScript-kód két üzenetsor-üzenetet küld az egyes fogadott HTTP-kérelmekhez rögzített értékekkel.

```javascript
module.exports = function(context) {
    context.bindings.myQueueItem = ["message 1","message 2"];
    context.done();
};
```

### <a name="output---java-example"></a>Kimenet – Java-példában

 Az alábbi példa egy Java-függvényt mutat be, amely üzenetsor-üzenetet hoz létre a HTTP-kérések indításakor.

```java
@FunctionName("httpToQueue")
@QueueOutput(name = "item", queueName = "myqueue-items", connection = "AzureWebJobsStorage")
 public String pushToQueue(
     @HttpTrigger(name = "request", methods = {HttpMethod.POST}, authLevel = AuthorizationLevel.ANONYMOUS)
     final String message,
     @HttpOutput(name = "response") final OutputBinding&lt;String&gt; result) {
       result.setValue(message + " has been added.");
       return message;
 }
```

A [Java functions runtime library](/java/api/overview/azure/functions/runtime)-ben használja `@QueueOutput` a Megjegyzés azon paramétereket, amelyek értékét a várólista-tárolóba kívánja írni.  A paraméter típusa legyen `OutputBinding<T>`, ahol a T natív Java bármilyen egy pojo-vá.

### <a name="output---python-example"></a>Kimenet – Python-példa

Az alábbi példa bemutatja, hogyan lehet egy és több értéket kiadni a tárolási várólistáknak. A *function. JSON* fájlhoz szükséges konfiguráció ugyanaz, mint az egyik módja.

A Storage-várólista kötése a *function. JSON* fájlban van definiálva, `queue`ahol a Type érték van beállítva.

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

Ha több üzenetet szeretne létrehozni a várólistán, deklaráljon egy paramétert a megfelelő lista típusaként, és adjon át egy értékeket tartalmazó tömböt (amely megfelel a `set` lista típusának) a metódusnak.

```python
import azure.functions as func
import typing

def main(req: func.HttpRequest, msg: func.Out[typing.List[str]]) -> func.HttpResponse:

    msg.set(['one', 'two'])

    return 'OK'
```

## <a name="output---attributes"></a>Kimenet – attribútumok

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

Egy teljes példa: [kimenet – C#-példa](#output---c-example).

`StorageAccount` Az attribútummal megadhatja a Storage-fiókot az osztály, a metódus vagy a paraméter szintjén. További információ: trigger-attributes.

## <a name="output---configuration"></a>Kimenete – konfiguráció

A következő táblázat ismerteti a megadott kötés konfigurációs tulajdonságaiban a *function.json* fájlt, és a `Queue` attribútum.

|Function.JSON tulajdonság | Attribútum tulajdonsága |Leírás|
|---------|---------|----------------------|
|**type** | n/a | Meg kell `queue`. Ez a tulajdonság beállítása automatikusan történik, ha az eseményindítót fog létrehozni az Azure Portalon.|
|**direction** | n/a | Meg kell `out`. Ez a tulajdonság beállítása automatikusan történik, ha az eseményindítót fog létrehozni az Azure Portalon. |
|**name** | n/a | Annak a változónak a neve, amely a függvény kódjában a várólistát jelképezi. `$return` Állítsa a értékre a függvény visszatérési értékének hivatkozásához.|
|**queueName** |**queueName** | Az üzenetsor neve. |
|**kapcsolat** | **kapcsolat** |Egy olyan Alkalmazásbeállítás neve, amely a kötéshez használandó tárolási kapcsolati karakterláncot tartalmazza. Ha az Alkalmazásbeállítások neve "AzureWebJobs" előtaggal kezdődik, akkor itt csak a nevet adja meg. Ha például a "MyStorage" `connection` értékre van állítva, a functions futtatókörnyezet egy "AzureWebJobsMyStorage" nevű alkalmazás-beállítást keres. Ha üresen `connection` hagyja, a functions futtatókörnyezet az alapértelmezett tárolási kapcsolatok karakterláncát használja a nevű `AzureWebJobsStorage`alkalmazás-beállításban.|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="output---usage"></a>Kimenet – használat

A C# és C# a parancsfájlban írjon egy üzenetsor-üzenetet egy metódus- `out T paramName`paraméter (például) használatával. A C# szkriptben `paramName` a *függvény. JSON* `name` tulajdonságában megadott érték. `out` Paraméter helyett a metódus visszatérési típusát is használhatja, és `T` a következő típusok bármelyike lehet:

* Egy, JSON-ként szerializálható objektum
* `string`
* `byte[]`
* [CloudQueueMessage] 

Ha egy hibaüzenetet próbál meg `CloudQueueMessage` kötni, és hibaüzenetet kap, ellenőrizze, hogy rendelkezik-e [a megfelelő Storage SDK](#azure-storage-sdk-version-in-functions-1x)-verzióra mutató hivatkozással.

A C# és C# a parancsfájlban több üzenetsor-üzenetet is írhat a következő típusok egyikének használatával: 

* `ICollector<T>` vagy `IAsyncCollector<T>`
* [CloudQueue](/dotnet/api/microsoft.azure.storage.queue.cloudqueue)

A JavaScript-függvényekben `context.bindings.<name>` a használatával érheti el a kimeneti üzenetsor-üzenetet. A várólista-elem hasznos adatainak karakterláncot vagy JSON-szerializálható objektumot is használhat.


## <a name="exceptions-and-return-codes"></a>Kivételek és a visszatérési kódok

| Kötés |  Hivatkozás |
|---|---|
| Várólista | [Üzenetsor-hibakódok](https://docs.microsoft.com/rest/api/storageservices/queue-service-error-codes) |
| Blob, Table, Queue | [Tárolási hibakódok](https://docs.microsoft.com/rest/api/storageservices/fileservices/common-rest-api-error-codes) |
| Blob, Table, Queue |  [Hibaelhárítás](https://docs.microsoft.com/rest/api/storageservices/fileservices/troubleshooting-api-operations) |

<a name="host-json"></a>  

## <a name="hostjson-settings"></a>Host.JSON-beállítások

Ez a szakasz ismerteti a globális konfigurációs beállításoknak a kötéshez verziójában elérhető 2.x. Az alábbi példa host.json-fájl csak a verzió 2.x beállításait tartalmazza ezt a kötést. További információ a globális konfigurációs beállításoknak verzióban 2.x verzióját, lásd: [verzióját az Azure Functions – host.json referencia 2.x](functions-host-json.md).

> [!NOTE]
> Az a funkciók host.json odkaz 1.x, lásd: [Azure Functions – host.json referencia 1.x](functions-host-json-v1.md).

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
|maxPollingInterval|00:00:01|A várólista-lekérdezések közötti maximális időköz. Minimum 00:00:00.100 (100 MS), és legfeljebb 00:01:00 (1 perc) értékkel növekszik. |
|visibilityTimeout|00:00:00|Az újrapróbálkozások között eltelt idő az üzenet feldolgozásakor. |
|batchSize|16|Azoknak a üzenetsor-üzeneteknek a száma, amelyeket a függvények futtatókörnyezete egyszerre kér le, és párhuzamosan dolgozza fel a folyamatokat. A feldolgozás alatt álló szám lekérése `newBatchThreshold`után a futtatókörnyezet egy másik köteget kap, és elindítja az üzenetek feldolgozását. Így a függvények `batchSize` által feldolgozott egyidejű üzenetek maximális száma plusz `newBatchThreshold`. Ez a korlát külön vonatkozik az egyes üzenetsor-vezérelt függvényekre. <br><br>Ha el szeretné kerülni az egy várólistán fogadott üzenetek párhuzamos végrehajtását, beállíthatja az 1 `batchSize` értékre. Ez a beállítás azonban csak akkor teszi feleslegessé a párhuzamosságot, ha a Function alkalmazás egyetlen virtuális gépen fut (VM). Ha a Function alkalmazás több virtuális gépre is kiterjed, minden egyes virtuális gép futtathatja az egyes üzenetsor által aktivált függvények egy példányát.<br><br>A maximális `batchSize` érték 32. |
|maxDequeueCount|5|Azon alkalmak száma, amelyekkel az üzenetek feldolgozására kerül sor, mielőtt a rendszer áthelyezi azt a Megmérgező várólistára.|
|newBatchThreshold|batchSize/2|Ha az egyidejűleg feldolgozható üzenetek száma leállítja ezt a számot, a futtatókörnyezet egy másik köteget kérdez le.|

## <a name="next-steps"></a>További lépések

* [Tudjon meg többet az Azure functions eseményindítók és kötések](functions-triggers-bindings.md)

<!--
> [!div class="nextstepaction"]
> [Go to a quickstart that uses a Queue storage trigger](functions-create-storage-queue-triggered-function.md)
-->

> [!div class="nextstepaction"]
> [Ugrás egy üzenetsor-tároló kimeneti kötést használó oktatóanyagra](functions-integrate-storage-queue-output-binding.md)

<!-- LINKS -->

[CloudQueueMessage]: /dotnet/api/microsoft.azure.storage.queue.cloudqueuemessage
