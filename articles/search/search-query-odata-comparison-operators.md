---
title: OData összehasonlító operátorhivatkozás
titleSuffix: Azure Cognitive Search
description: Szintaxis és referencia dokumentáció az Azure Cognitive Search lekérdezésekben az OData-összehasonlító operátorok (eq, ne, gt, lt, ge és le) használatával.
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
ms.openlocfilehash: 62c8c93e07326e776cbe089042abc481544794bc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74113225"
---
# <a name="odata-comparison-operators-in-azure-cognitive-search---eq-ne-gt-lt-ge-and-le"></a>Az Azure Cognitive Search `eq`- , `ne` `gt`, `lt` `ge`, , és oData-összehasonlító operátorai`le`

Az Azure Cognitive Search [OData-szűrőkifejezésének](query-odata-filter-orderby-syntax.md) legalapvetőbb művelete egy mező és egy adott érték összehasonlítása. Kétféle összehasonlítás lehetséges : egyenlőség összehasonlítása és tartomány-összehasonlítás. A következő operátorok segítségével hasonlíthatja össze a mezőt egy állandó értékkel:

Esélyegyenlőségi szereplők:

- `eq`: Ellenőrizze, hogy egy mező **egyenlő-e** állandó értékkel
- `ne`: Annak vizsgálata, hogy egy mező **nem egyenlő-e** állandó értékkel

Tartomány operátorok:

- `gt`: Annak vizsgálata, hogy egy mező nagyobb-e állandó **értéknél**
- `lt`: Annak vizsgálata, hogy egy mező kisebb-e állandó **értéknél**
- `ge`: Annak vizsgálata, hogy egy mező nagyobb vagy egyenlő-e állandó **értékkel**
- `le`: Annak vizsgálata, hogy egy mező **kisebb vagy egyenlő-e** állandó értékkel

