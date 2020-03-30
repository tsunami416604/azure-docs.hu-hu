---
title: Több független Azure-függvény aktiválásának létrehozása a Cosmos DB számára
description: Ismerje meg, hogyan konfigurálhat több független Azure Functions elindítja a Cosmos DB eseményvezérelt architektúrák létrehozásához.
author: ealsur
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/17/2019
ms.author: maquaran
ms.openlocfilehash: 32b680acdee29bf97a0e132fee93d5fee3377245
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77604940"
---
# <a name="create-multiple-azure-functions-triggers-for-cosmos-db"></a>Több Azure-függvény eseményindítólétrehozása a Cosmos DB számára

Ez a cikk azt ismerteti, hogyan konfigurálhat több Azure Functions-eseményindítót a Cosmos DB-hez úgy, hogy párhuzamosan működjenek és egymástól függetlenül reagáljanak a módosításokra.

![Kiszolgáló nélküli eseményalapú függvények, amelyek a Cosmos DB Azure Functions eseményindítójával dolgoznak, és megosztanak egy bérlettárolót](./media/change-feed-functions/multi-trigger.png)

## <a name="event-based-architecture-requirements"></a>Eseményalapú architektúra-követelmények

Kiszolgáló nélküli architektúrák [létrehozásakor](../azure-functions/functions-overview.md)az Azure Functions használata esetén [ajánlott](../azure-functions/functions-best-practices.md#avoid-long-running-functions) olyan kis függvénykészleteket létrehozni, amelyek a nagy, hosszú ideig futó függvények helyett együttműködnek.

Amikor eseményalapú kiszolgáló nélküli folyamatokat hoz létre a [Cosmos DB Azure Functions eseményindítójával,](./change-feed-functions.md)akkor a forgatókönyvbe fut, ahol több dolgot szeretne megtenni, amikor egy adott [Azure Cosmos-tárolóban](./databases-containers-items.md#azure-cosmos-containers)új esemény történik. Ha a kívánt műveleteket szeretne indítani, egymástól függetlenek, az ideális megoldás az lenne, hogy **hozzon létre egy Azure Functions-eseményindítók a Cosmos DB műveletenként** kívánt műveletet, minden figyelése módosítások ugyanazon az Azure Cosmos-tárolón.

## <a name="optimizing-containers-for-multiple-triggers"></a>Tárolók optimalizálása több eseményindítóhoz

Tekintettel az Azure Functions eseményindító a Cosmos *DB,* szükségünk van egy második tároló állapot tárolására, más néven a *bérleti tároló.* Ez azt jelenti, hogy minden Azure-függvényhez külön bérlettárolóra van szükség?

Itt két lehetőség közül választhat:

* **Függvényenként egy címbérlet-tároló létrehozása:** Ez a megközelítés további költségeket okozhat, kivéve, ha [megosztott átviteli-adatbázist](./set-throughput.md#set-throughput-on-a-database)használ. Ne feledje, hogy a tároló szintjén a minimális átviteli mennyiség 400 [kérelemegység,](./request-units.md)és a bérlettároló esetében csak a folyamat ellenőrzőpontjának és az állapot fenntartásának szolgál.
* Egy **bérlettárolóval rendelkezik, és ossza meg** az összes függvényéhez: Ez a második lehetőség jobban kihasználja a tárolón lévő kiosztott kérelemegységeket, mivel lehetővé teszi, hogy több Azure-függvény ossza meg és használja ugyanazt a kiosztott átviteli értéket.

A cél ennek a cikknek az, hogy végigvezeti Önt, hogy elérjék a második lehetőség.

## <a name="configuring-a-shared-leases-container"></a>Megosztott bérlettároló konfigurálása

A megosztott bérletek tárolójának konfigurálásához az egyetlen további konfiguráció, amelyet az eseményindítókon kell megadnia, az `LeaseCollectionPrefix` [attribútum](../azure-functions/functions-bindings-cosmosdb-v2-trigger.md#attributes-and-annotations) hozzáadása, ha C# vagy `leaseCollectionPrefix` [attribútumot](../azure-functions/functions-bindings-cosmosdb-v2-trigger.md) használ JavaScript használata esetén. Az attribútum értékének logikai leírónak kell lennie, hogy az adott eseményindító tetszése milyen.

Ha például három eseményindítója van: az egyik e-maileket küld, az egyik egy aggregációt hajt végre egy materializált nézet `LeaseCollectionPrefix` létrehozásához, és az egyik, amely elküldi a módosításokat egy másik tárolóba, későbbi elemzés céljából az "e-mailek" hozzárendelése az elsőhöz, "materializálódik" a másodikhoz, és "elemzés" a harmadikhoz.

A fontos rész az, hogy mindhárom eseményindítók **használhatja ugyanazt a bérletek tároló konfiguráció** (fiók, adatbázis és a tároló neve).

Egy nagyon egyszerű kódminták a `LeaseCollectionPrefix` C# attribútumhasználatával a következőkre hasonlítanak:

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

JavaScript esetén alkalmazhatja a konfigurációt `function.json` a fájlon, az `leaseCollectionPrefix` attribútummal:

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
> Mindig figyelje a megosztott bérletek tárolóján kiosztott kérelemegységeket. Minden egyes eseményindító, amely megosztja, növeli az átviteli átlagos felhasználást, így szükség lehet a kiosztott átviteli érték növelésére, ahogy növeli az azt használó Azure-függvények számát.

## <a name="next-steps"></a>További lépések

* Tekintse meg a [Cosmos DB Azure Functions eseményindítójának](../azure-functions/functions-bindings-cosmosdb-v2-trigger.md#configuration) teljes konfigurációját
* Ellenőrizze a minták bővített [listáját](../azure-functions/functions-bindings-cosmosdb-v2-trigger.md) az összes nyelven.
* További mintákért látogasson el a Kiszolgáló nélküli receptek az Azure Cosmos DB és az Azure Functions [GitHub-tárház](https://github.com/ealsur/serverless-recipes/tree/master/cosmosdbtriggerscenarios) segítségével.