---
title: Összesítő függvények az Azure Cosmos DB-ben
description: Ismerje meg az SQL összesítő függvény szintaxisát, az Azure Cosmos DB által támogatott összesítő függvények típusait.
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/16/2020
ms.author: tisande
ms.openlocfilehash: 24acd1e9c13320244ff4c27abd13abeda6f70b2b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79464461"
---
# <a name="aggregate-functions-in-azure-cosmos-db"></a>Összesítő függvények az Azure Cosmos DB-ben

Az összesítő függvények számítást hajtanak végre a `SELECT` záradék ban szereplő értékhalmazon, és egyetlen értéket adnak vissza. A következő lekérdezés például a `Families` tárolón belüli elemek számát adja vissza:

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

Az érték kulcsszó használatával csak az összesítés skaláris értékét is visszaadhatja. A következő lekérdezés például egyetlen számként adja vissza az értékek számát:

```sql
    SELECT VALUE COUNT(1)
    FROM Families f
```

Az eredmény a következő:

```json
    [ 2 ]
```

Az összesítéseket szűrőkkel is kombinálhatja. A következő lekérdezés például a címállapotával rendelkező `WA`elemek számát adja vissza.

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

Az SQL API a következő összesítő függvényeket támogatja. `SUM`és `AVG` numerikus értékeken `COUNT` `MIN`működik, `MAX` és , és számokon, karakterláncokon, logikai és nullértékeken dolgozik.

| Függvény | Leírás |
|-------|-------------|
| COUNT | A kifejezésben szereplő elemek számát adja eredményül. |
| SUM   | A kifejezés összes értékének összegét adja eredményül. |
| MIN   | A kifejezés minimális értékét adja eredményül. |
| MAX   | A kifejezés maximális értékét adja eredményül. |
| AVG   | A kifejezés értékeinek átlagát adja eredményül. |

A tömbiteráció eredményeit is összesítheti.

> [!NOTE]
> Az Azure Portal adatkezelőjében az összesítési lekérdezések csak egy lekérdezési lapon összesíthetik a részleges eredményeket. Az SDK egyetlen összegző értéket hoz létre az összes oldalon. A kód használatával történő összesítési lekérdezések végrehajtásához .NET SDK 1.12.0, .NET Core SDK 1.1.0 vagy Java SDK 1.9.5 vagy újabb szükséges.

## <a name="remarks"></a>Megjegyzések

Ezek az összesített rendszerfunkciók egy [tartományindex](index-policy.md#includeexclude-strategy)előnyeit élvezhetik . Ha azt szeretné, `COUNT` `SUM`hogy `MIN` `MAX`a `AVG` , , , , vagy egy tulajdonság, akkor meg kell [adnia a megfelelő elérési utat az indexelési politika.](index-policy.md#includeexclude-strategy)

## <a name="next-steps"></a>További lépések

- [Bevezetés az Azure Cosmos DB bemutatása](introduction.md)
- [Rendszerfüggvények](sql-query-system-functions.md)
- [Felhasználó által definiált függvények](sql-query-udfs.md)