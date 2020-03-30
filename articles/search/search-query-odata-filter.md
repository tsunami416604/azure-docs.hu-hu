---
title: OData-szűrő hivatkozása
titleSuffix: Azure Cognitive Search
description: OData nyelvi referencia és teljes szintaxis, amelyet az Azure Cognitive Search-lekérdezésekben szűrőkifejezések létrehozásához használnak.
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
ms.openlocfilehash: b966e9cfa3ef40666dbbd62135f8f964e5eb2023
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79282886"
---
# <a name="odata-filter-syntax-in-azure-cognitive-search"></a>OData $filter szintaxis az Azure Cognitive Search-ben

Az Azure Cognitive Search [oData-szűrőkifejezéseket](query-odata-filter-orderby-syntax.md) használ a teljes szöveges keresési kifejezések mellett további feltételek alkalmazásához a keresési lekérdezésekre. Ez a cikk részletesen ismerteti a szűrők szintaxisát. További általános tudnivalók at, hogy mik a szűrők, és hogyan használhatók konkrét lekérdezési forgatókönyvek megvalósításához, olvassa el a Szűrők az Azure Cognitive Search alkalmazásban című [témakört.](search-filters.md)

## <a name="syntax"></a>Szintaxis

Az OData nyelvben lévő szűrő egy logikai kifejezés, amely viszont számos kifejezéstípus egyike lehet, amint azt a következő EBNF[(Extended Backus-Naur Form):](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form)

<!-- Upload this EBNF using https://bottlecaps.de/rr/ui to create a downloadable railroad diagram. -->

```
boolean_expression ::=
    collection_filter_expression
    | logical_expression
    | comparison_expression
    | boolean_literal
    | boolean_function_call
    | '(' boolean_expression ')'
    | variable

/* This can be a range variable in the case of a lambda, or a field path. */
variable ::= identifier | field_path
```

Interaktív szintaktikai diagram is elérhető:

