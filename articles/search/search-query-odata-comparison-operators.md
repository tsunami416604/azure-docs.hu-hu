---
title: OData-összehasonlító operátor referenciája – Azure Search
description: OData összehasonlító operátorok, EQ, ne, gt, lt, GE és le Azure Search lekérdezésekben.
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
ms.openlocfilehash: a8bd8b05fd874e05e5e59042d461f4a4286c81e4
ms.sourcegitcommit: bb8e9f22db4b6f848c7db0ebdfc10e547779cccc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/20/2019
ms.locfileid: "69648066"
---
# <a name="odata-comparison-operators-in-azure-search---eq-ne-gt-lt-ge-and-le"></a>OData összehasonlító operátorok a Azure Search `eq`- `ne`, `gt`, `lt` `ge`,, és`le`

A Azure Search egyik [OData](query-odata-filter-orderby-syntax.md) -szűrési kifejezésének legalapvetőbb művelete egy mező egy adott értékkel való összevetése. Az összehasonlítás két típusa lehetséges – egyenlőség-összehasonlítás és tartomány-összehasonlítás. A következő operátorok segítségével hasonlíthatja össze a mezőket egy állandó értékkel:

Esélyegyenlőségi operátorok:

- `eq`: Annak tesztelése, hogy a mező **egyenlő-** e egy konstans értékkel
- `ne`: Annak tesztelése, hogy egy mező **nem egyenlő-** e állandó értékkel

Tartomány operátorai:

- `gt`: Annak tesztelése, hogy a mező nagyobb-e, **mint** az állandó érték
- `lt`: Annak tesztelése, hogy a mező kisebb-e, **mint** a konstans érték
- `ge`: Annak tesztelése, hogy a mező **nagyobb-e vagy egyenlő-** e állandó értékkel
- `le`: Annak tesztelése, hogy a mező **kisebb vagy egyenlő-** e állandó értékkel

