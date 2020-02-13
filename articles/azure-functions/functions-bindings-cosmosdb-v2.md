---
title: Azure Cosmos DB a 2. x függvények kötéseit
description: Megtudhatja, hogyan használhatja az Azure Cosmos DB-eseményindítók és kötések az Azure Functions szolgáltatásban.
author: craigshoemaker
ms.topic: reference
ms.date: 11/21/2017
ms.author: cshoe
ms.openlocfilehash: 3ef2fdcaefeedb0769eac34d292e67a99524a6f2
ms.sourcegitcommit: bdf31d87bddd04382effbc36e0c465235d7a2947
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/12/2020
ms.locfileid: "77168061"
---
# <a name="azure-cosmos-db-bindings-for-azure-functions-2x"></a>Azure Functions 2. x Azure Cosmos DB kötései

> [!div class="op_single_selector" title1="Válassza ki az Ön által használt Azure Functions futtatókörnyezet verzióját: "]
> * [1-es verzió](functions-bindings-cosmosdb.md)
> * [2-es verzió](functions-bindings-cosmosdb-v2.md)

Ez a cikk azt ismerteti, hogyan használhatók [Azure Cosmos db](../cosmos-db/serverless-computing-database.md) kötések Azure functions 2. x verzióban. Az Azure Functions támogatja a-trigger, bemeneti és kimeneti kötések az Azure Cosmos DB.

> [!NOTE]
> Ez a cikk a [Azure functions 2. x verziójának](functions-versions.md).  További információ a kötések használatáról az 1. x függvényeknél: [Azure Cosmos db kötések Azure functions 1. x verzióhoz](functions-bindings-cosmosdb.md).
>
> Ennek a kötésnek a DocumentDB eredetileg neve. A functions 2. x verziójában a trigger, a kötések és a csomag neve Cosmos DB.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="supported-apis"></a>Támogatott API-k

[!INCLUDE [SQL API support only](../../includes/functions-cosmosdb-sqlapi-note.md)]

## <a name="packages---functions-2x"></a>Csomagok – 2.x függvények

A függvények Azure Cosmos DB kötései a 2. x verzióhoz tartoznak a [Microsoft. Azure. webjobs. Extensions. CosmosDB](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.CosmosDB) NuGet csomag 3. x verziójában. A kötések forráskódja az [Azure-webjobs-SDK-Extensions GitHub-](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.CosmosDB/) tárházban található.

[!INCLUDE [functions-package-v2](../../includes/functions-package-v2.md)]

## <a name="trigger"></a>Eseményindító

A Azure Cosmos DB trigger a [Azure Cosmos db változási csatornát](../cosmos-db/change-feed.md) használja, hogy figyelje a lapkákat és a frissítéseket a partíciók között. A módosítási hírcsatorna beszúrások és frissítéseket, törléseket nem tesz közzé.

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

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

# <a name="c-scripttabcsharp-script"></a>[C#Parancsfájl](#tab/csharp-script)

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

Íme a C#-szkriptkódot:

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

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

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

A következő JavaScript-kódot:

```javascript
    module.exports = function (context, documents) {
      context.log('First document Id modified : ', documents[0].id);

      context.done();
    }
```

# <a name="pythontabpython"></a>[Python](#tab/python)

Az alábbi példa egy Cosmos DB trigger kötést mutat be egy *function. JSON* fájlban, valamint egy olyan [Python-függvényt](functions-reference-python.md) , amely a kötést használja. A függvény naplóüzenetek ír, Cosmos DB-rekordok módosításakor.

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

# <a name="javatabjava"></a>[Java](#tab/java)

Ez a függvény akkor kerül meghívásra, ha a megadott adatbázisban és gyűjteményben lapkák vagy frissítések vannak.

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


A [Java functions runtime library](/java/api/overview/azure/functions/runtime)-ben használja a `@CosmosDBTrigger` Megjegyzés azon paramétereket, amelyek értéke Cosmos DBból származik.  Ezt a jegyzetet natív Java-típusokkal, Szerializálói vagy NULL értékű értékekkel lehet használni `Optional<T>`használatával.

---

## <a name="trigger---attributes-and-annotations"></a>Trigger – attribútumok és jegyzetek

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

