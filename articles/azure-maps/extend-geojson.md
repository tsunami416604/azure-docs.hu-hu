---
title: Kiterjesztett GeoJSON geometriák | Microsoft Azure Maps
description: Ebben a cikkben megtudhatja, hogy a Microsoft Azure Maps hogyan terjeszti ki a GeoJSON specifikációt bizonyos geometriák ra.
author: sataneja
ms.author: sataneja
ms.date: 05/17/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.openlocfilehash: 98db10f0fc7a417f39d4bb00e77af6bdea034a03
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79276399"
---
# <a name="extended-geojson-geometries"></a>Kiterjesztett GeoJSON geometriák

Az Azure Maps a földrajzi funkciókon belüli és azok mentén végzett hatékony API-k listáját tartalmazza. Ezek az API-k megfelelnek a standard [GeoJSON spec][1] képviselő földrajzi jellemzők.  

A [GeoJSON specifikáció][1] csak a következő geometriákat támogatja:

* Geometriagyűjtemény
* Vonalsor-karakterlánc
* Többsoros karakterlánc
* Multipont
* MultiPoligon
* Pont
* Sokszög

Egyes Azure Maps API-k olyan geometriákat fogadnak el, amelyek nem részei a [GeoJSON specifikációnak.][1] Például a [Search Inside Geometry](https://docs.microsoft.com/rest/api/maps/search/postsearchinsidegeometry) API elfogadja a Kör és a sokszögek.

Ez a cikk részletes magyarázatot ad arról, hogy az Azure Maps hogyan terjeszti ki a [GeoJSON-specifikációt][1] bizonyos geometriák ábrázolására.

## <a name="circle"></a>Kör

A `Circle` [geoJSON specifikáció][1]nem támogatja a geometriát. Egy tárgyat `GeoJSON Point Feature` használunk, hogy egy kört jelezzünk.

Az `Circle` objektumot használó `GeoJSON Feature` geometriának a következő koordinátákat és tulajdonságokat __kell__ tartalmaznia:

- Középső

    A kör középpontja egy `GeoJSON Point` objektum segítségével jelenik meg.

- Sugár

    A kör `radius` a 's tulajdonságok használatával `GeoJSON Feature`jelenik meg. A sugár értéke _méterben_ van, `double`és a típusnak kell lennie.

- Altípus

    A kör geometriájának `subType` tartalmaznia kell a tulajdonságot is. Ennek a tulajdonságnak a `GeoJSON Feature`tulajdonságok részét kell, hogy bevegye, és értékét Körnek kell _tekinteni._

#### <a name="example"></a>Példa

Így fog ábrázolni egy kört `GeoJSON Feature` egy objektum használatával. Középre a kör szélességi fokon: 47.639754 és hosszúság: -122.126986, és rendelje hozzá a sugara egyenlő 100 méter:

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

A `Rectangle` [geoJSON specifikáció][1]nem támogatja a geometriát. Egy objektumot `GeoJSON Polygon Feature` használunk egy téglalap ábrázolására. A téglalapkiterjesztést elsősorban a Web SDK rajzeszköz-modulja használja.

Az `Rectangle` objektumot használó `GeoJSON Polygon Feature` geometriának a következő koordinátákat és tulajdonságokat __kell__ tartalmaznia:

- Sarkok

    A téglalap sarkai egy `GeoJSON Polygon` objektum koordinátáinak használatával jelennek meg. Öt koordinátának kell lennie, minden sarokhoz egy. És egy ötödik koordináta, amely megegyezik az első koordinátával, hogy lezárja a poligongyűrűt. Feltételezzük, hogy ezek a koordináták igazodnak, és hogy a fejlesztő elforgathatja őket, ahogy akarták.

- Altípus

    A téglalap geometriájának tartalmaznia kell a `subType` tulajdonságot is. Ennek a tulajdonságnak a `GeoJSON Feature`tulajdonságok részét kell, hogy bevegye, és az értéke _téglalap legyen._

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

További információ a GeoJSON-adatokról az Azure Mapsben:

> [!div class="nextstepaction"]
> [Geofence GeoJSON formátum](geofence-geojson.md)

Tekintse át az Azure Maps és a helymeghatározási intelligencia alkalmazásokkal kapcsolatos általános technikai kifejezések szószedetét:

> [!div class="nextstepaction"]
> [Az Azure Maps szószedete](glossary.md)

[1]: https://tools.ietf.org/html/rfc7946
