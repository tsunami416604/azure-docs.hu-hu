---
title: COS az Azure Cosmos DB lekérdezési nyelvében
description: Ismerje meg, hogyan adja vissza a Cosine (COS) SQL-rendszer függvényét az Azure Cosmos DB-ben a megadott szög trigonometriai koszinuszát radiánban a megadott kifejezésben.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 40d277ff38691e2cb74bd4d5d78a666c304acfcd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78304021"
---
# <a name="cos-azure-cosmos-db"></a>COS (Azure Cosmos DB)
 A megadott szög trigonometriai koszinuszát számítja ki radiánban a megadott kifejezésben.  
  
## <a name="syntax"></a>Szintaxis
  
```sql
COS(<numeric_expr>)  
```  
  
## <a name="arguments"></a>Argumentumok
  
*numeric_expr*  
   Ez egy numerikus kifejezés.  
  
## <a name="return-types"></a>Visszatérési típusok
  
  Numerikus kifejezést ad eredményül.  
  
## <a name="examples"></a>Példák
  
  A következő példa `COS` kiszámítja a megadott szöget.  
  
```sql
SELECT COS(14.78) AS cos  
```  
  
 Itt van az eredményhalmaz.  
  
```json
[{"cos": -0.59946542619465426}]  
```  

## <a name="remarks"></a>Megjegyzések

Ez a rendszerfunkció nem használja az indexet.

## <a name="next-steps"></a>További lépések

- [Matematikai függvények Az Azure Cosmos DB](sql-query-mathematical-functions.md)
- [Rendszerfüggvények Az Azure Cosmos DB](sql-query-system-functions.md)
- [Bevezetés az Azure Cosmos DB bemutatása](introduction.md)
