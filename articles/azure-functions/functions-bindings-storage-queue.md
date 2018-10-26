---
title: Az Azure storage üzenetsorkötések az Azure Functions szolgáltatáshoz
description: Megtudhatja, hogyan használhatja az Azure Queue storage eseményindítója, és kimeneti kötése az Azure Functions szolgáltatásban.
services: functions
documentationcenter: na
author: ggailey777
manager: jeconnoc
keywords: az Azure functions, függvények, eseményfeldolgozás, dinamikus számítás, kiszolgáló nélküli architektúra
ms.service: azure-functions
ms.devlang: multiple
ms.topic: reference
ms.date: 09/03/2018
ms.author: glenga
ms.custom: cc996988-fb4f-47
ms.openlocfilehash: b3d4019fc5bde2eb10f0534291749dd25e7b5bed
ms.sourcegitcommit: 5de9de61a6ba33236caabb7d61bee69d57799142
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/25/2018
ms.locfileid: "50086930"
---
# <a name="azure-queue-storage-bindings-for-azure-functions"></a>Az Azure storage üzenetsorkötések az Azure Functions szolgáltatáshoz

Ez a cikk bemutatja, hogyan használható az Azure Queue storage-kötések az Azure Functions szolgáltatásban. Az Azure Functions támogatja a-trigger, és a kimeneti kötések a várólistákhoz.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="packages---functions-1x"></a>Csomagok – 1.x függvények

