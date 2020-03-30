---
title: ACOS az Azure Cosmos DB lekérdezési nyelvében
description: Ismerje meg, hogyan adja vissza az ACOS (arckozice) SQL-rendszer függvényét az Azure Cosmos DB-ben a búzsában lévő szöget, amelynek koszinusza a megadott numerikus kifejezés
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 733d6b009f03d61c37170cc506a3b2ec842d7c47
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78300961"
---
# <a name="acos-azure-cosmos-db"></a>ACOS (Azure Cosmos DB)
 Radiánban lévő szöget számít ja, amelynek koszinusza a megadott numerikus kifejezés; más néven arckoszin.  
  
## <a name="syntax"></a>Szintaxis
  
```sql
ACOS(<numeric_expr>)  
```  
  
## <a name="arguments"></a>Argumentumok
  
*numeric_expr*  
   Ez egy numerikus kifejezés.  
  
## <a name="return-types"></a>Visszatérési típusok
  
  Numerikus kifejezést ad eredményül.  
  
## <a name="examples"></a>Példák
  
  A következő példa `ACOS` a -1 értékét adja vissza.  
  
```sql
SELECT ACOS(-1) AS acos 
```  
  
 Itt van az eredményhalmaz.  
  
```json
[{"acos": 3.1415926535897931}]  
```  

## <a name="remarks"></a>Megjegyzések

Ez a rendszerfunkció nem használja az indexet.

## <a name="next-steps"></a>További lépések

- [Matematikai függvények Az Azure Cosmos DB](sql-query-mathematical-functions.md)
- [Rendszerfüggvények Az Azure Cosmos DB](sql-query-system-functions.md)
- [Bevezetés az Azure Cosmos DB bemutatása](introduction.md)
