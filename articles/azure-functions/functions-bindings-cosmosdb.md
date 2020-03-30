---
title: Az Azure Cosmos DB kötései az 1.x függvényekhez
description: Ismerje meg, hogyan használhatja az Azure Cosmos DB eseményindítók és kötések az Azure Functionsben.
author: craigshoemaker
ms.author: cshoe
ms.topic: reference
ms.date: 11/21/2017
ms.custom: seodec18
ms.openlocfilehash: e30b256d9fa43402c3b2c444aa1a0e0dc16cfdcf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79277543"
---
# <a name="azure-cosmos-db-bindings-for-azure-functions-1x"></a>Az Azure Cosmos DB kötései az Azure Functions 1.x-hez

> [!div class="op_single_selector" title1="Válassza ki a használt Azure Functions futásidejű verzióját: "]
> * [1-es verzió](functions-bindings-cosmosdb.md)
> * [2-es verzió](functions-bindings-cosmosdb-v2.md)

Ez a cikk bemutatja, hogyan dolgozhat az [Azure Cosmos](../cosmos-db/serverless-computing-database.md) DB-kötésekkel az Azure Functionsben. Az Azure Functions támogatja az Azure Cosmos DB eseményindító, bemeneti és kimeneti kötéseit.

> [!NOTE]
> Ez a cikk az Azure Functions 1.x. A kötések functions 2.x-es és újabb verzióiban való használatáról az [Azure Functions 2.x Azure-funkciókhoz való Elintézendő konti.)](functions-bindings-cosmosdb-v2.md)
>
>Ez a kötés eredetileg DocumentDB volt. A Functions 1.x verziójában csak az eseményindító lett átnevezve Cosmos DB; a bemeneti kötés, a kimeneti kötés és a NuGet csomag megtartja a DocumentDB nevet.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

> [!NOTE]
> Az Azure Cosmos DB-kötések csak az SQL API-val használhatók. Az összes többi Azure Cosmos DB API-k, az adatbázis eléréséhez a függvény segítségével a statikus ügyfél az API-t, beleértve az [Azure Cosmos DB API-t a MongoDB,](../cosmos-db/mongodb-introduction.md) [Cassandra API,](../cosmos-db/cassandra-introduction.md) [Gremlin API](../cosmos-db/graph-introduction.md), és a Table [API.](../cosmos-db/table-introduction.md)

## <a name="packages---functions-1x"></a>Csomagok - 1.x függvények

