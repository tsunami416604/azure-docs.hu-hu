---
title: OData geo-térbeli függvények referenciája – Azure Search
description: OData geo-térbeli függvények, Geo. Distance és Geo. metszetek Azure Search lekérdezésekben.
ms.date: 06/13/2019
services: search
ms.service: search
ms.topic: conceptual
author: brjohnstmsft
ms.author: brjohnst
manager: nitinme
translation.priority.mt:
- de-de
- es-es
- fr-fr
- it-it
- ja-jp
- ko-kr
- pt-br
- ru-ru
- zh-cn
- zh-tw
ms.openlocfilehash: 9585a9a7ea976ed32ccb8eed1e69877339196f87
ms.sourcegitcommit: bb8e9f22db4b6f848c7db0ebdfc10e547779cccc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/20/2019
ms.locfileid: "69647564"
---
# <a name="odata-geo-spatial-functions-in-azure-search---geodistance-and-geointersects"></a>OData geo-térbeli függvények a Azure Search- `geo.distance` és`geo.intersects`

Azure Search támogatja a Geo-térbeli lekérdezéseket a [OData](query-odata-filter-orderby-syntax.md) -szűrési `geo.intersects` kifejezésekben a `geo.distance` és függvények használatával. A `geo.distance` függvény két pont közötti távolságot adja vissza, amelyek közül az egyik egy mező-vagy tartomány-változó, és a szűrő részeként egy konstans lett átadva. A `geo.intersects` függvény akkor `true` adja vissza, ha egy adott pont egy adott sokszögen belül van, ahol a pont egy mező-vagy tartomány-változó, és a sokszög a szűrő részeként megadott konstansként van megadva.

A `geo.distance` függvény a [ **$OrderBy** paraméterben](search-query-odata-orderby.md) is használható a keresési eredmények rendezésére egy adott pont távolsága alapján. A `geo.distance` **$OrderBy** szintaxisa megegyezik a **$Filter**. A `geo.distance` **$OrderBy**használatakor a mezőnek, amelyre az alkalmazás vonatkozik, típusnak `Edm.GeographyPoint` kell lennie, és azt is **rendezve**kell lennie.

## <a name="syntax"></a>Szintaxis

A következő EBNF ([bővített Naur űrlap](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form)) meghatározza a `geo.distance` és `geo.intersects` a függvények nyelvtanát, valamint azokat a Geo-térbeli értékeket, amelyeken működnek:

<!-- Upload this EBNF using https://bottlecaps.de/rr/ui to create a downloadable railroad diagram. -->

```
geo_distance_call ::=
    'geo.distance(' variable ',' geo_point ')'
    | 'geo.distance(' geo_point ',' variable ')'

geo_point ::= "geography'POINT(" lon_lat ")'"

lon_lat ::= float_literal ' ' float_literal

geo_intersects_call ::=
    'geo.intersects(' variable ',' geo_polygon ')'

/* You need at least four points to form a polygon, where the first and
last points are the same. */
geo_polygon ::=
    "geography'POLYGON((" lon_lat ',' lon_lat ',' lon_lat ',' lon_lat_list "))'"

lon_lat_list ::= lon_lat(',' lon_lat)*
```

Az interaktív szintaxis diagram is elérhető:

