---
title: OData geo-térbeli függvények referenciája
titleSuffix: Azure Cognitive Search
description: Szintaxis és dokumentáció a OData geo-térbeli függvények, a Geo. Distance és a Geo. metszetek használatáról az Azure Cognitive Search lekérdezésekben.
manager: nitinme
author: brjohnstmsft
ms.author: brjohnst
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
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
ms.openlocfilehash: 902996c1813931638012c78f81bd65c400bee7a1
ms.sourcegitcommit: 598c5a280a002036b1a76aa6712f79d30110b98d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/15/2019
ms.locfileid: "74113172"
---
# <a name="odata-geo-spatial-functions-in-azure-cognitive-search---geodistance-and-geointersects"></a>OData geo-térbeli függvények az Azure Cognitive Search-`geo.distance` és `geo.intersects`

Az Azure Cognitive Search támogatja a Geo-térbeli lekérdezéseket a [OData-szűrési kifejezésekben](query-odata-filter-orderby-syntax.md) a `geo.distance` és a `geo.intersects` függvények használatával. A `geo.distance` függvény két pont közötti távolságot adja vissza, amelyek közül az egyik egy mező-vagy tartomány-változó, és a szűrő részeként egy konstans lett átadva. A `geo.intersects` függvény `true` adja vissza, ha egy adott pont egy adott sokszögen belül van, ahol a pont egy mező-vagy tartomány-változó, és a sokszög a szűrő részeként megadott konstansként van megadva.

A `geo.distance` függvényt a [ **$OrderBy** paraméterben](search-query-odata-orderby.md) is használhatja a keresési eredmények egy adott pontról való távolság szerinti rendezéséhez. A **$orderby** `geo.distance` szintaxisa ugyanaz, mint **$Filter**. A **$orderby**`geo.distance` használatakor a mezőnek, amelyre az vonatkozik, `Edm.GeographyPoint` típusúnak kell lennie, és azt is **rendezheti**.

> [!NOTE]
> Ha `geo.distance`t használ a **$OrderBy** paraméterben, akkor a függvénynek átadott mezőnek csak egyetlen földrajzi pontot kell tartalmaznia. Más szóval `Edm.GeographyPoint` típusúnak kell lennie, és nem `Collection(Edm.GeographyPoint)`. Az Azure Cognitive Searchban nem rendezhető a gyűjtemény mezői.

## <a name="syntax"></a>Szintaxis

