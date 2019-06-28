---
title: Az Azure Cosmos DB aliasképző
description: További tudnivalók az Azure Cosmos DB SQL-lekérdezések aliasképző értékek
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 06/20/2019
ms.author: mjbrown
ms.openlocfilehash: e532fb7180af8a21de6ae9a2e4d798abd9e93e7b
ms.sourcegitcommit: a12b2c2599134e32a910921861d4805e21320159
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/24/2019
ms.locfileid: "67342538"
---
# <a name="aliasing-in-azure-cosmos-db"></a>Az Azure Cosmos DB aliasképző

Így explicit módon alias értékek lekérdezésekben. Ha a lekérdezés két tulajdonság azonos nevű, használja a aliasképző egyikét vagy mindkettőt a Tulajdonságok átnevezése, így azok az előre jelzett eredmény van használatát.

## <a name="examples"></a>Példák

Az AS-aliasképző használt kulcsszó nem kötelező használni, mint a második érték Segédben az alábbi példában látható módon `NameInfo`:

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

- [Azure Cosmos DB .NET-minták](https://github.com/Azure/azure-cosmosdb-dotnet)
- [SELECT záradék](sql-query-select.md)
- [FROM záradékban](sql-query-from.md)
