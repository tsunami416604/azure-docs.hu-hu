---
title: Bevezetés az Azure Stream Analytics térinformatikai függvényeibe
description: Ez a cikk az Azure Stream Analytics-feladatokban használt térinformatikai függvényeket ismerteti.
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 12/06/2018
ms.openlocfilehash: f47f34b60c858bb9a0feafd25176e4a811046630
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75426220"
---
# <a name="introduction-to-stream-analytics-geospatial-functions"></a>Bevezetés a Stream Analytics térinformatikai funkcióiba

Az Azure Stream Analytics térinformatikai funkciói valós idejű elemzést tesznek lehetővé a térinformatikai adatok streamelése során. Néhány sornyi kóddal kifejleszthet egy éles szintű megoldást összetett forgatókönyvekhez. 

Példák a térinformatikai függvények előnyeit élvező forgatókönyvekre:

* Fuvarmegosztás
* Flottamenedzsment
* Eszközkövetés
* Geo-kerítés
* Telefonkövetés a mobiloldalakközött

A Stream Analytics lekérdezési nyelvének hét beépített térinformatikai függvénye van: **CreateLineString**, **CreatePoint**, **CreatePolygon**, **ST_DISTANCE**, **ST_OVERLAPS**, **ST_INTERSECTS**és **ST_WITHIN**.

## <a name="createlinestring"></a>CreateLineString

A `CreateLineString` függvény pontokat fogad el, és egy GeoJSON LineString karakterláncot ad vissza, amely vonalként ábrázolható a térképen. LineString létrehozásához legalább két ponttal kell rendelkeznie. A LineString pontok sorrendben lesznek csatlakoztatva.

A következő `CreateLineString` lekérdezés segítségével hozzon létre egy LineString három pontot. Az első pont a streamelési bemeneti adatokból jön létre, míg a másik kettő manuálisan jön létre.

```SQL 
SELECT  
     CreateLineString(CreatePoint(input.latitude, input.longitude), CreatePoint(10.0, 10.0), CreatePoint(10.5, 10.5))  
FROM input  
```  

### <a name="input-example"></a>Példa bevitelre  
  
|Szélesség|Hosszúság|  
|--------------|---------------|  
|3.0|-10.2|  
|-87.33|20.2321|  
  
### <a name="output-example"></a>Példa kimenetre  

 {"type" : "LineString", "koordináták" : [ -10.2, 3.0], [10.0, 10.0], [10.5, 10.5] ]}

 {"type" : "LineString", "koordináták" : [ [20.2321, -87.33], [10.0, 10.0], [10.5, 10.5] ]}

