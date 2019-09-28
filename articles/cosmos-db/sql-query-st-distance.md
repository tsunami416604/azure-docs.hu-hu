---
title: ST_DISTANCE Azure Cosmos DB lekérdezési nyelven
description: Ismerkedjen meg az SQL System Function ST_DISTANCE Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 1c55bac14b3379f29d57bbad36026749089ec0fd
ms.sourcegitcommit: 7f6d986a60eff2c170172bd8bcb834302bb41f71
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/27/2019
ms.locfileid: "71349395"
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
  
  Az alábbi példa azt mutatja be, hogyan lehet visszaadni az összes olyan családi dokumentumot, amely a megadott hely 30 km-n belül található a `ST_DISTANCE` beépített függvénnyel. .  
  
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

## <a name="next-steps"></a>További lépések

- [Térbeli függvények Azure Cosmos DB](sql-query-spatial-functions.md)
- [Rendszerfunkciók Azure Cosmos DB](sql-query-system-functions.md)
- [Bevezetés a Azure Cosmos DBba](introduction.md)
