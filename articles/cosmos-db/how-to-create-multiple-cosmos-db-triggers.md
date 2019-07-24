---
title: Több független Azure Functions eseményindító létrehozása a Cosmos DBhoz
description: Megtudhatja, hogyan konfigurálhat több független Azure Functions eseményindítót a Cosmos DBhoz eseményvezérelt architektúrák létrehozásához.
author: ealsur
ms.service: cosmos-db
ms.topic: sample
ms.date: 07/17/2019
ms.author: maquaran
ms.openlocfilehash: 315ac1025a2b05ec7b16f7f0b14b66f224905d92
ms.sourcegitcommit: e9c866e9dad4588f3a361ca6e2888aeef208fc35
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/19/2019
ms.locfileid: "68335675"
---
# <a name="create-multiple-azure-functions-triggers-for-cosmos-db"></a>Több Azure Functions eseményindító létrehozása a Cosmos DBhoz

Ez a cikk azt ismerteti, hogyan konfigurálhat több Azure Functions eseményindítót, hogy a Cosmos DB párhuzamosan működjön, és egymástól függetlenül reagáljon a változásokra.

![Kiszolgáló nélküli eseményvezérelt függvények a Azure Functions eseményindítóval a Cosmos DB és a bérletek tárolójának megosztásakor](./media/change-feed-functions/multi-trigger.png)

## <a name="event-based-architecture-requirements"></a>Eseményvezérelt architektúra-követelmények

