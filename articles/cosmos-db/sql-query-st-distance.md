---
title: ST_DISTANCE az Azure Cosmos DB lekérdezési nyelvében
description: Az Azure Cosmos DB ST_DISTANCE SQL-rendszerfunkciós tudnivalók.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/12/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 02844569137a46ea030b2189191b84a9db24ed22
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79537295"
---
# <a name="st_distance-azure-cosmos-db"></a>ST_DISTANCE (Azure Cosmos DB)
 A két GeoJSON-pont, Sokszög, MultiPoligon vagy LineString kifejezés közötti távolságot adja eredményül. További információ: [A Térinformatikai és GeoJSON helyadatokról szóló](sql-query-geospatial-intro.md) cikk.
  
## <a name="syntax"></a>Szintaxis
  
```sql
ST_DISTANCE (<spatial_expr>, <spatial_expr>)  
```  
  
## <a name="arguments"></a>Argumentumok
  
*spatial_expr*  
   Érvényes GeoJSON-pont, Sokszög vagy LineString objektumkifejezés.  
  
## <a name="return-types"></a>Visszatérési típusok
  
  A távolságot tartalmazó numerikus kifejezést ad eredményül. Ezt az alapértelmezett referenciarendszer esetében méterben fejezik ki.  
  
## <a name="examples"></a>Példák
  
  A következő példa bemutatja, hogyan küldhet vissza minden olyan családi dokumentumot, amely a megadott hely30 km-en belül van a `ST_DISTANCE` beépített funkció használatával. .  
  
```sql
SELECT f.id
FROM Families f
WHERE ST_DISTANCE(f.location, {'type': 'Point', 'coordinates':[31.9, -4.8]}) < 30000  
```  
  
 Itt van az eredményhalmaz.  
  
```json
[{  
  "id": "WakefieldFamily"  
}]  
```

## <a name="remarks"></a>Megjegyzések

Ez a rendszerfunkció egy [térinformatikai index](index-policy.md#spatial-indexes)előnyeit élvezi.

## <a name="next-steps"></a>További lépések

- [Térbeli függvények Az Azure Cosmos DB](sql-query-spatial-functions.md)
- [Rendszerfüggvények Az Azure Cosmos DB](sql-query-system-functions.md)
- [Bevezetés az Azure Cosmos DB bemutatása](introduction.md)