> [!div class="nextstepaction"]
> [Azure Search OData szintaxisának diagramja](https://azuresearch.github.io/odata-syntax-diagram/#geo_distance_call)

> [!NOTE]
> A teljes EBNF [Azure Search a OData kifejezés szintaxisát](search-query-odata-syntax-reference.md) ismertető témakörben talál.

### <a name="geodistance"></a>földrajzi távolság

A `geo.distance` függvény két típusú `Edm.GeographyPoint` paramétert fogad, és olyan `Edm.Double` értéket ad vissza, amely a kettő közötti távolság. Ez különbözik az olyan egyéb szolgáltatásokkal, amelyek támogatják a OData geo-térbeli műveleteit, amelyek jellemzően a mért távolságokat adják vissza a mérőórákban.

Az egyik paraméternek `geo.distance` a földrajzi pont állandójának kell lennie, és a másiknak mező elérési útnak kell lennie (vagy egy tartományban lévő változónak kell lennie, ha a szűrő `Collection(Edm.GeographyPoint)`egy típusú mezőnél megismétli a mezőt). A paraméterek sorrendje nem számít.

A földrajzi pont állandója az űrlap `geography'POINT(<longitude> <latitude>)'`, ahol a hosszúság és a szélesség numerikus konstans.

> [!NOTE]
> Szűrőben `geo.distance` való használat esetén össze kell hasonlítani a függvény által visszaadott távolságot állandó `le`használatával `lt` `gt`,, vagy `ge`. Az `eq` operátorok `ne` és a távolságok összehasonlítása nem támogatott. Ez például a következő helyes használata `geo.distance`:. `$filter=geo.distance(location, geography'POINT(-122.131577 47.678581)') le 5`

### <a name="geointersects"></a>Geo. metszetek

A `geo.intersects` függvény egy típus `Edm.GeographyPoint` és egy állandó `Edm.GeographyPolygon` változót használ, és egy `Edm.Boolean`  --  `true` értéket ad vissza, ha a pont a sokszög határain belül `false` van, ellenkező esetben.

A sokszög egy kétdimenziós felület, amely egy határoló gyűrűt definiáló pontok sorozata (lásd az alábbi [példákat](#examples) ). A sokszöget le kell zárni, ami azt jelenti, hogy az első és az utolsó pont készletének meg kell egyeznie. [A sokszögben lévő pontoknak nem megfelelő sorrendben kell lenniük](https://docs.microsoft.com/rest/api/searchservice/supported-data-types#Anchor_1).

### <a name="geo-spatial-queries-and-polygons-spanning-the-180th-meridian"></a>Geo-térbeli lekérdezések és sokszögek, amelyek a 180th meridiánt átívelően

Számos, a 180th Meridian-t (a Dátumvonali közelében) tartalmazó lekérdezést alkotó geo-térbeli lekérdezési függvénytár esetében vagy nem korlátozza a korlátot, vagy megkerülő megoldásra van szükség, például a sokszög felosztása két, a meridián egyik oldalán.

Azure Search az 180 fokos hosszúságú földrajzi koordinátákat tartalmazó lekérdezések a várt módon fognak működni, ha a lekérdezési alakzat téglalap alakú, és a koordináták a hosszúság és a szélesség (például `geo.intersects(location, geography'POLYGON((179 65, 179 66, -179 66, -179 65, 179 65))'`) rácsos elrendezésére vannak igazítva. Ellenkező esetben a nem téglalap alakú vagy nem igazított alakzatok esetében vegye figyelembe a felosztott sokszög megközelítését.  

### <a name="geo-spatial-functions-and-null"></a>Geo-térbeli függvények és`null`

A Azure Search összes többi nem gyűjteményéhez hasonlóan a típusú `Edm.GeographyPoint` mezők is tartalmazhatnak `null` értékeket. Ha Azure Search kiértékel `geo.intersects` egy mezőt `null`, `false`az eredmény mindig a következő lesz:. Ebben az esetben `geo.distance` a viselkedése a kontextustól függ:

- A szűrők `geo.distance` területen egy `null` mező eredményét jeleníti meg `null`. Ez azt jelenti, `null` `false`hogy a dokumentum nem fog megegyezni, mert a nem null értékhez képest kiértékeli a értéket.
- Ha az eredményeket **$OrderBy**használatával rendezi `geo.distance` , `null` a mező a lehető legnagyobb távolságot eredményezi. Az ilyen mezőkkel rendelkező dokumentumok a rendezési irány `asc` használatakor alacsonyabbak lesznek, mint az összes többinél (az alapértelmezett érték), és a többinél nagyobb, mint az `desc`irány.

## <a name="examples"></a>Példák

### <a name="filter-examples"></a>Példák szűrésére

Az összes Hotel megkeresése egy adott hivatkozási ponttól számított 10 kilométeren belül (ahol a `Edm.GeographyPoint`hely egy típusú mező):

    geo.distance(location, geography'POINT(-122.131577 47.678581)') le 10

A megadott nézetablakban lévő összes Hotel megkeresése sokszögként (ahol a hely egy típusú `Edm.GeographyPoint`mező). Vegye figyelembe, hogy a sokszög le van zárva (az első és az utolsó pontnak azonosnak kell lennie), és [a pontoknak](https://docs.microsoft.com/rest/api/searchservice/supported-data-types#Anchor_1)a következő sorrendben kell szerepelniük.

    geo.intersects(location, geography'POLYGON((-122.031577 47.578581, -122.031577 47.678581, -122.131577 47.678581, -122.031577 47.578581))')

### <a name="order-by-examples"></a>Rendezési példák

Rendezheti a szállodákat `rating`csökkenő sorrendben, majd a megadott koordináták távolsága alapján:

    rating desc,geo.distance(location, geography'POINT(-122.131577 47.678581)') asc

Rendezheti a szállodákat csökkenő sorrendben `search.score` , `rating`a és a alapján, majd növekvő sorrendben, a megadott koordináták távolsága alapján, hogy két, azonos minősítéssel rendelkező, egymással azonos minősítéssel rendelkező Hotel között szerepeljenek a legközelebb:

    search.score() desc,rating desc,geo.distance(location, geography'POINT(-122.131577 47.678581)') asc

## <a name="next-steps"></a>További lépések  

- [Szűrők a Azure Searchban](search-filters.md)
- [A OData kifejezés nyelvének áttekintése Azure Search](query-odata-filter-orderby-syntax.md)
- [Azure Search OData-kifejezés szintaxisának referenciája](search-query-odata-syntax-reference.md)
- [Dokumentumok &#40;keresése Azure Search szolgáltatás REST API&#41;](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)
