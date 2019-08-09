---
title: Kiterjesztett GeoJSON geometriák a Azure Mapsban | Microsoft Docs
description: Ismerje meg, hogyan bővítheti a GeoJSON-geometriákat Azure Maps
author: sataneja
ms.author: sataneja
ms.date: 05/17/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.openlocfilehash: 52325248d21a5d5112c9a7f9497c3e03fdf102a4
ms.sourcegitcommit: aa042d4341054f437f3190da7c8a718729eb675e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/09/2019
ms.locfileid: "68881985"
---
# <a name="extended-geojson-geometries"></a>Kiterjesztett GeoJSON geometriák

Azure Maps a földrajzi funkciókon belüli és azok mentén történő keresésre szolgáló hatékony API-k listáját tartalmazza.
Ezek az API-k a [GeoJSON spec][1] -ra szabványosítják a földrajzi funkciókat (például az állami határokat, az útvonalakat) jelképező specifikációkat.  

A [GeoJSON spec][1] csak a következő geometriákat támogatja:

* GeometryCollection
* LineString
* MultiLineString
* Többpontos
* MultiPolygon
* Pont
* Sokszög

Néhány Azure Maps API-k (például: [Keresés a geometrián belül](https://docs.microsoft.com/rest/api/maps/search/postsearchinsidegeometry)) olyan geometriákat fogad el, mint a "Circle", amelyek nem részei a [GeoJSON][1]-specifikációnak.

Ez a cikk részletesen ismerteti, hogy Azure Maps kiterjeszti a [GeoJSON SPECT][1] bizonyos geometriák ábrázolására.

## <a name="circle"></a>Kör

A `Circle` [GeoJSON-specifikáció][1]nem támogatja a geometriát. Egy olyan `GeoJSON Point Feature` objektumot használunk, amely egy kört jelöl.

Az `Circle` objektummal jelölt geometriának a következőket kell tartalmaznia `GeoJSON Feature` :

- Középre

    A kör középpontja egy `GeoJSON Point` objektum használatával van ábrázolva.

- Sugár

    A kör a `radius` tulajdonságok használatával `GeoJSON Feature`jelenik meg. A sugár értéke méterben , és a típusnak `double`kell lennie.

- Altípus

    A körgeometriának a `subType` tulajdonságot is tartalmaznia kell. Ennek a tulajdonságnak a tulajdonságok részének `GeoJSON Feature`kell lennie, és az értékének _kör_ típusúnak kell lennie

#### <a name="example"></a>Példa

A (z) (földrajzi szélesség: 47,639754, hosszúság:-122,126986) 100 méternél nagyobb sugárral, `GeoJSON Feature` objektum használatával:

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

A `Rectangle` [GeoJSON-specifikáció][1]nem támogatja a geometriát. Egy téglalapot `GeoJSON Polygon Feature` jelölő objektumot használunk. A téglalap-bővítményt elsősorban a web SDK rajzolási eszközei modulja használja.

Az `Rectangle` objektummal jelölt geometriának a következőket kell tartalmaznia `GeoJSON Polygon Feature` :

- Sarkok

    A téglalap sarkai egy `GeoJSON Polygon` objektum koordinátái alapján jelennek meg. Öt koordinátáknak kell lennie, egyet az egyes sarokokhoz, és egy ötödik koordináta, amely ugyanaz, mint az 1. a sokszög-gyűrű bezárásához. A rendszer azt feltételezi, hogy a fejlesztő a kívánt módon igazítja és elforgatja ezeket a koordinátákat.

- Altípus

    A négyszög geometriájának tartalmaznia kell a `subType` tulajdonságot is. Ennek a tulajdonságnak a tulajdonságok részének `GeoJSON Feature`kell lennie, és az értékének _négyszögnek_ kell lennie

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
## <a name="next-steps"></a>További lépések

További információ a Azure Maps GeoJSON-adatainak használatáról:

> [!div class="nextstepaction"]
> [Geokerítésen GeoJSON formátuma](geofence-geojson.md)

Tekintse át a Azure Maps és a hely intelligenciát használó alkalmazásokkal kapcsolatos általános technikai feltételek szószedetét:

> [!div class="nextstepaction"]
> [Szószedet Azure Maps](glossary.md)

[1]: https://tools.ietf.org/html/rfc7946
