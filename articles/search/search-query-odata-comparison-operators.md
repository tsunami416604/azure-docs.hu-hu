---
title: OData-összehasonlító operátor referenciája
titleSuffix: Azure Cognitive Search
description: OData összehasonlító operátorok, EQ, ne, gt, lt, GE és le, az Azure Cognitive Search lekérdezésekben.
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
ms.openlocfilehash: 068e2ec822f0a292ac83b3e48049830eb77b49f6
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/23/2019
ms.locfileid: "72793387"
---
# <a name="odata-comparison-operators-in-azure-cognitive-search---eq-ne-gt-lt-ge-and-le"></a>OData összehasonlító operátorok az Azure-ban Cognitive Search-`eq`, `ne`, `gt`, `lt`, `ge`és `le`

Az Azure Cognitive Search [OData-szűrési kifejezésének](query-odata-filter-orderby-syntax.md) legalapvetőbb művelete egy mező egy adott értékkel való összevetése. Az összehasonlítás két típusa lehetséges – egyenlőség-összehasonlítás és tartomány-összehasonlítás. A következő operátorok segítségével hasonlíthatja össze a mezőket egy állandó értékkel:

Esélyegyenlőségi operátorok:

- `eq`: annak tesztelése, hogy egy mező **egyenlő-** e egy konstans értékkel
- `ne`: annak tesztelése, hogy egy mező **nem egyenlő-** e állandó értékkel

Tartomány operátorai:

- `gt`: annak tesztelése, hogy egy mező nagyobb-e, **mint** egy konstans érték
- `lt`: annak tesztelése, hogy egy mező kisebb-e, **mint** egy konstans érték
- `ge`: annak tesztelése, hogy egy mező **nagyobb-e vagy egyenlő** -e állandó értékkel
- `le`: annak tesztelése, hogy egy mező értéke **kisebb vagy egyenlő** -e állandó értékkel