Az Azure Cosmos DB-kötések a Functions 1.x-es verziójához a [Microsoft.Azure.WebJobs.Extensions.DocumentDB](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.DocumentDB) NuGet csomag 1.x-es verziójában találhatók. A kötések forráskódja az [azure-webjobs-sdk-extensions](https://github.com/Azure/azure-webjobs-sdk-extensions/tree/v2.x/src/WebJobs.Extensions.DocumentDB) GitHub-tárházban található.

[!INCLUDE [functions-package](../../includes/functions-package.md)]

## <a name="trigger"></a>Eseményindító

Az Azure Cosmos DB Trigger az [Azure Cosmos DB change feed](../cosmos-db/change-feed.md) használatával figyeli a beszúrások és frissítések partíciók között. A módosítási hírcsatorna beszúrásokat és frissítéseket tesz közzé, nem törléseket.

## <a name="trigger---example"></a>Trigger - példa

# <a name="c"></a>[C #](#tab/csharp)

A következő példa egy [C# függvényt](functions-dotnet-class-library.md) mutat be, amely et a megadott adatbázisban és gyűjteményben beszúrások vagy frissítések vannak meghívva.

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

# <a name="c-script"></a>[C# parancsfájl](#tab/csharp-script)

A következő példa egy Cosmos DB-eseményindítót mutat be egy *function.json* fájlban és egy [C# parancsfájlfüggvényt,](functions-reference-csharp.md) amely a kötést használja. A függvény naplóüzeneteket ír, amikor a Cosmos DB rekordok módosulnak.

A *function.json* fájlban a kötési adatok:

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

Itt a C# script kód:

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

# <a name="javascript"></a>[Javascript](#tab/javascript)

A következő példa egy Cosmos DB-eseményindítót mutat be egy *function.json* fájlban és egy [JavaScript-függvényt,](functions-reference-node.md) amely a kötést használja. A függvény naplóüzeneteket ír, amikor a Cosmos DB rekordok módosulnak.

A *function.json* fájlban a kötési adatok:

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

Itt a JavaScript-kód:

```javascript
    module.exports = function (context, documents) {
        context.log('First document Id modified : ', documents[0].id);

        context.done();
    }
```

---

## <a name="trigger---attributes"></a>Eseményindító - attribútumok

# <a name="c"></a>[C #](#tab/csharp)

A [C# osztálykönyvtárakban](functions-dotnet-class-library.md)használja a [CosmosDBTrigger](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.CosmosDB/Trigger/CosmosDBTriggerAttribute.cs) attribútumot.

Az attribútum konstruktora felveszi az adatbázis nevét és a gyűjtemény nevét. A beállításokkal és a konfigurálható egyéb tulajdonságokkal kapcsolatos további tudnivalókért olvassa el [az Eseményindító – konfiguráció](#trigger---configuration)című témakört. Íme egy `CosmosDBTrigger` attribútum példa egy metódus aláírás:

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

Egy teljes példa, [lásd: Trigger - C# példa.](#trigger)

# <a name="c-script"></a>[C# parancsfájl](#tab/csharp-script)

Az attribútumokat a C# script nem támogatja.

# <a name="javascript"></a>[Javascript](#tab/javascript)

A JavaScript nem támogatja az attribútumokat.

---

## <a name="trigger---configuration"></a>Trigger - konfiguráció

Az alábbi táblázat a *function.json* fájlban és az `CosmosDBTrigger` attribútumban beállított kötési konfigurációs tulajdonságokat ismerteti.

|function.json tulajdonság | Attribútum tulajdonság |Leírás|
|---------|---------|----------------------|
|**Típus** | n/a | A beállításnak `cosmosDBTrigger`a beállítására kell beállítható. |
|**direction** | n/a | A beállításnak `in`a beállítására kell beállítható. Ez a paraméter automatikusan be van állítva, amikor létrehozza az eseményindítót az Azure Portalon. |
|**név** | n/a | A függvénykódban használt változónév, amely a módosításokkal rendelkező dokumentumok listáját jelöli. |
|**connectionStringSetting**|**ConnectionStringSetting (Kapcsolatkarakterlánc-beállítás)** | A figyelt Azure Cosmos DB-fiókhoz való csatlakozáshoz használt kapcsolati karakterláncot tartalmazó alkalmazásbeállítás neve. |
|**adatbázisneve**|**DatabaseName**  | Az Azure Cosmos DB-adatbázis neve a gyűjtemény figyelt használatával. |
|**Lekérdezés_neve** |**CollectionName** | A figyelt gyűjtemény neve. |
|**leaseConnectionStringSetting** | **LeaseConnectionStringSetting** | (Nem kötelező) A címbérlet-gyűjteményt tartalmazó szolgáltatáshoz való kapcsolati karakterláncot tartalmazó alkalmazásbeállítás neve. Ha nincs beállítva, a függvény az `connectionStringSetting` értéket használja. Ez a paraméter automatikusan be van állítva, amikor a kötés jön létre a portálon. A címbérlet-gyűjtemény kapcsolati karakterláncának írási engedéllyel kell rendelkeznie.|
|**leaseDatabaseName** |**LeaseDatabaseName** | (Nem kötelező) Annak az adatbázisnak a neve, amely a bérletek tárolására használt gyűjteményt tárolja. Ha nincs beállítva, `databaseName` a beállítás értéke lesz használva. Ez a paraméter automatikusan be van állítva, amikor a kötés jön létre a portálon. |
|**leaseCollectionName** | **LeaseCollectionName** | (Nem kötelező) A bérletek tárolására használt gyűjtemény neve. Ha nincs beállítva, a függvény az értéket `leases` használja. |
|**createLeaseCollectionIfNotExists** | **CreateLeaseCollectionIfNotExists** | (Nem kötelező) Ha a `true`beállítás, a bérletgyűjtemény automatikusan létrejön, ha még nem létezik. Az alapértelmezett érték `false`. |
|**leasesCollectionThroughput**| **LeasesCollectionThroughput**| (Nem kötelező) A címbérlet-gyűjtemény létrehozásakor hozzárendelhető kérelemegységek mennyiségét határozza meg. Ez a beállítás `createLeaseCollectionIfNotExists` csak akkor `true`használatos: Ha a . Ez a paraméter automatikusan be van állítva, amikor a kötést a portál használatával hozza létre.
|**leaseCollectionPrefix**| **LeaseCollectionPrefix**| (Nem kötelező) Ha be van állítva, hozzáad egy előtagot a függvény bérleti gyűjteményében létrehozott bérletekhez, így gyakorlatilag lehetővé teszi, hogy két különálló Azure-függvény különböző előtagok használatával ugyanazt a címbérlet-gyűjteményt ossza meg.
|**feedPollDelay**| **FeedPollDelay (FeedPollDelay)**| (Nem kötelező) Ha be van állítva, ezredmásodpercben határozza meg a partíció lekérdezése közötti késleltetést a hírcsatorna új módosításaihoz, miután az összes aktuális módosítás kiürül. Az alapértelmezett érték 5000 (5 másodperc).
|**leaseAcquireInterval**| **LeaseAcquireInterval**| (Nem kötelező) Ha be van állítva, ezredmásodpercben határozza meg azt az időközt, amelyelindítja a feladatot, hogy kiszámítsa a partíciókat az ismert gazdapéldányok között egyenletesen elosztva. Az alapértelmezett érték 13000 (13 másodperc).
|**leaseExpirationInterval**| **LeaseExpirationInterval**| (Nem kötelező) Ha be van állítva, ezredmásodpercben határozza meg azt az időtartamot, amelyre a bérlet egy partíciót képviselő címbérletre kerül. Ha a bérlet nem újul meg ezen az időtartamon belül, akkor lejár, és a partíció tulajdonjoga egy másik példányba kerül. Az alapértelmezett érték 60000 (60 másodperc).
|**leaseRenewInterval között**| **LeaseRenewInterval között**| (Nem kötelező) Ha be van állítva, ezredmásodpercben határozza meg a megújítási időközt a partíciók jelenleg egy példány által birtokolt összes címbérletéhez. Az alapértelmezett érték 17000 (17 másodperc).
|**ellenőrzőpontGyakoriság**| **Ellenőrzőpontgyakoriság**| (Nem kötelező) Ha be van állítva, ezredmásodpercben határozza meg a címbérlet-ellenőrzőpontok közötti intervallumot. Az alapértelmezett érték mindig minden függvényhívás után történik.
|**maxItemsPerInvocation**| **MaxItemsPerInvocation**| (Nem kötelező) Ha be van állítva, testre szabja a függvényhívásonként fogadott elemek maximális mennyiségét.
|**startFromBeginning**| **Elejétől kezdve**| (Nem kötelező) Ha be van állítva, azt mondja az eseményindító nak, hogy a gyűjtemény előzményeinek elejétől kezdje el olvasni a módosításokat az aktuális idő helyett. Ez csak akkor működik, amikor az eseményindító először elindul, mint a későbbi futtatások, az ellenőrzőpontok már tárolják. Ha ezt `true` a beállítást úgy állítja be, hogy ha már vannak már létrehozott bérletek, annak nincs hatása.

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="trigger---usage"></a>Trigger - használat

Az eseményindító hoz egy második gyűjtemény, amely a partíciók on-át _bérletek_ tárolására használja. Mind a figyelt gyűjteménynek, mind a címbérleteket tartalmazó gyűjteménynek elérhetőnek kell lennie ahhoz, hogy az eseményindító működjön.

>[!IMPORTANT]
> Ha több függvény van konfigurálva egy Cosmos DB-eseményindító ugyanazon gyűjtemény, minden a függvények kell használnia egy dedikált címbérlet-gyűjtemény, vagy adja meg az egyes függvények egy másik. `LeaseCollectionPrefix` Ellenkező esetben csak az egyik funkció aktiválódik. Az előtagról a [Konfiguráció című szakaszban](#trigger---configuration)talál további információt.

Az eseményindító nem azt jelzi, hogy egy dokumentumot frissítettek vagy beszúrtak, csak magát a dokumentumot biztosítja. Ha a frissítéseket és a beszúrásokat eltérően kell kezelnie, ezt megteheti időbélyeg-mezők beillesztésre vagy frissítésre való megvalósításával.

## <a name="input"></a>Input (Bemenet)

Az Azure Cosmos DB bemeneti adatkötése az SQL API-t használja egy vagy több Azure Cosmos DB-dokumentum lekérésére, majd átadja ezeket a függvény bemeneti paraméterének. A dokumentumazonosítót vagy lekérdezési paramétereket a függvényt meghívó eseményindító alapján lehet meghatározni.

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

### <a name="queue-trigger-look-up-id-from-json"></a>Várólista-eseményindító, a JSON azonosítójának megnézése

A következő példa egy [C# függvényt](functions-dotnet-class-library.md) mutat be, amely egyetlen dokumentumot olvas be. A függvényt egy JSON-objektumot tartalmazó üzenetsor-üzenet váltja ki. A várólista-eseményindító a JSON-t egy objektum nevű `ToDoItemLookup`objektumba elemzi, amely tartalmazza a kikeresni a lapjául naneresztendő azonosítót. Ez az azonosító a `ToDoItem` megadott adatbázisból és gyűjteményből származó dokumentumok lekéréséhez használható.

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

### <a name="http-trigger-look-up-id-from-query-string"></a>HTTP-eseményindító, azonosító kinézete a lekérdezési karakterláncból

A következő példa egy [C# függvényt](functions-dotnet-class-library.md) mutat be, amely egyetlen dokumentumot olvas be. A függvényt egy olyan HTTP-kérés aktiválja, amely egy lekérdezési sztringgel adja meg a keresendő azonosítót. Ez az azonosító a `ToDoItem` megadott adatbázisból és gyűjteményből származó dokumentumok lekéréséhez használható.

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

### <a name="http-trigger-look-up-id-from-route-data"></a>HTTP-eseményindító, azonosító kinézete az útvonaladatokból

A következő példa egy [C# függvényt](functions-dotnet-class-library.md) mutat be, amely egyetlen dokumentumot olvas be. A függvényt egy HTTP-kérelem váltja ki, amely útvonaladatokat használ a megkeresni kívánt azonosító megadásához. Ez az azonosító a `ToDoItem` megadott adatbázisból és gyűjteményből származó dokumentumok lekéréséhez használható.

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

[Példabeviteli példák kihagyása](#input---attributes)

<a id="http-trigger-look-up-id-from-route-data-using-sqlquery-c"></a>

### <a name="http-trigger-look-up-id-from-route-data-using-sqlquery"></a>HTTP-eseményindító, azonosító kinézete az útvonaladatokból az SqlQuery használatával

A következő példa egy [C# függvényt](functions-dotnet-class-library.md) mutat be, amely egyetlen dokumentumot olvas be. A függvényt egy HTTP-kérelem váltja ki, amely útvonaladatokat használ a megkeresni kívánt azonosító megadásához. Ez az azonosító a `ToDoItem` megadott adatbázisból és gyűjteményből származó dokumentumok lekéréséhez használható.

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

[Példabeviteli példák kihagyása](#input---attributes)

<a id="http-trigger-get-multiple-docs-using-sqlquery-c"></a>

### <a name="http-trigger-get-multiple-docs-using-sqlquery"></a>HTTP-eseményindító, több dokumentum beszereznie az SqlQuery használatával

A következő példa egy [C# függvényt](functions-dotnet-class-library.md) mutat be, amely beolvassa a dokumentumok listáját. A függvényt egy HTTP-kérelem váltja ki. A lekérdezés az `SqlQuery` attribútumtulajdonságban van megadva.

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

[Példabeviteli példák kihagyása](#input---attributes)

<a id="http-trigger-get-multiple-docs-using-documentclient-c"></a>

### <a name="http-trigger-get-multiple-docs-using-documentclient-c"></a>HTTP-eseményindító, több dokumentum bekésezése a DocumentClient (C#) használatával

A következő példa egy [C# függvényt](functions-dotnet-class-library.md) mutat be, amely beolvassa a dokumentumok listáját. A függvényt egy HTTP-kérelem váltja ki. A kód `DocumentClient` az Azure Cosmos DB-kötés által biztosított példányt használja a dokumentumok listájának olvasásához. A `DocumentClient` példány írási műveletekhez is használható.

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

### <a name="queue-trigger-look-up-id-from-string"></a>Várólista-eseményindító, azonosító kinézete a karakterláncból

A következő példa egy Cosmos DB bemeneti kötést mutat be egy *function.json* fájlban és egy [C# parancsfájl függvényt,](functions-reference-csharp.md) amely a kötést használja. A függvény egyetlen dokumentumot olvas be, és frissíti a dokumentum szöveges értékét.

A *function.json* fájlban a kötési adatok:

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

A [konfigurációs](#input---configuration) szakasz ismerteti ezeket a tulajdonságokat.

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
    "type": "documentdb",
    "direction": "in",
    "databaseName": "MyDb",
    "collectionName": "MyCollection",
    "sqlQuery": "SELECT * from c where c.departmentId = {departmentId}",
    "connection": "CosmosDBConnection"
}
```

A [konfigurációs](#input---configuration) szakasz ismerteti ezeket a tulajdonságokat.

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

A következő példa egy [C# parancsfájlfüggvényt](functions-reference-csharp.md) mutat be, amely egyetlen dokumentumot olvas be. A függvényt egy olyan HTTP-kérés aktiválja, amely egy lekérdezési sztringgel adja meg a keresendő azonosítót. Ez az azonosító a `ToDoItem` megadott adatbázisból és gyűjteményből származó dokumentumok lekéréséhez használható.

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

Itt a C# script kód:

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

### <a name="http-trigger-look-up-id-from-route-data"></a>HTTP-eseményindító, azonosító kinézete az útvonaladatokból

A következő példa egy [C# parancsfájlfüggvényt](functions-reference-csharp.md) mutat be, amely egyetlen dokumentumot olvas be. A függvényt egy HTTP-kérelem váltja ki, amely útvonaladatokat használ a megkeresni kívánt azonosító megadásához. Ez az azonosító a `ToDoItem` megadott adatbázisból és gyűjteményből származó dokumentumok lekéréséhez használható.

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

Itt a C# script kód:

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

Itt a C# script kód:

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

Itt a C# script kód:

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

# <a name="javascript"></a>[Javascript](#tab/javascript)

Ez a szakasz a következő példákat tartalmazza:

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

A [konfigurációs](#input---configuration) szakasz ismerteti ezeket a tulajdonságokat.

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

A következő példa egy [JavaScript-függvényt](functions-reference-node.md) mutat be, amely egyetlen dokumentumot olvas be. A függvényt egy olyan HTTP-kérés aktiválja, amely egy lekérdezési sztringgel adja meg a keresendő azonosítót. Ez az azonosító a `ToDoItem` megadott adatbázisból és gyűjteményből származó dokumentumok lekéréséhez használható.

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

A következő példa egy [JavaScript-függvényt](functions-reference-node.md) mutat be, amely egyetlen dokumentumot olvas be. A függvényt egy olyan HTTP-kérés aktiválja, amely egy lekérdezési sztringgel adja meg a keresendő azonosítót. Ez az azonosító a `ToDoItem` megadott adatbázisból és gyűjteményből származó dokumentumok lekéréséhez használható.

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
    "type": "documentdb",
    "direction": "in",
    "databaseName": "MyDb",
    "collectionName": "MyCollection",
    "sqlQuery": "SELECT * from c where c.departmentId = {departmentId}",
    "connection": "CosmosDBConnection"
}
```

A [konfigurációs](#input---configuration) szakasz ismerteti ezeket a tulajdonságokat.

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

---

## <a name="input---attributes"></a>Bemenet - attribútumok

# <a name="c"></a>[C #](#tab/csharp)

A [C# osztálytárakban](functions-dotnet-class-library.md)használja a [DocumentDB](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/v2.x/src/WebJobs.Extensions.DocumentDB/DocumentDBAttribute.cs) attribútumot.

Az attribútum konstruktora felveszi az adatbázis nevét és a gyűjtemény nevét. A beállításokról és a konfigurálható egyéb tulajdonságokról [a következő konfigurációs szakaszban](#input---configuration)olvashat.

# <a name="c-script"></a>[C# parancsfájl](#tab/csharp-script)

Az attribútumokat a C# script nem támogatja.

# <a name="javascript"></a>[Javascript](#tab/javascript)

A JavaScript nem támogatja az attribútumokat.

---

## <a name="input---configuration"></a>Bemenet - konfiguráció

Az alábbi táblázat a *function.json* fájlban és az `DocumentDB` attribútumban beállított kötési konfigurációs tulajdonságokat ismerteti.

|function.json tulajdonság | Attribútum tulajdonság |Leírás|
|---------|---------|----------------------|
|**Típus**     | n/a | A beállításnak `documentdb`a beállítására kell beállítható.        |
|**direction**     | n/a | A beállításnak `in`a beállítására kell beállítható.         |
|**név**     | n/a | A függvényben lévő dokumentumot jelölő kötési paraméter neve.  |
|**adatbázisneve** |**DatabaseName** |A dokumentumot tartalmazó adatbázis.        |
|**Lekérdezés_neve** |**CollectionName** | A dokumentumot tartalmazó gyűjtemény neve. |
|**id**    | **Id** | A beolvasandó dokumentum azonosítója. Ez a tulajdonság támogatja a [kötési kifejezéseket.](./functions-bindings-expressions-patterns.md) Ne állítsa be mind az **id,** mind az **sqlQuery** tulajdonságokat. Ha egyiket sem állítja be, a rendszer a teljes gyűjteményt beolvassa. |
|**sqlQuery**  |**SqlQuery**  | Egy Azure Cosmos DB SQL-lekérdezés több dokumentum beolvasásához használt. A tulajdonság támogatja a futásidejű kötéseket, `SELECT * FROM c where c.departmentId = {departmentId}`mint például ebben a példában: . Ne állítsa be mind az **id,** mind az **sqlQuery** tulajdonságokat. Ha egyiket sem állítja be, a rendszer a teljes gyűjteményt beolvassa.|
|**Kapcsolat**     |**ConnectionStringSetting (Kapcsolatkarakterlánc-beállítás)**|Az Azure Cosmos DB kapcsolati karakterláncot tartalmazó alkalmazásbeállítás neve.        |
|**partíciókulcs**|**PartitionKey**|Megadja a válaszkulcs értékét. Tartalmazhat kötési paramétereket.|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="input---usage"></a>Bemenet - használat

# <a name="c"></a>[C #](#tab/csharp)

Amikor a függvény sikeresen kilép, a bemeneti dokumentum névvel ellátott bemeneti paramétereken keresztül végrehajtott módosításai automatikusan megmaradnak.

# <a name="c-script"></a>[C# parancsfájl](#tab/csharp-script)

Amikor a függvény sikeresen kilép, a bemeneti dokumentum névvel ellátott bemeneti paramétereken keresztül végrehajtott módosításai automatikusan megmaradnak.

# <a name="javascript"></a>[Javascript](#tab/javascript)

A frissítések nem lesznek automatikusan a funkció kilépésekor. Ehelyett `context.bindings.<documentName>In` használja, `context.bindings.<documentName>Out` és a frissítéseket. Lásd a [bemeneti példa](#input).

---

## <a name="output"></a>Kimenet

Az Azure Cosmos DB kimeneti kötés lehetővé teszi, hogy írjon egy új dokumentumot egy Azure Cosmos DB adatbázis az SQL API használatával.

# <a name="c"></a>[C #](#tab/csharp)

Ez a szakasz a következő példákat tartalmazza:

* Várólista-eseményindító, egy dokumentum írása
* Várólista-eseményindító, dokumentumok írása a használatával`IAsyncCollector`

A példák egy `ToDoItem` egyszerű típusra hivatkoznak:

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

### <a name="queue-trigger-write-one-doc"></a>Várólista-eseményindító, egy dokumentum írása

A következő példa egy [C# függvényt](functions-dotnet-class-library.md) mutat be, amely egy dokumentumot ad hozzá az adatbázishoz a Várólista-tárolóból származó üzenetben megadott adatok felhasználásával.

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

### <a name="queue-trigger-write-docs-using-iasynccollector"></a>Várólista-eseményindító, írási dokumentumok az IAsyncCollector használatával

A következő példa egy [C# függvényt](functions-dotnet-class-library.md) mutat be, amely dokumentumok gyűjteményét adja hozzá egy adatbázishoz a JSON üzenetben megadott adatok használatával.

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

# <a name="c-script"></a>[C# parancsfájl](#tab/csharp-script)

Ez a szakasz a következő példákat tartalmazza:

* Várólista-eseményindító, egy dokumentum írása
* Várólista-eseményindító, dokumentumok írása a használatával`IAsyncCollector`

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
    "type": "documentDB",
    "databaseName": "MyDatabase",
    "collectionName": "MyCollection",
    "createIfNotExists": true,
    "connection": "MyAccount_COSMOSDB",
    "direction": "out"
}
```

A [konfigurációs](#output---configuration) szakasz ismerteti ezeket a tulajdonságokat.

Itt a C# script kód:

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

### <a name="queue-trigger-write-docs-using-iasynccollector"></a>Várólista-eseményindító, írási dokumentumok az IAsyncCollector használatával

Több dokumentum létrehozásához kötésre `ICollector<T>` `IAsyncCollector<T>` lehet, vagy ahol `T` az egyik támogatott típus található.

Ez a példa egy `ToDoItem` egyszerű típusra vonatkozik:

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

Itt a function.json fájl:

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

Itt a C# script kód:

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
    "type": "documentDB",
    "databaseName": "MyDatabase",
    "collectionName": "MyCollection",
    "createIfNotExists": true,
    "connection": "MyAccount_COSMOSDB",
    "direction": "out"
}
```

A [konfigurációs](#output---configuration) szakasz ismerteti ezeket a tulajdonságokat.

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

---

## <a name="output---attributes"></a>Kimenet - attribútumok

# <a name="c"></a>[C #](#tab/csharp)

A [C# osztálytárakban](functions-dotnet-class-library.md)használja a [DocumentDB](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/v2.x/src/WebJobs.Extensions.DocumentDB/DocumentDBAttribute.cs) attribútumot.

Az attribútum konstruktora felveszi az adatbázis nevét és a gyűjtemény nevét. A beállításokkal és a konfigurálható egyéb tulajdonságokkal kapcsolatos további tudnivalókért olvassa el [a Kimenet - konfiguráció](#output---configuration)című témakört. Íme egy `DocumentDB` attribútum példa egy metódus aláírás:

```csharp
    [FunctionName("QueueToDocDB")]
    public static void Run(
        [QueueTrigger("myqueue-items", Connection = "AzureWebJobsStorage")] string myQueueItem,
        [DocumentDB("ToDoList", "Items", Id = "id", ConnectionStringSetting = "myCosmosDB")] out dynamic document)
    {
        ...
    }
```

A teljes példát lásd: [Kimenet](#output).

# <a name="c-script"></a>[C# parancsfájl](#tab/csharp-script)

Az attribútumokat a C# script nem támogatja.

# <a name="javascript"></a>[Javascript](#tab/javascript)

A JavaScript nem támogatja az attribútumokat.

---

## <a name="output---configuration"></a>Kimenet - konfiguráció

Az alábbi táblázat a *function.json* fájlban és az `DocumentDB` attribútumban beállított kötési konfigurációs tulajdonságokat ismerteti.

|function.json tulajdonság | Attribútum tulajdonság |Leírás|
|---------|---------|----------------------|
|**Típus**     | n/a | A beállításnak `documentdb`a beállítására kell beállítható.        |
|**direction**     | n/a | A beállításnak `out`a beállítására kell beállítható.         |
|**név**     | n/a | A függvényben lévő dokumentumot jelölő kötési paraméter neve.  |
|**adatbázisneve** | **DatabaseName**|A dokumentumot létrehozó gyűjteményt tartalmazó adatbázis.     |
|**Lekérdezés_neve** |**CollectionName**  | Annak a gyűjteménynek a neve, amelyben a dokumentumot létrehozták. |
|**createIfNotExists**  |**CreateIfNotExists**    | Logikai érték, amely azt jelzi, hogy a gyűjtemény akkor jön-e létre, ha nem létezik. Az alapértelmezett *hamis,* mert az új gyűjtemények jönnek létre a fenntartott átviteli, amelynek költségvonzatai vannak. További tájékoztatás a [díjszabási lapon](https://azure.microsoft.com/pricing/details/documentdb/) olvasható.  |
|**partíciókulcs**|**PartitionKey** |Ha `CreateIfNotExists` igaz, meghatározza a létrehozott gyűjtemény partíciókulcs-elérési útját.|
|**kollekcióThroughput**|**CollectionThroughput (Gyűjteményátmenő)**| Ha `CreateIfNotExists` igaz, meghatározza a létrehozott gyűjtemény [átviteli.](../cosmos-db/set-throughput.md)|
|**Kapcsolat**    |**ConnectionStringSetting (Kapcsolatkarakterlánc-beállítás)** |Az Azure Cosmos DB kapcsolati karakterláncot tartalmazó alkalmazásbeállítás neve.        |

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="output---usage"></a>Kimenet - használat

Alapértelmezés szerint, amikor a függvény kimeneti paraméterére ír, egy dokumentum jön létre az adatbázisban. Ez a dokumentum automatikusan létrehozott GUID azonosítóval rendelkezik. A kimeneti dokumentum dokumentumazonosítóját a kimeneti `id` paraméternek átadott JSON-objektum tulajdonságának megadásával adhatja meg.

> [!Note]
> Amikor megadja egy meglévő dokumentum azonosítóját, az új kimeneti dokumentum felülírja azt.

## <a name="exceptions-and-return-codes"></a>Kivételek és visszaküldési kódok

| Kötés | Referencia |
|---|---|
| CosmosDB | [CosmosDB hibakódok](https://docs.microsoft.com/rest/api/cosmos-db/http-status-codes-for-cosmosdb) |

## <a name="next-steps"></a>További lépések

* [További információ a kiszolgáló nélküli adatbázis-használatról a Cosmos DB segítségével](../cosmos-db/serverless-computing-database.md)
* [További információ az Azure-függvények aktiválásáról és kötéseiről](functions-triggers-bindings.md)

<!---
> [!div class="nextstepaction"]
> [Go to a quickstart that uses a Cosmos DB trigger](functions-create-cosmos-db-triggered-function.md)
--->
