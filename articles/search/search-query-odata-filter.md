---
title: OData-szűrő referencia – Azure Search
description: OData-nyelvi dokumentáció az Azure Search-lekérdezések szűrőszintaxisának.
ms.date: 06/13/2019
services: search
ms.service: search
ms.topic: conceptual
author: Brjohnstmsft
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
ms.openlocfilehash: c44645ebcbf8808cc929bfaa0c3cb0d3ee9c90cd
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "67079911"
---
# <a name="odata-filter-syntax-in-azure-search"></a>Az Azure Search OData $filter-szintaxis

Használja az Azure Search [OData szűrési kifejezésekben](query-odata-filter-orderby-syntax.md) a további feltételek alkalmazása egy lekérdezés teljes szöveges keresési feltételek mellett. Ez a cikk ismerteti részletesen szűrők szintaxisa a következő. Szűrők vannak és kihasználására – az adott lekérdezés forgatókönyveket használatával kapcsolatos további általános információkért lásd: [szűrők az Azure Search](search-filters.md).

## <a name="syntax"></a>Szintaxis

Egy szűrő, az OData-nyelven egy logikai kifejezés, amely ezután lehet számos különböző típusú kifejezés, ahogy azt a következő EBNF ([kiterjesztett Backus-Naur űrlap](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form)):

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

Egy interaktív szintaxisdiagramja is érhető el:

