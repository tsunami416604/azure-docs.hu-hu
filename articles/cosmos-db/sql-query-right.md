---
title: JOBBRA az Azure Cosmos DB lekérdezési nyelvében
description: Ismerje meg az SQL-rendszer függvény e KÖZVETLEN az Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 247616d2ac4f2a5799a5896d679f6e6d5917d5a6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78302151"
---
# <a name="right-azure-cosmos-db"></a>JOBB (Azure Cosmos DB)
 A megadott számú karakterrel rendelkező karakterlánc jobb részét adja eredményül.  
  
## <a name="syntax"></a>Szintaxis
  
```sql
RIGHT(<str_expr>, <num_expr>)  
```  
  
## <a name="arguments"></a>Argumentumok
  
*str_expr*  
   Az a karakterlánc-kifejezés, amelyből ki szeretné nyerni a karaktereket.  
  
*num_expr*  
   Numerikus kifejezés, amely megadja a karakterek számát.  
  
## <a name="return-types"></a>Visszatérési típusok
  
  Karakterlánc-kifejezést ad vissza.  
  
## <a name="examples"></a>Példák
  
  A következő példa az "abc" jobb oldali részét adja vissza a különböző hosszértékekhez.  
  
```sql
SELECT RIGHT("abc", 1) AS r1, RIGHT("abc", 2) AS r2 
```  
  
 Itt van az eredményhalmaz.  
  
```json
[{"r1": "c", "r2": "bc"}]  
```  

## <a name="remarks"></a>Megjegyzések

Ez a rendszerfunkció nem használja az indexet.

## <a name="next-steps"></a>További lépések

- [Az Azure Cosmos DB karakterlánc-függvényei](sql-query-string-functions.md)
- [Rendszerfüggvények Az Azure Cosmos DB](sql-query-system-functions.md)
- [Bevezetés az Azure Cosmos DB bemutatása](introduction.md)