> [!div class="nextstepaction"]
> [OData-szintaktikai diagram az Azure Cognitive Search szolgáltatáshoz](https://azuresearch.github.io/odata-syntax-diagram/#boolean_expression)

> [!NOTE]
> Lásd: [OData kifejezés szintaxis referencia az Azure Cognitive Search](search-query-odata-syntax-reference.md) a teljes EBNF.

A logikai kifejezések típusai a következők:

- A vagy használatával `any` `all`a program a szűrőkifejezéseket gyűjti. Ezek szűrőfeltételeket alkalmaznak a gyűjteménymezőkre. További információ: [OData collection operátorok az Azure Cognitive Search.](search-query-odata-collection-operators.md)
- Logikai kifejezések, amelyek más logikai kifejezéseket kombinálnak az operátorokkal `and`és `or` `not`a . További információ: [OData logikai operátorok az Azure Cognitive Search.](search-query-odata-logical-operators.md)
- Összehasonlító kifejezések, amelyek a mezőket vagy tartományváltozókat `eq` `ne`állandó `gt` `lt`értékekkel hasonlítják össze az operátorok , , , , `ge`, és `le`. További információ: [OData-összehasonlító operátorok az Azure Cognitive Search.](search-query-odata-comparison-operators.md) Az összehasonlító kifejezések a `geo.distance` függvény használatával a térinformatikai koordináták közötti távolságok összehasonlítására is használhatók. További információ: [OData geospatial functions in Azure Cognitive Search](search-query-odata-geo-spatial-functions.md).
- A logikai `true` szó `false`és . Ezek az állandók néha hasznosak lehetnek, amikor programozott módon szűrőket hoz létre, de egyébként nem használják a gyakorlatban.
- Logikai függvények hívásai, többek között:
  - `geo.intersects`, amely azt vizsgálja, hogy egy adott pont egy adott sokszögen belül van-e. További információ: [OData geospatial functions in Azure Cognitive Search](search-query-odata-geo-spatial-functions.md).
  - `search.in`, amely egy mezőt vagy tartományváltozót hasonlít össze az értéklista egyes értékeivel. További információ: [OData `search.in` függvény az Azure Cognitive Search.](search-query-odata-search-in-function.md)
  - `search.ismatch`és `search.ismatchscoring`a , amely teljes szöveges keresési műveleteket hajt végre egy szűrőkörnyezetben. További információ: [OData full-text search functions in Azure Cognitive Search](search-query-odata-full-text-search-functions.md).
- Mezőgörbék vagy típustípusú `Edm.Boolean`tartományváltozók . Ha például az indexnek logikai `IsEnabled` mezője van, és minden `true`olyan dokumentumot vissza szeretne `IsEnabled`küldeni, ahol ez a mező van, a szűrőkifejezés lehet a neve is.
- Logikai kifejezések zárójelben. Zárójelek használata segíthet a szűrőben lévő műveletek sorrendjének egyértelmű meghatározásában. Az OData operátorok alapértelmezett elsőbbségéről a következő szakaszban olvashat bővebben.

### <a name="operator-precedence-in-filters"></a>Operátori sorrend a szűrőkben

Ha olyan szűrőkifejezést ír, amelynek nincs enek zárójele az alkifejezések körül, az Azure Cognitive Search az operátori elsőbbségi szabályok szerint értékeli ki. Ezek a szabályok azalkifejezések egyesítésére szolgáló operátorokon alapulnak. Az alábbi táblázat operátorcsoportokat sorol fel a legmagasabbtól a legalacsonyabb sorrendig:

| Csoport | Operátor(ok) |
| --- | --- |
| Logikai operátorok | `not` |
| Összehasonlító operátorok | `eq`, `ne`, `gt`, `lt`, `ge`, `le` |
| Logikai operátorok | `and` |
| Logikai operátorok | `or` |

A fenti táblázatban magasabb anamti "szorosabban kötődik" az operandusához, mint más operátorok. Például `and` magasabb a prioritása, `or`mint a , és az összehasonlító operátorok elsőbbséget élveznek, mint bármelyikük, így a következő két kifejezés egyenértékű:

    Rating gt 0 and Rating lt 3 or Rating gt 7 and Rating lt 10
    ((Rating gt 0) and (Rating lt 3)) or ((Rating gt 7) and (Rating lt 10))

Az `not` operátor nak van a legmagasabb prioritása - még magasabb, mint az összehasonlító operátorok. Ezért, ha megpróbál írni egy szűrőt, mint ez:

    not Rating gt 5

A következő hibaüzenet jelenik meg:

    Invalid expression: A unary operator with an incompatible type was detected. Found operand type 'Edm.Int32' for operator kind 'Not'.

Ez a hiba azért fordul elő, mert az operátor csak a `Rating` mezőhöz van társítva, amely típus, `Edm.Int32`és nem a teljes összehasonlító kifejezéshez. A javítás az, hogy az `not` operandusa zárójelben:

    not (Rating gt 5)

<a name="bkmk_limits"></a>

### <a name="filter-size-limitations"></a>A szűrő méretére vonatkozó korlátozások

Az Azure Cognitive Search szolgáltatásba küldhető szűrőkifejezések mérete és összetettsége korlátozott. A korlátok nagyjából a szűrőkifejezésben szereplő záradékok számán alapulnak. Egy jó iránymutatás az, hogy ha több száz záradékot, akkor fennáll a veszélye, hogy túllépi a határértéket. Azt javasoljuk, hogy úgy tervezze meg az alkalmazást, hogy az ne hozzon létre határtalan méretű szűrőket.

> [!TIP]
> A [ `search.in` függvény](search-query-odata-search-in-function.md) használata az egyenlőség-összehasonlítások hosszú eltiltása helyett segíthet elkerülni a szűrőzáradék-korlátot, mivel a függvényhívás egyetlen záradéknak számít.

## <a name="examples"></a>Példák

Keresse meg az összes olyan szállodát, ahol legalább egy szoba van, és az alapára kevesebb, mint 200 $, amely 4-es vagy annál magasabb besorolású:

    $filter=Rooms/any(room: room/BaseRate lt 200.0) and Rating ge 4

Keresse meg az összes szálloda más, mint a "Sea View Motel", hogy már felújított 2010 óta:

    $filter=HotelName ne 'Sea View Motel' and LastRenovationDate ge 2010-01-01T00:00:00Z

Keresse meg az összes hotel, hogy felújították 2010-ben vagy később. A datetime literális adatok a csendes-óceáni téli idő:  

    $filter=LastRenovationDate ge 2010-01-01T00:00:00-08:00

Keresse meg az összes szálloda, amely parkoló tartalmazza, és ahol az összes szoba nemdohányzó:

    $filter=ParkingIncluded and Rooms/all(room: not room/SmokingAllowed)

 \-VAGY -  

    $filter=ParkingIncluded eq true and Rooms/all(room: room/SmokingAllowed eq false)

Keresse meg az összes szálloda, amely luxus vagy parkoló, és a minősítés 5:  

    $filter=(Category eq 'Luxury' or ParkingIncluded eq true) and Rating eq 5

Keresse meg az összes szálloda a tag "wifi" legalább egy szobában `Collection(Edm.String)` (ahol minden szobában van címkék tárolt területen):  

    $filter=Rooms/any(room: room/Tags/any(tag: tag eq 'wifi'))

Keresse meg az összes szálloda minden szoba:  

    $filter=Rooms/any()

Keresse meg az összes olyan szállodát, ahol nincs szoba:

    $filter=not Rooms/any()

Az összes szálloda megkeresése egy adott referenciaponttól 10 km-en belül (ahol `Location` egy mező típus): `Edm.GeographyPoint`

    $filter=geo.distance(Location, geography'POINT(-122.131577 47.678581)') le 10

Keresse meg az összes szállodát egy adott nézetablakon belül, amelyet sokszögként írtak le (ahol `Location` az Edm.GeographyPoint típusú mező). A sokszöget be kell zárni, ami azt jelenti, hogy az első és az utolsó pontkészletnek azonosnak kell lennie. A [pontokat az óramutató járásával ellentétes sorrendben is fel kell sorolni.](https://docs.microsoft.com/rest/api/searchservice/supported-data-types#Anchor_1)

    $filter=geo.intersects(Location, geography'POLYGON((-122.031577 47.578581, -122.031577 47.678581, -122.131577 47.678581, -122.031577 47.578581))')

Keresse meg az összes olyan szállodát, ahol a "Leírás" mező null értékű. A mező null értékű lesz, ha soha nem volt beállítva, vagy ha kifejezetten null értékre volt állítva:  

    $filter=Description eq null

Keresse meg az összes olyan hotelt, amelynek neve megegyezik a "Sea View motel" vagy a "Budget hotel" névvel). Ezek a kifejezések szóközöket tartalmaznak, a szóközök pedig az alapértelmezett határolójel. Harmadik karakterlánc-paraméterként egy idézőjelben alternatív határolószert adhat meg:  

    $filter=search.in(HotelName, 'Sea View motel,Budget hotel', ',')

Keresse meg az összes olyan szállodát, amelynek neve megegyezik a "Sea View motel" vagy a "Budget hotel" névvel, a következővel elválasztva: '|'):  

    $filter=search.in(HotelName, 'Sea View motel|Budget hotel', '|')

