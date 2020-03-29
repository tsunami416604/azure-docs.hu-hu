---
title: Az Azure Cosmos DB bemeneti kötése a Functions 2.x-hez
description: Ismerje meg az Azure Cosmos DB bemeneti kötés használatát az Azure Functionsben.
author: craigshoemaker
ms.topic: reference
ms.date: 02/24/2020
ms.author: cshoe
ms.openlocfilehash: eabcf40e28927919215979ccc46fa029d19adbfe
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78943426"
---
# <a name="azure-cosmos-db-input-binding-for-azure-functions-2x"></a>Az Azure Cosmos DB bemeneti kötése az Azure Functions 2.x-hez

Az Azure Cosmos DB bemeneti adatkötése az SQL API-t használja egy vagy több Azure Cosmos DB-dokumentum lekérésére, majd átadja ezeket a függvény bemeneti paraméterének. A dokumentumazonosítót vagy lekérdezési paramétereket a függvényt meghívó eseményindító alapján lehet meghatározni.

A beállítással és a konfigurációval kapcsolatos részletekről az [áttekintésben](./functions-bindings-cosmosdb-v2.md)olvashat.

> [!NOTE]
> Ha a gyűjtemény [particionálva](../cosmos-db/partition-data.md#logical-partitions)van, a kapcsolatfelvételi műveleteknek meg kell adniuk a partíciókulcs értékét is.
>

<a id="example" name="example"></a>

# <a name="c"></a>[C #](#tab/csharp)

Ez a szakasz a következő példákat tartalmazza:

* [Várólista-eseményindító, a JSON azonosítójának megnézése](#queue-trigger-look-up-id-from-json-c)
* [HTTP-eseményindító, azonosító kinézete a lekérdezési karakterláncból](#http-trigger-look-up-id-from-query-string-c)
* [HTTP-eseményindító, azonosító kinézete az útvonaladatokból](#http-trigger-look-up-id-from-route-data-c)
* [HTTP-eseményindító, azonosító kinézete az útvonaladatokból az SqlQuery használatával](#http-trigger-look-up-id-from-route-data-using-sqlquery-c)
* [HTTP-eseményindító, több dokumentum beszereznie az SqlQuery használatával](#http-trigger-get-multiple-docs-using-sqlquery-c)
* [HTTP-eseményindító, több dokumentum beszereznie a DocumentClient használatával](#http-trigger-get-multiple-docs-using-documentclient-c)

A példák egy `ToDoItem` egyszerű típusra hivatkoznak:

```cs
namespace CosmosDBSamplesV2
{
    public class ToDoItem
    {
        public string Id { get; set; }
        public string PartitionKey { get; set; }
        public string Description { get; set; }
    }
}
```

<a id="queue-trigger-look-up-id-from-json-c"></a>

### <a name="queue-trigger-look-up-id-from-json"></a>Várólista-eseményindító, a JSON azonosítójának megnézése 

A következő példa egy [C# függvényt](functions-dotnet-class-library.md) mutat be, amely egyetlen dokumentumot olvas be. A függvényt egy JSON-objektumot tartalmazó üzenetsor-üzenet váltja ki. A várólista-eseményindító a JSON-t egy típusú `ToDoItemLookup`objektumba elemzi, amely tartalmazza a kikeresni az azonosítót és a partíciókulcs értékét. Az azonosító és a partíciókulcs értéke `ToDoItem` a megadott adatbázisból és gyűjteményből származó dokumentumok lekéréséhez használatos.

```cs
namespace CosmosDBSamplesV2
{
    public class ToDoItemLookup
    {
        public string ToDoItemId { get; set; }

        public string ToDoItemPartitionKeyValue { get; set; }
    }
}
```

```cs
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Host;
using Microsoft.Extensions.Logging;

namespace CosmosDBSamplesV2
{
    public static class DocByIdFromJSON
    {
        [FunctionName("DocByIdFromJSON")]
        public static void Run(
            [QueueTrigger("todoqueueforlookup")] ToDoItemLookup toDoItemLookup,
            [CosmosDB(
                databaseName: "ToDoItems",
                collectionName: "Items",
                ConnectionStringSetting = "CosmosDBConnection",
                Id = "{ToDoItemId}",
                PartitionKey = "{ToDoItemPartitionKeyValue}")]ToDoItem toDoItem,
            ILogger log)
        {
            log.LogInformation($"C# Queue trigger function processed Id={toDoItemLookup?.ToDoItemId} Key={toDoItemLookup?.ToDoItemPartitionKeyValue}");

            if (toDoItem == null)
            {
                log.LogInformation($"ToDo item not found");
            }
            else
            {
                log.LogInformation($"Found ToDo item, Description={toDoItem.Description}");
            }
        }
    }
}
```

<a id="http-trigger-look-up-id-from-query-string-c"></a>

### <a name="http-trigger-look-up-id-from-query-string"></a>HTTP-eseményindító, azonosító kinézete a lekérdezési karakterláncból

A következő példa egy [C# függvényt](functions-dotnet-class-library.md) mutat be, amely egyetlen dokumentumot olvas be. A függvényt egy HTTP-kérelem váltja ki, amely egy lekérdezési karakterláncot használ a megkeresni kívánt azonosító és partíciókulcs értékének megadásához. Az azonosító és a partíciókulcs értéke `ToDoItem` a megadott adatbázisból és gyűjteményből származó dokumentumok lekéréséhez használatos.

>[!NOTE]
>A HTTP-lekérdezési karakterlánc paraméter a kis- és nagybetűk et is figyelembe kell.
>

```cs
using Microsoft.AspNetCore.Http;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.Http;
using Microsoft.Azure.WebJobs.Host;
using Microsoft.Extensions.Logging;

namespace CosmosDBSamplesV2
{
    public static class DocByIdFromQueryString
    {
        [FunctionName("DocByIdFromQueryString")]
        public static IActionResult Run(
            [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post", Route = null)]
                HttpRequest req,
            [CosmosDB(
                databaseName: "ToDoItems",
                collectionName: "Items",
                ConnectionStringSetting = "CosmosDBConnection",
                Id = "{Query.id}",
                PartitionKey = "{Query.partitionKey}")] ToDoItem toDoItem,
            ILogger log)
        {
            log.LogInformation("C# HTTP trigger function processed a request.");

            if (toDoItem == null)
            {
                log.LogInformation($"ToDo item not found");
            }
            else
            {
                log.LogInformation($"Found ToDo item, Description={toDoItem.Description}");
            }
            return new OkResult();
        }
    }
}
```

<a id="http-trigger-look-up-id-from-route-data-c"></a>

### <a name="http-trigger-look-up-id-from-route-data"></a>HTTP-eseményindító, azonosító kinézete az útvonaladatokból

A következő példa egy [C# függvényt](functions-dotnet-class-library.md) mutat be, amely egyetlen dokumentumot olvas be. A függvényt egy HTTP-kérelem váltja ki, amely útvonaladatok segítségével adja meg a megkeresni kívánt azonosítót és partíciókulcs-értéket. Az azonosító és a partíciókulcs értéke `ToDoItem` a megadott adatbázisból és gyűjteményből származó dokumentumok lekéréséhez használatos.

```cs
using Microsoft.AspNetCore.Http;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.Http;
using Microsoft.Azure.WebJobs.Host;
using Microsoft.Extensions.Logging;

namespace CosmosDBSamplesV2
{
    public static class DocByIdFromRouteData
    {
        [FunctionName("DocByIdFromRouteData")]
        public static IActionResult Run(
            [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post",
                Route = "todoitems/{partitionKey}/{id}")]HttpRequest req,
            [CosmosDB(
                databaseName: "ToDoItems",
                collectionName: "Items",
                ConnectionStringSetting = "CosmosDBConnection",
                Id = "{id}",
                PartitionKey = "{partitionKey}")] ToDoItem toDoItem,
            ILogger log)
        {
            log.LogInformation("C# HTTP trigger function processed a request.");

            if (toDoItem == null)
            {
                log.LogInformation($"ToDo item not found");
            }
            else
            {
                log.LogInformation($"Found ToDo item, Description={toDoItem.Description}");
            }
            return new OkResult();
        }
    }
}
```

<a id="http-trigger-look-up-id-from-route-data-using-sqlquery-c"></a>

### <a name="http-trigger-look-up-id-from-route-data-using-sqlquery"></a>HTTP-eseményindító, azonosító kinézete az útvonaladatokból az SqlQuery használatával

A következő példa egy [C# függvényt](functions-dotnet-class-library.md) mutat be, amely egyetlen dokumentumot olvas be. A függvényt egy HTTP-kérelem váltja ki, amely útvonaladatokat használ a megkeresni kívánt azonosító megadásához. Ez az azonosító a `ToDoItem` megadott adatbázisból és gyűjteményből származó dokumentumok lekéréséhez használható.

A példa bemutatja, hogyan kell `SqlQuery` használni egy kötési kifejezést a paraméterben. Az útvonaladatokat az `SqlQuery` ábrán látható módon továbbíthatja a paraméternek, de jelenleg [nem tud lekérdezési karakterlánc-értékeket átadni.](https://github.com/Azure/azure-functions-host/issues/2554#issuecomment-392084583)

> [!NOTE]
> Ha csak az azonosítóval kell lekérdeznie, ajánlott az előző [példákhoz](#http-trigger-look-up-id-from-query-string-c)hasonlóan felkeresni, mivel kevesebb [kérésegységet](../cosmos-db/request-units.md)fog használni. A pontolvasási műveletek [more efficient](../cosmos-db/optimize-cost-queries.md) (GET) hatékonyabbak, mint az azonosítóval végzett lekérdezések.
>

```cs
using Microsoft.AspNetCore.Http;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.Http;
using Microsoft.Azure.WebJobs.Host;
using System.Collections.Generic;
using Microsoft.Extensions.Logging;

namespace CosmosDBSamplesV2
{
    public static class DocByIdFromRouteDataUsingSqlQuery
    {
        [FunctionName("DocByIdFromRouteDataUsingSqlQuery")]
        public static IActionResult Run(
            [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post",
                Route = "todoitems2/{id}")]HttpRequest req,
            [CosmosDB("ToDoItems", "Items",
                ConnectionStringSetting = "CosmosDBConnection",
                SqlQuery = "select * from ToDoItems r where r.id = {id}")]
                IEnumerable<ToDoItem> toDoItems,
            ILogger log)
        {
            log.LogInformation("C# HTTP trigger function processed a request.");

            foreach (ToDoItem toDoItem in toDoItems)
            {
                log.LogInformation(toDoItem.Description);
            }
            return new OkResult();
        }
    }
}
```

<a id="http-trigger-get-multiple-docs-using-sqlquery-c"></a>

### <a name="http-trigger-get-multiple-docs-using-sqlquery"></a>HTTP-eseményindító, több dokumentum beszereznie az SqlQuery használatával

A következő példa egy [C# függvényt](functions-dotnet-class-library.md) mutat be, amely beolvassa a dokumentumok listáját. A függvényt egy HTTP-kérelem váltja ki. A lekérdezés az `SqlQuery` attribútumtulajdonságban van megadva.

```cs
using Microsoft.AspNetCore.Http;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.Http;
using Microsoft.Azure.WebJobs.Host;
using System.Collections.Generic;
using Microsoft.Extensions.Logging;

namespace CosmosDBSamplesV2
{
    public static class DocsBySqlQuery
    {
        [FunctionName("DocsBySqlQuery")]
        public static IActionResult Run(
            [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post", Route = null)]
                HttpRequest req,
            [CosmosDB(
                databaseName: "ToDoItems",
                collectionName: "Items",
                ConnectionStringSetting = "CosmosDBConnection",
                SqlQuery = "SELECT top 2 * FROM c order by c._ts desc")]
                IEnumerable<ToDoItem> toDoItems,
            ILogger log)
        {
            log.LogInformation("C# HTTP trigger function processed a request.");
            foreach (ToDoItem toDoItem in toDoItems)
            {
                log.LogInformation(toDoItem.Description);
            }
            return new OkResult();
        }
    }
}

```

<a id="http-trigger-get-multiple-docs-using-documentclient-c"></a>

### <a name="http-trigger-get-multiple-docs-using-documentclient"></a>HTTP-eseményindító, több dokumentum beszereznie a DocumentClient használatával

A következő példa egy [C# függvényt](functions-dotnet-class-library.md) mutat be, amely beolvassa a dokumentumok listáját. A függvényt egy HTTP-kérelem váltja ki. A kód `DocumentClient` az Azure Cosmos DB-kötés által biztosított példányt használja a dokumentumok listájának olvasásához. A `DocumentClient` példány írási műveletekhez is használható.

> [!NOTE]
> Az [IDocumentClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.idocumentclient?view=azure-dotnet) felület segítségével is megkönnyítheti a tesztelést.

```cs
using Microsoft.AspNetCore.Http;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Azure.Documents.Client;
using Microsoft.Azure.Documents.Linq;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.Http;
using Microsoft.Azure.WebJobs.Host;
using Microsoft.Extensions.Logging;
using System;
using System.Linq;
using System.Threading.Tasks;

namespace CosmosDBSamplesV2
{
    public static class DocsByUsingDocumentClient
    {
        [FunctionName("DocsByUsingDocumentClient")]
        public static async Task<IActionResult> Run(
            [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post",
                Route = null)]HttpRequest req,
            [CosmosDB(
                databaseName: "ToDoItems",
                collectionName: "Items",
                ConnectionStringSetting = "CosmosDBConnection")] DocumentClient client,
            ILogger log)
        {
            log.LogInformation("C# HTTP trigger function processed a request.");

            var searchterm = req.Query["searchterm"];
            if (string.IsNullOrWhiteSpace(searchterm))
            {
                return (ActionResult)new NotFoundResult();
            }

            Uri collectionUri = UriFactory.CreateDocumentCollectionUri("ToDoItems", "Items");

            log.LogInformation($"Searching for: {searchterm}");

            IDocumentQuery<ToDoItem> query = client.CreateDocumentQuery<ToDoItem>(collectionUri)
                .Where(p => p.Description.Contains(searchterm))
                .AsDocumentQuery();

            while (query.HasMoreResults)
            {
                foreach (ToDoItem result in await query.ExecuteNextAsync())
                {
                    log.LogInformation(result.Description);
                }
            }
            return new OkResult();
        }
    }
}
```

# <a name="c-script"></a>[C# parancsfájl](#tab/csharp-script)

Ez a szakasz a következő példákat tartalmazza:

* [Várólista-eseményindító, azonosító kinézete a karakterláncból](#queue-trigger-look-up-id-from-string-c-script)
* [Várólista-eseményindító, több dokumentum bekésezése az SqlQuery használatával](#queue-trigger-get-multiple-docs-using-sqlquery-c-script)
* [HTTP-eseményindító, azonosító kinézete a lekérdezési karakterláncból](#http-trigger-look-up-id-from-query-string-c-script)
* [HTTP-eseményindító, azonosító kinézete az útvonaladatokból](#http-trigger-look-up-id-from-route-data-c-script)
* [HTTP-eseményindító, több dokumentum beszereznie az SqlQuery használatával](#http-trigger-get-multiple-docs-using-sqlquery-c-script)
* [HTTP-eseményindító, több dokumentum beszereznie a DocumentClient használatával](#http-trigger-get-multiple-docs-using-documentclient-c-script)

A HTTP-eseményindító példák egy egyszerű `ToDoItem` típusra hivatkoznak:

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

<a id="queue-trigger-look-up-id-from-string-c-script"></a>

### <a name="queue-trigger-look-up-id-from-string"></a>Várólista-eseményindító, azonosító kinézete a karakterláncból

A következő példa egy Cosmos DB bemeneti kötést mutat be egy *function.json* fájlban és egy [C# parancsfájl függvényt,](functions-reference-csharp.md) amely a kötést használja. A függvény egyetlen dokumentumot olvas be, és frissíti a dokumentum szöveges értékét.

A *function.json* fájlban a kötési adatok:

```json
{
    "name": "inputDocument",
    "type": "cosmosDB",
    "databaseName": "MyDatabase",
    "collectionName": "MyCollection",
    "id" : "{queueTrigger}",
    "partitionKey": "{partition key value}",
    "connectionStringSetting": "MyAccount_COSMOSDB",
    "direction": "in"
}
```
A [konfigurációs](#configuration) szakasz ismerteti ezeket a tulajdonságokat.

Itt a C# script kód:

```cs
    using System;

    // Change input document contents using Azure Cosmos DB input binding
    public static void Run(string myQueueItem, dynamic inputDocument)
    {
      inputDocument.text = "This has changed.";
    }
```

<a id="queue-trigger-get-multiple-docs-using-sqlquery-c-script"></a>

### <a name="queue-trigger-get-multiple-docs-using-sqlquery"></a>Várólista-eseményindító, több dokumentum bekésezése az SqlQuery használatával

A következő példa egy Azure Cosmos DB bemeneti kötést mutat be egy *function.json* fájlban és egy [C# parancsfájl függvényt,](functions-reference-csharp.md) amely a kötést használja. A függvény egy SQL-lekérdezés által megadott több dokumentumot olvas be, egy várólista-eseményindítóval a lekérdezési paraméterek testreszabásához.

A várólista-eseményindító paramétert `departmentId`ad meg. A várólista-üzenet `{ "departmentId" : "Finance" }` visszaadja a pénzügyi részleg összes rekordját.

A *function.json* fájlban a kötési adatok:

```json
{
    "name": "documents",
    "type": "cosmosDB",
    "direction": "in",
    "databaseName": "MyDb",
    "collectionName": "MyCollection",
    "sqlQuery": "SELECT * from c where c.departmentId = {departmentId}",
    "connectionStringSetting": "CosmosDBConnection"
}
```

A [konfigurációs](#configuration) szakasz ismerteti ezeket a tulajdonságokat.

Itt a C# script kód:

```csharp
    public static void Run(QueuePayload myQueueItem, IEnumerable<dynamic> documents)
    {
        foreach (var doc in documents)
        {
            // operate on each document
        }
    }

    public class QueuePayload
    {
        public string departmentId { get; set; }
    }
```

<a id="http-trigger-look-up-id-from-query-string-c-script"></a>

### <a name="http-trigger-look-up-id-from-query-string"></a>HTTP-eseményindító, azonosító kinézete a lekérdezési karakterláncból

A következő példa egy [C# parancsfájlfüggvényt](functions-reference-csharp.md) mutat be, amely egyetlen dokumentumot olvas be. A függvényt egy HTTP-kérelem váltja ki, amely egy lekérdezési karakterláncot használ a megkeresni kívánt azonosító és partíciókulcs értékének megadásához. Az azonosító és a partíciókulcs értéke `ToDoItem` a megadott adatbázisból és gyűjteményből származó dokumentumok lekéréséhez használatos.

Itt a *function.json* fájl:

```json
{
  "bindings": [
    {
      "authLevel": "anonymous",
      "name": "req",
      "type": "httpTrigger",
      "direction": "in",
      "methods": [
        "get",
        "post"
      ]
    },
    {
      "name": "$return",
      "type": "http",
      "direction": "out"
    },
    {
      "type": "cosmosDB",
      "name": "toDoItem",
      "databaseName": "ToDoItems",
      "collectionName": "Items",
      "connectionStringSetting": "CosmosDBConnection",
      "direction": "in",
      "Id": "{Query.id}",
      "PartitionKey" : "{Query.partitionKeyValue}"
    }
  ],
  "disabled": false
}
```

Itt a C# script kód:

```cs
using System.Net;
using Microsoft.Extensions.Logging;

public static HttpResponseMessage Run(HttpRequestMessage req, ToDoItem toDoItem, ILogger log)
{
    log.LogInformation("C# HTTP trigger function processed a request.");

    if (toDoItem == null)
    {
         log.LogInformation($"ToDo item not found");
    }
    else
    {
        log.LogInformation($"Found ToDo item, Description={toDoItem.Description}");
    }
    return req.CreateResponse(HttpStatusCode.OK);
}
```

<a id="http-trigger-look-up-id-from-route-data-c-script"></a>

### <a name="http-trigger-look-up-id-from-route-data"></a>HTTP-eseményindító, azonosító kinézete az útvonaladatokból

A következő példa egy [C# parancsfájlfüggvényt](functions-reference-csharp.md) mutat be, amely egyetlen dokumentumot olvas be. A függvényt egy HTTP-kérelem váltja ki, amely útvonaladatok segítségével adja meg a megkeresni kívánt azonosítót és partíciókulcs-értéket. Az azonosító és a partíciókulcs értéke `ToDoItem` a megadott adatbázisból és gyűjteményből származó dokumentumok lekéréséhez használatos.

Itt a *function.json* fájl:

```json
{
  "bindings": [
    {
      "authLevel": "anonymous",
      "name": "req",
      "type": "httpTrigger",
      "direction": "in",
      "methods": [
        "get",
        "post"
      ],
      "route":"todoitems/{partitionKeyValue}/{id}"
    },
    {
      "name": "$return",
      "type": "http",
      "direction": "out"
    },
    {
      "type": "cosmosDB",
      "name": "toDoItem",
      "databaseName": "ToDoItems",
      "collectionName": "Items",
      "connectionStringSetting": "CosmosDBConnection",
      "direction": "in",
      "Id": "{id}",
      "PartitionKey": "{partitionKeyValue}"
    }
  ],
  "disabled": false
}
```

Itt a C# script kód:

```cs
using System.Net;
using Microsoft.Extensions.Logging;

public static HttpResponseMessage Run(HttpRequestMessage req, ToDoItem toDoItem, ILogger log)
{
    log.LogInformation("C# HTTP trigger function processed a request.");

    if (toDoItem == null)
    {
         log.LogInformation($"ToDo item not found");
    }
    else
    {
        log.LogInformation($"Found ToDo item, Description={toDoItem.Description}");
    }
    return req.CreateResponse(HttpStatusCode.OK);
}
```

<a id="http-trigger-get-multiple-docs-using-sqlquery-c-script"></a>

### <a name="http-trigger-get-multiple-docs-using-sqlquery"></a>HTTP-eseményindító, több dokumentum beszereznie az SqlQuery használatával

A következő példa egy [C# parancsfájlfüggvényt](functions-reference-csharp.md) mutat be, amely beolvassa a dokumentumok listáját. A függvényt egy HTTP-kérelem váltja ki. A lekérdezés az `SqlQuery` attribútumtulajdonságban van megadva.

Itt a *function.json* fájl:

```json
{
  "bindings": [
    {
      "authLevel": "anonymous",
      "name": "req",
      "type": "httpTrigger",
      "direction": "in",
      "methods": [
        "get",
        "post"
      ]
    },
    {
      "name": "$return",
      "type": "http",
      "direction": "out"
    },
    {
      "type": "cosmosDB",
      "name": "toDoItems",
      "databaseName": "ToDoItems",
      "collectionName": "Items",
      "connectionStringSetting": "CosmosDBConnection",
      "direction": "in",
      "sqlQuery": "SELECT top 2 * FROM c order by c._ts desc"
    }
  ],
  "disabled": false
}
```

Itt a C# script kód:

```cs
using System.Net;
using Microsoft.Extensions.Logging;

public static HttpResponseMessage Run(HttpRequestMessage req, IEnumerable<ToDoItem> toDoItems, ILogger log)
{
    log.LogInformation("C# HTTP trigger function processed a request.");

    foreach (ToDoItem toDoItem in toDoItems)
    {
        log.LogInformation(toDoItem.Description);
    }
    return req.CreateResponse(HttpStatusCode.OK);
}
```

<a id="http-trigger-get-multiple-docs-using-documentclient-c-script"></a>

### <a name="http-trigger-get-multiple-docs-using-documentclient"></a>HTTP-eseményindító, több dokumentum beszereznie a DocumentClient használatával

A következő példa egy [C# parancsfájlfüggvényt](functions-reference-csharp.md) mutat be, amely beolvassa a dokumentumok listáját. A függvényt egy HTTP-kérelem váltja ki. A kód `DocumentClient` az Azure Cosmos DB-kötés által biztosított példányt használja a dokumentumok listájának olvasásához. A `DocumentClient` példány írási műveletekhez is használható.

Itt a *function.json* fájl:

```json
{
  "bindings": [
    {
      "authLevel": "anonymous",
      "name": "req",
      "type": "httpTrigger",
      "direction": "in",
      "methods": [
        "get",
        "post"
      ]
    },
    {
      "name": "$return",
      "type": "http",
      "direction": "out"
    },
    {
      "type": "cosmosDB",
      "name": "client",
      "databaseName": "ToDoItems",
      "collectionName": "Items",
      "connectionStringSetting": "CosmosDBConnection",
      "direction": "inout"
    }
  ],
  "disabled": false
}
```

Itt a C# script kód:

```cs
#r "Microsoft.Azure.Documents.Client"

using System.Net;
using Microsoft.Azure.Documents.Client;
using Microsoft.Azure.Documents.Linq;
using Microsoft.Extensions.Logging;

public static async Task<HttpResponseMessage> Run(HttpRequestMessage req, DocumentClient client, ILogger log)
{
    log.LogInformation("C# HTTP trigger function processed a request.");

    Uri collectionUri = UriFactory.CreateDocumentCollectionUri("ToDoItems", "Items");
    string searchterm = req.GetQueryNameValuePairs()
        .FirstOrDefault(q => string.Compare(q.Key, "searchterm", true) == 0)
        .Value;

    if (searchterm == null)
    {
        return req.CreateResponse(HttpStatusCode.NotFound);
    }

    log.LogInformation($"Searching for word: {searchterm} using Uri: {collectionUri.ToString()}");
    IDocumentQuery<ToDoItem> query = client.CreateDocumentQuery<ToDoItem>(collectionUri)
        .Where(p => p.Description.Contains(searchterm))
        .AsDocumentQuery();

    while (query.HasMoreResults)
    {
        foreach (ToDoItem result in await query.ExecuteNextAsync())
        {
            log.LogInformation(result.Description);
        }
    }
    return req.CreateResponse(HttpStatusCode.OK);
}
```

# <a name="javascript"></a>[Javascript](#tab/javascript)

Ez a szakasz a következő példákat tartalmazza, amelyek egyetlen dokumentumot olvasnak különböző forrásokból származó azonosítóérték megadásával:

* [Várólista-eseményindító, a JSON azonosítójának megnézése](#queue-trigger-look-up-id-from-json-javascript)
* [HTTP-eseményindító, azonosító kinézete a lekérdezési karakterláncból](#http-trigger-look-up-id-from-query-string-javascript)
* [HTTP-eseményindító, azonosító kinézete az útvonaladatokból](#http-trigger-look-up-id-from-route-data-javascript)
* [Várólista-eseményindító, több dokumentum bekésezése az SqlQuery használatával](#queue-trigger-get-multiple-docs-using-sqlquery-javascript)

<a id="queue-trigger-look-up-id-from-json-javascript"></a>

### <a name="queue-trigger-look-up-id-from-json"></a>Várólista-eseményindító, a JSON azonosítójának megnézése

A következő példa egy Cosmos DB bemeneti kötést mutat be egy *function.json* fájlban és egy [JavaScript függvényt,](functions-reference-node.md) amely a kötést használja. A függvény egyetlen dokumentumot olvas be, és frissíti a dokumentum szöveges értékét.

A *function.json* fájlban a kötési adatok:

```json
{
    "name": "inputDocumentIn",
    "type": "cosmosDB",
    "databaseName": "MyDatabase",
    "collectionName": "MyCollection",
    "id" : "{queueTrigger_payload_property}",
    "partitionKey": "{queueTrigger_payload_property}",
    "connectionStringSetting": "MyAccount_COSMOSDB",
    "direction": "in"
},
{
    "name": "inputDocumentOut",
    "type": "cosmosDB",
    "databaseName": "MyDatabase",
    "collectionName": "MyCollection",
    "createIfNotExists": false,
    "partitionKey": "{queueTrigger_payload_property}",
    "connectionStringSetting": "MyAccount_COSMOSDB",
    "direction": "out"
}
```

A [konfigurációs](#configuration) szakasz ismerteti ezeket a tulajdonságokat.

Itt a JavaScript-kód:

```javascript
    // Change input document contents using Azure Cosmos DB input binding, using context.bindings.inputDocumentOut
    module.exports = function (context) {
    context.bindings.inputDocumentOut = context.bindings.inputDocumentIn;
    context.bindings.inputDocumentOut.text = "This was updated!";
    context.done();
    };
```

<a id="http-trigger-look-up-id-from-query-string-javascript"></a>

### <a name="http-trigger-look-up-id-from-query-string"></a>HTTP-eseményindító, azonosító kinézete a lekérdezési karakterláncból

A következő példa egy [JavaScript-függvényt](functions-reference-node.md) mutat be, amely egyetlen dokumentumot olvas be. A függvényt egy HTTP-kérelem váltja ki, amely egy lekérdezési karakterláncot használ a megkeresni kívánt azonosító és partíciókulcs értékének megadásához. Az azonosító és a partíciókulcs értéke `ToDoItem` a megadott adatbázisból és gyűjteményből származó dokumentumok lekéréséhez használatos.

Itt a *function.json* fájl:

```json
{
  "bindings": [
    {
      "authLevel": "anonymous",
      "name": "req",
      "type": "httpTrigger",
      "direction": "in",
      "methods": [
        "get",
        "post"
      ]
    },
    {
      "name": "$return",
      "type": "http",
      "direction": "out"
    },
    {
      "type": "cosmosDB",
      "name": "toDoItem",
      "databaseName": "ToDoItems",
      "collectionName": "Items",
      "connectionStringSetting": "CosmosDBConnection",
      "direction": "in",
      "Id": "{Query.id}",
      "PartitionKey": "{Query.partitionKeyValue}"
    }
  ],
  "disabled": false
}
```

Itt a JavaScript-kód:

```javascript
module.exports = function (context, req, toDoItem) {
    context.log('JavaScript queue trigger function processed work item');
    if (!toDoItem)
    {
        context.log("ToDo item not found");
    }
    else
    {
        context.log("Found ToDo item, Description=" + toDoItem.Description);
    }

    context.done();
};
```

<a id="http-trigger-look-up-id-from-route-data-javascript"></a>

### <a name="http-trigger-look-up-id-from-route-data"></a>HTTP-eseményindító, azonosító kinézete az útvonaladatokból

A következő példa egy [JavaScript-függvényt](functions-reference-node.md) mutat be, amely egyetlen dokumentumot olvas be. A függvényt egy HTTP-kérelem váltja ki, amely útvonaladatok segítségével adja meg a megkeresni kívánt azonosítót és partíciókulcs-értéket. Az azonosító és a partíciókulcs értéke `ToDoItem` a megadott adatbázisból és gyűjteményből származó dokumentumok lekéréséhez használatos.

Itt a *function.json* fájl:

```json
{
  "bindings": [
    {
      "authLevel": "anonymous",
      "name": "req",
      "type": "httpTrigger",
      "direction": "in",
      "methods": [
        "get",
        "post"
      ],
      "route":"todoitems/{partitionKeyValue}/{id}"
    },
    {
      "name": "$return",
      "type": "http",
      "direction": "out"
    },
    {
      "type": "cosmosDB",
      "name": "toDoItem",
      "databaseName": "ToDoItems",
      "collectionName": "Items",
      "connection": "CosmosDBConnection",
      "direction": "in",
      "Id": "{id}",
      "PartitionKey": "{partitionKeyValue}"
    }
  ],
  "disabled": false
}
```

Itt a JavaScript-kód:

```javascript
module.exports = function (context, req, toDoItem) {
    context.log('JavaScript queue trigger function processed work item');
    if (!toDoItem)
    {
        context.log("ToDo item not found");
    }
    else
    {
        context.log("Found ToDo item, Description=" + toDoItem.Description);
    }

    context.done();
};
```

<a id="queue-trigger-get-multiple-docs-using-sqlquery-javascript"></a>

### <a name="queue-trigger-get-multiple-docs-using-sqlquery"></a>Várólista-eseményindító, több dokumentum bekésezése az SqlQuery használatával

A következő példa egy Azure Cosmos DB bemeneti kötést mutat be egy *function.json* fájlban és egy [JavaScript-függvényt,](functions-reference-node.md) amely a kötést használja. A függvény egy SQL-lekérdezés által megadott több dokumentumot olvas be, egy várólista-eseményindítóval a lekérdezési paraméterek testreszabásához.

A várólista-eseményindító paramétert `departmentId`ad meg. A várólista-üzenet `{ "departmentId" : "Finance" }` visszaadja a pénzügyi részleg összes rekordját.

A *function.json* fájlban a kötési adatok:

```json
{
    "name": "documents",
    "type": "cosmosDB",
    "direction": "in",
    "databaseName": "MyDb",
    "collectionName": "MyCollection",
    "sqlQuery": "SELECT * from c where c.departmentId = {departmentId}",
    "connectionStringSetting": "CosmosDBConnection"
}
```

A [konfigurációs](#configuration) szakasz ismerteti ezeket a tulajdonságokat.

Itt a JavaScript-kód:

```javascript
    module.exports = function (context, input) {
        var documents = context.bindings.documents;
        for (var i = 0; i < documents.length; i++) {
            var document = documents[i];
            // operate on each document
        }
        context.done();
    };
```

# <a name="python"></a>[Python](#tab/python)

Ez a szakasz a következő példákat tartalmazza, amelyek egyetlen dokumentumot olvasnak különböző forrásokból származó azonosítóérték megadásával:

* [Várólista-eseményindító, a JSON azonosítójának megnézése](#queue-trigger-look-up-id-from-json-python)
* [HTTP-eseményindító, azonosító kinézete a lekérdezési karakterláncból](#http-trigger-look-up-id-from-query-string-python)
* [HTTP-eseményindító, azonosító kinézete az útvonaladatokból](#http-trigger-look-up-id-from-route-data-python)
* [Várólista-eseményindító, több dokumentum bekésezése az SqlQuery használatával](#queue-trigger-get-multiple-docs-using-sqlquery-python)

<a id="queue-trigger-look-up-id-from-json-python"></a>

### <a name="queue-trigger-look-up-id-from-json"></a>Várólista-eseményindító, a JSON azonosítójának megnézése

A következő példa egy Cosmos DB bemeneti kötést mutat be egy *function.json* fájlban és egy [Python-függvényt,](functions-reference-python.md) amely a kötést használja. A függvény egyetlen dokumentumot olvas be, és frissíti a dokumentum szöveges értékét.

A *function.json* fájlban a kötési adatok:

```json
{
    "name": "documents",
    "type": "cosmosDB",
    "databaseName": "MyDatabase",
    "collectionName": "MyCollection",
    "id" : "{queueTrigger_payload_property}",
    "partitionKey": "{queueTrigger_payload_property}",
    "connectionStringSetting": "MyAccount_COSMOSDB",
    "direction": "in"
},
{
    "name": "$return",
    "type": "cosmosDB",
    "databaseName": "MyDatabase",
    "collectionName": "MyCollection",
    "createIfNotExists": false,
    "partitionKey": "{queueTrigger_payload_property}",
    "connectionStringSetting": "MyAccount_COSMOSDB",
    "direction": "out"
}
```

A [konfigurációs](#configuration) szakasz ismerteti ezeket a tulajdonságokat.

Itt a Python kód:

```python
import azure.functions as func


def main(queuemsg: func.QueueMessage, documents: func.DocumentList) -> func.Document:
    if documents:
        document = documents[0]
        document['text'] = 'This was updated!'
        return document
```

<a id="http-trigger-look-up-id-from-query-string-python"></a>

### <a name="http-trigger-look-up-id-from-query-string"></a>HTTP-eseményindító, azonosító kinézete a lekérdezési karakterláncból

A következő példa egy [Python-függvényt](functions-reference-python.md) mutat be, amely egyetlen dokumentumot olvas be. A függvényt egy HTTP-kérelem váltja ki, amely egy lekérdezési karakterláncot használ a megkeresni kívánt azonosító és partíciókulcs értékének megadásához. Az azonosító és a partíciókulcs értéke `ToDoItem` a megadott adatbázisból és gyűjteményből származó dokumentumok lekéréséhez használatos.

Itt a *function.json* fájl:

```json
{
  "bindings": [
    {
      "authLevel": "anonymous",
      "name": "req",
      "type": "httpTrigger",
      "direction": "in",
      "methods": [
        "get",
        "post"
      ]
    },
    {
      "name": "$return",
      "type": "http",
      "direction": "out"
    },
    {
      "type": "cosmosDB",
      "name": "todoitems",
      "databaseName": "ToDoItems",
      "collectionName": "Items",
      "connectionStringSetting": "CosmosDBConnection",
      "direction": "in",
      "Id": "{Query.id}",
      "PartitionKey": "{Query.partitionKeyValue}"
    }
  ],
  "disabled": true,
  "scriptFile": "__init__.py"
}
```

Itt a Python kód:

```python
import logging
import azure.functions as func


def main(req: func.HttpRequest, todoitems: func.DocumentList) -> str:
    if not todoitems:
        logging.warning("ToDo item not found")
    else:
        logging.info("Found ToDo item, Description=%s",
                     todoitems[0]['description'])

    return 'OK'
```

<a id="http-trigger-look-up-id-from-route-data-python"></a>

### <a name="http-trigger-look-up-id-from-route-data"></a>HTTP-eseményindító, azonosító kinézete az útvonaladatokból

A következő példa egy [Python-függvényt](functions-reference-python.md) mutat be, amely egyetlen dokumentumot olvas be. A függvényt egy HTTP-kérelem váltja ki, amely útvonaladatok segítségével adja meg a megkeresni kívánt azonosítót és partíciókulcs-értéket. Az azonosító és a partíciókulcs értéke `ToDoItem` a megadott adatbázisból és gyűjteményből származó dokumentumok lekéréséhez használatos.

Itt a *function.json* fájl:

```json
{
  "bindings": [
    {
      "authLevel": "anonymous",
      "name": "req",
      "type": "httpTrigger",
      "direction": "in",
      "methods": [
        "get",
        "post"
      ],
      "route":"todoitems/{partitionKeyValue}/{id}"
    },
    {
      "name": "$return",
      "type": "http",
      "direction": "out"
    },
    {
      "type": "cosmosDB",
      "name": "todoitems",
      "databaseName": "ToDoItems",
      "collectionName": "Items",
      "connection": "CosmosDBConnection",
      "direction": "in",
      "Id": "{id}",
      "PartitionKey": "{partitionKeyValue}"
    }
  ],
  "disabled": false,
  "scriptFile": "__init__.py"
}
```

Itt a Python kód:

```python
import logging
import azure.functions as func


def main(req: func.HttpRequest, todoitems: func.DocumentList) -> str:
    if not todoitems:
        logging.warning("ToDo item not found")
    else:
        logging.info("Found ToDo item, Description=%s",
                     todoitems[0]['description'])
    return 'OK'
```

<a id="queue-trigger-get-multiple-docs-using-sqlquery-python"></a>

### <a name="queue-trigger-get-multiple-docs-using-sqlquery"></a>Várólista-eseményindító, több dokumentum bekésezése az SqlQuery használatával

A következő példa egy Azure Cosmos DB bemeneti kötést mutat be egy *function.json* fájlban és egy [Python-függvényt,](functions-reference-python.md) amely a kötést használja. A függvény egy SQL-lekérdezés által megadott több dokumentumot olvas be, egy várólista-eseményindítóval a lekérdezési paraméterek testreszabásához.

A várólista-eseményindító paramétert `departmentId`ad meg. A várólista-üzenet `{ "departmentId" : "Finance" }` visszaadja a pénzügyi részleg összes rekordját.

A *function.json* fájlban a kötési adatok:

```json
{
    "name": "documents",
    "type": "cosmosDB",
    "direction": "in",
    "databaseName": "MyDb",
    "collectionName": "MyCollection",
    "sqlQuery": "SELECT * from c where c.departmentId = {departmentId}",
    "connectionStringSetting": "CosmosDBConnection"
}
```

A [konfigurációs](#configuration) szakasz ismerteti ezeket a tulajdonságokat.

Itt a Python kód:

```python
import azure.functions as func

def main(queuemsg: func.QueueMessage, documents: func.DocumentList):
    for document in documents:
        # operate on each document
```

# <a name="java"></a>[Java](#tab/java)

Ez a szakasz a következő példákat tartalmazza:

* [HTTP-eseményindító, azonosító megkeresése a lekérdezési karakterláncból - Karakterlánc paraméter](#http-trigger-look-up-id-from-query-string---string-parameter-java)
* [HTTP-eseményindító, azonosító kiírása a lekérdezési karakterláncból - POJO paraméter](#http-trigger-look-up-id-from-query-string---pojo-parameter-java)
* [HTTP-eseményindító, azonosító kinézete az útvonaladatokból](#http-trigger-look-up-id-from-route-data-java)
* [HTTP-eseményindító, azonosító kinézete az útvonaladatokból az SqlQuery használatával](#http-trigger-look-up-id-from-route-data-using-sqlquery-java)
* [HTTP-eseményindító, több dokumentum bekésezése az útvonaladatokból az SqlQuery használatával](#http-trigger-get-multiple-docs-from-route-data-using-sqlquery-java)

A példák egy `ToDoItem` egyszerű típusra hivatkoznak:

```java
public class ToDoItem {

  private String id;
  private String description;

  public String getId() {
    return id;
  }

  public String getDescription() {
    return description;
  }

  @Override
  public String toString() {
    return "ToDoItem={id=" + id + ",description=" + description + "}";
  }
}
```

<a id="http-trigger-look-up-id-from-query-string---string-parameter-java"></a>

### <a name="http-trigger-look-up-id-from-query-string---string-parameter"></a>HTTP-eseményindító, azonosító megkeresése a lekérdezési karakterláncból - Karakterlánc paraméter

A következő példa egy Java függvényt mutat be, amely egyetlen dokumentumot olvas be. A függvényt egy HTTP-kérelem váltja ki, amely egy lekérdezési karakterláncot használ a megkeresni kívánt azonosító és partíciókulcs értékének megadásához. Az azonosító és a partíciókulcs értéke a megadott adatbázisból és gyűjteményből származó dokumentumok string formában történő beolvasására szolgál.

```java
public class DocByIdFromQueryString {

    @FunctionName("DocByIdFromQueryString")
    public HttpResponseMessage run(
            @HttpTrigger(name = "req",
              methods = {HttpMethod.GET, HttpMethod.POST},
              authLevel = AuthorizationLevel.ANONYMOUS)
            HttpRequestMessage<Optional<String>> request,
            @CosmosDBInput(name = "database",
              databaseName = "ToDoList",
              collectionName = "Items",
              id = "{Query.id}",
              partitionKey = "{Query.partitionKeyValue}",
              connectionStringSetting = "Cosmos_DB_Connection_String")
            Optional<String> item,
            final ExecutionContext context) {

        // Item list
        context.getLogger().info("Parameters are: " + request.getQueryParameters());
        context.getLogger().info("String from the database is " + (item.isPresent() ? item.get() : null));

        // Convert and display
        if (!item.isPresent()) {
            return request.createResponseBuilder(HttpStatus.BAD_REQUEST)
                          .body("Document not found.")
                          .build();
        }
        else {
            // return JSON from Cosmos. Alternatively, we can parse the JSON string
            // and return an enriched JSON object.
            return request.createResponseBuilder(HttpStatus.OK)
                          .header("Content-Type", "application/json")
                          .body(item.get())
                          .build();
        }
    }
}
 ```

A [Java függvények futásidejű függvénytárban](/java/api/overview/azure/functions/runtime)használja a notnotation a `@CosmosDBInput` függvényparamétereket, amelyek értéke származna Cosmos DB.  Ez a jegyzet használható natív Java-típusok, POJOs vagy `Optional<T>`nullable értékek segítségével.

<a id="http-trigger-look-up-id-from-query-string---pojo-parameter-java"></a>

### <a name="http-trigger-look-up-id-from-query-string---pojo-parameter"></a>HTTP-eseményindító, azonosító kiírása a lekérdezési karakterláncból - POJO paraméter

A következő példa egy Java függvényt mutat be, amely egyetlen dokumentumot olvas be. A függvényt egy HTTP-kérelem váltja ki, amely egy lekérdezési karakterláncot használ a megkeresni kívánt azonosító és partíciókulcs értékének megadásához. Ez az azonosító és a partíciókulcs értéke a dokumentum nak a megadott adatbázisból és gyűjteményből való lekéréséhez. A dokumentum ezután a ```ToDoItem``` korábban létrehozott POJO egy példányává alakul át, és argumentumként átkerül a függvénybe.

```java
public class DocByIdFromQueryStringPojo {

    @FunctionName("DocByIdFromQueryStringPojo")
    public HttpResponseMessage run(
            @HttpTrigger(name = "req",
              methods = {HttpMethod.GET, HttpMethod.POST},
              authLevel = AuthorizationLevel.ANONYMOUS)
            HttpRequestMessage<Optional<String>> request,
            @CosmosDBInput(name = "database",
              databaseName = "ToDoList",
              collectionName = "Items",
              id = "{Query.id}",
              partitionKey = "{Query.partitionKeyValue}",
              connectionStringSetting = "Cosmos_DB_Connection_String")
            ToDoItem item,
            final ExecutionContext context) {

        // Item list
        context.getLogger().info("Parameters are: " + request.getQueryParameters());
        context.getLogger().info("Item from the database is " + item);

        // Convert and display
        if (item == null) {
            return request.createResponseBuilder(HttpStatus.BAD_REQUEST)
                          .body("Document not found.")
                          .build();
        }
        else {
            return request.createResponseBuilder(HttpStatus.OK)
                          .header("Content-Type", "application/json")
                          .body(item)
                          .build();
        }
    }
}
 ```

<a id="http-trigger-look-up-id-from-route-data-java"></a>

### <a name="http-trigger-look-up-id-from-route-data"></a>HTTP-eseményindító, azonosító kinézete az útvonaladatokból

A következő példa egy Java függvényt mutat be, amely egyetlen dokumentumot olvas be. A függvényt egy HTTP-kérelem váltja ki, amely egy útvonalparamétert használ a megkeresni kívánt azonosító és partíciókulcs értékének megadásához. Az azonosító és a partíciókulcs értéke a dokumentum nak a megadott adatbázisból ```Optional<String>```és gyűjteményből való beolvasására szolgál, és azt .

```java
public class DocByIdFromRoute {

    @FunctionName("DocByIdFromRoute")
    public HttpResponseMessage run(
            @HttpTrigger(name = "req",
              methods = {HttpMethod.GET, HttpMethod.POST},
              authLevel = AuthorizationLevel.ANONYMOUS,
              route = "todoitems/{partitionKeyValue}/{id}")
            HttpRequestMessage<Optional<String>> request,
            @CosmosDBInput(name = "database",
              databaseName = "ToDoList",
              collectionName = "Items",
              id = "{id}",
              partitionKey = "{partitionKeyValue}",
              connectionStringSetting = "Cosmos_DB_Connection_String")
            Optional<String> item,
            final ExecutionContext context) {

        // Item list
        context.getLogger().info("Parameters are: " + request.getQueryParameters());
        context.getLogger().info("String from the database is " + (item.isPresent() ? item.get() : null));

        // Convert and display
        if (!item.isPresent()) {
            return request.createResponseBuilder(HttpStatus.BAD_REQUEST)
                          .body("Document not found.")
                          .build();
        }
        else {
            // return JSON from Cosmos. Alternatively, we can parse the JSON string
            // and return an enriched JSON object.
            return request.createResponseBuilder(HttpStatus.OK)
                          .header("Content-Type", "application/json")
                          .body(item.get())
                          .build();
        }
    }
}
 ```

 <a id="http-trigger-look-up-id-from-route-data-using-sqlquery-java"></a>

### <a name="http-trigger-look-up-id-from-route-data-using-sqlquery"></a>HTTP-eseményindító, azonosító kinézete az útvonaladatokból az SqlQuery használatával

A következő példa egy Java függvényt mutat be, amely egyetlen dokumentumot olvas be. A függvényt egy HTTP-kérelem váltja ki, amely egy útvonalparamétert használ a megkeresni kívánt azonosító megadásához. Ez az azonosító a megadott adatbázisból és gyűjteményből származó dokumentumok beolvasására szolgál, és az eredményhalmazt egy ```ToDoItem[]```-re konvertálja, mivel a lekérdezési feltételektől függően számos dokumentum is visszaadható.

> [!NOTE]
> Ha csak az azonosítóval kell lekérdeznie, ajánlott az előző [példákhoz](#http-trigger-look-up-id-from-query-string---pojo-parameter-java)hasonlóan felkeresni, mivel kevesebb [kérésegységet](../cosmos-db/request-units.md)fog használni. A pontolvasási műveletek [more efficient](../cosmos-db/optimize-cost-queries.md) (GET) hatékonyabbak, mint az azonosítóval végzett lekérdezések.
>

```java
public class DocByIdFromRouteSqlQuery {

    @FunctionName("DocByIdFromRouteSqlQuery")
    public HttpResponseMessage run(
            @HttpTrigger(name = "req",
              methods = {HttpMethod.GET, HttpMethod.POST},
              authLevel = AuthorizationLevel.ANONYMOUS,
              route = "todoitems2/{id}")
            HttpRequestMessage<Optional<String>> request,
            @CosmosDBInput(name = "database",
              databaseName = "ToDoList",
              collectionName = "Items",
              sqlQuery = "select * from Items r where r.id = {id}",
              connectionStringSetting = "Cosmos_DB_Connection_String")
            ToDoItem[] item,
            final ExecutionContext context) {

        // Item list
        context.getLogger().info("Parameters are: " + request.getQueryParameters());
        context.getLogger().info("Items from the database are " + item);

        // Convert and display
        if (item == null) {
            return request.createResponseBuilder(HttpStatus.BAD_REQUEST)
                          .body("Document not found.")
                          .build();
        }
        else {
            return request.createResponseBuilder(HttpStatus.OK)
                          .header("Content-Type", "application/json")
                          .body(item)
                          .build();
        }
    }
}
 ```

 <a id="http-trigger-get-multiple-docs-from-route-data-using-sqlquery-java"></a>

### <a name="http-trigger-get-multiple-docs-from-route-data-using-sqlquery"></a>HTTP-eseményindító, több dokumentum bekésezése az útvonaladatokból az SqlQuery használatával

A következő példa egy Java függvényt mutat be, amely több dokumentumot olvas be. A függvényt egy HTTP-kérelem váltja ```desc``` ki, amely egy útvonalparamétert használ a ```description``` mezőben keresandó karakterlánc megadásához. A keresési kifejezés a megadott adatbázisból és gyűjteményből származó dokumentumok gyűjteményének beolvasására szolgál, az eredményhalmaz a-ra ```ToDoItem[]``` konvertálására és argumentumként a függvénybe való átadásával.

```java
public class DocsFromRouteSqlQuery {

    @FunctionName("DocsFromRouteSqlQuery")
    public HttpResponseMessage run(
            @HttpTrigger(name = "req",
              methods = {HttpMethod.GET},
              authLevel = AuthorizationLevel.ANONYMOUS,
              route = "todoitems3/{desc}")
            HttpRequestMessage<Optional<String>> request,
            @CosmosDBInput(name = "database",
              databaseName = "ToDoList",
              collectionName = "Items",
              sqlQuery = "select * from Items r where contains(r.description, {desc})",
              connectionStringSetting = "Cosmos_DB_Connection_String")
            ToDoItem[] items,
            final ExecutionContext context) {

        // Item list
        context.getLogger().info("Parameters are: " + request.getQueryParameters());
        context.getLogger().info("Number of items from the database is " + (items == null ? 0 : items.length));

        // Convert and display
        if (items == null) {
            return request.createResponseBuilder(HttpStatus.BAD_REQUEST)
                          .body("No documents found.")
                          .build();
        }
        else {
            return request.createResponseBuilder(HttpStatus.OK)
                          .header("Content-Type", "application/json")
                          .body(items)
                          .build();
        }
    }
}
 ```

 ---

## <a name="attributes-and-annotations"></a>Attribútumok és jegyzetek

# <a name="c"></a>[C #](#tab/csharp)

A [C# osztálykönyvtárakban](functions-dotnet-class-library.md)használja a [CosmosDB](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.CosmosDB/CosmosDBAttribute.cs) attribútumot.

Az attribútum konstruktora felveszi az adatbázis nevét és a gyűjtemény nevét. A beállításokról és a konfigurálható egyéb tulajdonságokról [a következő konfigurációs szakaszban](#configuration)olvashat.

# <a name="c-script"></a>[C# parancsfájl](#tab/csharp-script)

Az attribútumokat a C# script nem támogatja.

# <a name="javascript"></a>[Javascript](#tab/javascript)

A JavaScript nem támogatja az attribútumokat.

# <a name="python"></a>[Python](#tab/python)

Az attribútumokat a Python nem támogatja.

# <a name="java"></a>[Java](#tab/java)

A [Java függvények futásidejű függvénytár,](https://docs.microsoft.com/java/api/overview/azure/functions/runtime)használja a `@CosmosDBOutput` jegyzet a paramétereket, amelyek írásban Cosmos DB. A jegyzetparaméter típusának `OutputBinding<T>`a `T` : -nak kell lennie, ahol natív Java-típus vagy POJO.

---

## <a name="configuration"></a>Konfiguráció

Az alábbi táblázat a *function.json* fájlban és az `CosmosDB` attribútumban beállított kötési konfigurációs tulajdonságokat ismerteti.

|function.json tulajdonság | Attribútum tulajdonság |Leírás|
|---------|---------|----------------------|
|**Típus**     | n/a | A beállításnak `cosmosDB`a beállítására kell beállítható.        |
|**direction**     | n/a | A beállításnak `in`a beállítására kell beállítható.         |
|**név**     | n/a | A függvényben lévő dokumentumot jelölő kötési paraméter neve.  |
|**adatbázisneve** |**DatabaseName** |A dokumentumot tartalmazó adatbázis.        |
|**Lekérdezés_neve** |**CollectionName** | A dokumentumot tartalmazó gyűjtemény neve. |
|**id**    | **Id** | A beolvasandó dokumentum azonosítója. Ez a tulajdonság támogatja a [kötési kifejezéseket.](./functions-bindings-expressions-patterns.md) Ne állítsa be `id` mind a mind az **sqlQuery** tulajdonságokat. Ha egyiket sem állítja be, a rendszer a teljes gyűjteményt beolvassa. |
|**sqlQuery**  |**SqlQuery**  | Egy Azure Cosmos DB SQL-lekérdezés több dokumentum beolvasásához használt. A tulajdonság támogatja a futásidejű kötéseket, `SELECT * FROM c where c.departmentId = {departmentId}`mint például ebben a példában: . Ne állítsa be `id` mind `sqlQuery` a tulajdonságokat. Ha egyiket sem állítja be, a rendszer a teljes gyűjteményt beolvassa.|
|**connectionStringSetting**     |**ConnectionStringSetting (Kapcsolatkarakterlánc-beállítás)**|Az Azure Cosmos DB kapcsolati karakterláncot tartalmazó alkalmazásbeállítás neve. |
|**partíciókulcs**|**PartitionKey**|Megadja a válaszkulcs értékét. Tartalmazhat kötési paramétereket. A [particionált](../cosmos-db/partition-data.md#logical-partitions) gyűjtemények keres.|
|**preferredLocations (elsődleges helyek)**| **PreferredLocations (PreferredLocations)**| (Nem kötelező) Az Azure Cosmos DB szolgáltatás georeplikált adatbázis-fiókjainak előnyben részesített helyeit (régióit) határozza meg. Az értékeket vesszővel kell elválasztani. Például: "USA keleti régiója, USA déli középső régiója, Észak-Európa". |

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="usage"></a>Használat

# <a name="c"></a>[C #](#tab/csharp)

Amikor a függvény sikeresen kilép, a bemeneti dokumentum névvel ellátott bemeneti paramétereken keresztül végrehajtott módosításai automatikusan megmaradnak.

# <a name="c-script"></a>[C# parancsfájl](#tab/csharp-script)

Amikor a függvény sikeresen kilép, a bemeneti dokumentum névvel ellátott bemeneti paramétereken keresztül végrehajtott módosításai automatikusan megmaradnak.

# <a name="javascript"></a>[Javascript](#tab/javascript)

A frissítések nem lesznek automatikusan a funkció kilépésekor. Ehelyett `context.bindings.<documentName>In` használja, `context.bindings.<documentName>Out` és a frissítéseket. Lásd a JavaScript példa.

# <a name="python"></a>[Python](#tab/python)

Az adatok egy `DocumentList` paraméteren keresztül érhetők el a függvény számára. A dokumentumon végrehajtott módosítások nem maradnak meg automatikusan.

# <a name="java"></a>[Java](#tab/java)

A [Java függvények futásidejű függvénytárából](https://docs.microsoft.com/java/api/overview/azure/functions/runtime)a [@CosmosDBInput](https://docs.microsoft.com/java/api/com.microsoft.azure.functions.annotation.cosmosdbinput) jegyzet cosmos DB-adatokat ad ki a függvénynek. Ez a jegyzet használható natív Java-típusok, POJOs vagy `Optional<T>`nullable értékek segítségével.

---

## <a name="next-steps"></a>További lépések

- [Egy függvény futtatása, ha egy Azure Cosmos DB-dokumentum jön létre vagy módosul (Trigger)](./functions-bindings-cosmosdb-v2-trigger.md)
- [Az Azure Cosmos DB-dokumentum módosításának mentése (kimeneti kötés)](./functions-bindings-cosmosdb-v2-output.md)