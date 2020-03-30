---
title: POWER az Azure Cosmos DB lekérdezési nyelvében
description: Ismerje meg az SQL rendszer funkció POWER az Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 683c53c369f136ad4b917b93e9a92a71072d05e0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "71349638"
---
# <a name="power-azure-cosmos-db"></a>POWER (Azure Cosmos DB)
 A megadott kifejezés értékét adja vissza a megadott teljesítménynek.  
  
## <a name="syntax"></a>Szintaxis
  
```sql
POWER (<numeric_expr1>, <numeric_expr2>)  
```  
  
## <a name="arguments"></a>Argumentumok
  
*numeric_expr1*  
   Ez egy numerikus kifejezés.  
  
*numeric_expr2*  
   A hatalom, amely emelni *numeric_expr1*.  
  
## <a name="return-types"></a>Visszatérési típusok
  
  Numerikus kifejezést ad eredményül.  
  
## <a name="examples"></a>Példák
  
  A következő példa bemutatja egy szám 3(a szám kockája) erejére való emelést.  
  
```sql
SELECT POWER(2, 3) AS pow1, POWER(2.5, 3) AS pow2  
```  
  
 Itt van az eredményhalmaz.  
  
```json
[{pow1: 8, pow2: 15.625}]  
```  

## <a name="next-steps"></a>További lépések

- [Matematikai függvények Az Azure Cosmos DB](sql-query-mathematical-functions.md)
- [Rendszerfüggvények Az Azure Cosmos DB](sql-query-system-functions.md)
- [Bevezetés az Azure Cosmos DB bemutatása](introduction.md)
