---
title: Aliasing az Azure Cosmos DB-ben
description: Megtudhatja, hogy miként használhat aliast az Azure Cosmos DB SQL-lekérdezésekben két azonos nevű tulajdonság megkülönböztetésére
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/02/2019
ms.author: mjbrown
ms.openlocfilehash: 74849eec4c5808a584894321269c49c41f0b8a5c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74873471"
---
# <a name="aliasing-in-azure-cosmos-db"></a>Aliasing az Azure Cosmos DB-ben

A lekérdezésekben explicit módon is eladhat értékeket. Ha egy lekérdezés nek két azonos nevű tulajdonsága van, az aliasok segítségével nevezze át az egyik vagy mindkét tulajdonságot, hogy a tervezett eredmény ne kerülhessen egyértelműre.

## <a name="examples"></a>Példák

Az aliasoláshoz használt AS-kulcsszó nem kötelező, amint az a `NameInfo`következő példában is látható a második érték kivetítésekénél:

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

## <a name="next-steps"></a>További lépések

- [Az Azure Cosmos DB .NET-mintái](https://github.com/Azure/azure-cosmos-dotnet-v3)
- [SELECT záradék](sql-query-select.md)
- [FROM záradék](sql-query-from.md)