A tartomány-operátorokat a [logikai operátorokkal](search-query-odata-logical-operators.md) együtt használva ellenőrizheti, hogy egy adott mező bizonyos tartományon belül van-e. Tekintse [](#examples) meg a jelen cikk későbbi részében található példákat.

> [!NOTE]
> Ha szeretné, az állandó értéket az operátor bal oldalán, a mező nevét pedig a jobb oldalon helyezheti el. A tartomány-operátorok esetében az összehasonlítás jelentése fordított. Ha például az állandó érték a bal oldalon van, akkor teszteli, hogy az állandó érték nagyobb-e, `gt` mint a mező. Az összehasonlító operátorok segítségével összehasonlíthatja a függvények eredményét, például `geo.distance`egy értéket is. Olyan logikai függvények esetében `search.ismatch`, mint például a, az `true` eredmény vagy `false` a választható érték összevetése.

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
> [Azure Search OData szintaxisának diagramja](https://azuresearch.github.io/odata-syntax-diagram/#comparison_expression)

> [!NOTE]
> A teljes EBNF [Azure Search a OData kifejezés szintaxisát](search-query-odata-syntax-reference.md) ismertető témakörben talál.

Az összehasonlító kifejezések két formája létezik. Az egyetlen különbség, hogy az állandó megjelenik-e az operátor bal vagy jobb oldali részén. Az operátor másik oldalán lévő kifejezésnek **változónak** vagy függvény hívásnak kell lennie. A változó lehet egy mezőnév vagy egy tartomány változó egy [lambda kifejezés](search-query-odata-collection-operators.md)esetén.

## <a name="data-types-for-comparisons"></a>Összehasonlítások adattípusai

Az összehasonlító operátorok mindkét oldalán lévő adattípusoknak kompatibilisnek kell lenniük. Ha például a bal oldali mező típusa `Edm.DateTimeOffset`, akkor a jobb oldalon dátum-idő állandónak kell lennie. A numerikus adattípusok rugalmasabbak. Bármilyen numerikus típus változóit és függvényeit összehasonlíthatja bármilyen más numerikus típussal, néhány korlátozással, az alábbi táblázatban leírtak szerint.

| Változó vagy függvény típusa | Konstans értéktípus | Korlátozások |
| --- | --- | --- |
| `Edm.Double` | `Edm.Double` | Az összehasonlításra a [következő speciális szabályok `NaN` vonatkoznak:](#special-case-nan) |
| `Edm.Double` | `Edm.Int64` | A konstans a értékre lett konvertálva `Edm.Double`, ami a nagy mennyiségű értékek pontosságának elvesztését eredményezi |
| `Edm.Double` | `Edm.Int32` | n/a |
| `Edm.Int64` | `Edm.Double` | A `NaN`, `-INF`, vagy`INF` nem engedélyezett összehasonlítások |
| `Edm.Int64` | `Edm.Int64` | n/a |
| `Edm.Int64` | `Edm.Int32` | A konstans konvertálása az `Edm.Int64` összehasonlítás előtt történik |
| `Edm.Int32` | `Edm.Double` | A `NaN`, `-INF`, vagy`INF` nem engedélyezett összehasonlítások |
| `Edm.Int32` | `Edm.Int64` | n/a |
| `Edm.Int32` | `Edm.Int32` | n/a |

A nem engedélyezett összehasonlításokhoz, például a típusú `Edm.Int64` mezők összehasonlításához a Azure Search REST API egy "http 400- `NaN`as értéket ad vissza: Hibás kérelem hiba.

> [!IMPORTANT]
> Bár a numerikus típusok összehasonlítása rugalmas, javasoljuk, hogy az összehasonlításokat a szűrőkben is érdemes megírni, hogy az állandó érték ugyanolyan adattípusú legyen, mint a változó vagy a függvény, amelyhez a rendszer hasonlít. Ez különösen akkor fontos, ha a lebegőpontos és az egész értékeket keverik, ahol a pontosságot elveszítő implicit konverziók lehetségesek.

<a name="special-case-nan"></a>

### <a name="special-cases-for-null-and-nan"></a>A és a `null` speciális esetei`NaN`

Az összehasonlító operátorok használatakor fontos megjegyezni, hogy a Azure Searchban lévő összes nem gyűjteményes mező lehetséges lehet `null`. A következő táblázat az összehasonlítási kifejezés összes lehetséges eredményét tartalmazza, ahol mindkét oldal `null`lehet:

| Operator | Eredmény, ha csak a mező vagy a változó szerepel`null` | Eredmény, ha csak az állandó érték van`null` | Eredmény, ha a mező vagy a változó és az állandó is`null` |
| --- | --- | --- | --- |
| `gt` | `false` | HTTP 400: Hibás kérelem hiba | HTTP 400: Hibás kérelem hiba |
| `lt` | `false` | HTTP 400: Hibás kérelem hiba | HTTP 400: Hibás kérelem hiba |
| `ge` | `false` | HTTP 400: Hibás kérelem hiba | HTTP 400: Hibás kérelem hiba |
| `le` | `false` | HTTP 400: Hibás kérelem hiba | HTTP 400: Hibás kérelem hiba |
| `eq` | `false` | `false` | `true` |
| `ne` | `true` | `true` | `false` |

Összefoglalva `null` , csak önmagával egyenlő, és nem lehet kisebb vagy nagyobb, mint bármely más érték.

Ha az indexnek vannak típusú `Edm.Double` mezői, és értékeket tölt fel `NaN` ezekre a mezőkre, akkor a szűrők írásakor el kell végeznie a fiók használatát. Azure Search megvalósítja az IEEE 754 szabványt az `NaN` értékek kezelésére, és az ilyen értékekkel való összehasonlítás nem nyilvánvaló eredményeket eredményez, ahogy az alábbi táblázatban is látható.

| Operator | Eredmény, ha legalább egy operandus`NaN` |
| --- | --- |
| `gt` | `false` |
| `lt` | `false` |
| `ge` | `false` |
| `le` | `false` |
| `eq` | `false` |
| `ne` | `true` |

Összefoglalva `NaN` , nem egyenlő egyetlen értékkel sem, beleértve a saját magát is.

### <a name="comparing-geo-spatial-data"></a>Geo-térbeli adatainak összehasonlítása

Egy típusú `Edm.GeographyPoint` mező nem hasonlítható össze állandó értékkel, de használhatja a `geo.distance` függvényt is. Ez a függvény egy típusú `Edm.Double`értéket ad vissza, így összehasonlíthatja azt egy numerikus konstanssal, hogy az állandó geo-térbeli koordináták távolsága alapján szűrje azt. Tekintse [](#examples) meg az alábbi példákat.

### <a name="comparing-string-data"></a>Karakterlánc-adatértékek összehasonlítása

A karakterláncok összehasonlítható a szűrőkben a és `eq` `ne` a operátorok pontos egyezéséhez. Ezek az összehasonlítások megkülönböztetik a kis-és nagybetűket.

## <a name="examples"></a>Példák

Olyan dokumentumok egyeztetése `Rating` , amelyekben a mező 3 és 5 közötti, beleértve a következőket:

    Rating ge 3 and Rating le 5

Olyan dokumentumok egyeztetése `Location` , amelyekben a mező kevesebb, mint 2 kilométer a megadott szélességi és hosszúsági fok:

    geo.distance(Location, geography'POINT(-122.031577 47.578581)') lt 2.0

Olyan dokumentumok egyeztetése `LastRenovationDate` , amelyekben a mező értéke nagyobb vagy egyenlő, mint 2015, éjfélkor UTC:

    LastRenovationDate ge 2015-01-01T00:00:00.000Z

Olyan dokumentumok egyeztetése `Details/Sku` , amelyek nem `null`a mező:

    Details/Sku ne null

A dokumentumok egyeztetése olyan hoteleknél, amelyekben legalább egy szoba "Deluxe szoba" típusú, ahol a `Rooms/Type` mező sztringje pontosan megfelel a szűrőnek:

    Rooms/any(room: room/Type eq 'Deluxe Room')

## <a name="next-steps"></a>További lépések  

- [Szűrők a Azure Searchban](search-filters.md)
- [A OData kifejezés nyelvének áttekintése Azure Search](query-odata-filter-orderby-syntax.md)
- [Azure Search OData-kifejezés szintaxisának referenciája](search-query-odata-syntax-reference.md)
- [Dokumentumok &#40;keresése Azure Search szolgáltatás REST API&#41;](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)
