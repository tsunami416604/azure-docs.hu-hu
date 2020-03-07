---
title: A Functions az Azure Cosmos DB-kötéseket 1.x
description: Megtudhatja, hogyan használhatja az Azure Cosmos DB-eseményindítók és kötések az Azure Functions szolgáltatásban.
author: craigshoemaker
ms.author: cshoe
ms.topic: reference
ms.date: 11/21/2017
ms.custom: seodec18
ms.openlocfilehash: e30b256d9fa43402c3b2c444aa1a0e0dc16cfdcf
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/05/2020
ms.locfileid: "78370853"
---
# <a name="azure-cosmos-db-bindings-for-azure-functions-1x"></a>Az Azure Functions az Azure Cosmos DB-kötéseket 1.x

> [!div class="op_single_selector" title1="Válassza ki az Ön által használt Azure Functions futtatókörnyezet verzióját: "]
> * [1-es verzió](functions-bindings-cosmosdb.md)
> * [2-es verzió](functions-bindings-cosmosdb-v2.md)

Ez a cikk azt ismerteti, hogyan használhatók [Azure Cosmos db](../cosmos-db/serverless-computing-database.md) kötések a Azure Functionsban. Az Azure Functions támogatja a-trigger, bemeneti és kimeneti kötések az Azure Cosmos DB.

> [!NOTE]
> Ez a cikk az Azure Functions 1.x. További információ a kötések használatáról a 2. x és újabb függvények esetében: [Azure Cosmos db kötések Azure functions 2. x verzióhoz](functions-bindings-cosmosdb-v2.md).
>
>Ennek a kötésnek a DocumentDB eredetileg neve. A Functions-verzió 1.x, csak az eseményindító lett átnevezve a Cosmos dB-t. a bemeneti kötést, a kimeneti kötést és a NuGet-csomag megőrzi a DocumentDB nevét.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

> [!NOTE]
> Az Azure Cosmos DB-kötések csak az SQL API-val használhatók. Az összes többi Azure Cosmos DB API esetében az API-hoz készült statikus ügyfél használatával kell elérnie az adatbázist a függvényből, beleértve a [Azure Cosmos db API](../cosmos-db/mongodb-introduction.md)-ját a MongoDB, a [Cassandra API](../cosmos-db/cassandra-introduction.md), a [Gremlin API](../cosmos-db/graph-introduction.md)és a [Table API](../cosmos-db/table-introduction.md)számára.

## <a name="packages---functions-1x"></a>Csomagok – 1.x függvények

