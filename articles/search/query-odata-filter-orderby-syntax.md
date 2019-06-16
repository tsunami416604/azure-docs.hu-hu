---
title: OData nyelvi áttekintése – Azure Search
description: OData nyelvi áttekintése szűrők, válassza ki, és rendezés az Azure Search-lekérdezéseket.
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
ms.openlocfilehash: 166c23088fe0388199ca51efde05153bb5697e38
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "67063703"
---
# <a name="odata-language-overview-for-filter-orderby-and-select-in-azure-search"></a>OData-nyelv áttekintés `$filter`, `$orderby`, és `$select` az Azure Search szolgáltatásban

Az Azure Search az OData-kifejezések szintaxisa a egy részét támogatja **$filter**, **$orderby**, és **$select** kifejezéseket. Szűrési kifejezésekben értékeli ki a lekérdezés-elemzés, search az egyes mezők neve vagy indexe vizsgálatok során használt egyezési feltételek hozzáadása során. Order by kifejezésnek alkalmazza a rendszer egy utólagos feldolgozási lépést az eredményhalmaz rendezze a visszaadott dokumentumok keresztül. Válassza kifejezések határozza meg, mely a dokumentum mezőket az eredményhalmaz tartalmazza. Ezek a kifejezések szintaxisát különbözik a [egyszerű](query-simple-syntax.md) vagy [teljes](query-lucene-syntax.md) lekérdezési szintaxist használ a **keresési** paraméter, de nincs átfedés szintaxisának mezők hivatkozik.

Ez a cikk áttekintést OData kifejezés nyelve szűrőket, az order by és select kifejezéseket. A nyelv "alulról felfelé", kezdve a legalapvetőbb elemeket, és gyakorta jelennek meg. A legfelső szintű minden paraméter szintaxisa egy külön cikkben ismertetett:

- [$filter szintaxis](search-query-odata-filter.md)
- [$orderby szintaxis](search-query-odata-orderby.md)
- [$select szintaxis](search-query-odata-select.md)

OData kifejezések köre a egyszerű nagy mértékben összetett, megtekinthetik azonban az összes megosztása a szokványos elemeket. Az Azure Search egy OData-kifejezésnek legalapvetőbb részei a következők:

- **Mező elérési utak**, ami az index meghatározott mezőkre hivatkozhatnak.
- **Állandók**, amely egy bizonyos adatok típusú konstans értékek.

