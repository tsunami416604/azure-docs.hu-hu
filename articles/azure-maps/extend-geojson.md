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
ms.openlocfilehash: 98db10f0fc7a417f39d4bb00e77af6bdea034a03
ms.sourcegitcommit: 333af18fa9e4c2b376fa9aeb8f7941f1b331c11d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/13/2020
ms.locfileid: "77198275"
---
# <a name="extended-geojson-geometries"></a>Kiterjesztett GeoJSON geometriák

Azure Maps a földrajzi funkciókon belül és azok mentén történő keresésre szolgáló hatékony API-k listáját tartalmazza. Ezek az API-k megfelelnek a földrajzi funkciók ábrázolására szolgáló szabványos [GeoJSON-specifikációnak][1] .  

A [GeoJSON specifikáció][1] csak a következő geometriákat támogatja:

* GeometryCollection
* LineString
* MultiLineString
* MultiPoint
* MultiPolygon
* Pont
* Sokszög

Egyes Azure Maps API-k olyan geometriákat fogadnak el, amelyek nem részei az [GeoJSON specifikációjának][1]. A geometria API-on [belüli keresés](https://docs.microsoft.com/rest/api/maps/search/postsearchinsidegeometry) például a Circle és a sokszögeket is elfogadja.

Ez a cikk részletesen ismerteti, hogy Azure Maps kiterjeszti a [GeoJSON SPECT][1] bizonyos geometriák ábrázolására.

## <a name="circle"></a>Kör

Az [GeoJSON specifikáció][1]nem támogatja a `Circle` geometriát. Egy kört jelölő `GeoJSON Point Feature` objektumot használunk.

A `GeoJSON Feature` objektummal jelölt `Circle` geometriának a következő koordinátákat és tulajdonságokat __kell__ tartalmaznia:

- Center

    A kör középpontja `GeoJSON Point` objektum használatával van ábrázolva.

- RADIUS-

    A kör `radius` `GeoJSON Feature`tulajdonságainak használatával van ábrázolva. A sugár értéke _méterben_ , és `double`típusúnak kell lennie.

- Altípus

    A kör geometriájának tartalmaznia kell a `subType` tulajdonságot is. Ennek a tulajdonságnak a `GeoJSON Feature`tulajdonságainak kell lennie, és az értékének _kör_ típusúnak kell lennie

#### <a name="example"></a>Példa

A kör `GeoJSON Feature` objektummal való ábrázolását mutatja be. Nézzük középre a kört a következő szélességben: 47,639754 és hosszúság:-122,126986, és rendeljen hozzá egy 100 méternél nagyobb sugarat:

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

A `GeoJSON Polygon Feature` objektummal jelölt `Rectangle` geometriának a következő koordinátákat és tulajdonságokat __kell__ tartalmaznia:

- Sarkok

    A téglalap sarkai egy `GeoJSON Polygon` objektum koordinátái alapján jelennek meg. Öt koordinátáknak kell lennie, egyet mindegyik sarokhoz. És egy olyan ötödik koordináta, amely megegyezik az első koordinátaval, a sokszög gyűrű bezárásához. A rendszer feltételezi, hogy ezek a koordináták összehangolják, és a fejlesztő a kívánt módon elforgathatja őket.

- Altípus

    A négyszög geometriájának tartalmaznia kell a `subType` tulajdonságot is. Ennek a tulajdonságnak a `GeoJSON Feature`tulajdonságainak kell lennie, és az értékének _négyszögnek_ kell lennie

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