> [!div class="nextstepaction"]
> [Azure Search OData szintaxisdiagramja](https://azuresearch.github.io/odata-syntax-diagram/#boolean_expression)

> [!NOTE]
> Lásd: [OData kifejezés szintaxisának referenciája az Azure Search](search-query-odata-syntax-reference.md) a teljes EBNF számára.

A logikai kifejezésen típusai a következők:

- Gyűjtemény szűrőkifejezésekben használatával `any` vagy `all`. Gyűjtemény mezők ezek szűrési feltételek vonatkoznak. További információkért lásd: [az Azure Search OData-gyűjtemény operátorok](search-query-odata-collection-operators.md).
- Logikai kifejezéseket, amelyek más logikai kifejezésen, az operátorok használatával `and`, `or`, és `not`. További információkért lásd: [OData logikai operátorokat, az Azure Search](search-query-odata-logical-operators.md).
- Összehasonlítási kifejezésekben, amely mezők összehasonlítása vagy tartományt, az operátorok használatával állandó értékek változókat `eq`, `ne`, `gt`, `lt`, `ge`, és `le`. További információkért lásd: [összehasonlító OData-operátorok az Azure Search](search-query-odata-comparison-operators.md). Összehasonlítási kifejezésekben is használhatók, hasonlítsa össze a földrajzi koordináták segítségével közötti távolságot a `geo.distance` függvény. További információkért lásd: [OData térinformatikai funkciók az Azure Search](search-query-odata-geo-spatial-functions.md).
- A logikai literálok `true` és `false`. Ezt a állandót akkor hasznos, néha Ha létrehozása programozott módon a szűrőket, de egyébként nem használható a gyakorlatban.
- Logikai függvények, beleértve a hívások:
  - `geo.intersects`, amely teszteli, hogy e egy adott időpontra belül egy adott sokszög. További információkért lásd: [OData térinformatikai funkciók az Azure Search](search-query-odata-geo-spatial-functions.md).
  - `search.in`, amely összehasonlítja a egy mezőben vagy a tartomány változót minden érték értékekből álló listát. További információkért lásd: [OData `search.in` az Azure Search függvény](search-query-odata-search-in-function.md).
  - `search.ismatch` és `search.ismatchscoring`, amely a teljes szöveges keresési műveletek végrehajtásához szűrőkörnyezetben. További információkért lásd: [OData teljes szöveges keresési funkciók az Azure Search](search-query-odata-full-text-search-functions.md).
- Elérési út mezőben vagy a tartomány típusú változók `Edm.Boolean`. Például, ha az index rendelkezik logikai mezője `IsEnabled` és be szeretné olvasni összes dokumentumot, ahol ezt a mezőt kötelező `true`, a szűrési kifejezés csak a név lehet `IsEnabled`.
- A logikai kifejezéseket zárójelek között. Zárójelek segítségével explicit módon meghatározni a műveletek sorrendje a szűrő segítségével. Az OData-operátorok az alapértelmezett sorrend a további információkért tekintse meg a következő szakaszban.

### <a name="operator-precedence-in-filters"></a>A szűrők precedencia

A szűrőkifejezés nincs az alkifejezések zárójelbe ír, ha a Azure Search értékeli azt a operátor végrehajtására vonatkozó elsőbbségi szabályok megfelelően. Ezek a szabályok alapján úgy, hogy az alkifejezések melyik operátorok használhatók. Az alábbi táblázat a csoportok sorrendben legmagasabbtól a legalacsonyabb prioritású operátort:

| Csoport | Üzemeltetőinek |
| --- | --- |
| Logikai operátorok | `not` |
| Összehasonlító operátorok | `eq`, `ne`, `gt`, `lt`, `ge`, `le` |
| Logikai operátorok | `and` |
| Logikai operátorok | `or` |

A fenti táblázatban szereplő magasabb operátor "köti nagyobb mértékben", mint más operátorokkal operandus. Ha például `and` nagyobb prioritással, mint a `or`, és összehasonlító operátorok nagyobb prioritással, mint hogy őket, ezért az alábbi két kifejezés megfelelői:

    Rating gt 0 and Rating lt 3 or Rating gt 7 and Rating lt 10
    ((Rating gt 0) and (Rating lt 3)) or ((Rating gt 7) and (Rating lt 10))

A `not` üzemeltető összes – még magasabb, mint a összehasonlító operátorok a legmagasabb prioritással rendelkezik. Miért, amely akkor, ha a hasonló szűrő írásakor:

    not Rating gt 5

Ez a hibaüzenet jelenik meg:

    Invalid expression: A unary operator with an incompatible type was detected. Found operand type 'Edm.Int32' for operator kind 'Not'.

Ez a hiba akkor fordul elő, mert az operátor társítva csak a `Rating` mező, amelynek a típusa `Edm.Int32`, és nem a teljes összehasonlítási kifejezésben. A javítás, hogy helyezze operandusa `not` zárójelek között:

    not (Rating gt 5)

<a name="bkmk_limits"></a>

### <a name="filter-size-limitations"></a>Szűrő fájlméretre vonatkozó korlátozások

A méret és összetettségét, melyet elküldhet az Azure Search szűrőkifejezésekben korlátozva van. A korlátok nagyjából a szűrőkifejezést záradékai száma alapulnak. Egy jól szemlélteti, hogy ha több száz szolgáló szerződéses klauzulák, áll fennáll a kockázata, meghaladja a korlátot. Azt javasoljuk, hogy az alkalmazás úgy, hogy nem készítése a korlátlan méretű szűrők tervezése.

> [!TIP]
> Használatával [a `search.in` függvény](search-query-odata-search-in-function.md) helyett az egyenlőség hosszú disjunctions összehasonlítások elkerülhetők a záradék korlátot, mivel egyetlen záradék számít a függvény hívásához szükséges.

## <a name="examples"></a>Példák

Keresse meg azt az alapdíj mellett legalább egy helyet az összes "Hotels" kisebb, mint 200 USD értékű, vagy afölött 4 értékelése:

    $filter=Rooms/any(room: room/BaseRate lt 200.0) and Rating ge 4

Keresse meg az összes hotels "Tenger nézetet, amelyben" kivételével, amely rendelkezik lett felújított 2010 óta:

    $filter=HotelName ne 'Sea View Motel' and LastRenovationDate ge 2010-01-01T00:00:00Z

Voltak felújított 2010 vagy újabb verzió, amely az összes hotels találja. Szövegkonstans dátum és idő a csendes-óceáni téli idő időzóna-információkat tartalmazza:  

    $filter=LastRenovationDate ge 2010-01-01T00:00:00-08:00

Keresse meg az összes hotels, amelyek ideiglenes tartalmazza, és ahol minden termek nem fogyasztási:

    $filter=ParkingIncluded and Rooms/all(room: not room/SmokingAllowed)

 \- VAGY –  

    $filter=ParkingIncluded eq true and Rooms/all(room: room/SmokingAllowed eq false)

Keresse meg az összes hotels engedélyezhető illetve ideiglenes közé tartozik és rendelkezik minősítéssel az 5-ből:  

    $filter=(Category eq 'Luxury' or ParkingIncluded eq true) and Rating eq 5

Keresse meg a "Wi-Fi" címkével ellátott összes hotels legalább egy helyiségben (ahol a minden hely tárolt címkékkel rendelkezik egy `Collection(Edm.String)` mező):  

    $filter=Rooms/any(room: room/Tags/any(tag: tag eq 'wifi'))

Keresse meg az összes szállodák bármely termek:  

    $filter=Rooms/any()

Keresse meg, amelyek nem rendelkeznek termek összes hotels:

    $filter=not Rooms/any()

Keresse meg az összes szállodák egy adott hivatkozási pont 10 kilométerben belül (amennyiben `Location` típusú mező `Edm.GeographyPoint`):

    $filter=geo.distance(Location, geography'POINT(-122.131577 47.678581)') le 10

Keresse meg az összes szállodák egy sokszög szerepeltek egy adott nézőponton belül (ahol `Location` Edm.GeographyPoint típusú mező). A sokszög be kell zárni, azaz első és utolsó pont csoportok azonosnak kell lennie. Ezenkívül [szerepelnie kell a pontok járásával sorrendben](https://docs.microsoft.com/rest/api/searchservice/supported-data-types#Anchor_1).

    $filter=geo.intersects(Location, geography'POLYGON((-122.031577 47.578581, -122.031577 47.678581, -122.131577 47.678581, -122.031577 47.578581))')

Keresse meg az összes "Hotels", ahol a "Leírás" mező értéke null. A mező üres lesz, ha soha nem lett beállítva, vagy ha explicit módon be lett állítva a null:  

    $filter=Description eq null

Keresse meg az összes "Hotels" nevű "Tenger nézet amelyben" vagy "Költségvetés hotel" egyenlő). Ezek a kifejezések tartalmazhatnak szóközt, és a hely egy alapértelmezett elválasztó karaktert. A harmadik paramétereként karakterlánc egy másik elválasztó szimpla idézőjelben adhatja meg:  

    $filter=search.in(HotelName, 'Sea View motel,Budget hotel', ',')

Keresse meg az összes "Hotels" nevű egyenlő "Tenger nézet amelyben" vagy a "Budget hotel" elválasztva ' |} "):  

    $filter=search.in(HotelName, 'Sea View motel|Budget hotel', '|')

Ha minden termek rendelkeznek a címke "Wi-Fi" vagy "vödör" összes "Hotels" keresése:

    $filter=Rooms/any(room: room/Tags/any(tag: search.in(tag, 'wifi, tub'))

Címkék-gyűjteményekben, például "fűtött törülköző állványt" vagy "hajszárító tartalmaz" kifejezések egyezés található.

    $filter=Rooms/any(room: room/Tags/any(tag: search.in(tag, 'heated towel racks,hairdryer included', ','))

Keresse meg az "i partszakasz" szót tartalmazó dokumentumokat. Ez a szűrő-lekérdezés megegyezik egy [keresési kérelmet](https://docs.microsoft.com/rest/api/searchservice/search-documents) a `search=waterfront`.

    $filter=search.ismatchscoring('waterfront')

Keresse meg a dokumentumot a word "hostel" és a nagyobb vagy egyenlő "amelyben" szót tartalmazó dokumentumok vagy 4 minősítés és minősítés megegyezik az 5. Ezt a kérést nem lehetett megadni nélkül a `search.ismatchscoring` működik, mivel a teljes szöveges keresési szűrő műveletekbe kombinálja `or`.

    $filter=search.ismatchscoring('hostel') and rating ge 4 or search.ismatchscoring('motel') and rating eq 5

Keresse meg a dokumentumok szó nélkül "engedélyezhető".

    $filter=not search.ismatch('luxury')

Keresse meg a dokumentumok minősítési értéknek 5 vagy "óceán view" kifejezés helyett szerepel. A `search.ismatchscoring` lekérdezés hajtani, csak a mező alapján `HotelName` és `Description`. Dokumentumok, amely megfelel a vagy műveletet csak a második záradék visszaad túl – a "Hotels" `Rating` megegyezik az 5. Ezeket a dokumentumokat fogja visszaadni a pontszám nulla, hogy törölje a jelet, hogy azok nem megfelelő a kifejezés a pontozott részeit.

    $filter=search.ismatchscoring('"ocean view"', 'Description,HotelName') or Rating eq 5

Keresse meg a "Hotels", ahol a feltételek "hotel" és "repülőtér" egymástól a leírás legfeljebb öt szavakat, és ahol minden termek nem fogyasztási. Ez a lekérdezés használ a [teljes Lucene lekérdezési nyelv](query-lucene-syntax.md).

    $filter=search.ismatch('"hotel airport"~5', 'Description', 'full', 'any') and not Rooms/any(room: room/SmokingAllowed)

## <a name="next-steps"></a>További lépések  

- [Szűrők az Azure Search szolgáltatásban](search-filters.md)
- [Az Azure Search OData kifejezés nyelvi – áttekintés](query-odata-filter-orderby-syntax.md)
- [Az Azure Search OData kifejezés szintaxisának referenciája](search-query-odata-syntax-reference.md)
- [Dokumentumok keresése &#40;az Azure Search szolgáltatás REST API-ja&#41;](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)