> [!NOTE]
> Különbözik terminológiai az Azure Search szolgáltatásban a [OData-szabványt](https://www.odata.org/documentation/) több módon is. Hívjuk a **mező** neve az Azure Search egy **tulajdonság** OData, és ehhez hasonlóan a **mező elérési útja** és **útvonal**. Egy **index** tartalmazó **dokumentumok** az Azure Search hivatkozik, amely általában a, az OData- **entitáskészlet** tartalmazó **entitások**. Az Azure Search-terminológia használja ezt a hivatkozást.

## <a name="field-paths"></a>A mező elérési utak

A következő EBNF ([kiterjesztett Backus-Naur űrlap](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form)) határozza meg a nyelvtani, a mező elérési utak.

<!-- Upload this EBNF using https://bottlecaps.de/rr/ui to create a downloadable railroad diagram. -->

```
field_path ::= identifier('/'identifier)*

identifier ::= [a-zA-Z_][a-zA-Z_0-9]*
```

Egy interaktív szintaxisdiagramja is érhető el:

> [!div class="nextstepaction"]
> [Azure Search OData szintaxisdiagramja](https://azuresearch.github.io/odata-syntax-diagram/#field_path)

> [!NOTE]
> Lásd: [OData kifejezés szintaxisának referenciája az Azure Search](search-query-odata-syntax-reference.md) a teljes EBNF számára.

A mező elérési útja épül fel egy vagy több **azonosítók** perjellel elválasztva. Minden azonosító, amely karaktersorozatot kell egy ASCII betűvel vagy aláhúzásjellel kezdődő, és csak ASCII betűket, számokat és aláhúzásjeleket tartalmazhat. A betűk a felső - és kisbetűs is lehetnek.

Identifikátor. olvassa el, vagy a nevével, egy mezőt, és a egy **tartomány változót** kontextusában egy [gyűjtemény kifejezés](search-query-odata-collection-operators.md) (`any` vagy `all`) egy szűrő. A tartomány változót olyan, mintha egy változó, amely az aktuális elem a gyűjtemény jelöli. Összetett gyűjtemények esetében a változó jelöli a objektumot, ezért a mező elérési utak segítségével a változó alárendelt mezőkre hivatkozhatnak. Ez a hasonló számos programozási nyelvet a felépítését.

Példák a mező elérési utak az alábbi táblázatban láthatók:

| Mező elérési útja | Leírás |
| --- | --- |
| `HotelName` | Az index egy legfelső szintű mezőre hivatkozik |
| `Address/City` | Hivatkozik az `City` az optimálisnál mezőt az indexben; egy összetett mező `Address` típusú `Edm.ComplexType` ebben a példában |
| `Rooms/Type` | Hivatkozik az `Type` az optimálisnál mezőt az indexben; egy összetett gyűjtemény mező `Rooms` típusú `Collection(Edm.ComplexType)` ebben a példában |
| `Stores/Address/Country` | Hivatkozik az `Country` az optimálisnál mezőjében a `Address` az optimálisnál mezőt az indexben; egy összetett gyűjtemény mező `Stores` típusú `Collection(Edm.ComplexType)` és `Address` típusú `Edm.ComplexType` ebben a példában |
| `room/Type` | Hivatkozik az `Type` az optimálisnál mezőjében a `room` tartomány változót, például a szűrési kifejezés `Rooms/any(room: room/Type eq 'deluxe')` |
| `store/Address/Country` | Hivatkozik az `Country` az optimálisnál mezőjében a `Address` az optimálisnál mezőjében a `store` tartomány változót, például a szűrőkifejezésben szereplő `Stores/any(store: store/Address/Country eq 'Canada')` |

A mező elérési útja értelmében a környezettől függően eltérő. A szűrők, a mező elérési útja, értékére hivatkozik egy *egypéldányos* az aktuális dokumentumon belüli egy mező. Más környezetekben például **$orderby**, **$select**, vagy a [teljes Lucene szintaxisú fielded keresési](query-lucene-syntax.md#bkmk_fields), a mező elérési útja maga mezőre hivatkozik. Ez a különbség a mező elérési utak használatát szűrők néhány következményekkel jár.

Fontolja meg a mező elérési útja `Address/City`. A szűrőt Ez vonatkozik az aktuális dokumentumon, például a "San Francisco" egységes városát. Ezzel szemben a `Rooms/Type` hivatkozik a `Type` az optimálisnál mezőjét számos termek (például a "standard" az első hely, a "deluxe", a második hely, és így tovább). Mivel `Rooms/Type` nem hivatkozik egy *egypéldányos* alárendelt mező `Type`, közvetlenül a szűrő nem használható. Ehelyett szoba typu szűréséhez használja a [lambda kifejezésnek](search-query-odata-collection-operators.md) tartomány változó ehhez hasonló:

    Rooms/any(room: room/Type eq 'deluxe')

Ebben a példában a tartomány változót `room` jelenik meg a `room/Type` mező elérési útja. Ezzel a módszerrel `room/Type` az aktuális dokumentumon belüli aktuális észlelnek a helyiségben típusára utal. Ez az egyetlen példánya a `Type` alárendelt mezőt, így közvetlenül a szűrőben is használható.

### <a name="using-field-paths"></a>A mező elérési utak használata

A mező elérési utak, sok paraméter szerepel a [Azure Search API](https://docs.microsoft.com/rest/api/searchservice/). A következő táblázat felsorolja minden olyan helyen, ahol is használhatók, valamint a használatuk korlátozások:

| API | Paraméter neve | Korlátozások |
| --- | --- | --- |
| [Hozzon létre](https://docs.microsoft.com/rest/api/searchservice/create-index) vagy [frissítés](https://docs.microsoft.com/rest/api/searchservice/update-index) Index | `suggesters/sourceFields` | None |
| [Hozzon létre](https://docs.microsoft.com/rest/api/searchservice/create-index) vagy [frissítés](https://docs.microsoft.com/rest/api/searchservice/update-index) Index | `scoringProfiles/text/weights` | Csak **kereshető** mezők |
| [Hozzon létre](https://docs.microsoft.com/rest/api/searchservice/create-index) vagy [frissítés](https://docs.microsoft.com/rest/api/searchservice/update-index) Index | `scoringProfiles/functions/fieldName` | Csak **szűrhető** mezők |
| [Search](https://docs.microsoft.com/rest/api/searchservice/search-documents) | `search` Amikor `queryType` van `full` | Csak **kereshető** mezők |
| [Search](https://docs.microsoft.com/rest/api/searchservice/search-documents) | `facet` | Csak **kategorizálható** mezők |
| [Search](https://docs.microsoft.com/rest/api/searchservice/search-documents) | `highlight` | Csak **kereshető** mezők |
| [Search](https://docs.microsoft.com/rest/api/searchservice/search-documents) | `searchFields` | Csak **kereshető** mezők |
| [Javasoljon](https://docs.microsoft.com/rest/api/searchservice/suggestions) és [automatikus kiegészítés](https://docs.microsoft.com/rest/api/searchservice/autocomplete) | `searchFields` | Csak részét képező mezőkre hivatkozhatnak egy [javaslattevő](index-add-suggesters.md) |
| [Keresés](https://docs.microsoft.com/rest/api/searchservice/search-documents), [javaslat](https://docs.microsoft.com/rest/api/searchservice/suggestions), és [automatikus kiegészítés](https://docs.microsoft.com/rest/api/searchservice/autocomplete) | `$filter` | Csak **szűrhető** mezők |
| [Keresés](https://docs.microsoft.com/rest/api/searchservice/search-documents) és [javaslat](https://docs.microsoft.com/rest/api/searchservice/suggestions) | `$orderby` | Csak **rendezhető** mezők |
| [Keresés](https://docs.microsoft.com/rest/api/searchservice/search-documents), [javaslat](https://docs.microsoft.com/rest/api/searchservice/suggestions), és [keresése](https://docs.microsoft.com/rest/api/searchservice/lookup-document) | `$select` | Csak **lekérhető** mezők |

## <a name="constants"></a>Állandók

OData állandót a konstans értékek egy adott [Entity Data Model](https://docs.microsoft.com/dotnet/framework/data/adonet/entity-data-model) (EDM) típus. Lásd: [által támogatott adattípusokkal](https://docs.microsoft.com/rest/api/searchservice/supported-data-types) az Azure Search szolgáltatásban a támogatott típusok listáját. Állandók gyűjtemény típusok nem támogatottak.

Az alábbi táblázat állandókat az Azure Search által támogatott adattípusokat mindegyike esetében:

| Adattípus | Példa állandók |
| --- | --- |
| `Edm.Boolean` | `true`, `false` |
| `Edm.DateTimeOffset` | `2019-05-06T12:30:05.451Z` |
| `Edm.Double` | `3.14159`, `-1.2e7`, `NaN`, `INF`, `-INF` |
| `Edm.GeographyPoint` | `geography'POINT(-122.131577 47.678581)'` |
| `Edm.GeographyPolygon` | `geography'POLYGON((-122.031577 47.578581, -122.031577 47.678581, -122.131577 47.678581, -122.031577 47.578581))'` |
| `Edm.Int32` | `123`, `-456` |
| `Edm.Int64` | `283032927235` |
| `Edm.String` | `'hello'` |

A következő EBNF ([kiterjesztett Backus-Naur űrlap](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form)) határozza meg a szintaxis a fenti táblázatban szereplő állandókat többsége esetében. A térinformatikai típusok szintaxis található [OData térinformatikai funkciók az Azure Search](search-query-odata-geo-spatial-functions.md).

<!-- Upload this EBNF using https://bottlecaps.de/rr/ui to create a downloadable railroad diagram. -->

```
constant ::=
    string_literal
    | date_time_offset_literal
    | integer_literal
    | float_literal
    | boolean_literal
    | 'null'

string_literal ::= "'"([^'] | "''")*"'"

date_time_offset_literal ::= date_part'T'time_part time_zone

date_part ::= year'-'month'-'day

time_part ::= hour':'minute(':'second('.'fractional_seconds)?)?

zero_to_fifty_nine ::= [0-5]digit

digit ::= [0-9]

year ::= digit digit digit digit

month ::= '0'[1-9] | '1'[0-2]

day ::= '0'[1-9] | [1-2]digit | '3'[0-1]

hour ::= [0-1]digit | '2'[0-3]

minute ::= zero_to_fifty_nine

second ::= zero_to_fifty_nine

fractional_seconds ::= integer_literal

time_zone ::= 'Z' | sign hour':'minute

sign ::= '+' | '-'

/* In practice integer literals are limited in length to the precision of
the corresponding EDM data type. */
integer_literal ::= digit+

float_literal ::=
    sign? whole_part fractional_part? exponent?
    | 'NaN'
    | '-INF'
    | 'INF'

whole_part ::= integer_literal

fractional_part ::= '.'integer_literal

exponent ::= 'e' sign? integer_literal

boolean_literal ::= 'true' | 'false'
```

Egy interaktív szintaxisdiagramja is érhető el:

> [!div class="nextstepaction"]
> [Azure Search OData szintaxisdiagramja](https://azuresearch.github.io/odata-syntax-diagram/#constant)

> [!NOTE]
> Lásd: [OData kifejezés szintaxisának referenciája az Azure Search](search-query-odata-syntax-reference.md) a teljes EBNF számára.

## <a name="building-expressions-from-field-paths-and-constants"></a>A mező elérési útja és konstansok épület kifejezések

Mező elérési útja és konstansok részei a legalapvetőbb egy OData-kifejezésnek, de már teljes kifejezések magukat. Valójában a **$select** az Azure Search paraméter nem semmi, de a mező elérési utak, vesszővel elválasztott listáját és **$orderby** nem sokkal bonyolultabb, mint **$select**. Ha rendelkezik típusú mező `Edm.Boolean` az indexben, még akkor is írhat egy szűrőt, amely nem, de az a mező elérési útja. Az állandókat `true` és `false` hasonlóképpen érvényes szűrők vannak.

Azonban a legtöbbször kell több összetett kifejezések, amelyek egynél több mező és állandó hivatkoznak. Ezek a kifejezések paraméter függően különböző módon épülnek.

A következő EBNF ([kiterjesztett Backus-Naur űrlap](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form)) határozza meg a nyelvtani a **$filter**, **$orderby**, és **$select** paramétereket. Ezek épülnek fel, tekintse meg a mező elérési útja és konstansok egyszerűbb kifejezések:

<!-- Upload this EBNF using https://bottlecaps.de/rr/ui to create a downloadable railroad diagram. -->

```
filter_expression ::= boolean_expression

order_by_expression ::= order_by_clause(',' order_by_clause)*

select_expression ::= '*' | field_path(',' field_path)*
```

Egy interaktív szintaxisdiagramja is érhető el:

> [!div class="nextstepaction"]
> [Azure Search OData szintaxisdiagramja](https://azuresearch.github.io/odata-syntax-diagram/#filter_expression)

> [!NOTE]
> Lásd: [OData kifejezés szintaxisának referenciája az Azure Search](search-query-odata-syntax-reference.md) a teljes EBNF számára.

A **$orderby** és **$select** paraméterek a következők mindkét egyszerűbb kifejezések vesszővel tagolt listája. A **$filter** paramétere egy logikai kifejezés, amelyet az egyszerűbb az alkifejezések. Ezeket az alkifejezések kombinálják, mint például a logikai operátorral egymáshoz csatolt [ `and`, `or`, és `not` ](search-query-odata-logical-operators.md), mint például a összehasonlító operátorok [ `eq`, `lt`, `gt`, és így tovább](search-query-odata-comparison-operators.md), és a gyűjtemény operátorok például [ `any` és `all` ](search-query-odata-collection-operators.md).

A **$filter**, **$orderby**, és **$select** paraméterek teljesítménykezelési részletesebben az alábbi cikkeket:

- [Az Azure Search OData $filter-szintaxis](search-query-odata-filter.md)
- [Az Azure Search OData $orderby-szintaxis](search-query-odata-orderby.md)
- [Az Azure Search OData $select-szintaxis](search-query-odata-select.md)

## <a name="see-also"></a>Lásd még  

- [Jellemzőalapú navigáció az Azure Search szolgáltatásban](search-faceted-navigation.md)
- [Szűrők az Azure Search szolgáltatásban](search-filters.md)
- [Dokumentumok keresése &#40;az Azure Search szolgáltatás REST API-ja&#41;](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)
- [Lucene lekérdezési szintaxis](query-lucene-syntax.md)
- [Az Azure Search egyszerű lekérdezési szintaxis](query-simple-syntax.md)
