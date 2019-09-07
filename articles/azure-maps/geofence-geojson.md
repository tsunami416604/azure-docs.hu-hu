---
title: Geokerítésen GeoJSON adatformátuma Azure Mapsban | Microsoft Docs
description: Ismerkedjen meg a Geokerítésen GeoJSON adatformátumával Azure Maps
author: walsehgal
ms.author: v-musehg
ms.date: 02/14/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.openlocfilehash: 5946180c161a38a30f44e235ce0b626fd70a5400
ms.sourcegitcommit: 97605f3e7ff9b6f74e81f327edd19aefe79135d2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/06/2019
ms.locfileid: "70735146"
---
# <a name="geofencing-geojson-data"></a>Geokerítések GeoJSON-adatbázis

A Azure Maps [Get geokerítésen](/rest/api/maps/spatial/getgeofence) és [post geokerítésen](/rest/api/maps/spatial/postgeofence) API-k lehetővé teszik egy koordináta közelségének lekérését egy megadott geokerítésen vagy kerítések készletéhez képest. Ez a cikk részletesen ismerteti a Azure Maps GET és POST API-ban felhasználható geokerítésen-adatok előkészítését.

A geokerítésen vagy `Feature` geofences vonatkozó adatmennyiséget objektum és `FeatureCollection` objektum `GeoJSON` jelképezi, formátum szerint, amely a [rfc7946](https://tools.ietf.org/html/rfc7946)-ben van meghatározva. Emellett:

* A GeoJSON objektum típusa `Feature` lehet objektum `FeatureCollection` vagy objektum.
* A geometria `Point`objektum típusa lehet a `LineString` `MultiPoint` `MultiLineString`,,,,, és`GeometryCollection`. `Polygon` `MultiPolygon`
* A szolgáltatás összes tulajdonságának tartalmaznia `geometryId`kell a-t, amely a geokerítésen azonosítására szolgál.
* `Point` A-`radius` ben a, a, a tulajdonságban szerepelniekellaszolgáltatásnak.`MultiLineString` `MultiPoint` `LineString` `radius`az érték mérése méterben történik `radius` , az érték 1-től 10000-ig terjed.
* A és `polygon` `multipolygon` a geometria típusú szolgáltatáshoz nem tartozik RADIUS-tulajdonság.
* `validityTime`a egy nem kötelező tulajdonság, amely lehetővé teszi a felhasználó számára a geokerítésen-adatmennyiség érvényességi időtartamának és érvényességi idejének beállítását. Ha nincs megadva, az adatértékek soha nem járnak le, és mindig érvényesek.
* A `expiredTime` a geokerítések-adatok lejárati dátuma és időpontja. Ha a kérelemben `userTime` szereplő érték későbbi ennél az értéknél, a megfelelő geokerítésen-adatmennyiség lejárt adatként minősül, és a rendszer nem kérdezi le. Ekkor a geokerítésen-geometryId a geokerítésen- `expiredGeofenceGeometryId` válaszban található tömbbe kerül.
* A `validityPeriod` a geokerítésen érvényességi időtartamának listája. Ha a kérelemben `userTime` szereplő érték az érvényességi időtartamon kívül esik, a megfelelő geokerítésen-adatmennyiség érvénytelennek minősül, és nem lesz lekérdezve. A geokerítésen-geometryId a geokerítésen- `invalidPeriodGeofenceGeometryId` válaszon belüli tömb tartalmazza. A következő táblázat a validityPeriod elem tulajdonságait mutatja be.

| Name (Név) | Type | Kötelező  | Leírás |
| :------------ |:------------: |:---------------:| :-----|
| startTime | Datetime  | true | Az érvényességi időtartam kezdő dátumának időpontja. |
| endTime   | Datetime  | true |  Az érvényességi időszak befejezési dátumának időpontja. |
| recurrenceType | Karakterlánc | false |   Az időszak ismétlődési típusa Az `Daily`érték lehet `Weekly` `Yearly`:,, vagy. `Monthly` Az `Daily`alapértelmezett érték:.|
| businessDayOnly | Logikai | false |  Jelezze, hogy az adatai csak munkanapokon érvényesek-e. Az `false`alapértelmezett érték:.|


* Az összes koordináta-érték [hosszúság, szélesség] szerint van megadva `WGS84`a ben.
* Minden olyan szolgáltatás esetében, amely `MultiPoint`tartalmazza `MultiLineString` `MultiPolygon` a, a `GeometryCollection`, vagy a tulajdonságokat, a rendszer az összes elemre alkalmazza a tulajdonságokat. például: Az összes pontja `MultiPoint` ugyanazokat a RADIUS-ket fogja használni a több kör geokerítésen létrehozásához.
* A pont-kör forgatókönyvben a kör alakú geometria egy `Point` geometriai objektum használatával ábrázolható, amely a [GeoJSON geometriák kiterjesztésével lett kibővítve](https://docs.microsoft.com/azure/azure-maps/extend-geojson).      

A következő példa egy minta-kérelem törzse, amely egy geokerítésen és egy sugarat `GeoJSON` használó kör alakú geokerítésen geometriát jelöl. A geokerítésen-adatok érvényes időtartama 2018-10-22, 9:00 és 17:00 között, a hétvégén kívül minden nap megismétlődik. `expiredTime`azt jelzi, hogy ez a geokerítésen-érték lejártnak minősül, ha `userTime` a kérelem későbbi, mint. `2019-01-01`  

```json
{
    "type": "Feature",
    "geometry": {
        "type": "Point",
        "coordinates": [-122.126986, 47.639754]
    },
    "properties": {
        "geometryId" : "1",
        "subType": "Circle",
        "radius": 500,
        "validityTime": 
        {
            "expiredTime": "2019-01-01T00:00:00",
            "validityPeriod": [
                {
                    "startTime": "2018-10-22T09:00:00",
                    "endTime": "2018-10-22T17:00:00",
                    "recurrenceType": "Daily",
                    "recurrenceFrequency": 1,
                    "businessDayOnly": true
                }
            ]
        }
    }
}
```
