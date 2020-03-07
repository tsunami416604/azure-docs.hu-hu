---
title: A 2. x függvények kimeneti kötésének Azure Cosmos DB
description: Megtudhatja, hogyan használhatja a Azure Functions Azure Cosmos DB kimeneti kötését.
author: craigshoemaker
ms.topic: reference
ms.date: 02/24/2020
ms.author: cshoe
ms.openlocfilehash: 9360a90b457f99cb9c15deda80dce8233069100d
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/05/2020
ms.locfileid: "78355762"
---
# <a name="azure-cosmos-db-output-binding-for-azure-functions-2x"></a>Azure Functions 2. x kimeneti kötésének Azure Cosmos DB

Az Azure Cosmos DB kimeneti kötés lehetővé teszi, hogy egy Azure Cosmos DB-adatbázishoz az SQL API használatával írhat egy új dokumentumot.

További információ a telepítésről és a konfigurációról: [Áttekintés](./functions-bindings-cosmosdb-v2.md).

<a id="example" name="example"></a>

# <a name="c"></a>[C#](#tab/csharp)

Ez a szakasz tartalmazza az alábbi példák:

* [Üzenetsor-trigger, egy doc írása](#queue-trigger-write-one-doc-c)
* [Üzenetsor-trigger, dokumentumok írása a IAsyncCollector használatával](#queue-trigger-write-docs-using-iasynccollector-c)

A példák egy egyszerű `ToDoItem` típusra vonatkoznak:

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

### <a name="queue-trigger-write-one-doc"></a>Üzenetsor eseményindító írási egy dokumentumot

Az alábbi példa egy olyan [ C# függvényt](functions-dotnet-class-library.md) mutat be, amely egy dokumentumot ad hozzá egy adatbázishoz, a várólista-tárolóból származó adatok felhasználásával.

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

### <a name="queue-trigger-write-docs-using-iasynccollector"></a>Üzenetsor eseményindító írási docs IAsyncCollector használatával

Az alábbi példa egy olyan [ C# függvényt](functions-dotnet-class-library.md) mutat be, amely dokumentumok gyűjteményét adja hozzá egy adatbázishoz egy üzenetsor-üzenet JSON-fájljában megadott adat használatával.

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

# <a name="c-script"></a>[C#Parancsfájl](#tab/csharp-script)

Ez a szakasz tartalmazza az alábbi példák:

* [Üzenetsor-trigger, egy doc írása](#queue-trigger-write-one-doc-c-script)
* [Üzenetsor-trigger, dokumentumok írása a IAsyncCollector használatával](#queue-trigger-write-docs-using-iasynccollector-c-script)


<a id="queue-trigger-write-one-doc-c-script"></a>

### <a name="queue-trigger-write-one-doc"></a>Üzenetsor eseményindító írási egy dokumentumot

Az alábbi példa egy Azure Cosmos db kimeneti kötést mutat be egy *function. JSON* fájlban, valamint egy olyan [ C# parancsfájl-függvényt](functions-reference-csharp.md) , amely a kötést használja. A függvény egy bemeneti várólista-kötést használja egy üzenetsor, amely megkapja a JSON a következő formátumban:

```json
{
    "name": "John Henry",
    "employeeId": "123456",
    "address": "A town nearby"
}
```

A függvény minden egyes rekord a következő formátumban hozza létre az Azure Cosmos DB-dokumentumot:

```json
{
    "id": "John Henry-123456",
    "name": "John Henry",
    "employeeId": "123456",
    "address": "A town nearby"
}
```

Itt található a *function. JSON* fájlban található kötési adat:

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

A [konfigurációs](#configuration) szakasz ezeket a tulajdonságokat ismerteti.

Íme a C#-szkriptkódot:

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

### <a name="queue-trigger-write-docs-using-iasynccollector"></a>Üzenetsor eseményindító írási docs IAsyncCollector használatával

Több dokumentum létrehozásához kötni `ICollector<T>` vagy `IAsyncCollector<T>`, ahol a `T` a támogatott típusok egyike.

Ez a példa egy egyszerű `ToDoItem` típusra hivatkozik:

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

Íme a function.json fájlban:

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

Íme a C#-szkriptkódot:

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

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Az alábbi példa egy Azure Cosmos DB kimeneti kötést mutat be egy *function. JSON* fájlban, valamint egy [JavaScript-függvényt](functions-reference-node.md) , amely a kötést használja. A függvény egy bemeneti várólista-kötést használja egy üzenetsor, amely megkapja a JSON a következő formátumban:

```json
{
    "name": "John Henry",
    "employeeId": "123456",
    "address": "A town nearby"
}
```

A függvény minden egyes rekord a következő formátumban hozza létre az Azure Cosmos DB-dokumentumot:

```json
{
    "id": "John Henry-123456",
    "name": "John Henry",
    "employeeId": "123456",
    "address": "A town nearby"
}
```

Itt található a *function. JSON* fájlban található kötési adat:

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

A [konfigurációs](#configuration) szakasz ezeket a tulajdonságokat ismerteti.

A következő JavaScript-kódot:

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

Az alábbi példa azt mutatja be, hogyan írhat dokumentumokat egy Azure CosmosDB-adatbázisba egy függvény kimenete.

A kötési definíció a *function. JSON* fájlban van definiálva, ahol a *Type* értéke `cosmosDB`.

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

Az adatbázisba való íráshoz adjon át egy dokumentum-objektumot az adatbázis-paraméter `set` metódusának.

```python
import azure.functions as func

def main(req: func.HttpRequest, doc: func.Out[func.Document]) -> func.HttpResponse:

    request_body = req.get_body()

    doc.set(func.Document.from_json(request_body))

    return 'OK'
```

# <a name="java"></a>[Java](#tab/java)

* [Üzenetsor-trigger, üzenet mentése az adatbázisba visszatérési érték használatával](#queue-trigger-save-message-to-database-via-return-value-java)
* [HTTP-trigger, egy dokumentum mentése az adatbázisba visszatérési érték használatával](#http-trigger-save-one-document-to-database-via-return-value-java)
* [HTTP-trigger, egy dokumentum mentése az adatbázisba a OutputBinding használatával](#http-trigger-save-one-document-to-database-via-outputbinding-java)
* [HTTP-trigger, több dokumentum mentése az adatbázisba a OutputBinding használatával](#http-trigger-save-multiple-documents-to-database-via-outputbinding-java)


<a id="queue-trigger-save-message-to-database-via-return-value-java"></a>

### <a name="queue-trigger-save-message-to-database-via-return-value"></a>Üzenetsor-trigger, üzenet mentése az adatbázisba visszatérési érték használatával

Az alábbi példa egy Java-függvényt mutat be, amely egy dokumentumot hoz létre egy, a várólista-tárolóban lévő üzenetből származó adatokkal rendelkező adatbázishoz.

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

#### <a name="http-trigger-save-one-document-to-database-via-return-value"></a>HTTP-trigger, egy dokumentum mentése az adatbázisba visszatérési érték használatával

Az alábbi példa egy Java-függvényt mutat be, amelynek aláírása ```@CosmosDBOutput```, és ```String```típusú visszatérési értékkel rendelkezik. A függvény által visszaadott JSON-dokumentum automatikusan a megfelelő CosmosDB-gyűjteménybe lesz írva.

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

### <a name="http-trigger-save-one-document-to-database-via-outputbinding"></a>HTTP-trigger, egy dokumentum mentése az adatbázisba a OutputBinding használatával

Az alábbi példa egy Java-függvényt mutat be, amely egy ```OutputBinding<T>``` kimeneti paraméter használatával ír egy dokumentumot a CosmosDB. Ebben a példában a ```outputItem``` paramétert ```@CosmosDBOutput```, nem pedig a függvény aláírásával kell megadnia. A ```OutputBinding<T>``` használata lehetővé teszi, hogy a függvény kihasználhassa a dokumentumot a CosmosDB, miközben lehetővé teszi, hogy más értéket adjanak vissza a függvény hívójának, például egy JSON-vagy XML-dokumentumnak.

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

### <a name="http-trigger-save-multiple-documents-to-database-via-outputbinding"></a>HTTP-trigger, több dokumentum mentése az adatbázisba a OutputBinding használatával

Az alábbi példa egy Java-függvényt mutat be, amely egy ```OutputBinding<T>``` kimeneti paraméterrel több dokumentumot ír a CosmosDB. Ebben a példában a ```outputItem``` paraméter ```@CosmosDBOutput```, nem pedig a függvény aláírásával van megjegyzetve. A kimeneti paraméter a ```outputItem``` ```ToDoItem``` objektumok listáját tartalmazza, mint a sablon paraméterének típusa. A ```OutputBinding<T>``` használata lehetővé teszi, hogy a függvény kihasználhassa a dokumentumokat a CosmosDB, miközben lehetővé teszi, hogy más értéket adjanak vissza a függvény hívójának, például egy JSON-vagy XML-dokumentumnak.

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

A [Java functions runtime library](/java/api/overview/azure/functions/runtime)-ben használja a `@CosmosDBOutput` megjegyzéseket a Cosmos DBba írandó paraméterekhez.  A Megjegyzés paraméterének típusának ```OutputBinding<T>```nak kell lennie, ahol a T egy natív Java-típus vagy egy POJO.

---

## <a name="attributes-and-annotations"></a>Attribútumok és jegyzetek

# <a name="c"></a>[C#](#tab/csharp)

Az [ C# osztályok könyvtáraiban](functions-dotnet-class-library.md)használja a [CosmosDB](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/v2.x/master/WebJobs.Extensions.CosmosDB/CosmosDBAttribute.cs) attribútumot.

Az attribútum konstruktorának paramétereként meg az adatbázis és gyűjtemény nevét. További információ ezekről a beállításokról és a konfigurálható egyéb tulajdonságokról: [kimeneti konfiguráció](#configuration). Példa `CosmosDB` attribútumra a metódus-aláírásban:

```csharp
    [FunctionName("QueueToDocDB")]
    public static void Run(
        [QueueTrigger("myqueue-items", Connection = "AzureWebJobsStorage")] string myQueueItem,
        [CosmosDB("ToDoList", "Items", Id = "id", ConnectionStringSetting = "myCosmosDB")] out dynamic document)
    {
        ...
    }
```

# <a name="c-script"></a>[C#Parancsfájl](#tab/csharp-script)

Az C# attribútumokat a parancsfájl nem támogatja.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

A JavaScript nem támogatja az attribútumokat.

# <a name="python"></a>[Python](#tab/python)

A Python nem támogatja az attribútumokat.

# <a name="java"></a>[Java](#tab/java)

A `CosmosDBOutput` jegyzet elérhető az adatCosmos DBba való íráshoz. A jegyzetet alkalmazhatja a függvényre vagy egy bizonyos Function paraméterre. A Function metódus használata esetén a függvény visszatérési értéke a Cosmos DBba írt érték. Ha a jegyzetet paraméterrel használja, a paraméter típusát olyan `OutputBinding<T>` kell deklarálni, amelyben `T` egy natív Java-típust vagy egy POJO.

---

## <a name="configuration"></a>Konfiguráció

Az alábbi táblázat a *function. JSON* fájlban és a `CosmosDB` attribútumban beállított kötési konfigurációs tulajdonságokat ismerteti.

|Function.JSON tulajdonság | Attribútum tulajdonsága |Leírás|
|---------|---------|----------------------|
|**type**     | n/a | `cosmosDB`értékre kell állítani.        |
|**direction**     | n/a | `out`értékre kell állítani.         |
|**név**     | n/a | A kötési paraméter, amely a dokumentumot, a függvény neve.  |
|**databaseName** | **DatabaseName**|Az a gyűjtemény, amelyben létrehozza a dokumentumban tartalmazó adatbázis.     |
|**collectionName** |**CollectionName**  | A gyűjtemény, amelyben létrehozza a dokumentumban neve. |
|**Createifnotexists metódust**  |**Createifnotexists metódust**    | Logikai érték jelzi, hogy a gyűjtemény létrehozásakor a rendszer még nem létezik. Az alapértelmezett érték a *false (hamis* ), mert az új gyűjtemények fenntartott átviteli sebességgel jönnek létre, ami a költségeket is érinti. További tájékoztatás a [díjszabási lapon](https://azure.microsoft.com/pricing/details/cosmos-db/) olvasható.  |
|**partitionKey**|**PartitionKey** |Ha a `CreateIfNotExists` értéke TRUE (igaz), akkor a létrehozott gyűjtemény partíciós kulcsának elérési útját határozza meg.|
|**collectionThroughput**|**CollectionThroughput**| Ha a `CreateIfNotExists` értéke igaz, meghatározza a létrehozott gyűjtemény [átviteli sebességét](../cosmos-db/set-throughput.md) .|
|**connectionStringSetting**    |**ConnectionStringSetting** |Az Azure Cosmos DB kapcsolati sztringjét tartalmazó alkalmazásbeállítás neve.        |

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="usage"></a>Használat

Alapértelmezés szerint ha a függvényben a kimeneti paraméter írni egy dokumentum létrehozása az adatbázisban. Ebben a dokumentumban az automatikusan előállított GUID Azonosítóhoz megegyezik a dokumentum azonosítója. A kimeneti dokumentumhoz tartozó dokumentum AZONOSÍTÓjának megadásához adja meg a kimeneti paraméternek átadott JSON-objektum `id` tulajdonságát.

> [!Note]
> Létező dokumentum Azonosítóját adja meg, ha azt az új kimeneti dokumentum szerint felülíródik.

## <a name="exceptions-and-return-codes"></a>Kivételek és a visszatérési kódok

| Kötés | Referencia |
|---|---|
| CosmosDB | [CosmosDB-hibakódok](https://docs.microsoft.com/rest/api/cosmos-db/http-status-codes-for-cosmosdb) |

<a name="host-json"></a>

## <a name="hostjson-settings"></a>Host.JSON-beállítások

Ez a szakasz ismerteti a globális konfigurációs beállításoknak a kötéshez verziójában elérhető 2.x. A 2. x verziójú globális konfigurációs beállításokkal kapcsolatos további információkért lásd: [Host. JSON-dokumentáció Azure functions 2. x verzióhoz](functions-host-json.md).

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
|GatewayMode|Átjáró|A függvény által a Azure Cosmos DB szolgáltatáshoz való csatlakozáskor használt kapcsolati mód. A lehetőségek `Direct` és `Gateway`|
|Protokoll|Https|A függvény által a Azure Cosmos DB szolgáltatáshoz való kapcsolódáskor használt kapcsolati protokoll.  A [két mód magyarázata itt](../cosmos-db/performance-tips.md#networking) olvasható|
|leasePrefix|n/a|Az alkalmazás összes függvényében használandó bérlet-előtag.|

## <a name="next-steps"></a>Következő lépések

- [Függvény futtatása Azure Cosmos DB dokumentum létrehozásakor vagy módosításakor (trigger)](./functions-bindings-cosmosdb-v2-trigger.md)
- [Azure Cosmos DB dokumentum olvasása (bemeneti kötés)](./functions-bindings-cosmosdb-v2-input.md)