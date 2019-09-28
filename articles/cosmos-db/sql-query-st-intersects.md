---
title: ST_INTERSECTS Azure Cosmos DB lekérdezési nyelven
description: Ismerkedjen meg az SQL System Function ST_INTERSECTS Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 549c6b69e9112a491060478e859338c14e977612
ms.sourcegitcommit: 7f6d986a60eff2c170172bd8bcb834302bb41f71
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/27/2019
ms.locfileid: "71349381"
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

## <a name="next-steps"></a>További lépések

- [Térbeli függvények Azure Cosmos DB](sql-query-spatial-functions.md)
- [Rendszerfunkciók Azure Cosmos DB](sql-query-system-functions.md)
- [Bevezetés a Azure Cosmos DBba](introduction.md)
