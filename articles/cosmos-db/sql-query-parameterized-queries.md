---
title: Paraméteres lekérdezések a Azure Cosmos DBban
description: Ismerje meg, hogy az SQL-paraméterek lekérdezései Hogyan biztosítják a felhasználói adatok hatékony kezelését és elkerülését, valamint az SQL-injektálással való véletlen adatexpozíciót.
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/30/2019
ms.author: tisande
ms.openlocfilehash: e15a8236723c1efd80f27f2d253e9bbc44af4b0b
ms.sourcegitcommit: 9405aad7e39efbd8fef6d0a3c8988c6bf8de94eb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/05/2019
ms.locfileid: "74870819"
---
# <a name="parameterized-queries-in-azure-cosmos-db"></a>Paraméteres lekérdezések a Azure Cosmos DBban

Cosmos DB támogatja a ismerős @ jelöléssel kifejezett paramétereket tartalmazó lekérdezéseket. A paraméteres SQL lehetővé teszi a felhasználói adatbevitel robusztus kezelését és megmenekülését, és megakadályozza az adatok véletlen kisugárzását az SQL-injektáláson keresztül.

## <a name="examples"></a>Példák

Írhat például egy olyan lekérdezést, amely `lastName` és `address.state` paramétereket, és végrehajtja a `lastName` és a `address.state` különböző értékeinek felhasználói bevitele alapján.

```sql
    SELECT *
    FROM Families f
    WHERE f.lastName = @lastName AND f.address.state = @addressState
```

Ezt a kérést elküldheti Cosmos DB paraméteres JSON-lekérdezésként, például az alábbiak szerint:

```sql
    {
        "query": "SELECT * FROM Families f WHERE f.lastName = @lastName AND f.address.state = @addressState",
        "parameters": [
            {"name": "@lastName", "value": "Wakefield"},
            {"name": "@addressState", "value": "NY"},
        ]
    }
```

A következő példa a felső argumentumot egy paraméteres lekérdezéssel állítja be: 

```sql
    {
        "query": "SELECT TOP @n * FROM Families",
        "parameters": [
            {"name": "@n", "value": 10},
        ]
    }
```

A paraméter értéke lehet bármilyen érvényes JSON: karakterlánc, szám, logikai érték, null, páros vagy beágyazott JSON. Mivel Cosmos DB séma nélküli, a paraméterek nem ellenőrizhetők semmilyen típuson.


## <a name="next-steps"></a>Következő lépések

- [.NET-minták Azure Cosmos DB](https://github.com/Azure/azure-cosmos-dotnet-v3)
- [Dokumentum-adattípusok](modeling-data.md)
