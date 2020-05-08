---
title: Hírcsatorna-lekérési modell módosítása
description: Megtudhatja, hogyan használhatja a Azure Cosmos DB Change feed lekérési modellt a változási csatorna és a lekéréses modell és a hírcsatorna-feldolgozó közötti különbségek olvasásához
author: timsander1
ms.author: tisande
ms.service: cosmos-db
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 05/06/2020
ms.reviewer: sngun
ms.openlocfilehash: 2854e3d92462ced3958afd1cf1e7e99d7e9892f6
ms.sourcegitcommit: 999ccaf74347605e32505cbcfd6121163560a4ae
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/08/2020
ms.locfileid: "82984680"
---
# <a name="change-feed-pull-model-in-azure-cosmos-db"></a>A hírcsatorna lekérési modelljének módosítása Azure Cosmos DB

Ha a Change feed pull modellt használja, a saját tempójában használhatja a Azure Cosmos DB változási csatornát. Ahogy az [adatváltozási folyamattal](change-feed-processor.md)már megteheti a változást, a hírcsatorna-lekérési modell módosításával integrálással a változások feldolgozását több módosítási csatornán keresztül is.

> [!NOTE]
> A hírcsatorna-váltási lekérési modell jelenleg csak előzetes verzióban érhető [el a Azure Cosmos db .net SDK](https://www.nuget.org/packages/Microsoft.Azure.Cosmos/3.9.0-preview) -ban. Az előzetes verzió még nem érhető el más SDK-verziókhoz.

## <a name="consuming-an-entire-containers-changes"></a>Teljes tároló módosításainak felhasználása

Létrehozhat egy `FeedIterator` t a módosítási csatorna feldolgozásához a lekéréses modell használatával. Amikor először létrehoz egy `FeedIterator`-t, megadhat egy opcionálisat `StartTime` a `ChangeFeedRequestOptions`on belül. Ha nem ad meg értéket, `StartTime` a lesz az aktuális időpont.

A `FeedIterator` két ízeket tartalmaz. Az entitás-objektumokat visszaadó lenti példákon kívül a `Stream` támogatással is kérheti a választ. A streamek lehetővé teszik az adatolvasást anélkül, hogy először deszerializált, az ügyfél erőforrásain mentve.

Az alábbi példa egy olyan objektum beszerzését szemlélteti, `FeedIterator` amely entitás-objektumokat ad vissza `User` , ebben az esetben egy objektumot:

```csharp
FeedIterator<User> iteratorWithPOCOS = container.GetChangeFeedIterator<User>();
```

Az alábbi példa egy `FeedIterator` , a értéket visszaadó beszerzését szemlélteti: `Stream`

```csharp
FeedIterator iteratorWithStreams = container.GetChangeFeedStreamIterator();
```

A `FeedIterator`használatával könnyedén feldolgozhatja a teljes tároló változási csatornáját a saját tempójában. Például:

```csharp
FeedIterator<User> iteratorForTheEntireContainer= container.GetChangeFeedIterator(new ChangeFeedRequestOptions{StartTime = DateTime.MinValue});

while (iteratorForTheEntireContainer.HasMoreResults)
{
   FeedResponse<User> users = await iteratorForTheEntireContainer.ReadNextAsync();

   foreach (User user in users)
    {
        Console.WriteLine($"Detected change for user with id {user.id}");
    }
}
```

## <a name="consuming-a-partition-keys-changes"></a>Partíciós kulcs módosításainak felhasználása

Bizonyos esetekben előfordulhat, hogy csak egy adott partíciós kulcs módosításait szeretné feldolgozni. Beszerezhet egy `FeedIterator` adott partíciós kulcsot, és feldolgozhatja a módosításokat ugyanúgy, mint a teljes tárolóban:

```csharp
FeedIterator<User> iteratorForThePartitionKey = container.GetChangeFeedIterator(new PartitionKey("myPartitionKeyValueToRead"), new ChangeFeedRequestOptions{StartTime = DateTime.MinValue});

while (iteratorForThePartitionKey.HasMoreResults)
{
   FeedResponse<User> users = await iteratorForThePartitionKey.ReadNextAsync();

   foreach (User user in users)
    {
        Console.WriteLine($"Detected change for user with id {user.id}");
    }
}
```

## <a name="using-feedrange-for-parallelization"></a>A FeedRange használata a párhuzamos

A [change feed processzorban](change-feed-processor.md)a munka automatikusan több felhasználó között oszlik meg. A Change feed lekérési modellben `FeedRange` a használatával integrálással a módosítási csatorna feldolgozását. A `FeedRange` a partíciós kulcs értékeinek egy tartományát jelöli.

Az alábbi példa bemutatja, hogyan kérhető le a tárolóhoz tartozó tartományok listája:

```csharp
IReadOnlyList<FeedRange> ranges = await container.GetFeedRangesAsync();
```

A tárolóhoz tartozó FeedRanges listájának beszerzése után a `FeedRange` [fizikai partíciók](partition-data.md#physical-partitions)közül egyet kell kapnia.

`FeedRange`A használatával létrehozhatja `FeedIterator` a integrálással a változási csatorna több gépen vagy szálon történő feldolgozását. Az előző példától eltérően, amely megmutatta, `FeedIterator` hogyan szerezhet be egyetlent a teljes tárolóhoz `FeedRange` , a használatával több FeedIterators is beszerezhet, amely párhuzamosan feldolgozhatja a változási csatornát.

Abban az esetben, ha a FeedRanges-t szeretné használni, rendelkeznie kell egy Orchestrator-folyamattal, amely beolvassa a FeedRanges, és elosztja azokat a gépeken. A terjesztés a következő lehet:

* A `FeedRange.ToJsonString` karakterlánc értékének használata és terjesztése. A felhasználók használhatják ezt az értéket a következővel`FeedRange.FromJsonString`
* Ha a terjesztés folyamatban van, a rendszer átadja `FeedRange` az objektum hivatkozását.

Íme egy példa, amely bemutatja, hogyan olvashatja el a tároló változási csatornájának elejét két feltételezett különálló, párhuzamosan olvasott gép használatával:

1. gép:

```csharp
FeedIterator<User> iteratorA = container.GetChangeFeedIterator<Person>(ranges[0], new ChangeFeedRequestOptions{StartTime = DateTime.MinValue});
while (iteratorA.HasMoreResults)
{
   FeedResponse<User> users = await iteratorA.ReadNextAsync();

   foreach (User user in users)
    {
        Console.WriteLine($"Detected change for user with id {user.id}");
    }
}
```

2. gép:

```csharp
FeedIterator<User> iteratorB = container.GetChangeFeedIterator<User>(ranges[1], new ChangeFeedRequestOptions{StartTime = DateTime.MinValue});
while (iteratorB.HasMoreResults)
{
   FeedResponse<User> users = await iteratorB.ReadNextAsync();

   foreach (User user in users)
    {
        Console.WriteLine($"Detected change for user with id {user.id}");
    }
}
```

## <a name="saving-continuation-tokens"></a>Folytatási tokenek mentése

Egy folytatási token létrehozásával mentheti `FeedIterator` a pozícióját. A folytatási token olyan karakterlánc-érték, amely nyomon követi a FeedIterator legutóbbi feldolgozott módosításait. Ez lehetővé teszi `FeedIterator` , hogy később folytassa a folytatást. A következő kód beolvassa a változási csatornát a tároló létrehozása óta. Ha nem áll rendelkezésre több módosítás, a rendszer továbbra is megőrzi a folytatási jogkivonatot, így később folytathatja a módosítást.

```csharp
FeedIterator<User> iterator = container.GetChangeFeedIterator<User>(ranges[0], new ChangeFeedRequestOptions{StartTime = DateTime.MinValue});

string continuation = null;

while (iterator.HasMoreResults)
{
   FeedResponse<User> users = await iterator.ReadNextAsync();
   continuation = orders.ContinuationToken;

   foreach (User user in Users)
    {
        Console.WriteLine($"Detected change for user with id {user.id}");
    }
}

// Some time later
FeedIterator<User> iteratorThatResumesFromLastPoint = container.GetChangeFeedIterator<User>(continuation);
```

## <a name="comparing-with-change-feed-processor"></a>Összehasonlítás a csatorna változása folyamattal

Számos forgatókönyv képes feldolgozni a változási csatornát a [változási csatorna](change-feed-processor.md) vagy a lekéréses modell használatával. A lekéréses modell folytatási jogkivonatai és a változási hírcsatorna-feldolgozó címbérleti tárolója "Könyvjelzők" is a legutóbbi feldolgozott elemhez (vagy köteghez) a változási hírcsatornában.
A folytatási tokeneket azonban nem lehet címbérleti tárolóra konvertálni (vagy fordítva).

Az alábbi helyzetekben érdemes megfontolni a lekéréses modell használatát:

- A változási hírcsatornában lévő meglévő információk egyszeri olvasását szeretné elvégezni
- Csak egy adott partíciós kulcs módosításait szeretné beolvasni
- Nem szeretne leküldéses modellt használni, és a módosítási csatornát saját tempójában szeretné felhasználni

Íme néhány lényeges különbség a változási hírcsatorna-feldolgozó és a lekérési modell között:

|  | Változáscsatorna feldolgozója| Lekéréses modell |
| --- | --- | --- |
| A változási hírcsatorna aktuális pontjának nyomon követése | Bérlet (Azure Cosmos DB tárolóban tárolva) | Folytatási token (memóriában tárolt vagy manuálisan megőrzött) |
| A korábbi módosítások visszajátszásának lehetősége | Igen, leküldéses modellel | Igen, lekéréses modellel|
| A jövőbeli változások lekérdezése | Automatikusan ellenőrzi a módosításokat a felhasználó által megadott érték alapján`WithPollInterval` | Kézi |
| A teljes tároló változásainak feldolgozása | Igen, és automatikusan párhuzamosan, több szálon/gépen, ugyanarról a tárolóból| Igen, és manuálisan párhuzamosan a FeedTokens használatával |
| Csak egyetlen partíciós kulcs változásainak feldolgozása | Nem támogatott | Igen|
| Támogatási szint | Általánosan elérhető | Előzetes verzió |

## <a name="next-steps"></a>További lépések

* [A hírcsatorna változásának áttekintése](change-feed.md)
* [Az adatcsatorna módosításának használata](change-feed-processor.md)
* [Az Azure Functions aktiválása](change-feed-functions.md)