További információért látogasson el a [CreateLineString](https://docs.microsoft.com/stream-analytics-query/createlinestring) hivatkozásra.

## <a name="createpoint"></a>CreatePoint

A `CreatePoint` függvény egy szélességi és hosszúsági fokot fogad el, és egy GeoJSON-pontot ad vissza, amely térképen ábrázolható. A szélességi és hosszúsági foknak **úszós** adattípusnak kell lennie.

A következő példalekérdezés segítségével `CreatePoint` hozzon létre egy pontot a szélességi és hosszúsági adatok használatával.

```SQL 
SELECT  
     CreatePoint(input.latitude, input.longitude)  
FROM input 
```  

### <a name="input-example"></a>Példa bevitelre  
  
|Szélesség|Hosszúság|  
|--------------|---------------|  
|3.0|-10.2|  
|-87.33|20.2321|  
  
### <a name="output-example"></a>Példa kimenetre
  
 {"type" : "Pont", "koordináták" : [-10.2, 3.0]}  
  
 {"type" : "Pont", "koordináták" : [20.2321, -87.33]}  

További információért látogasson [CreatePoint](https://docs.microsoft.com/stream-analytics-query/createpoint) el a CreatePoint-hivatkozásra.

## <a name="createpolygon"></a>CreatePolygon

A `CreatePolygon` függvény pontokat fogad el, és GeoJSON sokszögrekordot ad vissza. A pontok sorrendjének a jobb oldali gyűrű tájolását kell követnie, vagy az óramutató járásával ellentétes irányban. Képzeld el, hogy egyik pontból a másikba sétálsz abban a sorrendben, ahogy bejelentették őket. A poligon középpontja egész idő alatt balra van.

A következő példalekérdezés három pontból származó sokszög létrehozásához használja. `CreatePolygon` Az első két pont manuálisan jön létre, és az utolsó pont a bemeneti adatokból jön létre.

```SQL 
SELECT  
     CreatePolygon(CreatePoint(input.latitude, input.longitude), CreatePoint(10.0, 10.0), CreatePoint(10.5, 10.5), CreatePoint(input.latitude, input.longitude))  
FROM input  
```  

### <a name="input-example"></a>Példa bevitelre  
  
|Szélesség|Hosszúság|  
|--------------|---------------|  
|3.0|-10.2|  
|-87.33|20.2321|  
  
### <a name="output-example"></a>Példa kimenetre  

 {"type" : "Sokszög", "koordináták" : [[-10.2, 3.0], [10.0, 10.0], [10.5, 10.5], [-10.2, 3.0] ]]}
 
 {"type" : "Sokszög", "koordináták" : [[ [[ [20.2321, -87.33], [10.0, 10.0], [10.5, 10.5], [20.2321, -87.33] ]]}

További információ: [CreatePolygon](https://docs.microsoft.com/stream-analytics-query/createpolygon) reference.


## <a name="st_distance"></a>ST_DISTANCE
A `ST_DISTANCE` függvény két pont közötti távolságot adja vissza méterben. 

A következő `ST_DISTANCE` lekérdezés egy esemény létrehozásához használ, amikor egy benzinkút kevesebb, mint 10 km-re van az autótól.

```SQL
SELECT Cars.Location, Station.Location 
FROM Cars c  
JOIN Station s ON ST_DISTANCE(c.Location, s.Location) < 10 * 1000
```

További információért látogasson el a [ST_DISTANCE](https://docs.microsoft.com/stream-analytics-query/st-distance) hivatkozásra.

## <a name="st_overlaps"></a>ST_OVERLAPS
A `ST_OVERLAPS` függvény két poligont hasonlít össze. Ha a sokszögek átfedik egymást, a függvény 1-es értéket ad vissza. A függvény 0 értéket ad vissza, ha a sokszögek nem fedik át egymást. 

A következő `ST_OVERLAPS` lekérdezés egy esemény létrehozásához használ, ha egy épület egy lehetséges elárasztási zónán belül van.

```SQL
SELECT Building.Polygon, Building.Polygon 
FROM Building b 
JOIN Flooding f ON ST_OVERLAPS(b.Polygon, b.Polygon) 
```

A következő példa lekérdezés létrehoz egy eseményt, amikor egy vihar felé egy autó.

```SQL
SELECT Cars.Location, Storm.Course
FROM Cars c, Storm s
JOIN Storm s ON ST_OVERLAPS(c.Location, s.Course)
```

Ha többet szeretne megtudni, látogasson el a [ST_OVERLAPS](https://docs.microsoft.com/stream-analytics-query/st-overlaps) referencia.

## <a name="st_intersects"></a>ST_INTERSECTS
A `ST_INTERSECTS` függvény két LineString-et hasonlít össze. Ha a LineString metszi egymást, akkor a függvény 1 értéket ad vissza. A függvény 0 értéket ad vissza, ha a LineString nem metszi egymást.

A következő példalekérdezés annak meghatározására használja, `ST_INTERSECTS` hogy egy burkolt út metszik-e a földutat.

```SQL 
SELECT  
     ST_INTERSECTS(input.pavedRoad, input.dirtRoad)  
FROM input  
```  

### <a name="input-example"></a>Példa bevitelre  
  
|datacenterArea|viharTerület|  
|--------------------|---------------|  
|{"type":"LineString", "koordináták": [ [-10.0, 0.0], [0.0, 0.0], [10.0, 0.0] ]}|{"type":"LineString", "koordináták": [ [ [0.0, 10.0], [0.0, 0.0], [0.0, -10.0] ]}|  
|{"type":"LineString", "koordináták": [ [-10.0, 0.0], [0.0, 0.0], [10.0, 0.0] ]}|{"type":"LineString", "koordináták": [ [-10.0, 10.0], [0.0, 10.0], [10.0, 10.0] ]}|  
  
### <a name="output-example"></a>Példa kimenetre  

 1  
  
 0  

További információért látogasson el a [ST_INTERSECTS](https://docs.microsoft.com/stream-analytics-query/st-intersects) hivatkozásra.

## <a name="st_within"></a>ST_WITHIN
A `ST_WITHIN` függvény határozza meg, hogy egy pont vagy sokszög sokszögen belül van-e. Ha a sokszög tartalmazza a pontot vagy a sokszöget, a függvény 1-et ad vissza. A függvény 0-t ad vissza, ha a pont vagy a sokszög nem a deklarált sokszögben található.

A következő példalekérdezés annak meghatározására használja, `ST_WITHIN` hogy a szállítási célpont az adott raktári sokszögön belül van-e.

```SQL 
SELECT  
     ST_WITHIN(input.deliveryDestination, input.warehouse)  
FROM input 
```  

### <a name="input-example"></a>Példa bevitelre  
  
|deliveryDestination|Raktár|  
|-------------------------|---------------|  
|{"type":"Pont", "koordináták": [76.6, 10.1]}|{"type":"Sokszög", "koordináták": [ [ [0.0, 0.0], [10.0, 0.0], [10.0, 10.0], [0.0, 10.0], [0.0, 0.0] ]}|  
|{"type":"Point", "koordináták": [15.0, 15.0]}|{"type":"Sokszög", "koordináták": [ [ [10.0, 10.0], [20.0, 10.0], [20.0, 20.0], [10.0, 20.0], [10.0, 10.0] ]}|  
  
### <a name="output-example"></a>Példa kimenetre  

 0  
  
 1  

Ha többet szeretne megtudni, látogasson el a [ST_WITHIN](https://docs.microsoft.com/stream-analytics-query/st-within) referencia.

## <a name="next-steps"></a>További lépések

* [Bevezetés az Azure Stream Analytics szolgáltatásba](stream-analytics-introduction.md)
* [Get started using Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md) (Bevezetés az Azure Stream Analytics használatába)
* [Scale Azure Stream Analytics jobs (Azure Stream Analytics-feladatok méretezése)](stream-analytics-scale-jobs.md)
* [Azure Stream Analytics Query Language Reference (Referencia az Azure Stream Analytics lekérdezési nyelvhez)](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference)
* [Az Azure Stream Analytics felügyeleti REST API referenciája](https://msdn.microsoft.com/library/azure/dn835031.aspx)
