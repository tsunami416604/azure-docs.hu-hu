---
title: Adatfolyamok módosítása Azure Cosmos DB API-MongoDB
description: Megtudhatja, hogyan használhatja a MongoDB n Azure Cosmos DB API-ját az adatain végrehajtott módosítások beszerzéséhez.
author: srchi
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: conceptual
ms.date: 11/16/2019
ms.author: srchi
ms.openlocfilehash: fbfce1c107fcf4b6f7d0b5f590a8ddfa64e69190
ms.sourcegitcommit: dbde4aed5a3188d6b4244ff7220f2f75fce65ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/19/2019
ms.locfileid: "74184737"
---
# <a name="change-streams-in-azure-cosmos-dbs-api-for-mongodb"></a>Adatfolyamok módosítása Azure Cosmos DB API-MongoDB

A Azure Cosmos DB API-MongoDB való [adatcsatorna](change-feed.md) -támogatás módosítása a Streams API-t használva érhető el. Az adatfolyamok módosítása API használatával az alkalmazások beszerezhetik a gyűjteményen vagy az egyetlen szegmensben lévő elemeken végrehajtott módosításokat. Később további műveleteket is végrehajthat az eredmények alapján. A gyűjtemény elemeinek módosításait a rendszer a módosítási idő sorrendjében rögzíti, és a rendezési sorrendet a rendszer a szegmens kulcsa szerint biztosítja.

Az alábbi példa azt szemlélteti, hogyan lehet módosítani a gyűjtemény összes elemének változásait. Ez a példa létrehoz egy kurzort, amellyel megtekintheti a beszúrt, frissített vagy lecserélt elemeket. A változtatási adatfolyamok beolvasásához a $match szakasz, a $project színpad és a fullDocument beállítás szükséges. A módosítási streameket használó törlési műveletek figyelése jelenleg nem támogatott. Megkerülő megoldásként hozzáadhat egy lágy jelölőt a törölt elemekhez. Hozzáadhat például egy attribútumot a "törölt" nevű elemhez, és beállíthatja "igaz" értékre, és beállíthatja az elem ÉLETTARTAMát, így automatikusan törölheti azt, és nyomon követheti is.

```javascript
var cursor = db.coll.watch(
    [
        { $match: { "operationType": { $in: ["insert", "update", "replace"] } } },
        { $project: { "_id": 1, "fullDocument": 1, "ns": 1, "documentKey": 1 } }
    ],
    { fullDocument: "updateLookup" });

while (!cursor.isExhausted()) {
    if (cursor.hasNext()) {
        printjson(cursor.next());
    }
}
```

Az alábbi példa azt szemlélteti, hogyan lehet módosítani az elemeket egyetlen szegmensben. Ez a példa a "a" és a "1" értékkel egyenlő, a szilánkok kulcsának értékét tartalmazó elemek változásait olvassa be.

```javascript
var cursor = db.coll.watch(
    [
        { 
            $match: { 
                $and: [
                    { "fullDocument.a": 1 }, 
                    { "operationType": { $in: ["insert", "update", "replace"] } }
                ]
            }
        },
        { $project: { "_id": 1, "fullDocument": 1, "ns": 1, "documentKey": 1} }
    ],
    { fullDocument: "updateLookup" });

```

## <a name="current-limitations"></a>Aktuális korlátozások

A Change streamek használatakor a következő korlátozások érvényesek:

* A `operationType` és `updateDescription` tulajdonságok még nem támogatottak a kimeneti dokumentumban.
* A `insert`, `update`és `replace` műveleti típusok jelenleg támogatottak. A törlési művelet vagy más esemény még nem támogatott.

A korlátozások miatt a $match szakasz, a $project szakasz és a fullDocument lehetőségek szükségesek, ahogy az előző példákban is látható.

## <a name="error-handling"></a>Hibakezelés

A Change streamek használatakor a következő hibakódok és üzenetek támogatottak:

* **Http-hibakód 429** – ha a Change stream szabályozása megtörtént, üres lapot ad vissza.

* **NamespaceNotFound (OperationType-érvénytelenítés)** – ha a módosítási adatfolyamot nem létező gyűjteményen futtatja, vagy ha a gyűjtemény el van dobva, akkor a rendszer `NamespaceNotFound` hibát ad vissza. Mivel a `operationType` tulajdonságot nem lehet visszaadni a kimeneti dokumentumban a `operationType Invalidate` hiba helyett, a rendszer a `NamespaceNotFound` hibát adja vissza.

## <a name="next-steps"></a>Következő lépések

* [A MongoDB Azure Cosmos DB API-ban való használatának ideje az élettartam lejáratára](mongodb-time-to-live.md)
* [Indexelés Azure Cosmos DB API-MongoDB](mongodb-indexing.md)