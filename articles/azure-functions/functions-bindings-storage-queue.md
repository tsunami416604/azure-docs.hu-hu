---
title: "Az Azure Functions az Azure várólista tárolási kötések"
description: "Megtudhatja, hogyan használható az Azure Queue storage eseményindító és kimeneti kötése az Azure Functions."
services: functions
documentationcenter: na
author: ggailey777
manager: cfowler
editor: 
tags: 
keywords: "Azure functions, Funkciók, Eseményfeldolgozási, dinamikus számítási kiszolgáló nélküli architektúrája"
ms.service: functions
ms.devlang: multiple
ms.topic: reference
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 10/23/2017
ms.author: glenga
ms.openlocfilehash: 258393884c156b2cff00559d3cedfde1380c6c28
ms.sourcegitcommit: 85012dbead7879f1f6c2965daa61302eb78bd366
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/02/2018
---
# <a name="azure-queue-storage-bindings-for-azure-functions"></a>Az Azure Functions az Azure várólista tárolási kötések

Ez a cikk ismerteti az Azure Functions kötések Azure Queue storage használata. Az Azure Functions támogatja aktiválhatja, és kimeneti várólista kötései.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="trigger"></a>Eseményindító

A várólista eseményindító segítségével indítsa el a függvényt, ha új elem érkezik egy várólista. Az üzenetsorban lévő üzenetet bemenetként a szolgáltatás valósul meg.

## <a name="trigger---example"></a>Eseményindító – példa

Tekintse meg a nyelvspecifikus példát:

