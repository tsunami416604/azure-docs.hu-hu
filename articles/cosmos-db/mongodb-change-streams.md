---
title: Adatfolyamok módosítása az Azure Cosmos DB MongoDB-hoz való API-jában
description: Ismerje meg, hogyan használhatja a változásfolyamokat az Azure Cosmos DB MongoDB API-jában az adatok módosításai beszerezéséhez.
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: conceptual
ms.date: 03/30/2020
ms.author: tisande
ms.openlocfilehash: ecfa98241f74aac43a827b645a6ed877624d643d
ms.sourcegitcommit: ced98c83ed25ad2062cc95bab3a666b99b92db58
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/31/2020
ms.locfileid: "80437811"
---
# <a name="change-streams-in-azure-cosmos-dbs-api-for-mongodb"></a>Adatfolyamok módosítása az Azure Cosmos DB MongoDB-hoz való API-jában

[Módosítsa a hírcsatorna-támogatást](change-feed.md) az Azure Cosmos DB MongoDB API-jában a változásfolyamok API használatával érhető el. A változásstreamek API használatával az alkalmazások lejuthatnak a gyűjteményben vagy az elemekegyetlen szegmensben végrehajtott módosításai. Később további műveleteket is végrehajthat az eredmények alapján. A gyűjteményelemeinek módosításai a módosítási idő sorrendjében kerülnek rögzítésre, és a rendezési sorrend shard kulcsonként garantált.

> [!NOTE]
> A változásfolyamok használatához hozza létre a fiókot az Azure Cosmos DB MongoDB API-jának 3.6-os verziójával vagy egy újabb verzióval. Ha a változásfolyam-példákat egy korábbi verzióhoz `Unrecognized pipeline stage name: $changeStream` futtatja, előfordulhat, hogy megjelenik a hiba.

## <a name="current-limitations"></a>Aktuális korlátozások

A következő korlátozások vonatkoznak a változási adatfolyamok használatára:

* A `operationType` `updateDescription` és a tulajdonságok még nem támogatottak a kimeneti dokumentumban.
* A `insert` `update`, `replace` és a műveletek típusajelenleg támogatott. A törlési művelet vagy más események még nem támogatottak.

Ezek a korlátozások miatt a $match szakasz, $project szakasz és a fullDocument beállítások szükségesek, ahogy az előző példákban látható.

Az Azure Cosmos DB SQL API-jának módosítási hírcsatornájával ellentétben nincs külön [változáscsatorna-feldolgozókód-kód,](change-feed-processor.md) amely a változásfolyamok felhasználásához vagy egy bérlettárolót igényelne. Jelenleg nem támogatja az [Azure Functions eseményindítók](change-feed-functions.md) a változásstreamek feldolgozásához.

## <a name="error-handling"></a>Hibakezelés

A következő hibakódok és üzenetek támogatottak a változási adatfolyamok használatakor:

* **HTTP-hibakód 16500** - Ha a változásstream szabályozása van, üres lapot ad vissza.

* **NamespaceNotFound (OperationType Invalidate)** - Ha nem létező változásfolyamot futtat a gyűjteményen, `NamespaceNotFound` vagy ha a gyűjteményt eldobják, akkor a rendszer hibaüzenetet ad vissza. Mivel `operationType` a tulajdonság nem adható vissza a kimeneti `operationType Invalidate` dokumentumban, a hiba helyett a `NamespaceNotFound` hiba ad vissza.

## <a name="examples"></a>Példák

A következő példa bemutatja, hogyan lehet a gyűjtemény összes elemére módosítási adatfolyamokat beszerezni. Ez a példa egy kurzort hoz létre az elemek megtekintéséhez, amikor beszúrják, frissítik vagy kicserélik őket. A `$match` fázis, `$project` a `fullDocument` szakasz és a beállítás szükséges a változásfolyamok leválasztásához. A módosítási adatfolyamok használatával végzett törlési műveletek figyelése jelenleg nem támogatott. Kerülő megoldásként lágy jelölőt adhat a törölt elemekhez. Hozzáadhat például egy attribútumot a "törölt" elemhez. Ha törölni szeretné az elemet, beállíthatja a "törölt" szót, `true` és beállíthat egy TTL-t az elemen. Mivel a "törölt" `true` frissítés frissítésegy frissítés, ez a változás látható lesz a változásfolyamban.

### <a name="javascript"></a>Javascript:

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

### <a name="c"></a>C#:

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

## <a name="changes-within-a-single-shard"></a>Változások egyetlen szegmensen belül

A következő példa bemutatja, hogyan lehet az elemek módosításait egyetlen szegmensen belül. Ebben a példában leteszi az elemek módosításait, amelyek shard kulcs egyenlő "a" és a szegmens kulcs értéke egyenlő "1". Lehetőség van arra, hogy a különböző ügyfelek olvasási változások a különböző szegmensek párhuzamosan.

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

* [Az élő idő használata az adatok automatikus lejáratához az Azure Cosmos DB MongoDB-hoz való API-jában](mongodb-time-to-live.md)
* [Indexelés az Azure Cosmos DB MongoDB-hoz elérhető API-jában](mongodb-indexing.md)
