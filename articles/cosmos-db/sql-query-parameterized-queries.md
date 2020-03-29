---
title: Paraméterezett lekérdezések az Azure Cosmos DB-ben
description: Ismerje meg, hogy az SQL paraméterezett lekérdezések hogyan biztosítják a felhasználói bevitel megbízható kezelését és kikerülését, és hogyan akadályozzák meg az adatok véletlen expozícióját az SQL-injektálás révén.
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/30/2019
ms.author: tisande
ms.openlocfilehash: e15a8236723c1efd80f27f2d253e9bbc44af4b0b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74870819"
---
# <a name="parameterized-queries-in-azure-cosmos-db"></a>Paraméterezett lekérdezések az Azure Cosmos DB-ben

A Cosmos DB támogatja az ismerős @ jelöléssel kifejezett paraméterekkel rendelkező lekérdezéseket. A paraméteres SQL megbízható kezelést és a felhasználói bevitel elkerülését biztosítja, és megakadályozza az adatok véletlen expozícióját az SQL-injektálás révén.

## <a name="examples"></a>Példák

Például `lastName` írhat unk egy lekérdezést, amely úgy és `address.state` paraméterek, `lastName` és `address.state` végrehajtja azt a különböző értékeket, és a felhasználói bevitel alapján.

```sql
    SELECT *
    FROM Families f
    WHERE f.lastName = @lastName AND f.address.state = @addressState
```

Ezt a kérést ezután a Cosmos DB-nek paraméterezett JSON-lekérdezésként elküldheti, például a következőképpen:

```sql
    {
        "query": "SELECT * FROM Families f WHERE f.lastName = @lastName AND f.address.state = @addressState",
        "parameters": [
            {"name": "@lastName", "value": "Wakefield"},
            {"name": "@addressState", "value": "NY"},
        ]
    }
```

A következő példa a TOP argumentumot paraméterezett lekérdezéssel állítja be: 

```sql
    {
        "query": "SELECT TOP @n * FROM Families",
        "parameters": [
            {"name": "@n", "value": 10},
        ]
    }
```

A paraméterértékek bármely érvényes JSON: karakterláncok, számok, logikai értékek, null, páros tömbök vagy beágyazott JSON lehetnek. Mivel a Cosmos DB séma nélküli, a paraméterek nem érvényesíthetők semmilyen típussal szemben.


## <a name="next-steps"></a>További lépések

- [Az Azure Cosmos DB .NET-mintái](https://github.com/Azure/azure-cosmos-dotnet-v3)
- [Dokumentumadatok modellezése](modeling-data.md)
