---
title: ST_WITHIN az Azure Cosmos DB lekérdezési nyelvében
description: Az Azure Cosmos DB ST_WITHIN SQL-rendszerfunkcióiról.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 07a339d82f5e4bea1ea0412a5d5b19522611b54a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78296116"
---
# <a name="st_within-azure-cosmos-db"></a>ST_WITHIN (Azure Cosmos DB)
 Logikai kifejezést ad vissza, amely azt jelzi, hogy az első argumentumban megadott GeoJSON objektum (Point, Polygon vagy LineString) a második argumentum GeoJSON (Point, Polygon vagy LineString) argumentumában található-e.  
  
## <a name="syntax"></a>Szintaxis
  
```sql
ST_WITHIN (<spatial_expr>, <spatial_expr>)  
```  
  
## <a name="arguments"></a>Argumentumok
  
*spatial_expr*  
   GeoJSON-pont, sokszög vagy LineString objektumkifejezés.  
  
## <a name="return-types"></a>Visszatérési típusok
  
  Logikai értéket ad eredményül.  
  
## <a name="examples"></a>Példák
  
  A következő példa bemutatja, hogyan keresheti meg `ST_WITHIN`az összes családi dokumentumot egy sokszögben a használatával.  
  
```sql
SELECT f.id
FROM Families f
WHERE ST_WITHIN(f.location, {  
    'type':'Polygon',
    'coordinates': [[[31.8, -5], [32, -5], [32, -4.7], [31.8, -4.7], [31.8, -5]]]  
})  
```  
  
 Itt van az eredményhalmaz.  
  
```json
[{ "id": "WakefieldFamily" }]  
```  

## <a name="remarks"></a>Megjegyzések

Ez a rendszerfunkció egy [térinformatikai index](index-policy.md#spatial-indexes)előnyeit élvezi.

## <a name="next-steps"></a>További lépések

- [Térbeli függvények Az Azure Cosmos DB](sql-query-spatial-functions.md)
- [Rendszerfüggvények Az Azure Cosmos DB](sql-query-system-functions.md)
- [Bevezetés az Azure Cosmos DB bemutatása](introduction.md)
