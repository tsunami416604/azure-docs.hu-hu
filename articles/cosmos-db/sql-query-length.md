---
title: HOSSZ az Azure Cosmos DB lekérdezési nyelvében
description: Ismerje meg az SQL-rendszer hossz függvényét az Azure Cosmos DB-ben.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: e020555b0c706b5577bd20ac9bd537604d43ba3f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78303715"
---
# <a name="length-azure-cosmos-db"></a>HOSSZ (Azure Cosmos DB)
 A megadott karakterlánc-kifejezés karaktereinek számát adja eredményül.  
  
## <a name="syntax"></a>Szintaxis
  
```sql
LENGTH(<str_expr>)  
```  
  
## <a name="arguments"></a>Argumentumok
  
*str_expr*  
   A kiértékelendő karakterlánc-kifejezés.  
  
## <a name="return-types"></a>Visszatérési típusok
  
  Numerikus kifejezést ad eredményül.  
  
## <a name="examples"></a>Példák
  
  A következő példa egy karakterlánc hosszát adja vissza.  
  
```sql
SELECT LENGTH("abc") AS len 
```  
  
 Itt van az eredményhalmaz.  
  
```json
[{"len": 3}]  
```  

## <a name="remarks"></a>Megjegyzések

Ez a rendszerfunkció nem használja az indexet.

## <a name="next-steps"></a>További lépések

- [Az Azure Cosmos DB karakterlánc-függvényei](sql-query-string-functions.md)
- [Rendszerfüggvények Az Azure Cosmos DB](sql-query-system-functions.md)
- [Bevezetés az Azure Cosmos DB bemutatása](introduction.md)
