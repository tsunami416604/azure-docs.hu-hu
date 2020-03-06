---
title: A 2. x függvények Azure Cosmos DB triggere
description: Ismerje meg, hogyan használhatja a Azure Cosmos DB triggert a Azure Functionsban.
author: craigshoemaker
ms.topic: reference
ms.date: 02/24/2020
ms.author: cshoe
ms.openlocfilehash: c006aa8c46864b78ae46aa9c351605cca1d1e425
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/05/2020
ms.locfileid: "78388586"
---
# <a name="azure-cosmos-db-trigger-for-azure-functions-2x"></a>Azure Cosmos DB trigger a Azure Functions 2. x esetében

A Azure Cosmos DB trigger a [Azure Cosmos db változási csatornát](../cosmos-db/change-feed.md) használja, hogy figyelje a lapkákat és a frissítéseket a partíciók között. A módosítási hírcsatorna beszúrások és frissítéseket, törléseket nem tesz közzé.

További információ a telepítésről és a konfigurációról: [Áttekintés](./functions-bindings-cosmosdb-v2.md).

<a id="example" name="example"></a>

# <a name="c"></a>[C#](#tab/csharp)

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

# <a name="c-script"></a>[C#Parancsfájl](#tab/csharp-script)

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

# <a name="javascript"></a>[JavaScript](#tab/javascript)

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

# <a name="python"></a>[Python](#tab/python)

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

# <a name="java"></a>[Java](#tab/java)

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

## <a name="attributes-and-annotations"></a>Attribútumok és jegyzetek

# <a name="c"></a>[C#](#tab/csharp)

Az [ C# osztályok könyvtáraiban](functions-dotnet-class-library.md)használja a [CosmosDBTrigger](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.CosmosDB/Trigger/CosmosDBTriggerAttribute.cs) attribútumot.

Az attribútum konstruktorának paramétereként meg az adatbázis és gyűjtemény nevét. További információ ezekről a beállításokról és a konfigurálható egyéb tulajdonságokról: [trigger-Configuration](#configuration). Példa `CosmosDBTrigger` attribútumra a metódus-aláírásban:

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

A teljes példa: [trigger](#example).

# <a name="c-script"></a>[C#Parancsfájl](#tab/csharp-script)

Az C# attribútumokat a parancsfájl nem támogatja.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

A JavaScript nem támogatja az attribútumokat.

# <a name="python"></a>[Python](#tab/python)

A Python nem támogatja az attribútumokat.

# <a name="java"></a>[Java](#tab/java)

A [Java functions futtatókörnyezet könyvtárában](https://docs.microsoft.com/java/api/overview/azure/functions/runtime)használja a `@CosmosDBInput` megjegyzését azon paramétereknél, amelyek a Cosmos db adatait olvassák.

---

## <a name="configuration"></a>Konfiguráció

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
|**feedPollDelay**| **FeedPollDelay**| Választható Az az idő (ezredmásodpercben), amely során a rendszer lekérdezi a partíciót az új változásokhoz a hírcsatornán, miután az összes aktuális változást kiüríti. Az alapértelmezett érték 5 000 ezredmásodperc vagy 5 másodperc.
|**leaseAcquireInterval**| **LeaseAcquireInterval**| (Nem kötelező) Érték beállítása esetén azt határozza meg, ezredmásodpercben, az időköz elindít egy feladatot a számítási, ha a partíciók lesznek elosztva a gazdagép ismert példányok között. Alapértelmezés szerint 13000 (13 másodperc).
|**leaseExpirationInterval**| **LeaseExpirationInterval**| (Nem kötelező) Érték beállítása esetén azt határozza meg, ezredmásodpercben, az időköz, amelynek a bérlet egy bérletet, egy partíciót jelölő készül. A bérlet ezen az időtartamon belül nem újítja meg, ha azt eredményezi, hamarosan lejár, és a partíció tulajdonjogának áthelyezi egy másik példánya. Alapértelmezés szerint 60000 (60 másodperc).
|**leaseRenewInterval**| **LeaseRenewInterval**| (Nem kötelező) Érték beállítása esetén azt határozza meg, ezredmásodpercben, minden bérletek példány által jelenleg birtokolt partíciók megújítási időköz. Alapértelmezés szerint 17000 (17 másodperc).
|**checkpointFrequency**| **CheckpointFrequency**| (Nem kötelező) Érték beállítása esetén azt határozza meg, ezredmásodpercben, a bérlet ellenőrzőpontok közötti időtartam. Az alapértelmezett érték mindig az egyes függvények hívása után történik.
|**maxItemsPerInvocation**| **MaxItemsPerInvocation**| Választható Ha be van állítva, ez a tulajdonság határozza meg a függvényhívás által fogadott elemek maximális számát. Ha a figyelt gyűjteményben lévő műveleteket tárolt eljárásokkal hajtják végre, a [tranzakció hatóköre](../cosmos-db/stored-procedures-triggers-udfs.md#transactions) megmarad a változási csatornán lévő elemek olvasása során. Ennek eredményeképpen a fogadott elemek száma magasabb lehet a megadott értéknél, így az ugyanazon tranzakció által módosított elemek egy atomi köteg részeként lesznek visszaadva.
|**startFromBeginning**| **StartFromBeginning**| Választható Ez a beállítás azt jelzi, hogy a trigger beolvassa a gyűjtemény korábbi változásait, nem pedig az aktuális időpontot. Az elejétől való olvasás csak az indítás első indításakor működik, ahogy az a későbbi futtatásokban is, az ellenőrzőpontok már tárolódnak. Ha úgy állítja be ezt a beállítást, hogy a már létrehozott bérletek már nem lépnek érvénybe, `true`.

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="usage"></a>Használat

Az triggerhez egy második gyűjteményre van szükség, amelyet a a partíciók _bérletének_ tárolására használ. A figyelt gyűjtemény és a gyűjtemény, amely tartalmazza a bérleteket is működnek az eseményindító elérhetőnek kell lennie.

>[!IMPORTANT]
> Ha több függvény úgy van konfigurálva, hogy egy Cosmos DB triggert használjon ugyanahhoz a gyűjteményhez, a függvények mindegyikének dedikált címbérleti gyűjteményt kell használnia, vagy más `LeaseCollectionPrefix` kell megadnia az egyes függvényekhez. Ellenkező esetben a függvények csak az egyik aktiválódik. További információ az előtagról: [konfiguráció szakasz](#configuration).

Az eseményindító egy dokumentumot frissítve lett, vagy beszúrva, azt csak biztosítja-e a saját maga a dokumentum nem jelzi. Ha frissítések, és beszúr eltérően kezelésére van szüksége, beszúrási vagy frissítési időbélyegző mezők az életbe léptetésével ezt.

## <a name="next-steps"></a>Következő lépések

- [Azure Cosmos DB dokumentum olvasása (bemeneti kötés)](./functions-bindings-cosmosdb-v2-input.md)
- [Azure Cosmos DB dokumentum módosításainak mentése (kimeneti kötés)](./functions-bindings-cosmosdb-v2-output.md)