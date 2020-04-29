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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "79276399"
---
# <a name="extended-geojson-geometries"></a>Kiterjesztett GeoJSON geometriák

Azure Maps a földrajzi funkciókon belül és azok mentén történő keresésre szolgáló hatékony API-k listáját tartalmazza. Ezek az API-k megfelelnek a földrajzi funkciók ábrázolására szolgáló szabványos [GeoJSON-specifikációnak][1] .  

A [GeoJSON specifikáció][1] csak a következő geometriákat támogatja:

* GeometryCollection
* LineString
* MultiLineString
* Többpontos
* MultiPolygon
* Pont
* Sokszög

Egyes Azure Maps API-k olyan geometriákat fogadnak el, amelyek nem részei az [GeoJSON specifikációjának][1]. A geometria API-on [belüli keresés](https://docs.microsoft.com/rest/api/maps/search/postsearchinsidegeometry) például a Circle és a sokszögeket is elfogadja.

Ez a cikk részletesen ismerteti, hogy Azure Maps kiterjeszti a [GeoJSON SPECT][1] bizonyos geometriák ábrázolására.

## <a name="circle"></a>Kör

A `Circle` [GeoJSON-specifikáció][1]nem támogatja a geometriát. Egy olyan `GeoJSON Point Feature` objektumot használunk, amely egy kört jelöl.

Az `Circle` `GeoJSON Feature` objektummal jelölt geometriának a következő koordinátákat és tulajdonságokat __kell__ tartalmaznia:

- Középső

    A kör középpontja egy `GeoJSON Point` objektum használatával van ábrázolva.

- Sugár

    A kör a `radius` tulajdonságok használatával `GeoJSON Feature`jelenik meg. A sugár értéke _méterben_ , és a típusnak `double`kell lennie.

- Altípus

    A kör geometriájának tartalmaznia kell a `subType` tulajdonságot is. Ennek a tulajdonságnak a tulajdonságok részének `GeoJSON Feature`kell lennie, és az értékének _kör_ típusúnak kell lennie

#### <a name="example"></a>Példa

A következő módon lehet egy kört ábrázolni egy `GeoJSON Feature` objektum használatával. Nézzük középre a kört a következő szélességben: 47,639754 és hosszúság:-122,126986, és rendeljen hozzá egy 100 méternél nagyobb sugarat:

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

Az `Rectangle` `GeoJSON Polygon Feature` objektummal jelölt geometriának a következő koordinátákat és tulajdonságokat __kell__ tartalmaznia:

- Sarkok

    A téglalap sarkai egy `GeoJSON Polygon` objektum koordinátái alapján jelennek meg. Öt koordinátáknak kell lennie, egyet mindegyik sarokhoz. És egy olyan ötödik koordináta, amely megegyezik az első koordinátaval, a sokszög gyűrű bezárásához. A rendszer feltételezi, hogy ezek a koordináták összehangolják, és a fejlesztő a kívánt módon elforgathatja őket.

- Altípus

    A négyszög geometriájának tartalmaznia kell a `subType` tulajdonságot is. Ennek a tulajdonságnak a `GeoJSON Feature`tulajdonságok részének kell lennie, és az értékének _négyszögnek_ kell lennie

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
