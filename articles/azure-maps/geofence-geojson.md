---
title: Az Azure Maps a Geokerítésen GeoJSON adatformátum |} A Microsoft Docs
description: További tudnivalók az Azure Maps a Geokerítésen GeoJSON adatformátum
author: walsehgal
ms.author: v-musehg
ms.date: 02/14/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.openlocfilehash: 6f76eea365311cd6c10dd39054187bf3a07bd595
ms.sourcegitcommit: dd1a9f38c69954f15ff5c166e456fda37ae1cdf2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/07/2019
ms.locfileid: "57568991"
---
# <a name="geofencing-geojson-data"></a>A Geokerítések GeoJSON-adatok

Az Azure Maps [első Geokerítésen](https://docs.microsoft.com/rest/api/maps/spatial/getgeofencepreview) és [POST Geokerítésen](https://docs.microsoft.com/rest/api/maps/spatial/postgeofencepreview) az API-kkal könnyen elérhető viszonyítva megadott geokerítésen koordináta vagy kerítések készletét lekéréséhez. Ez a cikk részletesen, hogyan készíti elő az a geokerítésen adatokat, amelyek az Azure Maps szolgáltatással és a bejegyzés API is használható.

Az adatok a geokerítésen vagy geokerítések készletét képviseli `Feature` objektum és `FeatureCollection` objektumának `GeoJSON` formátum, amelyet a [rfc7946](https://tools.ietf.org/html/rfc7946). Amellett, hogy:

* A GeoJSON objektumtípus lehet egy `Feature` objektum vagy egy `FeatureCollection` objektum.
* Geometriai objektum típusa lehet egy `Point`, `MultiPoint`, `LineString`, `MultiLineString`, `Polygon`, `MultiPolygon`, és `GeometryCollection`.
* Az összes szolgáltatás tulajdonságainak tartalmaznia kell egy `geometryId`, amely a geokerítésen azonosítására szolgál.
* A funkció `Point`, `MultiPoint`, `LineString`, `MultiLineString` tartalmaznia kell `radius` tulajdonságai. `radius` a program méri a mérőszámok, a `radius` érték-1-től tartományok 10000.
* A funkció `polygon` és `multipolygon` geometry típusú nem rendelkezik a radius-tulajdonsággal.
* `validityTime` egy nem kötelező tulajdonsággal, amely lehetővé teszi, hogy a felhasználó értéke lejárati idő és az érvényességi időszak a geokerítésen adatok. Ha nincs megadva, az adatok soha nem jár le, és mindig érvényességét.
* A `expiredTime` a lejárati dátum és idő a geokerítések adatok. Ha az értéke `userTime` a kérésben van később ennél nagyobb értéket, adatok lejárt adatnak minősül, és nem lekérik a megfelelő geokerítésen. A geometryId adatokat fog szerepelni a geokerítésen, amelyre `expiredGeofenceGeometryId` tömb a geokerítésen válasz belül.
* A `validityPeriod` az érvényességi listája a geokerítésen időtartammal. Ha az értéke `userTime` az kérelem esik az érvényességi időszakon kívül, az a geokerítésen vonatkozó adatok számít, érvénytelen, és nem lehet lekérdezni. Az a geokerítésen adatok geometryId szerepel `invalidPeriodGeofenceGeometryId` tömb válasz a geokerítésen belül. Az alábbi táblázat validityPeriod elem tulajdonságait.

| Name (Név) | Typo | Kötelező  | Leírás |
| :------------ |:------------: |:---------------:| :-----|
| startTime | Dátum és idő  | true | A kezdő dátuma és időpontja az érvényességi időszak. |
| endTime   | Dátum és idő  | true |  A záró dátuma és időpontja az érvényességi időszak. |
| recurrenceType | sztring | false |   Az ismétlődési időszak típusa. Az érték lehet `Daily`, `Weekly`, `Monthly`, vagy `Yearly`. Alapértelmezett érték `Daily`.|
| businessDayOnly | Logikai | false |  Adja meg, hogy az adatok csak érvényes üzleti nap során. Alapértelmezett érték `false`.|


* Az összes koordináta értékek helyettesítik [szélesség, hosszúság] meghatározott `WGS84`.
* Az egyes szolgáltatásokhoz, amely tartalmaz `MultiPoint`, `MultiLineString`, `MultiPolygon` , vagy `GeometryCollection`, a Tulajdonságok érvénybe lépnek minden elemét. Példa: Minden pont `MultiPoint` azonos radius fogja használni egy több kör geokerítésen kialakításához.
* Pont – kör a forgatókönyvben egy kör geometriai jelölhető használatával egy `Point` geometriai tulajdonsággal rendelkező objektumot az kidolgozott [kiterjesztése GeoJSON geometriája](https://docs.microsoft.com/azure/azure-maps/extend-geojson).      

Az alábbiakban a számára egy egy kör geokerítésen geometriai-kiszolgálókként geokerítésen minta kéréstörzs `GeoJSON` egy középpontjának és a radius használatával. Az érvényes időszak a geokerítésen adatok 2018-10-22-es, 9 -kor, 17: 00 ismétlődik minden nap, kivéve a hétvégi indul. `expiredTime` azt jelzi, hogy az a geokerítésen adatok minősülnek lejárt, ha `userTime` a kérésben a későbbi, mint `2019-01-01`.  

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