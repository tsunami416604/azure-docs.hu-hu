---
title: Az Azure Cosmos DB kötéseket a következő funkciók 2.x (előzetes verzió)
description: Az Azure Functions Azure Cosmos DB eseményindítók és kötések használatának megismerése.
services: functions
documentationcenter: na
author: tdykstra
manager: cfowler
editor: ''
tags: ''
keywords: Azure functions, Funkciók, Eseményfeldolgozási, dinamikus számítási kiszolgáló nélküli architektúrája
ms.service: functions; cosmos-db
ms.devlang: multiple
ms.topic: reference
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 11/21/2017
ms.author: tdykstra
ms.openlocfilehash: e40ba6bcfa1b6247f62849626d4a7803a76362a1
ms.sourcegitcommit: 4e36ef0edff463c1edc51bce7832e75760248f82
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/08/2018
ms.locfileid: "35234869"
---
# <a name="azure-cosmos-db-bindings-for-azure-functions-2x-preview"></a>Az Azure Functions kötései Azure Cosmos DB 2.x (előzetes verzió)

> [!div class="op_single_selector" title1="Select the version of the Azure Functions runtime you are using: "]
> * [1. verzió – Általánosan elérhető](functions-bindings-cosmosdb.md)
> * [2. verzió – Előzetes verzió](functions-bindings-cosmosdb-v2.md)

Ez a cikk azt ismerteti, hogyan használható [Azure Cosmos DB](..\cosmos-db\serverless-computing-database.md) Azure Functions kötések 2.x. Az Azure Functions támogatja indítás, bemeneti és kimeneti Azure Cosmos DB kötései.

> [!NOTE]
> Ez a cikk [Azure Functions verzió 2.x](functions-versions.md), amely jelenleg előzetes verzióban érhető.  Ezek a kötések függvény használatával kapcsolatos információt 1.x, lásd: [Azure Functions Azure Cosmos DB kötései 1.x](functions-bindings-cosmosdb.md).
>
> A kötés a DocumentDB eredetileg nevet kapta. Funkciók verziójában 2.x, az eseményindító, a kötések és a csomag összes megnevezett Cosmos DB.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="packages---functions-2x"></a>Csomagok - 2.x működik

