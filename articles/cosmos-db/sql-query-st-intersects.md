---
title: Azure Cosmos DB lekérdezési nyelv ST_INTERSECTS
description: Ismerkedjen meg a Azure Cosmos DB SQL System Function ST_INTERSECTSával.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: f3c3878956b90ffb45556ed819046af9eb7618f1
ms.sourcegitcommit: f915d8b43a3cefe532062ca7d7dbbf569d2583d8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/05/2020
ms.locfileid: "78303137"
---
# <a name="st_intersects-azure-cosmos-db"></a>ST_INTERSECTS (Azure Cosmos DB)
 Egy logikai kifejezés jelzi-e a GeoJSON-objektum (pont, Polygon vagy LineString) az első argumentumban megadott metszi a GeoJSON (pont, Polygon vagy LineString) a második argumentumban adja vissza.  
  
## <a name="syntax"></a>Szintaxis
  
```sql
ST_INTERSECTS (<spatial_expr>, <spatial_expr>)  
```  
  
## <a name="arguments"></a>Argumentumok
  
*spatial_expr*  
   Egy GeoJSON pont, sokszög vagy LineString objektum kifejezés.  
  
## <a name="return-types"></a>Visszatérési típusok
  
  Egy logikai értéket ad vissza.  
  
## <a name="examples"></a>Példák
  
  Az alábbi példa bemutatja, hogyan keresse meg a megadott sokszög metsző minden terület.  
  
```sql
SELECT a.id
FROM Areas a
WHERE ST_INTERSECTS(a.location, {  
    'type':'Polygon',
    'coordinates': [[[31.8, -5], [32, -5], [32, -4.7], [31.8, -4.7], [31.8, -5]]]  
})  
```  
  
 Íme az eredményhalmaz.  
  
```json
[{ "id": "IntersectingPolygon" }]  
```  

## <a name="remarks"></a>Megjegyzések

Ez a rendszerfunkció kihasználja a [térinformatikai index](index-policy.md#spatial-indexes)előnyeit.

## <a name="next-steps"></a>Következő lépések

- [Térbeli függvények Azure Cosmos DB](sql-query-spatial-functions.md)
- [Rendszerfunkciók Azure Cosmos DB](sql-query-system-functions.md)
- [Bevezetés a Azure Cosmos DBba](introduction.md)
