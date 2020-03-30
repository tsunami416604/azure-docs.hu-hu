---
title: SIN az Azure Cosmos DB lekérdezési nyelvében
description: Ismerje meg az SQL rendszer funkció SIN az Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 25e7cf66fdd55a0b641c35443e38b0a67cbe365d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78303103"
---
# <a name="sin-azure-cosmos-db"></a>SIN (Azure Cosmos DB)
 A megadott szög trigonometriai szinuszát számítja ki radiánban a megadott kifejezésben.  
  
## <a name="syntax"></a>Szintaxis
  
```sql
SIN(<numeric_expr>)  
```  
  
## <a name="arguments"></a>Argumentumok
  
*numeric_expr*  
   Ez egy numerikus kifejezés.  
  
## <a name="return-types"></a>Visszatérési típusok
  
  Numerikus kifejezést ad eredményül.  
  
## <a name="examples"></a>Példák
  
  A következő példa `SIN` kiszámítja a megadott szöget.  
  
```sql
SELECT SIN(45.175643) AS sin  
```  
  
 Itt van az eredményhalmaz.  
  
```json
[{"sin": 0.929607286611012}]  
```  

## <a name="remarks"></a>Megjegyzések

Ez a rendszerfunkció nem használja az indexet.

## <a name="next-steps"></a>További lépések

- [Matematikai függvények Az Azure Cosmos DB](sql-query-mathematical-functions.md)
- [Rendszerfüggvények Az Azure Cosmos DB](sql-query-system-functions.md)
- [Bevezetés az Azure Cosmos DB bemutatása](introduction.md)