A kiszolgáló nélküli architektúrák a [Azure functions](../azure-functions/functions-overview.md)használatával történő létrehozásakor [ajánlott](../azure-functions/functions-best-practices.md#avoid-long-running-functions) olyan kis functions-csoportokat létrehozni, amelyek együttesen működnek a nagyméretű, hosszú ideig futó függvények helyett.

Az eseményvezérelt kiszolgáló nélküli folyamatok a [Cosmos DB Azure functions eseményindítójának](./change-feed-functions.md)használatával történő létrehozásakor a forgatókönyvben fog futni, ahol több dolgot szeretne tenni, amikor egy adott [Azure Cosmos](./databases-containers-items.md#azure-cosmos-containers)-tárolóban új esemény van. Ha az aktiválni kívánt műveletek egymástól függetlenek, az ideális megoldás az, hogy **egy Azure functions eseményindítót hozzon létre a kívánt művelethez Cosmos db** egy adott Azure Cosmos-tárolón végzett módosításokat.

## <a name="optimizing-containers-for-multiple-triggers"></a>Tárolók optimalizálása több eseményindítóhoz

Cosmos DB esetén  a Azure functions trigger követelményeit figyelembe véve egy második tárolóra van szükség az állapot tárolásához, más néven a *bérletek tárolója*. Ez azt jelenti, hogy minden egyes Azure-függvényhez külön címbérleti tárolóra van szükség?

Itt két lehetőség közül választhat:

* Hozzon létre **egy-egy bérletek tárolót**egy függvényben: Ez a megközelítés további költségekre is fordítható, kivéve, ha [megosztott átviteli sebességű adatbázist](./set-throughput.md#set-throughput-on-a-database)használ. Ne feledje, hogy a tároló szintjének minimális átviteli [sebessége 400,](./request-units.md)a bérletek tároló esetében pedig csak a folyamat előrehaladásának és karbantartásának ellenőrzéséhez használatos.
* Egyetlen címbérleti tárolóval kell rendelkeznie **, és** meg kell osztania az összes funkciója számára: Ez a második lehetőség a tárolóban lévő kiépített kérelmek jobb kihasználását teszi lehetővé, mivel több Azure Functionst is engedélyez a kiosztott átviteli sebesség megosztásához és használatához.

Ennek a cikknek a célja a második lehetőség megvalósítása.

## <a name="configuring-a-shared-leases-container"></a>Megosztott bérletek tárolójának konfigurálása

A megosztott bérletek tárolójának konfigurálásához az eseményindítók esetében az egyetlen további konfigurációt kell hozzáadnia [, ha a](../azure-functions/functions-bindings-cosmosdb-v2.md#trigger---c-attributes) C# `leaseCollectionPrefix` `LeaseCollectionPrefix` vagy az [attribútumot](../azure-functions/functions-bindings-cosmosdb-v2.md#trigger---javascript-example) használja, ha JavaScriptet használ. Az attribútum értékének logikai leírónak kell lennie az adott triggernél.

Ha például három eseményindítóval rendelkezik: az egyik az e-maileket küldi el, amelyek összesítést végeznek egy anyagbeli nézet létrehozásához, és az egyiket, amely egy másik tárhelyre küldi a módosításokat, a `LeaseCollectionPrefix` későbbi elemzésekhez az "e-maileket" is hozzárendelheti az elsőhöz, " "a második" és "analitika" a harmadik felé.

A fontos rész az, hogy mindhárom eseményindító **használhatja ugyanazt a bérletek tároló konfigurációját** (fiók, adatbázis és tároló neve).

A (z) `LeaseCollectionPrefix` attribútumát használó C#, nagyon egyszerű kódrészletek a következőképpen néznek ki:

```cs
using Microsoft.Azure.Documents;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Host;
using System.Collections.Generic;
using Microsoft.Extensions.Logging;

[FunctionName("SendEmails")]
public static void SendEmails([CosmosDBTrigger(
    databaseName: "ToDoItems",
    collectionName: "Items",
    ConnectionStringSetting = "CosmosDBConnection",
    LeaseCollectionName = "leases",
    LeaseCollectionPrefix = "emails")]IReadOnlyList<Document> documents,
    ILogger log)
{
    ...
}

[FunctionName("MaterializedViews")]
public static void MaterializedViews([CosmosDBTrigger(
    databaseName: "ToDoItems",
    collectionName: "Items",
    ConnectionStringSetting = "CosmosDBConnection",
    LeaseCollectionName = "leases",
    LeaseCollectionPrefix = "materialized")]IReadOnlyList<Document> documents,
    ILogger log)
{
    ...
}
```

A JavaScript esetében a konfigurációt `function.json` a fájlra `leaseCollectionPrefix` is alkalmazhatja a következő attribútummal:

```json
{
    "type": "cosmosDBTrigger",
    "name": "documents",
    "direction": "in",
    "leaseCollectionName": "leases",
    "connectionStringSetting": "CosmosDBConnection",
    "databaseName": "ToDoItems",
    "collectionName": "Items",
    "leaseCollectionPrefix": "emails"
},
{
    "type": "cosmosDBTrigger",
    "name": "documents",
    "direction": "in",
    "leaseCollectionName": "leases",
    "connectionStringSetting": "CosmosDBConnection",
    "databaseName": "ToDoItems",
    "collectionName": "Items",
    "leaseCollectionPrefix": "materialized"
}
```

> [!NOTE]
> Mindig figyelje a megosztott bérletek tárolóján kiépített kérelmek egységeit. Minden olyan trigger, amely megosztja azt, növeli az átviteli sebesség átlagos felhasználását, ezért előfordulhat, hogy az azt használó Azure Functions számának növelésével növelheti a kiosztott átviteli sebességet.

## <a name="next-steps"></a>További lépések

* Tekintse meg a [Cosmos DB Azure functions trigger](../azure-functions/functions-bindings-cosmosdb-v2.md#trigger---configuration) teljes konfigurációját
* Keresse meg az összes nyelvhez tartozó [minták kiterjesztett listáját](../azure-functions/functions-bindings-cosmosdb-v2.md#trigger---example) .
* További példákért látogasson el a kiszolgáló nélküli receptek Azure Cosmos DB és Azure Functions [GitHub](https://github.com/ealsur/serverless-recipes/tree/master/cosmosdbtriggerscenarios) -tárházba.