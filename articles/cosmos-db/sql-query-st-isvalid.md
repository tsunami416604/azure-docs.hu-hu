---
title: ST_ISVALID Azure Cosmos DB lekérdezési nyelven
description: Ismerkedjen meg az SQL System Function ST_ISVALID Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 8fbddbe82ae13585b8259a66dffaeef8024baf5d
ms.sourcegitcommit: 7f6d986a60eff2c170172bd8bcb834302bb41f71
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/27/2019
ms.locfileid: "71349362"
---
# <a name="st_isvalid-azure-cosmos-db"></a>ST_ISVALID (Azure Cosmos DB)
 Jelzi, hogy a megadott GeoJSON-pont, Polygon vagy LineString kifejezés érvénytelen egy logikai értéket ad vissza.  
  
## <a name="syntax"></a>Szintaxis
  
```sql
ST_ISVALID(<spatial_expr>)  
```  
  
## <a name="arguments"></a>Argumentumok
  
*spatial_expr*  
   Egy GeoJSON pont, sokszög vagy LineString kifejezés.  
  
## <a name="return-types"></a>Visszatérési típusok
  
  Egy logikai kifejezés adja vissza.  
  
## <a name="examples"></a>Példák
  
  Az alábbi példa bemutatja, hogyan ellenőrizze, hogy egy pont érvényes ST_VALID használatával.  
  
  Például ezen a ponton rendelkezik egy földrajzi szélességének értéke, amely nem szerepel az érvényes tartomány értékkel [-90 és 90], ezért a lekérdezés hamis értéket adja vissza.  
  
  A GeoJSON specifikációnak poligonok, szükséges, hogy a megadott utolsó koordináta pár legyen ugyanaz, mint az első zárt alakzatot hozhat létre. Sokszög belül pontok óramutató járásával ellentétes irányban sorrendben kell adni. Egy megadott óramutató sorrendben sokszög benne a régió inverzét jelöli.  
  
```sql
SELECT ST_ISVALID({ "type": "Point", "coordinates": [31.9, -132.8] }) AS b 
```  
  
 Íme az eredményhalmaz.  
  
```json
[{ "b": false }]  
```  

## <a name="next-steps"></a>További lépések

- [Térbeli függvények Azure Cosmos DB](sql-query-spatial-functions.md)
- [Rendszerfunkciók Azure Cosmos DB](sql-query-system-functions.md)
- [Bevezetés a Azure Cosmos DBba](introduction.md)