Keresse meg az összes szálloda, ahol minden szobában van a tag "wifi" vagy "kád":

    $filter=Rooms/any(room: room/Tags/any(tag: search.in(tag, 'wifi, tub'))

Keressen egyezést a gyűjteményen belüli kifejezéseken, például a "fűtött törölközőtartók" vagy a "hajszárító szerepel" címkékben.

    $filter=Rooms/any(room: room/Tags/any(tag: search.in(tag, 'heated towel racks,hairdryer included', ','))

Keresse meg a "vízparti" szóval ellátott dokumentumokat. Ez a szűrőlekérdezés megegyezik `search=waterfront`a [keresési kérelemmel.](https://docs.microsoft.com/rest/api/searchservice/search-documents)

    $filter=search.ismatchscoring('waterfront')

Keresse meg a "hostel" szót és a 4-es minősítéssel rendelkező dokumentumokat, vagy a "motel" szóval és az 5-ös minősítéssel rendelkező dokumentumokat. Ez a kérés nem fejezhető ki a funkció nélkül, mivel a teljes szöveges keresést kombinálja a `search.ismatchscoring` szűrőműveletekkel a használatával. `or`

    $filter=search.ismatchscoring('hostel') and rating ge 4 or search.ismatchscoring('motel') and rating eq 5

Dokumentumok keresése a "luxus" szó nélkül.

    $filter=not search.ismatch('luxury')

Keresse meg az "óceánnézet" vagy az 5-ös minősítésű dokumentumokat. A `search.ismatchscoring` lekérdezés csak mezőkön `HotelName` `Description`hajtható végre, és a. Azok a dokumentumok, amelyek csak a rendelkezés második záradékának feleltek meg, szintén visszakerülnek - a szállodák 5-tel egyenlők. `Rating` Ezek a dokumentumok nulla pontszámmal kerülnek vissza, hogy egyértelművé tegyék, hogy nem egyeztek a kifejezés egyik pontozott részével sem.

    $filter=search.ismatchscoring('"ocean view"', 'Description,HotelName') or Rating eq 5

Keressen olyan szállodákat, ahol a "hotel" és a "repülőtér" kifejezések nem több, mint öt szó a leírásban, és ahol minden szoba nemdohányzó. Ez a lekérdezés a [teljes Lucene lekérdezési nyelvet](query-lucene-syntax.md)használja.

    $filter=search.ismatch('"hotel airport"~5', 'Description', 'full', 'any') and not Rooms/any(room: room/SmokingAllowed)

## <a name="next-steps"></a>További lépések  

- [Szűrők az Azure Cognitive Search szolgáltatásban](search-filters.md)
- [Az Azure Cognitive Search OData-kifejezés nyelvének áttekintése](query-odata-filter-orderby-syntax.md)
- [Az Azure Cognitive Search OData-kifejezés szintaxisának hivatkozása](search-query-odata-syntax-reference.md)
- [Az Azure Cognitive Search REST API-&#41;&#40;dokumentumok keresése](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)
