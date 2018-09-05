---
title: A térinformatikai függvények az Azure Stream Analytics bemutatása
description: Ez a cikk ismerteti az Azure Stream Analytics-feladatok által használt térinformatikai függvényeket.
services: stream-analytics
author: mamccrea
ms.author: mamccrea
manager: kfile
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 09/04/2018
ms.openlocfilehash: 02d1f551c7ec2856bbfce65c5397f454f6b9d5be
ms.sourcegitcommit: cb61439cf0ae2a3f4b07a98da4df258bfb479845
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/05/2018
ms.locfileid: "43703661"
---
# <a name="introduction-to-stream-analytics-geospatial-functions"></a>Bevezetés a Stream Analytics a térinformatikai függvények

A térinformatikai függvények az Azure Stream Analytics szolgáltatásban engedélyezze a streamelési térinformatikai adatok valós idejű elemzését. Mindössze néhány sornyi kóddal összetett forgatókönyvek termelési szintű megoldást fejleszthet. 

A térinformatikai függvények is kihasználó forgatókönyvek például:

* Autómegosztások
* Flottakezelés
* Eszközkövetés
* Geokerítés-
* Telefonos követési cella helyek között

Stream Analytics lekérdezési nyelv hét beépített térinformatikai függvényekkel rendelkezik: **CreateLineString**, **CreatePoint**, **CreatePolygon**, **ST_DISTANCE** , **ST_OVERLAPS**, **ST_INTERSECTS**, és **ST_WITHIN**.

## <a name="createlinestring"></a>CreateLineString

A `CreateLineString` függvény elfogad pontok, és adja vissza egy GeoJSON LineString, amely egy térképen vonal algoritmus. Hozhat létre egy LineString legalább két pontból kell rendelkeznie. A LineString pontok csatlakozik ahhoz.

Az alábbi lekérdezés használja `CreateLineString` hozhat létre egy LineString, három pontot használ. Az első pont létrehozása a streamadatok bemeneti, míg a másik két kézzel létrehozott.

```SQL 
SELECT  
     CreateLineString(CreatePoint(input.latitude, input.longitude), CreatePoint(10.0, 10.0), CreatePoint(10.5, 10.5))  
FROM input  
```  

### <a name="input-example"></a>A bemeneti példa  
  
|Szélesség|Hosszúság|  
|--------------|---------------|  
|3.0|-10.2|  
|-87.33|20.2321|  
  
### <a name="output-example"></a>Példa a kimenetre  

 {"type": "LineString", "koordináták": [[-10.2, 3.0-s], [10.0, 10.0-s verzióját], [10,5, 10,5]]}

 {"type": "LineString", "koordináták": [[20.2321,-87.33], [10.0, 10.0-s verzióját], [10,5, 10,5]]}

