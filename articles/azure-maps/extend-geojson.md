---
title: Kiterjesztett GeoJSON geometriák | Microsoft Azure térképek
description: Ebből a cikkből megtudhatja, hogy a Microsoft Azure Maps Hogyan terjeszti ki a GeoJSON SPECT bizonyos geometriák ábrázolására.
author: sataneja
ms.author: sataneja
ms.date: 05/17/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.openlocfilehash: 75ad83411edfdfe7545e8f80df17fea56e317ee0
ms.sourcegitcommit: f9601bbccddfccddb6f577d6febf7b2b12988911
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/12/2020
ms.locfileid: "75911623"
---
# <a name="extended-geojson-geometries"></a>Kiterjesztett GeoJSON geometriák

Azure Maps a földrajzi funkciókon belüli és azok mentén történő keresésre szolgáló hatékony API-k listáját tartalmazza.
Ezek az API-k a GeoJSON spec-ra szabványosítják a földrajzi funkciókat (például az állami határokat, az útvonalakat) jelképező [specifikációkat][1] .  

A [GeoJSON spec][1] csak a következő geometriákat támogatja:

* GeometryCollection
* LineString
* MultiLineString
* MultiPoint
* MultiPolygon
* Pont
* Sokszög

Egyes Azure Maps API-k (például a [geometrián belüli keresés](https://docs.microsoft.com/rest/api/maps/search/postsearchinsidegeometry)) olyan geometriákat fogadnak el, mint a "Circle", amelyek nem részei a [GeoJSON-specifikációnak][1].

Ez a cikk részletesen ismerteti, hogy Azure Maps kiterjeszti a [GeoJSON SPECT][1] bizonyos geometriák ábrázolására.

## <a name="circle"></a>Kör

Az [GeoJSON specifikáció][1]nem támogatja a `Circle` geometriát. Egy kört jelölő `GeoJSON Point Feature` objektumot használunk.

A `GeoJSON Feature` objektummal jelölt `Circle` geometriának a következőket __kell__ tartalmaznia:

- Középső

    A kör középpontja `GeoJSON Point` objektum használatával van ábrázolva.

- Sugár

    A kör `radius` `GeoJSON Feature`tulajdonságainak használatával van ábrázolva. A sugár értéke _méterben_ , és `double`típusúnak kell lennie.

- Altípus

    A kör geometriájának tartalmaznia kell a `subType` tulajdonságot is. Ennek a tulajdonságnak a `GeoJSON Feature`tulajdonságainak kell lennie, és az értékének _kör_ típusúnak kell lennie

#### <a name="example"></a>Példa

A következőkben egy `GeoJSON Feature` objektummal 100 rendelkező, a (szélesség: 47,639754, hosszúság:-122,126986) körben megjelenő kör jelenik meg.

```json            
{
    "type": "Feature",
    "geometry": {
        "type": "Point",
        "coordinates": [-122.126986, 47.639754]
    },
    "properties": {
        "subType": "Circle",
        "radius": 100
    }
}          
```

## <a name="rectangle"></a>Téglalap

Az [GeoJSON specifikáció][1]nem támogatja a `Rectangle` geometriát. Egy téglalapot jelölő `GeoJSON Polygon Feature` objektumot használunk. A téglalap-bővítményt elsősorban a web SDK rajzolási eszközei modulja használja.

A `GeoJSON Polygon Feature` objektummal jelölt `Rectangle` geometriának a következőket __kell__ tartalmaznia:

- Sarkok

    A téglalap sarkai egy `GeoJSON Polygon` objektum koordinátái alapján jelennek meg. Öt koordinátáknak kell lennie, egyet az egyes sarokokhoz, és egy ötödik koordináta, amely ugyanaz, mint az 1. a sokszög-gyűrű bezárásához. A rendszer azt feltételezi, hogy a fejlesztő a kívánt módon igazítja és elforgatja ezeket a koordinátákat.

- Altípus

    A négyszög geometriájának tartalmaznia kell a `subType` tulajdonságot is. Ennek a tulajdonságnak a `GeoJSON Feature`tulajdonságainak egy részének kell lennie, és az értékének _téglalapnak_ kell lennie

### <a name="example"></a>Példa

```json
{
    "type": "Feature",
    "geometry": {
        "type": "Polygon",
        "coordinates": [[[5,25],[14,25],[14,29],[5,29],[5,25]]]
    },
    "properties": {
        "subType": "Rectangle"
    }
}

```
## <a name="next-steps"></a>Következő lépések

További információ a Azure Maps GeoJSON-adatainak használatáról:

> [!div class="nextstepaction"]
> [Geokerítésen GeoJSON formátuma](geofence-geojson.md)

Tekintse át a Azure Maps és a hely intelligenciát használó alkalmazásokkal kapcsolatos általános technikai feltételek szószedetét:

> [!div class="nextstepaction"]
> [Szószedet Azure Maps](glossary.md)

[1]: https://tools.ietf.org/html/rfc7946
