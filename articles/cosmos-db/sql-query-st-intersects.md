---
title: ST_INTERSECTS az Azure Cosmos DB lekérdezési nyelvében
description: Az Azure Cosmos DB ST_INTERSECTS SQL-rendszerfüggvényekről.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: f3c3878956b90ffb45556ed819046af9eb7618f1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78303137"
---
# <a name="st_intersects-azure-cosmos-db"></a>ST_INTERSECTS (Azure Cosmos DB)
 Logikai kifejezést ad vissza, amely azt jelzi, hogy az első argumentumban megadott GeoJSON objektum (Point, Polygon vagy LineString) metszi-e a GeoJSON (Point, Polygon vagy LineString) paramétert a második argumentumban.  
  
## <a name="syntax"></a>Szintaxis
  
```sql
ST_INTERSECTS (<spatial_expr>, <spatial_expr>)  
```  
  
## <a name="arguments"></a>Argumentumok
  
*spatial_expr*  
   GeoJSON-pont, sokszög vagy LineString objektumkifejezés.  
  
## <a name="return-types"></a>Visszatérési típusok
  
  Logikai értéket ad eredményül.  
  
## <a name="examples"></a>Példák
  
  A következő példa bemutatja, hogyan keresheti meg az adott sokszöget metsző összes területet.  
  
```sql
SELECT a.id
FROM Areas a
WHERE ST_INTERSECTS(a.location, {  
    'type':'Polygon',
    'coordinates': [[[31.8, -5], [32, -5], [32, -4.7], [31.8, -4.7], [31.8, -5]]]  
})  
```  
  
 Itt van az eredményhalmaz.  
  
```json
[{ "id": "IntersectingPolygon" }]  
```  

## <a name="remarks"></a>Megjegyzések

Ez a rendszerfunkció egy [térinformatikai index](index-policy.md#spatial-indexes)előnyeit élvezi.

## <a name="next-steps"></a>További lépések

- [Térbeli függvények Az Azure Cosmos DB](sql-query-spatial-functions.md)
- [Rendszerfüggvények Az Azure Cosmos DB](sql-query-system-functions.md)
- [Bevezetés az Azure Cosmos DB bemutatása](introduction.md)
