---
title: LOG10 az Azure Cosmos DB lekérdezési nyelvében
description: Ismerje meg az Azure Cosmos DB LOG10 SQL rendszerfunkcióját a megadott numerikus kifejezés 10 logaritmusának visszaadásához
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 6f47200f6978d91f46c010640bb9c2bb26e9b7d5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78302491"
---
# <a name="log10-azure-cosmos-db"></a>LOG10 (Azure Cosmos DB)
 A megadott numerikus kifejezés 10-es alapú logaritmusát adja eredményül.  
  
## <a name="syntax"></a>Szintaxis
  
```sql
LOG10 (<numeric_expr>)  
```  
  
## <a name="arguments"></a>Argumentumok
  
*numeric_expression*  
   Ez egy numerikus kifejezés.  
  
## <a name="return-types"></a>Visszatérési típusok
  
  Numerikus kifejezést ad eredményül.  
  
## <a name="remarks"></a>Megjegyzések
  
  A LOG10 és a POWER függvény fordítottan kapcsolódnak egymáshoz. Például: 10 ^ LOG10(n) = n.  
  
## <a name="examples"></a>Példák
  
  A következő példa deklarál egy változót, és a megadott változó LOG10 értékét adja vissza (100).  
  
```sql
SELECT LOG10(100) AS log10 
```  
  
 Itt van az eredményhalmaz.  
  
```json
[{log10: 2}]  
```  

## <a name="remarks"></a>Megjegyzések

Ez a rendszerfunkció nem használja az indexet.

## <a name="next-steps"></a>További lépések

- [Matematikai függvények Az Azure Cosmos DB](sql-query-mathematical-functions.md)
- [Rendszerfüggvények Az Azure Cosmos DB](sql-query-system-functions.md)
- [Bevezetés az Azure Cosmos DB bemutatása](introduction.md)