További tudnivalókért látogasson el a [CreateLineString](https://msdn.microsoft.com/azure/stream-analytics/reference/createlinestring) hivatkozást.

## <a name="createpoint"></a>CreatePoint

A `CreatePoint` függvény elfogad egy szélességi és hosszúsági, és adja vissza egy GeoJSON-pont, amely egy térképen algoritmus. A földrajzi szélességet és hosszúságot kell lennie egy **lebegőpontos** adattípus.

Használja a következő példalekérdezés `CreatePoint` földrajzi szélesség és hosszúság a bemeneti adatok streaming használatával pont létrehozásához.

```SQL 
SELECT  
     CreatePoint(input.latitude, input.longitude)  
FROM input 
```  

### <a name="input-example"></a>A bemeneti példa  
  
|Szélesség|Hosszúság|  
|--------------|---------------|  
|3.0|-10.2|  
|-87.33|20.2321|  
  
### <a name="output-example"></a>Példa a kimenetre
  
 {"type": "Pont", "koordináták": [-10.2, 3.0-s]}  
  
 {"type": "Pont", "koordináták": [20.2321,-87.33]}  

További tudnivalókért látogasson el a [CreatePoint](https://msdn.microsoft.com/azure/stream-analytics/reference/createpoint) hivatkozást.

## <a name="createpolygon"></a>CreatePolygon

A `CreatePolygon` függvény pontok fogad és a egy GeoJSON-poligon rekordot ad vissza. A pontok sorrendje kell követnie a kör jobb tájolású, vagy az óramutató járásával ellentétes irányban. Az Imagine walking egyik helyről egy másikra a sorrendben megadva azok. A sokszög középpontjának a bal oldali lenne, a teljes időt.

Használja a következő példalekérdezés `CreatePolygon` sokszög a három pont létrehozásához. Az első két pontot hoz létre manuálisan, és a legutóbbi pont jön létre a bemeneti adatok.

```SQL 
SELECT  
     CreatePolygon(CreatePoint(input.latitude, input.longitude), CreatePoint(10.0, 10.0), CreatePoint(10.5, 10.5), CreatePoint(input.latitude, input.longitude))  
FROM input  
```  

### <a name="input-example"></a>A bemeneti példa  
  
|Szélesség|Hosszúság|  
|--------------|---------------|  
|3.0|-10.2|  
|-87.33|20.2321|  
  
### <a name="output-example"></a>Példa a kimenetre  

 {"type": "Sokszög", "koordináták": [[[-10.2, 3.0-s], [10.0, 10.0-s verzióját], [10,5, 10,5], [-10.2, 3.0-s]]]}
 
 {"type": "Sokszög", "koordináták": [[[20.2321,-87.33], [10.0, 10.0-s verzióját], [10,5, 10,5], [20.2321,-87.33]]]}

További tudnivalókért látogasson el a [CreatePolygon](https://msdn.microsoft.com/azure/stream-analytics/reference/createpolygon) hivatkozást.


## <a name="stdistance"></a>ST_DISTANCE
A `ST_DISTANCE` függvény méterben két pont közötti távolságot adja vissza. 

Az alábbi lekérdezés használja `ST_DISTANCE` egy eseményt, amikor egy benzinkutat kevesebb mint 10 km-re az autó létrehozásához.

```SQL
SELECT Cars.Location, Station.Location 
FROM Cars c  
JOIN Station s ON ST_DISTANCE(c.Location, s.Location) < 10 * 1000
```

További tudnivalókért látogasson el a [ST_DISTANCE](https://msdn.microsoft.com/azure/stream-analytics/reference/st-distance) hivatkozást.

## <a name="stoverlaps"></a>ST_OVERLAPS
A `ST_OVERLAPS` függvény két poligonok hasonlítja össze. Ha a sokszög átfedésben vannak, a függvény egy 1 adja vissza. A függvény 0 adja vissza, ha a poligonok nem lehetnek átfedésben. 

Az alábbi lekérdezés használja `ST_OVERLAPS` létrehozni egy eseményt, amikor egy épület-elárasztás zóna lehetséges belül van.

```SQL
SELECT Building.Polygon, Building.Polygon 
FROM Building b 
JOIN Flooding f ON ST_OVERLAPS(b.Polygon, b.Polygon) 
```

A következő példalekérdezés generál egy eseményt, amikor egy storm felé egy autó fejléc.

```SQL
SELECT Cars.Location, Storm.Course
FROM Cars c, Storm s
JOIN Storm s ON ST_OVERLAPS(c.Location, s.Course)
```

További tudnivalókért látogasson el a [ST_OVERLAPS](https://msdn.microsoft.com/azure/stream-analytics/reference/st-overlaps) hivatkozást.

## <a name="stintersects"></a>ST_INTERSECTS
A `ST_INTERSECTS` függvény két LineString hasonlítja össze. Ha a LineString metszetoszlop, a függvény 1 adja vissza. A függvény 0 adja vissza, ha a LineString nem átfedésben.

Használja a következő példalekérdezés `ST_INTERSECTS` meghatározni, ha egy kövezett utat metszi a terepmotor úton.

```SQL 
SELECT  
     ST_INTERSECTS(input.pavedRoad, input.dirtRoad)  
FROM input  
```  

### <a name="input-example"></a>A bemeneti példa  
  
|datacenterArea|stormArea|  
|--------------------|---------------|  
|{"type": "LineString", "koordináták": [[-10.0, 0.0], [0.0, 0.0], [10.0, 0.0]]}|{"type": "LineString", "koordináták": [[0.0, 10.0], [0.0, 0.0], [0.0,-10.0]]}|  
|{"type": "LineString", "koordináták": [[-10.0, 0.0], [0.0, 0.0], [10.0, 0.0]]}|{"type": "LineString", "koordináták": [[-10.0, 10.0-s verzióját], [0.0, 10.0], [10.0, 10.0-s verzióját]]}|  
  
### <a name="output-example"></a>Példa a kimenetre  

 1  
  
 0  

További tudnivalókért látogasson el a [ST_INTERSECTS](https://msdn.microsoft.com/azure/stream-analytics/reference/st-intersects) hivatkozást.

## <a name="stwithin"></a>ST_WITHIN
A `ST_WITHIN` függvény meghatározza, hogy egy pontot vagy a sokszög egy sokszög belül. Ha a sokszög pont vagy sokszög tartalmazza, a függvény 1 adja vissza. A függvény 0 vissza, ha a pont és a sokszög nem a deklarált sokszög belül található.

Használja a következő példalekérdezés `ST_WITHIN` meghatározni, hogy a megadott adatraktár sokszög belül van-e a tartalomkézbesítési célhelyre.

```SQL 
SELECT  
     ST_WITHIN(input.deliveryDestination, input.warehouse)  
FROM input 
```  

### <a name="input-example"></a>A bemeneti példa  
  
|deliveryDestination|adatraktár|  
|-------------------------|---------------|  
|{"type": "Pont", "koordináták": [76.6, 10.1]}|{"type": "Sokszög", "koordináták": [[0.0, 0.0], [10.0, 0.0], [10.0, 10.0-s verzióját], [0.0, 10.0], [0.0, 0.0]]}|  
|{"type": "Pont", "koordináták": [15.0, 15.0]}|{"type": "Sokszög", "koordináták": [[10.0, 10.0-s verzióját], [20,0, 10.0], [20.0, 20.0], [10.0, 20,0], [10.0, 10.0-s verzióját]]}|  
  
### <a name="output-example"></a>Példa a kimenetre  

 0  
  
 1  

További tudnivalókért látogasson el a [ST_WITHIN](https://msdn.microsoft.com/azure/stream-analytics/reference/st-within) hivatkozást.

## <a name="next-steps"></a>További lépések

* [Az Azure Stream Analytics bemutatása](stream-analytics-introduction.md)
* [Get started using Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md) (Bevezetés az Azure Stream Analytics használatába)
* [Scale Azure Stream Analytics jobs](stream-analytics-scale-jobs.md) (Azure Stream Analytics-feladatok méretezése)
* [Azure Stream Analytics Query Language Reference](https://msdn.microsoft.com/library/azure/dn834998.aspx) (Referencia az Azure Stream Analytics lekérdezési nyelvhez)
* [Az Azure Stream Analytics felügyeleti REST API referenciája](https://msdn.microsoft.com/library/azure/dn835031.aspx)