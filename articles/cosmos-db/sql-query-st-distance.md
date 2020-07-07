---
title: Azure Cosmos DB lekérdezési nyelv ST_DISTANCE
description: Ismerkedjen meg a Azure Cosmos DB SQL System Function ST_DISTANCEával.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/12/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 02844569137a46ea030b2189191b84a9db24ed22
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "79537295"
---
# <a name="st_distance-azure-cosmos-db"></a>ST_DISTANCE (Azure Cosmos DB)
 A két GeoJSON pont, a sokszög, a többsokszögű vagy a LineString kifejezés közötti távolságot adja vissza. További információért lásd a [térinformatikai és a GeoJSON vonatkozó információkat](sql-query-geospatial-intro.md) ismertető cikket.
  
## <a name="syntax"></a>Szintaxis
  
```sql
ST_DISTANCE (<spatial_expr>, <spatial_expr>)  
```  
  
## <a name="arguments"></a>Argumentumok
  
*spatial_expr*  
   Bármely érvényes GeoJSON pont, sokszög vagy LineString objektum kifejezés.  
  
## <a name="return-types"></a>Visszatérési típusok
  
  A távolságot tartalmazó numerikus kifejezést adja vissza. Ez az alapértelmezett hivatkozási rendszer mérőszámában van kifejezve.  
  
## <a name="examples"></a>Példák
  
  Az alábbi példa bemutatja, hogyan lehet visszaadni az összes olyan családi dokumentumot, amely a megadott hely 30 km-n belül található a `ST_DISTANCE` beépített függvénnyel. .  
  
```sql
SELECT f.id
FROM Families f
WHERE ST_DISTANCE(f.location, {'type': 'Point', 'coordinates':[31.9, -4.8]}) < 30000  
```  
  
 Itt látható az eredményhalmaz.  
  
```json
[{  
  "id": "WakefieldFamily"  
}]  
```

## <a name="remarks"></a>Megjegyzések

Ez a rendszerfunkció kihasználja a [térinformatikai index](index-policy.md#spatial-indexes)előnyeit.

## <a name="next-steps"></a>További lépések

- [Térbeli függvények Azure Cosmos DB](sql-query-spatial-functions.md)
- [Rendszerfunkciók Azure Cosmos DB](sql-query-system-functions.md)
- [Bevezetés a Azure Cosmos DBba](introduction.md)
