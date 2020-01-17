---
title: Azure Cosmos DB a 2. x függvények kötéseit
description: Megtudhatja, hogyan használhatók Azure Cosmos DB eseményindítók és kötések a Azure Functionsban.
author: craigshoemaker
ms.topic: reference
ms.date: 11/21/2017
ms.author: cshoe
ms.openlocfilehash: 478a7ae8d6938ee4d4ef5c30c8126c3e95f35305
ms.sourcegitcommit: 5bbe87cf121bf99184cc9840c7a07385f0d128ae
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/16/2020
ms.locfileid: "76121284"
---
# <a name="azure-cosmos-db-bindings-for-azure-functions-2x"></a>Azure Functions 2. x Azure Cosmos DB kötései

> [!div class="op_single_selector" title1="Válassza ki az Ön által használt Azure Functions futtatókörnyezet verzióját: "]
> * [1-es verzió](functions-bindings-cosmosdb.md)
> * [2-es verzió](functions-bindings-cosmosdb-v2.md)

Ez a cikk azt ismerteti, hogyan használhatók [Azure Cosmos db](../cosmos-db/serverless-computing-database.md) kötések Azure functions 2. x verzióban. Azure Functions támogatja a Azure Cosmos DB trigger-, bemeneti és kimeneti kötéseit.

> [!NOTE]
> Ez a cikk a [Azure functions 2. x verziójának](functions-versions.md).  További információ a kötések használatáról az 1. x függvényeknél: [Azure Cosmos db kötések Azure functions 1. x verzióhoz](functions-bindings-cosmosdb.md).
>
> Ez a kötés eredetileg a DocumentDB nevet kapta. A functions 2. x verziójában a trigger, a kötések és a csomag neve Cosmos DB.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="supported-apis"></a>Támogatott API-k

[!INCLUDE [SQL API support only](../../includes/functions-cosmosdb-sqlapi-note.md)]

## <a name="packages---functions-2x"></a>Csomagok – 2. x függvények

A függvények Azure Cosmos DB kötései a 2. x verzióhoz tartoznak a [Microsoft. Azure. webjobs. Extensions. CosmosDB](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.CosmosDB) NuGet csomag 3. x verziójában. A kötések forráskódja az [Azure-webjobs-SDK-Extensions GitHub-](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.CosmosDB/) tárházban található.

[!INCLUDE [functions-package-v2](../../includes/functions-package-v2.md)]

## <a name="trigger"></a>Eseményindító

A Azure Cosmos DB trigger a [Azure Cosmos db változási csatornát](../cosmos-db/change-feed.md) használja, hogy figyelje a lapkákat és a frissítéseket a partíciók között. A módosítási hírcsatorna közzéteszi a lapkákat és a frissítéseket, a törlést nem.

## <a name="trigger---example"></a>Trigger – példa

Tekintse meg a nyelvspecifikus példát:

