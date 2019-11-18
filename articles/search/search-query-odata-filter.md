---
title: OData-szűrő referenciája
titleSuffix: Azure Cognitive Search
description: A OData az Azure Cognitive Search lekérdezésekben a szűrési kifejezések létrehozásához használt nyelvi referenciát és teljes szintaxist.
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
ms.sourcegitcommit: 598c5a280a002036b1a76aa6712f79d30110b98d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/15/2019
ms.locfileid: "74113203"
---
# <a name="odata-filter-syntax-in-azure-cognitive-search"></a>OData $filter szintaxis az Azure-ban Cognitive Search

Az Azure Cognitive Search [OData szűrő kifejezéseket](query-odata-filter-orderby-syntax.md) használ a keresési lekérdezésre vonatkozó további feltételek alkalmazásához a teljes szöveges keresési feltételek mellett. Ez a cikk részletesen ismerteti a szűrők szintaxisát. További általános információk arról, hogy a szűrők milyen módon és hogyan használhatók fel bizonyos lekérdezési forgatókönyvek megvalósításához: [szűrők az Azure Cognitive Searchban](search-filters.md).

## <a name="syntax"></a>Szintaxis

A OData nyelvének egyik szűrője egy logikai kifejezés, amely viszont a különböző típusú kifejezések egyike lehet, ahogyan az a következő EBNF látható ([bővített Naur űrlap](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form)):

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

Az interaktív szintaxis diagram is elérhető:

> [!div class="nextstepaction"]
> [Az Azure Cognitive Search OData szintaxisának diagramja](https://azuresearch.github.io/odata-syntax-diagram/#boolean_expression)

> [!NOTE]
> Tekintse meg az [Azure Cognitive Search OData-kifejezés szintaxisának referenciáját](search-query-odata-syntax-reference.md) a teljes EBNF.

A logikai kifejezések típusai a következők:

- Gyűjtési szűrési kifejezések `any` vagy `all`használatával. Ezek a szűrési feltételek a gyűjtemény mezőire vonatkoznak. További információ: [OData Collection Operators in Azure Cognitive Search](search-query-odata-collection-operators.md).
- Más logikai kifejezéseket kombináló logikai kifejezések `and`, `or`és `not`operátorok használatával. További információ: [OData logikai operátorok az Azure-ban Cognitive Search](search-query-odata-logical-operators.md).
- Az összehasonlító kifejezések, amelyek a mezőket vagy a tartomány változóit összehasonlítják állandó értékekkel az operátorok `eq`, `ne`, `gt`, `lt`, `ge`és `le`használatával. További információ: [OData összehasonlító operátorok az Azure Cognitive Searchban](search-query-odata-comparison-operators.md). Az összehasonlító kifejezések a földrajzi térbeli koordináták közötti távolságok összehasonlítására is használhatók a `geo.distance` függvény használatával. További információ: [OData geo-térbeli függvények az Azure Cognitive Searchban](search-query-odata-geo-spatial-functions.md).
- A logikai literál `true` és `false`. Ezek az állandók esetenként hasznosak lehetnek a szűrők programozott módon történő létrehozásakor, de máskülönben nem általában a gyakorlatban használják őket.
- Logikai függvények hívása, beleértve a következőket:
  - `geo.intersects`, amely azt ellenőrzi, hogy egy adott pont egy adott sokszögen belül van-e. További információ: [OData geo-térbeli függvények az Azure Cognitive Searchban](search-query-odata-geo-spatial-functions.md).
  - `search.in`, amely egy mező-vagy tartomány-változót hasonlít össze az értékek listájában szereplő értékekkel. További információ: [OData `search.in` Function az Azure Cognitive Search](search-query-odata-search-in-function.md).
  - `search.ismatch` és `search.ismatchscoring`, amely teljes szöveges keresési műveleteket hajt végre a szűrő környezetében. További információ: [OData teljes szöveges keresési függvények az Azure Cognitive Searchban](search-query-odata-full-text-search-functions.md).
- A mező elérési útjai vagy a `Edm.Boolean`típusú tartomány változói. Ha például az index egy `IsEnabled` nevű logikai mezővel rendelkezik, és az összes olyan dokumentumot vissza szeretné állítani, ahol ez a mező `true`, akkor a szűrő kifejezése csak a név lehet `IsEnabled`.
- Logikai kifejezések zárójelben. A zárójelek használatával explicit módon határozható meg a szűrők műveleteinek sorrendje. A OData operátorok alapértelmezett sorrendjéről a következő szakaszban talál további információt.

### <a name="operator-precedence-in-filters"></a>Operátori prioritás a szűrőkben

Ha olyan szűrési kifejezést ír, amely nem rendelkezik zárójelekkel az alkifejezések köré, az Azure Cognitive Search az operátor elsőbbségi szabályainak megfelelően értékeli ki azt. Ezek a szabályok azon alapulnak, hogy mely operátorok használják az alkifejezések összevonását. A következő táblázat az operátorok csoportjait sorolja fel a legmagasabbtól a legalacsonyabb prioritásig:

| Csoport | Operátor (ok) |
| --- | --- |
| Logikai operátorok | `not` |
| Összehasonlító operátorok | `eq`, `ne`, `gt`, `lt`, `ge`, `le` |
| Logikai operátorok | `and` |
| Logikai operátorok | `or` |

A fenti táblázatban magasabb szintű operátor "szorosabban" kötődik az operandusokhoz, mint a többi operátor. Például a `and` magasabb prioritású, mint a `or`, és az összehasonlító operátorok magasabb prioritással rendelkeznek, mint bármelyikük, így a következő két kifejezés egyenértékű:

    Rating gt 0 and Rating lt 3 or Rating gt 7 and Rating lt 10
    ((Rating gt 0) and (Rating lt 3)) or ((Rating gt 7) and (Rating lt 10))

A `not` operátor a legmagasabb prioritással rendelkezik – még az összehasonlító operátorok esetében is magasabb. Ezért ha a következőhöz hasonló szűrőt próbál meg írni:

    not Rating gt 5

A következő hibaüzenet jelenik meg:

    Invalid expression: A unary operator with an incompatible type was detected. Found operand type 'Edm.Int32' for operator kind 'Not'.

Ez a hiba azért fordul elő, mert az operátor csak a `Edm.Int32`típusú `Rating` mezőhöz van társítva, és nem a teljes összehasonlító kifejezéssel. A javítás a `not` operandusának zárójelek közé helyezése:

    not (Rating gt 5)

<a name="bkmk_limits"></a>

### <a name="filter-size-limitations"></a>A szűrő méretére vonatkozó korlátozások

Az Azure Cognitive Searchba küldendő szűrési kifejezések mérete és összetettsége korlátozott. A határértékek nagyjából a szűrési kifejezésben szereplő záradékok számán alapulnak. Egy jó iránymutatás, hogy ha több száz záradékkal rendelkezik, azzal a kockázattal jár, hogy túllépi a korlátot. Azt javasoljuk, hogy az alkalmazást úgy tervezze meg, hogy ne állítson be nem kötött méretű szűrőket.

> [!TIP]
> A `search.in` függvény használata az egyenlőségi összehasonlítások hosszú [kiosztása](search-query-odata-search-in-function.md) helyett segít elkerülni a szűrő záradék korlátját, mivel a függvények hívása egyetlen záradékként számít.

## <a name="examples"></a>Példák

Az összes olyan szálloda megkeresése, amelynek legalább egy olyan szobája van, amelynek a 4-es vagy annál magasabb névleges $200-nál kisebb a száma:

    $filter=Rooms/any(room: room/BaseRate lt 200.0) and Rating ge 4

A "Sea View Motel" kivételével minden olyan szálloda megkeresése, amelyet a 2010 óta újítottak fel:

    $filter=HotelName ne 'Sea View Motel' and LastRenovationDate ge 2010-01-01T00:00:00Z

A 2010-es vagy újabb verzióban felújított összes Hotel megkeresése. A DateTime konstans a csendes-óceáni téli idő időzónájának adatait tartalmazza:  

    $filter=LastRenovationDate ge 2010-01-01T00:00:00-08:00

Az összes olyan Hotel megkeresése, amely tartalmazza a parkolóhelyet, és ahol az összes szoba nem dohányzó:

    $filter=ParkingIncluded and Rooms/all(room: not room/SmokingAllowed)

 \- vagy-  

    $filter=ParkingIncluded eq true and Rooms/all(room: room/SmokingAllowed eq false)

Keresse meg az összes olyan szállodát, amely luxust vagy parkolót tartalmaz, és a minősítésük 5:  

    $filter=(Category eq 'Luxury' or ParkingIncluded eq true) and Rating eq 5

A "WiFi" címkével ellátott összes Hotel megkeresése legalább egy helyiségben (ahol minden szobához egy `Collection(Edm.String)` mezőben tárolt címke tartozik):  

    $filter=Rooms/any(room: room/Tags/any(tag: tag eq 'wifi'))

Minden olyan szálloda megkeresése, amely tartalmaz egy tetszőleges szobát:  

    $filter=Rooms/any()

Az összes olyan Hotel megkeresése, amely nem rendelkezik szobával:

    $filter=not Rooms/any()

Az összes Hotel megkeresése egy adott hivatkozási ponttól 10 kilométeren belül (ahol a `Location` `Edm.GeographyPoint`típusú mező):

    $filter=geo.distance(Location, geography'POINT(-122.131577 47.678581)') le 10

A megadott nézetablakban lévő összes Hotel megkeresése sokszögként (ahol `Location` EDM. Geographypoint adattípuson típusú mező.) A sokszöget be kell zárni, ami azt jelenti, hogy az első és az utolsó pont készletének meg kell egyeznie. Emellett [a pontoknak a sorrendben](https://docs.microsoft.com/rest/api/searchservice/supported-data-types#Anchor_1)kell szerepelniük.

    $filter=geo.intersects(Location, geography'POLYGON((-122.031577 47.578581, -122.031577 47.678581, -122.131577 47.678581, -122.031577 47.578581))')

Az összes olyan szálláshely megkeresése, ahol a "Leírás" mező null értékű. A mező értéke null, ha nem volt beállítva, vagy ha explicit módon NULL értékre lett állítva:  

    $filter=Description eq null

Az összes olyan Hotel megkeresése, amelynek neve egyenlő a "Sea View Motel" vagy a "Budget Hotel" névvel. Ezek a kifejezések szóközöket tartalmaznak, a szóköz pedig egy alapértelmezett határolójel. Az idézőjelek között egy alternatív határolójelet is megadhat a harmadik sztring paraméterként:  

    $filter=search.in(HotelName, 'Sea View motel,Budget hotel', ',')

Az összes olyan Hotel megkeresése, amelynek neve egyenlő a "Sea View Motel" vagy a "Budget Hotel" névvel, a következővel elválasztva: "|".  

    $filter=search.in(HotelName, 'Sea View motel|Budget hotel', '|')

Az összes olyan Hotel megkeresése, ahol az összes szoba a "WiFi" vagy a "kád" címkével rendelkezik:

    $filter=Rooms/any(room: room/Tags/any(tag: search.in(tag, 'wifi, tub'))

Megtalálhatja a gyűjteményen belüli kifejezéseket, például a "fűtött törölköző állványok" vagy a "hajszárító" címkét.

    $filter=Rooms/any(room: room/Tags/any(tag: search.in(tag, 'heated towel racks,hairdryer included', ','))

Keresse meg a "Waterfront" szót tartalmazó dokumentumokat. Ez a szűrési lekérdezés megegyezik a `search=waterfront`val rendelkező [keresési kérelemmel](https://docs.microsoft.com/rest/api/searchservice/search-documents) .

    $filter=search.ismatchscoring('waterfront')

Keresse meg a "Hostel" szót tartalmazó dokumentumokat, vagy a 4-es vagy annál nagyobb minősítést, vagy a "Motel" szót tartalmazó dokumentumokat, valamint az 5 értékű minősítést. Ezt a kérést nem lehetett kifejezni a `search.ismatchscoring` függvény nélkül, mert a teljes szöveges keresést kombinálja a szűrési műveletekkel `or`használatával.

    $filter=search.ismatchscoring('hostel') and rating ge 4 or search.ismatchscoring('motel') and rating eq 5

Dokumentumok keresése a "luxus" szó nélkül.

    $filter=not search.ismatch('luxury')

Megkeresheti az "Ocean View" kifejezéssel vagy 5 értékkel egyenlő minősítéssel rendelkező dokumentumokat. Az `search.ismatchscoring`-lekérdezés csak a `HotelName` és a `Description`mezőkön lesz végrehajtva. Azok a dokumentumok, amelyek megfelelnek az eltérésnek, csak a második záradékot adják vissza. a--Hotels `Rating` egyenlő, mint 5. Ezeket a dokumentumokat a pontszám nullával adja vissza, így egyértelművé válik, hogy nem felelnek meg a kifejezés egyik pontszámának sem.

    $filter=search.ismatchscoring('"ocean view"', 'Description,HotelName') or Rating eq 5

Itt megtalálhatja azokat a szállodákat, amelyekben a "Hotel" és a "repülőtér" kifejezés nem több mint öt szót tartalmaz a leírásban, és ahol az összes szoba nem dohányzó. Ez a lekérdezés a [teljes Lucene lekérdezési nyelvet](query-lucene-syntax.md)használja.

    $filter=search.ismatch('"hotel airport"~5', 'Description', 'full', 'any') and not Rooms/any(room: room/SmokingAllowed)

## <a name="next-steps"></a>További lépések  

- [Szűrők az Azure Cognitive Search](search-filters.md)
- [Az Azure Cognitive Search OData kifejezés nyelvének áttekintése](query-odata-filter-orderby-syntax.md)
- [Az Azure Cognitive Search OData-kifejezési szintaxisának referenciája](search-query-odata-syntax-reference.md)
- [Dokumentumok &#40;keresése az Azure Cognitive Search REST API&#41;](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)
