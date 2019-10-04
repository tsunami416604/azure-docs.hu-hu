---
title: Alias a Azure Cosmos DBban
description: További információ az Azure Cosmos DB SQL-lekérdezésekben szereplő értékek aliasáról
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 06/20/2019
ms.author: mjbrown
ms.openlocfilehash: 3b17cbc7710647b1e1875025a1db1849034ec1dc
ms.sourcegitcommit: e97a0b4ffcb529691942fc75e7de919bc02b06ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/15/2019
ms.locfileid: "71002074"
---
# <a name="aliasing-in-azure-cosmos-db"></a>Alias a Azure Cosmos DBban

Explicit módon alias értékeket a lekérdezésekben. Ha egy lekérdezésnek két azonos nevű tulajdonsága van, az alias használatával nevezze át az egyik vagy mindkét tulajdonságot, hogy disambiguated a tervezett eredményben.

## <a name="examples"></a>Példák

Az aliasként használt AS kulcsszó nem kötelező, ahogy az alábbi példában is látható, amikor a második értéket `NameInfo`a következőként jeleníti meg:

```sql
    SELECT 
           { "state": f.address.state, "city": f.address.city } AS AddressInfo,
           { "name": f.id } NameInfo
    FROM Families f
    WHERE f.id = "AndersenFamily"
```

Az eredmények a következők:

```json
    [{
      "AddressInfo": {
        "state": "WA",
        "city": "Seattle"
      },
      "NameInfo": {
        "name": "AndersenFamily"
      }
    }]
```

## <a name="next-steps"></a>További lépések

- [Azure Cosmos DB .NET-minták](https://github.com/Azure/azure-cosmos-dotnet-v3)
- [SELECT záradék](sql-query-select.md)
- [FROM záradékban](sql-query-from.md)
