---
title: Több független az Azure Cosmos DB-eseményindítókról létrehozása
description: Megtudhatja, hogyan konfigurálhatja több független az Azure Cosmos DB-eseményindítókról az Azure Functions eseményvezérelt architektúrák létrehozásához.
author: ealsur
ms.service: cosmos-db
ms.topic: sample
ms.date: 4/15/2019
ms.author: maquaran
ms.openlocfilehash: 7a47a928e97d52535a6d3baa808f1fcb81d9bb55
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/17/2019
ms.locfileid: "59700135"
---
# <a name="create-multiple-azure-cosmos-db-triggers"></a>Több Azure Cosmos DB-eseményindító létrehozása

Ez a cikk bemutatja, hogyan konfigurálhat több Cosmos DB-eseményindítókról dolgozhat párhuzamosan, és egymástól függetlenül reagálhat a változásokra.

![Kiszolgáló nélküli eseményvezérelt funkciók az Azure Cosmos DB-eseményindító kezelése és a egy bérleteket tároló megosztás](./media/change-feed-functions/multi-trigger.png)

## <a name="event-based-architecture-requirements"></a>Eseményvezérelt architektúra követelmények

Ha a kiszolgáló nélküli architektúrák építését [Azure Functions](../azure-functions/functions-overview.md), rendelkezik [ajánlott](../azure-functions/functions-best-practices.md#avoid-long-running-functions) kis függvény csoportok, amelyek együttműködve helyett nagy hosszú ideig futó függvények létrehozása.

Eseményalapú, kiszolgáló nélküli folyamatok használatával létrehozása a [Azure Cosmos DB-eseményindító](./change-feed-functions.md), fog futtatni a forgatókönyvet, ahol szeretné több dolog, amikor új esemény van egy adott [AzureCosmos-tároló](./databases-containers-items.md#azure-cosmos-containers). Ha műveleteket kiváltó, függetlenek egymástól, az ideális megoldás az lenne **hozzon létre egy Cosmos DB-eseményindító műveletenként** kíván végrehajtani, az összes figyeli a változásokat a ugyanazt a tárolót az Azure Cosmos.

## <a name="optimizing-containers-for-multiple-triggers"></a>A több eseményindító tárolók optimalizálása

Adott a *követelmények* a Cosmos DB-eseményindító, az állapot, más néven, tárolásához egy második tárolót kell a *bérleteket tároló*. Ez azt jelenti, hogy minden egyes Azure-függvény egy külön bérleteket tároló szükséges?

Itt két lehetősége van:

* Hozzon létre **egy tároló függvény bérletei**: Ez a megközelítés is jelenti azt, hogy további költségek, kivéve, ha használ olyan [megosztott átviteli adatbázis](./set-throughput.md#set-throughput-on-a-database). Ne feledje, hogy a minimális átviteli sebesség, a tároló szintjén-e a 400-as [kérelemegység](./request-units.md), és a címbérlet-tároló esetén, folyamatban van, csak a folyamatban használt ellenőrzőpont és -állapot karbantartásához.
* Rendelkezik **egy bérletbe a tárolót, és ossza meg** a függvények: A második lehetőség felhasznál jobban a kiosztott Kérelemegységek a tárolón, lehetővé teszi a több Azure Functions megosztani, és az azonos kiosztott átviteli sebesség.

Ez a cikk célja, nyissa meg a második lehetőség elvégzéséhez.

## <a name="configuring-a-shared-leases-container"></a>A bérletek megosztott tároló konfigurálása

A bérletek megosztott tároló konfigurálásához az kell, hogy az eseményindítók csak további konfigurációs hozzáadása a `LeaseCollectionPrefix` [attribútum](../azure-functions/functions-bindings-cosmosdb-v2.md#trigger---c-attributes) használatakor C# vagy `leaseCollectionPrefix` [attribútum](../azure-functions/functions-bindings-cosmosdb-v2.md#trigger---javascript-example)JavaScript használatakor. Az attribútum értékét, mely az adott eseményindító logikai leíró kell lennie.

Például, ha a három eseményindítók rendelkezik: egy által küldött e-mailt, egy, amelyet a materializált nézet létrehozása az összesítést, és egy, amely a módosításokat küld egy másik tárolási újabb elemzéshez, hozzárendelheti a `LeaseCollectionPrefix` "e-mail", az elsőt a " materializált", a másikat, és a harmadik egy" analytics".

A fontos része, hogy mindhárom aktivál **használhatja ugyanazt a bérletek konfigurációjának** (fiók, adatbázis és a tároló neve).

Egy nagyon egyszerű kódminták segítségével a `LeaseCollectionPrefix` attribútum C#, következő lenne:

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

És a Javascripthez, alkalmazhatja a konfiguráció a `function.json` fájlt, az a `leaseCollectionPrefix` attribútum:

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
> A kérelemegység kiépítve a bérletek megosztott tárolót, a folyamatosan figyelni. Minden Trigger, amely megosztja, szüksége lehet a kiosztott átviteli sebesség növelése növeli az Azure Functions által használt számát, így növeli az átviteli sebesség átlagos használat.

## <a name="next-steps"></a>További lépések

* Tekintse meg a teljes konfigurációját a [Azure Cosmos DB-eseményindító](../azure-functions/functions-bindings-cosmosdb-v2.md#trigger---configuration)
* Ellenőrizze a kiterjesztett [példák listájában](../azure-functions/functions-bindings-cosmosdb-v2.md#trigger---example) összes nyelv.
* Látogasson el az Azure Cosmos DB és az Azure Functions kiszolgáló nélküli receptek [GitHub-adattár](https://github.com/ealsur/serverless-recipes/tree/master/cosmosdbtriggerscenarios) további minták.