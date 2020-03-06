---
title: Azure Cosmos DB lekérdezési nyelv ARRAY_LENGTH
description: Ismerje meg, hogy a tömb hosszúságú SQL System függvénye hogyan Azure Cosmos DB a megadott Array kifejezés elemeinek számát adja vissza.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 3596ce4bc702d5e54225d8c90db2f9563feab670
ms.sourcegitcommit: f915d8b43a3cefe532062ca7d7dbbf569d2583d8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/05/2020
ms.locfileid: "78303987"
---
# <a name="array_length-azure-cosmos-db"></a>ARRAY_LENGTH (Azure Cosmos DB)
 A megadott tömb kifejezés elemek számát adja vissza.  
  
## <a name="syntax"></a>Szintaxis
  
```sql
ARRAY_LENGTH(<arr_expr>)  
```  
  
## <a name="arguments"></a>Argumentumok
  
*arr_expr*  
   Tömb kifejezés.  
  
## <a name="return-types"></a>Visszatérési típusok
  
  A numerikus kifejezést ad vissza.  
  
## <a name="examples"></a>Példák
  
  A következő példa egy tömb hosszának lekérését mutatja be `ARRAY_LENGTH`használatával.  
  
```sql
SELECT ARRAY_LENGTH(["apples", "strawberries", "bananas"]) AS len  
```  
  
 Íme az eredményhalmaz.  
  
```json
[{"len": 3}]  
```  
  
## <a name="remarks"></a>Megjegyzések

Ez a rendszerfüggvény nem fogja használni az indexet.

## <a name="next-steps"></a>Következő lépések

- [Array függvények Azure Cosmos DB](sql-query-array-functions.md)
- [Rendszerfunkciók Azure Cosmos DB](sql-query-system-functions.md)
- [Bevezetés a Azure Cosmos DBba](introduction.md)
