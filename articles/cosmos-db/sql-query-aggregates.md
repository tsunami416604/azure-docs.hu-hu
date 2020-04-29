---
title: Összesítő függvények a Azure Cosmos DBban
description: Ismerje meg az SQL összesítő függvények szintaxisát, a Azure Cosmos DB által támogatott összesítő függvények típusait.
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/16/2020
ms.author: tisande
ms.openlocfilehash: 24acd1e9c13320244ff4c27abd13abeda6f70b2b
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "79464461"
---
# <a name="aggregate-functions-in-azure-cosmos-db"></a>Összesítő függvények a Azure Cosmos DBban

Az `SELECT` összesítő függvények számítást végeznek a záradékban található értékek halmazán, és egyetlen értéket adnak vissza. A következő lekérdezés például a `Families` tárolóban lévő elemek számát adja vissza:

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

Az összesítéseket szűrők használatával is egyesítheti. Például a következő lekérdezés visszaadja a címe állapotú elemek számát `WA`.

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

Az SQL API a következő összesítő függvényeket támogatja. `SUM`a `AVG` és a függvény numerikus értékekkel `COUNT`, `MIN`és, `MAX` és számok, karakterláncok, logikai értékek és nullák alapján működik.

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

## <a name="remarks"></a>Megjegyzések

Ezek az összesített rendszerfunkciók kihasználják a [tartomány indexét](index-policy.md#includeexclude-strategy). Ha a (z), `COUNT`, `SUM`, `MIN`vagy `MAX` `AVG` tulajdonságot szeretné elvégezni, a [megfelelő elérési utat is meg kell adni az indexelési házirendben](index-policy.md#includeexclude-strategy).

## <a name="next-steps"></a>További lépések

- [Bevezetés a Azure Cosmos DBba](introduction.md)
- [Rendszerfüggvények](sql-query-system-functions.md)
- [Felhasználó által definiált függvények](sql-query-udfs.md)