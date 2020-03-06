---
title: Azure Cosmos DB lekérdezési nyelv ST_DISTANCE
description: Ismerkedjen meg a Azure Cosmos DB SQL System Function ST_DISTANCEával.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 4908d5f9f6eccaaaf71308b868d712f0eb96cb52
ms.sourcegitcommit: f915d8b43a3cefe532062ca7d7dbbf569d2583d8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/05/2020
ms.locfileid: "78303154"
---
# <a name="st_distance-azure-cosmos-db"></a>ST_DISTANCE (Azure Cosmos DB)
 A két GeoJSON-pont, Polygon vagy LineString kifejezések között adja vissza a távolságot.  
  
## <a name="syntax"></a>Szintaxis
  
```sql
ST_DISTANCE (<spatial_expr>, <spatial_expr>)  
```  
  
## <a name="arguments"></a>Argumentumok
  
*spatial_expr*  
   Ez bármilyen érvényes GeoJSON-pont, Polygon vagy LineString objektum kifejezés.  
  
## <a name="return-types"></a>Visszatérési típusok
  
  Egy numerikus kifejezés tartalmazó távolságot adja vissza. Ez az alapértelmezett referenciarendszer mérőszámai van megadva.  
  
## <a name="examples"></a>Példák
  
  Az alábbi példa azt mutatja be, hogyan lehet visszaadni az összes olyan családi dokumentumot, amely a megadott hely 30 km-n belül található a `ST_DISTANCE` beépített függvény használatával. .  
  
```sql
SELECT f.id
FROM Families f
WHERE ST_DISTANCE(f.location, {'type': 'Point', 'coordinates':[31.9, -4.8]}) < 30000  
```  
  
 Íme az eredményhalmaz.  
  
```json
[{  
  "id": "WakefieldFamily"  
}]  
```

## <a name="remarks"></a>Megjegyzések

Ez a rendszerfunkció kihasználja a [térinformatikai index](index-policy.md#spatial-indexes)előnyeit.

## <a name="next-steps"></a>Következő lépések

- [Térbeli függvények Azure Cosmos DB](sql-query-spatial-functions.md)
- [Rendszerfunkciók Azure Cosmos DB](sql-query-system-functions.md)
- [Bevezetés a Azure Cosmos DBba](introduction.md)
