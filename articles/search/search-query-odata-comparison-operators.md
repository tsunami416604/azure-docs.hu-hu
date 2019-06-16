---
title: OData összehasonlító operátor referencia – Azure Search
description: Összehasonlító OData-operátorok, eq, ne, gt, lt, ge és le, az Azure Search-lekérdezéseket.
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
ms.openlocfilehash: b51bf3d77283ae828f47fdb0355d2deb43f071a1
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "67079924"
---
# <a name="odata-comparison-operators-in-azure-search---eq-ne-gt-lt-ge-and-le"></a>Az Azure Search - OData-összehasonlító operátorok `eq`, `ne`, `gt`, `lt`, `ge`, és `le`

A legalapvetőbb műveletet egy [OData szűrési kifejezés](query-odata-filter-orderby-syntax.md) az Azure Search szolgáltatásban egy mezőt egy adott érték összehasonlítására. Kétféle típusú összehasonlító lehetségesek--közötti egyenlőségi összehasonlítás, és a tartomány összehasonlítása. Az alábbi operátorok segítségével egy mezőt egy állandó érték összehasonlítása:

Egyenlőség kezelők:

- `eq`: Tesztelje, hogy az adott mező kitöltése **egyenlő** állandó érték
- `ne`: Tesztelje, hogy az adott mező kitöltése **nem egyenlő** állandó érték

Tartomány-kezelők:

- `gt`: Tesztelje, hogy az adott mező kitöltése **nagyobb, mint** állandó érték
- `lt`: Tesztelje, hogy az adott mező kitöltése **kevesebb mint** állandó érték
- `ge`: Tesztelje, hogy az adott mező kitöltése **nagyobb vagy egyenlő** állandó érték
- `le`: Tesztelje, hogy az adott mező kitöltése **kisebb vagy egyenlő** állandó érték

