---
title: Azure Cosmos DB lekérdezési nyelv ST_ISVALIDDETAILED
description: Ismerkedjen meg a Azure Cosmos DB SQL System Function ST_ISVALIDDETAILEDával.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 9e640c223c2fef844b9b53e1f4afa3a5d398c8c0
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "71349354"
---
# <a name="st_isvaliddetailed-azure-cosmos-db"></a>ST_ISVALIDDETAILED (Azure Cosmos DB)
 Egy logikai értéket tartalmazó JSON-értéket ad vissza, ha a megadott GeoJSON pont, sokszög vagy LineString kifejezés érvényes, és ha az érték érvénytelen, akkor a karakterlánc értékét is adja meg.  
  
## <a name="syntax"></a>Szintaxis
  
```sql
ST_ISVALIDDETAILED(<spatial_expr>)  
```  
  
## <a name="arguments"></a>Argumentumok
  
*spatial_expr*  
   Egy GeoJSON pont vagy sokszög kifejezés.  
  
## <a name="return-types"></a>Visszatérési típusok
  
  Egy logikai értéket tartalmazó JSON-értéket ad vissza, ha a megadott GeoJSON pont-vagy sokszög-kifejezés érvényes, és ha az érték érvénytelen, akkor a karakterlánc értékét is adja meg.  
  
## <a name="examples"></a>Példák
  
  Az alábbi példa bemutatja, hogyan ellenőrizhető az érvényesség (részletekkel) a használatával `ST_ISVALIDDETAILED` .  
  
```sql
SELECT ST_ISVALIDDETAILED({   
  "type": "Polygon",   
  "coordinates": [[ [ 31.8, -5 ], [ 31.8, -4.7 ], [ 32, -4.7 ], [ 32, -5 ] ]]  
}) AS b 
```  
  
 Itt látható az eredményhalmaz.  
  
```json
[{  
  "b": {
    "valid": false,
    "reason": "The Polygon input is not valid because the start and end points of the ring number 1 are not the same. Each ring of a polygon must have the same start and end points."   
  }  
}]  
```  

## <a name="next-steps"></a>További lépések

- [Térbeli függvények Azure Cosmos DB](sql-query-spatial-functions.md)
- [Rendszerfunkciók Azure Cosmos DB](sql-query-system-functions.md)
- [Bevezetés a Azure Cosmos DBba](introduction.md)
