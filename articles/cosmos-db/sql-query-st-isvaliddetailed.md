---
title: ST_ISVALIDDETAILED az Azure Cosmos DB lekérdezési nyelvében
description: Az Azure Cosmos DB ST_ISVALIDDETAILED SQL-rendszerfunkcióiról.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 9e640c223c2fef844b9b53e1f4afa3a5d398c8c0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "71349354"
---
# <a name="st_isvaliddetailed-azure-cosmos-db"></a>ST_ISVALIDDETAILED (Azure Cosmos DB)
 Logikai értéket tartalmazó JSON-értéket ad eredményül, ha a megadott GeoJSON point, Polygon vagy LineString kifejezés érvényes, és ha érvénytelen, akkor az ok karakterláncértékként is.  
  
## <a name="syntax"></a>Szintaxis
  
```sql
ST_ISVALIDDETAILED(<spatial_expr>)  
```  
  
## <a name="arguments"></a>Argumentumok
  
*spatial_expr*  
   GeoJSON-pont vagy sokszögkifejezés.  
  
## <a name="return-types"></a>Visszatérési típusok
  
  Logikai értéket tartalmazó JSON-értéket ad eredményül, ha a megadott GeoJSON-pont vagy sokszögkifejezés érvényes, és ha érvénytelen, akkor az ok karakterlánc-értékként is.  
  
## <a name="examples"></a>Példák
  
  A következő példa arra, hogyan ellenőrizheti az érvényességet (részletekkel) a használatával. `ST_ISVALIDDETAILED`  
  
```sql
SELECT ST_ISVALIDDETAILED({   
  "type": "Polygon",   
  "coordinates": [[ [ 31.8, -5 ], [ 31.8, -4.7 ], [ 32, -4.7 ], [ 32, -5 ] ]]  
}) AS b 
```  
  
 Itt van az eredményhalmaz.  
  
```json
[{  
  "b": {
    "valid": false,
    "reason": "The Polygon input is not valid because the start and end points of the ring number 1 are not the same. Each ring of a polygon must have the same start and end points."   
  }  
}]  
```  

## <a name="next-steps"></a>További lépések

- [Térbeli függvények Az Azure Cosmos DB](sql-query-spatial-functions.md)
- [Rendszerfüggvények Az Azure Cosmos DB](sql-query-system-functions.md)
- [Bevezetés az Azure Cosmos DB bemutatása](introduction.md)