* [C#](#trigger---c-example)
* [C# parancsfájl (.csx)](#trigger---c-script-example)
* [JavaScript](#trigger---javascript-example)

### <a name="trigger---c-example"></a>Eseményindító - C# – példa

Az alábbi példa mutatja egy [C# függvény](functions-dotnet-class-library.md) , amely lekérdezi a `myqueue-items` várólistára, és írja a napló minden alkalommal, amikor egy elem feldolgozása.

```csharp
public static class QueueFunctions
{
    [FunctionName("QueueTrigger")]
    public static void QueueTrigger(
        [QueueTrigger("myqueue-items")] string myQueueItem, 
        TraceWriter log)
    {
        log.Info($"C# function processed: {myQueueItem}");
    }
}
```

### <a name="trigger---c-script-example"></a>Eseményindító - C# parancsfájl – példa

A következő példa bemutatja egy blob eseményindító kötelező egy *function.json* fájl és [C# parancsfájl (.csx)](functions-reference-csharp.md) kódot, amely a kötés használja. A függvény szavazások a `myqueue-items` várólistára, és írja a napló minden alkalommal, amikor egy elem dolgoz fel.

Itt a *function.json* fájlt:

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

A [konfigurációs](#trigger---configuration) a szakasz ismerteti ezeket a tulajdonságokat.

A C# parancsfájl kód itt látható:

```csharp
#r "Microsoft.WindowsAzure.Storage"

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
    TraceWriter log)
{
    log.Info($"C# Queue trigger function processed: {myQueueItem.AsString}\n" +
        $"queueTrigger={queueTrigger}\n" +
        $"expirationTime={expirationTime}\n" +
        $"insertionTime={insertionTime}\n" +
        $"nextVisibleTime={nextVisibleTime}\n" +
        $"id={id}\n" +
        $"popReceipt={popReceipt}\n" + 
        $"dequeueCount={dequeueCount}");
}
```

A [használati](#trigger---usage) a szakasz ismerteti, `myQueueItem`, amelyek neve szerint a `name` function.json tulajdonságot.  A [metaadatszakasz üzenet](#trigger---message-metadata) valamennyi látható változót ismerteti.

### <a name="trigger---javascript-example"></a>Eseményindító - JavaScript – példa

A következő példa bemutatja egy blob eseményindító kötelező egy *function.json* fájlt és egy [JavaScript függvény](functions-reference-node.md) , amely a kötés használja. A függvény szavazások a `myqueue-items` várólistára, és írja a napló minden alkalommal, amikor egy elem dolgoz fel.

Itt a *function.json* fájlt:

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

A [konfigurációs](#trigger---configuration) a szakasz ismerteti ezeket a tulajdonságokat.

A JavaScript-kód itt látható:

```javascript
module.exports = function (context) {
    context.log('Node.js queue trigger function processed work item', context.bindings.myQueueItem);
    context.log('queueTrigger =', context.bindingData.queueTrigger);
    context.log('expirationTime =', context.bindingData.expirationTime);
    context.log('insertionTime =', context.bindingData.insertionTime);
    context.log('nextVisibleTime =', context.bindingData.nextVisibleTime);
    context.log('id =', context.bindingData.id);
    context.log('popReceipt =', context.bindingData.popReceipt);
    context.log('dequeueCount =', context.bindingData.dequeueCount);
    context.done();
};
```

A [használati](#trigger---usage) a szakasz ismerteti, `myQueueItem`, amelyek neve szerint a `name` function.json tulajdonságot.  A [metaadatszakasz üzenet](#trigger---message-metadata) valamennyi látható változót ismerteti.

## <a name="trigger---attributes"></a>Eseményindító - attribútumok
 
A [C# osztálykönyvtárakhoz](functions-dotnet-class-library.md), várólista eseményindító konfigurálása a következő attribútumokat használhatja:

* [QueueTriggerAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/QueueTriggerAttribute.cs)NuGet-csomagot a definiált [Microsoft.Azure.WebJobs](http://www.nuget.org/packages/Microsoft.Azure.WebJobs)

  Az attribútum konstruktora figyeléséhez, a várólista nevét hajtja végre a következő példában látható módon:

  ```csharp
  [FunctionName("QueueTrigger")]
  public static void Run(
      [QueueTrigger("myqueue-items")] string myQueueItem, 
      TraceWriter log)
  {
      ...
  }
  ```

  Beállíthatja a `Connection` tulajdonság adja meg a tárfiókot, a következő példában látható módon:

  ```csharp
  [FunctionName("QueueTrigger")]
  public static void Run(
      [QueueTrigger("myqueue-items", Connection = "StorageConnectionAppSetting")] string myQueueItem, 
      TraceWriter log)
  {
      ....
  }
  ```
 
  Tekintse meg a teljes például [eseményindító - C# példa](#trigger---c-example).

* [StorageAccountAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/StorageAccountAttribute.cs)NuGet-csomagot a definiált [Microsoft.Azure.WebJobs](http://www.nuget.org/packages/Microsoft.Azure.WebJobs)

  Adja meg a tárfiókot egy másik lehetőséget nyújt. A konstruktornak, amely tartalmazza a tárolási kapcsolati karakterlánc alkalmazásbeállítás neve vesz igénybe. Az attribútum a paraméter, módszer vagy osztály szintjén is alkalmazható. A következő példa bemutatja az osztály és módszer:

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

A használt tárfiók határozza meg a következő sorrendben:

* A `QueueTrigger` attribútum `Connection` tulajdonság.
* A `StorageAccount` attribútuma ugyanezt a paramétert, mint a `QueueTrigger` attribútum.
* A `StorageAccount` függvény attribútuma.
* A `StorageAccount` osztály attribútuma.
* Az "AzureWebJobsStorage" alkalmazásbeállítás.

## <a name="trigger---configuration"></a>Eseményindító - konfiguráció

Az alábbi táblázat ismerteti a beállított kötés konfigurációs tulajdonságok a *function.json* fájl és a `QueueTrigger` attribútum.

|Function.JSON tulajdonság | Attribútum tulajdonsága |Leírás|
|---------|---------|----------------------|
|**típusa** | n/a| meg kell `queueTrigger`. Ez a tulajdonság rendszer automatikusan beállítja az eseményindítót hoz létre az Azure portálon.|
|**iránya**| n/a | Az a *function.json* csak fájlt. meg kell `in`. Ez a tulajdonság rendszer automatikusan beállítja az eseményindítót hoz létre az Azure portálon. |
|**név** | n/a |A várólista a funkciókódot jelölő neve.  | 
|**queueName** | **QueueName**| A várólista, és kérdezze le a neve. | 
|**kapcsolat** | **Kapcsolat** |A tárolási kapcsolati karakterlánc az ehhez a kötéshez használandó tartalmazó alkalmazásbeállítás neve. Ha az alkalmazás neve "AzureWebJobs" kezdődik, megadhatja a nevét itt csak a maradékot. Ha például `connection` "MyStorage", hogy a Functions futtatókörnyezete keresi, hogy az alkalmazás neve "AzureWebJobsMyStorage." Ha nem adja meg `connection` üres, a Functions futtatókörnyezete használja az alapértelmezett tárolási kapcsolati karakterlánc az nevű Alkalmazásbeállítás `AzureWebJobsStorage`.|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="trigger---usage"></a>Eseményindító - használat
 
C# és C# a parancsfájlt, nyissa meg a blobadatokat metódusparaméter használatával `Stream paramName`. A C# parancsfájl `paramName` érték szerepel a `name` tulajdonsága *function.json*. Köthető a következő típusok:

* POCO objektum – a Functions futtatókörnyezete deserializes egy JSON-adattartalmat POCO objektumba. 
* `string`
* `byte[]`
* [CloudQueueMessage]

A JavaScript, használjon `context.bindings.<name>` a várólista elem tartalom eléréséhez. Ha a tartalom JSON-NÁ, akkor az objektum van deszerializálni.

## <a name="trigger---message-metadata"></a>Eseményindító - üzenet metaadatok

A várólista eseményindító biztosít több metaadat-tulajdonságot. Ezeket a tulajdonságokat meg más kötésekben kötési kifejezés részeként vagy a kód paramétereiben használható. Az értékek rendelkezik a azonos szemantikákkal, [CloudQueueMessage](https://docs.microsoft.com/dotnet/api/microsoft.windowsazure.storage.queue.cloudqueuemessage).

|Tulajdonság|Típus|Leírás|
|--------|----|-----------|
|`QueueTrigger`|`string`|Várólista hasznos (ha érvényes karakterlánc). A várólista hibaüzenet karakterláncként, hasznos `QueueTrigger` nevű változó értéke a `name` tulajdonság *function.json*.|
|`DequeueCount`|`int`|Ez az üzenet várólistából nincs kivéve. eléréseinek a száma.|
|`ExpirationTime`|`DateTimeOffset?`|Az üzenet lejárati ideje.|
|`Id`|`string`|Várólista azonosító.|
|`InsertionTime`|`DateTimeOffset?`|Az üzenet a várólistához lett adva időpontja.|
|`NextVisibleTime`|`DateTimeOffset?`|Az üzenet mellett látható lesz ideje.|
|`PopReceipt`|`string`|A pop visszaigazolás.|

## <a name="trigger---poison-messages"></a>Eseményindító - elhalt üzenetek

Ha nem sikerül egy várólista funkció, az Azure Functions újrapróbálkozik a függvény legfeljebb ötször egy adott várólistában üzenet, beleértve az első próbálkozás. Minden öt kísérlet sikertelen, ha a functions futtatókörnyezete ad hozzá egy üzenet nevű várólista  *&lt;originalqueuename >-poison*. Írhat egy folyamat üzenetek működnek, mint az elhalt üzenetsorból naplózásukhoz, vagy értesítést küld, hogy manuális beavatkozást van szükség.

Az elhalt üzenetek manuálisan kezeléséhez, ellenőrizze a [dequeueCount](#trigger---message-metadata) , az üzenetsorban lévő üzenetet.

## <a name="trigger---hostjson-properties"></a>Eseményindító - host.json tulajdonságai

A [host.json](functions-host-json.md#queues) fájl várólista eseményindító viselkedését vezérlő beállításokat tartalmaz.

[!INCLUDE [functions-host-json-queues](../../includes/functions-host-json-queues.md)]

## <a name="output"></a>Kimenet

Az Azure Queue storage kimeneti kötése üzeneteket írni a várólista használja.

## <a name="output---example"></a>Kimeneti – példa

Tekintse meg a nyelvspecifikus példát:

* [C#](#output---c-example)
* [C# parancsfájl (.csx)](#output---c-script-example)
* [JavaScript](#output---javascript-example)

### <a name="output---c-example"></a>Kimeneti - C# – példa

Az alábbi példa mutatja egy [C# függvény](functions-dotnet-class-library.md) a minden fogadott HTTP-kérelmek egy üzenetsor-üzenetet hoz létre.

```csharp
[StorageAccount("AzureWebJobsStorage")]
public static class QueueFunctions
{
    [FunctionName("QueueOutput")]
    [return: Queue("myqueue-items")]
    public static string QueueOutput([HttpTrigger] dynamic input,  TraceWriter log)
    {
        log.Info($"C# function processed: {input.Text}");
        return input.Text;
    }
}
```

### <a name="output---c-script-example"></a>Kimeneti - C# parancsfájl – példa

A következő példa bemutatja egy blob eseményindító kötelező egy *function.json* fájl és [C# parancsfájl (.csx)](functions-reference-csharp.md) kódot, amely a kötés használja. A függvény egy elem egy POCO hasznos a minden fogadott HTTP-kérelmek hoz létre.

Itt a *function.json* fájlt:

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
      "connection": "MyStorageConnectionAppSetting",
    }
  ]
}
``` 

A [konfigurációs](#output---configuration) a szakasz ismerteti ezeket a tulajdonságokat.

C# parancsfájlkódot egyetlen üzenetsor hoz létre a következő:

```cs
public class CustomQueueMessage
{
    public string PersonName { get; set; }
    public string Title { get; set; }
}

public static CustomQueueMessage Run(CustomQueueMessage input, TraceWriter log)
{
    return input;
}
```

Azonnal elküldheti a több üzenet a használatával egy `ICollector` vagy `IAsyncCollector` paraméter. Az alábbiakban C# parancsfájlkódot elküldi több üzenetet, egyet a HTTP-kérelem adatai, és egy kódolt értékekkel:

```cs
public static void Run(
    CustomQueueMessage input, 
    ICollector<CustomQueueMessage> myQueueItem, 
    TraceWriter log)
{
    myQueueItem.Add(input);
    myQueueItem.Add(new CustomQueueMessage { PersonName = "You", Title = "None" });
}
```

### <a name="output---javascript-example"></a>Kimeneti - JavaScript – példa

A következő példa bemutatja egy blob eseményindító kötelező egy *function.json* fájlt és egy [JavaScript függvény](functions-reference-node.md) , amely a kötés használja. A függvény minden egyes HTTP-kérelem érkezett a várólista elem hoz létre.

Itt a *function.json* fájlt:

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
      "connection": "MyStorageConnectionAppSetting",
    }
  ]
}
``` 

A [konfigurációs](#output---configuration) a szakasz ismerteti ezeket a tulajdonságokat.

A JavaScript-kód itt látható:

```javascript
module.exports = function (context, input) {
    context.done(null, input.body);
};
```

Küldhet több üzenetet egyszerre egy üzenet tömb definiálása a `myQueueItem` kimeneti kötése. A következő JavaScript-kódot a minden fogadott HTTP-kérelmek kódolt értékekkel két üzenetsor-üzeneteket küld.

```javascript
module.exports = function(context) {
    context.bindings.myQueueItem = ["message 1","message 2"];
    context.done();
};
```

## <a name="output---attributes"></a>Kimeneti - attribútumok
 
A [C# osztálykönyvtárakhoz](functions-dotnet-class-library.md), használja a [QueueAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/QueueAttribute.cs), amely van megadva a NuGet-csomag [Microsoft.Azure.WebJobs](http://www.nuget.org/packages/Microsoft.Azure.WebJobs).

Az attribútum vonatkozik egy `out` paraméter vagy a függvény visszatérési értéke. Az attribútum konstruktora a várólista nevét hajtja végre a következő példában látható módon:

```csharp
[FunctionName("QueueOutput")]
[return: Queue("myqueue-items")]
public static string Run([HttpTrigger] dynamic input,  TraceWriter log)
{
    ...
}
```

Beállíthatja a `Connection` tulajdonság adja meg a tárfiókot, a következő példában látható módon:

```csharp
[FunctionName("QueueOutput")]
[return: Queue("myqueue-items, Connection = "StorageConnectionAppSetting")]
public static string Run([HttpTrigger] dynamic input,  TraceWriter log)
{
    ...
}
```

Tekintse meg a teljes például [kimeneti - C# példa](#output---c-example).

Használhatja a `StorageAccount` attribútum segítségével adhatja meg a tárfiók osztály, módszer vagy paraméter szinten. További információkért lásd: [eseményindító - attribútumok](#trigger---attribute).

## <a name="output---configuration"></a>Kimeneti - konfiguráció

Az alábbi táblázat ismerteti a beállított kötés konfigurációs tulajdonságok a *function.json* fájl és a `Queue` attribútum.

|Function.JSON tulajdonság | Attribútum tulajdonsága |Leírás|
|---------|---------|----------------------|
|**típusa** | n/a | meg kell `queue`. Ez a tulajdonság rendszer automatikusan beállítja az eseményindítót hoz létre az Azure portálon.|
|**iránya** | n/a | meg kell `out`. Ez a tulajdonság rendszer automatikusan beállítja az eseményindítót hoz létre az Azure portálon. |
|**név** | n/a | A várólista a funkciókódot jelölő neve. Beállítása `$return` hivatkozni, a függvény visszatérési értéke.| 
|**queueName** |**QueueName** | Az üzenetsor neve. | 
|**kapcsolat** | **Kapcsolat** |A tárolási kapcsolati karakterlánc az ehhez a kötéshez használandó tartalmazó alkalmazásbeállítás neve. Ha az alkalmazás neve "AzureWebJobs" kezdődik, megadhatja a nevét itt csak a maradékot. Ha például `connection` "MyStorage", hogy a Functions futtatókörnyezete keresi, hogy az alkalmazás neve "AzureWebJobsMyStorage." Ha nem adja meg `connection` üres, a Functions futtatókörnyezete használja az alapértelmezett tárolási kapcsolati karakterlánc az nevű Alkalmazásbeállítás `AzureWebJobsStorage`.|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="output---usage"></a>Kimeneti - használat
 
A C# és C# a parancsfájlt, írja be egyetlen üzenetsor metódusparaméter használatával `out T paramName`. A C# parancsfájl `paramName` érték szerepel a `name` tulajdonsága *function.json*. A metódus visszatérési típusának helyett használhat egy `out` paramétert, és `T` a következő típusok egyike lehet:

* Egy POCO szerializálható JSON-fájlként
* `string`
* `byte[]`
* [CloudQueueMessage] 

A C# és C# a parancsfájlt írja be több üzenetsor-üzeneteket a következő típusok egyikének használatával: 

* `ICollector<T>` vagy `IAsyncCollector<T>`
* [CloudQueue](/dotnet/api/microsoft.windowsazure.storage.queue.cloudqueue)

A JavaScript-funkcióként használható `context.bindings.<name>` a kimeneti várólista üzenet eléréséhez. A várólista elem forgalma egy karakterlánc- vagy szerializálhatók a JSON-objektum használható.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Nyissa meg a várólista tárolási eseményindító használó gyors üzembe helyezés](functions-create-storage-queue-triggered-function.md)

> [!div class="nextstepaction"]
> [Nyissa meg a várólista tárolási alkalmazó oktatóanyagot kimeneti kötése](functions-integrate-storage-queue-output-binding.md)

> [!div class="nextstepaction"]
> [További tudnivalók az Azure functions eseményindítók és kötések](functions-triggers-bindings.md)

<!-- LINKS -->

[CloudQueueMessage]: /dotnet/api/microsoft.windowsazure.storage.queue.cloudqueuemessage