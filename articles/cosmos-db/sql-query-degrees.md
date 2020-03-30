---
title: FOK az Azure Cosmos DB lekérdezési nyelvében
description: Ismerje meg az Azure Cosmos DB FOKOS SQL rendszerfunkcióját, amely a megfelelő szöget adja vissza fokban a radiánban megadott szögben.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: d175ba53a71998fc8e7812a1b761f9cd264c38a9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78299470"
---
# <a name="degrees-azure-cosmos-db"></a>FOK (Azure Cosmos DB)
 A radiánban megadott szög megfelelő szögét adja eredményül fokban.  
  
## <a name="syntax"></a>Szintaxis
  
```sql
DEGREES (<numeric_expr>)  
```  
  
## <a name="arguments"></a>Argumentumok
  
*numeric_expr*  
   Ez egy numerikus kifejezés.  
  
## <a name="return-types"></a>Visszatérési típusok
  
  Numerikus kifejezést ad eredményül.  
  
## <a name="examples"></a>Példák
  
  A következő példa a PI/2 radián szögében lévő fokok számát adja eredményül.  
  
```sql
SELECT DEGREES(PI()/2) AS degrees  
```  
  
 Itt van az eredményhalmaz.  
  
```json
[{"degrees": 90}]  
```  

## <a name="remarks"></a>Megjegyzések

Ez a rendszerfunkció nem használja az indexet.

## <a name="next-steps"></a>További lépések

- [Matematikai függvények Az Azure Cosmos DB](sql-query-mathematical-functions.md)
- [Rendszerfüggvények Az Azure Cosmos DB](sql-query-system-functions.md)
- [Bevezetés az Azure Cosmos DB bemutatása](introduction.md)