A tartomány operátorok használhatja együtt a [logikai operátorokat](search-query-odata-logical-operators.md) annak megállapítására, hogy a mező értékeit egy tartományon belül van. Tekintse meg a [példák](#examples) a cikk későbbi részében.

> [!NOTE]
> Igény szerint az operátor és a mező nevének jobb oldalán a bal oldali helyezheti a konstans érték. Az operátorok a tartományt az összehasonlítás szerinti fordított irányú. Például, ha az állandó érték a bal oldali `gt` szeretné tesztelni, e állandó értéke nagyobb, mint a mező. Egy függvény eredménye például összehasonlítását is használhatja a összehasonlító operátorok `geo.distance`, értékkel. A logikai függvények, mint például `search.ismatch`, az eredmény összehasonlítása `true` vagy `false` nem kötelező.

## <a name="syntax"></a>Szintaxis

A következő EBNF ([kiterjesztett Backus-Naur űrlap](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form)) határozza meg a szintaxis egy OData-kifejezés, amely a összehasonlító operátorok használja.

<!-- Upload this EBNF using https://bottlecaps.de/rr/ui to create a downloadable railroad diagram. -->

```
comparison_expression ::=
    variable_or_function comparison_operator constant |
    constant comparison_operator variable_or_function

variable_or_function ::= variable | function_call

comparison_operator ::= 'gt' | 'lt' | 'ge' | 'le' | 'eq' | 'ne'
```

Egy interaktív szintaxisdiagramja is érhető el:

> [!div class="nextstepaction"]
> [Azure Search OData szintaxisdiagramja](https://azuresearch.github.io/odata-syntax-diagram/#comparison_expression)

> [!NOTE]
> Lásd: [OData kifejezés szintaxisának referenciája az Azure Search](search-query-odata-syntax-reference.md) a teljes EBNF számára.

Nincsenek kétféle összehasonlítási kifejezésekben. Ezek között az egyetlen különbség-e a konstans megjelenik-e meg a balra - vagy a jobb oldali üzemeltető. Az üzemeltető a másik oldalon a kifejezésnek kell lennie egy **változó** vagy egy függvény hívásához szükséges. Egy változó lehet, a mező nevét, vagy abban az esetben, a tartomány változót egy [lambda kifejezésnek](search-query-odata-collection-operators.md).

## <a name="data-types-for-comparisons"></a>Adattípusok a összehasonlításához

Az adattípusok mindkét oldalán egy összehasonlító operátor kompatibilisnek kell lenniük. Például, ha a bal oldali típusú mező `Edm.DateTimeOffset`, akkor a jobb oldalon dátum-idő állandónak kell lennie. Numerikus adattípusok rugalmasabbak. Összehasonlíthatja változók és bármely más numerikus típus, néhány korlátozás vonatkozik, és együttműködik minden olyan numerikus típusú az alábbi táblázatban leírtak szerint.

| A változó vagy függvény típusa | Konstans érték típusa | Korlátozások |
| --- | --- | --- |
| `Edm.Double` | `Edm.Double` | Összehasonlítás eredménye státuszban [speciális szabályok `NaN`](#special-case-nan) |
| `Edm.Double` | `Edm.Int64` | Állandó alakítja át `Edm.Double`, ez pedig egy nagy magnitude értékkel pontosság csökkenéséhez |
| `Edm.Double` | `Edm.Int32` | n/a |
| `Edm.Int64` | `Edm.Double` | Összehasonlítások `NaN`, `-INF`, vagy `INF` használata nem engedélyezett |
| `Edm.Int64` | `Edm.Int64` | n/a |
| `Edm.Int64` | `Edm.Int32` | Állandó alakítja át `Edm.Int64` előtt összehasonlítása |
| `Edm.Int32` | `Edm.Double` | Összehasonlítások `NaN`, `-INF`, vagy `INF` használata nem engedélyezett |
| `Edm.Int32` | `Edm.Int64` | n/a |
| `Edm.Int32` | `Edm.Int32` | n/a |

Az összehasonlítás nem engedélyezett, például az típusú mező `Edm.Int64` való `NaN`, adja vissza az Azure Search REST API egy "HTTP 400: Hibás kérés"hiba történt.

> [!IMPORTANT]
> Annak ellenére, hogy numerikus típusú összehasonlítások rugalmas, erősen ajánlott összehasonlítások írása a szűrőket úgy, hogy az állandó érték, a változó vagy függvény, amely azt az összehasonlított ugyanabba az adattípusba tartozik. Ez különösen fontos mikor keverése lebegőpontos és az egész számok, amelyeknél lehetséges, hogy elvesznek a pontosság implicit konverzió.

<a name="special-case-nan"></a>

### <a name="special-cases-for-null-and-nan"></a>Speciális esetekben a `null` és `NaN`

Összehasonlítási operátor használata esetén fontos megjegyezni, hogy az Azure Search szolgáltatásban az összes nem gyűjtemény mező lehet `null`. Az alábbi táblázat tartalmazza az összes lehetséges kimenetek egy összehasonlítási kifejezésben: ahol sem lehet `null`:

| Művelet | Ha csak a mezőben vagy a változó eredménye `null` | Ha csak a állandó eredménye `null` | Ha a mezőben vagy a változót és az állandó eredménye `null` |
| --- | --- | --- | --- |
| `gt` | `false` | HTTP 400: Hibás kérelem hiba | HTTP 400: Hibás kérelem hiba |
| `lt` | `false` | HTTP 400: Hibás kérelem hiba | HTTP 400: Hibás kérelem hiba |
| `ge` | `false` | HTTP 400: Hibás kérelem hiba | HTTP 400: Hibás kérelem hiba |
| `le` | `false` | HTTP 400: Hibás kérelem hiba | HTTP 400: Hibás kérelem hiba |
| `eq` | `false` | `false` | `true` |
| `ne` | `true` | `true` | `false` |

Összefoglalva `null` nem csak magát, és nem kisebb vagy nagyobb, mint bármely más érték.

Ha az index típusú mezők `Edm.Double` és feltöltött `NaN` értékeket ezekhez a mezőkhöz, szüksége lesz az adott fiók szűrők írásakor. Az Azure Search valósítja meg az IEEE 754 standard kezelésére vonatkozó `NaN` értékeket, és ilyen értékkel rendelkező összehasonlítást eredményeket nem nyilvánvaló, az alábbi táblázatban látható módon.

| Művelet | Ha legalább egy operandusa eredménye `NaN` |
| --- | --- |
| `gt` | `false` |
| `lt` | `false` |
| `ge` | `false` |
| `le` | `false` |
| `eq` | `false` |
| `ne` | `true` |

Összefoglalva `NaN` nem egyenlő értéket, beleértve magát.

### <a name="comparing-geo-spatial-data"></a>Térinformatikai adatok összehasonlítása

Nem lehet közvetlenül két típusú mező `Edm.GeographyPoint` egy állandó értékkel is használhatja, de a `geo.distance` függvény. Ez a függvény típusú értéket ad vissza `Edm.Double`, így összehasonlíthatja numerikus állandó szűrése alapján állandó földrajzi koordináták közötti távolságot. Tekintse meg a [példák](#examples) alatt.

### <a name="comparing-string-data"></a>Karakterláncadatok összehasonlítása

Karakterláncok szűrők használatával pontos egyezések összehasonlíthatók az `eq` és `ne` operátorok. Ezek az összehasonlítások-és nagybetűk.

## <a name="examples"></a>Példák

Egyezés dokumentálja a helyét a `Rating` mező hossza 3 és 5 között lehet között:

    Rating ge 3 and Rating le 5

Egyezés dokumentálja a helyét a `Location` mező értéke a megadott hosszúsági és szélességi foktól a kevesebb mint 2 alapján:

    geo.distance(Location, geography'POINT(-122.031577 47.578581)') lt 2.0

Egyezés dokumentálja a helyét a `LastRenovationDate` mező értéke nagyobb, mint vagy egyenlő 1-től, 2015 január, éjfélkor (UTC):

    LastRenovationDate ge 2015-01-01T00:00:00.000Z

Egyezés dokumentálja a helyét a `Details/Sku` Pole není `null`:

    Details/Sku ne null

A "Hotels", ahol a legalább egy hely rendelkezik típusa "Deluxe Room", ahol a dokumentumokat a karakterláncot, a `Rooms/Type` mezője pontosan megegyezik a szűrő:

    Rooms/any(room: room/Type eq 'Deluxe Room')

## <a name="next-steps"></a>További lépések  

- [Szűrők az Azure Search szolgáltatásban](search-filters.md)
- [Az Azure Search OData kifejezés nyelvi – áttekintés](query-odata-filter-orderby-syntax.md)
- [Az Azure Search OData kifejezés szintaxisának referenciája](search-query-odata-syntax-reference.md)
- [Dokumentumok keresése &#40;az Azure Search szolgáltatás REST API-ja&#41;](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)
