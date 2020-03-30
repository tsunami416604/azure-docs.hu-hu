---
title: OData geotéri függvény hivatkozása
titleSuffix: Azure Cognitive Search
description: Szintaxis és referencia dokumentáció az Azure Cognitive Search lekérdezésekben az OData geo-spatial függvények, a geo.distance és a geo.intersects használatával.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74113172"
---
# <a name="odata-geo-spatial-functions-in-azure-cognitive-search---geodistance-and-geointersects"></a>OData térinformatikai függvények az `geo.distance` Azure Cognitive Search -és`geo.intersects`

Az Azure Cognitive Search támogatja a térinformatikai lekérdezések `geo.distance` `geo.intersects` [OData szűrő kifejezések](query-odata-filter-orderby-syntax.md) en keresztül a és a függvények. A `geo.distance` függvény két pont közötti távolságot adja vissza kilométerben, az egyik egy mező vagy tartomány változó, a másik pedig a szűrő részeként átadott állandó. A `geo.intersects` függvény `true` akkor ad eredményül, ha egy adott pont egy adott sokszögen belül van, ahol a pont egy mező vagy tartomány változó, és a sokszög a szűrő részeként átadott állandóként van megadva.

A `geo.distance` funkció a [ **$orderby** paraméterben](search-query-odata-orderby.md) is használható a keresési eredmények adott ponttól való távolság szerint történő rendezésére. A **$orderby** `geo.distance` szintaxisa megegyezik a **$filter.** A `geo.distance` **$orderby**használatakor annak a mezőnek , `Edm.GeographyPoint` amelyre vonatkozik , típusnak kell lennie , és **rendezhetőnek**is kell lennie .

> [!NOTE]
> Ha `geo.distance` a **$orderby** paraméterben használja, a függvénynek átadott mezőnek csak egyetlen geopontot kell tartalmaznia. Más szóval, meg kell `Edm.GeographyPoint` típusú, és nem `Collection(Edm.GeographyPoint)`. Az Azure Cognitive Search gyűjteménymezői nem rendezheti.

## <a name="syntax"></a>Szintaxis

A következő EBNF ([Extended Backus-Naur Form](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form) `geo.distance` ) `geo.intersects` határozza meg a funkciók nyelvtanát és a függvényeket, valamint azokat a földrajzi-térbeli értékeket, amelyeken működnek:

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

Interaktív szintaktikai diagram is elérhető:

