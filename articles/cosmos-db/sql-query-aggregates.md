---
title: Összesítő függvények a Azure Cosmos DBban
description: Ismerje meg az SQL összesítő függvények szintaxisát, a Azure Cosmos DB által támogatott összesítő függvények típusait.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/02/2019
ms.author: mjbrown
ms.openlocfilehash: 1ce3b18dd31944a1a4d4e6fad8fb49e63996dace
ms.sourcegitcommit: 9405aad7e39efbd8fef6d0a3c8988c6bf8de94eb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/05/2019
ms.locfileid: "74871839"
---
# <a name="aggregate-functions-in-azure-cosmos-db"></a>Összesítő függvények a Azure Cosmos DBban

Az összesítő függvények számítást végeznek a SELECT záradékban lévő értékek halmazán, és egyetlen értéket adnak vissza. A következő lekérdezés például a `Families` tárolóban lévő elemek számát adja vissza:

## <a name="examples"></a>Példák

```sql
    SELECT COUNT(1)
    FROM Families f
```

Az eredmény a következő:

```json
    [{
        "$1": 2
    }]
```

A VALUE kulcsszó használatával csak az Összesítés skaláris értékét adhatja vissza. Például a következő lekérdezés az értékek számát adja vissza egyetlen számként:

```sql
    SELECT VALUE COUNT(1)
    FROM Families f
```

Az eredmény a következő:

```json
    [ 2 ]
```

Az összesítéseket szűrők használatával is egyesítheti. Például a következő lekérdezés visszaadja az elemek számát `WA`címe állapotával.

```sql
    SELECT VALUE COUNT(1)
    FROM Families f
    WHERE f.address.state = "WA"
```

Az eredmény a következő:

```json
    [ 1 ]
```

## <a name="types-of-aggregate-functions"></a>Az összesítő függvények típusai

Az SQL API a következő összesítő függvényeket támogatja. A SUM és az AVG függvény a numerikus értékeken, valamint a COUNT, a MIN és a MAX függvény számokat, karakterláncokat, logikai értékeket és null értékeket használ.

| Függvény | Leírás |
|-------|-------------|
| COUNT | A kifejezésben szereplő elemek számát adja vissza. |
| SUM   | A kifejezésben szereplő értékek összegét adja vissza. |
| MIN   | A kifejezésben szereplő minimális értéket adja vissza. |
| MAX   | A kifejezésben szereplő maximális értéket adja vissza. |
| AVG   | A kifejezésben szereplő értékek átlagát adja vissza. |

A tömb iterációjának eredményét is összesítheti.

> [!NOTE]
> A Azure Portal Adatkezelő az összesítési lekérdezések részleges eredményeket összesítenek csak egy lekérdezési oldalon. Az SDK egyetlen összesített értéket hoz létre az összes oldalon. Az összesítési lekérdezések kóddal való végrehajtásához .NET SDK 1.12.0, .NET Core SDK 1.1.0 vagy Java SDK 1.9.5 vagy újabb verzió szükséges.

## <a name="next-steps"></a>Következő lépések

- [Bevezetés a Azure Cosmos DBba](introduction.md)
- [Rendszerfunkciók](sql-query-system-functions.md)
- [Felhasználó által definiált függvények](sql-query-udfs.md)