Az 1. x függvényekhez tartozó Azure Cosmos DB kötések a [Microsoft. Azure. webjobs. Extensions. DocumentDB](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.DocumentDB) NuGet csomagban, 1. x verzióban vannak megadva. A kötések forráskódja az [Azure-webjobs-SDK-Extensions GitHub-](https://github.com/Azure/azure-webjobs-sdk-extensions/tree/v2.x/src/WebJobs.Extensions.DocumentDB) tárházban található.

[!INCLUDE [functions-package](../../includes/functions-package.md)]

## <a name="trigger"></a>Eseményindító

A Azure Cosmos DB trigger a [Azure Cosmos db változási csatornát](../cosmos-db/change-feed.md) használja, hogy figyelje a lapkákat és a frissítéseket a partíciók között. A módosítási hírcsatorna beszúrások és frissítéseket, törléseket nem tesz közzé.

## <a name="trigger---example"></a>Az eseményindító – példa

# <a name="c"></a>[C#](#tab/csharp)

Az alábbi példa egy [ C# függvényt](functions-dotnet-class-library.md) mutat be, amely akkor jelenik meg, ha a megadott adatbázisban és gyűjteményben lapkák vagy frissítések vannak.

```cs
using Microsoft.Azure.Documents;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Host;
using System.Collections.Generic;

namespace CosmosDBSamplesV1
{
    public static class CosmosTrigger
    {
        [FunctionName("CosmosTrigger")]
        public static void Run([CosmosDBTrigger(
            databaseName: "ToDoItems",
            collectionName: "Items",
            ConnectionStringSetting = "CosmosDBConnection",
            LeaseCollectionName = "leases",
            CreateLeaseCollectionIfNotExists = true)]IReadOnlyList<Document> documents,
            TraceWriter log)
        {
            if (documents != null && documents.Count > 0)
            {
                log.Info($"Documents modified: {documents.Count}");
                log.Info($"First document Id: {documents[0].Id}");
            }
        }
    }
}
```

# <a name="c-script"></a>[C#Parancsfájl](#tab/csharp-script)

Az alábbi példa egy Cosmos db trigger kötést mutat be egy *function. JSON* fájlban, valamint egy olyan [ C# parancsfájl-függvényt](functions-reference-csharp.md) , amely a kötést használja. A függvény naplóüzenetek ír, Cosmos DB-rekordok módosításakor.

Itt található a *function. JSON* fájlban található kötési adat:

```json
{
    "type": "cosmosDBTrigger",
    "name": "documents",
    "direction": "in",
    "leaseCollectionName": "leases",
    "connectionStringSetting": "<connection-app-setting>",
    "databaseName": "Tasks",
    "collectionName": "Items",
    "createLeaseCollectionIfNotExists": true
}
```

Íme a C#-szkriptkódot:

```cs
    #r "Microsoft.Azure.Documents.Client"
    
    using System;
    using Microsoft.Azure.Documents;
    using System.Collections.Generic;
    

    public static void Run(IReadOnlyList<Document> documents, TraceWriter log)
    {
        log.Info("Documents modified " + documents.Count);
        log.Info("First document Id " + documents[0].Id);
    }
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Az alábbi példa egy Cosmos DB trigger kötést mutat be egy *function. JSON* fájlban, valamint egy [JavaScript-függvényt](functions-reference-node.md) , amely a kötést használja. A függvény naplóüzenetek ír, Cosmos DB-rekordok módosításakor.

Itt található a *function. JSON* fájlban található kötési adat:

```json
{
    "type": "cosmosDBTrigger",
    "name": "documents",
    "direction": "in",
    "leaseCollectionName": "leases",
    "connectionStringSetting": "<connection-app-setting>",
    "databaseName": "Tasks",
    "collectionName": "Items",
    "createLeaseCollectionIfNotExists": true
}
```

A következő JavaScript-kódot:

```javascript
    module.exports = function (context, documents) {
        context.log('First document Id modified : ', documents[0].id);

        context.done();
    }
```

---

## <a name="trigger---attributes"></a>Eseményindító - attribútumok

# <a name="c"></a>[C#](#tab/csharp)

Az [ C# osztályok könyvtáraiban](functions-dotnet-class-library.md)használja a [CosmosDBTrigger](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.CosmosDB/Trigger/CosmosDBTriggerAttribute.cs) attribútumot.

Az attribútum konstruktorának paramétereként meg az adatbázis és gyűjtemény nevét. További információ ezekről a beállításokról és a konfigurálható egyéb tulajdonságokról: [trigger-Configuration](#trigger---configuration). Példa `CosmosDBTrigger` attribútumra a metódus-aláírásban:

```csharp
    [FunctionName("DocumentUpdates")]
    public static void Run(
        [CosmosDBTrigger("database", "collection", ConnectionStringSetting = "myCosmosDB")]
    IReadOnlyList<Document> documents,
        TraceWriter log)
    {
        ...
    }
```

A teljes példa: [trigger- C# example](#trigger).

# <a name="c-script"></a>[C#Parancsfájl](#tab/csharp-script)

Az C# attribútumokat a parancsfájl nem támogatja.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

A JavaScript nem támogatja az attribútumokat.

---

## <a name="trigger---configuration"></a>Eseményindító - konfiguráció

Az alábbi táblázat a *function. JSON* fájlban és a `CosmosDBTrigger` attribútumban beállított kötési konfigurációs tulajdonságokat ismerteti.

|Function.JSON tulajdonság | Attribútum tulajdonsága |Leírás|
|---------|---------|----------------------|
|**type** | n/a | `cosmosDBTrigger`értékre kell állítani. |
|**direction** | n/a | `in`értékre kell állítani. Ez a paraméter beállítása automatikusan történik, amikor az eseményindító hoz létre az Azure Portalon. |
|**név** | n/a | A függvénykód módosítása dokumentumok listájának jelölő a használt változó neve. |
|**connectionStringSetting**|**ConnectionStringSetting** | A figyelt Azure Cosmos DB-fiókhoz való csatlakozáshoz használt kapcsolati karakterlánc tartalmazó alkalmazásbeállítás neve. |
|**databaseName**|**DatabaseName**  | A figyelt gyűjteményhez az Azure Cosmos DB-adatbázis neve. |
|**collectionName** |**CollectionName** | A figyelt gyűjtemény neve. |
|**leaseConnectionStringSetting** | **LeaseConnectionStringSetting** | (Nem kötelező) A kapcsolati karakterláncot a szolgáltatásnak, amely tartalmazza a bérletek gyűjteményének tartalmazó alkalmazásbeállítás neve. Ha nincs beállítva, a rendszer a `connectionStringSetting` értéket használja. Ez a paraméter értéke a portálon a kötés létrehozásakor automatikusan. A bérletek gyűjteménye kapcsolati karakterláncára írási engedéllyel kell rendelkeznie.|
|**leaseDatabaseName** |**LeaseDatabaseName** | (Nem kötelező) A database csatlakoztatásához használt bérletek tárolásához használni kívánt gyűjtemény neve. Ha nincs beállítva, a rendszer a `databaseName` beállítás értékét használja. Ez a paraméter értéke a portálon a kötés létrehozásakor automatikusan. |
|**leaseCollectionName** | **LeaseCollectionName** | (Nem kötelező) A használt bérletek tárolásához használni kívánt gyűjtemény neve. Ha nincs beállítva, a rendszer a `leases` értéket használja. |
|**createLeaseCollectionIfNotExists** | **CreateLeaseCollectionIfNotExists** | Választható Ha `true`értékre van állítva, a bérletek gyűjteménye automatikusan létrejön, ha még nem létezik. Az alapértelmezett érték `false`. |
|**leasesCollectionThroughput**| **LeasesCollectionThroughput**| (Nem kötelező) A Kérelemegységek rendel a bérletek gyűjteménye létrehozásakor mennyisége határozza meg. Ezt a beállítást csak akkor használja a rendszer, ha a `createLeaseCollectionIfNotExists` `true`ra van beállítva. A rendszer automatikusan beállítja ezt a paramétert, ha a kötést a portál használatával hozza létre.
|**leaseCollectionPrefix**| **LeaseCollectionPrefix**| (Nem kötelező) Érték beállítása esetén azt ad hozzá egy előtagot a bérletek a bérletek gyűjteményének ennél a függvénynél, hatékonyan lehetővé teszi két különálló Azure Functions, az ugyanazon a bérletek gyűjteményének megosztása más előtagok használatával létrehozott.
|**feedPollDelay**| **FeedPollDelay**| (Nem kötelező) Ha a készlet, azt határozza meg, ezredmásodpercben, a késleltetés között egy partíciót a hírcsatorna az új módosításokat a lekérdezések összes aktuális változtatások vannak ürítve. Alapértelmezés szerint 5000 (5 másodperces).
|**leaseAcquireInterval**| **LeaseAcquireInterval**| (Nem kötelező) Érték beállítása esetén azt határozza meg, ezredmásodpercben, az időköz elindít egy feladatot a számítási, ha a partíciók lesznek elosztva a gazdagép ismert példányok között. Alapértelmezés szerint 13000 (13 másodperc).
|**leaseExpirationInterval**| **LeaseExpirationInterval**| (Nem kötelező) Érték beállítása esetén azt határozza meg, ezredmásodpercben, az időköz, amelynek a bérlet egy bérletet, egy partíciót jelölő készül. A bérlet ezen az időtartamon belül nem újítja meg, ha azt eredményezi, hamarosan lejár, és a partíció tulajdonjogának áthelyezi egy másik példánya. Alapértelmezés szerint 60000 (60 másodperc).
|**leaseRenewInterval**| **LeaseRenewInterval**| (Nem kötelező) Érték beállítása esetén azt határozza meg, ezredmásodpercben, minden bérletek példány által jelenleg birtokolt partíciók megújítási időköz. Alapértelmezés szerint 17000 (17 másodperc).
|**checkpointFrequency**| **CheckpointFrequency**| (Nem kötelező) Érték beállítása esetén azt határozza meg, ezredmásodpercben, a bérlet ellenőrzőpontok közötti időtartam. Az alapértelmezett érték mindig az egyes függvények hívása után történik.
|**maxItemsPerInvocation**| **MaxItemsPerInvocation**| (Nem kötelező) Ha a beállítás, azt testreszabja egy függvény hívásához szükséges fogadott elemek maximális számát.
|**startFromBeginning**| **StartFromBeginning**| Választható Ha be van állítva, a elindítja a triggert, hogy a jelenlegi időpont helyett a gyűjtemény előzményeinek elejéről olvassa be a módosításokat. Ez csak az indítás első indításakor működik, ahogy a későbbi futtatásokban is, az ellenőrzőpontok már tárolva vannak. Ha úgy állítja be ezt a `true`, hogy a bérletek már létrejöttek, nincs hatása.

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="trigger---usage"></a>Eseményindító - használat

Az triggerhez egy második gyűjteményre van szükség, amelyet a a partíciók _bérletének_ tárolására használ. A figyelt gyűjtemény és a gyűjtemény, amely tartalmazza a bérleteket is működnek az eseményindító elérhetőnek kell lennie.

>[!IMPORTANT]
> Ha több függvény úgy van konfigurálva, hogy egy Cosmos DB triggert használjon ugyanahhoz a gyűjteményhez, a függvények mindegyikének dedikált címbérleti gyűjteményt kell használnia, vagy más `LeaseCollectionPrefix` kell megadnia az egyes függvényekhez. Ellenkező esetben a függvények csak az egyik aktiválódik. További információ az előtagról: [konfiguráció szakasz](#trigger---configuration).

Az eseményindító egy dokumentumot frissítve lett, vagy beszúrva, azt csak biztosítja-e a saját maga a dokumentum nem jelzi. Ha frissítések, és beszúr eltérően kezelésére van szüksége, beszúrási vagy frissítési időbélyegző mezők az életbe léptetésével ezt.

## <a name="input"></a>Input (Bemenet)

Az Azure Cosmos DB bemeneti kötéssel legalább egy Azure Cosmos DB-dokumentumok lekérdezésének az SQL API-t használja, és továbbítja őket a függvény a bemeneti paraméter. A dokumentum azonosítója vagy lekérdezési paramétereket az eseményindítót, amely meghívja a függvényt alapján lehet meghatározni.

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
namespace CosmosDBSamplesV1
{
    public class ToDoItem
    {
        public string Id { get; set; }
        public string Description { get; set; }
    }
}
```

<a id="queue-trigger-look-up-id-from-json-c"></a>

### <a name="queue-trigger-look-up-id-from-json"></a>Üzenetsor-trigger, Keresés azonosító a JSON-ből

Az alábbi példa egy olyan [ C# függvényt](functions-dotnet-class-library.md) mutat be, amely egyetlen dokumentumot kér le. A függvény, amely tartalmaz egy JSON-objektum egy üzenetsor által aktivált. A várólista-trigger elemzi a JSON-t egy `ToDoItemLookup`nevű objektumba, amely tartalmazza a megkeresni kívánt azonosítót. Ez az azonosító egy `ToDoItem` dokumentum lekérésére szolgál a megadott adatbázisból és gyűjteményből.

```cs
namespace CosmosDBSamplesV1
{
    public class ToDoItemLookup
    {
        public string ToDoItemId { get; set; }
    }
}
```

```cs
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Host;

namespace CosmosDBSamplesV1
{
    public static class DocByIdFromJSON
    {
        [FunctionName("DocByIdFromJSON")]
        public static void Run(
            [QueueTrigger("todoqueueforlookup")] ToDoItemLookup toDoItemLookup,
            [DocumentDB(
                databaseName: "ToDoItems",
                collectionName: "Items",
                ConnectionStringSetting = "CosmosDBConnection",
                Id = "{ToDoItemId}")]ToDoItem toDoItem,
            TraceWriter log)
        {
            log.Info($"C# Queue trigger function processed Id={toDoItemLookup?.ToDoItemId}");

            if (toDoItem == null)
            {
                log.Info($"ToDo item not found");
            }
            else
            {
                log.Info($"Found ToDo item, Description={toDoItem.Description}");
            }
        }
    }
}
```

<a id="http-trigger-look-up-id-from-query-string-c"></a>

### <a name="http-trigger-look-up-id-from-query-string"></a>HTTP-trigger, keresési azonosító lekérdezési karakterláncból

Az alábbi példa egy olyan [ C# függvényt](functions-dotnet-class-library.md) mutat be, amely egyetlen dokumentumot kér le. A függvényt egy HTTP-kérelem keresse ki az azonosító megadása egy lekérdezési karakterláncot használó aktiválja. Ez az azonosító egy `ToDoItem` dokumentum lekérésére szolgál a megadott adatbázisból és gyűjteményből.

```cs
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.Http;
using Microsoft.Azure.WebJobs.Host;
using System.Net;
using System.Net.Http;

namespace CosmosDBSamplesV1
{
    public static class DocByIdFromQueryString
    {
        [FunctionName("DocByIdFromQueryString")]
        public static HttpResponseMessage Run(
            [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post", Route = null)]HttpRequestMessage req,
            [DocumentDB(
                databaseName: "ToDoItems",
                collectionName: "Items",
                ConnectionStringSetting = "CosmosDBConnection",
                Id = "{Query.id}")] ToDoItem toDoItem,
            TraceWriter log)
        {
            log.Info("C# HTTP trigger function processed a request.");
            if (toDoItem == null)
            {
                log.Info($"ToDo item not found");
            }
            else
            {
                log.Info($"Found ToDo item, Description={toDoItem.Description}");
            }
            return req.CreateResponse(HttpStatusCode.OK);
        }
    }
}
```

<a id="http-trigger-look-up-id-from-route-data-c"></a>

### <a name="http-trigger-look-up-id-from-route-data"></a>HTTP-trigger, azonosító megkeresése az útvonal adatainak alapján

Az alábbi példa egy olyan [ C# függvényt](functions-dotnet-class-library.md) mutat be, amely egyetlen dokumentumot kér le. A függvény akkor indul el, hogy a használt, keresse ki az azonosító megadása adatainak átirányítása a HTTP-kérelemmel történik. Ez az azonosító egy `ToDoItem` dokumentum lekérésére szolgál a megadott adatbázisból és gyűjteményből.

```cs
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.Http;
using Microsoft.Azure.WebJobs.Host;
using System.Net;
using System.Net.Http;

namespace CosmosDBSamplesV1
{
    public static class DocByIdFromRouteData
    {
        [FunctionName("DocByIdFromRouteData")]
        public static HttpResponseMessage Run(
            [HttpTrigger(
                AuthorizationLevel.Anonymous, "get", "post",
                Route = "todoitems/{id}")]HttpRequestMessage req,
            [DocumentDB(
                databaseName: "ToDoItems",
                collectionName: "Items",
                ConnectionStringSetting = "CosmosDBConnection",
                Id = "{id}")] ToDoItem toDoItem,
            TraceWriter log)
        {
            log.Info("C# HTTP trigger function processed a request.");

            if (toDoItem == null)
            {
                log.Info($"ToDo item not found");
            }
            else
            {
                log.Info($"Found ToDo item, Description={toDoItem.Description}");
            }
            return req.CreateResponse(HttpStatusCode.OK);
        }
    }
}
```

[Bemeneti példák kihagyása](#input---attributes)

<a id="http-trigger-look-up-id-from-route-data-using-sqlquery-c"></a>

### <a name="http-trigger-look-up-id-from-route-data-using-sqlquery"></a>HTTP-trigger, keresés az útvonal adatai alapján, SqlQuery használatával

Az alábbi példa egy olyan [ C# függvényt](functions-dotnet-class-library.md) mutat be, amely egyetlen dokumentumot kér le. A függvény akkor indul el, hogy a használt, keresse ki az azonosító megadása adatainak átirányítása a HTTP-kérelemmel történik. Ez az azonosító egy `ToDoItem` dokumentum lekérésére szolgál a megadott adatbázisból és gyűjteményből.

```cs
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.Http;
using Microsoft.Azure.WebJobs.Host;
using System.Collections.Generic;
using System.Net;
using System.Net.Http;

namespace CosmosDBSamplesV1
{
    public static class DocByIdFromRouteDataUsingSqlQuery
    {
        [FunctionName("DocByIdFromRouteDataUsingSqlQuery")]
        public static HttpResponseMessage Run(
            [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post",
                Route = "todoitems2/{id}")]HttpRequestMessage req,
            [DocumentDB(
                databaseName: "ToDoItems",
                collectionName: "Items",
                ConnectionStringSetting = "CosmosDBConnection",
                SqlQuery = "select * from ToDoItems r where r.id = {id}")] IEnumerable<ToDoItem> toDoItems,
            TraceWriter log)
        {
            log.Info("C# HTTP trigger function processed a request.");
            foreach (ToDoItem toDoItem in toDoItems)
            {
                log.Info(toDoItem.Description);
            }
            return req.CreateResponse(HttpStatusCode.OK);
        }
    }
}
```

[Bemeneti példák kihagyása](#input---attributes)

<a id="http-trigger-get-multiple-docs-using-sqlquery-c"></a>

### <a name="http-trigger-get-multiple-docs-using-sqlquery"></a>HTTP-trigger, több dokumentum beolvasása a SqlQuery használatával

Az alábbi példa egy olyan [ C# függvényt](functions-dotnet-class-library.md) mutat be, amely lekéri a dokumentumok listáját. A függvényt egy HTTP-kérelem aktiválja. A lekérdezés a `SqlQuery` attribútum tulajdonságban van megadva.

```cs
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.Http;
using Microsoft.Azure.WebJobs.Host;
using System.Collections.Generic;
using System.Net;
using System.Net.Http;

namespace CosmosDBSamplesV1
{
    public static class DocsBySqlQuery
    {
        [FunctionName("DocsBySqlQuery")]
        public static HttpResponseMessage Run(
            [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post", Route = null)]
                HttpRequestMessage req,
            [DocumentDB(
                databaseName: "ToDoItems",
                collectionName: "Items",
                ConnectionStringSetting = "CosmosDBConnection",
                SqlQuery = "SELECT top 2 * FROM c order by c._ts desc")]
                IEnumerable<ToDoItem> toDoItems,
            TraceWriter log)
        {
            log.Info("C# HTTP trigger function processed a request.");
            foreach (ToDoItem toDoItem in toDoItems)
            {
                log.Info(toDoItem.Description);
            }
            return req.CreateResponse(HttpStatusCode.OK);
        }
    }
}
```

[Bemeneti példák kihagyása](#input---attributes)

<a id="http-trigger-get-multiple-docs-using-documentclient-c"></a>

### <a name="http-trigger-get-multiple-docs-using-documentclient-c"></a>HTTP-trigger, a több docs DocumentClient (C#) használatával

Az alábbi példa egy olyan [ C# függvényt](functions-dotnet-class-library.md) mutat be, amely lekéri a dokumentumok listáját. A függvényt egy HTTP-kérelem aktiválja. A kód a Azure Cosmos DB kötés által megadott `DocumentClient` példányt használja a dokumentumok listájának olvasásához. Az `DocumentClient` példány írási műveletekhez is használható.

```cs
using Microsoft.Azure.Documents.Client;
using Microsoft.Azure.Documents.Linq;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.Http;
using Microsoft.Azure.WebJobs.Host;
using System;
using System.Linq;
using System.Net;
using System.Net.Http;
using System.Threading.Tasks;

namespace CosmosDBSamplesV1
{
    public static class DocsByUsingDocumentClient
    {
        [FunctionName("DocsByUsingDocumentClient")]
        public static async Task<HttpResponseMessage> Run(
            [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post", Route = null)]HttpRequestMessage req,
            [DocumentDB(
                databaseName: "ToDoItems",
                collectionName: "Items",
                ConnectionStringSetting = "CosmosDBConnection")] DocumentClient client,
            TraceWriter log)
        {
            log.Info("C# HTTP trigger function processed a request.");

            Uri collectionUri = UriFactory.CreateDocumentCollectionUri("ToDoItems", "Items");
            string searchterm = req.GetQueryNameValuePairs()
                .FirstOrDefault(q => string.Compare(q.Key, "searchterm", true) == 0)
                .Value;

            if (searchterm == null)
            {
                return req.CreateResponse(HttpStatusCode.NotFound);
            }

            log.Info($"Searching for word: {searchterm} using Uri: {collectionUri.ToString()}");
            IDocumentQuery<ToDoItem> query = client.CreateDocumentQuery<ToDoItem>(collectionUri)
                .Where(p => p.Description.Contains(searchterm))
                .AsDocumentQuery();

            while (query.HasMoreResults)
            {
                foreach (ToDoItem result in await query.ExecuteNextAsync())
                {
                    log.Info(result.Description);
                }
            }
            return req.CreateResponse(HttpStatusCode.OK);
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
namespace CosmosDBSamplesV1
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
    "type": "documentDB",
    "databaseName": "MyDatabase",
    "collectionName": "MyCollection",
    "id" : "{queueTrigger}",
    "partitionKey": "{partition key value}",
    "connection": "MyAccount_COSMOSDB",
    "direction": "in"
}
```

A [konfigurációs](#input---configuration) szakasz ezeket a tulajdonságokat ismerteti.

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
    "type": "documentdb",
    "direction": "in",
    "databaseName": "MyDb",
    "collectionName": "MyCollection",
    "sqlQuery": "SELECT * from c where c.departmentId = {departmentId}",
    "connection": "CosmosDBConnection"
}
```

A [konfigurációs](#input---configuration) szakasz ezeket a tulajdonságokat ismerteti.

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

Az alábbi példa egy olyan [ C# parancsfájl-függvényt](functions-reference-csharp.md) mutat be, amely egyetlen dokumentumot kér le. A függvényt egy HTTP-kérelem keresse ki az azonosító megadása egy lekérdezési karakterláncot használó aktiválja. Ez az azonosító egy `ToDoItem` dokumentum lekérésére szolgál a megadott adatbázisból és gyűjteményből.

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
      "type": "documentDB",
      "name": "toDoItem",
      "databaseName": "ToDoItems",
      "collectionName": "Items",
      "connection": "CosmosDBConnection",
      "direction": "in",
      "Id": "{Query.id}"
    }
  ],
  "disabled": true
}
```

Íme a C#-szkriptkódot:

```cs
using System.Net;

public static HttpResponseMessage Run(HttpRequestMessage req, ToDoItem toDoItem, TraceWriter log)
{
    log.Info("C# HTTP trigger function processed a request.");

    if (toDoItem == null)
    {
        log.Info($"ToDo item not found");
    }
    else
    {
        log.Info($"Found ToDo item, Description={toDoItem.Description}");
    }
    return req.CreateResponse(HttpStatusCode.OK);
}
```

<a id="http-trigger-look-up-id-from-route-data-c-script"></a>

### <a name="http-trigger-look-up-id-from-route-data"></a>HTTP-trigger, azonosító megkeresése az útvonal adatainak alapján

Az alábbi példa egy olyan [ C# parancsfájl-függvényt](functions-reference-csharp.md) mutat be, amely egyetlen dokumentumot kér le. A függvény akkor indul el, hogy a használt, keresse ki az azonosító megadása adatainak átirányítása a HTTP-kérelemmel történik. Ez az azonosító egy `ToDoItem` dokumentum lekérésére szolgál a megadott adatbázisból és gyűjteményből.

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
      "route":"todoitems/{id}"
    },
    {
      "name": "$return",
      "type": "http",
      "direction": "out"
    },
    {
      "type": "documentDB",
      "name": "toDoItem",
      "databaseName": "ToDoItems",
      "collectionName": "Items",
      "connection": "CosmosDBConnection",
      "direction": "in",
      "Id": "{id}"
    }
  ],
  "disabled": false
}
```

Íme a C#-szkriptkódot:

```cs
using System.Net;

public static HttpResponseMessage Run(HttpRequestMessage req, ToDoItem toDoItem, TraceWriter log)
{
    log.Info("C# HTTP trigger function processed a request.");

    if (toDoItem == null)
    {
        log.Info($"ToDo item not found");
    }
    else
    {
        log.Info($"Found ToDo item, Description={toDoItem.Description}");
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
      "type": "documentDB",
      "name": "toDoItems",
      "databaseName": "ToDoItems",
      "collectionName": "Items",
      "connection": "CosmosDBConnection",
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

public static HttpResponseMessage Run(HttpRequestMessage req, IEnumerable<ToDoItem> toDoItems, TraceWriter log)
{
    log.Info("C# HTTP trigger function processed a request.");

    foreach (ToDoItem toDoItem in toDoItems)
    {
        log.Info(toDoItem.Description);
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
      "type": "documentDB",
      "name": "client",
      "databaseName": "ToDoItems",
      "collectionName": "Items",
      "connection": "CosmosDBConnection",
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

public static async Task<HttpResponseMessage> Run(HttpRequestMessage req, DocumentClient client, TraceWriter log)
{
    log.Info("C# HTTP trigger function processed a request.");

    Uri collectionUri = UriFactory.CreateDocumentCollectionUri("ToDoItems", "Items");
    string searchterm = req.GetQueryNameValuePairs()
        .FirstOrDefault(q => string.Compare(q.Key, "searchterm", true) == 0)
        .Value;

    if (searchterm == null)
    {
        return req.CreateResponse(HttpStatusCode.NotFound);
    }

    log.Info($"Searching for word: {searchterm} using Uri: {collectionUri.ToString()}");
    IDocumentQuery<ToDoItem> query = client.CreateDocumentQuery<ToDoItem>(collectionUri)
        .Where(p => p.Description.Contains(searchterm))
        .AsDocumentQuery();

    while (query.HasMoreResults)
    {
        foreach (ToDoItem result in await query.ExecuteNextAsync())
        {
            log.Info(result.Description);
        }
    }
    return req.CreateResponse(HttpStatusCode.OK);
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Ez a szakasz tartalmazza az alábbi példák:

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
    "type": "documentDB",
    "databaseName": "MyDatabase",
    "collectionName": "MyCollection",
    "id" : "{queueTrigger_payload_property}",
    "partitionKey": "{queueTrigger_payload_property}",
    "connection": "MyAccount_COSMOSDB",
    "direction": "in"
},
{
    "name": "inputDocumentOut",
    "type": "documentDB",
    "databaseName": "MyDatabase",
    "collectionName": "MyCollection",
    "createIfNotExists": false,
    "partitionKey": "{queueTrigger_payload_property}",
    "connection": "MyAccount_COSMOSDB",
    "direction": "out"
}
```

A [konfigurációs](#input---configuration) szakasz ezeket a tulajdonságokat ismerteti.

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

Az alábbi példa egy [JavaScript-függvényt](functions-reference-node.md) mutat be, amely egyetlen dokumentumot kér le. A függvényt egy HTTP-kérelem keresse ki az azonosító megadása egy lekérdezési karakterláncot használó aktiválja. Ez az azonosító egy `ToDoItem` dokumentum lekérésére szolgál a megadott adatbázisból és gyűjteményből.

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
      "type": "documentDB",
      "name": "toDoItem",
      "databaseName": "ToDoItems",
      "collectionName": "Items",
      "connection": "CosmosDBConnection",
      "direction": "in",
      "Id": "{Query.id}"
    }
  ],
  "disabled": true
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

Az alábbi példa egy [JavaScript-függvényt](functions-reference-node.md) mutat be, amely egyetlen dokumentumot kér le. A függvényt egy HTTP-kérelem keresse ki az azonosító megadása egy lekérdezési karakterláncot használó aktiválja. Ez az azonosító egy `ToDoItem` dokumentum lekérésére szolgál a megadott adatbázisból és gyűjteményből.

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
      "route":"todoitems/{id}"
    },
    {
      "name": "$return",
      "type": "http",
      "direction": "out"
    },
    {
      "type": "documentDB",
      "name": "toDoItem",
      "databaseName": "ToDoItems",
      "collectionName": "Items",
      "connection": "CosmosDBConnection",
      "direction": "in",
      "Id": "{id}"
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
    "type": "documentdb",
    "direction": "in",
    "databaseName": "MyDb",
    "collectionName": "MyCollection",
    "sqlQuery": "SELECT * from c where c.departmentId = {departmentId}",
    "connection": "CosmosDBConnection"
}
```

A [konfigurációs](#input---configuration) szakasz ezeket a tulajdonságokat ismerteti.

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

---

## <a name="input---attributes"></a>Bemenet - attribútumok

# <a name="c"></a>[C#](#tab/csharp)

Az [ C# osztályok könyvtáraiban](functions-dotnet-class-library.md)használja a [DocumentDB](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/v2.x/src/WebJobs.Extensions.DocumentDB/DocumentDBAttribute.cs) attribútumot.

Az attribútum konstruktorának paramétereként meg az adatbázis és gyűjtemény nevét. Ezekről a beállításokról és a konfigurálható egyéb tulajdonságokról [a következő konfigurációs szakaszban](#input---configuration)tájékozódhat.

# <a name="c-script"></a>[C#Parancsfájl](#tab/csharp-script)

Az C# attribútumokat a parancsfájl nem támogatja.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

A JavaScript nem támogatja az attribútumokat.

---

## <a name="input---configuration"></a>Bemenet - konfiguráció

Az alábbi táblázat a *function. JSON* fájlban és a `DocumentDB` attribútumban beállított kötési konfigurációs tulajdonságokat ismerteti.

|Function.JSON tulajdonság | Attribútum tulajdonsága |Leírás|
|---------|---------|----------------------|
|**type**     | n/a | `documentdb`értékre kell állítani.        |
|**direction**     | n/a | `in`értékre kell állítani.         |
|**név**     | n/a | A kötési paraméter, amely a dokumentumot, a függvény neve.  |
|**databaseName** |**DatabaseName** |A dokumentum tartalmazó adatbázis.        |
|**collectionName** |**CollectionName** | A gyűjtemény, amely tartalmazza a dokumentum neve. |
|**ID**    | **Azonosító** | A lekérdezni kívánt a dokumentum Azonosítóját. Ez a tulajdonság támogatja a [kötési kifejezéseket](./functions-bindings-expressions-patterns.md). Ne állítsa be az **ID** és a **sqlQuery** tulajdonságokat is. Ha nem állít be egy, a rendszer lekéri a teljes gyűjteményt. |
|**sqlQuery**  |**SqlQuery**  | Egy Azure Cosmos DB SQL-lekérdezés több dokumentumok használt. A tulajdonság támogatja a futásidejű kötéseket, ahogy az a következő példában látható: `SELECT * FROM c where c.departmentId = {departmentId}`. Ne állítsa be az **ID** és a **sqlQuery** tulajdonságokat is. Ha nem állít be egy, a rendszer lekéri a teljes gyűjteményt.|
|**kapcsolat**     |**ConnectionStringSetting**|Az Azure Cosmos DB kapcsolati sztringjét tartalmazó alkalmazásbeállítás neve.        |
|**partitionKey**|**PartitionKey**|Megadja a partíciós kulcs értékét a kereséshez. Előfordulhat, hogy tartalmazza a kötési paramétereket.|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="input---usage"></a>Bemenet - használat

# <a name="c"></a>[C#](#tab/csharp)

Ha a függvény sikeresen kilép, az elnevezett bemeneti paramétereken keresztül végrehajtott módosítások automatikusan megmaradnak.

# <a name="c-script"></a>[C#Parancsfájl](#tab/csharp-script)

Ha a függvény sikeresen kilép, az elnevezett bemeneti paramétereken keresztül végrehajtott módosítások automatikusan megmaradnak.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

A rendszer nem végez automatikusan frissítéseket a függvény kilépése után. Ehelyett a `context.bindings.<documentName>In` és a `context.bindings.<documentName>Out` használatával végezze el a frissítéseket. Tekintse meg a [bemeneti példát](#input).

---

## <a name="output"></a>Kimenet

Az Azure Cosmos DB kimeneti kötés lehetővé teszi, hogy egy Azure Cosmos DB-adatbázishoz az SQL API használatával írhat egy új dokumentumot.

# <a name="c"></a>[C#](#tab/csharp)

Ez a szakasz tartalmazza az alábbi példák:

* Üzenetsor eseményindító írási egy dokumentumot
* Üzenetsor-trigger, dokumentumok írása `IAsyncCollector` használatával

A példák egy egyszerű `ToDoItem` típusra vonatkoznak:

```cs
namespace CosmosDBSamplesV1
{
    public class ToDoItem
    {
        public string Id { get; set; }
        public string Description { get; set; }
    }
}
```

### <a name="queue-trigger-write-one-doc"></a>Üzenetsor eseményindító írási egy dokumentumot

Az alábbi példa egy olyan [ C# függvényt](functions-dotnet-class-library.md) mutat be, amely egy dokumentumot ad hozzá egy adatbázishoz, a várólista-tárolóból származó adatok felhasználásával.

```cs
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Host;
using System;

namespace CosmosDBSamplesV1
{
    public static class WriteOneDoc
    {
        [FunctionName("WriteOneDoc")]
        public static void Run(
            [QueueTrigger("todoqueueforwrite")] string queueMessage,
            [DocumentDB(
                databaseName: "ToDoItems",
                collectionName: "Items",
                ConnectionStringSetting = "CosmosDBConnection")]out dynamic document,
            TraceWriter log)
        {
            document = new { Description = queueMessage, id = Guid.NewGuid() };

            log.Info($"C# Queue trigger function inserted one row");
            log.Info($"Description={queueMessage}");
        }
    }
}
```

### <a name="queue-trigger-write-docs-using-iasynccollector"></a>Üzenetsor eseményindító írási docs IAsyncCollector használatával

Az alábbi példa egy olyan [ C# függvényt](functions-dotnet-class-library.md) mutat be, amely dokumentumok gyűjteményét adja hozzá egy adatbázishoz egy üzenetsor-üzenet JSON-fájljában megadott adat használatával.

```cs
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Host;
using System.Threading.Tasks;

namespace CosmosDBSamplesV1
{
    public static class WriteDocsIAsyncCollector
    {
        [FunctionName("WriteDocsIAsyncCollector")]
        public static async Task Run(
            [QueueTrigger("todoqueueforwritemulti")] ToDoItem[] toDoItemsIn,
            [DocumentDB(
                databaseName: "ToDoItems",
                collectionName: "Items",
                ConnectionStringSetting = "CosmosDBConnection")]
                IAsyncCollector<ToDoItem> toDoItemsOut,
            TraceWriter log)
        {
            log.Info($"C# Queue trigger function processed {toDoItemsIn?.Length} items");

            foreach (ToDoItem toDoItem in toDoItemsIn)
            {
                log.Info($"Description={toDoItem.Description}");
                await toDoItemsOut.AddAsync(toDoItem);
            }
        }
    }
}
```

# <a name="c-script"></a>[C#Parancsfájl](#tab/csharp-script)

Ez a szakasz tartalmazza az alábbi példák:

* Üzenetsor eseményindító írási egy dokumentumot
* Üzenetsor-trigger, dokumentumok írása `IAsyncCollector` használatával

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
    "type": "documentDB",
    "databaseName": "MyDatabase",
    "collectionName": "MyCollection",
    "createIfNotExists": true,
    "connection": "MyAccount_COSMOSDB",
    "direction": "out"
}
```

A [konfigurációs](#output---configuration) szakasz ezeket a tulajdonságokat ismerteti.

Íme a C#-szkriptkódot:

```cs
    #r "Newtonsoft.Json"

    using Microsoft.Azure.WebJobs.Host;
    using Newtonsoft.Json.Linq;

    public static void Run(string myQueueItem, out object employeeDocument, TraceWriter log)
    {
        log.Info($"C# Queue trigger function processed: {myQueueItem}");

        dynamic employee = JObject.Parse(myQueueItem);

        employeeDocument = new {
            id = employee.name + "-" + employee.employeeId,
            name = employee.name,
            employeeId = employee.employeeId,
            address = employee.address
        };
    }
```

### <a name="queue-trigger-write-docs-using-iasynccollector"></a>Üzenetsor eseményindító írási docs IAsyncCollector használatával

Több dokumentum létrehozásához kötni `ICollector<T>` vagy `IAsyncCollector<T>`, ahol a `T` a támogatott típusok egyike.

Ez a példa egy egyszerű `ToDoItem` típusra hivatkozik:

```cs
namespace CosmosDBSamplesV1
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
      "connection": "AzureWebJobsStorage"
    },
    {
      "type": "documentDB",
      "name": "toDoItemsOut",
      "databaseName": "ToDoItems",
      "collectionName": "Items",
      "connection": "CosmosDBConnection",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

Íme a C#-szkriptkódot:

```cs
using System;

public static async Task Run(ToDoItem[] toDoItemsIn, IAsyncCollector<ToDoItem> toDoItemsOut, TraceWriter log)
{
    log.Info($"C# Queue trigger function processed {toDoItemsIn?.Length} items");

    foreach (ToDoItem toDoItem in toDoItemsIn)
    {
        log.Info($"Description={toDoItem.Description}");
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
    "type": "documentDB",
    "databaseName": "MyDatabase",
    "collectionName": "MyCollection",
    "createIfNotExists": true,
    "connection": "MyAccount_COSMOSDB",
    "direction": "out"
}
```

A [konfigurációs](#output---configuration) szakasz ezeket a tulajdonságokat ismerteti.

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

---

## <a name="output---attributes"></a>Kimenet – attribútumok

# <a name="c"></a>[C#](#tab/csharp)

Az [ C# osztályok könyvtáraiban](functions-dotnet-class-library.md)használja a [DocumentDB](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/v2.x/src/WebJobs.Extensions.DocumentDB/DocumentDBAttribute.cs) attribútumot.

Az attribútum konstruktorának paramétereként meg az adatbázis és gyűjtemény nevét. További információ ezekről a beállításokról és a konfigurálható egyéb tulajdonságokról: [kimeneti konfiguráció](#output---configuration). Példa `DocumentDB` attribútumra a metódus-aláírásban:

```csharp
    [FunctionName("QueueToDocDB")]
    public static void Run(
        [QueueTrigger("myqueue-items", Connection = "AzureWebJobsStorage")] string myQueueItem,
        [DocumentDB("ToDoList", "Items", Id = "id", ConnectionStringSetting = "myCosmosDB")] out dynamic document)
    {
        ...
    }
```

A teljes példa: output ( [kimenet](#output)).

# <a name="c-script"></a>[C#Parancsfájl](#tab/csharp-script)

Az C# attribútumokat a parancsfájl nem támogatja.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

A JavaScript nem támogatja az attribútumokat.

---

## <a name="output---configuration"></a>Kimenete – konfiguráció

Az alábbi táblázat a *function. JSON* fájlban és a `DocumentDB` attribútumban beállított kötési konfigurációs tulajdonságokat ismerteti.

|Function.JSON tulajdonság | Attribútum tulajdonsága |Leírás|
|---------|---------|----------------------|
|**type**     | n/a | `documentdb`értékre kell állítani.        |
|**direction**     | n/a | `out`értékre kell állítani.         |
|**név**     | n/a | A kötési paraméter, amely a dokumentumot, a függvény neve.  |
|**databaseName** | **DatabaseName**|Az a gyűjtemény, amelyben létrehozza a dokumentumban tartalmazó adatbázis.     |
|**collectionName** |**CollectionName**  | A gyűjtemény, amelyben létrehozza a dokumentumban neve. |
|**Createifnotexists metódust**  |**Createifnotexists metódust**    | Logikai érték jelzi, hogy a gyűjtemény létrehozásakor a rendszer még nem létezik. Az alapértelmezett érték a *false (hamis* ), mert az új gyűjtemények fenntartott átviteli sebességgel jönnek létre, ami a költségeket is érinti. További tájékoztatás a [díjszabási lapon](https://azure.microsoft.com/pricing/details/documentdb/) olvasható.  |
|**partitionKey**|**PartitionKey** |Ha a `CreateIfNotExists` értéke TRUE (igaz), a a létrehozott gyűjteményhez tartozó partíciós kulcs elérési útját határozza meg.|
|**collectionThroughput**|**CollectionThroughput**| Ha `CreateIfNotExists` értéke TRUE (igaz), meghatározza a létrehozott gyűjtemény [átviteli sebességét](../cosmos-db/set-throughput.md) .|
|**kapcsolat**    |**ConnectionStringSetting** |Az Azure Cosmos DB kapcsolati sztringjét tartalmazó alkalmazásbeállítás neve.        |

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="output---usage"></a>Kimenet – használat

Alapértelmezés szerint ha a függvényben a kimeneti paraméter írni egy dokumentum létrehozása az adatbázisban. Ebben a dokumentumban az automatikusan előállított GUID Azonosítóhoz megegyezik a dokumentum azonosítója. A kimeneti dokumentumhoz tartozó dokumentum AZONOSÍTÓjának megadásához adja meg a kimeneti paraméternek átadott JSON-objektum `id` tulajdonságát.

> [!Note]
> Létező dokumentum Azonosítóját adja meg, ha azt az új kimeneti dokumentum szerint felülíródik.

## <a name="exceptions-and-return-codes"></a>Kivételek és a visszatérési kódok

| Kötés | Referencia |
|---|---|
| CosmosDB | [CosmosDB-hibakódok](https://docs.microsoft.com/rest/api/cosmos-db/http-status-codes-for-cosmosdb) |

## <a name="next-steps"></a>Következő lépések

* [További információ a kiszolgáló nélküli adatbázis-számítástechnika Cosmos DB](../cosmos-db/serverless-computing-database.md)
* [További információ az Azure functions-eseményindítók és-kötésekről](functions-triggers-bindings.md)

<!---
> [!div class="nextstepaction"]
> [Go to a quickstart that uses a Cosmos DB trigger](functions-create-cosmos-db-triggered-function.md)
--->