Az [ C# osztályok könyvtáraiban](functions-dotnet-class-library.md)használja a [CosmosDBTrigger](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.CosmosDB/Trigger/CosmosDBTriggerAttribute.cs) attribútumot.

Az attribútum konstruktorának paramétereként meg az adatbázis és gyűjtemény nevét. További információ ezekről a beállításokról és a konfigurálható egyéb tulajdonságokról: [trigger-Configuration](#trigger---configuration). Példa `CosmosDBTrigger` attribútumra a metódus-aláírásban:

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

A teljes példa: [trigger](#trigger).

# <a name="c-scripttabcsharp-script"></a>[C#Parancsfájl](#tab/csharp-script)

Az C# attribútumokat a parancsfájl nem támogatja.

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

A JavaScript nem támogatja az attribútumokat.

# <a name="pythontabpython"></a>[Python](#tab/python)

A Python nem támogatja az attribútumokat.

# <a name="javatabjava"></a>[Java](#tab/java)

A [Java functions futtatókörnyezet könyvtárában](https://docs.microsoft.com/java/api/overview/azure/functions/runtime)használja a `@CosmosDBInput` megjegyzését azon paramétereknél, amelyek a Cosmos db adatait olvassák.

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
|**leaseConnectionStringSetting** | **LeaseConnectionStringSetting** | Választható Egy olyan Alkalmazásbeállítás neve, amely tartalmazza a kapcsolódási karakterláncot a címbérleti gyűjteményt tároló Azure Cosmos DB fiókhoz. Ha nincs beállítva, a rendszer a `connectionStringSetting` értéket használja. Ez a paraméter értéke a portálon a kötés létrehozásakor automatikusan. A bérletek gyűjteménye kapcsolati karakterláncára írási engedéllyel kell rendelkeznie.|
|**leaseDatabaseName** |**LeaseDatabaseName** | (Nem kötelező) A database csatlakoztatásához használt bérletek tárolásához használni kívánt gyűjtemény neve. Ha nincs beállítva, a rendszer a `databaseName` beállítás értékét használja. Ez a paraméter értéke a portálon a kötés létrehozásakor automatikusan. |
|**leaseCollectionName** | **LeaseCollectionName** | (Nem kötelező) A használt bérletek tárolásához használni kívánt gyűjtemény neve. Ha nincs beállítva, a rendszer a `leases` értéket használja. |
|**createLeaseCollectionIfNotExists** | **CreateLeaseCollectionIfNotExists** | Választható Ha `true`értékre van állítva, a bérletek gyűjteménye automatikusan létrejön, ha még nem létezik. Az alapértelmezett érték `false`. |
|**leasesCollectionThroughput**| **LeasesCollectionThroughput**| Választható Meghatározza a bérletek gyűjteményének létrehozásakor hozzárendelni kívánt kérelmek számát. Ezt a beállítást csak akkor használja a rendszer, ha a `createLeaseCollectionIfNotExists` `true`ra van beállítva. Ez a paraméter értéke automatikusan, a kötés létrehozásakor a portál használatával.
|**leaseCollectionPrefix**| **LeaseCollectionPrefix**| Választható Ha be van állítva, a rendszer hozzáadja az értéket az ehhez a függvényhez tartozó címbérleti gyűjteményben létrehozott bérletek előtagjaként. Az előtaggal két különálló Azure Functions is megoszthatja ugyanazt a címbérletet különböző előtagok használatával.
|**feedPollDelay**| **FeedPollDelay**| Választható Az az idő (ezredmásodpercben), ameddig a rendszer megkérdezi a partíciót az új változásokhoz a hírcsatornán, miután az összes aktuális változást kiüríti. Az alapértelmezett érték 5 000 ezredmásodperc vagy 5 másodperc.
|**leaseAcquireInterval**| **LeaseAcquireInterval**| (Nem kötelező) Érték beállítása esetén azt határozza meg, ezredmásodpercben, az időköz elindít egy feladatot a számítási, ha a partíciók lesznek elosztva a gazdagép ismert példányok között. Alapértelmezés szerint 13000 (13 másodperc).
|**leaseExpirationInterval**| **LeaseExpirationInterval**| (Nem kötelező) Érték beállítása esetén azt határozza meg, ezredmásodpercben, az időköz, amelynek a bérlet egy bérletet, egy partíciót jelölő készül. A bérlet ezen az időtartamon belül nem újítja meg, ha azt eredményezi, hamarosan lejár, és a partíció tulajdonjogának áthelyezi egy másik példánya. Alapértelmezés szerint 60000 (60 másodperc).
|**leaseRenewInterval**| **LeaseRenewInterval**| (Nem kötelező) Érték beállítása esetén azt határozza meg, ezredmásodpercben, minden bérletek példány által jelenleg birtokolt partíciók megújítási időköz. Alapértelmezés szerint 17000 (17 másodperc).
|**checkpointFrequency**| **CheckpointFrequency**| (Nem kötelező) Érték beállítása esetén azt határozza meg, ezredmásodpercben, a bérlet ellenőrzőpontok közötti időtartam. Az alapértelmezett érték mindig az egyes függvények hívása után történik.
|**maxItemsPerInvocation**| **MaxItemsPerInvocation**| Választható Ha be van állítva, ez a tulajdonság határozza meg a függvényhívás által fogadott elemek maximális számát. Ha a figyelt gyűjteményben lévő műveleteket tárolt eljárásokkal hajtják végre, a [tranzakció hatóköre](../cosmos-db/stored-procedures-triggers-udfs.md#transactions) megmarad a változási csatornán lévő elemek olvasása során. Ennek eredményeképpen a fogadott elemek száma magasabb lehet a megadott értéknél, így az ugyanazon tranzakció által módosított elemek egy atomi köteg részeként lesznek visszaadva.
|**startFromBeginning**| **StartFromBeginning**| Választható Ez a beállítás azt jelzi, hogy a trigger beolvassa a gyűjtemény korábbi változásait, nem pedig az aktuális időpontot. Az elejétől való olvasás csak az indítás első indításakor működik, ahogy az a későbbi futtatásokban is, az ellenőrzőpontok már tárolódnak. Ha úgy állítja be ezt a beállítást, hogy a már létrehozott bérletek már nem lépnek érvénybe, `true`.

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="trigger---usage"></a>Eseményindító - használat

Az triggerhez egy második gyűjteményre van szükség, amelyet a a partíciók _bérletének_ tárolására használ. A figyelt gyűjtemény és a gyűjtemény, amely tartalmazza a bérleteket is működnek az eseményindító elérhetőnek kell lennie.

>[!IMPORTANT]
> Ha több függvény úgy van konfigurálva, hogy egy Cosmos DB triggert használjon ugyanahhoz a gyűjteményhez, a függvények mindegyikének dedikált címbérleti gyűjteményt kell használnia, vagy más `LeaseCollectionPrefix` kell megadnia az egyes függvényekhez. Ellenkező esetben a függvények csak az egyik aktiválódik. További információ az előtagról: [konfiguráció szakasz](#trigger---configuration).

Az eseményindító egy dokumentumot frissítve lett, vagy beszúrva, azt csak biztosítja-e a saját maga a dokumentum nem jelzi. Ha frissítések, és beszúr eltérően kezelésére van szüksége, beszúrási vagy frissítési időbélyegző mezők az életbe léptetésével ezt.

## <a name="input"></a>Input (Bemenet)

Az Azure Cosmos DB bemeneti kötéssel legalább egy Azure Cosmos DB-dokumentumok lekérdezésének az SQL API-t használja, és továbbítja őket a függvény a bemeneti paraméter. A dokumentum azonosítója vagy lekérdezési paramétereket az eseményindítót, amely meghívja a függvényt alapján lehet meghatározni.

> [!NOTE]
> Ha a gyűjtemény [particionálva](../cosmos-db/partition-data.md#logical-partitions)van, a keresési műveleteknek meg kell adniuk a partíciós kulcs értékét is.
>

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

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

# <a name="c-scripttabcsharp-script"></a>[C#Parancsfájl](#tab/csharp-script)

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
    "type": "cosmosDB",
    "direction": "in",
    "databaseName": "MyDb",
    "collectionName": "MyCollection",
    "sqlQuery": "SELECT * from c where c.departmentId = {departmentId}",
    "connectionStringSetting": "CosmosDBConnection"
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

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

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

# <a name="pythontabpython"></a>[Python](#tab/python)

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

A [konfigurációs](#input---configuration) szakasz ezeket a tulajdonságokat ismerteti.

Itt látható a Python-kód:

```python
import azure.functions as func

def main(queuemsg: func.QueueMessage, documents: func.DocumentList):
    for document in documents:
        # operate on each document
```

# <a name="javatabjava"></a>[Java](#tab/java)

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

## <a name="input---attributes-and-annotations"></a>Bemenet – attribútumok és jegyzetek

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

Az [ C# osztályok könyvtáraiban](functions-dotnet-class-library.md)használja a [CosmosDB](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.CosmosDB/CosmosDBAttribute.cs) attribútumot.

Az attribútum konstruktorának paramétereként meg az adatbázis és gyűjtemény nevét. Ezekről a beállításokról és a konfigurálható egyéb tulajdonságokról [a következő konfigurációs szakaszban](#input---configuration)tájékozódhat.

# <a name="c-scripttabcsharp-script"></a>[C#Parancsfájl](#tab/csharp-script)

Az C# attribútumokat a parancsfájl nem támogatja.

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

A JavaScript nem támogatja az attribútumokat.

# <a name="pythontabpython"></a>[Python](#tab/python)

A Python nem támogatja az attribútumokat.

# <a name="javatabjava"></a>[Java](#tab/java)

A [Java functions futtatókörnyezet könyvtárában](https://docs.microsoft.com/java/api/overview/azure/functions/runtime)használja a `@CosmosDBOutput` megjegyzéseket a Cosmos DBba írandó paramétereknél. A Megjegyzés paraméterének típusának `OutputBinding<T>`nak kell lennie, ahol a `T` natív Java-típus vagy POJO.

---

## <a name="input---configuration"></a>Bemenet - konfiguráció

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

## <a name="input---usage"></a>Bemenet - használat

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

Ha a függvény sikeresen kilép, az elnevezett bemeneti paramétereken keresztül végrehajtott módosítások automatikusan megmaradnak.

# <a name="c-scripttabcsharp-script"></a>[C#Parancsfájl](#tab/csharp-script)

Ha a függvény sikeresen kilép, az elnevezett bemeneti paramétereken keresztül végrehajtott módosítások automatikusan megmaradnak.

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

A rendszer nem végez automatikusan frissítéseket a függvény kilépése után. Ehelyett a `context.bindings.<documentName>In` és a `context.bindings.<documentName>Out` használatával végezze el a frissítéseket. Tekintse meg a JavaScript példáját.

# <a name="pythontabpython"></a>[Python](#tab/python)

Az adat a függvény számára elérhetővé válik egy `DocumentList` paraméter használatával. A dokumentumban végrehajtott módosítások nem maradnak meg automatikusan.

# <a name="javatabjava"></a>[Java](#tab/java)

A [Java functions futtatókörnyezet könyvtárában](https://docs.microsoft.com/java/api/overview/azure/functions/runtime)a [@CosmosDBInput](https://docs.microsoft.com/java/api/com.microsoft.azure.functions.annotation.cosmosdbinput) jegyzet elérhetővé teszi a függvény Cosmos db adatait. Ezt a jegyzetet natív Java-típusokkal, Szerializálói vagy NULL értékű értékekkel lehet használni `Optional<T>`használatával.

---

## <a name="output"></a>Kimenet

Az Azure Cosmos DB kimeneti kötés lehetővé teszi, hogy egy Azure Cosmos DB-adatbázishoz az SQL API használatával írhat egy új dokumentumot.

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

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

# <a name="c-scripttabcsharp-script"></a>[C#Parancsfájl](#tab/csharp-script)

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

A [konfigurációs](#output---configuration) szakasz ezeket a tulajdonságokat ismerteti.

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

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

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

# <a name="pythontabpython"></a>[Python](#tab/python)

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

# <a name="javatabjava"></a>[Java](#tab/java)

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

## <a name="output---attributes-and-annotations"></a>Kimenet – attribútumok és jegyzetek

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

Az [ C# osztályok könyvtáraiban](functions-dotnet-class-library.md)használja a [CosmosDB](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/v2.x/master/WebJobs.Extensions.CosmosDB/CosmosDBAttribute.cs) attribútumot.

Az attribútum konstruktorának paramétereként meg az adatbázis és gyűjtemény nevét. További információ ezekről a beállításokról és a konfigurálható egyéb tulajdonságokról: [kimeneti konfiguráció](#output---configuration). Példa `CosmosDB` attribútumra a metódus-aláírásban:

```csharp
    [FunctionName("QueueToDocDB")]
    public static void Run(
        [QueueTrigger("myqueue-items", Connection = "AzureWebJobsStorage")] string myQueueItem,
        [CosmosDB("ToDoList", "Items", Id = "id", ConnectionStringSetting = "myCosmosDB")] out dynamic document)
    {
        ...
    }
```

# <a name="c-scripttabcsharp-script"></a>[C#Parancsfájl](#tab/csharp-script)

Az C# attribútumokat a parancsfájl nem támogatja.

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

A JavaScript nem támogatja az attribútumokat.

# <a name="pythontabpython"></a>[Python](#tab/python)

A Python nem támogatja az attribútumokat.

# <a name="javatabjava"></a>[Java](#tab/java)

A `CosmosDBOutput` jegyzet elérhető az adatCosmos DBba való íráshoz. A jegyzetet alkalmazhatja a függvényre vagy egy bizonyos Function paraméterre. A Function metódus használata esetén a függvény visszatérési értéke a Cosmos DBba írt érték. Ha a jegyzetet paraméterrel használja, a paraméter típusát olyan `OutputBinding<T>` kell deklarálni, amelyben `T` egy natív Java-típust vagy egy POJO.

---

## <a name="output---configuration"></a>Kimenete – konfiguráció

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

## <a name="output---usage"></a>Kimenet – használat

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

* [További információ a kiszolgáló nélküli adatbázis-számítástechnika Cosmos DB](../cosmos-db/serverless-computing-database.md)
* [További információ az Azure functions-eseményindítók és-kötésekről](functions-triggers-bindings.md)

<!---
> [!div class="nextstepaction"]
> [Go to a quickstart that uses a Cosmos DB trigger](functions-create-cosmos-db-triggered-function.md)
--->
