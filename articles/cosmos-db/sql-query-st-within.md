---
title: ST_WITHIN Azure Cosmos DB lekérdezési nyelven
description: Ismerkedjen meg az SQL System Function ST_WITHIN Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 40ecb26e7ac782d7831e6ef94c9d3cfc6a370cbb
ms.sourcegitcommit: 7f6d986a60eff2c170172bd8bcb834302bb41f71
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/27/2019
ms.locfileid: "71349331"
---
# <a name="st_within-azure-cosmos-db"></a>ST_WITHIN (Azure Cosmos DB)
 Egy logikai kifejezés, amely azt jelzi, hogy a második argumentum GeoJSON (pont, Polygon vagy LineString) belül van-e a GeoJSON-objektum (pont, Polygon vagy LineString) az első argumentumban megadott adja vissza.  
  
## <a name="syntax"></a>Szintaxis
  
```sql
ST_WITHIN (<spatial_expr>, <spatial_expr>)  
```  
  
## <a name="arguments"></a>Argumentumok
  
*spatial_expr*  
   Egy GeoJSON pont, sokszög vagy LineString objektum kifejezés.  
  
## <a name="return-types"></a>Visszatérési típusok
  
  Egy logikai értéket ad vissza.  
  
## <a name="examples"></a>Példák
  
  Az alábbi példa bemutatja, hogyan keresheti meg az összes családi dokumentumot egy sokszögen belül `ST_WITHIN` használatával.  
  
```sql
SELECT f.id   
FROM Families f   
WHERE ST_WITHIN(f.location, {  
    'type':'Polygon',   
    'coordinates': [[[31.8, -5], [32, -5], [32, -4.7], [31.8, -4.7], [31.8, -5]]]  
})  
```  
  
 Íme az eredményhalmaz.  
  
```json
[{ "id": "WakefieldFamily" }]  
```  

## <a name="next-steps"></a>További lépések

- [Térbeli függvények Azure Cosmos DB](sql-query-spatial-functions.md)
- [Rendszerfunkciók Azure Cosmos DB](sql-query-system-functions.md)
- [Bevezetés a Azure Cosmos DBba](introduction.md)
