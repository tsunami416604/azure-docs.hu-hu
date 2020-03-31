---
title: ATN2 az Azure Cosmos DB lekérdezési nyelvében
description: Ismerje meg, hogyan adja vissza az ATN2 SQL-rendszer függvényét az Azure Cosmos DB-ben az y/x ívérintőjének radiánban kifejezett fő értékét.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 696e14e75998ead04c99fab2b84fc4c742d5f54a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78302661"
---
# <a name="atn2-azure-cosmos-db"></a>ATN2 (Azure Cosmos DB)
 Az y/x ívérintő nek a radiánban kifejezett fő értékét adja eredményül.  
  
## <a name="syntax"></a>Szintaxis
  
```sql
ATN2(<numeric_expr>, <numeric_expr>)  
```  
  
## <a name="arguments"></a>Argumentumok
  
*numeric_expr*  
   Ez egy numerikus kifejezés.  
  
## <a name="return-types"></a>Visszatérési típusok
  
  Numerikus kifejezést ad eredményül.  
  
## <a name="examples"></a>Példák
  
  A következő példa kiszámítja a megadott x és y összetevők ATN2-jét.  
  
```sql
SELECT ATN2(35.175643, 129.44) AS atn2  
```  
  
 Itt van az eredményhalmaz.  
  
```json
[{"atn2": 1.3054517947300646}]  
```  

## <a name="remarks"></a>Megjegyzések

Ez a rendszerfunkció nem használja az indexet.

## <a name="next-steps"></a>További lépések

- [Matematikai függvények Az Azure Cosmos DB](sql-query-mathematical-functions.md)
- [Rendszerfüggvények Az Azure Cosmos DB](sql-query-system-functions.md)
- [Bevezetés az Azure Cosmos DB bemutatása](introduction.md)