> [!div class="nextstepaction"]
> [OData-szintaktikai diagram az Azure Cognitive Search szolgáltatáshoz](https://azuresearch.github.io/odata-syntax-diagram/#geo_distance_call)

> [!NOTE]
> Lásd: [OData kifejezés szintaxis referencia az Azure Cognitive Search](search-query-odata-syntax-reference.md) a teljes EBNF.

### <a name="geodistance"></a>geo.distance

A `geo.distance` függvény két típusparamétert `Edm.GeographyPoint` vesz `Edm.Double` fel, és olyan értéket ad vissza, amely a közöttük lévő távolság kilométerben. Ez eltér az OData térinformatikai műveleteket támogató egyéb szolgáltatásoktól, amelyek általában méterben adják vissza a távolságokat.

Az egyik paraméternek `geo.distance` földrajzi pontállandónak kell lennie, a másiknak pedig mezőútvonalnak (vagy egy típuson lévő szűrő `Collection(Edm.GeographyPoint)`iterációesetén egy tartományváltozónak) kell lennie. Ezeknek a paramétereknek a sorrendje nem számít.

A földrajzi pont állandója `geography'POINT(<longitude> <latitude>)'`az űrlapé, ahol a hosszúság és a szélesség numerikus állandók.

> [!NOTE]
> Szűrőben `geo.distance` való használatesetén a függvény által visszaadott távolságot `lt`a `le` `gt`, `ge`, vagy a használatával kell összehasonlítani. Az operátorok, `eq` és `ne` nem támogatottak a távolságok összehasonlításakor. Például ez a helyesen `geo.distance`használt: `$filter=geo.distance(location, geography'POINT(-122.131577 47.678581)') le 5`.

### <a name="geointersects"></a>geo.metszi

A `geo.intersects` függvény egy változó `Edm.GeographyPoint` típusú `Edm.GeographyPolygon` és egy `Edm.Boolean`  --  `true` állandó, és visszaad egy ha `false` a pont határain belül a sokszög, egyébként.

A sokszög egy kétdimenziós felület, amelyet egy határológyűrűt meghatározó pontok sorozataként tárolnak (lásd az alábbi [példákat).](#examples) A sokszöget be kell zárni, ami azt jelenti, hogy az első és az utolsó pontkészletnek azonosnak kell lennie. [A sokszög pontjainak az óramutató járásával ellentétes sorrendben kell lenniük.](https://docs.microsoft.com/rest/api/searchservice/supported-data-types#Anchor_1)

### <a name="geo-spatial-queries-and-polygons-spanning-the-180th-meridian"></a>A 180.

A 180th meridiánt (a dátumvonal közelében) tartalmazó lekérdezést készítő számos tértéri lekérdezéskódszerint vagy tiltott terület, vagy kerülő megoldást igényel, például a sokszöget kettéosztja, a meridián mindkét oldalán az egyiket.

Az Azure Cognitive Search,térinformatikai lekérdezések, amelyek 180 fokos hosszúsági szint reked, a várt módon fog működni, `geo.intersects(location, geography'POLYGON((179 65, 179 66, -179 66, -179 65, 179 65))'`ha a lekérdezés alakja téglalap alakú, és a koordináták igazodnak a rács elrendezés mentén hosszúsági és szélességi (például). Ellenkező esetben a nem téglalap vagy nem igazított alakzatok esetében vegye figyelembe az osztott sokszög megközelítést.  

### <a name="geo-spatial-functions-and-null"></a>Térinformatikai funkciók és`null`

Az Azure Cognitive Search összes többi nem gyűjteménymezőjéhez hasonlóan a típusmezők `Edm.GeographyPoint` is tartalmazhatnak `null` értékeket. Amikor az Azure `geo.intersects` Cognitive Search kiértékeli a mezőt, amely `null`a , az eredmény mindig a lesz. `false` A viselkedés `geo.distance` ebben az esetben függ a kontextus:

- A szűrőkben `geo.distance` `null` a mező `null`eredménye a . Ez azt jelenti, hogy `null` `false`a dokumentum nem egyezik, mert a nem null értékű kiértékeléshez képest a.
- Ha **$orderby**használatával rendezi `geo.distance` az `null` eredményeket, a mező a lehető legnagyobb távolságot eredményezi. Az ilyen mezővel rendelkező dokumentumok a rendezési `asc` irány (alapértelmezett) használatesetén az összes többinél alacsonyabb, a többinél magasabbak lesznek, ha az irány a `desc`.

## <a name="examples"></a>Példák

### <a name="filter-examples"></a>Példák szűrőkre

Keresse meg az összes szállodát egy adott referenciaponttól 10 `Edm.GeographyPoint`km-en belül (ahol a hely egy típusú mező):

    geo.distance(location, geography'POINT(-122.131577 47.678581)') le 10

Keresse meg az összes szállodát egy adott nézetablakon belül, `Edm.GeographyPoint`amelyet sokszögként írtak le (ahol a hely típusú). Ne feledje, hogy a sokszög zárva van (az első és az utolsó pontkészletnek azonosnak kell lennie), és [a pontokat az óramutató járásával ellentétes sorrendben kell felsorolni.](https://docs.microsoft.com/rest/api/searchservice/supported-data-types#Anchor_1)

    geo.intersects(location, geography'POLYGON((-122.031577 47.578581, -122.031577 47.678581, -122.131577 47.678581, -122.031577 47.578581))')

### <a name="order-by-examples"></a>Rendelési példák

A szálláshelyek `rating`rendezése a , majd a megadott koordinátáktól való távolság szerint növekvő sorrendben:

    rating desc,geo.distance(location, geography'POINT(-122.131577 47.678581)') asc

A szállodákat csökkenő `search.score` sorrendben rendezheti a szerint és `rating`a , majd növekvő sorrendben a megadott koordinátáktól való távolság szerint, hogy két azonos minősítésű szálloda között a legközelebbi legyen az első:

    search.score() desc,rating desc,geo.distance(location, geography'POINT(-122.131577 47.678581)') asc

## <a name="next-steps"></a>További lépések  

- [Szűrők az Azure Cognitive Search szolgáltatásban](search-filters.md)
- [Az Azure Cognitive Search OData-kifejezés nyelvének áttekintése](query-odata-filter-orderby-syntax.md)
- [Az Azure Cognitive Search OData-kifejezés szintaxisának hivatkozása](search-query-odata-syntax-reference.md)
- [Az Azure Cognitive Search REST API-&#41;&#40;dokumentumok keresése](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)
