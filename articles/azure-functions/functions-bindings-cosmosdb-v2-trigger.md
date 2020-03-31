---
title: Az Azure Cosmos DB eseményindító a Functions 2.x-hez
description: Ismerje meg az Azure Cosmos DB eseményindító használatát az Azure Functionsben.
author: craigshoemaker
ms.topic: reference
ms.date: 02/24/2020
ms.author: cshoe
ms.openlocfilehash: de8ad39ef731af3dc272d700eeee346acda64b53
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79277569"
---
# <a name="azure-cosmos-db-trigger-for-azure-functions-2x"></a>Az Azure Cosmos DB eseményindítója az Azure Functions 2.x-hez

Az Azure Cosmos DB Trigger az [Azure Cosmos DB change feed](../cosmos-db/change-feed.md) használatával figyeli a beszúrások és frissítések partíciók között. A módosítási hírcsatorna beszúrásokat és frissítéseket tesz közzé, nem törléseket.

A beállítással és a konfigurációval kapcsolatos részletekről az [áttekintésben](./functions-bindings-cosmosdb-v2.md)olvashat.

<a id="example" name="example"></a>

# <a name="c"></a>[C #](#tab/csharp)

A következő példa egy [C# függvényt](functions-dotnet-class-library.md) mutat be, amely et a megadott adatbázisban és gyűjteményben beszúrások vagy frissítések vannak meghívva.

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

# <a name="c-script"></a>[C# parancsfájl](#tab/csharp-script)

A következő példa egy Cosmos DB-eseményindítót mutat be egy *function.json* fájlban és egy [C# parancsfájlfüggvényt,](functions-reference-csharp.md) amely a kötést használja. A függvény naplóüzeneteket ír, amikor a Cosmos DB rekordok hozzáadása vagy módosítása.

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

# <a name="javascript"></a>[Javascript](#tab/javascript)

A következő példa egy Cosmos DB-eseményindítót mutat be egy *function.json* fájlban és egy [JavaScript-függvényt,](functions-reference-node.md) amely a kötést használja. A függvény naplóüzeneteket ír, amikor a Cosmos DB rekordok hozzáadása vagy módosítása.

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

# <a name="python"></a>[Python](#tab/python)

A következő példa egy Cosmos DB-eseményindítót mutat be egy *function.json* fájlban és egy [Python-függvényt,](functions-reference-python.md) amely a kötést használja. A függvény naplóüzeneteket ír, amikor a Cosmos DB rekordok módosulnak.

A *function.json* fájlban a kötési adatok:

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

Itt a Python kód:

```python
    import logging
    import azure.functions as func


    def main(documents: func.DocumentList) -> str:
        if documents:
            logging.info('First document Id modified: %s', documents[0]['id'])
```

# <a name="java"></a>[Java](#tab/java)

Ez a függvény meghívása akkor történik meg, ha a megadott adatbázisban és gyűjteményben beszúrások vagy frissítések vannak.

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


A [Java függvények futásidejű függvénytárában](/java/api/overview/azure/functions/runtime)használja a jegyzeteket azon `@CosmosDBTrigger` paramétereken, amelyek értéke a Cosmos DB-ből származik.  Ez a jegyzet használható natív Java-típusok, POJOs vagy `Optional<T>`nullable értékek segítségével.

---

## <a name="attributes-and-annotations"></a>Attribútumok és jegyzetek

# <a name="c"></a>[C #](#tab/csharp)

A [C# osztálykönyvtárakban](functions-dotnet-class-library.md)használja a [CosmosDBTrigger](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.CosmosDB/Trigger/CosmosDBTriggerAttribute.cs) attribútumot.

Az attribútum konstruktora felveszi az adatbázis nevét és a gyűjtemény nevét. A beállításokkal és a konfigurálható egyéb tulajdonságokkal kapcsolatos további tudnivalókért olvassa el [az Eseményindító – konfiguráció](#configuration)című témakört. Íme egy `CosmosDBTrigger` attribútum példa egy metódus aláírás:

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

A teljes példa: [Trigger](#example).

# <a name="c-script"></a>[C# parancsfájl](#tab/csharp-script)

Az attribútumokat a C# script nem támogatja.

# <a name="javascript"></a>[Javascript](#tab/javascript)

A JavaScript nem támogatja az attribútumokat.

# <a name="python"></a>[Python](#tab/python)

Az attribútumokat a Python nem támogatja.

# <a name="java"></a>[Java](#tab/java)

A [Java függvények futásidejű függvénytár,](https://docs.microsoft.com/java/api/overview/azure/functions/runtime)használja a `@CosmosDBInput` jegyzet a paramétereket, amelyek adatokat olvasnak a Cosmos DB.

---

## <a name="configuration"></a>Konfiguráció

Az alábbi táblázat a *function.json* fájlban és az `CosmosDBTrigger` attribútumban beállított kötési konfigurációs tulajdonságokat ismerteti.

|function.json tulajdonság | Attribútum tulajdonság |Leírás|
|---------|---------|----------------------|
|**Típus** | n/a | A beállításnak `cosmosDBTrigger`a beállítására kell beállítható. |
|**direction** | n/a | A beállításnak `in`a beállítására kell beállítható. Ez a paraméter automatikusan be van állítva, amikor létrehozza az eseményindítót az Azure Portalon. |
|**név** | n/a | A függvénykódban használt változónév, amely a módosításokkal rendelkező dokumentumok listáját jelöli. |
|**connectionStringSetting**|**ConnectionStringSetting (Kapcsolatkarakterlánc-beállítás)** | A figyelt Azure Cosmos DB-fiókhoz való csatlakozáshoz használt kapcsolati karakterláncot tartalmazó alkalmazásbeállítás neve. |
|**adatbázisneve**|**DatabaseName**  | Az Azure Cosmos DB-adatbázis neve a gyűjtemény figyelt használatával. |
|**Lekérdezés_neve** |**CollectionName** | A figyelt gyűjtemény neve. |
|**leaseConnectionStringSetting** | **LeaseConnectionStringSetting** | (Nem kötelező) A címbérlet-gyűjteményt tartalmazó Azure Cosmos DB-fiókkapcsolati karakterláncát tartalmazó alkalmazásbeállítás neve. Ha nincs beállítva, a függvény az `connectionStringSetting` értéket használja. Ez a paraméter automatikusan be van állítva, amikor a kötés jön létre a portálon. A címbérlet-gyűjtemény kapcsolati karakterláncának írási engedéllyel kell rendelkeznie.|
|**leaseDatabaseName** |**LeaseDatabaseName** | (Nem kötelező) Annak az adatbázisnak a neve, amely a bérletek tárolására használt gyűjteményt tárolja. Ha nincs beállítva, `databaseName` a beállítás értéke lesz használva. Ez a paraméter automatikusan be van állítva, amikor a kötés jön létre a portálon. |
|**leaseCollectionName** | **LeaseCollectionName** | (Nem kötelező) A bérletek tárolására használt gyűjtemény neve. Ha nincs beállítva, a függvény az értéket `leases` használja. |
|**createLeaseCollectionIfNotExists** | **CreateLeaseCollectionIfNotExists** | (Nem kötelező) Ha a `true`beállítás, a bérletgyűjtemény automatikusan létrejön, ha még nem létezik. Az alapértelmezett érték `false`. |
|**leasesCollectionThroughput**| **LeasesCollectionThroughput**| (Nem kötelező) A címbérlet-gyűjtemény létrehozásakor hozzárendelhető kérelemegységek számát határozza meg. Ez a beállítás `createLeaseCollectionIfNotExists` csak akkor `true`használatos, ha a . Ez a paraméter automatikusan be van állítva, amikor a kötést a portál használatával hozza létre.
|**leaseCollectionPrefix**| **LeaseCollectionPrefix**| (Nem kötelező) Ha be van állítva, az érték előtagjaként hozzáadódik a függvény címbérlet-gyűjteményében létrehozott címbérletekhez. Egy előtag használata lehetővé teszi, hogy két különálló Azure-függvények ugyanazt a bérletgyűjteménykülönböző előtagok használatával.
|**feedPollDelay**| **FeedPollDelay (FeedPollDelay)**| (Nem kötelező) A partíció lekérdezése közötti késleltetés (ezredmásodpercben) a hírcsatorna új módosításai közötti késleltetés ideje, miután az összes aktuális módosítás kiürül. Az alapértelmezett érték 5000 ezredmásodperc, azaz 5 másodperc.
|**leaseAcquireInterval**| **LeaseAcquireInterval**| (Nem kötelező) Ha be van állítva, ezredmásodpercben határozza meg azt az időközt, amelyelindítja a feladatot, hogy kiszámítsa a partíciókat az ismert gazdapéldányok között egyenletesen elosztva. Az alapértelmezett érték 13000 (13 másodperc).
|**leaseExpirationInterval**| **LeaseExpirationInterval**| (Nem kötelező) Ha be van állítva, ezredmásodpercben határozza meg azt az időtartamot, amelyre a bérlet egy partíciót képviselő címbérletre kerül. Ha a bérlet nem újul meg ezen az időtartamon belül, akkor lejár, és a partíció tulajdonjoga egy másik példányba kerül. Az alapértelmezett érték 60000 (60 másodperc).
|**leaseRenewInterval között**| **LeaseRenewInterval között**| (Nem kötelező) Ha be van állítva, ezredmásodpercben határozza meg a megújítási időközt a partíciók jelenleg egy példány által birtokolt összes címbérletéhez. Az alapértelmezett érték 17000 (17 másodperc).
|**ellenőrzőpontGyakoriság**| **Ellenőrzőpontgyakoriság**| (Nem kötelező) Ha be van állítva, ezredmásodpercben határozza meg a címbérlet-ellenőrzőpontok közötti intervallumot. Az alapértelmezett érték mindig minden függvényhívás után történik.
|**maxItemsPerInvocation**| **MaxItemsPerInvocation**| (Nem kötelező) Ha be van állítva, ez a tulajdonság beállítja a függvényhívásonként fogadott elemek maximális számát. Ha a figyelt gyűjteményben a műveletek tárolt eljárásokon keresztül történnek, a [tranzakció hatóköre](../cosmos-db/stored-procedures-triggers-udfs.md#transactions) megmarad, amikor elemeket olvas be a változási hírből. Ennek eredményeképpen a kapott cikkek száma magasabb lehet, mint a megadott érték, így az ugyanazon tranzakció által módosított cikkek egy atomi köteg részeként kerülnek visszaadásra.
|**startFromBeginning**| **Elejétől kezdve**| (Nem kötelező) Ez a beállítás arra utasítja az eseményindítót, hogy a módosításokat a gyűjtemény módosítási előzményeinek elejétől olvassa el, és ne az aktuális időpontban kezdje. Az indítási eseményindító első indításakor az olvasás, az ellenőrzőpontok már tárolva vannak. Ha ezt `true` a beállítást úgy állítja be, hogy ha már vannak már létrehozott bérletek, annak nincs hatása. |
|**preferredLocations (elsődleges helyek)**| **PreferredLocations (PreferredLocations)**| (Nem kötelező) Az Azure Cosmos DB szolgáltatás georeplikált adatbázis-fiókjainak előnyben részesített helyeit (régióit) határozza meg. Az értékeket vesszővel kell elválasztani. Például: "USA keleti régiója, USA déli középső régiója, Észak-Európa". |

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="usage"></a>Használat

Az eseményindító hoz egy második gyűjtemény, amely a partíciók on-át _bérletek_ tárolására használja. Mind a figyelt gyűjteménynek, mind a címbérleteket tartalmazó gyűjteménynek elérhetőnek kell lennie ahhoz, hogy az eseményindító működjön.

>[!IMPORTANT]
> Ha több függvény van konfigurálva egy Cosmos DB-eseményindító ugyanazon gyűjtemény, minden a függvények kell használnia egy dedikált címbérlet-gyűjtemény, vagy adja meg az egyes függvények egy másik. `LeaseCollectionPrefix` Ellenkező esetben csak az egyik funkció aktiválódik. Az előtagról a [Konfiguráció című szakaszban](#configuration)talál további információt.

Az eseményindító nem azt jelzi, hogy egy dokumentumot frissítettek vagy beszúrtak, csak magát a dokumentumot biztosítja. Ha a frissítéseket és a beszúrásokat eltérően kell kezelnie, ezt megteheti időbélyeg-mezők beillesztésre vagy frissítésre való megvalósításával.

## <a name="next-steps"></a>További lépések

- [Azure Cosmos DB-dokumentum olvasása (bemeneti kötés)](./functions-bindings-cosmosdb-v2-input.md)
- [Az Azure Cosmos DB-dokumentum módosításának mentése (kimeneti kötés)](./functions-bindings-cosmosdb-v2-output.md)