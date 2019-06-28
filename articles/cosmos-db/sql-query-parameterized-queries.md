---
title: A paraméteres lekérdezések az Azure Cosmos DB-ben
description: Paraméteres SQL-lekérdezések ismertetése
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/30/2019
ms.author: tisande
ms.openlocfilehash: 2bfc22346c1dd43d7d3c2937ffc286e48ae774d0
ms.sourcegitcommit: a12b2c2599134e32a910921861d4805e21320159
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/24/2019
ms.locfileid: "67342639"
---
# <a name="parameterized-queries-in-azure-cosmos-db"></a>A paraméteres lekérdezések az Azure Cosmos DB-ben

A cosmos DB támogatja a lekérdezések az ismerős @ jelöléssel kifejezett paraméterekkel. Paraméteres SQL hatékony kezelése és a felhasználói adatbevitel escape-karaktersorozat biztosít, és megakadályozza, hogy a SQL-injektálás az adatok véletlen kapta.

## <a name="examples"></a>Példák

Például írhat egy lekérdezést, amely a `lastName` és `address.state` meg paraméterként, és hajtsa végre, a különböző értékek `lastName` és `address.state` felhasználói bemenet alapján.

```sql
    SELECT *
    FROM Families f
    WHERE f.lastName = @lastName AND f.address.state = @addressState
```

Majd elküldheti a kérelem Cosmos DB-hez egy paraméteres JSON lekérdezést a következőhöz hasonló:

```sql
    {
        "query": "SELECT * FROM Families f WHERE f.lastName = @lastName AND f.address.state = @addressState",
        "parameters": [
            {"name": "@lastName", "value": "Wakefield"},
            {"name": "@addressState", "value": "NY"},
        ]
    }
```

Az alábbi példa egy parametrizált lekérdezés első argumentumban állítja be: 

```sql
    {
        "query": "SELECT TOP @n * FROM Families",
        "parameters": [
            {"name": "@n", "value": 10},
        ]
    }
```

A paraméter értéke lehet bármely érvényes JSON: karakterláncok, számok, logikai értékek, NULL értékű, akkor is igaz, tömbök, vagy beágyazott JSON. Mivel a Cosmos DB séma nélküli, paraméterek bármilyen nem ellenőrzi.


## <a name="next-steps"></a>További lépések

- [Azure Cosmos DB .NET-minták](https://github.com/Azure/azure-cosmosdb-dotnet)
- [Dokumentumadatok modellezése](modeling-data.md)
