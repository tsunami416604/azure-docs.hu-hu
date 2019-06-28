---
title: Az Azure Cosmos DB az aggregátumfüggvények
description: Az Azure Cosmos DB ismerje meg az SQL összesítő függvény szintaxisát.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/31/2019
ms.author: mjbrown
ms.openlocfilehash: a6937e9e811ea8e44eda6f2bcb5d2c7d78db4934
ms.sourcegitcommit: a12b2c2599134e32a910921861d4805e21320159
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/24/2019
ms.locfileid: "67342759"
---
# <a name="aggregate-functions-in-azure-cosmos-db"></a>Az Azure Cosmos DB az aggregátumfüggvények

Aggregátumfüggvények számítás elvégzése a SELECT záradékban szereplő értékek egy halmazát, és egyetlen értéket ad vissza. Például a következő lekérdezés belül elemek számát adja meg a `Families` tároló:

## <a name="examples"></a>Példák

```sql
    SELECT COUNT(1)
    FROM Families f
```

Az eredmények a következők:

```json
    [{
        "$1": 2
    }]
```

Csak a skaláris érték az összesítést is visszaadható érték kulcsszó használatával. Például a következő lekérdezést egy egyetlen számot ad vissza értékek száma:

```sql
    SELECT VALUE COUNT(1)
    FROM Families f
```

Az eredmények a következők:

```json
    [ 2 ]
```

Összesítések szűrőket is kombinálhatók. Például a következő lekérdezést a cím állapotú elemek számát küldi vissza `WA`.

```sql
    SELECT VALUE COUNT(1)
    FROM Families f
    WHERE f.address.state = "WA"
```

Az eredmények a következők:

```json
    [ 1 ]
```

## <a name="types-of-aggregate-functions"></a>Az aggregátumfüggvények típusai

Az SQL API támogatja a következő összesítő függvények. ÖSSZEG és az átlagos numerikus értékeken működnek, és száma, minimális és maximális dolgozhat számok, karakterláncok, logikai és null értékeket.

| Függvény | Leírás |
|-------|-------------|
| COUNT | A kifejezésben található elemek számát adja vissza. |
| SUM   | A kifejezésben található értékek összegét adja vissza. |
| MIN   | A kifejezés minimumértékét adja vissza. |
| MAX   | A kifejezés maximumértékét adja vissza. |
| AVG   | A kifejezésben található értékek átlagát adja vissza. |

Egy tömb iteráció eredményét feletti is lehet összesíteni.

> [!NOTE]
> Az Azure portal adatkezelőjében összesítési lekérdezések csak egy lekérdezést lap alatt előfordulhat, hogy összesítés részleges eredményeket. Az SDK összes oldalain egyetlen összesített értéket állít elő. Kód használatával összesítési lekérdezések végrehajtásához, a .NET SDK-val 1.12.0, .NET Core SDK 1.1.0-ás vagy a Java SDK 1.9.5 szüksége vagy újabb.

## <a name="next-steps"></a>További lépések

- [Az Azure Cosmos DB bemutatása](introduction.md)
- [Rendszer-funkciók](sql-query-system-functions.md)
- [Felhasználó által megadott függvények](sql-query-udfs.md)