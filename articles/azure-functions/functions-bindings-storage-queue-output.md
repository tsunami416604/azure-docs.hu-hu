---
title: Azure Queue storage-kimenetkötés az Azure Functionshez
description: Ismerje meg, hogyan hozhat létre Azure Queue storage üzeneteket az Azure Functionsben.
author: craigshoemaker
ms.topic: reference
ms.date: 02/18/2020
ms.author: cshoe
ms.custom: cc996988-fb4f-47
ms.openlocfilehash: 76af5f398edd736874fa79095f2e80c02298eac0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79277335"
---
# <a name="azure-queue-storage-output-bindings-for-azure-functions"></a>Az Azure Functions Azure-várólistára helyezett tárolási kimeneti kötései

Az Azure Functions új Azure Queue storage-üzeneteket hozhat létre egy kimeneti kötés beállításával.

A beállítással és a konfigurációval kapcsolatos részletekről az [áttekintésben](./functions-bindings-storage-queue.md)olvashat.

## <a name="example"></a>Példa

# <a name="c"></a>[C #](#tab/csharp)

A következő példa egy [C# függvényt](functions-dotnet-class-library.md) mutat be, amely minden fogadott HTTP-kérelemhez létrehoz egy üzenetsor-üzenetet.

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

# <a name="c-script"></a>[C# parancsfájl](#tab/csharp-script)

A következő példa egy *FÜGGVÉNY.json* fájlhttp-eseményindítóját és a kötést használó [C# parancsfájl (.csx)](functions-reference-csharp.md) kódot mutatja be. A függvény létrehoz egy várólistaelemet **egy CustomQueueMessage** objektum tartalommal minden fogadott HTTP-kérelemhez.

Itt a *function.json* fájl:

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

A [konfigurációs](#configuration) szakasz ismerteti ezeket a tulajdonságokat.

Itt a C# parancsfájlkód, amely egyetlen üzenetüzenetjön:

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

Egy `ICollector` vagy `IAsyncCollector` paraméter használatával egyszerre több üzenetet is küldhet. Itt a C# parancsfájlkód, amely több üzenetet küld, az egyik a HTTP-kérelem adataival, a egyik pedig a kódolt értékekkel rendelkezik:

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

# <a name="javascript"></a>[Javascript](#tab/javascript)

A következő példa egy HTTP-eseményindítót mutat be egy *function.json* fájlban, és egy [JavaScript függvényt,](functions-reference-node.md) amely a kötést használja. A függvény minden fogadott HTTP-kérelemhez létrehoz egy várólistaelemet.

Itt a *function.json* fájl:

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

A [konfigurációs](#configuration) szakasz ismerteti ezeket a tulajdonságokat.

Itt a JavaScript-kód:

```javascript
module.exports = function (context, input) {
    context.done(null, input.body);
};
```

Egyszerre több üzenetet is küldhet, ha a `myQueueItem` kimeneti kötéshez egy üzenettömböt határoz meg. A következő JavaScript-kód két várólista-üzenetet küld, kódolt értékekkel minden fogadott HTTP-kérelemhez.

```javascript
module.exports = function(context) {
    context.bindings.myQueueItem = ["message 1","message 2"];
    context.done();
};
```

# <a name="python"></a>[Python](#tab/python)

A következő példa bemutatja, hogyan lehet egy- és több értéket kiadni a tárolási várólistákba. A *function.json-hoz* szükséges konfiguráció mindkét irányban azonos.

A storage-várólista-kötés definiálva van a *function.json* ban, ahol a *típus* a. `queue`

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

Ha egyéni üzenetet szeretne beállítani a várólistán, `set` egyetlen értéket kell átadnia a metódusnak.

```python
import azure.functions as func

def main(req: func.HttpRequest, msg: func.Out[str]) -> func.HttpResponse:

    input_msg = req.params.get('message')

    msg.set(input_msg)

    return 'OK'
```

Ha több üzenetet szeretne létrehozni a várólistán, deklarálja a paramétert megfelelő listatípusként, és adja át a metódusnak egy `set` értéktömböt (amely megfelel a listatípusnak).

```python
import azure.functions as func
import typing

def main(req: func.HttpRequest, msg: func.Out[typing.List[str]]) -> func.HttpResponse:

    msg.set(['one', 'two'])

    return 'OK'
```

# <a name="java"></a>[Java](#tab/java)

 A következő példa egy Java-függvényt mutat be, amely egy várólista-üzenetet hoz létre, ha http-kérelem váltja ki.

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

A [Java függvények futásidejű könyvtárában](/java/api/overview/azure/functions/runtime)használja a jegyzetet azon `@QueueOutput` paramétereken, amelyek értéke a várólista-tárolóba íródik.  A paramétertípusnak `OutputBinding<T>`a `T` : "Hol van a POJO bármely natív Java típusa).The parameter type should be , , where is any native Java type of a POJO.

---

## <a name="attributes-and-annotations"></a>Attribútumok és jegyzetek

# <a name="c"></a>[C #](#tab/csharp)

A [C# osztálytárakban](functions-dotnet-class-library.md)használja a [QueueAttribute attribútumot.](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.Extensions.Storage/Queues/QueueAttribute.cs)

Az attribútum egy `out` paraméterre vagy a függvény visszatérési értékére vonatkozik. Az attribútum konstruktora felveszi a várólista nevét, ahogy az a következő példában látható:

```csharp
[FunctionName("QueueOutput")]
[return: Queue("myqueue-items")]
public static string Run([HttpTrigger] dynamic input,  ILogger log)
{
    ...
}
```

Beállíthatja, `Connection` hogy a tulajdonság adja meg a tárfiókot használni, ahogy az a következő példában látható:

```csharp
[FunctionName("QueueOutput")]
[return: Queue("myqueue-items", Connection = "StorageConnectionAppSetting")]
public static string Run([HttpTrigger] dynamic input,  ILogger log)
{
    ...
}
```

A teljes példa: [Kimenet i](#example).

Az `StorageAccount` attribútum segítségével megadhatja a tárfiókot osztály, metódus vagy paraméter szinten. További információ: Trigger - attribútumok.

# <a name="c-script"></a>[C# parancsfájl](#tab/csharp-script)

Az attribútumokat a C# script nem támogatja.

# <a name="javascript"></a>[Javascript](#tab/javascript)

A JavaScript nem támogatja az attribútumokat.

# <a name="python"></a>[Python](#tab/python)

Az attribútumokat a Python nem támogatja.

# <a name="java"></a>[Java](#tab/java)

A `QueueOutput` jegyzet lehetővé teszi, hogy egy függvény kimeneteként írjon egy üzenetet. A következő példa egy HTTP-aktivált függvényt mutat be, amely várólista-üzenetet hoz létre.

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
|`name`       | Deklarálja a paraméter nevét a függvényaláírásban. A függvény aktiválásakor a paraméter értéke a várólistaüzenet tartalmával rendelkezik. |
|`queueName`  | Deklarálja a várólista nevét a tárfiókban. |
|`connection` | A tárfiók kapcsolati karakterláncára mutat. |

A `QueueOutput` jegyzethez társított paraméter [\<outputkötési T-példányként\> ](https://github.com/Azure/azure-functions-java-library/blob/master/src/main/java/com/microsoft/azure/functions/OutputBinding.java) van beírva.

---

## <a name="configuration"></a>Konfiguráció

Az alábbi táblázat a *function.json* fájlban és az `Queue` attribútumban beállított kötési konfigurációs tulajdonságokat ismerteti.

|function.json tulajdonság | Attribútum tulajdonság |Leírás|
|---------|---------|----------------------|
|**Típus** | n/a | A beállításnak `queue`a beállítására kell beállítható. Ez a tulajdonság automatikusan be van állítva, amikor létrehozza az eseményindítót az Azure Portalon.|
|**direction** | n/a | A beállításnak `out`a beállítására kell beállítható. Ez a tulajdonság automatikusan be van állítva, amikor létrehozza az eseményindítót az Azure Portalon. |
|**név** | n/a | Annak a változónak a neve, amely a várólistát jelöli a függvénykódban. A `$return` függvény visszatérési értékére való hivatkozás beállítása.|
|**queueName (sornév)** |**Várólistaneve** | A várólista neve. |
|**Kapcsolat** | **Kapcsolat** |A kötéshez használandó Storage-kapcsolati karakterláncot tartalmazó alkalmazásbeállítás neve. Ha az alkalmazásbeállítás neve "AzureWebJobs" programmal kezdődik, itt csak a név fennmaradó részét adhatja meg. Ha például a `connection` "MyStorage" beállítást állítja be, a Functions futásidejű megkeresi a "MyStorage" nevű alkalmazásbeállítást. Ha üresen hagyja, `connection` a Functions futásidejű az alapértelmezett Storage-kapcsolati karakterláncot használja a neve súgás `AzureWebJobsStorage`alkalmazásbeállításban.|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="usage"></a>Használat

# <a name="c"></a>[C #](#tab/csharp)

Írjon egyetlen üzenetsor-üzenetet egy `out T paramName`metódusparaméter használatával, például . `out` Paraméter helyett használhatja a metódus visszatérési `T` típusát, és a következő típusok bármelyike lehet:

* JSON-ként szerializálható objektum
* `string`
* `byte[]`
* [CloudQueueMessage üzenet] 

Ha megpróbál kötődni, `CloudQueueMessage` és hibaüzenetet kap, győződjön meg arról, hogy [rendelkezik a megfelelő Storage SDK-verzióra](functions-bindings-storage-queue.md#azure-storage-sdk-version-in-functions-1x)mutató hivatkozással.

A C# és a C# parancsfájlban írjon több várólista-üzenetet az alábbi típusok egyikével: 

* `ICollector<T>` vagy `IAsyncCollector<T>`
* [CloudQueue között](/dotnet/api/microsoft.azure.storage.queue.cloudqueue)

# <a name="c-script"></a>[C# parancsfájl](#tab/csharp-script)

Írjon egyetlen üzenetsor-üzenetet egy `out T paramName`metódusparaméter használatával, például . A `paramName` a `name` *function.json*tulajdonságában megadott érték. `out` Paraméter helyett használhatja a metódus visszatérési `T` típusát, és a következő típusok bármelyike lehet:

* JSON-ként szerializálható objektum
* `string`
* `byte[]`
* [CloudQueueMessage üzenet] 

Ha megpróbál kötődni, `CloudQueueMessage` és hibaüzenetet kap, győződjön meg arról, hogy [rendelkezik a megfelelő Storage SDK-verzióra](functions-bindings-storage-queue.md#azure-storage-sdk-version-in-functions-1x)mutató hivatkozással.

A C# és a C# parancsfájlban írjon több várólista-üzenetet az alábbi típusok egyikével: 

* `ICollector<T>` vagy `IAsyncCollector<T>`
* [CloudQueue között](/dotnet/api/microsoft.azure.storage.queue.cloudqueue)

# <a name="javascript"></a>[Javascript](#tab/javascript)

A kimeneti várólista elem elérhető, `context.bindings.<NAME>` ahol `<NAME>` megegyezik a *function.json*ban megadott névvel. A várólistacikk-tartalomhoz karakterláncot vagy JSON-szerializálható objektumot használhat.

# <a name="python"></a>[Python](#tab/python)

Az Event Hub-üzenetek függvényből való kihirdetésére két lehetőség van:

- **Visszatérési érték** `name` : Állítsa a *function.json* tulajdonságát a értékre. `$return` Ezzel a konfigurációval a függvény visszatérési értéke várólista-tárolási üzenetként marad meg.

- **Elengedhetetlen:** Adja át [az](https://docs.microsoft.com/python/api/azure-functions/azure.functions.out?view=azure-python#set-val--t-----none) értéket a megadott metódusa a paraméter deklarált [out](https://docs.microsoft.com/python/api/azure-functions/azure.functions.out?view=azure-python) típus. Az átadott `set` érték várólista-tárolási üzenetként marad meg.

# <a name="java"></a>[Java](#tab/java)

Az Event Hub-üzenetek nek a [QueueOutput](https://docs.microsoft.com/java/api/com.microsoft.azure.functions.annotation.queueoutput) megjegyzéshasználatával kétféleképpen lehet kiküldeni egy függvényből:

- **Visszatérési érték**: Ha a notációt magára a függvényre alkalmazza, a függvény visszatérési értéke Eseményközpont-üzenetként marad meg.

- **Elengedhetetlen**: Az üzenet értékének explicit beállításához alkalmazza a megjegyzéseket [`OutputBinding<T>`](https://docs.microsoft.com/java/api/com.microsoft.azure.functions.OutputBinding)a `T` típus egy adott paraméterére, ahol POJO vagy bármely natív Java típus. Ezzel a konfigurációval egy `setValue` érték átadása a metódus nak megőrzi az értéket, mint egy Event Hub-üzenet.

---

## <a name="exceptions-and-return-codes"></a>Kivételek és visszaküldési kódok

| Kötés |  Referencia |
|---|---|
| Várólista | [Várólista-hibakódok](https://docs.microsoft.com/rest/api/storageservices/queue-service-error-codes) |
| Blob, Tábla, Várólista | [Tárolási hibakódok](https://docs.microsoft.com/rest/api/storageservices/fileservices/common-rest-api-error-codes) |
| Blob, Tábla, Várólista |  [hibaelhárítással](https://docs.microsoft.com/rest/api/storageservices/fileservices/troubleshooting-api-operations) |

<a name="host-json"></a>  

## <a name="hostjson-settings"></a>host.json beállítások

Ez a szakasz a kötéshez a 2.x-es vagy újabb verziókban elérhető globális konfigurációs beállításokat ismerteti. Az alábbi host.json példafájl csak a kötés 2.x+ verzióját tartalmazza. A 2.x-es és az azt meghaladó verziók globális konfigurációs beállításairól az [Azure Functions host.json referencia című témakörében](functions-host-json.md)olvashat bővebben.

> [!NOTE]
> A host.json 1.x függvényében a [host.json témakörben az Azure Functions 1.x host.json hivatkozása](functions-host-json-v1.md)látható.

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
|maxPollingInterval érték|00:00:01|A várólista-szavazások közötti maximális időtartam. A minimum 00:00:00.100 (100 ms) és 00:01:00 (1 min) lépésekben.  1.x-ben az adattípus ezredmásodperc, 2.x vagy újabb verzióban pedig TimeSpan.|
|láthatóságTimeout|00:00:00|Az újrapróbálkozások közötti időintervallum, amikor az üzenet feldolgozása sikertelen. |
|batchSize|16|A Functions futásidejű várólistán lévő üzenetek száma, amelyeket a Függvények futásidejű lehív és párhuzamosan dolgoz fel. Amikor a feldolgozott szám lesz `newBatchThreshold`a, a futásidejű kap egy másik köteget, és megkezdi az üzenetek feldolgozását. Így a függvényenként feldolgozott egyidejű üzenetek maximális `batchSize` `newBatchThreshold`száma plusz . Ez a korlát külön-külön vonatkozik az egyes várólista által aktivált függvények. <br><br>Ha el szeretné kerülni az egy várólistán fogadott `batchSize` üzenetek párhuzamos végrehajtását, állítsa be az 1-et. Ez a beállítás azonban csak addig szünteti meg az egyidejűséget, amíg a függvényalkalmazás egyetlen virtuális gépen (VM) fut. Ha a függvényalkalmazás több virtuális gépre skálázódik, minden virtuális gép minden egyes várólista által aktivált függvény egy példányát futtathatja.<br><br>A `batchSize` maximális érték 32. |
|maxDequeueCount (maxDequeueCount)|5|Az üzenetek feldolgozása a méregvárólistába való áthelyezés e sorokba való áthelyezése előtt többször is.|
|újBatchThreshold|batchSize/2|Amikor a feldolgozás alatt álló üzenetek száma egyidejűleg lesz le erre a számra, a futásidejű lekéri egy másik köteget.|

## <a name="next-steps"></a>További lépések

- [Függvény futtatása várólista-tárolási adatok változásakor (Eseményindító)](./functions-bindings-storage-queue-trigger.md)

<!-- LINKS -->

[CloudQueueMessage üzenet]: /dotnet/api/microsoft.azure.storage.queue.cloudqueuemessage
