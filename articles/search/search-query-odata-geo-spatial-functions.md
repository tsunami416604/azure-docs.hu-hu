---
title: OData térinformatikai függvényhivatkozás – Azure Search
description: OData térinformatikai funkciók, geo.distance és geo.intersects, az Azure Search-lekérdezéseket.
ms.date: 06/13/2019
services: search
ms.service: search
ms.topic: conceptual
author: brjohnstmsft
ms.author: brjohnst
ms.manager: cgronlun
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
ms.openlocfilehash: 0ce63ab1143c784eb3e10f47c20ef2b5034d63a7
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "67079794"
---
# <a name="odata-geo-spatial-functions-in-azure-search---geodistance-and-geointersects"></a>Az Azure Search - OData a térinformatikai függvények `geo.distance` és `geo.intersects`

Az Azure Search támogatja a térinformatikai lekérdezések [OData szűrési kifejezésekben](query-odata-filter-orderby-syntax.md) keresztül a `geo.distance` és `geo.intersects` funkciók. A `geo.distance` függvényt ad vissza a távolságot adja meg kilométerben két pont között, egy mezőt, vagy a tartomány változót, és a egy folyamatban van egy állandó átadott szűrő részeként. A `geo.intersects` függvény visszaad `true` belül egy adott sokszög van egy adott időpontra, amelyben a pont egy mezőben vagy a tartomány változót és a sokszög egy szűrő részeként átadott konstansként van megadva.

A `geo.distance` függvény is használható a [ **$orderby** paraméter](search-query-odata-orderby.md) a távolság egy adott pont a keresési eredmények rendezéséhez. A szintaxist a `geo.distance` a **$orderby** van ugyanaz, mint az **$filter**. Használata esetén `geo.distance` a **$orderby**, amelyekre vonatkozik a mező típusúnak kell lennie `Edm.GeographyPoint` kell lennie, és **rendezhető**.

## <a name="syntax"></a>Szintaxis

A következő EBNF ([kiterjesztett Backus-Naur űrlap](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form)) határozza meg a nyelvtani, a `geo.distance` és `geo.intersects` funkciók, valamint a térinformatikai értékek, amelyen működnek:

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

Egy interaktív szintaxisdiagramja is érhető el:

> [!div class="nextstepaction"]
> [Azure Search OData szintaxisdiagramja](https://azuresearch.github.io/odata-syntax-diagram/#geo_distance_call)

> [!NOTE]
> Lásd: [OData kifejezés szintaxisának referenciája az Azure Search](search-query-odata-syntax-reference.md) a teljes EBNF számára.

### <a name="geodistance"></a>GEO.Distance

A `geo.distance` függvény típusú két paraméter szükséges `Edm.GeographyPoint` adja vissza, és egy `Edm.Double` értéket adja meg kilométerben őket közötti távolság. Ez eltér más szolgáltatásokkal, amelyek támogatják az OData térinformatikai műveleteket, amelyeket általában mérőszámok távolságot adja vissza.

A paraméterek egyikét `geo.distance` kell földrajzi pont állandónak kell lennie, és a egy mező elérési útja, hogy a többi kell (vagy a tartomány változót szűrő esetén léptetés típusú mező felett `Collection(Edm.GeographyPoint)`). Ezek a paraméterek sorrendje nem számít.

A földrajzi pont konstanta je az űrlap `geography'POINT(<longitude> <latitude>)'`, ahol a szélességi és hosszúsági rendszer numerikus állandót.

> [!NOTE]
> Használata esetén `geo.distance` a szűrőben vesse össze a távolság egy állandó használatakor a függvény által visszaadott `lt`, `le`, `gt`, vagy `ge`. Az operátorok `eq` és `ne` távokat összehasonlítása nem támogatott. Például ez a helyes használatát, `geo.distance`: `$filter=geo.distance(location, geography'POINT(-122.131577 47.678581)') le 5`.

### <a name="geointersects"></a>GEO.intersects

A `geo.intersects` függvény argumentuma típusú változó `Edm.GeographyPoint` és a egy állandó `Edm.GeographyPolygon` adja vissza, és egy `Edm.Boolean`  --  `true` a sokszög határain belül a pontra esetén `false` más módon.

A sokszög az egy kétdimenziós felület meghatározása a határoló kört pontok sorozataként tárolja (lásd a [példák](#examples) alább). A sokszög kell zárni, tehát az első és utolsó pont csoportok azonosnak kell lennie. [Egy sokszög pontjainak járásával sorrendben kell](https://docs.microsoft.com/rest/api/searchservice/supported-data-types#Anchor_1).

### <a name="geo-spatial-queries-and-polygons-spanning-the-180th-meridian"></a>Térinformatikai lekérdezések és a 180th délkörének átfedés poligonok

Számos térinformatikai lekérdezés kidolgozásában egy lekérdezést, amely magában foglalja a (közel az Dátumvonali) 180th délkörének kódtárak, mint off-limits vagy egy megkerülő megoldás, például a sokszög felosztása két, egyet a hosszúság egyik oldalán igényel.

Az Azure Search szolgáltatásban 180 fokos hosszúsági tartalmazó térinformatikai lekérdezések fog a várt módon működik-e a lekérdezés alakzat téglalap alakú, és a koordináták a szélességi és hosszúsági mentén rácsos elrendezés igazítás (például `geo.intersects(location, geography'POLYGON((179 65, 179 66, -179 66, -179 65, 179 65))'`). Ellenkező esetben nem téglalap vagy nem igazított alakzatok, fontolja meg a vegyes sokszög megközelítés.  

### <a name="geo-spatial-functions-and-null"></a>Térinformatikai funkciókat és `null`

Az összes többi nem gyűjtemény mezőt az Azure Search szolgáltatásban típusú mezők, például `Edm.GeographyPoint` tartalmazhat `null` értékeket. Ha az Azure Search kiértékeli `geo.intersects` mező, amely `null`, az eredmény mindig lesz `false`. Viselkedését `geo.distance` ebben az esetben a a környezettől függ:

- A szűrők `geo.distance` , egy `null` mező az eredmény `null`. Ez azt jelenti, hogy a dokumentum nem egyezik, mert `null` képest minden nem null érték kiértékelésének `false`.
- Ha az eredményeket az **$orderby**, `geo.distance` , egy `null` mezőt a legnagyobb lehetséges távolság eredményez. Dokumentumok, amelyek egy ilyen mező rendezhető alacsonyabb, mint minden más során a rendezési irány `asc` van használt (alapértelmezett), és magasabb, mint minden más, amikor a iránya `desc`.

## <a name="examples"></a>Példák

### <a name="filter-examples"></a>Példák a szűrők

Keresse meg az összes szállodák egy adott hivatkozási pont 10 kilométerben belül (ahol a hely a következő típusú mező `Edm.GeographyPoint`):

    geo.distance(location, geography'POINT(-122.131577 47.678581)') le 10

Keresse meg az összes szállodák egy sokszög szerepeltek egy adott nézőponton belül (ahol a hely a következő típusú mező `Edm.GeographyPoint`). Vegye figyelembe, hogy bezárja-e a sokszög (az első és utolsó pont csoportok azonosnak kell lennie), és [szerepelnie kell a pontok járásával sorrendben](https://docs.microsoft.com/rest/api/searchservice/supported-data-types#Anchor_1).

    geo.intersects(location, geography'POLYGON((-122.031577 47.578581, -122.031577 47.678581, -122.131577 47.678581, -122.031577 47.578581))')

### <a name="order-by-examples"></a>Az Order by példák

A "Hotels" szerint csökkenő rendezés `rating`, majd az adott koordinátái a növekvő távolság szerint:

    rating desc,geo.distance(location, geography'POINT(-122.131577 47.678581)') asc

Rendezés csökkenő sorrendben a szállodák `search.score` és `rating`, majd a növekvő sorrendben távolság, a megadott koordináták, így között két hotels azonos besorolású, a legközelebbire első helyen szerepel:

    search.score() desc,rating desc,geo.distance(location, geography'POINT(-122.131577 47.678581)') asc

## <a name="next-steps"></a>További lépések  

- [Szűrők az Azure Search szolgáltatásban](search-filters.md)
- [Az Azure Search OData kifejezés nyelvi – áttekintés](query-odata-filter-orderby-syntax.md)
- [Az Azure Search OData kifejezés szintaxisának referenciája](search-query-odata-syntax-reference.md)
- [Dokumentumok keresése &#40;az Azure Search szolgáltatás REST API-ja&#41;](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)
