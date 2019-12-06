---
title: Alias a Azure Cosmos DBban
description: Megtudhatja, hogyan használhatja az aliasokat Azure Cosmos DB SQL-lekérdezésekben, hogy megkülönböztesse a két tulajdonságot ugyanazzal a névvel
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/02/2019
ms.author: mjbrown
ms.openlocfilehash: 74849eec4c5808a584894321269c49c41f0b8a5c
ms.sourcegitcommit: 9405aad7e39efbd8fef6d0a3c8988c6bf8de94eb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/05/2019
ms.locfileid: "74873471"
---
# <a name="aliasing-in-azure-cosmos-db"></a>Alias a Azure Cosmos DBban

Explicit módon alias értékeket a lekérdezésekben. Ha egy lekérdezésnek két azonos nevű tulajdonsága van, az alias használatával nevezze át az egyik vagy mindkét tulajdonságot, hogy disambiguated a tervezett eredményben.

## <a name="examples"></a>Példák

Az aliasként használt AS kulcsszó nem kötelező, ahogy az az alábbi példában is látható, amikor a második értéket `NameInfo`:

```sql
    SELECT 
           { "state": f.address.state, "city": f.address.city } AS AddressInfo,
           { "name": f.id } NameInfo
    FROM Families f
    WHERE f.id = "AndersenFamily"
```

Az eredmény a következő:

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

## <a name="next-steps"></a>Következő lépések

- [.NET-minták Azure Cosmos DB](https://github.com/Azure/azure-cosmos-dotnet-v3)
- [SELECT záradék](sql-query-select.md)
- [FROM záradék](sql-query-from.md)
