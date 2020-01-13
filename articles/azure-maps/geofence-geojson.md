---
title: GeoJSON adatformátuma geokerítésen | Microsoft Azure térképek
description: Ebből a cikkből megtudhatja, hogyan készítse elő a geokerítésen-adatait, amelyek felhasználhatók a Microsoft Azure Maps GET és POST Geokerítésen API használatával.
author: walsehgal
ms.author: v-musehg
ms.date: 02/14/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.openlocfilehash: f853962bba7302affd78d5ef267460893ea80a33
ms.sourcegitcommit: f9601bbccddfccddb6f577d6febf7b2b12988911
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/12/2020
ms.locfileid: "75911591"
---
# <a name="geofencing-geojson-data"></a>Geokerítések GeoJSON-adatbázis

A Azure Maps [Get geokerítésen](/rest/api/maps/spatial/getgeofence) és [post geokerítésen](/rest/api/maps/spatial/postgeofence) API-k lehetővé teszik egy koordináta közelségének lekérését egy megadott geokerítésen vagy kerítések készletéhez képest. Ez a cikk részletesen ismerteti a Azure Maps GET és POST API-ban felhasználható geokerítésen-adatok előkészítését.

A geokerítésen vagy geofences vonatkozó információkat a rendszer a [rfc7946](https://tools.ietf.org/html/rfc7946)-ben definiált `Feature` objektum és `FeatureCollection` objektum alapján jeleníti meg `GeoJSON` formátumban. Emellett:

* A GeoJSON objektum típusa lehet egy `Feature` objektum vagy egy `FeatureCollection` objektum.
* A geometriai objektum típusa lehet `Point`, `MultiPoint`, `LineString`, `MultiLineString`, `Polygon`, `MultiPolygon`és `GeometryCollection`.
* A szolgáltatás összes tulajdonságának tartalmaznia kell egy `geometryId`, amely a geokerítésen azonosítására szolgál.
* A szolgáltatásnak `Point`, `MultiPoint`, `LineString`, `MultiLineString` kell tartalmaznia `radius` a tulajdonságok között. `radius` az érték mérése méterben történik, a `radius` érték 1 és 10000 közötti tartományba esik.
* A `polygon` és `multipolygon` geometriai típussal rendelkező szolgáltatás nem rendelkezik RADIUS-tulajdonsággal.
* a `validityTime` egy opcionális tulajdonság, amely lehetővé teszi a felhasználó számára a geokerítésen-adatmennyiség lejárati idejét és érvényességi időtartamát. Ha nincs megadva, az adatértékek soha nem járnak le, és mindig érvényesek.
* A `expiredTime` a geokerítések adatok lejárati dátuma és időpontja. Ha a kérelemben szereplő `userTime` értéke későbbi ennél az értéknél, a megfelelő geokerítésen-adatmennyiség lejárt adatként minősül, és nem kérdezi le. Ekkor a geokerítésen-geometryId a geokerítésen-válaszban található `expiredGeofenceGeometryId` tömbbe kerül.
* A `validityPeriod` a geokerítésen érvényességi időtartamának listája. Ha a kérelemben szereplő `userTime` értéke az érvényességi időtartamon kívül esik, a megfelelő geokerítésen-érték érvénytelennek minősül, és nem lesz lekérdezve. A geokerítésen-geometryId a geokerítésen-válaszon belül szerepel `invalidPeriodGeofenceGeometryId` tömbben. A következő táblázat a validityPeriod elem tulajdonságait mutatja be.

| Név | Type (Típus) | Szükséges  | Leírás |
| :------------ |:------------: |:---------------:| :-----|
| startTime | Dátum/idő  | igaz | Az érvényességi időtartam kezdő dátumának időpontja. |
| endTime   | Dátum/idő  | igaz |  Az érvényességi időszak befejezési dátumának időpontja. |
| naptárkivételhez RecurrenceType érték | sztring | false |   Az időszak ismétlődési típusa Az érték lehet `Daily`, `Weekly`, `Monthly`vagy `Yearly`. Az alapértelmezett érték `Daily`.|
| businessDayOnly | Logikai | false |  Jelezze, hogy az adatai csak munkanapokon érvényesek-e. Az alapértelmezett érték `false`.|


* Az összes koordináta-érték a (`WGS84`ban definiált [hosszúsági, szélességi] értéket jelöli.
* Minden olyan szolgáltatáshoz, amely `MultiPoint`, `MultiLineString`, `MultiPolygon` vagy `GeometryCollection`tartalmaz, a tulajdonságok az összes elemre vonatkoznak. például: a `MultiPoint` összes pontja ugyanazt a sugarat fogja használni több kör alakú geokerítésen létrehozásához.
* A pont-kör forgatókönyvben a kör geometriája egy olyan `Point` geometriai objektum használatával ábrázolható, amely a [GeoJSON geometriák kiterjesztésével lett kibővítve](https://docs.microsoft.com/azure/azure-maps/extend-geojson).      

A következő példa egy minta-kérelem törzse, amely a `GeoJSON` kör alakú geokerítésen geometriája egy középpont és egy sugár használatával. A geokerítésen-adatok érvényes időtartama 2018-10-22, 9:00 és 17:00 között, a hétvégén kívül minden nap megismétlődik. `expiredTime` jelzi, hogy ez a geokerítésen-érték lejártnak minősül, ha a kérelemben szereplő `userTime` későbbi, mint `2019-01-01`.  

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
