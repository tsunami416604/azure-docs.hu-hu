---
title: Az Azure Cosmos DB kimeneti kötése a Functions 2.x-hez
description: Ismerje meg az Azure Cosmos DB kimeneti kötés használatát az Azure Functionsben.
author: craigshoemaker
ms.topic: reference
ms.date: 02/24/2020
ms.author: cshoe
ms.openlocfilehash: 636903c20e07f11a2fd919654cfaa62037171f20
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79277764"
---
# <a name="azure-cosmos-db-output-binding-for-azure-functions-2x"></a>Az Azure Cosmos DB kimeneti kötése az Azure Functions 2.x-hez

Az Azure Cosmos DB kimeneti kötés lehetővé teszi, hogy írjon egy új dokumentumot egy Azure Cosmos DB adatbázis az SQL API használatával.

A beállítással és a konfigurációval kapcsolatos részletekről az [áttekintésben](./functions-bindings-cosmosdb-v2.md)olvashat.

<a id="example" name="example"></a>

# <a name="c"></a>[C #](#tab/csharp)

Ez a szakasz a következő példákat tartalmazza:

* [Várólista-eseményindító, egy dokumentum írása](#queue-trigger-write-one-doc-c)
* [Várólista-eseményindító, írási dokumentumok az IAsyncCollector használatával](#queue-trigger-write-docs-using-iasynccollector-c)

A példák egy `ToDoItem` egyszerű típusra hivatkoznak:

```cs
namespace CosmosDBSamplesV2
{
    public class ToDoItem
    {
        public string Id { get; set; }
        public string Description { get; set; }
    }
}
```

<a id="queue-trigger-write-one-doc-c"></a>

### <a name="queue-trigger-write-one-doc"></a>Várólista-eseményindító, egy dokumentum írása

A következő példa egy [C# függvényt](functions-dotnet-class-library.md) mutat be, amely egy dokumentumot ad hozzá az adatbázishoz a Várólista-tárolóból származó üzenetben megadott adatok felhasználásával.

```cs
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Host;
using Microsoft.Extensions.Logging;
using System;

namespace CosmosDBSamplesV2
{
    public static class WriteOneDoc
    {
        [FunctionName("WriteOneDoc")]
        public static void Run(
            [QueueTrigger("todoqueueforwrite")] string queueMessage,
            [CosmosDB(
                databaseName: "ToDoItems",
                collectionName: "Items",
                ConnectionStringSetting = "CosmosDBConnection")]out dynamic document,
            ILogger log)
        {
            document = new { Description = queueMessage, id = Guid.NewGuid() };

            log.LogInformation($"C# Queue trigger function inserted one row");
            log.LogInformation($"Description={queueMessage}");
        }
    }
}
```

<a id="queue-trigger-write-docs-using-iasynccollector-c"></a>

### <a name="queue-trigger-write-docs-using-iasynccollector"></a>Várólista-eseményindító, írási dokumentumok az IAsyncCollector használatával

A következő példa egy [C# függvényt](functions-dotnet-class-library.md) mutat be, amely dokumentumok gyűjteményét adja hozzá egy adatbázishoz a JSON üzenetben megadott adatok használatával.

```cs
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Host;
using System.Threading.Tasks;
using Microsoft.Extensions.Logging;

namespace CosmosDBSamplesV2
{
    public static class WriteDocsIAsyncCollector
    {
        [FunctionName("WriteDocsIAsyncCollector")]
        public static async Task Run(
            [QueueTrigger("todoqueueforwritemulti")] ToDoItem[] toDoItemsIn,
            [CosmosDB(
                databaseName: "ToDoItems",
                collectionName: "Items",
                ConnectionStringSetting = "CosmosDBConnection")]
                IAsyncCollector<ToDoItem> toDoItemsOut,
            ILogger log)
        {
            log.LogInformation($"C# Queue trigger function processed {toDoItemsIn?.Length} items");

            foreach (ToDoItem toDoItem in toDoItemsIn)
            {
                log.LogInformation($"Description={toDoItem.Description}");
                await toDoItemsOut.AddAsync(toDoItem);
            }
        }
    }
}
```

# <a name="c-script"></a>[C# parancsfájl](#tab/csharp-script)

Ez a szakasz a következő példákat tartalmazza:

* [Várólista-eseményindító, egy dokumentum írása](#queue-trigger-write-one-doc-c-script)
* [Várólista-eseményindító, írási dokumentumok az IAsyncCollector használatával](#queue-trigger-write-docs-using-iasynccollector-c-script)


<a id="queue-trigger-write-one-doc-c-script"></a>

### <a name="queue-trigger-write-one-doc"></a>Várólista-eseményindító, egy dokumentum írása

A következő példa egy Azure Cosmos DB kimeneti kötést mutat be egy *function.json* fájlban és egy [C# parancsfájl függvényt,](functions-reference-csharp.md) amely a kötést használja. A függvény várólista bemeneti kötést használ egy olyan várólistához, amely a JSON-t a következő formátumban fogadja:

```json
{
    "name": "John Henry",
    "employeeId": "123456",
    "address": "A town nearby"
}
```

A függvény az Azure Cosmos DB-dokumentumokat a következő formátumban hozza létre az egyes rekordokhoz:

```json
{
    "id": "John Henry-123456",
    "name": "John Henry",
    "employeeId": "123456",
    "address": "A town nearby"
}
```

A *function.json* fájlban a kötési adatok:

```json
{
    "name": "employeeDocument",
    "type": "cosmosDB",
    "databaseName": "MyDatabase",
    "collectionName": "MyCollection",
    "createIfNotExists": true,
    "connectionStringSetting": "MyAccount_COSMOSDB",
    "direction": "out"
}
```

A [konfigurációs](#configuration) szakasz ismerteti ezeket a tulajdonságokat.

Itt a C# script kód:

```cs
    #r "Newtonsoft.Json"

    using Microsoft.Azure.WebJobs.Host;
    using Newtonsoft.Json.Linq;
    using Microsoft.Extensions.Logging;

    public static void Run(string myQueueItem, out object employeeDocument, ILogger log)
    {
      log.LogInformation($"C# Queue trigger function processed: {myQueueItem}");

      dynamic employee = JObject.Parse(myQueueItem);

      employeeDocument = new {
        id = employee.name + "-" + employee.employeeId,
        name = employee.name,
        employeeId = employee.employeeId,
        address = employee.address
      };
    }
```

<a id="queue-trigger-write-docs-using-iasynccollector-c-script"></a>

### <a name="queue-trigger-write-docs-using-iasynccollector"></a>Várólista-eseményindító, írási dokumentumok az IAsyncCollector használatával

Több dokumentum létrehozásához kötésre `ICollector<T>` `IAsyncCollector<T>` lehet, vagy ahol `T` az egyik támogatott típus található.

Ez a példa egy `ToDoItem` egyszerű típusra vonatkozik:

```cs
namespace CosmosDBSamplesV2
{
    public class ToDoItem
    {
        public string Id { get; set; }
        public string Description { get; set; }
    }
}
```

Itt a function.json fájl:

```json
{
  "bindings": [
    {
      "name": "toDoItemsIn",
      "type": "queueTrigger",
      "direction": "in",
      "queueName": "todoqueueforwritemulti",
      "connectionStringSetting": "AzureWebJobsStorage"
    },
    {
      "type": "cosmosDB",
      "name": "toDoItemsOut",
      "databaseName": "ToDoItems",
      "collectionName": "Items",
      "connectionStringSetting": "CosmosDBConnection",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

Itt a C# script kód:

```cs
using System;
using Microsoft.Extensions.Logging;

public static async Task Run(ToDoItem[] toDoItemsIn, IAsyncCollector<ToDoItem> toDoItemsOut, ILogger log)
{
    log.LogInformation($"C# Queue trigger function processed {toDoItemsIn?.Length} items");

    foreach (ToDoItem toDoItem in toDoItemsIn)
    {
        log.LogInformation($"Description={toDoItem.Description}");
        await toDoItemsOut.AddAsync(toDoItem);
    }
}
```

# <a name="javascript"></a>[Javascript](#tab/javascript)

A következő példa egy Azure Cosmos DB kimeneti kötést mutat be egy *function.json* fájlban és egy [JavaScript-függvényt,](functions-reference-node.md) amely a kötést használja. A függvény várólista bemeneti kötést használ egy olyan várólistához, amely a JSON-t a következő formátumban fogadja:

```json
{
    "name": "John Henry",
    "employeeId": "123456",
    "address": "A town nearby"
}
```

A függvény az Azure Cosmos DB-dokumentumokat a következő formátumban hozza létre az egyes rekordokhoz:

```json
{
    "id": "John Henry-123456",
    "name": "John Henry",
    "employeeId": "123456",
    "address": "A town nearby"
}
```

A *function.json* fájlban a kötési adatok:

```json
{
    "name": "employeeDocument",
    "type": "cosmosDB",
    "databaseName": "MyDatabase",
    "collectionName": "MyCollection",
    "createIfNotExists": true,
    "connectionStringSetting": "MyAccount_COSMOSDB",
    "direction": "out"
}
```

A [konfigurációs](#configuration) szakasz ismerteti ezeket a tulajdonságokat.

Itt a JavaScript-kód:

```javascript
    module.exports = function (context) {

      context.bindings.employeeDocument = JSON.stringify({
        id: context.bindings.myQueueItem.name + "-" + context.bindings.myQueueItem.employeeId,
        name: context.bindings.myQueueItem.name,
        employeeId: context.bindings.myQueueItem.employeeId,
        address: context.bindings.myQueueItem.address
      });

      context.done();
    };
```

# <a name="python"></a>[Python](#tab/python)

A következő példa bemutatja, hogyan írhat egy dokumentumot egy Azure CosmosDB-adatbázisba egy függvény kimeneteként.

A kötésdefiníció a *function.json* *type* ban van `cosmosDB`definiálva, ahol a típus a .

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
      "type": "cosmosDB",
      "direction": "out",
      "name": "doc",
      "databaseName": "demodb",
      "collectionName": "data",
      "createIfNotExists": "true",
      "connectionStringSetting": "AzureCosmosDBConnectionString"
    },
    {
      "type": "http",
      "direction": "out",
      "name": "$return"
    }
  ]
}
```

Ha írni szeretne az adatbázisba, `set` adja át a dokumentumobjektumot az adatbázis-paraméter metódusának.

```python
import azure.functions as func

def main(req: func.HttpRequest, doc: func.Out[func.Document]) -> func.HttpResponse:

    request_body = req.get_body()

    doc.set(func.Document.from_json(request_body))

    return 'OK'
```

# <a name="java"></a>[Java](#tab/java)

* [Várólista-eseményindító, üzenet mentése az adatbázisba visszatérési értéken keresztül](#queue-trigger-save-message-to-database-via-return-value-java)
* [HTTP-eseményindító, egy dokumentum mentése adatbázisba visszatérési értéken keresztül](#http-trigger-save-one-document-to-database-via-return-value-java)
* [HTTP-eseményindító, egy dokumentum mentése adatbázisba a OutputBinding segítségével](#http-trigger-save-one-document-to-database-via-outputbinding-java)
* [HTTP-eseményindító, több dokumentum mentése adatbázisba az OutputBinding segítségével](#http-trigger-save-multiple-documents-to-database-via-outputbinding-java)


<a id="queue-trigger-save-message-to-database-via-return-value-java"></a>

### <a name="queue-trigger-save-message-to-database-via-return-value"></a>Várólista-eseményindító, üzenet mentése az adatbázisba visszatérési értéken keresztül

A következő példa egy Java-függvényt mutat be, amely egy dokumentumot ad hozzá egy adatbázishoz a Várólista tárolójában lévő üzenetből származó adatokkal.

```java
@FunctionName("getItem")
@CosmosDBOutput(name = "database",
  databaseName = "ToDoList",
  collectionName = "Items",
  connectionStringSetting = "AzureCosmosDBConnection")
public String cosmosDbQueryById(
    @QueueTrigger(name = "msg",
      queueName = "myqueue-items",
      connection = "AzureWebJobsStorage")
    String message,
    final ExecutionContext context)  {
     return "{ id: \"" + System.currentTimeMillis() + "\", Description: " + message + " }";
   }
```
<a id="http-trigger-save-one-document-to-database-via-return-value-java"></a>

#### <a name="http-trigger-save-one-document-to-database-via-return-value"></a>HTTP-eseményindító, egy dokumentum mentése adatbázisba visszatérési értéken keresztül

A következő példa egy Java-függvényt mutat ```@CosmosDBOutput``` be, amelynek ```String```aláírásával van elvan tévik, és visszatérési értéke . A függvény által visszaadott JSON-dokumentum automatikusan a megfelelő CosmosDB-gyűjteménybe kerül.

```java
    @FunctionName("WriteOneDoc")
    @CosmosDBOutput(name = "database",
      databaseName = "ToDoList",
      collectionName = "Items",
      connectionStringSetting = "Cosmos_DB_Connection_String")
    public String run(
            @HttpTrigger(name = "req",
              methods = {HttpMethod.GET, HttpMethod.POST},
              authLevel = AuthorizationLevel.ANONYMOUS)
            HttpRequestMessage<Optional<String>> request,
            final ExecutionContext context) {

        // Item list
        context.getLogger().info("Parameters are: " + request.getQueryParameters());

        // Parse query parameter
        String query = request.getQueryParameters().get("desc");
        String name = request.getBody().orElse(query);

        // Generate random ID
        final int id = Math.abs(new Random().nextInt());

        // Generate document
        final String jsonDocument = "{\"id\":\"" + id + "\", " +
                                    "\"description\": \"" + name + "\"}";

        context.getLogger().info("Document to be saved: " + jsonDocument);

        return jsonDocument;
    }
```

<a id="http-trigger-save-one-document-to-database-via-outputbinding-java"></a>

### <a name="http-trigger-save-one-document-to-database-via-outputbinding"></a>HTTP-eseményindító, egy dokumentum mentése adatbázisba a OutputBinding segítségével

A következő példa egy Java függvényt mutat be, ```OutputBinding<T>``` amely egy kimeneti paraméteren keresztül írja a dokumentumot a CosmosDB-nek. Ebben a példában a paramétert ```outputItem``` a, ```@CosmosDBOutput```a paraméterrel kell eljegyzetálni, nem pedig a függvény aláírásával. Használata ```OutputBinding<T>``` lehetővé teszi, hogy a függvény kihasználhatja a kötés a dokumentum írása a CosmosDB, miközben lehetővé teszi, hogy egy másik értéket a függvény hívó, például egy JSON vagy XML-dokumentum.

```java
    @FunctionName("WriteOneDocOutputBinding")
    public HttpResponseMessage run(
            @HttpTrigger(name = "req",
              methods = {HttpMethod.GET, HttpMethod.POST},
              authLevel = AuthorizationLevel.ANONYMOUS)
            HttpRequestMessage<Optional<String>> request,
            @CosmosDBOutput(name = "database",
              databaseName = "ToDoList",
              collectionName = "Items",
              connectionStringSetting = "Cosmos_DB_Connection_String")
            OutputBinding<String> outputItem,
            final ExecutionContext context) {

        // Parse query parameter
        String query = request.getQueryParameters().get("desc");
        String name = request.getBody().orElse(query);

        // Item list
        context.getLogger().info("Parameters are: " + request.getQueryParameters());

        // Generate random ID
        final int id = Math.abs(new Random().nextInt());

        // Generate document
        final String jsonDocument = "{\"id\":\"" + id + "\", " +
                                    "\"description\": \"" + name + "\"}";

        context.getLogger().info("Document to be saved: " + jsonDocument);

        // Set outputItem's value to the JSON document to be saved
        outputItem.setValue(jsonDocument);

        // return a different document to the browser or calling client.
        return request.createResponseBuilder(HttpStatus.OK)
                      .body("Document created successfully.")
                      .build();
    }
```

<a id="http-trigger-save-multiple-documents-to-database-via-outputbinding-java"></a>

### <a name="http-trigger-save-multiple-documents-to-database-via-outputbinding"></a>HTTP-eseményindító, több dokumentum mentése adatbázisba az OutputBinding segítségével

A következő példa egy Java függvényt mutat be, ```OutputBinding<T>``` amely több dokumentumot ír a CosmosDB-nek egy kimeneti paraméteren keresztül. Ebben a példában a ```outputItem``` paraméter a, a paraméterrel ```@CosmosDBOutput```van elhangzva, nem pedig a függvény aláírásával. A kimeneti ```outputItem``` paraméter sablonparaméter-típusa az ```ToDoItem``` objektumok listájával rendelkezik. Használata ```OutputBinding<T>``` lehetővé teszi, hogy a függvény kihasználhatja a kötés a dokumentumok írása a CosmosDB, miközben lehetővé teszi, hogy egy másik értéket a függvény hívó, például egy JSON vagy XML-dokumentum.

```java
    @FunctionName("WriteMultipleDocsOutputBinding")
    public HttpResponseMessage run(
            @HttpTrigger(name = "req",
              methods = {HttpMethod.GET, HttpMethod.POST},
              authLevel = AuthorizationLevel.ANONYMOUS)
            HttpRequestMessage<Optional<String>> request,
            @CosmosDBOutput(name = "database",
              databaseName = "ToDoList",
              collectionName = "Items",
              connectionStringSetting = "Cosmos_DB_Connection_String")
            OutputBinding<List<ToDoItem>> outputItem,
            final ExecutionContext context) {

        // Parse query parameter
        String query = request.getQueryParameters().get("desc");
        String name = request.getBody().orElse(query);

        // Item list
        context.getLogger().info("Parameters are: " + request.getQueryParameters());

        // Generate documents
        List<ToDoItem> items = new ArrayList<>();

        for (int i = 0; i < 5; i ++) {
          // Generate random ID
          final int id = Math.abs(new Random().nextInt());

          // Create ToDoItem
          ToDoItem item = new ToDoItem(String.valueOf(id), name);

          items.add(item);
        }

        // Set outputItem's value to the list of POJOs to be saved
        outputItem.setValue(items);
        context.getLogger().info("Document to be saved: " + items);

        // return a different document to the browser or calling client.
        return request.createResponseBuilder(HttpStatus.OK)
                      .body("Documents created successfully.")
                      .build();
    }
```

A [Java függvények futásidejű könyvtárában](/java/api/overview/azure/functions/runtime)használja a `@CosmosDBOutput` Cosmos DB-nek írt paraméterek jegyzetelését.  A jegyzetparaméter típusának ```OutputBinding<T>```a legyen , ahol T vagy natív Java-típus vagy POJO.

---

## <a name="attributes-and-annotations"></a>Attribútumok és jegyzetek

# <a name="c"></a>[C #](#tab/csharp)

A [C# osztálykönyvtárakban](functions-dotnet-class-library.md)használja a [CosmosDB](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/v2.x/master/WebJobs.Extensions.CosmosDB/CosmosDBAttribute.cs) attribútumot.

Az attribútum konstruktora felveszi az adatbázis nevét és a gyűjtemény nevét. A beállításokkal és a konfigurálható egyéb tulajdonságokkal kapcsolatos további tudnivalókért olvassa el [a Kimenet - konfiguráció](#configuration)című témakört. Íme egy `CosmosDB` attribútum példa egy metódus aláírás:

```csharp
    [FunctionName("QueueToDocDB")]
    public static void Run(
        [QueueTrigger("myqueue-items", Connection = "AzureWebJobsStorage")] string myQueueItem,
        [CosmosDB("ToDoList", "Items", Id = "id", ConnectionStringSetting = "myCosmosDB")] out dynamic document)
    {
        ...
    }
```

# <a name="c-script"></a>[C# parancsfájl](#tab/csharp-script)

Az attribútumokat a C# script nem támogatja.

# <a name="javascript"></a>[Javascript](#tab/javascript)

A JavaScript nem támogatja az attribútumokat.

# <a name="python"></a>[Python](#tab/python)

Az attribútumokat a Python nem támogatja.

# <a name="java"></a>[Java](#tab/java)

A `CosmosDBOutput` jegyzet a Cosmos DB-nek történő adatok írásához érhető el. A jegyzetet alkalmazhatja a függvényre vagy egy egyedi függvényparaméterre. A függvénymetódus, a függvény visszatérési értéke, amit a Cosmos DB írása. Ha a jegyzetet paraméterrel használja, a paraméter típusát olyan `OutputBinding<T>` helyként `T` kell deklarálni, ahol natív Java-típus vagy POJO.

---

## <a name="configuration"></a>Konfiguráció

Az alábbi táblázat a *function.json* fájlban és az `CosmosDB` attribútumban beállított kötési konfigurációs tulajdonságokat ismerteti.

|function.json tulajdonság | Attribútum tulajdonság |Leírás|
|---------|---------|----------------------|
|**Típus**     | n/a | A beállításnak `cosmosDB`a beállítására kell beállítható.        |
|**direction**     | n/a | A beállításnak `out`a beállítására kell beállítható.         |
|**név**     | n/a | A függvényben lévő dokumentumot jelölő kötési paraméter neve.  |
|**adatbázisneve** | **DatabaseName**|A dokumentumot létrehozó gyűjteményt tartalmazó adatbázis.     |
|**Lekérdezés_neve** |**CollectionName**  | Annak a gyűjteménynek a neve, amelyben a dokumentumot létrehozták. |
|**createIfNotExists**  |**CreateIfNotExists**    | Logikai érték, amely azt jelzi, hogy a gyűjtemény akkor jön-e létre, ha nem létezik. Az alapértelmezett *hamis,* mert az új gyűjtemények jönnek létre a fenntartott átviteli, amelynek költségvonzatai vannak. További tájékoztatás a [díjszabási lapon](https://azure.microsoft.com/pricing/details/cosmos-db/) olvasható.  |
|**partíciókulcs**|**PartitionKey** |Ha `CreateIfNotExists` igaz, meghatározza a létrehozott gyűjtemény partíciókulcs elérési útját.|
|**kollekcióThroughput**|**CollectionThroughput (Gyűjteményátmenő)**| Ha `CreateIfNotExists` igaz, meghatározza a létrehozott gyűjtemény [átviteli.](../cosmos-db/set-throughput.md)|
|**connectionStringSetting**    |**ConnectionStringSetting (Kapcsolatkarakterlánc-beállítás)** |Az Azure Cosmos DB kapcsolati karakterláncot tartalmazó alkalmazásbeállítás neve.        |
|**preferredLocations (elsődleges helyek)**| **PreferredLocations (PreferredLocations)**| (Nem kötelező) Az Azure Cosmos DB szolgáltatás georeplikált adatbázis-fiókjainak előnyben részesített helyeit (régióit) határozza meg. Az értékeket vesszővel kell elválasztani. Például: "USA keleti régiója, USA déli középső régiója, Észak-Európa". |
|**useMultipleWriteLocations (Többwrite–helyek használata)**| **UseMultipleWriteLocations (Többwrite–hely használata)**| (Nem kötelező) Ha a `true` beállítás `PreferredLocations`a használatával együtt, akkor az Azure Cosmos DB szolgáltatás [többrégiós írási](../cosmos-db/how-to-manage-database-account.md#configure-multiple-write-regions) műveleteket is kihasználhatja. |

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="usage"></a>Használat

Alapértelmezés szerint, amikor a függvény kimeneti paraméterére ír, egy dokumentum jön létre az adatbázisban. Ez a dokumentum automatikusan létrehozott GUID azonosítóval rendelkezik. A kimeneti dokumentum dokumentumazonosítóját a kimeneti `id` paraméternek átadott JSON-objektum tulajdonságának megadásával adhatja meg.

> [!Note]
> Amikor megadja egy meglévő dokumentum azonosítóját, az új kimeneti dokumentum felülírja azt.

## <a name="exceptions-and-return-codes"></a>Kivételek és visszaküldési kódok

| Kötés | Referencia |
|---|---|
| CosmosDB | [CosmosDB hibakódok](https://docs.microsoft.com/rest/api/cosmos-db/http-status-codes-for-cosmosdb) |

<a name="host-json"></a>

## <a name="hostjson-settings"></a>host.json beállítások

Ez a szakasz a kötéshez a 2.x-es verzióban elérhető globális konfigurációs beállításokat ismerteti. A 2.x-es verzió globális konfigurációs beállításairól az [Azure Functions 2.x-es verziójának host.json-referencia című témakörében](functions-host-json.md)olvashat bővebben.

```json
{
    "version": "2.0",
    "extensions": {
        "cosmosDB": {
            "connectionMode": "Gateway",
            "protocol": "Https",
            "leaseOptions": {
                "leasePrefix": "prefix1"
            }
        }
    }
}
```

|Tulajdonság  |Alapértelmezett | Leírás |
|---------|---------|---------|
|GatewayMode (Átjáró mód)|Átjáró|A függvény által az Azure Cosmos DB szolgáltatáshoz való csatlakozáskor használt csatlakozási mód. A `Direct` lehetőségek a következők: és`Gateway`|
|Protocol (Protokoll)|Https (https)|A függvény által használt kapcsolati protokoll, amikor az Azure Cosmos DB szolgáltatáshoz való csatlakozáskor.  Olvassa el [itt a magyarázata mindkét mód](../cosmos-db/performance-tips.md#networking)|
|leasePrefix|n/a|Az alkalmazás összes függvényében használható bérletelőtag.|

## <a name="next-steps"></a>További lépések

- [Egy függvény futtatása, ha egy Azure Cosmos DB-dokumentum jön létre vagy módosul (Trigger)](./functions-bindings-cosmosdb-v2-trigger.md)
- [Azure Cosmos DB-dokumentum olvasása (bemeneti kötés)](./functions-bindings-cosmosdb-v2-input.md)