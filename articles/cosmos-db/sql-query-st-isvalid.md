---
title: Azure Cosmos DB lekérdezési nyelv ST_ISVALID
description: Ismerkedjen meg a Azure Cosmos DB SQL System Function ST_ISVALIDával.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 8fbddbe82ae13585b8259a66dffaeef8024baf5d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2020
ms.locfileid: "71349362"
---
# <a name="st_isvalid-azure-cosmos-db"></a>ST_ISVALID (Azure Cosmos DB)
 Egy logikai értéket ad vissza, amely azt jelzi, hogy a megadott GeoJSON pont, sokszög vagy LineString kifejezés érvényes-e.  
  
## <a name="syntax"></a>Szintaxis
  
```sql
ST_ISVALID(<spatial_expr>)  
```  
  
## <a name="arguments"></a>Argumentumok
  
*spatial_expr*  
   Egy GeoJSON pont, sokszög vagy LineString kifejezés.  
  
## <a name="return-types"></a>Visszatérési típusok
  
  Egy logikai kifejezést ad vissza.  
  
## <a name="examples"></a>Példák
  
  Az alábbi példa bemutatja, hogyan ellenőrizhető, hogy egy pont érvényes-e ST_VALID használatával.  
  
  Ez a pont például olyan szélességi értékkel rendelkezik, amely nem szerepel a [-90, 90] értékek érvényes tartományában, így a lekérdezés hamis értéket ad vissza.  
  
  A sokszögek esetében a GeoJSON-specifikáció megköveteli, hogy a megadott koordináta-pároknak meg kell egyezniük az elsővel, és létre kell hozni egy bezárt alakzatot. A sokszögben lévő pontokat a megfelelő sorrendben kell megadni. A megegyező sorrendben megadott sokszög a régió inverzét jelöli.  
  
```sql
SELECT ST_ISVALID({ "type": "Point", "coordinates": [31.9, -132.8] }) AS b 
```  
  
 Itt látható az eredményhalmaz.  
  
```json
[{ "b": false }]  
```  

## <a name="next-steps"></a>További lépések

- [Térbeli függvények Azure Cosmos DB](sql-query-spatial-functions.md)
- [Rendszerfunkciók Azure Cosmos DB](sql-query-system-functions.md)
- [Az Azure Cosmos DB bemutatása](introduction.md)