A tartomány-operátorokat a [logikai operátorokkal](search-query-odata-logical-operators.md) együtt használva ellenőrizheti, hogy egy adott mező bizonyos tartományon belül van-e. Tekintse meg a jelen cikk későbbi részében található [példákat](#examples) .

> [!NOTE]
> Ha szeretné, az állandó értéket az operátor bal oldalán, a mező nevét pedig a jobb oldalon helyezheti el. A tartomány-operátorok esetében az összehasonlítás jelentése fordított. Ha például az állandó érték van a bal oldalon, `gt` teszteli, hogy az állandó érték nagyobb-e, mint a mező. Az összehasonlító operátorok segítségével összehasonlíthatja egy függvény eredményét, például `geo.distance`t egy értékkel. A logikai függvények, például a `search.ismatch`esetében az eredmény összevetése `true` vagy `false` esetén nem kötelező.

## <a name="syntax"></a>Szintaxis

A következő EBNF ([bővített Naur-űrlap](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form)) az összehasonlító operátorokat használó OData-kifejezések nyelvtanát határozzák meg.

<!-- Upload this EBNF using https://bottlecaps.de/rr/ui to create a downloadable railroad diagram. -->

```
comparison_expression ::=
    variable_or_function comparison_operator constant |
    constant comparison_operator variable_or_function

variable_or_function ::= variable | function_call

comparison_operator ::= 'gt' | 'lt' | 'ge' | 'le' | 'eq' | 'ne'
```

Az interaktív szintaxis diagram is elérhető:

> [!div class="nextstepaction"]
> [Az Azure Cognitive Search OData szintaxisának diagramja](https://azuresearch.github.io/odata-syntax-diagram/#comparison_expression)

> [!NOTE]
> Tekintse meg az [Azure Cognitive Search OData-kifejezés szintaxisának referenciáját](search-query-odata-syntax-reference.md) a teljes EBNF.

Az összehasonlító kifejezések két formája létezik. Az egyetlen különbség, hogy az állandó megjelenik-e az operátor bal vagy jobb oldali részén. Az operátor másik oldalán lévő kifejezésnek **változónak** vagy függvény hívásnak kell lennie. A változó lehet egy mezőnév vagy egy tartomány változó egy [lambda kifejezés](search-query-odata-collection-operators.md)esetén.

## <a name="data-types-for-comparisons"></a>Összehasonlítások adattípusai

Az összehasonlító operátorok mindkét oldalán lévő adattípusoknak kompatibilisnek kell lenniük. Ha például a bal oldalon egy `Edm.DateTimeOffset`típusú mező van, akkor a jobb oldalon dátum-idő állandónak kell lennie. A numerikus adattípusok rugalmasabbak. Bármilyen numerikus típus változóit és függvényeit összehasonlíthatja bármilyen más numerikus típussal, néhány korlátozással, az alábbi táblázatban leírtak szerint.

| Változó vagy függvény típusa | Konstans értéktípus | Korlátozások |
| --- | --- | --- |
| `Edm.Double` | `Edm.Double` | Az összehasonlításra a [`NaN`speciális szabályai](#special-case-nan) vonatkoznak |
| `Edm.Double` | `Edm.Int64` | A konstans a `Edm.Double`re lett konvertálva, ami a nagy mennyiségű értékek pontosságának csökkenését eredményezi |
| `Edm.Double` | `Edm.Int32` | – |
| `Edm.Int64` | `Edm.Double` | `NaN`, `-INF`vagy `INF` összehasonlítása nem engedélyezett |
| `Edm.Int64` | `Edm.Int64` | – |
| `Edm.Int64` | `Edm.Int32` | Az összehasonlítás előtt a rendszer `Edm.Int64` konvertálja az állandót |
| `Edm.Int32` | `Edm.Double` | `NaN`, `-INF`vagy `INF` összehasonlítása nem engedélyezett |
| `Edm.Int32` | `Edm.Int64` | – |
| `Edm.Int32` | `Edm.Int32` | – |

A nem engedélyezett összehasonlításokhoz, például a `Edm.Int64` `NaN`típusú mezők összehasonlításához az Azure Cognitive Search REST API "HTTP 400: hibás kérés" hibaüzenetet ad vissza.

> [!IMPORTANT]
> Bár a numerikus típusok összehasonlítása rugalmas, javasoljuk, hogy az összehasonlításokat a szűrőkben is érdemes megírni, hogy az állandó érték ugyanolyan adattípusú legyen, mint a változó vagy a függvény, amelyhez a rendszer hasonlít. Ez különösen akkor fontos, ha a lebegőpontos és az egész értékeket keverik, ahol a pontosságot elveszítő implicit konverziók lehetségesek.

<a name="special-case-nan"></a>

### <a name="special-cases-for-null-and-nan"></a>`null` és `NaN` speciális esetei

Az összehasonlító operátorok használatakor fontos megjegyezni, hogy az Azure Cognitive Search összes nem gyűjteményes mezője potenciálisan `null`. A következő táblázat az összehasonlítási kifejezés összes lehetséges eredményét mutatja, ahol az egyik oldalon lehet `null`:

| Művelet | Eredmény, ha csak a mező vagy változó `null` | Eredmény, ha csak az állandó `null` | Eredmény, ha a mező vagy a változó és az állandó is `null` |
| --- | --- | --- | --- |
| `gt` | `false` | HTTP 400: hibás kérelem hiba | HTTP 400: hibás kérelem hiba |
| `lt` | `false` | HTTP 400: hibás kérelem hiba | HTTP 400: hibás kérelem hiba |
| `ge` | `false` | HTTP 400: hibás kérelem hiba | HTTP 400: hibás kérelem hiba |
| `le` | `false` | HTTP 400: hibás kérelem hiba | HTTP 400: hibás kérelem hiba |
| `eq` | `false` | `false` | `true` |
| `ne` | `true` | `true` | `false` |

Összefoglalva, `null` csak önmagával egyenlő, és nem kevesebb vagy nagyobb, mint bármely más érték.

Ha az indexnek van `Edm.Double` típusú mezői, és fel kell töltenie `NaN` értékeket ezekre a mezőkre, akkor a szűrők írásakor fel kell vennie a fiókot. Az Azure Cognitive Search implementálja `NaN` értékek kezelésére szolgáló IEEE 754 szabványt, az ilyen értékekkel való összehasonlítás pedig nem nyilvánvaló eredményeket eredményez, ahogy az alábbi táblázatban is látható.

| Művelet | Eredmény, ha legalább egy operandus `NaN` |
| --- | --- |
| `gt` | `false` |
| `lt` | `false` |
| `ge` | `false` |
| `le` | `false` |
| `eq` | `false` |
| `ne` | `true` |

Összefoglalva, `NaN` nem egyenlő egyetlen értékkel sem, beleértve a saját magát is.

### <a name="comparing-geo-spatial-data"></a>Geo-térbeli adatainak összehasonlítása

Egy `Edm.GeographyPoint` típusú mező nem hasonlítható össze állandó értékkel, de használhatja a `geo.distance` függvényt is. Ez a függvény `Edm.Double`típusú értéket ad vissza, így összehasonlíthatja azt egy numerikus konstanssal, hogy az állandó geo-térbeli koordináták távolsága alapján szűrhet. Tekintse meg az alábbi [példákat](#examples) .

### <a name="comparing-string-data"></a>Karakterlánc-adatértékek összehasonlítása

A karakterláncokat a `eq` és `ne` operátorok segítségével a pontos egyezésekhez hasonlíthatja a szűrőkben. Ezek az összehasonlítások megkülönböztetik a kis-és nagybetűket.

## <a name="examples"></a>Példák

Olyan dokumentumok egyeztetése, amelyekben a `Rating` mező 3 és 5 közötti, beleértve a következőket:

    Rating ge 3 and Rating le 5

Olyan dokumentumok egyeztetése, amelyekben a `Location` mező kevesebb, mint 2 kilométer a megadott szélességi és hosszúsági fok:

    geo.distance(Location, geography'POINT(-122.031577 47.578581)') lt 2.0

Azok a dokumentumok, amelyekben a `LastRenovationDate` mező értéke nagyobb vagy egyenlő, mint január 1., 2015, UTC éjfél:

    LastRenovationDate ge 2015-01-01T00:00:00.000Z

Olyan dokumentumok egyeztetése, amelyekben a `Details/Sku` mező nem `null`:

    Details/Sku ne null

A dokumentumok egyeztetése olyan hoteleknél, amelyekben legalább egy szoba "Deluxe szoba" típusú, ahol a `Rooms/Type` mező sztringje pontosan megfelel a szűrőnek:

    Rooms/any(room: room/Type eq 'Deluxe Room')

## <a name="next-steps"></a>Következő lépések  

- [Szűrők az Azure Cognitive Search](search-filters.md)
- [Az Azure Cognitive Search OData kifejezés nyelvének áttekintése](query-odata-filter-orderby-syntax.md)
- [Az Azure Cognitive Search OData-kifejezési szintaxisának referenciája](search-query-odata-syntax-reference.md)
- [Dokumentumok &#40;keresése az Azure Cognitive Search REST API&#41;](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)
