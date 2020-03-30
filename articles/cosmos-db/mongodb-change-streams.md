---
title: Adatfolyamok módosítása az Azure Cosmos DB MongoDB-hoz való API-jában
description: Ismerje meg, hogyan használhatja a változásfolyamok n Az Azure Cosmos DB API-t A MongoDB az adatok módosításait.
author: srchi
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: conceptual
ms.date: 11/16/2019
ms.author: srchi
ms.openlocfilehash: ec1ec1a8a80953f8988355341ee7128bd29b982d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77467777"
---
# <a name="change-streams-in-azure-cosmos-dbs-api-for-mongodb"></a>Adatfolyamok módosítása az Azure Cosmos DB MongoDB-hoz való API-jában

[Módosítsa a hírcsatorna-támogatást](change-feed.md) az Azure Cosmos DB MongoDB API-jában a változásfolyamok API használatával érhető el. A változásstreamek API használatával az alkalmazások lejuthatnak a gyűjteményben vagy az elemekegyetlen szegmensben végrehajtott módosításai. Később további műveleteket is végrehajthat az eredmények alapján. A gyűjteményelemeinek módosításai a módosítási idő sorrendjében kerülnek rögzítésre, és a rendezési sorrend shard kulcsonként garantált.

> [!NOTE]
> A változásfolyamok használatához hozza létre a fiókot az Azure Cosmos DB MongoDB API-jának 3.6-os verziójával vagy egy újabb verzióval. Ha a változásfolyam-példákat egy korábbi verzióhoz `Unrecognized pipeline stage name: $changeStream` futtatja, előfordulhat, hogy megjelenik a hiba. 

A következő példa bemutatja, hogyan lehet a gyűjtemény összes elemére módosítási adatfolyamokat beszerezni. Ez a példa egy kurzort hoz létre az elemek megtekintéséhez, amikor beszúrják, frissítik vagy kicserélik őket. A $match szakasz, $project szakasz és fullDocument beállítás szükséges a változásfolyamok leválasztásához. A módosítási adatfolyamok használatával végzett törlési műveletek figyelése jelenleg nem támogatott. Kerülő megoldásként lágy jelölőt adhat a törölt elemekhez. Hozzáadhat például egy attribútumot a "törölt" nevű elemhez, és beállíthatja azt "true" értékre, és beállíthat egy TTL-t az elemen, így automatikusan törölheti és nyomon követheti.

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

A következő példa bemutatja, hogyan lehet az elemek módosításait egyetlen szegmensben beszerezni. Ebben a példában leteszi az elemek módosításait, amelyek shard kulcs egyenlő "a" és a szegmens kulcs értéke egyenlő "1".

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

A következő korlátozások vonatkoznak a változási adatfolyamok használatára:

* A `operationType` `updateDescription` és a tulajdonságok még nem támogatottak a kimeneti dokumentumban.
* A `insert` `update`, `replace` és a műveletek típusajelenleg támogatott. A törlési művelet vagy más események még nem támogatottak.

Ezek a korlátozások miatt a $match szakasz, $project szakasz és a fullDocument beállítások szükségesek, ahogy az előző példákban látható.

## <a name="error-handling"></a>Hibakezelés

A következő hibakódok és üzenetek támogatottak a változási adatfolyamok használatakor:

* **HTTP-hibakód 429** - Ha a változásstream szabályozott, üres lapot ad vissza.

* **NamespaceNotFound (OperationType Invalidate)** - Ha nem létező változásfolyamot futtat a gyűjteményen, `NamespaceNotFound` vagy ha a gyűjteményt eldobják, akkor a rendszer hibaüzenetet ad vissza. Mivel `operationType` a tulajdonság nem adható vissza a kimeneti `operationType Invalidate` dokumentumban, a hiba helyett a `NamespaceNotFound` hiba ad vissza.

## <a name="next-steps"></a>További lépések

* [Az élő idő használata az adatok automatikus lejáratához az Azure Cosmos DB MongoDB-hoz való API-jában](mongodb-time-to-live.md)
* [Indexelés az Azure Cosmos DB MongoDB-hoz elérhető API-jában](mongodb-indexing.md)