A tartományoperátorok és a [logikai operátorok](search-query-odata-logical-operators.md) együttes használatával ellenőrizheti, hogy egy mező egy bizonyos értéktartományon belül van-e. Tekintse meg a cikk későbbi [példáit.](#examples)

> [!NOTE]
> Ha szeretné, az állandó értéket az operátor bal oldalára, a mezőnevét pedig a jobb oldalra helyezheti. Tartományoperátorok esetében az összehasonlítás jelentése megfordul. Ha például az állandó érték a `gt` bal oldalon van, akkor azt kell tesztelni, hogy az állandó érték nagyobb-e, mint a mező. Az összehasonlító operátorok segítségével összehasonlíthatja egy függvény, például `geo.distance`a, egy értékkel eredményét. Logikai függvények, `search.ismatch`például a `true` `false` , az eredmény összehasonlítása a vagy nem kötelező.

## <a name="syntax"></a>Szintaxis

A következő EBNF ([Extended Backus-Naur Form](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form)) határozza meg az összehasonlító operátorokat használó OData-kifejezések nyelvtanát.

<!-- Upload this EBNF using https://bottlecaps.de/rr/ui to create a downloadable railroad diagram. -->

```
comparison_expression ::=
    variable_or_function comparison_operator constant |
    constant comparison_operator variable_or_function

variable_or_function ::= variable | function_call

comparison_operator ::= 'gt' | 'lt' | 'ge' | 'le' | 'eq' | 'ne'
```

Interaktív szintaktikai diagram is elérhető:

> [!div class="nextstepaction"]
> [OData-szintaktikai diagram az Azure Cognitive Search szolgáltatáshoz](https://azuresearch.github.io/odata-syntax-diagram/#comparison_expression)

> [!NOTE]
> Lásd: [OData kifejezés szintaxis referencia az Azure Cognitive Search](search-query-odata-syntax-reference.md) a teljes EBNF.

Az összehasonlító kifejezéseknek két formája van. Az egyetlen különbség közöttük az, hogy az állandó megjelenik-e a kezelő bal vagy jobb oldalán. Az operátor másik oldalán lévő kifejezésnek **változónak** vagy függvényhívásnak kell lennie. [Lambda kifejezés](search-query-odata-collection-operators.md)esetén a változó lehet mezőnév vagy tartományváltozó.

## <a name="data-types-for-comparisons"></a>Adattípusok összehasonlításhoz

Az összehasonlító operátor mindkét oldalán lévő adattípusoknak kompatibiliseknek kell lenniük. Ha például a bal oldal egy `Edm.DateTimeOffset`típusú mező, akkor a jobb oldalnak dátum-idő állandónak kell lennie. A numerikus adattípusok rugalmasabbak. Bármilyen numerikus típusú változót és függvényt bármely más numerikus típusú állandóval összehasonlíthat, néhány korlátozással, az alábbi táblázatban leírtak szerint.

| Változó vagy függvénytípusa | Állandó érték típusa | Korlátozások |
| --- | --- | --- |
| `Edm.Double` | `Edm.Double` | Az összehasonlításra [különleges `NaN` szabályok vonatkoznak a](#special-case-nan) |
| `Edm.Double` | `Edm.Int64` | Az állandót `Edm.Double`a program átalakítja a programba, ami a nagy nagyságrendű értékek pontosságának csökkenését eredményezi. |
| `Edm.Double` | `Edm.Int32` | n/a |
| `Edm.Int64` | `Edm.Double` | Összehasonlítása `NaN`a `-INF`,, vagy `INF` nem engedélyezett |
| `Edm.Int64` | `Edm.Int64` | n/a |
| `Edm.Int64` | `Edm.Int32` | Az állandó `Edm.Int64` ta-k összehasonlítás előtti értékké alakítják |
| `Edm.Int32` | `Edm.Double` | Összehasonlítása `NaN`a `-INF`,, vagy `INF` nem engedélyezett |
| `Edm.Int32` | `Edm.Int64` | n/a |
| `Edm.Int32` | `Edm.Int32` | n/a |

A nem engedélyezett összehasonlítások, például egy `Edm.Int64` mező `NaN`összehasonlítása a , az Azure Cognitive Search REST API a "HTTP 400: Bad Request" hibaüzenetet ad vissza.

> [!IMPORTANT]
> Annak ellenére, hogy a numerikus típusok összehasonlítása rugalmas, javasoljuk, hogy az összehasonlításokat szűrőkben írja, hogy az állandó érték ugyanolyan típusú legyen, mint az a változó vagy függvény, amelyhez hasonlítják. Ez különösen fontos lebegőpontos és egész értékek keverésekénél, ahol a pontosságot elveszítő implicit konverziók lehetségesek.

<a name="special-case-nan"></a>

### <a name="special-cases-for-null-and-nan"></a>Különleges esetek `null` és`NaN`

Az összehasonlító operátorok használatakor fontos megjegyezni, hogy az Azure Cognitive Search összes `null`nem gyűjteménymezőjének potenciálisan lehet. Az alábbi táblázat az összehasonlítási kifejezés összes lehetséges eredményét `null`mutatja be, ahol bármelyik oldal lehet:

| Művelet | Eredmény, ha csak a mező vagy a változó`null` | Eredmény, ha csak az állandó`null` | Eredmény, ha mind a mező, mind a változó, mind az állandó`null` |
| --- | --- | --- | --- |
| `gt` | `false` | HTTP 400: Hibás kérési hiba | HTTP 400: Hibás kérési hiba |
| `lt` | `false` | HTTP 400: Hibás kérési hiba | HTTP 400: Hibás kérési hiba |
| `ge` | `false` | HTTP 400: Hibás kérési hiba | HTTP 400: Hibás kérési hiba |
| `le` | `false` | HTTP 400: Hibás kérési hiba | HTTP 400: Hibás kérési hiba |
| `eq` | `false` | `false` | `true` |
| `ne` | `true` | `true` | `false` |

Összefoglalva, `null` egyenlő csak magát, és nem kevesebb vagy nagyobb, mint bármely más érték.

Ha az index típusmezőket `Edm.Double` `NaN` tartalmaz, és értékeket tölt fel ezekbe a mezőkbe, a szűrők írásakor ezt figyelembe kell vennie. Az Azure Cognitive Search megvalósítja az IEEE 754 szabvány értékek kezelésére, `NaN` és az ilyen értékek összehasonlítása nem nyilvánvaló eredményeket, ahogy az alábbi táblázatban látható.

| Művelet | Eredmény, ha legalább egy operandi`NaN` |
| --- | --- |
| `gt` | `false` |
| `lt` | `false` |
| `ge` | `false` |
| `le` | `false` |
| `eq` | `false` |
| `ne` | `true` |

Összefoglalva, `NaN` nem egyenlő semmilyen értékkel, beleértve magát is.

### <a name="comparing-geo-spatial-data"></a>Földrajzi téradatok összehasonlítása

Az állandó értékkel rendelkező típusú `Edm.GeographyPoint` mezőt nem lehet közvetlenül `geo.distance` összehasonlítani, de használhatja a függvényt. Ez a függvény típusértéket `Edm.Double`ad vissza, így összehasonlíthatja egy numerikus állandóval, hogy az állandó térinformatikai koordinátáktól való távolság alapján szűrjön. Lásd az alábbi [példákat.](#examples)

### <a name="comparing-string-data"></a>Karakterláncadatok összehasonlítása

A karakterláncok a pontos egyezések `ne` szűrőivel összehasonlíthatók a és operátorokkal. `eq` Ezek az összehasonlítások a kis- és nagybetűket is figyelembe vevő adatok.

## <a name="examples"></a>Példák

Olyan dokumentumok `Rating` egyeztetése, amelyeknél a mező 3 és 5 között van, a következőket is beleértve:

    Rating ge 3 and Rating le 5

Olyan dokumentumok `Location` egyeztetése, amelyeknél a mező kevesebb, mint 2 km-re van az adott szélességi és hosszúsági foktól:

    geo.distance(Location, geography'POINT(-122.031577 47.578581)') lt 2.0

Azolyan dokumentumok `LastRenovationDate` egyeztetése, amelyeknél a mező nagyobb vagy egyenlő, mint 2015.

    LastRenovationDate ge 2015-01-01T00:00:00.000Z

Olyan dokumentumok `Details/Sku` egyeztetése, `null`amelyeknél a mező nem:

    Details/Sku ne null

Megegyeznie kell azoknak a szállodáknak a dokumentumait, ahol `Rooms/Type` legalább egy szoba "Deluxe Room" típusú, ahol a mező húrja pontosan megegyezik a szűrővel:

    Rooms/any(room: room/Type eq 'Deluxe Room')

## <a name="next-steps"></a>További lépések  

- [Szűrők az Azure Cognitive Search szolgáltatásban](search-filters.md)
- [Az Azure Cognitive Search OData-kifejezés nyelvének áttekintése](query-odata-filter-orderby-syntax.md)
- [Az Azure Cognitive Search OData-kifejezés szintaxisának hivatkozása](search-query-odata-syntax-reference.md)
- [Az Azure Cognitive Search REST API-&#41;&#40;dokumentumok keresése](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)
