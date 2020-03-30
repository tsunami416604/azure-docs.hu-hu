---
title: COT az Azure Cosmos DB lekérdezési nyelvében
description: Ismerje meg, hogyan adja vissza a Cotangent(COT) SQL-rendszer függvényét az Azure Cosmos DB-ben a megadott szög trigonometriai kotangensét radiánban a megadott numerikus kifejezésben.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 25c907644f58ee40ea08e5636d68dc0e84564a28
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78299487"
---
# <a name="cot-azure-cosmos-db"></a>COT (Azure Cosmos DB)
 A megadott szög trigonometriai kotangensét számítja ki radiánban, a megadott numerikus kifejezésben.  
  
## <a name="syntax"></a>Szintaxis
  
```sql
COT(<numeric_expr>)  
```  
  
## <a name="arguments"></a>Argumentumok
  
*numeric_expr*  
   Ez egy numerikus kifejezés.  
  
## <a name="return-types"></a>Visszatérési típusok
  
  Numerikus kifejezést ad eredményül.  
  
## <a name="examples"></a>Példák
  
  A következő példa `COT` kiszámítja a megadott szöget.  
  
```sql
SELECT COT(124.1332) AS cot  
```  
  
 Itt van az eredményhalmaz.  
  
```json
[{"cot": -0.040311998371148884}]  
```  

## <a name="remarks"></a>Megjegyzések

Ez a rendszerfunkció nem használja az indexet.

## <a name="next-steps"></a>További lépések

- [Matematikai függvények Az Azure Cosmos DB](sql-query-mathematical-functions.md)
- [Rendszerfüggvények Az Azure Cosmos DB](sql-query-system-functions.md)
- [Bevezetés az Azure Cosmos DB bemutatása](introduction.md)