* [C#](#trigger---c-example)
* [C#parancsfájl (. CSX)](#trigger---c-script-example)
* [Java](#trigger---java-example)
* [JavaScript](#trigger---javascript-example)
* [Python](#trigger---python-example)

[Trigger-példák kihagyása](#trigger---c-attributes)

### <a name="trigger---c-example"></a>Trigger – C# példa

Az alábbi példa egy [ C# függvényt](functions-dotnet-class-library.md) mutat be, amely akkor jelenik meg, ha a megadott adatbázisban és gyűjteményben lapkák vagy frissítések vannak.

```cs
using Microsoft.Azure.Documents;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Host;
using System.Collections.Generic;
using Microsoft.Extensions.Logging;

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
            ILogger log)
        {
            if (documents != null && documents.Count > 0)
            {
                log.LogInformation($"Documents modified: {documents.Count}");
                log.LogInformation($"First document Id: {documents[0].Id}");
            }
        }
    }
}
```

[Trigger-példák kihagyása](#trigger---c-attributes)

### <a name="trigger---c-script-example"></a>Trigger – C# parancsfájl-példa

Az alábbi példa egy Cosmos db trigger kötést mutat be egy *function. JSON* fájlban, valamint egy olyan [ C# parancsfájl-függvényt](functions-reference-csharp.md) , amely a kötést használja. A függvény naplófájlokat ír, amikor Cosmos DB rekordokat adnak hozzá vagy módosítanak.

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

A C# szkript kódja:

```cs
    #r "Microsoft.Azure.DocumentDB.Core"

    using System;
    using Microsoft.Azure.Documents;
    using System.Collections.Generic;
    using Microsoft.Extensions.Logging;

    public static void Run(IReadOnlyList<Document> documents, ILogger log)
    {
      log.LogInformation("Documents modified " + documents.Count);
      log.LogInformation("First document Id " + documents[0].Id);
    }
```

[Trigger-példák kihagyása](#trigger---c-attributes)

### <a name="trigger---javascript-example"></a>Trigger – JavaScript-példa

Az alábbi példa egy Cosmos DB trigger kötést mutat be egy *function. JSON* fájlban, valamint egy [JavaScript-függvényt](functions-reference-node.md) , amely a kötést használja. A függvény naplófájlokat ír, amikor Cosmos DB rekordokat adnak hozzá vagy módosítanak.

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

Itt látható a JavaScript-kód:

```javascript
    module.exports = function (context, documents) {
      context.log('First document Id modified : ', documents[0].id);

      context.done();
    }
```

[Trigger-példák kihagyása](#trigger---c-attributes)

### <a name="trigger---java-example"></a>Trigger – Java-példa

Az alábbi példa egy Cosmos DB trigger-kötést mutat be a *function. JSON* fájlban, valamint egy olyan [Java-függvényt](functions-reference-java.md) , amely a kötést használja. A függvény akkor kerül meghívásra, ha a megadott adatbázisban és gyűjteményben lapkák vagy frissítések vannak.

```json
{
    "type": "cosmosDBTrigger",
    "name": "items",
    "direction": "in",
    "leaseCollectionName": "leases",
    "connectionStringSetting": "AzureCosmosDBConnection",
    "databaseName": "ToDoList",
    "collectionName": "Items",
    "createLeaseCollectionIfNotExists": false
}
```

A Java-kód a következő:

```java
    @FunctionName("cosmosDBMonitor")
    public void cosmosDbProcessor(
        @CosmosDBTrigger(name = "items",
            databaseName = "ToDoList",
            collectionName = "Items",
            leaseCollectionName = "leases",
            createLeaseCollectionIfNotExists = true,
            connectionStringSetting = "AzureCosmosDBConnection") String[] items,
            final ExecutionContext context ) {
                context.getLogger().info(items.length + "item(s) is/are changed.");
            }
```


A [Java functions runtime library](/java/api/overview/azure/functions/runtime)-ben használja a `@CosmosDBTrigger` Megjegyzés azon paramétereket, amelyek értéke Cosmos DBból származik.  Ezt a jegyzetet natív Java-típusokkal, Szerializálói vagy NULL értékű értékekkel lehet használni opcionális\<T > használatával.


[Trigger-példák kihagyása](#trigger---c-attributes)


### <a name="trigger---python-example"></a>Trigger – Python-példa

Az alábbi példa egy Cosmos DB trigger kötést mutat be egy *function. JSON* fájlban, valamint egy olyan [Python-függvényt](functions-reference-python.md) , amely a kötést használja. A függvény naplófájlokat ír, amikor Cosmos DB rekordokat adnak hozzá vagy módosítanak.

Itt található a *function. JSON* fájlban található kötési adat:

```json
{
    "name": "documents",
    "type": "cosmosDBTrigger",
    "direction": "in",
    "leaseCollectionName": "leases",
    "connectionStringSetting": "<connection-app-setting>",
    "databaseName": "Tasks",
    "collectionName": "Items",
    "createLeaseCollectionIfNotExists": true
}
```

Itt látható a Python-kód:

```python
    import logging
    import azure.functions as func


    def main(documents: func.DocumentList) -> str:
        if documents:
            logging.info('First document Id modified: %s', documents[0]['id'])
```

## <a name="trigger---c-attributes"></a>Trigger – C# attribútumok

Az [ C# osztályok könyvtáraiban](functions-dotnet-class-library.md)használja a [CosmosDBTrigger](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.CosmosDB/Trigger/CosmosDBTriggerAttribute.cs) attribútumot.

Az attribútum konstruktora az adatbázis nevét és a gyűjtemény nevét veszi át. További információ ezekről a beállításokról és a konfigurálható egyéb tulajdonságokról: [trigger-Configuration](#trigger---configuration). Példa `CosmosDBTrigger` attribútumra a metódus-aláírásban:

```csharp
    [FunctionName("DocumentUpdates")]
    public static void Run(
        [CosmosDBTrigger("database", "collection", ConnectionStringSetting = "myCosmosDB")]
    IReadOnlyList<Document> documents,
        ILogger log)
    {
        ...
    }
```

A teljes példa: [trigger- C# example](#trigger---c-example).


## <a name="trigger---configuration"></a>Trigger – konfiguráció

Az alábbi táblázat a *function. JSON* fájlban és a `CosmosDBTrigger` attribútumban beállított kötési konfigurációs tulajdonságokat ismerteti.

|function. JSON-tulajdonság | Attribútum tulajdonsága |Leírás|
|---------|---------|----------------------|
|**type** | – | `cosmosDBTrigger`értékre kell állítani. |
|**direction** | – | `in`értékre kell állítani. Ez a paraméter automatikusan be van állítva, amikor létrehozza az triggert a Azure Portalban. |
|**név** | – | A módosítást tartalmazó dokumentumok listáját jelölő függvény kódjában használt változó neve. |
|**connectionStringSetting**|**ConnectionStringSetting** | A figyelt Azure Cosmos DB-fiókhoz való kapcsolódáshoz használt kapcsolati sztringet tartalmazó Alkalmazásbeállítás neve. |
|**databaseName**|**DatabaseName**  | Annak a Azure Cosmos DB-adatbázisnak a neve, amelyen a megfigyelt gyűjtemény szerepel. |
|**collectionName** |**CollectionName** | A figyelni kívánt gyűjtemény neve. |
|**leaseConnectionStringSetting** | **LeaseConnectionStringSetting** | Választható Egy olyan Alkalmazásbeállítás neve, amely a Azure Cosmos DB fiókhoz tartozó kapcsolódási karakterláncot tartalmazza, amely a címbérleti gyűjteményt tárolja. Ha nincs beállítva, a rendszer a `connectionStringSetting` értéket használja. A rendszer automatikusan beállítja ezt a paramétert, ha a kötés a portálon jön létre. A bérletek gyűjteményéhez tartozó kapcsolatok karakterláncának írási engedélyekkel kell rendelkeznie.|
|**leaseDatabaseName** |**LeaseDatabaseName** | Választható Annak az adatbázisnak a neve, amely a bérletek tárolásához használt gyűjteményt tárolja. Ha nincs beállítva, a rendszer a `databaseName` beállítás értékét használja. A rendszer automatikusan beállítja ezt a paramétert, ha a kötés a portálon jön létre. |
|**leaseCollectionName** | **LeaseCollectionName** | Választható A bérletek tárolásához használt gyűjtemény neve. Ha nincs beállítva, a rendszer a `leases` értéket használja. |
|**createLeaseCollectionIfNotExists** | **CreateLeaseCollectionIfNotExists** | Választható Ha `true`értékre van állítva, a bérletek gyűjteménye automatikusan létrejön, ha még nem létezik. Az alapértelmezett érték 0.`false` |
|**leasesCollectionThroughput**| **LeasesCollectionThroughput**| Választható Meghatározza a bérletek gyűjteményének létrehozásakor hozzárendelni kívánt kérelmek mennyiségét. Ezt a beállítást csak akkor használja a rendszer, ha a `createLeaseCollectionIfNotExists` `true`ra van beállítva. A rendszer automatikusan beállítja ezt a paramétert, ha a kötést a portál használatával hozza létre.
|**leaseCollectionPrefix**| **LeaseCollectionPrefix**| Választható Ha be van állítva, egy előtagot hoz létre a függvény címbérleti gyűjteményében létrehozott bérletekhez, ami gyakorlatilag lehetővé teszi két különálló Azure Functions számára, hogy ugyanazt a címbérleti gyűjteményt használják különböző előtagok használatával.
|**feedPollDelay**| **FeedPollDelay**| Választható Ha be van állítva, a rendszer ezredmásodpercben meghatározza, hogy a rendszer hogyan kérdezi le az új változásokhoz tartozó partíciót a hírcsatornában, miután az összes aktuális változást kiüríti. Az alapértelmezett érték 5000 (5 másodperc).
|**leaseAcquireInterval**| **LeaseAcquireInterval**| Választható Ha be van állítva, a rendszer ezredmásodpercben meghatározza az intervallumot, hogy kiindítson egy feladatot a számításhoz, ha a partíciókat egyenletesen osztják el az ismert gazdagép-példányok között. Az alapértelmezett érték 13000 (13 másodperc).
|**leaseExpirationInterval**| **LeaseExpirationInterval**| Választható A beállításakor a rendszer ezredmásodpercben meghatározza azt az intervallumot, ameddig a bérlet egy partíciót jelképező bérletre kerül. Ha a bérlet nem kerül megújításra ezen az intervallumon belül, az azt eredményezi, hogy a partíció tulajdonjoga lejár, és egy másik példányra kerül át. Az alapértelmezett érték 60000 (60 másodperc).
|**leaseRenewInterval**| **LeaseRenewInterval**| Választható A beállításakor a rendszer ezredmásodpercben meghatározza az adott példány által jelenleg tárolt partíciók címbérletének megújítási intervallumát. Az alapértelmezett érték 17000 (17 másodperc).
|**checkpointFrequency**| **CheckpointFrequency**| Választható Ha be van állítva, az meghatározza a címbérleti ellenőrzőpontok közötti intervallumot ezredmásodpercben. Az alapértelmezett érték mindig az egyes függvények hívása után történik.
|**maxItemsPerInvocation**| **MaxItemsPerInvocation**| Választható Ha be van állítva, ez a tulajdonság határozza meg a függvényhívás által fogadott elemek maximális mennyiségét. Ha a figyelt gyűjteményben lévő műveleteket tárolt eljárásokkal hajtják végre, a [tranzakció hatóköre](../cosmos-db/stored-procedures-triggers-udfs.md#transactions) megmarad a változási csatornán lévő elemek olvasása során. Ennek eredményeképpen előfordulhat, hogy a fogadott elemek mennyisége magasabb, mint a megadott érték, így az azonos tranzakció által módosított elemek egy atomi köteg részeként lesznek visszaadva.
|**startFromBeginning**| **StartFromBeginning**| Választható Ha be van állítva, a elindítja a triggert, hogy a jelenlegi időpont helyett a gyűjtemény előzményeinek elejéről olvassa be a módosításokat. Ez csak az indítás első indításakor működik, ahogy a későbbi futtatásokban is, az ellenőrzőpontok már tárolva vannak. Ha úgy állítja be ezt a `true`, hogy a bérletek már létrejöttek, nincs hatása.

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="trigger---usage"></a>Trigger – használat

Az triggerhez egy második gyűjteményre van szükség, amelyet a a partíciók _bérletének_ tárolására használ. Mind a figyelt gyűjtemény, mind a bérleteket tartalmazó gyűjteménynek elérhetőnek kell lennie az trigger működéséhez.

>[!IMPORTANT]
> Ha több függvény úgy van konfigurálva, hogy egy Cosmos DB triggert használjon ugyanahhoz a gyűjteményhez, a függvények mindegyikének dedikált címbérleti gyűjteményt kell használnia, vagy más `LeaseCollectionPrefix` kell megadnia az egyes függvényekhez. Ellenkező esetben csak az egyik függvény lesz aktiválva. További információ az előtagról: [konfiguráció szakasz](#trigger---configuration).

Az trigger nem jelzi, hogy a dokumentumot frissítették vagy beszúrták-e, csak magát a dokumentumot biztosítja. Ha a frissítéseket és a lapkákat különbözőképpen kell kezelnie, ezt megteheti a beszúráshoz vagy a frissítéshez tartozó időbélyegző-mezők megvalósításával.

## <a name="input"></a>Input (Bemenet)

Az Azure Cosmos DB bemeneti adatkötése az SQL API-t használja egy vagy több Azure Cosmos DB-dokumentum lekérésére, majd átadja ezeket a függvény bemeneti paraméterének. A dokumentumazonosítót vagy lekérdezési paramétereket a függvényt meghívó eseményindító alapján lehet meghatározni.

> [!NOTE]
> Ha a gyűjtemény [particionálva](../cosmos-db/partition-data.md#logical-partitions)van, a keresési műveleteknek meg kell adniuk a partíciós kulcs értékét is.
>

## <a name="input---examples"></a>Input – példák

Az azonosító érték megadásával tekintse meg az egyetlen dokumentumot olvasó nyelvspecifikus példákat:

* [C#](#input---c-examples)
* [C#parancsfájl (. CSX)](#input---c-script-examples)
* [F#](#input---f-examples)
* [Java](#input---java-examples)
* [JavaScript](#input---javascript-examples)
* [Python](#input---python-examples)

[Bemeneti példák kihagyása](#input---attributes)

### <a name="input---c-examples"></a>Input – C# példák

Ez a szakasz a következő példákat tartalmazza:

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

[Bemeneti példák kihagyása](#input---attributes)

#### <a name="queue-trigger-look-up-id-from-json-c"></a>Üzenetsor-trigger, Keresés azonosító a JSON-C#ből ()

Az alábbi példa egy olyan [ C# függvényt](functions-dotnet-class-library.md) mutat be, amely egyetlen dokumentumot kér le. A függvényt egy olyan üzenetsor-üzenet indítja el, amely egy JSON-objektumot tartalmaz. A várólista-trigger a JSON-t egy `ToDoItemLookup`típusú objektummá elemzi, amely tartalmazza az azonosító és a partíció kulcs értékét a kereséshez. Az azonosító és a partíciós kulcs értéke `ToDoItem` dokumentum lekérésére szolgál a megadott adatbázisból és gyűjteményből.

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

[Bemeneti példák kihagyása](#input---attributes)

#### <a name="http-trigger-look-up-id-from-query-string-c"></a>HTTP-trigger, Keresés azonosító a lekérdezési karakterláncból (C#)

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

[Bemeneti példák kihagyása](#input---attributes)

#### <a name="http-trigger-look-up-id-from-route-data-c"></a>HTTP-trigger, az útvonal adatainak megkereséseC#()

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

[Bemeneti példák kihagyása](#input---attributes)

#### <a name="http-trigger-look-up-id-from-route-data-using-sqlquery-c"></a>HTTP-trigger, keresés az útvonal adatai között, a SqlQuery (C#) használatával

Az alábbi példa egy olyan [ C# függvényt](functions-dotnet-class-library.md) mutat be, amely egyetlen dokumentumot kér le. A függvényt egy olyan HTTP-kérelem indítja el, amely az útválasztási adat használatával megkeresi az azonosítót. Ez az azonosító egy `ToDoItem` dokumentum lekérésére szolgál a megadott adatbázisból és gyűjteményből.

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

[Bemeneti példák kihagyása](#input---attributes)

#### <a name="http-trigger-get-multiple-docs-using-sqlquery-c"></a>HTTP-trigger, több dokumentum beolvasása aC#SqlQuery () használatával

Az alábbi példa egy olyan [ C# függvényt](functions-dotnet-class-library.md) mutat be, amely lekéri a dokumentumok listáját. A függvényt egy HTTP-kérelem indítja el. A lekérdezés a `SqlQuery` attribútum tulajdonságban van megadva.

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

[Bemeneti példák kihagyása](#input---attributes)

#### <a name="http-trigger-get-multiple-docs-using-documentclient-c"></a>HTTP-trigger, több dokumentum beolvasása aC#DocumentClient () használatával

Az alábbi példa egy olyan [ C# függvényt](functions-dotnet-class-library.md) mutat be, amely lekéri a dokumentumok listáját. A függvényt egy HTTP-kérelem indítja el. A kód a Azure Cosmos DB kötés által megadott `DocumentClient` példányt használja a dokumentumok listájának olvasásához. Az `DocumentClient` példány írási műveletekhez is használható.

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

[Bemeneti példák kihagyása](#input---attributes)

### <a name="input---c-script-examples"></a>Bemeneti – C# parancsfájl-példák

Ez a szakasz a következő példákat tartalmazza:

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

[Bemeneti példák kihagyása](#input---attributes)

#### <a name="queue-trigger-look-up-id-from-string-c-script"></a>Üzenetsor-trigger, Keresés azonosító karakterláncból (C# szkript)

Az alábbi példa egy Cosmos db bemeneti kötést mutat be egy *function. JSON* fájlban, valamint egy olyan [ C# parancsfájl-függvényt](functions-reference-csharp.md) , amely a kötést használja. A függvény egyetlen dokumentumot olvas be, és frissíti a dokumentum szöveges értékét.

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
A [konfigurációs](#input---configuration) szakasz ezeket a tulajdonságokat ismerteti.

A C# szkript kódja:

```cs
    using System;

    // Change input document contents using Azure Cosmos DB input binding
    public static void Run(string myQueueItem, dynamic inputDocument)
    {
      inputDocument.text = "This has changed.";
    }
```

[Bemeneti példák kihagyása](#input---attributes)

#### <a name="queue-trigger-get-multiple-docs-using-sqlquery-c-script"></a>Üzenetsor-trigger, több dokumentum beolvasása aC# SqlQuery (szkript) használatával

Az alábbi példa egy Azure Cosmos db bemeneti kötést mutat be egy *function. JSON* fájlban, valamint egy olyan [ C# parancsfájl-függvényt](functions-reference-csharp.md) , amely a kötést használja. A függvény több, egy SQL-lekérdezés által megadott dokumentumot kér le, a várólista-trigger használatával testreszabva a lekérdezési paramétereket.

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

A [konfigurációs](#input---configuration) szakasz ezeket a tulajdonságokat ismerteti.

A C# szkript kódja:

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

#### <a name="http-trigger-look-up-id-from-query-string-c-script"></a>HTTP-trigger, Keresés azonosító lekérdezési karakterláncbólC# (parancsfájl)

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

A C# szkript kódja:

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

[Bemeneti példák kihagyása](#input---attributes)

#### <a name="http-trigger-look-up-id-from-route-data-c-script"></a>HTTP-trigger, azonosító megkeresése az útvonalC# adatainak (szkript) alapján

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

A C# szkript kódja:

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

[Bemeneti példák kihagyása](#input---attributes)

#### <a name="http-trigger-get-multiple-docs-using-sqlquery-c-script"></a>HTTP-trigger, több dokumentum beolvasása aC# SqlQuery (szkript) használatával

A következő példa egy [ C# parancsfájl-függvényt](functions-reference-csharp.md) mutat be, amely a dokumentumok listáját kéri le. A függvényt egy HTTP-kérelem indítja el. A lekérdezés a `SqlQuery` attribútum tulajdonságban van megadva.

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

A C# szkript kódja:

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

[Bemeneti példák kihagyása](#input---attributes)

#### <a name="http-trigger-get-multiple-docs-using-documentclient-c-script"></a>HTTP-trigger, több dokumentum beolvasása aC# DocumentClient (szkript) használatával

A következő példa egy [ C# parancsfájl-függvényt](functions-reference-csharp.md) mutat be, amely a dokumentumok listáját kéri le. A függvényt egy HTTP-kérelem indítja el. A kód a Azure Cosmos DB kötés által megadott `DocumentClient` példányt használja a dokumentumok listájának olvasásához. Az `DocumentClient` példány írási műveletekhez is használható.

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

A C# szkript kódja:

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

[Bemeneti példák kihagyása](#input---attributes)

### <a name="input---javascript-examples"></a>Input-JavaScript-példák

Ez a szakasz az alábbi példákat tartalmazza, amelyek egyetlen dokumentumot olvasnak be a különböző forrásokból származó azonosító érték megadásával:

* [Üzenetsor-trigger, Keresés azonosító a JSON-ből](#queue-trigger-look-up-id-from-json-javascript)
* [HTTP-trigger, keresési azonosító lekérdezési karakterláncból](#http-trigger-look-up-id-from-query-string-javascript)
* [HTTP-trigger, azonosító megkeresése az útvonal adatainak alapján](#http-trigger-look-up-id-from-route-data-javascript)
* [Üzenetsor-trigger, több dokumentum beolvasása a SqlQuery használatával](#queue-trigger-get-multiple-docs-using-sqlquery-javascript)

[Bemeneti példák kihagyása](#input---attributes)

#### <a name="queue-trigger-look-up-id-from-json-javascript"></a>Üzenetsor-trigger, Keresés azonosító a JSON-ből (JavaScript)

Az alábbi példa egy Cosmos DB bemeneti kötést mutat be egy *function. JSON* fájlban, valamint egy [JavaScript-függvényt](functions-reference-node.md) , amely a kötést használja. A függvény egyetlen dokumentumot olvas be, és frissíti a dokumentum szöveges értékét.

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
A [konfigurációs](#input---configuration) szakasz ezeket a tulajdonságokat ismerteti.

Itt látható a JavaScript-kód:

```javascript
    // Change input document contents using Azure Cosmos DB input binding, using context.bindings.inputDocumentOut
    module.exports = function (context) {
    context.bindings.inputDocumentOut = context.bindings.inputDocumentIn;
    context.bindings.inputDocumentOut.text = "This was updated!";
    context.done();
    };
```

[Bemeneti példák kihagyása](#input---attributes)

#### <a name="http-trigger-look-up-id-from-query-string-javascript"></a>HTTP-trigger, Keresés azonosító lekérdezési karakterláncból (JavaScript)

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

Itt látható a JavaScript-kód:

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

[Bemeneti példák kihagyása](#input---attributes)

#### <a name="http-trigger-look-up-id-from-route-data-javascript"></a>HTTP-trigger, keresés az útvonal adatainak alapján (JavaScript)

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

Itt látható a JavaScript-kód:

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

[Bemeneti példák kihagyása](#input---attributes)

#### <a name="queue-trigger-get-multiple-docs-using-sqlquery-javascript"></a>Üzenetsor-trigger, több dokumentum beolvasása a SqlQuery használatával (JavaScript)

Az alábbi példa egy Azure Cosmos DB bemeneti kötést mutat be egy *function. JSON* fájlban, valamint egy [JavaScript-függvényt](functions-reference-node.md) , amely a kötést használja. A függvény több, egy SQL-lekérdezés által megadott dokumentumot kér le, a várólista-trigger használatával testreszabva a lekérdezési paramétereket.

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

A [konfigurációs](#input---configuration) szakasz ezeket a tulajdonságokat ismerteti.

Itt látható a JavaScript-kód:

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

### <a name="input---python-examples"></a>Bemenet – Python-példák

Ez a szakasz az alábbi példákat tartalmazza, amelyek egyetlen dokumentumot olvasnak be a különböző forrásokból származó azonosító érték megadásával:

* [Üzenetsor-trigger, Keresés azonosító a JSON-ből](#queue-trigger-look-up-id-from-json-python)
* [HTTP-trigger, keresési azonosító lekérdezési karakterláncból](#http-trigger-look-up-id-from-query-string-python)
* [HTTP-trigger, azonosító megkeresése az útvonal adatainak alapján](#http-trigger-look-up-id-from-route-data-python)
* [Üzenetsor-trigger, több dokumentum beolvasása a SqlQuery használatával](#queue-trigger-get-multiple-docs-using-sqlquery-python)

[Bemeneti példák kihagyása](#input---attributes)

#### <a name="queue-trigger-look-up-id-from-json-python"></a>Üzenetsor-trigger, Keresés azonosító a JSON-ből (Python)

Az alábbi példa egy Cosmos DB bemeneti kötést mutat be egy *function. JSON* fájlban, valamint egy olyan [Python-függvényt](functions-reference-python.md) , amely a kötést használja. A függvény egyetlen dokumentumot olvas be, és frissíti a dokumentum szöveges értékét.

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

A [konfigurációs](#input---configuration) szakasz ezeket a tulajdonságokat ismerteti.

Itt látható a Python-kód:

```python
import azure.functions as func


def main(queuemsg: func.QueueMessage, documents: func.DocumentList) -> func.Document:
    if documents:
        document = documents[0]
        document['text'] = 'This was updated!'
        return document
```

[Bemeneti példák kihagyása](#input---attributes)

#### <a name="http-trigger-look-up-id-from-query-string-python"></a>HTTP-trigger, Keresés azonosító lekérdezési karakterláncból (Python)

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

[Bemeneti példák kihagyása](#input---attributes)

#### <a name="http-trigger-look-up-id-from-route-data-python"></a>HTTP-trigger, azonosító megkeresése az útválasztási adatokból (Python)

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

[Bemeneti példák kihagyása](#input---attributes)

#### <a name="queue-trigger-get-multiple-docs-using-sqlquery-python"></a>Üzenetsor-trigger, több dokumentum beolvasása a SqlQuery (Python) használatával

Az alábbi példa egy Azure Cosmos DB bemeneti kötést mutat be egy *function. JSON* fájlban, valamint egy olyan [Python-függvényt](functions-reference-python.md) , amely a kötést használja. A függvény több, egy SQL-lekérdezés által megadott dokumentumot kér le, a várólista-trigger használatával testreszabva a lekérdezési paramétereket.

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

A [konfigurációs](#input---configuration) szakasz ezeket a tulajdonságokat ismerteti.

Itt látható a Python-kód:

```python
import azure.functions as func

def main(queuemsg: func.QueueMessage, documents: func.DocumentList):
    for document in documents:
        # operate on each document
```


[Bemeneti példák kihagyása](#input---attributes)

<a name="infsharp"></a>

### <a name="input---f-examples"></a>Input – F# példák

Az alábbi példa egy Cosmos db bemeneti kötést mutat be egy *function. JSON* fájlban, valamint egy olyan [ F# függvényt](functions-reference-fsharp.md) , amely a kötést használja. A függvény egyetlen dokumentumot olvas be, és frissíti a dokumentum szöveges értékét.

Itt található a *function. JSON* fájlban található kötési adat:

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

A [konfigurációs](#input---configuration) szakasz ezeket a tulajdonságokat ismerteti.

A kód a F# következő:

```fsharp
    (* Change input document contents using Azure Cosmos DB input binding *)
    open FSharp.Interop.Dynamic
    let Run(myQueueItem: string, inputDocument: obj) =
    inputDocument?text <- "This has changed."
```

Ehhez a példához egy `project.json` fájl szükséges, amely meghatározza a `FSharp.Interop.Dynamic` és a `Dynamitey` NuGet függőségeit:

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

`project.json` fájl hozzáadásával kapcsolatban lásd: [ F# csomagkezelő](functions-reference-fsharp.md#package).

### <a name="input---java-examples"></a>Bevitel – Java-példák

Ez a szakasz a következő példákat tartalmazza:

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

#### <a name="http-trigger-look-up-id-from-query-string---string-parameter-java"></a>HTTP-trigger, Keresés azonosító lekérdezési karakterláncból – karakterlánc-paraméter (Java)

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

A [Java functions runtime library](/java/api/overview/azure/functions/runtime)-ben használja a `@CosmosDBInput` Megjegyzés azon függvény paramétereit, amelyek értéke Cosmos DBból származik.  Ezt a jegyzetet natív Java-típusokkal, Szerializálói vagy NULL értékű értékekkel lehet használni opcionális\<T > használatával.

#### <a name="http-trigger-look-up-id-from-query-string---pojo-parameter-java"></a>HTTP-trigger, Keresés azonosító lekérdezési karakterláncból – POJO paraméter (Java)

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

#### <a name="http-trigger-look-up-id-from-route-data-java"></a>HTTP-trigger, azonosító megkeresése az útvonal adatainak (Java) alapján

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

#### <a name="http-trigger-look-up-id-from-route-data-using-sqlquery-java"></a>HTTP-trigger, keresés az útvonal adatai alapján, SqlQuery (Java) használatával

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

#### <a name="http-trigger-get-multiple-docs-from-route-data-using-sqlquery-java"></a>HTTP-trigger, több dokumentum beolvasása az útvonal adataiból a SqlQuery (Java) használatával

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

## <a name="input---attributes"></a>Input-attributes

Az [ C# osztályok könyvtáraiban](functions-dotnet-class-library.md)használja a [CosmosDB](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.CosmosDB/CosmosDBAttribute.cs) attribútumot.

Az attribútum konstruktora az adatbázis nevét és a gyűjtemény nevét veszi át. Ezekről a beállításokról és a konfigurálható egyéb tulajdonságokról [a következő konfigurációs szakaszban](#input---configuration)tájékozódhat.

## <a name="input---configuration"></a>Bemenet – konfiguráció

Az alábbi táblázat a *function. JSON* fájlban és a `CosmosDB` attribútumban beállított kötési konfigurációs tulajdonságokat ismerteti.

|function. JSON-tulajdonság | Attribútum tulajdonsága |Leírás|
|---------|---------|----------------------|
|**type**     | – | `cosmosDB`értékre kell állítani.        |
|**direction**     | – | `in`értékre kell állítani.         |
|**név**     | – | A függvényben szereplő dokumentumot jelképező kötési paraméter neve.  |
|**databaseName** |**DatabaseName** |A dokumentumot tartalmazó adatbázis.        |
|**collectionName** |**CollectionName** | A dokumentumot tartalmazó gyűjtemény neve. |
|**id**    | **Azonosító** | A lekérdezni kívánt dokumentum azonosítója. Ez a tulajdonság támogatja a [kötési kifejezéseket](./functions-bindings-expressions-patterns.md). Ne állítsa be az **ID** és a **sqlQuery** tulajdonságokat is. Ha nem állítja be az egyiket, a rendszer lekéri a teljes gyűjteményt. |
|**sqlQuery**  |**SqlQuery**  | Több dokumentum beolvasására szolgáló Azure Cosmos DB SQL-lekérdezés. A tulajdonság támogatja a futásidejű kötéseket, ahogy az a következő példában látható: `SELECT * FROM c where c.departmentId = {departmentId}`. Ne állítsa be az **ID** és a **sqlQuery** tulajdonságokat is. Ha nem állítja be az egyiket, a rendszer lekéri a teljes gyűjteményt.|
|**connectionStringSetting**     |**ConnectionStringSetting**|Az Azure Cosmos DB-kapcsolatok karakterláncát tartalmazó Alkalmazásbeállítás neve.        |
|**partitionKey**|**PartitionKey**|Meghatározza a keresés partíciós kulcsának értékét. Tartalmazhat kötési paramétereket. A [particionált](../cosmos-db/partition-data.md#logical-partitions) gyűjtemények esetében szükséges a keresés.|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="input---usage"></a>Bemenet – használat

A C# és F# a függvények esetében, ha a függvény sikeresen kilép, a bemeneti dokumentumon végrehajtott összes módosítás automatikusan megmarad.

JavaScript-függvényekben a frissítések nem automatikusan történnek a függvény kilépése után. Ehelyett a `context.bindings.<documentName>In` és a `context.bindings.<documentName>Out` használatával végezze el a frissítéseket. Tekintse meg a JavaScript példáját.

## <a name="output"></a>Kimenet

A Azure Cosmos DB kimeneti kötés lehetővé teszi, hogy új dokumentumot írjon egy Azure Cosmos DB-adatbázisba az SQL API használatával.

## <a name="output---examples"></a>Kimenet – példák

Tekintse meg a nyelvspecifikus példákat:

* [C#](#output---c-examples)
* [C#parancsfájl (. CSX)](#output---c-script-examples)
* [F#](#output---f-examples)
* [Java](#output---java-examples)
* [JavaScript](#output---javascript-examples)
* [Python](#output---python-examples)

Tekintse meg a `DocumentClient`t használó [bemeneti példát](#input---c-examples) is.

[Kimeneti példák kihagyása](#output---attributes)

### <a name="output---c-examples"></a>Kimenet – C# példák

Ez a szakasz a következő példákat tartalmazza:

* Üzenetsor-trigger, egy doc írása
* Üzenetsor-trigger, dokumentumok írása a IAsyncCollector használatával

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

[Kimeneti példák kihagyása](#output---attributes)

#### <a name="queue-trigger-write-one-doc-c"></a>Üzenetsor-trigger, írás egy docC#()

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

[Kimeneti példák kihagyása](#output---attributes)

#### <a name="queue-trigger-write-docs-using-iasynccollector-c"></a>Üzenetsor-trigger, dokumentumok írása a IAsyncCollectorC#() használatával

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

[Kimeneti példák kihagyása](#output---attributes)

### <a name="output---c-script-examples"></a>Kimeneti parancsfájl C# – példák

Ez a szakasz a következő példákat tartalmazza:

* Üzenetsor-trigger, egy doc írása
* Üzenetsor-trigger, dokumentumok írása a IAsyncCollector használatával

[Kimeneti példák kihagyása](#output---attributes)

#### <a name="queue-trigger-write-one-doc-c-script"></a>Üzenetsor-trigger, egy doc írásaC# (parancsfájl)

Az alábbi példa egy Azure Cosmos db kimeneti kötést mutat be egy *function. JSON* fájlban, valamint egy olyan [ C# parancsfájl-függvényt](functions-reference-csharp.md) , amely a kötést használja. A függvény üzenetsor-beviteli kötést használ egy olyan várólistához, amely a következő formátumban fogadja a JSON-t:

```json
{
    "name": "John Henry",
    "employeeId": "123456",
    "address": "A town nearby"
}
```

A függvény a következő formátumban hozza létre Azure Cosmos DB dokumentumokat az egyes rekordokhoz:

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

A [konfigurációs](#output---configuration) szakasz ezeket a tulajdonságokat ismerteti.

A C# szkript kódja:

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

#### <a name="queue-trigger-write-docs-using-iasynccollector"></a>Üzenetsor-trigger, dokumentumok írása a IAsyncCollector használatával

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

Itt látható a function. JSON fájl:

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

A C# szkript kódja:

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

[Kimeneti példák kihagyása](#output---attributes)

### <a name="output---javascript-examples"></a>Kimenet – JavaScript-példák

Az alábbi példa egy Azure Cosmos DB kimeneti kötést mutat be egy *function. JSON* fájlban, valamint egy [JavaScript-függvényt](functions-reference-node.md) , amely a kötést használja. A függvény üzenetsor-beviteli kötést használ egy olyan várólistához, amely a következő formátumban fogadja a JSON-t:

```json
{
    "name": "John Henry",
    "employeeId": "123456",
    "address": "A town nearby"
}
```

A függvény a következő formátumban hozza létre Azure Cosmos DB dokumentumokat az egyes rekordokhoz:

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

A [konfigurációs](#output---configuration) szakasz ezeket a tulajdonságokat ismerteti.

Itt látható a JavaScript-kód:

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

### <a name="output---f-examples"></a>Kimenet – F# példák

Az alábbi példa egy Azure Cosmos db kimeneti kötést mutat be egy *function. JSON* fájlban, valamint egy olyan [ F# függvényt](functions-reference-fsharp.md) , amely a kötést használja. A függvény üzenetsor-beviteli kötést használ egy olyan várólistához, amely a következő formátumban fogadja a JSON-t:

```json
{
    "name": "John Henry",
    "employeeId": "123456",
    "address": "A town nearby"
}
```

A függvény a következő formátumban hozza létre Azure Cosmos DB dokumentumokat az egyes rekordokhoz:

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
A [konfigurációs](#output---configuration) szakasz ezeket a tulajdonságokat ismerteti.

A kód a F# következő:

```fsharp
    open FSharp.Interop.Dynamic
    open Newtonsoft.Json
    open Microsoft.Extensions.Logging
    let Run(myQueueItem: string, employeeDocument: byref<obj>, log: ILogger) =
      log.LogInformation(sprintf "F# Queue trigger function processed: %s" myQueueItem)
      let employee = JObject.Parse(myQueueItem)
      employeeDocument <-
        { id = sprintf "%s-%s" employee?name employee?employeeId
          name = employee?name
          employeeId = employee?employeeId
          address = employee?address }
```

Ehhez a példához egy `project.json` fájl szükséges, amely meghatározza a `FSharp.Interop.Dynamic` és a `Dynamitey` NuGet függőségeit:

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

`project.json` fájl hozzáadásával kapcsolatban lásd: [ F# csomagkezelő](functions-reference-fsharp.md#package).

### <a name="output---java-examples"></a>Kimenet – Java-példák

* [Üzenetsor-trigger, üzenet mentése az adatbázisba visszatérési érték használatával](#queue-trigger-save-message-to-database-via-return-value-java)
* [HTTP-trigger, egy dokumentum mentése az adatbázisba visszatérési érték használatával](#http-trigger-save-one-document-to-database-via-return-value-java)
* [HTTP-trigger, egy dokumentum mentése az adatbázisba a OutputBinding használatával](#http-trigger-save-one-document-to-database-via-outputbinding-java)
* [HTTP-trigger, több dokumentum mentése az adatbázisba a OutputBinding használatával](#http-trigger-save-multiple-documents-to-database-via-outputbinding-java)


#### <a name="queue-trigger-save-message-to-database-via-return-value-java"></a>Üzenetsor-trigger, üzenet mentése az adatbázisba visszatérési érték (Java) használatával

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

#### <a name="http-trigger-save-one-document-to-database-via-return-value-java"></a>HTTP-trigger, egy dokumentum mentése az adatbázisba visszatérési értékkel (Java)

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

#### <a name="http-trigger-save-one-document-to-database-via-outputbinding-java"></a>HTTP-trigger, egy dokumentum mentése az adatbázisba a OutputBinding (Java) használatával

Az alábbi példa egy Java-függvényt mutat be, amely egy ```OutputBinding<T>``` kimeneti paraméter használatával ír egy dokumentumot a CosmosDB. Vegye figyelembe, hogy ebben a beállításban ez a ```outputItem``` paraméter, amelynek megjegyzéseit ```@CosmosDBOutput```, nem pedig a függvény aláírásával kell megadnia. A ```OutputBinding<T>``` használata lehetővé teszi, hogy a függvény kihasználhassa a dokumentumot a CosmosDB, miközben lehetővé teszi, hogy más értéket adjanak vissza a függvény hívójának, például egy JSON-vagy XML-dokumentumnak.

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

#### <a name="http-trigger-save-multiple-documents-to-database-via-outputbinding-java"></a>HTTP-trigger, több dokumentum mentése az adatbázisba a OutputBinding (Java) használatával

Az alábbi példa egy Java-függvényt mutat be, amely egy ```OutputBinding<T>``` kimeneti paraméterrel több dokumentumot ír a CosmosDB. Vegye figyelembe, hogy ebben a beállításban ez a ```outputItem``` paraméter, amelynek megjegyzéseit ```@CosmosDBOutput```, nem pedig a függvény aláírásával kell megadnia. A kimeneti paraméter a ```outputItem``` ```ToDoItem``` objektumok listáját tartalmazza, mint a sablon paraméterének típusa. A ```OutputBinding<T>``` használata lehetővé teszi, hogy a függvény kihasználhassa a dokumentumokat a CosmosDB, miközben lehetővé teszi, hogy más értéket adjanak vissza a függvény hívójának, például egy JSON-vagy XML-dokumentumnak.

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

### <a name="output---python-examples"></a>Kimenet – Python-példák

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

## <a name="output---attributes"></a>Kimenet – attribútumok

Az [ C# osztályok könyvtáraiban](functions-dotnet-class-library.md)használja a [CosmosDB](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/v2.x/master/WebJobs.Extensions.CosmosDB/CosmosDBAttribute.cs) attribútumot.

Az attribútum konstruktora az adatbázis nevét és a gyűjtemény nevét veszi át. További információ ezekről a beállításokról és a konfigurálható egyéb tulajdonságokról: [kimeneti konfiguráció](#output---configuration). Példa `CosmosDB` attribútumra a metódus-aláírásban:

```csharp
    [FunctionName("QueueToDocDB")]
    public static void Run(
        [QueueTrigger("myqueue-items", Connection = "AzureWebJobsStorage")] string myQueueItem,
        [CosmosDB("ToDoList", "Items", Id = "id", ConnectionStringSetting = "myCosmosDB")] out dynamic document)
    {
        ...
    }
```

Teljes példa: kimenet – C# példa.

## <a name="output---configuration"></a>Kimenet – konfiguráció

Az alábbi táblázat a *function. JSON* fájlban és a `CosmosDB` attribútumban beállított kötési konfigurációs tulajdonságokat ismerteti.

|function. JSON-tulajdonság | Attribútum tulajdonsága |Leírás|
|---------|---------|----------------------|
|**type**     | – | `cosmosDB`értékre kell állítani.        |
|**direction**     | – | `out`értékre kell állítani.         |
|**név**     | – | A függvényben szereplő dokumentumot jelképező kötési paraméter neve.  |
|**databaseName** | **DatabaseName**|A gyűjteményt tartalmazó adatbázis, amelyben a dokumentumot létrehozták.     |
|**collectionName** |**CollectionName**  | Annak a gyűjteménynek a neve, amelyben a dokumentumot létrehozták. |
|**Createifnotexists metódust**  |**Createifnotexists metódust**    | Logikai érték, amely azt jelzi, hogy a gyűjtemény létrejött-e, ha nem létezik. Az alapértelmezett érték a *false (hamis* ), mert az új gyűjtemények fenntartott átviteli sebességgel jönnek létre, ami a költségeket is érinti. További tájékoztatás a [díjszabási lapon](https://azure.microsoft.com/pricing/details/cosmos-db/) olvasható.  |
|**partitionKey**|**PartitionKey** |Ha a `CreateIfNotExists` értéke TRUE (igaz), a a létrehozott gyűjteményhez tartozó partíciós kulcs elérési útját határozza meg.|
|**collectionThroughput**|**CollectionThroughput**| Ha `CreateIfNotExists` értéke TRUE (igaz), meghatározza a létrehozott gyűjtemény [átviteli sebességét](../cosmos-db/set-throughput.md) .|
|**connectionStringSetting**    |**ConnectionStringSetting** |Az Azure Cosmos DB-kapcsolatok karakterláncát tartalmazó Alkalmazásbeállítás neve.        |

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="output---usage"></a>Kimenet – használat

Alapértelmezés szerint, amikor a függvény kimeneti paraméterére ír, létrejön egy dokumentum az adatbázisban. A dokumentum automatikusan generált GUID AZONOSÍTÓval rendelkezik. A kimeneti dokumentumhoz tartozó dokumentum AZONOSÍTÓjának megadásához adja meg a kimeneti paraméternek átadott JSON-objektum `id` tulajdonságát.

> [!Note]
> Egy meglévő dokumentum AZONOSÍTÓjának megadásakor a rendszer felülírja az új kimeneti dokumentumot.

## <a name="exceptions-and-return-codes"></a>Kivételek és visszatérési kódok

| Kötés | Leírások |
|---|---|
| CosmosDB | [CosmosDB-hibakódok](https://docs.microsoft.com/rest/api/cosmos-db/http-status-codes-for-cosmosdb) |

<a name="host-json"></a>

## <a name="hostjson-settings"></a>gazdagép. JSON-beállítások

Ez a szakasz a kötéshez elérhető globális konfigurációs beállításokat ismerteti a 2. x verzióban. A 2. x verziójú globális konfigurációs beállításokkal kapcsolatos további információkért lásd: [Host. JSON-dokumentáció Azure functions 2. x verzióhoz](functions-host-json.md).

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
|Protocol (Protokoll)|https|A függvény által a Azure Cosmos DB szolgáltatáshoz való kapcsolódáskor használt kapcsolati protokoll.  A [két mód magyarázata itt](../cosmos-db/performance-tips.md#networking) olvasható|
|leasePrefix|–|Az alkalmazás összes függvényében használandó bérlet-előtag.|

## <a name="next-steps"></a>Következő lépések

* [További információ a kiszolgáló nélküli adatbázis-számítástechnika Cosmos DB](../cosmos-db/serverless-computing-database.md)
* [További információ az Azure functions-eseményindítók és-kötésekről](functions-triggers-bindings.md)

<!---
> [!div class="nextstepaction"]
> [Go to a quickstart that uses a Cosmos DB trigger](functions-create-cosmos-db-triggered-function.md)
--->