A tárolási üzenetsor kötései szerepelnek a [Microsoft.Azure.WebJobs](http://www.nuget.org/packages/Microsoft.Azure.WebJobs) NuGet-csomag verziója 2.x. A csomag forráskódja a [azure-webjobs-sdk](https://github.com/Azure/azure-webjobs-sdk/tree/v2.x/src/Microsoft.Azure.WebJobs.Storage/Queue) GitHub-adattárban.

[!INCLUDE [functions-package-auto](../../includes/functions-package-auto.md)]

[!INCLUDE [functions-storage-sdk-version](../../includes/functions-storage-sdk-version.md)]

## <a name="packages---functions-2x"></a>Csomagok – 2.x függvények

A tárolási üzenetsor kötései szerepelnek a [Microsoft.Azure.WebJobs.Extensions.Storage](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.Storage) NuGet-csomag verziója 3.x. A csomag forráskódja a [azure-webjobs-sdk](https://github.com/Azure/azure-webjobs-sdk/tree/dev/src/Microsoft.Azure.WebJobs.Extensions.Storage/Queues) GitHub-adattárban.

[!INCLUDE [functions-package-v2](../../includes/functions-package-v2.md)]

## <a name="trigger"></a>Eseményindító

Az üzenetsor eseményindító használatával indít el egy függvényt, amikor új elem egy üzenetsorba való érkezésekor. Az üzenetsorban található üzenet van megadva a függvény bemeneteként.

## <a name="trigger---example"></a>Az eseményindító – példa

Tekintse meg az adott nyelvű példa:

* [C#](#trigger---c-example)
* [C# script (.csx)](#trigger---c-script-example)
* [JavaScript](#trigger---javascript-example)
* [Java](#trigger---Java-example)

### <a name="trigger---c-example"></a>Eseményindító - C#-példa

A következő példa bemutatja egy [C#-függvény](functions-dotnet-class-library.md) , amely lekérdezi a `myqueue-items` üzenetsort, és írja a napló minden alkalommal, amikor egy elem feldolgozása.

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

Az alábbi példa bemutatja egy üzenetsor eseményindító kötelező egy *function.json* fájl és [C#-szkript (.csx)](functions-reference-csharp.md) a kötést használó kód. A függvény lekérdezi a `myqueue-items` üzenetsort, és írja a napló minden alkalommal, amikor egy elem feldolgozása.

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

A [használati](#trigger---usage) szakasz ismerteti, `myQueueItem`, amelyek neve szerint a `name` function.json tulajdonságot.  A [metaadatokat tartalmazó szakasz üzenet](#trigger---message-metadata) összes látható változókat ismerteti.

### <a name="trigger---javascript-example"></a>Eseményindító - JavaScript-példa

Az alábbi példa bemutatja egy üzenetsor eseményindító kötelező egy *function.json* fájl és a egy [JavaScript-függvény](functions-reference-node.md) , amely a kötés használja. A függvény lekérdezi a `myqueue-items` üzenetsort, és írja a napló minden alkalommal, amikor egy elem feldolgozása.

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
> A name paraméter tükrözze az `context.bindings.<name>` várólista elem hasznos adatait tartalmazó JavaScript-kódban. Ezek a hasznos adatok is a függvénynek átadott második paraméterként.

A következő JavaScript-kódot:

```javascript
module.exports = async function (context, message) {
    context.log('Node.js queue trigger function processed work item', message);
    // OR access using context.bindings.<name>
    // context.log('Node.js queue trigger function processed work item', context.bindings.myQueueItem);
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

A [használati](#trigger---usage) szakasz ismerteti, `myQueueItem`, amelyek neve szerint a `name` function.json tulajdonságot.  A [metaadatokat tartalmazó szakasz üzenet](#trigger---message-metadata) összes látható változókat ismerteti.

### <a name="trigger---java-example"></a>Eseményindító - Java-példában

A következő Java-példa bemutatja egy storage-üzenetsor eseményindító függvények, amely az aktivált üzenet üzenetsorba helyezi `myqueuename`.
 
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

## <a name="trigger---attributes"></a>Eseményindító - attribútumok
 
A [C#-osztálykódtárakat](functions-dotnet-class-library.md), használja a következő attribútumok egy üzenetsor eseményindító konfigurálása:

* [QueueTriggerAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/QueueTriggerAttribute.cs)

  Az attribútum konstruktorának használatával figyelheti meg, az üzenetsor nevére a következő példában látható módon:

  ```csharp
  [FunctionName("QueueTrigger")]
  public static void Run(
      [QueueTrigger("myqueue-items")] string myQueueItem, 
      ILogger log)
  {
      ...
  }
  ```

  Beállíthatja a `Connection` tulajdonságot adja meg a storage-fiókot szeretne használni, az alábbi példában látható módon:

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

  A használandó tárfiókot adjon meg egy másik megoldást kínál. A konstruktorban vesz igénybe egy tárolási kapcsolati karakterlánccal tartalmazó alkalmazásbeállítás neve. Az attribútum a paramétert, a metódus vagy az osztály szintjén alkalmazhatók. Az alábbi példa bemutatja az osztály és metódust:

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

A használandó tárfiókot határozza meg a következő sorrendben:

* A `QueueTrigger` attribútum `Connection` tulajdonság.
* A `StorageAccount` a paramétert, amelyek azonos, alkalmazott attribútum a `QueueTrigger` attribútum.
* A `StorageAccount` attribútum a függvény a alkalmazni.
* A `StorageAccount` attribútum az osztály a alkalmazni.
* Az "AzureWebJobsStorage" alkalmazásbeállítást.

## <a name="trigger---configuration"></a>Eseményindító - konfiguráció

A következő táblázat ismerteti a megadott kötés konfigurációs tulajdonságaiban a *function.json* fájlt, és a `QueueTrigger` attribútum.

|Function.JSON tulajdonság | Attribútum tulajdonsága |Leírás|
|---------|---------|----------------------|
|**type** | n/a| Meg kell `queueTrigger`. Ez a tulajdonság beállítása automatikusan történik, ha az eseményindítót fog létrehozni az Azure Portalon.|
|**direction**| n/a | Az a *function.json* csak fájl. Meg kell `in`. Ez a tulajdonság beállítása automatikusan történik, ha az eseményindítót fog létrehozni az Azure Portalon. |
|**name** | n/a |A függvénykódot a várólista elem adattartalom tartalmazó változó neve.  | 
|**queueName** | **queueName**| A lekérdezéshez az üzenetsor neve. | 
|**kapcsolat** | **kapcsolat** |A tárolási kapcsolati karakterlánc használata ehhez a kötéshez tartalmazó alkalmazásbeállítás neve. Azon alkalmazásbeállítás neve "AzureWebJobs" kezdődik, ha csak a maradékot Itt a neve is megadhat. Például, ha a beállított `connection` a "MyStorage", a Functions futtatókörnyezete úgy tűnik, a beállítás, amely alkalmazás neve "AzureWebJobsMyStorage." Ha meghagyja a `connection` üres, a Functions futtatókörnyezete használja az alapértelmezett tárolási kapcsolati karakterlánc nevű Alkalmazásbeállítás `AzureWebJobsStorage`.|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="trigger---usage"></a>Eseményindító - használat
 
A C# és a C#-szkript, eléréséhez az állapotüzenet-adatokat egy metódus-paramétert használatával `string paramName`. A C#-szkript `paramName` az értéket a megadott a `name` tulajdonsága *function.json*. A következő típusok kell kötni:

* Az objektum – a Functions futtatókörnyezete deserializes egy JSON-adattartalom be egy tetszőleges, a kód meghatározott osztály egy példányát. 
* `string`
* `byte[]`
* [CloudQueueMessage]

Ha megpróbál kötést létrehozni `CloudQueueMessage` és a egy hibaüzenet, győződjön meg arról, hogy rendelkezik-e egy hivatkozást, [a megfelelő Storage SDK-verzió](#azure-storage-sdk-version-in-functions-1x).

A JavaScript használatával `context.bindings.<name>` a várólista elem hasznos adatainak eléréséhez. A hasznos JSON-ban, ha azt egy objektum be van deszerializálni.

## <a name="trigger---message-metadata"></a>Eseményindító - üzenet metaadatok

Az üzenetsor eseményindító biztosít több [metaadat-tulajdonságot](functions-triggers-bindings.md#binding-expressions---trigger-metadata). Ezek a tulajdonságok a kötési kifejezésekben való használata más kötések részeként vagy a kód paramétereiben használható. Ezek a tulajdonságai a [CloudQueueMessage](https://docs.microsoft.com/dotnet/api/microsoft.windowsazure.storage.queue.cloudqueuemessage) osztály.

|Tulajdonság|Típus|Leírás|
|--------|----|-----------|
|`QueueTrigger`|`string`|Várólista adattartalma (Ha egy érvényes karakterlánc). Ha az üzenetsor üzenetet karakterláncként, hasznos `QueueTrigger` ugyanazzal az értékkel, mint a változó neve szerint rendelkezik a `name` tulajdonság *function.json*.|
|`DequeueCount`|`int`|Hányszor Ez az üzenet rendelkezik lett el távolítva a sorból.|
|`ExpirationTime`|`DateTimeOffset`|Az az idő, amely az üzenet lejár.|
|`Id`|`string`|Várólista állapotüzenet-azonosító.|
|`InsertionTime`|`DateTimeOffset`|Az az idő, hogy az üzenet a várólistában hozzá lett adva.|
|`NextVisibleTime`|`DateTimeOffset`|Az az idő, hogy az üzenet mellett látható lesz.|
|`PopReceipt`|`string`|Az üzenet pop fogadását.|

## <a name="trigger---poison-messages"></a>Eseményindító - ártalmas üzenetek

Üzenetsor eseményindító függvény sikertelen lesz, ha az Azure Functions újrapróbálkozik a függvény legfeljebb ötször egy adott üzenetsorban üzenet, beleértve az első próbálkozáskor. Ha minden öt kísérlet sikertelen, a functions futtatókörnyezete nevű üzenetsor üzenet hozzáadása  *&lt;originalqueuename >-ártalmas*. Írhat a naplózásukhoz vagy egy értesítést küld, manuális beavatkozást ártalmas várólistában lévő üzenetek feldolgozásával függvény van szükség.

Manuálisan ártalmas üzenetek kezeléséhez, ellenőrizze a [dequeueCount](#trigger---message-metadata) , az üzenetsorban található üzenet.

## <a name="trigger---polling-algorithm"></a>Eseményindító - lekérdezési algoritmus

Az üzenetsor eseményindító valósít meg egy véletlenszerű exponenciális visszatartási algoritmus üresjárati várólista tárolási tranzakciós költségeket a lekérdezési hatásának csökkentése érdekében.  Amikor egy üzenet található, a futásidejű két másodpercet vár, és csak azután ellenőrzi egy másik üzenet; Ha nincs üzenet található, arra vár körülbelül négy másodpercig az újrapróbálkozás előtt. Későbbi a sikertelen kísérletek üzenetsori üzenetek beolvasásához, miután a várakozási idő továbbra is növelje addig a maximális várakozási idő, alapértelmezett értéke egy perc alatt. A maximális várakozási idő konfigurálhatók a `maxPollingInterval` tulajdonságot a [host.json fájl](functions-host-json.md#queues).

## <a name="trigger---concurrency"></a>Eseményindító - egyidejűség

Várakozás több üzenetsorbeli üzenetek esetén, az üzenetsor eseményindító egy üzenetköteget kérdezi le, és hívja meg a függvény-példányok egyidejűleg feldolgozni azokat. Alapértelmezés szerint a Köteg mérete 16. Ha a feldolgozás alatt száma 8 lekérdezi, a futásidejű lekérdezi egy másik köteg, és elindítja a feldolgozási ezeket az üzeneteket. Így a függvény egy virtuális gépen (VM) feldolgozott egyidejű üzenetek maximális száma: 24. Ez a korlátozás külön-külön mindegyik üzenetsor által aktivált függvény az egyes virtuális Gépeken vonatkozik. Ha több virtuális gépre méretezhető a függvényalkalmazást, minden virtuális gép eseményindítók várja meg, és megkísérli futtatni a functions. Például ha 3 virtuális gépre méretezhető egy függvényalkalmazást, párhuzamosan futó példánya egy üzenetsor által aktivált függvény az alapértelmezett maximális számát, a 72.

A Köteg mérete és a egy új batch első küszöbértéke is lehet beállítani a [host.json fájl](functions-host-json.md#queues). Ha azt szeretné, az üzenetsor által aktivált függvények a függvényalkalmazás párhuzamos végrehajtás minimalizálása érdekében, beállíthatja a köteg méretének 1-re. Ez a beállítás használata esetén nem egyidejűségi csak, feltéve, hogy a függvényalkalmazás futtatása egyetlen virtuális gépen (VM). 

Az üzenetsor eseményindító automatikusan megakadályozza, hogy egy függvény üzenetsori üzenet feldolgozása többször; függvények nem kell kell megírni, hogy idempotensek legyenek.

## <a name="trigger---hostjson-properties"></a>Eseményindító - host.json tulajdonságai

A [host.json](functions-host-json.md#queues) fájl üzenetsor eseményindító viselkedését vezérlő beállításokat tartalmaz.

[!INCLUDE [functions-host-json-queues](../../includes/functions-host-json-queues.md)]

## <a name="output"></a>Kimenet

Használja az Azure üzenetsor-tároló kimeneti kötését üzeneteket írni egy üzenetsorba.

## <a name="output---example"></a>Kimenete – példa

Tekintse meg az adott nyelvű példa:

* [C#](#output---c-example)
* [C# script (.csx)](#output---c-script-example)
* [JavaScript](#output---javascript-example)
* [Java](#output---java-example)

### <a name="output---c-example"></a>Kimenet – C#-példa

A következő példa bemutatja egy [C#-függvény](functions-dotnet-class-library.md) hoz létre üzenetsori üzenet minden HTTP-kérelem érkezett.

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

Az alábbi példa bemutatja egy kötelező HTTP-eseményindító egy *function.json* fájl és [C#-szkript (.csx)](functions-reference-csharp.md) a kötést használó kód. A függvény létrehoz egy üzenetsor elem egy **CustomQueueMessage** objektum minden fogadott HTTP-kérelem hasznos adatai.

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
      "connection": "MyStorageConnectionAppSetting",
    }
  ]
}
``` 

A [konfigurációs](#output---configuration) szakasz mutatja be ezeket a tulajdonságokat.

Itt látható C#-szkriptkódot, amely egyetlen üzenetsor hoz létre:

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

Használatával elküldheti a jelszó egyszerre több üzenetet egy `ICollector` vagy `IAsyncCollector` paraméter. Az alábbiakban C#-szkriptkódot, amely több üzenetet egy-a HTTP-kérésének adatait és egy változtatható értékek küld:

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

Az alábbi példa bemutatja egy kötelező HTTP-eseményindító egy *function.json* fájl és a egy [JavaScript-függvény](functions-reference-node.md) , amely a kötés használja. A függvény létrehoz egy elem minden HTTP-kérelem érkezett.

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
      "connection": "MyStorageConnectionAppSetting",
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

Az egy üzenet tömb definiálásával elküldheti a jelszó egyszerre több üzenetet a `myQueueItem` kimeneti kötést. A következő JavaScript-kódot küld két üzenetsorbeli üzenetek minden fogadott HTTP-kérelem kódolt értékekkel.

```javascript
module.exports = function(context) {
    context.bindings.myQueueItem = ["message 1","message 2"];
    context.done();
};
```

### <a name="output---java-example"></a>Kimenet – Java-példában

 Az alábbi példa bemutatja egy Java-függvény, amely az üzenetsori üzenet létrehoz egy HTTP-kérelem elindításakor.

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

Az a [Java-függvények futásidejű kódtár](/java/api/overview/azure/functions/runtime), használja a `@QueueOutput` paraméterek, amelynek értéke a Queue storage tartalmazná a jegyzet.  A paraméter típusa legyen `OutputBinding<T>`, ahol a T natív Java bármilyen egy pojo-vá.


## <a name="output---attributes"></a>Kimenet – attribútumok
 
A [C#-osztálykódtárakat](functions-dotnet-class-library.md), használja a [QueueAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/QueueAttribute.cs).

Az attribútum vonatkozik egy `out` paraméter vagy a függvény visszatérési értéke. Az attribútum konstruktorának az üzenetsor nevére a következő példában látható módon:

```csharp
[FunctionName("QueueOutput")]
[return: Queue("myqueue-items")]
public static string Run([HttpTrigger] dynamic input,  ILogger log)
{
    ...
}
```

Beállíthatja a `Connection` tulajdonságot adja meg a storage-fiókot szeretne használni, az alábbi példában látható módon:

```csharp
[FunctionName("QueueOutput")]
[return: Queue("myqueue-items", Connection = "StorageConnectionAppSetting")]
public static string Run([HttpTrigger] dynamic input,  ILogger log)
{
    ...
}
```

Egy teljes példa: [kimenet – C#-példa](#output---c-example).

Használhatja a `StorageAccount` attribútumot az osztályra, módszer vagy paraméter szintű tárfiókot adjon meg. További információkért lásd: [eseményindító - attribútumok](#trigger---attribute).

## <a name="output---configuration"></a>Kimenete – konfiguráció

A következő táblázat ismerteti a megadott kötés konfigurációs tulajdonságaiban a *function.json* fájlt, és a `Queue` attribútum.

|Function.JSON tulajdonság | Attribútum tulajdonsága |Leírás|
|---------|---------|----------------------|
|**type** | n/a | Meg kell `queue`. Ez a tulajdonság beállítása automatikusan történik, ha az eseményindítót fog létrehozni az Azure Portalon.|
|**direction** | n/a | Meg kell `out`. Ez a tulajdonság beállítása automatikusan történik, ha az eseményindítót fog létrehozni az Azure Portalon. |
|**name** | n/a | A függvény kódját a várólistájára változó neve. Állítsa be `$return` való hivatkozáshoz függvény visszatérési értéke.| 
|**queueName** |**queueName** | Az üzenetsor neve. | 
|**kapcsolat** | **kapcsolat** |A tárolási kapcsolati karakterlánc használata ehhez a kötéshez tartalmazó alkalmazásbeállítás neve. Azon alkalmazásbeállítás neve "AzureWebJobs" kezdődik, ha csak a maradékot Itt a neve is megadhat. Például, ha a beállított `connection` a "MyStorage", a Functions futtatókörnyezete úgy tűnik, a beállítás, amely alkalmazás neve "AzureWebJobsMyStorage." Ha meghagyja a `connection` üres, a Functions futtatókörnyezete használja az alapértelmezett tárolási kapcsolati karakterlánc nevű Alkalmazásbeállítás `AzureWebJobsStorage`.|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="output---usage"></a>Kimenet – használat
 
A C# és a C#-szkript, írhat egy metódus-paramétert használatával egyetlen üzenetsor `out T paramName`. A C#-szkript `paramName` az értéket a megadott a `name` tulajdonsága *function.json*. A metódus visszatérési típus helyett használhat egy `out` paramétert, és `T` a következők egyike lehet:

* Az objektum szerializálható JSON-fájlként
* `string`
* `byte[]`
* [CloudQueueMessage] 

Ha megpróbál kötést létrehozni `CloudQueueMessage` és a egy hibaüzenet, győződjön meg arról, hogy rendelkezik-e egy hivatkozást, [a megfelelő Storage SDK-verzió](#azure-storage-sdk-version-in-functions-1x).

A C# és a C#-szkript írási több üzenetsorbeli üzenetek esetén a következő típusok egyikének használatával: 

* `ICollector<T>` vagy `IAsyncCollector<T>`
* [CloudQueue](/dotnet/api/microsoft.windowsazure.storage.queue.cloudqueue)

A JavaScript-függvények használata `context.bindings.<name>` eléréséhez az kimeneti üzenetsorban található üzenet. A várólista elem hasznos karakterlánc vagy egy szerializálható JSON objektum használható.


## <a name="exceptions-and-return-codes"></a>Kivételek és a visszatérési kódok

| Kötés |  Leírások |
|---|---|
| Várólista | [Üzenetsor-hibakódok](https://docs.microsoft.com/rest/api/storageservices/queue-service-error-codes) |
| BLOB, tábla, üzenetsor | [Storage-hibakódok](https://docs.microsoft.com/rest/api/storageservices/fileservices/common-rest-api-error-codes) |
| BLOB, tábla, üzenetsor |  [hibaelhárítással](https://docs.microsoft.com/rest/api/storageservices/fileservices/troubleshooting-api-operations) |

## <a name="next-steps"></a>További lépések

* [Tudjon meg többet az Azure functions eseményindítók és kötések](functions-triggers-bindings.md)

<!--
> [!div class="nextstepaction"]
> [Go to a quickstart that uses a Queue storage trigger](functions-create-storage-queue-triggered-function.md)
-->

> [!div class="nextstepaction"]
> [Ugrás a Queue storage szolgáltatást alkalmazó oktatóanyagot a kimeneti kötése](functions-integrate-storage-queue-output-binding.md)

<!-- LINKS -->

[CloudQueueMessage]: /dotnet/api/microsoft.windowsazure.storage.queue.cloudqueuemessage