Az Azure Cosmos DB kötések funkciók verziójához 2.x szerepelnek a [Microsoft.Azure.WebJobs.Extensions.CosmosDB](http://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.CosmosDB) NuGet-csomagot, verziója 3.x. A kötések forráskódja van a [azure-webjobs-sdk-bővítmények](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.CosmosDB/) GitHub-tárházban.

[!INCLUDE [functions-package-v2](../../includes/functions-package-v2.md)]

## <a name="trigger"></a>Eseményindító

Az Azure Cosmos DB eseményindítót használ a [Azure Cosmos DB módosítás hírcsatorna](../cosmos-db/change-feed.md) beszúrása a figyelésére és partíciók önálló frissítések. A módosítás hírcsatorna beszúrások és frissítések, nem törlések tesz közzé.

## <a name="trigger---example"></a>Eseményindító – példa

Tekintse meg a nyelvspecifikus példát:

* [C#](#trigger---c-example)
* [C# script (.csx)](#trigger---c-script-example)
* [JavaScript](#trigger---javascript-example)

[Kihagyás eseményindító példák](#trigger---attributes)

### <a name="trigger---c-example"></a>Eseményindító - C# – példa

Az alábbi példa mutatja egy [C# függvény](functions-dotnet-class-library.md) , amely nyílik meg, ha nincsenek beszúrása vagy a megadott adatbázis és gyűjtemény frissíti.

```cs
using Microsoft.Azure.Documents;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Host;
using System.Collections.Generic;

namespace CosmosDBSamplesV2
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

[Kihagyás eseményindító példák](#trigger---attributes)

### <a name="trigger---c-script-example"></a>Eseményindító - C# parancsfájl – példa

A következő példa bemutatja egy Cosmos DB eseményindító kötelező egy *function.json* fájlt és egy [C# parancsfájl függvény](functions-reference-csharp.md) , amely a kötés használja. A függvény naplóüzenetek ír, amikor a Cosmos DB rekordok módosítva lett.

Itt az kötés adatai a *function.json* fájlt:

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

A C# parancsfájl kód itt látható:
 
```cs 
    #r "Microsoft.Azure.Documents.Client"
    
    using System;
    using Microsoft.Azure.Documents;
    using System.Collections.Generic;
    

    public static void Run(IReadOnlyList<Document> documents, TraceWriter log)
    {
      log.Verbose("Documents modified " + documents.Count);
      log.Verbose("First document Id " + documents[0].Id);
    }
```

[Kihagyás eseményindító példák](#trigger---attributes)

### <a name="trigger---javascript-example"></a>Eseményindító - JavaScript – példa

A következő példa bemutatja egy Cosmos DB eseményindító kötelező egy *function.json* fájlt és egy [JavaScript függvény](functions-reference-node.md) , amely a kötés használja. A függvény naplóüzenetek ír, amikor a Cosmos DB rekordok módosítva lett.

Itt az kötés adatai a *function.json* fájlt:

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

A JavaScript-kód itt látható:

```javascript
    module.exports = function (context, documents) {
      context.log('First document Id modified : ', documents[0].id);

      context.done();
    }
```

## <a name="trigger---attributes"></a>Eseményindító - attribútumok

A [C# osztálykönyvtárakhoz](functions-dotnet-class-library.md), használja a [CosmosDBTrigger](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.CosmosDB/Trigger/CosmosDBTriggerAttribute.cs) attribútum.

Az attribútum konstruktora időt vesz igénybe, az adatbázis és gyűjtemény nevét. Ezeket a beállításokat és egyéb tulajdonságok konfigurálható kapcsolatos információkért lásd: [eseményindító - konfigurációs](#trigger---configuration). Íme egy `CosmosDBTrigger` metódus-aláírás attribútum példát:

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

Tekintse meg a teljes például [eseményindító - C# példa](#trigger---c-example).

## <a name="trigger---configuration"></a>Eseményindító - konfiguráció

Az alábbi táblázat ismerteti a beállított kötés konfigurációs tulajdonságok a *function.json* fájl és a `CosmosDBTrigger` attribútum.

|Function.JSON tulajdonság | Attribútum tulajdonsága |Leírás|
|---------|---------|----------------------|
|**type** || meg kell `cosmosDBTrigger`. |
|**direction** || meg kell `in`. Ez a paraméter rendszer automatikusan beállítja az eseményindítót hoz létre az Azure portálon. |
|**name** || A dokumentumok a változások listájának jelölő függvény kódban használt változó neve. | 
|**connectionStringSetting**|**ConnectionStringSetting** | A figyelt Azure Cosmos DB fiók való kapcsolódáshoz használt kapcsolati karakterlánc tartalmazó alkalmazásbeállítás neve. |
|**DatabaseName**|**DatabaseName**  | A figyelt gyűjtemény Azure Cosmos DB adatbázis neve. |
|**CollectionName** |**CollectionName** | A figyelt gyűjtemény nevét. |
|**leaseConnectionStringSetting** | **LeaseConnectionStringSetting** | (Választható) A kapcsolati karakterláncot a szolgáltatás, amely tartalmazza a címbérlet gyűjteményt tartalmazó alkalmazásbeállítás neve. Ha nincs megadva, a `connectionStringSetting` értéket használja. Ez a paraméter értéke a kötés a portálon létrehozásakor automatikusan. A kapcsolati karakterláncot a bérletek gyűjtemény írási engedéllyel kell rendelkeznie.|
|**leaseDatabaseName** |**LeaseDatabaseName** | (Választható) Az adatbázis, amely tárolja a bérletek tároló gyűjtemény nevét. Ha nincs megadva, az értékét a `databaseName` beállítást használja. Ez a paraméter értéke a kötés a portálon létrehozásakor automatikusan. |
|**LeaseCollectionName** | **LeaseCollectionName** | (Választható) A bérletek tároló gyűjtemény nevét. Ha nincs megadva, az érték `leases` szolgál. |
|**CreateLeaseCollectionIfNotExists** | **CreateLeaseCollectionIfNotExists** | (Választható) Ha beállítása `true`, a bérletek gyűjtemény automatikusan jön létre, ha még nem létezik. Az alapértelmezett érték `false`. |
|**LeasesCollectionThroughput**| **LeasesCollectionThroughput**| (Választható) Rendel a bérletek gyűjtemény létrehozásakor kérjen egységek mennyisége határozza meg. Ez a beállítás csak akkor használhatók, ha a amikor nem `createLeaseCollectionIfNotExists` értéke `true`. Ez a paraméter értéke a kötés létrehozása a portál használatával automatikusan.
|**LeaseCollectionPrefix**| **LeaseCollectionPrefix**| (Választható) Ha a beállítás, hozzáadása előtag a bérletek ennél a függvénynél, két külön Azure Functions ugyanaz a címbérlet gyűjtemény megosztása különböző előtagok használatával hatékonyan így bérleti gyűjtemény létrehozása.
|**FeedPollDelay**| **FeedPollDelay**| (Választható) Amikor az összes módosítások vannak merül le, ha be van állítva, meghatározza, ezredmásodpercben megadva, a késés Between lekérdezési partíciója új módosítások az adatcsatorna számára. Alapértelmezés szerint 5000 (5 másodperc).
|**LeaseAcquireInterval**| **LeaseAcquireInterval**| (Választható) Érték beállítása esetén meghatározza, ezredmásodpercben, ha partíció van elosztva között ismert állomás-példányok számítási feladatot indítsa intervallum. Alapértelmezés szerint 13000 (13 másodperc).
|**LeaseExpirationInterval**| **LeaseExpirationInterval**| (Választható) Érték beállítása esetén meghatározza, ezredmásodpercben megadva, az időköz, amelynek a címbérlet születik partíció képviselő címbérletét. A bérlet intervallum nem hosszabbítja meg, ha annak történő lejártát okoz, és a partíció tulajdonjogát áthelyezi egy másik példánya. Alapértelmezés szerint 60000 (60 másodperc).
|**LeaseRenewInterval**| **LeaseRenewInterval**| (Választható) Ha a beállítás, meghatározza, ezredmásodpercben, partíciók példánya által jelenleg tárolt összes címbérlet megújítási időköz. Alapértelmezés szerint 17000 (17 másodperc).
|**CheckpointFrequency**| **CheckpointFrequency**| (Választható) Érték beállítása esetén meghatározza, ezredmásodpercben megadva, a címbérlet ellenőrzőpontokat közötti távolság. Alapértelmezés szerint mindig sikeres függvény hívása után.
|**MaxItemsPerInvocation**| **MaxItemsPerInvocation**| (Választható) Érték beállítása esetén az testreszabja függvény hívásához szükséges egy fogadott elemek maximális száma.

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="trigger---usage"></a>Eseményindító - használat

Az eseményindító szükséges egy második gyűjtemény, amelyet akkor használ _bérleteket_ a partíciók keresztül. A figyelt gyűjteményhez, mind a bérletek tartalmazó gyűjteményen működéséhez az eseményindító elérhetőnek kell lennie.

>[!IMPORTANT]
> Ha több funkciók ugyanahhoz a gyűjteményhez Cosmos DB eseményindító használatára van konfigurálva, funkciók mindegyike kell dedikált bérleti gyűjtemény használatára, vagy adjon meg másik `LeaseCollectionPrefix` az egyes funkciók. Ellenkező esetben a függvény csak az egyik indul. Az előtag kapcsolatos információkért tekintse meg a [konfigurációs szakasz](#trigger---configuration).

Az eseményindító nem jelzi egy dokumentumot lett frissítve, vagy szúrja be, azt csak itt maga a dokumentum. Ha a frissítések és beszúrások másképp van szüksége, sikerült szeretne Timestamp típusú mezőket a Beszúrás vagy frissítés alkalmazásával.

## <a name="input"></a>Input (Bemenet)

Az Azure Cosmos DB bemeneti kötése kéri le egy vagy több Azure Cosmos DB dokumentumot, és továbbadja őket a függvény a bemeneti paraméter. A dokumentum azonosító vagy lekérdezési paramétereket az eseményindító, amely hívja meg a függvény alapján lehet meghatározni. 

>[!NOTE]
> Nem használja a megadott Azure Cosmos-adatbázis, vagy kimeneti kötések használata MongoDB API egy Cosmos DB fiókon. Adatsérülés lehetőség.

## <a name="input---examples"></a>Bemenet – példa

Példák a nyelvspecifikus, olvassa el egy dokumentumot egy azonosító érték megadásával:

* [C#](#input---c-examples)
* [C# script (.csx)](#input---c-script-examples)
* [JavaScript](#input---javascript-examples)
* [F#](#input---f-examples)

[Bemeneti példák kihagyása](#input---attributes)

### <a name="input---c-examples"></a>Bemenet – C# példák

Ez a szakasz az alábbi példákat:

* [Várólista eseményindító, keresse meg a JSON formátumból azonosítója](#queue-trigger-look-up-id-from-json-c)
* [HTTP-eseményindítóval, azonosító keresse meg a lekérdezési karakterlánc](#http-trigger-look-up-id-from-query-string-c)
* [HTTP-eseményindítóval, azonosító keresse meg a útvonal adatokból](#http-trigger-look-up-id-from-route-data-c)
* [HTTP-eseményindítóval, azonosító keresse meg a útvonal adatokból SqlQuery használatával](#http-trigger-look-up-id-from-route-data-using-sqlquery-c)
* [HTTP indítható el, majd a több docs SqlQuery használatával](#http-trigger-get-multiple-docs-using-sqlquery-c)
* [HTTP indítható el, majd a több docs DocumentClient használatával](#http-trigger-get-multiple-docs-using-documentclient-c)

A példák hivatkozik egy egyszerű `ToDoItem` típusa:

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

[Bemeneti példák kihagyása](#input---attributes)

#### <a name="queue-trigger-look-up-id-from-json-c"></a>Várólista eseményindító, keresse meg a JSON (C#) azonosítója

Az alábbi példa mutatja egy [C# függvény](functions-dotnet-class-library.md) , amely egyetlen dokumentum kéri le. A függvény egy üzenetsor-üzenetet, amely tartalmaz egy JSON-objektum váltja ki. A várólista eseményindító elemzi a JSON nevű objektum a `ToDoItemLookup`, megkeresheti az Azonosítót tartalmazó. Hogy azonosító beolvasásához használt egy `ToDoItem` dokumentumot a megadott adatbázis és gyűjtemény.

```cs
namespace CosmosDBSamplesV2
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

[Bemeneti példák kihagyása](#input---attributes)

#### <a name="http-trigger-look-up-id-from-query-string-c"></a>HTTP-eseményindítóval, azonosító keresse meg a lekérdezési karakterlánc (C#)

Az alábbi példa mutatja egy [C# függvény](functions-dotnet-class-library.md) , amely egyetlen dokumentum kéri le. A függvény egy HTTP-kérelem adja meg az Azonosítót kereshet meg egy lekérdezési karakterláncot használó váltja ki. Hogy azonosító beolvasásához használt egy `ToDoItem` dokumentumot a megadott adatbázis és gyűjtemény.

```cs
using Microsoft.AspNetCore.Http;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.Http;
using Microsoft.Azure.WebJobs.Host;

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
            return new OkResult();
        }
    }
}
```

[Bemeneti példák kihagyása](#input---attributes)

#### <a name="http-trigger-look-up-id-from-route-data-c"></a>HTTP-eseményindítóval, azonosító keresse meg a útvonal adatokból (C#)

Az alábbi példa mutatja egy [C# függvény](functions-dotnet-class-library.md) , amely egyetlen dokumentum kéri le. A függvény, hogy használ irányításához adatokat adja meg az Azonosítót megkeresheti a HTTP-kérelem váltja ki. Hogy azonosító beolvasásához használt egy `ToDoItem` dokumentumot a megadott adatbázis és gyűjtemény.

```cs
using Microsoft.AspNetCore.Http;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.Http;
using Microsoft.Azure.WebJobs.Host;

namespace CosmosDBSamplesV2
{
    public static class DocByIdFromRouteData
    {
        [FunctionName("DocByIdFromRouteData")]
        public static IActionResult Run(
            [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post", 
                Route = "todoitems/{id}")]HttpRequest req,
            [CosmosDB(
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
            return new OkResult();
        }
    }
}
```

[Bemeneti példák kihagyása](#input---attributes)

#### <a name="http-trigger-look-up-id-from-route-data-using-sqlquery-c"></a>HTTP-eseményindítóval, azonosító keresse meg a útvonal adatokból SqlQuery (C#) használatával

Az alábbi példa mutatja egy [C# függvény](functions-dotnet-class-library.md) , amely egyetlen dokumentum kéri le. A függvény, hogy használ irányításához adatokat adja meg az Azonosítót megkeresheti a HTTP-kérelem váltja ki. Hogy azonosító beolvasásához használt egy `ToDoItem` dokumentumot a megadott adatbázis és gyűjtemény. 

A példa bemutatja, hogyan használható a kötési kifejezés a `SqlQuery` paraméter. Az útvonal adatai átadhatók a `SqlQuery` paraméter látható, de jelenleg [lekérdezési karakterlánc-érték nem adható át](https://github.com/Azure/azure-functions-host/issues/2554#issuecomment-392084583).


```cs
using Microsoft.AspNetCore.Http;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.Http;
using Microsoft.Azure.WebJobs.Host;
using System.Collections.Generic;

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
            TraceWriter log)
        {
            log.Info("C# HTTP trigger function processed a request.");

            foreach (ToDoItem toDoItem in toDoItems)
            {
                log.Info(toDoItem.Description);
            }
            return new OkResult();
        }
    }
}
```

[Bemeneti példák kihagyása](#input---attributes)

#### <a name="http-trigger-get-multiple-docs-using-sqlquery-c"></a>HTTP indítható el, majd a több docs SqlQuery (C#) használatával

Az alábbi példa mutatja egy [C# függvény](functions-dotnet-class-library.md) , amely kéri le a dokumentumot. A függvény egy HTTP-kérelem váltja ki. A lekérdezés van megadva a `SqlQuery` tulajdonság attribútum.

```cs
using Microsoft.AspNetCore.Http;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.Http;
using Microsoft.Azure.WebJobs.Host;
using System.Collections.Generic;

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
            TraceWriter log)
        {
            log.Info("C# HTTP trigger function processed a request.");
            foreach (ToDoItem toDoItem in toDoItems)
            {
                log.Info(toDoItem.Description);
            }
            return new OkResult();
        }
    }
}

```

[Bemeneti példák kihagyása](#input---attributes)

#### <a name="http-trigger-get-multiple-docs-using-documentclient-c"></a>HTTP indítható el, majd a több docs DocumentClient (C#) használatával

Az alábbi példa mutatja egy [C# függvény](functions-dotnet-class-library.md) , amely kéri le a dokumentumot. A függvény egy HTTP-kérelem váltja ki. A kód egy `DocumentClient` biztosítja a dokumentumok listája olvasható Azure Cosmos DB kötés példány. A `DocumentClient` példány is használható az írási műveletek.

```cs
using Microsoft.AspNetCore.Http;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Azure.Documents.Client;
using Microsoft.Azure.Documents.Linq;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.Http;
using Microsoft.Azure.WebJobs.Host;
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
            TraceWriter log)
        {
            log.Info("C# HTTP trigger function processed a request.");

            var searchterm = req.Query["searchterm"];
            if (string.IsNullOrWhiteSpace(searchterm))
            {
                return (ActionResult)new NotFoundResult();
            }

            Uri collectionUri = UriFactory.CreateDocumentCollectionUri("ToDoItems", "Items");

            log.Info($"Searching for: {searchterm}");

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
            return new OkResult();
        }
    }
}
```

[Bemeneti példák kihagyása](#input---attributes)

### <a name="input---c-script-examples"></a>Bemenet – C# parancsfájl példák

Ez a szakasz a következő példák, amelyek egyetlen dokumentum olvasása különböző forrásokból egy azonosító érték megadásával:

* Várólista eseményindító, azonosító keresse meg a várólista-üzenetének
* Várólista eseményindító, azonosító keresse meg a várólista-üzenetből, SqlQuery használatával

[Bemeneti példák kihagyása](#input---attributes)

#### <a name="queue-trigger-look-up-id-from-queue-message-c-script"></a>Várólista eseményindító, azonosító keresse meg a várólista-üzenetének (C# parancsfájl)

A következő példa bemutatja egy Cosmos DB bemeneti kötése a egy *function.json* fájlt és egy [C# parancsfájl függvény](functions-reference-csharp.md) , amely a kötés használja. A függvény egyetlen dokumentum olvas, és frissíti a dokumentum szöveges értéket.

Itt az kötés adatai a *function.json* fájlt:

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
A [konfigurációs](#input---configuration) a szakasz ismerteti ezeket a tulajdonságokat.

A C# parancsfájl kód itt látható:

```cs
    using System;

    // Change input document contents using Azure Cosmos DB input binding 
    public static void Run(string myQueueItem, dynamic inputDocument)
    {   
      inputDocument.text = "This has changed.";
    }
```

[Bemeneti példák kihagyása](#input---attributes)

#### <a name="queue-trigger-look-up-id-queue-message-using-sqlquery-c-script"></a>Várólista eseményindító, keresse meg a várólista üzenetet, SqlQuery (C# parancsfájl) használatával

A következő példa bemutatja egy Azure Cosmos DB bemeneti kötése a egy *function.json* fájlt és egy [C# parancsfájl függvény](functions-reference-csharp.md) , amely a kötés használja. A függvény egy SQL-lekérdezést, várólista eseményindító segítségével testre szabhatja a lekérdezési paraméter által megadott több dokumentumok kéri le.

A várólista eseményindító biztosít egy paraméter `departmentId`. A várólista üzenet `{ "departmentId" : "Finance" }` meghaladná a pénzügyi részleg összes rekordja. 

Itt az kötés adatai a *function.json* fájlt:

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

A [konfigurációs](#input---configuration) a szakasz ismerteti ezeket a tulajdonságokat.

A C# parancsfájl kód itt látható:

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

[Bemeneti példák kihagyása](#input---attributes)

### <a name="input---javascript-examples"></a>Bemenet – JavaScript példák

Ez a szakasz a következő példák, amelyek egyetlen dokumentum olvasása különböző forrásokból egy azonosító érték megadásával:

* Várólista eseményindító, azonosító keresse meg a várólista-üzenetének
* Várólista eseményindító, azonosító keresse meg a várólista-üzenetből, SqlQuery használatával

[Bemeneti példák kihagyása](#input---attributes)

#### <a name="queue-trigger-look-up-id-from-queue-message-javascript"></a>Várólista eseményindító, azonosító keresse meg a várólista-üzenetének (JavaScript)

A következő példa bemutatja egy Cosmos DB bemeneti kötése a egy *function.json* fájlt és egy [JavaScript függvény](functions-reference-node.md) , amely a kötés használja. A függvény egyetlen dokumentum olvas, és frissíti a dokumentum szöveges értéket.

Itt az kötés adatai a *function.json* fájlt:

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
A [konfigurációs](#input---configuration) a szakasz ismerteti ezeket a tulajdonságokat.

A JavaScript-kód itt látható:

```javascript
    // Change input document contents using Azure Cosmos DB input binding, using context.bindings.inputDocumentOut
    module.exports = function (context) {   
    context.bindings.inputDocumentOut = context.bindings.inputDocumentIn;
    context.bindings.inputDocumentOut.text = "This was updated!";
    context.done();
    };
```

[Bemeneti példák kihagyása](#input---attributes)

#### <a name="queue-trigger-look-up-id-from-queue-message-using-sqlquery-javascript"></a>Várólista eseményindító, azonosító keresse meg a várólista üzenetének használatával SqlQuery (JavaScript)

A következő példa bemutatja egy Azure Cosmos DB bemeneti kötése a egy *function.json* fájlt és egy [JavaScript függvény](functions-reference-node.md) , amely a kötés használja. A függvény egy SQL-lekérdezést, várólista eseményindító segítségével testre szabhatja a lekérdezési paraméter által megadott több dokumentumok kéri le.

A várólista eseményindító biztosít egy paraméter `departmentId`. A várólista üzenet `{ "departmentId" : "Finance" }` meghaladná a pénzügyi részleg összes rekordja. 

Itt az kötés adatai a *function.json* fájlt:

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

A [konfigurációs](#input---configuration) a szakasz ismerteti ezeket a tulajdonságokat.

A JavaScript-kód itt látható:

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

[Bemeneti példák kihagyása](#input---attributes)

<a name="infsharp"></a>

### <a name="input---f-examples"></a>Bemenet – F # példák

A következő példa bemutatja egy Cosmos DB bemeneti kötése a egy *function.json* fájlt és egy [F # függvény](functions-reference-fsharp.md) , amely a kötés használja. A függvény egyetlen dokumentum olvas, és frissíti a dokumentum szöveges értéket.

Itt az kötés adatai a *function.json* fájlt:

```json
{
    "name": "inputDocument",
    "type": "cosmosDB",
    "databaseName": "MyDatabase",
    "collectionName": "MyCollection",
    "id" : "{queueTrigger}",
    "connectionStringSetting": "MyAccount_COSMOSDB",     
    "direction": "in"
}
```

A [konfigurációs](#input---configuration) a szakasz ismerteti ezeket a tulajdonságokat.

A F # kód itt látható:

```fsharp
    (* Change input document contents using Azure Cosmos DB input binding *)
    open FSharp.Interop.Dynamic
    let Run(myQueueItem: string, inputDocument: obj) =
    inputDocument?text <- "This has changed."
```

Ez a példa szükséges egy `project.json` fájl, amely meghatározza a `FSharp.Interop.Dynamic` és `Dynamitey` NuGet függőségek:

```json
{
    "frameworks": {
        "net46": {
            "dependencies": {
                "Dynamitey": "1.0.2",
                "FSharp.Interop.Dynamic": "3.0.0"
            }
        }
    }
}
```

Hozzáadása egy `project.json` fájl című [F # felügyeleti csomag](functions-reference-fsharp.md#package).

## <a name="input---attributes"></a>Bemenet – attribútumok

A [C# osztálykönyvtárakhoz](functions-dotnet-class-library.md), használja a [CosmosDB](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.CosmosDB/CosmosDBAttribute.cs) attribútum.

Az attribútum konstruktora időt vesz igénybe, az adatbázis és gyűjtemény nevét. Ezeket a beállításokat és egyéb tulajdonságok konfigurálható kapcsolatos információkért lásd: [a következő konfigurációs szakasz](#input---configuration). 

## <a name="input---configuration"></a>Adjon meg - konfiguráció

Az alábbi táblázat ismerteti a beállított kötés konfigurációs tulajdonságok a *function.json* fájl és a `CosmosDB` attribútum.

|Function.JSON tulajdonság | Attribútum tulajdonsága |Leírás|
|---------|---------|----------------------|
|**type**     || meg kell `cosmosDB`.        |
|**direction**     || meg kell `in`.         |
|**name**     || A kötési paraméter, a függvény a dokumentum jelölő neve.  |
|**DatabaseName** |**DatabaseName** |A a dokumentum tartalmazó adatbázis.        |
|**CollectionName** |**CollectionName** | A gyűjtemény, amely tartalmazza a dokumentum nevét. |
|**id**    | **Azonosító** | A dokumentum beolvasása azonosítója. Ez a tulajdonság támogatja [kötési kifejezésként](functions-triggers-bindings.md#binding-expressions-and-patterns). Ne állítsa be mind a **azonosító** és **sqlQuery** tulajdonságait. Ha nem állít egy, a rendszer lekéri a teljes gyűjteményt. |
|**sqlQuery**  |**SqlQuery**  | Egy Azure Cosmos adatbázis SQL-lekérdezésben használt több dokumentumot. A tulajdonság támogatja a futásidejű kötések, például: `SELECT * FROM c where c.departmentId = {departmentId}`. Ne állítsa be mind a **azonosító** és **sqlQuery** tulajdonságait. Ha nem állít egy, a rendszer lekéri a teljes gyűjteményt.|
|**connectionStringSetting**     |**ConnectionStringSetting**|Az Alkalmazásbeállítás, amely tartalmazza az Azure Cosmos DB kapcsolati karakterlánc nevét.        |
|**partitionKey**|**PartitionKey**|Meghatározza a partíció a keresési kulcs értékét. Előfordulhat, hogy tartalmazzák a kötési paraméterek esetében.|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="input---usage"></a>Bemenet - használat

A C# és F # függvényekben amikor sikeresen megtörtént, kilép, a függvény a bemeneti dokumentum elnevezett bemeneti paraméterek keresztül végzett módosítások automatikusan megmaradnak. 

A JavaScript-funkcióként frissítések nem automatikusan történik függvény kilépés után. Ehelyett használjon `context.bindings.<documentName>In` és `context.bindings.<documentName>Out` a frissítések. Tekintse meg a [JavaScript példa](#input---javascript-example).

## <a name="output"></a>Kimenet

A kötés segítségével Azure Cosmos DB kimeneti írni egy új dokumentumot Azure Cosmos DB-adatbázishoz. 

>[!NOTE]
> Nem használja a megadott Azure Cosmos-adatbázis, vagy kimeneti kötések használata MongoDB API egy Cosmos DB fiókon. Adatsérülés lehetőség.

## <a name="output---example"></a>Kimeneti – példa

Tekintse meg a nyelvspecifikus példát:

* [C#](#output---c-examples)
* [C# script (.csx)](#output---c-script-examples)
* [JavaScript](#output---javascript-examples)
* [F#](#output---f-examples)

További információ a [bemeneti példa](#input---c-examples) használó `DocumentClient`.

[Kimeneti példák kihagyása](#output---attributes)

### <a name="ouput---c-examples"></a>Kimeneti - C# példák

Ez a szakasz az alábbi példákat:

* Várólista eseményindító, egy dokumentum írása
* Várólista eseményindító, írási docs IAsyncCollector használatával

A példák hivatkozik egy egyszerű `ToDoItem` típusa:

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

[Kimeneti példák kihagyása](#output---attributes)

#### <a name="queue-trigger-write-one-doc-c"></a>Várólista eseményindító, egy doc írási (C#)

Az alábbi példa mutatja egy [C# függvény](functions-dotnet-class-library.md) , amely ad hozzá egy adatbázist, a Queue storage az üzenetben megadott adatok.

```cs
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Host;
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
            TraceWriter log)
        {
            document = new { Description = queueMessage, id = Guid.NewGuid() };

            log.Info($"C# Queue trigger function inserted one row");
            log.Info($"Description={queueMessage}");
        }
    }
}
```

[Kimeneti példák kihagyása](#output---attributes)

#### <a name="queue-trigger-write-docs-using-iasynccollector-c"></a>Várólista eseményindító, írási docs IAsyncCollector (C#) használatával

Az alábbi példa mutatja egy [C# függvény](functions-dotnet-class-library.md) dokumentumok gyűjteménye, amely hozzáadja egy adatbázist egy üzenetsor JSON megadott adatok.

```cs
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Host;
using System.Threading.Tasks;

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

[Kimeneti példák kihagyása](#output---attributes)

### <a name="output---c-script-examples"></a>Kimeneti - C# parancsfájl példák

A következő példa bemutatja egy Azure Cosmos DB kimeneti kötelező egy *function.json* fájlt és egy [C# parancsfájl függvény](functions-reference-csharp.md) , amely a kötés használja. A függvény egy bemeneti várólista-kötést használja a várólistához, amely megkapja a JSON a következő formátumban:

```json
{
    "name": "John Henry",
    "employeeId": "123456",
    "address": "A town nearby"
}
```

A funkció a következő formátumban, az egyes rekordokhoz hoz létre Azure Cosmos DB dokumentumokat:

```json
{
    "id": "John Henry-123456",
    "name": "John Henry",
    "employeeId": "123456",
    "address": "A town nearby"
}
```

Itt az kötés adatai a *function.json* fájlt:

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

A [konfigurációs](#output---configuration) a szakasz ismerteti ezeket a tulajdonságokat.

A C# parancsfájl kód itt látható:

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

Több dokumentumok létrehozásához köthető `ICollector<T>` vagy `IAsyncCollector<T>` ahol `T` a támogatott típusok egyike.

[Kimeneti példák kihagyása](#output---attributes)

### <a name="output---javascript-examples"></a>Kimeneti - JavaScript példák

A következő példa bemutatja egy Azure Cosmos DB kimeneti kötelező egy *function.json* fájlt és egy [JavaScript függvény](functions-reference-node.md) , amely a kötés használja. A függvény egy bemeneti várólista-kötést használja a várólistához, amely megkapja a JSON a következő formátumban:

```json
{
    "name": "John Henry",
    "employeeId": "123456",
    "address": "A town nearby"
}
```

A funkció a következő formátumban, az egyes rekordokhoz hoz létre Azure Cosmos DB dokumentumokat:

```json
{
    "id": "John Henry-123456",
    "name": "John Henry",
    "employeeId": "123456",
    "address": "A town nearby"
}
```

Itt az kötés adatai a *function.json* fájlt:

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

A [konfigurációs](#output---configuration) a szakasz ismerteti ezeket a tulajdonságokat.

A JavaScript-kód itt látható:

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

[Kimeneti példák kihagyása](#output---attributes)

### <a name="output---f-examples"></a>Kimeneti - F # példák

A következő példa bemutatja egy Azure Cosmos DB kimeneti kötelező egy *function.json* fájlt és egy [F # függvény](functions-reference-fsharp.md) , amely a kötés használja. A függvény egy bemeneti várólista-kötést használja a várólistához, amely megkapja a JSON a következő formátumban:

```json
{
    "name": "John Henry",
    "employeeId": "123456",
    "address": "A town nearby"
}
```

A funkció a következő formátumban, az egyes rekordokhoz hoz létre Azure Cosmos DB dokumentumokat:

```json
{
    "id": "John Henry-123456",
    "name": "John Henry",
    "employeeId": "123456",
    "address": "A town nearby"
}
```

Itt az kötés adatai a *function.json* fájlt:

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
A [konfigurációs](#output---configuration) a szakasz ismerteti ezeket a tulajdonságokat.

A F # kód itt látható:

```fsharp
    open FSharp.Interop.Dynamic
    open Newtonsoft.Json

    type Employee = {
      id: string
      name: string
      employeeId: string
      address: string
    }

    let Run(myQueueItem: string, employeeDocument: byref<obj>, log: TraceWriter) =
      log.Info(sprintf "F# Queue trigger function processed: %s" myQueueItem)
      let employee = JObject.Parse(myQueueItem)
      employeeDocument <-
        { id = sprintf "%s-%s" employee?name employee?employeeId
          name = employee?name
          employeeId = employee?employeeId
          address = employee?address }
```

Ez a példa szükséges egy `project.json` fájl, amely meghatározza a `FSharp.Interop.Dynamic` és `Dynamitey` NuGet függőségek:

```json
{
    "frameworks": {
        "net46": {
          "dependencies": {
            "Dynamitey": "1.0.2",
            "FSharp.Interop.Dynamic": "3.0.0"
           }
        }
    }
}
```

Hozzáadása egy `project.json` fájl című [F # felügyeleti csomag](functions-reference-fsharp.md#package).

## <a name="output---attributes"></a>Kimeneti - attribútumok

A [C# osztálykönyvtárakhoz](functions-dotnet-class-library.md), használja a [CosmosDB](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/v2.x/master/WebJobs.Extensions.CosmosDB/CosmosDBAttribute.cs) attribútum.

Az attribútum konstruktora időt vesz igénybe, az adatbázis és gyűjtemény nevét. Ezeket a beállításokat és egyéb tulajdonságok konfigurálható kapcsolatos információkért lásd: [kimeneti - konfigurációs](#output---configuration). Íme egy `CosmosDB` metódus-aláírás attribútum példát:

```csharp
    [FunctionName("QueueToDocDB")]        
    public static void Run(
        [QueueTrigger("myqueue-items", Connection = "AzureWebJobsStorage")] string myQueueItem,
        [CosmosDB("ToDoList", "Items", Id = "id", ConnectionStringSetting = "myCosmosDB")] out dynamic document)
    {
        ...
    }
```

Tekintse meg a teljes például [kimeneti - C# példa](#output---c-example).

## <a name="output---configuration"></a>Kimeneti - konfiguráció

Az alábbi táblázat ismerteti a beállított kötés konfigurációs tulajdonságok a *function.json* fájl és a `CosmosDB` attribútum.

|Function.JSON tulajdonság | Attribútum tulajdonsága |Leírás|
|---------|---------|----------------------|
|**type**     || meg kell `cosmosDB`.        |
|**direction**     || meg kell `out`.         |
|**name**     || A kötési paraméter, a függvény a dokumentum jelölő neve.  |
|**DatabaseName** | **DatabaseName**|Az adatbázis, a gyűjteményt, ahol a dokumentum létre tartalmazó.     |
|**CollectionName** |**CollectionName**  | A gyűjtemény, ahol létrehozzák a dokumentum neve. |
|**createIfNotExists**  |**CreateIfNotExists**    | Egy logikai értéket, amely azt jelzi, hogy a gyűjtemény létrehozása, ha még nem létezik. Az alapértelmezett érték *hamis* új gyűjtemények létrejövő fenntartott átviteli, amelyek hatással vannak költsége van. További tájékoztatás a [díjszabási lapon](https://azure.microsoft.com/pricing/details/cosmos-db/) olvasható.  |
|**partitionKey**|**PartitionKey** |Amikor `CreateIfNotExists` igaz értékű, meghatározza a partíció kulcs elérési útja a létrehozott gyűjteményhez.|
|**CollectionThroughput**|**CollectionThroughput**| Ha `CreateIfNotExists` igaz értékű, meghatározza a [átviteli](../cosmos-db/set-throughput.md) a létrehozott gyűjtemény.|
|**connectionStringSetting**    |**ConnectionStringSetting** |Az Alkalmazásbeállítás, amely tartalmazza az Azure Cosmos DB kapcsolati karakterlánc nevét.        |

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="output---usage"></a>Kimeneti - használat

Alapértelmezés szerint amikor a a függvénynek a kimeneti paraméter írni egy dokumentum jön létre az adatbázisban. A dokumentumban az automatikusan előállított GUID Azonosítóhoz van, a dokumentum azonosítóját. A kimeneti dokumentum dokumentumazonosító megadásával is megadhat a `id` a kimeneti paraméternek átadott a JSON-objektum tulajdonságait. 

> [!Note]  
> Amikor megad egy meglévő dokumentum Azonosítóját, a fiókbeállítása felülíródik az új kimeneti dokumentum szerint. 

## <a name="exceptions-and-return-codes"></a>Kivételeket és a visszatérési kódok

| Kötelező | Leírások |
|---|---|
| CosmosDB | [CosmosDB hibakódok](https://docs.microsoft.com/rest/api/cosmos-db/http-status-codes-for-cosmosdb) |

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Nyissa meg egy Cosmos DB eseményindító használó gyors üzembe helyezés](functions-create-cosmos-db-triggered-function.md)

> [!div class="nextstepaction"]
> [További információ a Cosmos DB számítási kiszolgáló nélküli adatbázis](..\cosmos-db\serverless-computing-database.md)

> [!div class="nextstepaction"]
> [További tudnivalók az Azure functions eseményindítók és kötések](functions-triggers-bindings.md)
