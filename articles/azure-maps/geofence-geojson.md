---
title: GeoJSON adatformátum geokerítéshez | Microsoft Azure Maps
description: Ebből a cikkből megtudhatja, hogyan készítheti elő a Geofence adatokat, amelyek a Microsoft Azure Maps GET és a POST Geofence API-t használhatók.
author: philmea
ms.author: philmea
ms.date: 02/14/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.openlocfilehash: 7b9860908dd3bdf3dcda727f350578a97b890cac
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80335623"
---
# <a name="geofencing-geojson-data"></a>Geofencing GeoJSON adatok

Az Azure Maps [GET Geofence](/rest/api/maps/spatial/getgeofence) és [post geokerítés](/rest/api/maps/spatial/postgeofence) API-k lehetővé teszik, hogy egy adott geokerítéshez vagy kerítéskészlethez képest egy koordináta közelségét kérje le. Ez a cikk ismerteti, hogyan készítheti elő az Azure Maps GET és post API-ban használható geokerítés-adatokat.

A geokerítés vagy geokerítés-készlet adatait `Feature` az `FeatureCollection` `GeoJSON` [rfc7946-ban](https://tools.ietf.org/html/rfc7946)meghatározott Objektum és Objektum formátumban ábrázolja. Amellett, hogy ez:

* A GeoJSON objektumtípus `Feature` lehet objektum `FeatureCollection` vagy objektum.
* A Geometriaobjektum típusa `Point` `MultiPoint`lehet `LineString` `MultiLineString`a `Polygon` `MultiPolygon`, `GeometryCollection`, , , , , , és .
* Minden jellemzőtulajdonságnak `geometryId`tartalmaznia kell egy , amely a geokerítés azonosítására szolgál.
* A `Point`, `MultiPoint` `LineString`, `MultiLineString` , `radius` tulajdonsággal rendelkező szolgáltatásnak tulajdonságokat kell tartalmaznia. `radius`az értéket méterben `radius` mérik, az érték 1 és 10000 között van.
* A `polygon` jellemző `multipolygon` és a geometriatípus nem rendelkezik sugártulajdonsággal.
* `validityTime`egy választható tulajdonság, amely lehetővé teszi, hogy a felhasználó beállítsa a lejárt időt és érvényességi időt a geokerítés-adatokhoz. Ha nincs megadva, az adatok soha nem járnak le, és mindig érvényesek.
* A `expiredTime` a geofencing adatok lejárati dátuma és időpontja. Ha a `userTime` kérelem értéke későbbi, mint ez az érték, a megfelelő geokerítés-adatok lejárt adatnak minősülnek, és nem lesznek lekérdezve. Amelyen a geokerítés-adatok geometriaazonosítója szerepelni `expiredGeofenceGeometryId` fog a geokerítés-válasz tömbjében.
* A `validityPeriod` a geokerítés érvényességi idejének listája. Ha a `userTime` kérelem értéke kívül esik az érvényességi időtartamon, a megfelelő geokerítés-adatok érvénytelennek minősülnek, és nem lesznek lekérdezve. A geokerítés-adatok geometriaazonosítója `invalidPeriodGeofenceGeometryId` a geokerítés-választömbben található. Az alábbi táblázat a validityPeriod elem tulajdonságait mutatja be.

| Név | Típus | Kötelező  | Leírás |
| :------------ |:------------: |:---------------:| :-----|
| startTime | Datetime  | igaz | Az érvényességi időszak kezdő dátuma. |
| endTime   | Datetime  | igaz |  Az érvényességi időszak záró dátuma. |
| ismétlődéstípusa | sztring | hamis |   Az időszak ismétlődési típusa. Az érték `Daily`lehet `Weekly` `Monthly`, `Yearly`, , vagy . Az alapértelmezett `Daily`érték a .|
| businessDayOnly | Logikai | hamis |  Adja meg, hogy az adatok csak munkanapon belül érvényesek-e. Az alapértelmezett `false`érték a .|


* Az összes koordináta-érték a programban `WGS84`meghatározott [hosszúsági szélességi fok] értékként jelenik meg.
* Minden egyes funkcióhoz, `MultiLineString` `MultiPolygon` amely `GeometryCollection`a `MultiPoint`, , , vagy a tulajdonságokat tartalmazza, a tulajdonságok az összes elemre vonatkoznak. például: Az összes `MultiPoint` pont ugyanazt a sugarat fogja használni, hogy több kör geokerítést alkosson.
* Pontkör forgatókönyvesetén körgeometria ábrázolható egy `Point` geometriai objektum mal, amelynek tulajdonságai a [GeoJSON geometriák kiterjesztése kitágítása során](https://docs.microsoft.com/azure/azure-maps/extend-geojson)vannak kidolgozva.      

A következőkben egy geokerítés mintakérelem-törzse látható, `GeoJSON` amely egy középpont és egy sugár használatával kör geokerítés geometriaként jelenik meg. A geokerítés-adatok érvényes időtartama 2018-10-22, 9:00 és 17:00 óra között kezdődik, a hétvége kivételével minden nap ismétlődik. `expiredTime`azt jelzi, hogy ez a geokerítés-adatok lejártnak minősülnek, ha `userTime` a kérelemben későbbi, mint `2019-01-01`a .  

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
