---
title: ST_ISVALID az Azure Cosmos DB lekérdezési nyelvében
description: Ismerje meg az SQL-rendszer funkció ST_ISVALID az Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 8fbddbe82ae13585b8259a66dffaeef8024baf5d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "71349362"
---
# <a name="st_isvalid-azure-cosmos-db"></a>ST_ISVALID (Azure Cosmos DB)
 Logikai értéket ad vissza, amely azt jelzi, hogy a megadott GeoJSON-pont, Sokszög vagy LineString kifejezés érvényes-e.  
  
## <a name="syntax"></a>Szintaxis
  
```sql
ST_ISVALID(<spatial_expr>)  
```  
  
## <a name="arguments"></a>Argumentumok
  
*spatial_expr*  
   GeoJSON-pont, sokszög vagy LineString kifejezés.  
  
## <a name="return-types"></a>Visszatérési típusok
  
  Logikai kifejezést ad eredményül.  
  
## <a name="examples"></a>Példák
  
  A következő példa bemutatja, hogyan ellenőrizheti, hogy egy pont érvényes-e ST_VALID használatával.  
  
  Ez a pont például olyan szélességi értékkel rendelkezik, amely nem az érvényes értéktartományban van [-90, 90], így a lekérdezés hamis értéket ad vissza.  
  
  Sokszögek esetében a GeoJSON specifikáció megköveteli, hogy az utolsó megadott koordinátapár megegyezik az elsővel, hogy zárt alakzatot hozzon létre. A sokszögen belüli pontokat az óramutató járásával ellentétes sorrendben kell megadni. Az óramutató járásával megegyező sorrendben megadott sokszög a benne lévő terület inverzét jelöli.  
  
```sql
SELECT ST_ISVALID({ "type": "Point", "coordinates": [31.9, -132.8] }) AS b 
```  
  
 Itt van az eredményhalmaz.  
  
```json
[{ "b": false }]  
```  

## <a name="next-steps"></a>További lépések

- [Térbeli függvények Az Azure Cosmos DB](sql-query-spatial-functions.md)
- [Rendszerfüggvények Az Azure Cosmos DB](sql-query-system-functions.md)
- [Bevezetés az Azure Cosmos DB bemutatása](introduction.md)
