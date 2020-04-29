---
title: GeoJSON adatformátuma geokerítésen | Microsoft Azure térképek
description: Ebből a cikkből megtudhatja, hogyan készítse elő a geokerítésen-adatait, amelyek felhasználhatók a Microsoft Azure Maps GET és POST Geokerítésen API használatával.
author: philmea
ms.author: philmea
ms.date: 02/14/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.openlocfilehash: 7b9860908dd3bdf3dcda727f350578a97b890cac
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "80335623"
---
# <a name="geofencing-geojson-data"></a>Geokerítések GeoJSON-adatbázis

A Azure Maps [Get geokerítésen](/rest/api/maps/spatial/getgeofence) és [post geokerítésen](/rest/api/maps/spatial/postgeofence) API-k lehetővé teszik egy koordináta közelségének lekérését egy megadott geokerítésen vagy kerítések készletéhez képest. Ez a cikk részletesen ismerteti a Azure Maps GET és POST API-ban felhasználható geokerítésen-adatok előkészítését.

A geokerítésen `Feature` vagy geofences vonatkozó adatmennyiséget objektum és `FeatureCollection` objektum jelképezi, `GeoJSON` formátum szerint, amely a [rfc7946](https://tools.ietf.org/html/rfc7946)-ben van meghatározva. Emellett:

* A GeoJSON objektum típusa lehet `Feature` objektum vagy `FeatureCollection` objektum.
* A geometria objektum `Point`típusa lehet a `MultiPoint` `LineString` `MultiLineString` `Polygon` `MultiPolygon`,,,,, és. `GeometryCollection`
* A szolgáltatás összes tulajdonságának tartalmaznia `geometryId`kell a-t, amely a geokerítésen azonosítására szolgál.
* `LineString` `MultiLineString` A- `radius` ben a, a, a tulajdonságban szerepelnie kell a szolgáltatásnak. `Point` `MultiPoint` `radius`az érték mérése méterben történik `radius` , az érték 1-től 10000-ig terjed.
* A és `polygon` `multipolygon` a geometria típusú szolgáltatáshoz nem tartozik RADIUS-tulajdonság.
* `validityTime`a egy nem kötelező tulajdonság, amely lehetővé teszi a felhasználó számára a geokerítésen-adatmennyiség érvényességi időtartamának és érvényességi idejének beállítását. Ha nincs megadva, az adatértékek soha nem járnak le, és mindig érvényesek.
* A `expiredTime` a geokerítések-adatok lejárati dátuma és időpontja. Ha a kérelemben `userTime` szereplő érték későbbi ennél az értéknél, a megfelelő geokerítésen-adatmennyiség lejárt adatként minősül, és a rendszer nem kérdezi le. Ekkor a geokerítésen-geometryId a geokerítésen- `expiredGeofenceGeometryId` válaszban található tömbbe kerül.
* A `validityPeriod` a geokerítésen érvényességi időtartamának listája. Ha a kérelemben `userTime` szereplő érték az érvényességi időtartamon kívül esik, a megfelelő geokerítésen-adatmennyiség érvénytelennek minősül, és nem lesz lekérdezve. A geokerítésen-geometryId a geokerítésen- `invalidPeriodGeofenceGeometryId` válaszon belüli tömb tartalmazza. A következő táblázat a validityPeriod elem tulajdonságait mutatja be.

| Name (Név) | Típus | Kötelező  | Leírás |
| :------------ |:------------: |:---------------:| :-----|
| startTime | Datetime  | igaz | Az érvényességi időtartam kezdő dátumának időpontja. |
| endTime   | Datetime  | igaz |  Az érvényességi időszak befejezési dátumának időpontja. |
| naptárkivételhez RecurrenceType érték | sztring | hamis |   Az időszak ismétlődési típusa Az `Daily`érték lehet `Weekly` `Monthly`:,, vagy `Yearly`. Az `Daily`alapértelmezett érték:.|
| businessDayOnly | Logikai | hamis |  Jelezze, hogy az adatai csak munkanapokon érvényesek-e. Az `false`alapértelmezett érték:.|


* Az összes koordináta-érték [hosszúság, szélesség] szerint van megadva `WGS84`a ben.
* Minden olyan szolgáltatás esetében, amely `MultiPoint`tartalmazza `MultiLineString` `MultiPolygon` a, a `GeometryCollection`, vagy a tulajdonságokat, a rendszer az összes elemre alkalmazza a tulajdonságokat. például: a (z) minden `MultiPoint` pontja ugyanazt a sugarat fogja használni, hogy több kör alakú geokerítésen.
* A pont-kör forgatókönyvben a kör alakú geometria egy `Point` geometriai objektum használatával ábrázolható, amely a [GeoJSON geometriák kiterjesztésével lett kibővítve](https://docs.microsoft.com/azure/azure-maps/extend-geojson).      

A következő példa egy minta-kérelem törzse, amely egy geokerítésen és egy sugarat `GeoJSON` használó kör alakú geokerítésen geometriát jelöl. A geokerítésen-adatok érvényes időtartama 2018-10-22, 9:00 és 17:00 között, a hétvégén kívül minden nap megismétlődik. `expiredTime`azt jelzi, hogy ez a geokerítésen-érték lejártnak minősül, ha `userTime` a kérelem `2019-01-01`későbbi, mint.  

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