A következő EBNF ([kiterjesztett Naur-formátum](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form)) meghatározza a `geo.distance` és a `geo.intersects` függvények nyelvtanát, valamint azokat a Geo-térbeli értékeket, amelyeken működnek:

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
> [Az Azure Cognitive Search OData szintaxisának diagramja](https://azuresearch.github.io/odata-syntax-diagram/#geo_distance_call)

> [!NOTE]
> Tekintse meg az [Azure Cognitive Search OData-kifejezés szintaxisának referenciáját](search-query-odata-syntax-reference.md) a teljes EBNF.

### <a name="geodistance"></a>földrajzi távolság

A `geo.distance` függvény két, `Edm.GeographyPoint` típusú paramétert fogad, és egy `Edm.Double` értéket ad vissza, amely a kilométerek közötti távolság. Ez különbözik az olyan egyéb szolgáltatásokkal, amelyek támogatják a OData geo-térbeli műveleteit, amelyek jellemzően a mért távolságokat adják vissza a mérőórákban.

`geo.distance` paraméterek egyikének földrajzi pont állandónak kell lennie, és a másiknak mező elérési útnak kell lennie (vagy egy tartománybeli változónak kell lennie, ha a szűrő egy `Collection(Edm.GeographyPoint)`típusú mezőn keresztül ismétli meg). A paraméterek sorrendje nem számít.

A földrajzi pont állandó értéke `geography'POINT(<longitude> <latitude>)'`, ahol a hosszúság és a szélesség numerikus konstans.

> [!NOTE]
> Ha szűrőben `geo.distance` használ, össze kell hasonlítani a függvény által visszaadott távolságot `lt`, `le`, `gt`vagy `ge`használatával. A `eq` és `ne` operátorok nem támogatottak a távolságok összehasonlításakor. Ez például a `geo.distance`: `$filter=geo.distance(location, geography'POINT(-122.131577 47.678581)') le 5`helyes használata.

### <a name="geointersects"></a>Geo. metszetek

A `geo.intersects` függvény `Edm.GeographyPoint` és állandó `Edm.GeographyPolygon` típusú változót vesz fel, és egy `Edm.Boolean` -- `true` ad vissza, ha a pont a sokszög határain belül van, `false` egyéb módon.

A sokszög egy kétdimenziós felület, amely egy határoló gyűrűt definiáló pontok sorozata (lásd az alábbi [példákat](#examples) ). A sokszöget le kell zárni, ami azt jelenti, hogy az első és az utolsó pont készletének meg kell egyeznie. [A sokszögben lévő pontoknak nem megfelelő sorrendben kell lenniük](https://docs.microsoft.com/rest/api/searchservice/supported-data-types#Anchor_1).

### <a name="geo-spatial-queries-and-polygons-spanning-the-180th-meridian"></a>Geo-térbeli lekérdezések és sokszögek, amelyek a 180th meridiánt átívelően

Számos, a 180th Meridian-t (a Dátumvonali közelében) tartalmazó lekérdezést alkotó geo-térbeli lekérdezési függvénytár esetében vagy nem korlátozza a korlátot, vagy megkerülő megoldásra van szükség, például a sokszög felosztása két, a meridián egyik oldalán.

Az Azure Cognitive Search-ban a 180 fokos hosszúságú földrajzi térbeli lekérdezések a várt módon fognak működni, ha a lekérdezési alakzat téglalap alakú, és a koordináták a hosszúság és a szélesség (például `geo.intersects(location, geography'POLYGON((179 65, 179 66, -179 66, -179 65, 179 65))'`) rácsos elrendezésére vannak igazítva. Ellenkező esetben a nem téglalap alakú vagy nem igazított alakzatok esetében vegye figyelembe a felosztott sokszög megközelítését.  

### <a name="geo-spatial-functions-and-null"></a>Geo-térbeli függvények és `null`

Az Azure Cognitive Search összes többi nem gyűjteményéhez hasonlóan a `Edm.GeographyPoint` típusú mezők `null` értékeket is tartalmazhatnak. Amikor az Azure Cognitive Search kiértékeli `geo.intersects` egy `null`mezőre, az eredmény mindig `false`lesz. A `geo.distance` viselkedése ebben az esetben a kontextustól függ:

- A szűrők területen a `null` mező `geo.distance` `null`eredményez. Ez azt jelenti, hogy a dokumentum nem egyezik, mert a nem null értékhez képest `null` `false`.
- Ha az eredményeket **$OrderBy**használatával rendezi, a `null` mező `geo.distance` a lehető legnagyobb távolságot eredményezi. Az ilyen mezőkkel rendelkező dokumentumok az összes többinél alacsonyabbak lesznek, ha a rendezés irányát `asc` használni (az alapértelmezett érték), és a többinél nagyobb, ha az irány `desc`.

## <a name="examples"></a>Példák

### <a name="filter-examples"></a>Példák szűrésére

Az összes Hotel megkeresése egy adott hivatkozási ponttól 10 kilométeren belül (ahol a hely egy `Edm.GeographyPoint`típusú mező):

    geo.distance(location, geography'POINT(-122.131577 47.678581)') le 10

A megadott nézetablakban lévő összes Hotel megkeresése sokszögként (ahol a hely egy `Edm.GeographyPoint`típusú mező). Vegye figyelembe, hogy a sokszög le van zárva (az első és az utolsó pontnak azonosnak kell lennie), és [a pontoknak](https://docs.microsoft.com/rest/api/searchservice/supported-data-types#Anchor_1)a következő sorrendben kell szerepelniük.

    geo.intersects(location, geography'POLYGON((-122.031577 47.578581, -122.031577 47.678581, -122.131577 47.678581, -122.031577 47.578581))')

### <a name="order-by-examples"></a>Rendezési példák

A szállodák rendezése `rating`alapján csökkenő sorrendben történik, majd a megadott koordináták távolsága alapján emelkedik:

    rating desc,geo.distance(location, geography'POINT(-122.131577 47.678581)') asc

A szállodát csökkenő sorrendbe rendezheti `search.score` és `rating`szerint, majd növekvő sorrendben, a megadott koordináták távolsága alapján, így két, azonos minősítéssel rendelkező, egymással azonos minősítéssel rendelkező Hotel között, a legközelebb látható az első:

    search.score() desc,rating desc,geo.distance(location, geography'POINT(-122.131577 47.678581)') asc

## <a name="next-steps"></a>További lépések  

- [Szűrők az Azure Cognitive Search](search-filters.md)
- [Az Azure Cognitive Search OData kifejezés nyelvének áttekintése](query-odata-filter-orderby-syntax.md)
- [Az Azure Cognitive Search OData-kifejezési szintaxisának referenciája](search-query-odata-syntax-reference.md)
- [Dokumentumok &#40;keresése az Azure Cognitive Search REST API&#41;](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)
