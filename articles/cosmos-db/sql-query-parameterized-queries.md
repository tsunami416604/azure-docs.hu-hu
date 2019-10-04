---
title: Paraméteres lekérdezések a Azure Cosmos DBban
description: Tudnivalók az SQL paraméteres lekérdezésekről
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/30/2019
ms.author: tisande
ms.openlocfilehash: 45c1344c32e35f60f35ba8ed105e912d92574cce
ms.sourcegitcommit: e97a0b4ffcb529691942fc75e7de919bc02b06ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/15/2019
ms.locfileid: "71003609"
---
# <a name="parameterized-queries-in-azure-cosmos-db"></a>Paraméteres lekérdezések a Azure Cosmos DBban

Cosmos DB támogatja a ismerős @ jelöléssel kifejezett paramétereket tartalmazó lekérdezéseket. A paraméteres SQL lehetővé teszi a felhasználói adatbevitel robusztus kezelését és megmenekülését, és megakadályozza az adatok véletlen kisugárzását az SQL-injektáláson keresztül.

## <a name="examples"></a>Példák

Írhat például egy `lastName` olyan lekérdezést, amely `address.state` paramétereket fogad `lastName` el, és paraméterként végrehajtja azt, és a felhasználói bevitelen `address.state` alapuló különböző értékekre hajtja végre.

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


## <a name="next-steps"></a>További lépések

- [Azure Cosmos DB .NET-minták](https://github.com/Azure/azure-cosmos-dotnet-v3)
- [Dokumentum-adattípusok](modeling-data.md)
