---
title: Azure Cosmos DB lekérdezési nyelv ST_WITHIN
description: Ismerkedjen meg a Azure Cosmos DB SQL System Function ST_WITHINával.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 07a339d82f5e4bea1ea0412a5d5b19522611b54a
ms.sourcegitcommit: f915d8b43a3cefe532062ca7d7dbbf569d2583d8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/05/2020
ms.locfileid: "78296116"
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
  
  Az alábbi példa bemutatja, hogyan keresheti meg az összes családi dokumentumot egy sokszögen belül `ST_WITHIN`használatával.  
  
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

## <a name="remarks"></a>Megjegyzések

Ez a rendszerfunkció kihasználja a [térinformatikai index](index-policy.md#spatial-indexes)előnyeit.

## <a name="next-steps"></a>Következő lépések

- [Térbeli függvények Azure Cosmos DB](sql-query-spatial-functions.md)
- [Rendszerfunkciók Azure Cosmos DB](sql-query-system-functions.md)
- [Bevezetés a Azure Cosmos DBba](introduction.md)
