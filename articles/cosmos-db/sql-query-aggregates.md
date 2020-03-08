---
title: Összesítő függvények a Azure Cosmos DBban
description: Ismerje meg az SQL összesítő függvények szintaxisát, a Azure Cosmos DB által támogatott összesítő függvények típusait.
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/05/2020
ms.author: tisande
ms.openlocfilehash: df9700dd51c8915ff28c34cf0a29c2f5e48baa44
ms.sourcegitcommit: 668b3480cb637c53534642adcee95d687578769a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/07/2020
ms.locfileid: "78897829"
---
# <a name="aggregate-functions-in-azure-cosmos-db"></a>Összesítő függvények a Azure Cosmos DBban

Az összesítő függvények számítást végeznek a SELECT záradékban lévő értékek halmazán, és egyetlen értéket adnak vissza. A következő lekérdezés például a `Families` tárolóban lévő elemek számát adja vissza:

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

A VALUE kulcsszó használatával csak az Összesítés skaláris értékét adhatja vissza. Például a következő lekérdezést egy egyetlen számot ad vissza értékek száma:

```sql
    SELECT VALUE COUNT(1)
    FROM Families f
```

Az eredmények a következők:

```json
    [ 2 ]
```

Az összesítéseket szűrők használatával is egyesítheti. Például a következő lekérdezés visszaadja az elemek számát `WA`címe állapotával.

```sql
    SELECT VALUE COUNT(1)
    FROM Families f
    WHERE f.address.state = "WA"
```

Az eredmények a következők:

```json
    [ 1 ]
```

## <a name="types-of-aggregate-functions"></a>Az összesítő függvények típusai

Az SQL API a következő összesítő függvényeket támogatja. A SUM és az AVG függvény a numerikus értékeken, valamint a COUNT, a MIN és a MAX függvény számokat, karakterláncokat, logikai értékeket és null értékeket használ.

| Függvény | Leírás |
|-------|-------------|
| COUNT | A kifejezésben található elemek számát adja vissza. |
| SUM   | A kifejezésben található értékek összegét adja vissza. |
| MIN   | A kifejezés minimumértékét adja vissza. |
| MAX   | A kifejezés maximumértékét adja vissza. |
| AVG   | A kifejezésben található értékek átlagát adja vissza. |

A tömb iterációjának eredményét is összesítheti.

> [!NOTE]
> A Azure Portal Adatkezelő az összesítési lekérdezések részleges eredményeket összesítenek csak egy lekérdezési oldalon. Az SDK egyetlen összesített értéket hoz létre az összes oldalon. Az összesítési lekérdezések kóddal való végrehajtásához .NET SDK 1.12.0, .NET Core SDK 1.1.0 vagy Java SDK 1.9.5 vagy újabb verzió szükséges.

## <a name="next-steps"></a>További lépések

- [Bevezetés a Azure Cosmos DBba](introduction.md)
- [Rendszerfunkciók](sql-query-system-functions.md)
- [Felhasználó által definiált függvények](sql-query-udfs.md)