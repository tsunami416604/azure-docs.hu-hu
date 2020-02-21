---
title: Az Azure üzenetsor-tároló kimeneti kötése Azure Functions
description: Ismerje meg, hogyan hozhat létre Azure üzenetsor-tárolási üzeneteket a Azure Functionsban.
author: craigshoemaker
ms.topic: reference
ms.date: 02/18/2020
ms.author: cshoe
ms.custom: cc996988-fb4f-47
ms.openlocfilehash: 2c16cc965c1e7e98727170fd5896dd081482c692
ms.sourcegitcommit: 98a5a6765da081e7f294d3cb19c1357d10ca333f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/20/2020
ms.locfileid: "77493500"
---
# <a name="azure-queue-storage-output-bindings-for-azure-functions"></a>Az Azure üzenetsor-tároló kimeneti kötései Azure Functions

Azure Functions új Azure üzenetsor-tárolási üzeneteket hozhat létre egy kimeneti kötés beállításával.

## <a name="example"></a>Példa

# <a name="c"></a>[C#](#tab/csharp)

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

# <a name="c-script"></a>[C#Parancsfájl](#tab/csharp-script)

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

A [konfigurációs](#configuration) szakasz ezeket a tulajdonságokat ismerteti.

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

# <a name="javascript"></a>[JavaScript](#tab/javascript)

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

A [konfigurációs](#configuration) szakasz ezeket a tulajdonságokat ismerteti.

A következő JavaScript-kódot:

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

# <a name="python"></a>[Python](#tab/python)

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

# <a name="java"></a>[Java](#tab/java)

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

## <a name="attributes-and-annotations"></a>Attribútumok és jegyzetek

# <a name="c"></a>[C#](#tab/csharp)

Az [ C# osztályok könyvtáraiban](functions-dotnet-class-library.md)használja a [QueueAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.Extensions.Storage/Queues/QueueAttribute.cs).

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

Teljes példa: [kimeneti példa](#example).

Az `StorageAccount` attribútummal megadhatja a Storage-fiókot osztály, metódus vagy paraméter szintjén. További információ: trigger-attributes.

# <a name="c-script"></a>[C#Parancsfájl](#tab/csharp-script)

Az C# attribútumokat a parancsfájl nem támogatja.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

A JavaScript nem támogatja az attribútumokat.

# <a name="python"></a>[Python](#tab/python)

A Python nem támogatja az attribútumokat.

# <a name="java"></a>[Java](#tab/java)

A `QueueOutput` jegyzet lehetővé teszi, hogy egy üzenetet egy függvény kimenete írjon. Az alábbi példa egy HTTP által aktivált függvényt mutat be, amely üzenetsor-üzenetet hoz létre.

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

## <a name="configuration"></a>Konfiguráció

Az alábbi táblázat a *function. JSON* fájlban és a `Queue` attribútumban beállított kötési konfigurációs tulajdonságokat ismerteti.

|Function.JSON tulajdonság | Attribútum tulajdonsága |Leírás|
|---------|---------|----------------------|
|**type** | n/a | `queue`értékre kell állítani. Ez a tulajdonság beállítása automatikusan történik, ha az eseményindítót fog létrehozni az Azure Portalon.|
|**direction** | n/a | `out`értékre kell állítani. Ez a tulajdonság beállítása automatikusan történik, ha az eseményindítót fog létrehozni az Azure Portalon. |
|**név** | n/a | Annak a változónak a neve, amely a függvény kódjában a várólistát jelképezi. A függvény visszatérési értékének hivatkozásához állítsa a `$return` értéket.|
|**queueName** |**QueueName** | A várólista neve. |
|**kapcsolat** | **Kapcsolat** |Egy olyan Alkalmazásbeállítás neve, amely a kötéshez használandó tárolási kapcsolati karakterláncot tartalmazza. Ha az Alkalmazásbeállítások neve "AzureWebJobs" előtaggal kezdődik, akkor itt csak a nevet adja meg. Ha például a `connection` "MyStorage" értékre állítja, a functions futtatókörnyezet egy "MyStorage" nevű alkalmazás-beállítást keres. Ha üresen hagyja a `connection`, a functions futtatókörnyezet az alapértelmezett Storage-kapcsolatok karakterláncot használja az `AzureWebJobsStorage`nevű alkalmazás-beállításban.|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="usage"></a>Használat

# <a name="c"></a>[C#](#tab/csharp)

Egyetlen üzenetsor-üzenetet írhat egy metódus-paraméter (például `out T paramName`) használatával. `out` paraméter helyett a metódus visszatérési típusát is használhatja, és a `T` a következő típusok bármelyike lehet:

* Egy, JSON-ként szerializálható objektum
* `string`
* `byte[]`
* [CloudQueueMessage] 

Ha `CloudQueueMessage`hoz próbál kötni, és hibaüzenetet kap, ellenőrizze, hogy rendelkezik-e [a megfelelő Storage SDK-verzióra](functions-bindings-storage-queue.md#azure-storage-sdk-version-in-functions-1x)mutató hivatkozással.

A C# és C# a parancsfájlban több üzenetsor-üzenetet is írhat a következő típusok egyikének használatával: 

* `ICollector<T>` vagy `IAsyncCollector<T>`
* [CloudQueue](/dotnet/api/microsoft.azure.storage.queue.cloudqueue)

# <a name="c-script"></a>[C#Parancsfájl](#tab/csharp-script)

Egyetlen üzenetsor-üzenetet írhat egy metódus-paraméter (például `out T paramName`) használatával. A `paramName` a *function. json*`name` tulajdonságában megadott érték. `out` paraméter helyett a metódus visszatérési típusát is használhatja, és a `T` a következő típusok bármelyike lehet:

* Egy, JSON-ként szerializálható objektum
* `string`
* `byte[]`
* [CloudQueueMessage] 

Ha `CloudQueueMessage`hoz próbál kötni, és hibaüzenetet kap, ellenőrizze, hogy rendelkezik-e [a megfelelő Storage SDK-verzióra](functions-bindings-storage-queue.md#azure-storage-sdk-version-in-functions-1x)mutató hivatkozással.

A C# és C# a parancsfájlban több üzenetsor-üzenetet is írhat a következő típusok egyikének használatával: 

* `ICollector<T>` vagy `IAsyncCollector<T>`
* [CloudQueue](/dotnet/api/microsoft.azure.storage.queue.cloudqueue)

# <a name="javascript"></a>[JavaScript](#tab/javascript)

A kimeneti várólista elem `context.bindings.<NAME>`on keresztül érhető el, ahol a `<NAME>` megegyezik a *function. JSON*fájlban megadott névvel. A várólista-elem hasznos adatainak karakterláncot vagy JSON-szerializálható objektumot is használhat.

# <a name="python"></a>[Python](#tab/python)

Az Event hub-üzenetek egy függvényből való üzembe helyezésének két lehetősége van:

- **Visszatérési érték**: állítsa be az `name` tulajdonságot a *function. json* fájlban a `$return`. Ezzel a konfigurációval a függvény visszatérési értéke üzenetsor-tárolási üzenetként is megmarad.

- **Elengedhetetlen**: adjon meg egy értéket a [set](https://docs.microsoft.com/python/api/azure-functions/azure.functions.out?view=azure-python#set-val--t-----none) metódusnak, amely [kimenő](https://docs.microsoft.com/python/api/azure-functions/azure.functions.out?view=azure-python) típusként van deklarálva. Az `set`nek átadott értéket üzenetsor-tárolóként megőrzi a rendszer.

# <a name="java"></a>[Java](#tab/java)

Az Event hub-üzenetek egy függvényből való üzembe helyezésére két lehetőség áll rendelkezésre a [QueueOutput](https://docs.microsoft.com/java/api/com.microsoft.azure.functions.annotation.queueoutput) jegyzet használatával:

- Visszaadott **érték**: Ha a jegyzetet a függvényhez alkalmazza, a függvény visszatérési értéke Event hub-üzenetként marad.

- **Fontos**: Ha explicit módon be szeretné állítani az üzenet értékét, alkalmazza a jegyzetet a [`OutputBinding<T>`](https://docs.microsoft.com/java/api/com.microsoft.azure.functions.OutputBinding)típusú adott paraméterre, ahol a `T` POJO vagy bármely natív Java-típus. Ezzel a konfigurációval a `setValue` metódus értékének átadásával az érték az Event hub üzenete marad.

---

## <a name="exceptions-and-return-codes"></a>Kivételek és a visszatérési kódok

| Kötés |  Referencia |
|---|---|
| Várólista | [Üzenetsor-hibakódok](https://docs.microsoft.com/rest/api/storageservices/queue-service-error-codes) |
| Blob, Table, Queue | [Tárolási hibakódok](https://docs.microsoft.com/rest/api/storageservices/fileservices/common-rest-api-error-codes) |
| Blob, Table, Queue |  [Hibaelhárítás](https://docs.microsoft.com/rest/api/storageservices/fileservices/troubleshooting-api-operations) |

<a name="host-json"></a>  

## <a name="hostjson-settings"></a>Host.JSON-beállítások

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

- [Függvény futtatása üzenetsor-tárolási adatváltozásként (trigger)](./functions-bindings-storage-queue-trigger.md)

<!-- LINKS -->

[CloudQueueMessage]: /dotnet/api/microsoft.azure.storage.queue.cloudqueuemessage
