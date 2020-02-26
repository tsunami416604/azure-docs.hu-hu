---
title: A 2. x függvények bemeneti kötésének Azure Cosmos DB
description: Megtudhatja, hogyan használhatja a Azure Functions Azure Cosmos DB bemeneti kötését.
author: craigshoemaker
ms.topic: reference
ms.date: 02/24/2020
ms.author: cshoe
ms.openlocfilehash: 4fe04d491525b8119ca21ff1118a2ea460cc0795
ms.sourcegitcommit: 0cc25b792ad6ec7a056ac3470f377edad804997a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/25/2020
ms.locfileid: "77606606"
---
# <a name="azure-cosmos-db-input-binding-for-azure-functions-2x"></a>Azure Functions 2. x Azure Cosmos DB bemeneti kötésének megadása

Az Azure Cosmos DB bemeneti kötéssel legalább egy Azure Cosmos DB-dokumentumok lekérdezésének az SQL API-t használja, és továbbítja őket a függvény a bemeneti paraméter. A dokumentum azonosítója vagy lekérdezési paramétereket az eseményindítót, amely meghívja a függvényt alapján lehet meghatározni.

További információ a telepítésről és a konfigurációról: [Áttekintés](./functions-bindings-cosmosdb-v2.md).

> [!NOTE]
> Ha a gyűjtemény [particionálva](../cosmos-db/partition-data.md#logical-partitions)van, a keresési műveleteknek meg kell adniuk a partíciós kulcs értékét is.
>

<a id="example" name="example"></a>

# <a name="c"></a>[C#](#tab/csharp)

Ez a szakasz tartalmazza az alábbi példák:

* [Üzenetsor-trigger, Keresés azonosító a JSON-ből](#queue-trigger-look-up-id-from-json-c)
* [HTTP-trigger, keresési azonosító lekérdezési karakterláncból](#http-trigger-look-up-id-from-query-string-c)
* [HTTP-trigger, azonosító megkeresése az útvonal adatainak alapján](#http-trigger-look-up-id-from-route-data-c)
* [HTTP-trigger, keresés az útvonal adatai alapján, SqlQuery használatával](#http-trigger-look-up-id-from-route-data-using-sqlquery-c)
* [HTTP-trigger, több dokumentum beolvasása a SqlQuery használatával](#http-trigger-get-multiple-docs-using-sqlquery-c)
* [HTTP-trigger, több dokumentum beolvasása a DocumentClient használatával](#http-trigger-get-multiple-docs-using-documentclient-c)

A példák egy egyszerű `ToDoItem` típusra vonatkoznak:

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

### <a name="queue-trigger-look-up-id-from-json"></a>Üzenetsor-trigger, Keresés azonosító a JSON-ből 

Az alábbi példa egy olyan [ C# függvényt](functions-dotnet-class-library.md) mutat be, amely egyetlen dokumentumot kér le. A függvény, amely tartalmaz egy JSON-objektum egy üzenetsor által aktivált. A várólista-trigger a JSON-t egy `ToDoItemLookup`típusú objektummá elemzi, amely tartalmazza az azonosító és a partíció kulcs értékét a kereséshez. Az azonosító és a partíciós kulcs értéke `ToDoItem` dokumentum lekérésére szolgál a megadott adatbázisból és gyűjteményből.

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

### <a name="http-trigger-look-up-id-from-query-string"></a>HTTP-trigger, keresési azonosító lekérdezési karakterláncból

Az alábbi példa egy olyan [ C# függvényt](functions-dotnet-class-library.md) mutat be, amely egyetlen dokumentumot kér le. A függvényt egy lekérdezési karakterláncot használó HTTP-kérelem indítja el, amely megadja az azonosító és a partíció kulcsának értékét a kereséshez. Az azonosító és a partíciós kulcs értéke `ToDoItem` dokumentum lekérésére szolgál a megadott adatbázisból és gyűjteményből.

>[!NOTE]
>A HTTP-lekérdezési karakterlánc paraméter megkülönbözteti a kis-és nagybetűket.
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

### <a name="http-trigger-look-up-id-from-route-data"></a>HTTP-trigger, azonosító megkeresése az útvonal adatainak alapján

Az alábbi példa egy olyan [ C# függvényt](functions-dotnet-class-library.md) mutat be, amely egyetlen dokumentumot kér le. A függvényt egy olyan HTTP-kérelem indítja el, amely az útválasztási adat használatával megkeresi az azonosítót és a partíciós kulcs értékét. Az azonosító és a partíciós kulcs értéke `ToDoItem` dokumentum lekérésére szolgál a megadott adatbázisból és gyűjteményből.

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

### <a name="http-trigger-look-up-id-from-route-data-using-sqlquery"></a>HTTP-trigger, keresés az útvonal adatai alapján, SqlQuery használatával

Az alábbi példa egy olyan [ C# függvényt](functions-dotnet-class-library.md) mutat be, amely egyetlen dokumentumot kér le. A függvény akkor indul el, hogy a használt, keresse ki az azonosító megadása adatainak átirányítása a HTTP-kérelemmel történik. Ez az azonosító egy `ToDoItem` dokumentum lekérésére szolgál a megadott adatbázisból és gyűjteményből.

A példa bemutatja, hogyan használható egy kötési kifejezés a `SqlQuery` paraméterben. Átadhatja az útvonal adatait a `SqlQuery` paraméternek az ábrán látható módon, de jelenleg [nem adhat meg lekérdezési karakterlánc-értékeket](https://github.com/Azure/azure-functions-host/issues/2554#issuecomment-392084583).

> [!NOTE]
> Ha csak az azonosító alapján kell lekérdezni, ajánlott megkeresni az [előző példákhoz](#http-trigger-look-up-id-from-query-string-c)hasonlóan, mivel kevesebb [kérési egységet](../cosmos-db/request-units.md)fog használni. A pont olvasási műveletei (GET) [hatékonyabbak, mint a](../cosmos-db/optimize-cost-queries.md) lekérdezések azonosító alapján.
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

### <a name="http-trigger-get-multiple-docs-using-sqlquery"></a>HTTP-trigger, több dokumentum beolvasása a SqlQuery használatával

Az alábbi példa egy olyan [ C# függvényt](functions-dotnet-class-library.md) mutat be, amely lekéri a dokumentumok listáját. A függvényt egy HTTP-kérelem aktiválja. A lekérdezés a `SqlQuery` attribútum tulajdonságban van megadva.

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

### <a name="http-trigger-get-multiple-docs-using-documentclient"></a>HTTP-trigger, több dokumentum beolvasása a DocumentClient használatával

Az alábbi példa egy olyan [ C# függvényt](functions-dotnet-class-library.md) mutat be, amely lekéri a dokumentumok listáját. A függvényt egy HTTP-kérelem aktiválja. A kód a Azure Cosmos DB kötés által megadott `DocumentClient` példányt használja a dokumentumok listájának olvasásához. Az `DocumentClient` példány írási műveletekhez is használható.

> [!NOTE]
> A [IDocumentClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.idocumentclient?view=azure-dotnet) felületet is használhatja a tesztelés megkönnyítésére.

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

# <a name="c-script"></a>[C#Parancsfájl](#tab/csharp-script)

Ez a szakasz tartalmazza az alábbi példák:

* [Üzenetsor-trigger, azonosító megkeresése sztringből](#queue-trigger-look-up-id-from-string-c-script)
* [Üzenetsor-trigger, több dokumentum beolvasása a SqlQuery használatával](#queue-trigger-get-multiple-docs-using-sqlquery-c-script)
* [HTTP-trigger, keresési azonosító lekérdezési karakterláncból](#http-trigger-look-up-id-from-query-string-c-script)
* [HTTP-trigger, azonosító megkeresése az útvonal adatainak alapján](#http-trigger-look-up-id-from-route-data-c-script)
* [HTTP-trigger, több dokumentum beolvasása a SqlQuery használatával](#http-trigger-get-multiple-docs-using-sqlquery-c-script)
* [HTTP-trigger, több dokumentum beolvasása a DocumentClient használatával](#http-trigger-get-multiple-docs-using-documentclient-c-script)

A HTTP-triggerek példái egy egyszerű `ToDoItem` típusra vonatkoznak:

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

### <a name="queue-trigger-look-up-id-from-string"></a>Üzenetsor-trigger, azonosító megkeresése sztringből

Az alábbi példa egy Cosmos db bemeneti kötést mutat be egy *function. JSON* fájlban, valamint egy olyan [ C# parancsfájl-függvényt](functions-reference-csharp.md) , amely a kötést használja. A függvény beolvassa az egyetlen dokumentum, és frissíti a dokumentum szöveges érték.

Itt található a *function. JSON* fájlban található kötési adat:

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
A [konfigurációs](#configuration) szakasz ezeket a tulajdonságokat ismerteti.

Íme a C#-szkriptkódot:

```cs
    using System;

    // Change input document contents using Azure Cosmos DB input binding
    public static void Run(string myQueueItem, dynamic inputDocument)
    {
      inputDocument.text = "This has changed.";
    }
```

<a id="queue-trigger-get-multiple-docs-using-sqlquery-c-script"></a>

### <a name="queue-trigger-get-multiple-docs-using-sqlquery"></a>Üzenetsor-trigger, több dokumentum beolvasása a SqlQuery használatával

Az alábbi példa egy Azure Cosmos db bemeneti kötést mutat be egy *function. JSON* fájlban, valamint egy olyan [ C# parancsfájl-függvényt](functions-reference-csharp.md) , amely a kötést használja. A függvény egy SQL-lekérdezést, egy üzenetsor eseményindító segítségével testre szabhatja a lekérdezési paraméterek által megadott több dokumentumot kérdezi le.

A várólista-trigger `departmentId`paramétert biztosít. `{ "departmentId" : "Finance" }` üzenetsor-üzenete visszaküldi a pénzügyi részleg összes rekordját.

Itt található a *function. JSON* fájlban található kötési adat:

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

A [konfigurációs](#configuration) szakasz ezeket a tulajdonságokat ismerteti.

Íme a C#-szkriptkódot:

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

### <a name="http-trigger-look-up-id-from-query-string"></a>HTTP-trigger, keresési azonosító lekérdezési karakterláncból

Az alábbi példa egy olyan [ C# parancsfájl-függvényt](functions-reference-csharp.md) mutat be, amely egyetlen dokumentumot kér le. A függvényt egy lekérdezési karakterláncot használó HTTP-kérelem indítja el, amely megadja az azonosító és a partíció kulcsának értékét a kereséshez. Az azonosító és a partíciós kulcs értéke `ToDoItem` dokumentum lekérésére szolgál a megadott adatbázisból és gyűjteményből.

Itt látható a *function. JSON* fájl:

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

Íme a C#-szkriptkódot:

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

### <a name="http-trigger-look-up-id-from-route-data"></a>HTTP-trigger, azonosító megkeresése az útvonal adatainak alapján

Az alábbi példa egy olyan [ C# parancsfájl-függvényt](functions-reference-csharp.md) mutat be, amely egyetlen dokumentumot kér le. A függvényt egy olyan HTTP-kérelem indítja el, amely az útválasztási adat használatával megkeresi az azonosítót és a partíciós kulcs értékét. Az azonosító és a partíciós kulcs értéke `ToDoItem` dokumentum lekérésére szolgál a megadott adatbázisból és gyűjteményből.

Itt látható a *function. JSON* fájl:

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

Íme a C#-szkriptkódot:

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

### <a name="http-trigger-get-multiple-docs-using-sqlquery"></a>HTTP-trigger, több dokumentum beolvasása a SqlQuery használatával

A következő példa egy [ C# parancsfájl-függvényt](functions-reference-csharp.md) mutat be, amely a dokumentumok listáját kéri le. A függvényt egy HTTP-kérelem aktiválja. A lekérdezés a `SqlQuery` attribútum tulajdonságban van megadva.

Itt látható a *function. JSON* fájl:

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

Íme a C#-szkriptkódot:

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

### <a name="http-trigger-get-multiple-docs-using-documentclient"></a>HTTP-trigger, több dokumentum beolvasása a DocumentClient használatával

A következő példa egy [ C# parancsfájl-függvényt](functions-reference-csharp.md) mutat be, amely a dokumentumok listáját kéri le. A függvényt egy HTTP-kérelem aktiválja. A kód a Azure Cosmos DB kötés által megadott `DocumentClient` példányt használja a dokumentumok listájának olvasásához. Az `DocumentClient` példány írási műveletekhez is használható.

Itt látható a *function. JSON* fájl:

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

Íme a C#-szkriptkódot:

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

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Ez a szakasz az alábbi példákat tartalmazza, amelyek egyetlen dokumentumot olvasnak be a különböző forrásokból származó azonosító érték megadásával:

* [Üzenetsor-trigger, Keresés azonosító a JSON-ből](#queue-trigger-look-up-id-from-json-javascript)
* [HTTP-trigger, keresési azonosító lekérdezési karakterláncból](#http-trigger-look-up-id-from-query-string-javascript)
* [HTTP-trigger, azonosító megkeresése az útvonal adatainak alapján](#http-trigger-look-up-id-from-route-data-javascript)
* [Üzenetsor-trigger, több dokumentum beolvasása a SqlQuery használatával](#queue-trigger-get-multiple-docs-using-sqlquery-javascript)

<a id="queue-trigger-look-up-id-from-json-javascript"></a>

### <a name="queue-trigger-look-up-id-from-json"></a>Üzenetsor-trigger, Keresés azonosító a JSON-ből

Az alábbi példa egy Cosmos DB bemeneti kötést mutat be egy *function. JSON* fájlban, valamint egy [JavaScript-függvényt](functions-reference-node.md) , amely a kötést használja. A függvény beolvassa az egyetlen dokumentum, és frissíti a dokumentum szöveges érték.

Itt található a *function. JSON* fájlban található kötési adat:

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

A [konfigurációs](#configuration) szakasz ezeket a tulajdonságokat ismerteti.

A következő JavaScript-kódot:

```javascript
    // Change input document contents using Azure Cosmos DB input binding, using context.bindings.inputDocumentOut
    module.exports = function (context) {
    context.bindings.inputDocumentOut = context.bindings.inputDocumentIn;
    context.bindings.inputDocumentOut.text = "This was updated!";
    context.done();
    };
```

<a id="http-trigger-look-up-id-from-query-string-javascript"></a>

### <a name="http-trigger-look-up-id-from-query-string"></a>HTTP-trigger, keresési azonosító lekérdezési karakterláncból

Az alábbi példa egy [JavaScript-függvényt](functions-reference-node.md) mutat be, amely egyetlen dokumentumot kér le. A függvényt egy lekérdezési karakterláncot használó HTTP-kérelem indítja el, amely megadja az azonosító és a partíció kulcsának értékét a kereséshez. Az azonosító és a partíciós kulcs értéke `ToDoItem` dokumentum lekérésére szolgál a megadott adatbázisból és gyűjteményből.

Itt látható a *function. JSON* fájl:

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

A következő JavaScript-kódot:

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

### <a name="http-trigger-look-up-id-from-route-data"></a>HTTP-trigger, azonosító megkeresése az útvonal adatainak alapján

Az alábbi példa egy [JavaScript-függvényt](functions-reference-node.md) mutat be, amely egyetlen dokumentumot kér le. A függvényt egy olyan HTTP-kérelem indítja el, amely az útválasztási adat használatával megkeresi az azonosítót és a partíciós kulcs értékét. Az azonosító és a partíciós kulcs értéke `ToDoItem` dokumentum lekérésére szolgál a megadott adatbázisból és gyűjteményből.

Itt látható a *function. JSON* fájl:

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

A következő JavaScript-kódot:

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

### <a name="queue-trigger-get-multiple-docs-using-sqlquery"></a>Üzenetsor-trigger, több dokumentum beolvasása a SqlQuery használatával

Az alábbi példa egy Azure Cosmos DB bemeneti kötést mutat be egy *function. JSON* fájlban, valamint egy [JavaScript-függvényt](functions-reference-node.md) , amely a kötést használja. A függvény egy SQL-lekérdezést, egy üzenetsor eseményindító segítségével testre szabhatja a lekérdezési paraméterek által megadott több dokumentumot kérdezi le.

A várólista-trigger `departmentId`paramétert biztosít. `{ "departmentId" : "Finance" }` üzenetsor-üzenete visszaküldi a pénzügyi részleg összes rekordját.

Itt található a *function. JSON* fájlban található kötési adat:

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

A [konfigurációs](#configuration) szakasz ezeket a tulajdonságokat ismerteti.

A következő JavaScript-kódot:

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

Ez a szakasz az alábbi példákat tartalmazza, amelyek egyetlen dokumentumot olvasnak be a különböző forrásokból származó azonosító érték megadásával:

* [Üzenetsor-trigger, Keresés azonosító a JSON-ből](#queue-trigger-look-up-id-from-json-python)
* [HTTP-trigger, keresési azonosító lekérdezési karakterláncból](#http-trigger-look-up-id-from-query-string-python)
* [HTTP-trigger, azonosító megkeresése az útvonal adatainak alapján](#http-trigger-look-up-id-from-route-data-python)
* [Üzenetsor-trigger, több dokumentum beolvasása a SqlQuery használatával](#queue-trigger-get-multiple-docs-using-sqlquery-python)

<a id="queue-trigger-look-up-id-from-json-python"></a>

### <a name="queue-trigger-look-up-id-from-json"></a>Üzenetsor-trigger, Keresés azonosító a JSON-ből

Az alábbi példa egy Cosmos DB bemeneti kötést mutat be egy *function. JSON* fájlban, valamint egy olyan [Python-függvényt](functions-reference-python.md) , amely a kötést használja. A függvény beolvassa az egyetlen dokumentum, és frissíti a dokumentum szöveges érték.

Itt található a *function. JSON* fájlban található kötési adat:

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

A [konfigurációs](#configuration) szakasz ezeket a tulajdonságokat ismerteti.

Itt látható a Python-kód:

```python
import azure.functions as func


def main(queuemsg: func.QueueMessage, documents: func.DocumentList) -> func.Document:
    if documents:
        document = documents[0]
        document['text'] = 'This was updated!'
        return document
```

<a id="http-trigger-look-up-id-from-query-string-python"></a>

### <a name="http-trigger-look-up-id-from-query-string"></a>HTTP-trigger, keresési azonosító lekérdezési karakterláncból

Az alábbi példa egy olyan [Python-függvényt](functions-reference-python.md) mutat be, amely egyetlen dokumentumot kér le. A függvényt egy lekérdezési karakterláncot használó HTTP-kérelem indítja el, amely megadja az azonosító és a partíció kulcsának értékét a kereséshez. Az azonosító és a partíciós kulcs értéke `ToDoItem` dokumentum lekérésére szolgál a megadott adatbázisból és gyűjteményből.

Itt látható a *function. JSON* fájl:

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

Itt látható a Python-kód:

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

### <a name="http-trigger-look-up-id-from-route-data"></a>HTTP-trigger, azonosító megkeresése az útvonal adatainak alapján

Az alábbi példa egy olyan [Python-függvényt](functions-reference-python.md) mutat be, amely egyetlen dokumentumot kér le. A függvényt egy olyan HTTP-kérelem indítja el, amely az útválasztási adat használatával megkeresi az azonosítót és a partíciós kulcs értékét. Az azonosító és a partíciós kulcs értéke `ToDoItem` dokumentum lekérésére szolgál a megadott adatbázisból és gyűjteményből.

Itt látható a *function. JSON* fájl:

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

Itt látható a Python-kód:

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

### <a name="queue-trigger-get-multiple-docs-using-sqlquery"></a>Üzenetsor-trigger, több dokumentum beolvasása a SqlQuery használatával

Az alábbi példa egy Azure Cosmos DB bemeneti kötést mutat be egy *function. JSON* fájlban, valamint egy olyan [Python-függvényt](functions-reference-python.md) , amely a kötést használja. A függvény egy SQL-lekérdezést, egy üzenetsor eseményindító segítségével testre szabhatja a lekérdezési paraméterek által megadott több dokumentumot kérdezi le.

A várólista-trigger `departmentId`paramétert biztosít. `{ "departmentId" : "Finance" }` üzenetsor-üzenete visszaküldi a pénzügyi részleg összes rekordját.

Itt található a *function. JSON* fájlban található kötési adat:

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

A [konfigurációs](#configuration) szakasz ezeket a tulajdonságokat ismerteti.

Itt látható a Python-kód:

```python
import azure.functions as func

def main(queuemsg: func.QueueMessage, documents: func.DocumentList):
    for document in documents:
        # operate on each document
```

# <a name="java"></a>[Java](#tab/java)

Ez a szakasz tartalmazza az alábbi példák:

* [HTTP-trigger, Keresés azonosító a lekérdezési karakterláncból – karakterlánc paraméter](#http-trigger-look-up-id-from-query-string---string-parameter-java)
* [HTTP-trigger, Keresés azonosító a lekérdezési karakterláncból – POJO paraméter](#http-trigger-look-up-id-from-query-string---pojo-parameter-java)
* [HTTP-trigger, azonosító megkeresése az útvonal adatainak alapján](#http-trigger-look-up-id-from-route-data-java)
* [HTTP-trigger, keresés az útvonal adatai alapján, SqlQuery használatával](#http-trigger-look-up-id-from-route-data-using-sqlquery-java)
* [HTTP-trigger, több dokumentum beolvasása az útvonal adataiból, SqlQuery használatával](#http-trigger-get-multiple-docs-from-route-data-using-sqlquery-java)

A példák egy egyszerű `ToDoItem` típusra vonatkoznak:

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

### <a name="http-trigger-look-up-id-from-query-string---string-parameter"></a>HTTP-trigger, Keresés azonosító a lekérdezési karakterláncból – karakterlánc paraméter

Az alábbi példa egy Java-függvényt mutat be, amely egyetlen dokumentumot kér le. A függvényt egy lekérdezési karakterláncot használó HTTP-kérelem indítja el, amely megadja az azonosító és a partíció kulcsának értékét a kereséshez. Az azonosító és a partíciós kulcs értéke a megadott adatbázisból és gyűjteményből származó dokumentum lekérésére szolgál karakterlánc formájában.

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

A [Java functions runtime library](/java/api/overview/azure/functions/runtime)-ben használja a `@CosmosDBInput` Megjegyzés azon függvény paramétereit, amelyek értéke Cosmos DBból származik.  Ezt a jegyzetet natív Java-típusokkal, Szerializálói vagy NULL értékű értékekkel lehet használni `Optional<T>`használatával.

<a id="http-trigger-look-up-id-from-query-string---pojo-parameter-java"></a>

### <a name="http-trigger-look-up-id-from-query-string---pojo-parameter"></a>HTTP-trigger, Keresés azonosító a lekérdezési karakterláncból – POJO paraméter

Az alábbi példa egy Java-függvényt mutat be, amely egyetlen dokumentumot kér le. A függvényt egy lekérdezési karakterláncot használó HTTP-kérelem indítja el, amely megadja az azonosító és a partíció kulcsának értékét a kereséshez. A megadott adatbázisból és gyűjteményből származó dokumentum lekéréséhez használt azonosító és partíciós kulcs értéke. A rendszer ezután átalakítja a dokumentumot a korábban létrehozott ```ToDoItem``` POJO egy példányára, és a függvény argumentumaként adta át.

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

### <a name="http-trigger-look-up-id-from-route-data"></a>HTTP-trigger, azonosító megkeresése az útvonal adatainak alapján

Az alábbi példa egy Java-függvényt mutat be, amely egyetlen dokumentumot kér le. A függvényt egy Route paramétert használó HTTP-kérelem indítja el, amely megadja az azonosító és a partíció kulcsának értékét a kereséshez. Az azonosító és a partíciós kulcs értékét a rendszer a megadott adatbázisból és gyűjteményből származó dokumentum lekérésére használja, ```Optional<String>```ként való visszaküldéssel.

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

### <a name="http-trigger-look-up-id-from-route-data-using-sqlquery"></a>HTTP-trigger, keresés az útvonal adatai alapján, SqlQuery használatával

Az alábbi példa egy Java-függvényt mutat be, amely egyetlen dokumentumot kér le. A függvényt egy Route paramétert használó HTTP-kérelem indítja el, amely megadja a megkeresni kívánt azonosítót. Ez az azonosító a megadott adatbázisból és gyűjteményből származó dokumentum lekérésére, az eredményhalmaz egy ```ToDoItem[]```ra való átalakítására szolgál, mivel a lekérdezési feltételektől függően számos dokumentumot vissza lehet adni.

> [!NOTE]
> Ha csak az azonosító alapján kell lekérdezni, ajánlott megkeresni az [előző példákhoz](#http-trigger-look-up-id-from-query-string---pojo-parameter-java)hasonlóan, mivel kevesebb [kérési egységet](../cosmos-db/request-units.md)fog használni. A pont olvasási műveletei (GET) [hatékonyabbak, mint a](../cosmos-db/optimize-cost-queries.md) lekérdezések azonosító alapján.
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

### <a name="http-trigger-get-multiple-docs-from-route-data-using-sqlquery"></a>HTTP-trigger, több dokumentum beolvasása az útvonal adataiból, SqlQuery használatával

Az alábbi példa egy Java-függvényt mutat be, amely több dokumentumot kér le. A függvényt egy olyan HTTP-kérelem indítja el, amely egy ```desc``` útvonal-paramétert használ a ```description``` mezőben keresendő karakterlánc megadásához. A keresési kifejezés a megadott adatbázisból és gyűjteményből származó dokumentumok gyűjteményének lekérésére, az eredményhalmaz ```ToDoItem[]```ra alakítására és a függvény argumentumaként való átadására szolgál.

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

# <a name="c"></a>[C#](#tab/csharp)

Az [ C# osztályok könyvtáraiban](functions-dotnet-class-library.md)használja a [CosmosDB](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.CosmosDB/CosmosDBAttribute.cs) attribútumot.

Az attribútum konstruktorának paramétereként meg az adatbázis és gyűjtemény nevét. Ezekről a beállításokról és a konfigurálható egyéb tulajdonságokról [a következő konfigurációs szakaszban](#configuration)tájékozódhat.

# <a name="c-script"></a>[C#Parancsfájl](#tab/csharp-script)

Az C# attribútumokat a parancsfájl nem támogatja.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

A JavaScript nem támogatja az attribútumokat.

# <a name="python"></a>[Python](#tab/python)

A Python nem támogatja az attribútumokat.

# <a name="java"></a>[Java](#tab/java)

A [Java functions futtatókörnyezet könyvtárában](https://docs.microsoft.com/java/api/overview/azure/functions/runtime)használja a `@CosmosDBOutput` megjegyzéseket a Cosmos DBba írandó paramétereknél. A Megjegyzés paraméterének típusának `OutputBinding<T>`nak kell lennie, ahol a `T` natív Java-típus vagy POJO.

---

## <a name="configuration"></a>Konfiguráció

Az alábbi táblázat a *function. JSON* fájlban és a `CosmosDB` attribútumban beállított kötési konfigurációs tulajdonságokat ismerteti.

|Function.JSON tulajdonság | Attribútum tulajdonsága |Leírás|
|---------|---------|----------------------|
|**type**     | n/a | `cosmosDB`értékre kell állítani.        |
|**direction**     | n/a | `in`értékre kell állítani.         |
|**név**     | n/a | A kötési paraméter, amely a dokumentumot, a függvény neve.  |
|**databaseName** |**DatabaseName** |A dokumentum tartalmazó adatbázis.        |
|**collectionName** |**CollectionName** | A gyűjtemény, amely tartalmazza a dokumentum neve. |
|**ID**    | **Azonosító** | A lekérdezni kívánt a dokumentum Azonosítóját. Ez a tulajdonság támogatja a [kötési kifejezéseket](./functions-bindings-expressions-patterns.md). Ne állítsa be mind a `id`, mind a **sqlQuery** tulajdonságot. Ha nem állít be egy, a rendszer lekéri a teljes gyűjteményt. |
|**sqlQuery**  |**SqlQuery**  | Egy Azure Cosmos DB SQL-lekérdezés több dokumentumok használt. A tulajdonság támogatja a futásidejű kötéseket, ahogy az a következő példában látható: `SELECT * FROM c where c.departmentId = {departmentId}`. Ne állítsa be a `id` és a `sqlQuery` tulajdonságokat. Ha nem állít be egy, a rendszer lekéri a teljes gyűjteményt.|
|**connectionStringSetting**     |**ConnectionStringSetting**|Az Azure Cosmos DB kapcsolati sztringjét tartalmazó alkalmazásbeállítás neve.        |
|**partitionKey**|**PartitionKey**|Megadja a partíciós kulcs értékét a kereséshez. Előfordulhat, hogy tartalmazza a kötési paramétereket. A [particionált](../cosmos-db/partition-data.md#logical-partitions) gyűjtemények esetében szükséges a keresés.|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="usage"></a>Használat

# <a name="c"></a>[C#](#tab/csharp)

Ha a függvény sikeresen kilép, az elnevezett bemeneti paramétereken keresztül végrehajtott módosítások automatikusan megmaradnak.

# <a name="c-script"></a>[C#Parancsfájl](#tab/csharp-script)

Ha a függvény sikeresen kilép, az elnevezett bemeneti paramétereken keresztül végrehajtott módosítások automatikusan megmaradnak.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

A rendszer nem végez automatikusan frissítéseket a függvény kilépése után. Ehelyett a `context.bindings.<documentName>In` és a `context.bindings.<documentName>Out` használatával végezze el a frissítéseket. Tekintse meg a JavaScript példáját.

# <a name="python"></a>[Python](#tab/python)

Az adat a függvény számára elérhetővé válik egy `DocumentList` paraméter használatával. A dokumentumban végrehajtott módosítások nem maradnak meg automatikusan.

# <a name="java"></a>[Java](#tab/java)

A [Java functions futtatókörnyezet könyvtárában](https://docs.microsoft.com/java/api/overview/azure/functions/runtime)a [@CosmosDBInput](https://docs.microsoft.com/java/api/com.microsoft.azure.functions.annotation.cosmosdbinput) jegyzet elérhetővé teszi a függvény Cosmos db adatait. Ezt a jegyzetet natív Java-típusokkal, Szerializálói vagy NULL értékű értékekkel lehet használni `Optional<T>`használatával.

---

## <a name="next-steps"></a>Következő lépések

- [Függvény futtatása Azure Cosmos DB dokumentum létrehozásakor vagy módosításakor (trigger)](./functions-bindings-cosmosdb-v2-trigger.md)
- [Azure Cosmos DB dokumentum módosításainak mentése (kimeneti kötés)](./functions-bindings-cosmosdb-v2-output.md)