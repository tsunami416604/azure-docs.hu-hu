---
title: A Maps-Azure GeoJSON geometriája kiterjesztése |} Microsoft Docs
description: Ismerje meg, hogyan terjeszthető ki az Azure-leképezések GeoJSON geometriája
author: sataneja
ms.author: sataneja
ms.date: 05/17/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.openlocfilehash: 2cc0e29615ad4fc19040055d847435a9dffa9c95
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34655088"
---
# <a name="extending-geojson-geometries"></a>GeoJSON geometriája kiterjesztése

Az Azure Maps hatékony API-k belső/mentén földrajzi funkciók kereséséhez listáját tartalmazza.
Ezen API-k szabványosítsa a telepített [GeoJSON spec] [ 1] lapblobok az a földrajzi funkciók (például: állapot határokat, útvonalak).  

A [GeoJSON spec] [ 1] csak a következő geometriája támogatja:

* GeometryCollection
* LineString
* MultiLineString
* MultiPoint
* MultiPolygon
* pont
* Sokszög

Egyes Azure térképek API-k (például: [keresési belül geometriai](https://docs.microsoft.com/en-us/rest/api/maps/search/postsearchinsidegeometry)) fogadja el a "Kör", például geometriája, amelyek nem részei a [GeoJSON spec][1].

Ez a cikk ismerteti részletesen ismerteti a hogyan Azure Maps bővíti a [GeoJSON spec] [ 1] bizonyos geometriája képviseli.

### <a name="circle"></a>kör

A `Circle` geometriai nem támogatja a [GeoJSON spec][1]. Használjuk a `GeoJSON Feature` kör képviselő objektum.

A `Circle` geometriai ábrázolásához a `GeoJSON Feature` objektum __kell__ az alábbiakat tartalmazza:

1. Középre
   >A kör közepének képviselt használatával egy `GeoJSON Point` típusa.

2. Sugár
   >A kör `radius` használatával képviselt `GeoJSON Feature`tartozó tulajdonságok. A radius-érték _mérőszámok_ típusúnak kell lennie, és `double`.

3. Altípus
   >A kör geometriai is tartalmaznia kell a `subType` tulajdonság. Ez a tulajdonság egy részének kell lennie a `GeoJSON Feature`tartozó tulajdonságok és az érték legyen _kör_


#### <a name="example"></a>Példa

Íme, hogyan kell meghatároznia része a következő kör (szélesség: 47.639754, hosszúság:-122.126986) értéke 100 mérőszámok segítségével a radius protokollal a `GeoJSON Feature` objektum:

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

[1]: https://tools.ietf.org/html/rfc7946
