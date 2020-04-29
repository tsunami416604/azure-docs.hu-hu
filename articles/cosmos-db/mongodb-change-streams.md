---
title: Adatfolyamok módosítása Azure Cosmos DB API-MongoDB
description: Megtudhatja, hogyan használhatja az adatváltozásokat az Azure Cosmos DB API-MongoDB az adatváltozások beszerzéséhez.
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: conceptual
ms.date: 03/30/2020
ms.author: tisande
ms.openlocfilehash: 38e262abefe5444c1fe7586810f4b971cc7baf6c
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "81114149"
---
# <a name="change-streams-in-azure-cosmos-dbs-api-for-mongodb"></a>Adatfolyamok módosítása Azure Cosmos DB API-MongoDB

A Azure Cosmos DB API-MongoDB való [adatcsatorna](change-feed.md) -támogatás módosítása a Streams API-t használva érhető el. Az adatfolyamok módosítása API használatával az alkalmazások beszerezhetik a gyűjteményen vagy az egyetlen szegmensben lévő elemeken végrehajtott módosításokat. Később további műveleteket is végrehajthat az eredmények alapján. A gyűjtemény elemeinek módosításait a rendszer a módosítási idő sorrendjében rögzíti, és a rendezési sorrendet a rendszer a szegmens kulcsa szerint biztosítja.

> [!NOTE]
> Az adatfolyamok módosításának használatához hozza létre a fiókot a Azure Cosmos DB API-MongoDB vagy egy újabb verziójának 3,6-es verziójával. Ha az adatfolyam módosítása példát egy korábbi verzióra futtatja, akkor előfordulhat, hogy `Unrecognized pipeline stage name: $changeStream` a hibaüzenet jelenik meg.

## <a name="current-limitations"></a>Aktuális korlátozások

A Change streamek használatakor a következő korlátozások érvényesek:

* A `operationType` és `updateDescription` a tulajdonságok még nem támogatottak a kimeneti dokumentumban.
* A `insert`, `update`a és `replace` az Operations típusok jelenleg támogatottak. 
* A törlési művelet vagy más esemény még nem támogatott.

A korlátozások miatt a $match szakasz, a $project szakasz és a fullDocument lehetőségek szükségesek, ahogy az előző példákban is látható.

A Azure Cosmos DB SQL API-ban megjelenő változási csatornától eltérően a változási adatfolyamok nem használhatók fel külön [módosítási adatcsatornára](change-feed-processor.md) , vagy a bérletek tárolóra van szükségük. Jelenleg nem támogatott a [Azure functions eseményindítók](change-feed-functions.md) feldolgozása a változási adatfolyamok feldolgozásához.

## <a name="error-handling"></a>Hibakezelés

A Change streamek használatakor a következő hibakódok és üzenetek támogatottak:

* **Http-hibakód 16500** – ha a Change stream szabályozása megtörtént, üres lapot ad vissza.

* **NamespaceNotFound (OperationType-érvénytelenítés)** – ha nem létező gyűjteményen futtatja a Change streamet, vagy ha a gyűjtemény el van dobva, a `NamespaceNotFound` rendszer hibát ad vissza. Mivel a `operationType` tulajdonság nem adható vissza a kimeneti dokumentumban a `operationType Invalidate` hiba helyett, a rendszer a `NamespaceNotFound` hibát adja vissza.

## <a name="examples"></a>Példák

Az alábbi példa azt szemlélteti, hogyan lehet módosítani a gyűjtemény összes elemének változásait. Ez a példa létrehoz egy kurzort, amellyel megtekintheti a beszúrt, frissített vagy lecserélt elemeket. A `$match` változtatási `$project` adatfolyamok beszerzéséhez a szakasz, a szakasz és `fullDocument` a beállítás szükséges. A módosítási streameket használó törlési műveletek figyelése jelenleg nem támogatott. Megkerülő megoldásként hozzáadhat egy lágy jelölőt a törölt elemekhez. Hozzáadhat például egy attribútumot a "törölt" nevű elemhez. Ha törölni szeretné az elemeket, beállíthatja a "törölt" értéket, `true` és beállíthatja az élettartamot az elemnél. Mivel a "törölt" érték `true` frissítése frissül, ez a változás látható lesz a változási adatfolyamban.

### <a name="javascript"></a>JavaScript

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

### <a name="c"></a>C#

```csharp
var pipeline = new EmptyPipelineDefinition<ChangeStreamDocument<BsonDocument>>()
    .Match(change => change.OperationType == ChangeStreamOperationType.Insert || change.OperationType == ChangeStreamOperationType.Update || change.OperationType == ChangeStreamOperationType.Replace)
    .AppendStage<ChangeStreamDocument<BsonDocument>, ChangeStreamDocument<BsonDocument>, BsonDocument>(
    "{ $project: { '_id': 1, 'fullDocument': 1, 'ns': 1, 'documentKey': 1 }}");

var options = new ChangeStreamOptions{
        FullDocument = ChangeStreamFullDocumentOption.UpdateLookup
    };

var enumerator = coll.Watch(pipeline, options).ToEnumerable().GetEnumerator();

while (enumerator.MoveNext()){
        Console.WriteLine(enumerator.Current);
    }

enumerator.Dispose();
```

## <a name="changes-within-a-single-shard"></a>Egyetlen szegmensen belüli változások

Az alábbi példa azt szemlélteti, hogyan lehet módosítani az elemeket egyetlen szegmensen belül. Ez a példa a "a" és a "1" értékkel egyenlő, a szilánkok kulcsának értékét tartalmazó elemek változásait olvassa be. Lehetséges, hogy a különböző ügyfelek különböző szegmensekről származó módosításokat olvasnak párhuzamosan.

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

## <a name="next-steps"></a>További lépések

* [A MongoDB Azure Cosmos DB API-ban való használatának ideje az élettartam lejáratára](mongodb-time-to-live.md)
* [Indexelés Azure Cosmos DB API-MongoDB](mongodb-indexing.md)
