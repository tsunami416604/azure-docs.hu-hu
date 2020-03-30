---
title: TAN az Azure Cosmos DB lekérdezési nyelvében
description: Ismerje meg az SQL rendszer funkció TAN az Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/04/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 9d7187ba116067445e835769fc33aa70677ef80b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78301981"
---
# <a name="tan-azure-cosmos-db"></a>TAN (Azure Cosmos DB)
 A megadott szög nek a megadott szögének érintőit adja eredményül a megadott kifejezésben radiánban.  
  
## <a name="syntax"></a>Szintaxis
  
```sql
TAN (<numeric_expr>)  
```  
  
## <a name="arguments"></a>Argumentumok
  
*numeric_expr*  
   Ez egy numerikus kifejezés.  
  
## <a name="return-types"></a>Visszatérési típusok
  
  Numerikus kifejezést ad eredményül.  
  
## <a name="examples"></a>Példák
  
  A következő példa kiszámítja a PI()/2 érintőit.  
  
```sql
SELECT TAN(PI()/2) AS tan 
```  
  
 Itt van az eredményhalmaz.  
  
```json
[{"tan": 16331239353195370 }]  
```  

## <a name="remarks"></a>Megjegyzések

Ez a rendszerfunkció nem használja az indexet.

## <a name="next-steps"></a>További lépések

- [Matematikai függvények Az Azure Cosmos DB](sql-query-mathematical-functions.md)
- [Rendszerfüggvények Az Azure Cosmos DB](sql-query-system-functions.md)
- [Bevezetés az Azure Cosmos DB bemutatása](introduction.md)
