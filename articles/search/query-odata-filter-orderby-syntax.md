---
title: A OData nyelvének áttekintése – Azure Search
description: A OData nyelvének áttekintése a szűrők, a kiválasztás és a sorrend alapján Azure Search lekérdezésekhez.
ms.date: 06/13/2019
services: search
ms.service: search
ms.topic: conceptual
author: Brjohnstmsft
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
ms.openlocfilehash: 0bd446b0ffa97a758f68a0f85889b13da6e3d8b0
ms.sourcegitcommit: bb8e9f22db4b6f848c7db0ebdfc10e547779cccc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/20/2019
ms.locfileid: "69650034"
---
# <a name="odata-language-overview-for-filter-orderby-and-select-in-azure-search"></a>A `$filter`, `$orderby`a és`$select` a OData nyelvének áttekintése Azure Search

Azure Search támogatja a OData Expression szintaxisának egy részhalmazát **$Filter**, **$OrderBy**és **$Select** kifejezésekhez. A szűrési kifejezéseket a rendszer kiértékeli a lekérdezések elemzése során, megtiltja a keresést adott mezőkre, vagy az index vizsgálata során használt egyezési feltételeket ad hozzá. A sorrend szerinti kifejezéseket a rendszer utólagos feldolgozás utáni lépésként alkalmazza a visszaadott dokumentumok rendezéséhez. Válassza ki azokat a kifejezéseket, amelyek meghatározzák, hogy mely dokumentum mezők szerepeljenek az eredményhalmazban. Ezeknek a kifejezéseknek a szintaxisa különbözik a **keresési** paraméterben használt [egyszerű](query-simple-syntax.md) vagy [teljes](query-lucene-syntax.md) lekérdezési szintaxistól, bár átfedésben van a mezők hivatkozó szintaxisa.

Ez a cikk áttekintést nyújt a Filters, Order by és Select kifejezésekben használt OData kifejezés nyelvéről. A nyelv "bottom-up" (alapszintű), a legalapvetőbb elemektől kezdve, és azokra épül. Az egyes paraméterek legfelső szintű szintaxisát külön cikk ismerteti:

- [$filter szintaxis](search-query-odata-filter.md)
- [$orderby szintaxis](search-query-odata-orderby.md)
- [$select szintaxis](search-query-odata-select.md)

A OData-kifejezések egyszerűen és nagyon összetettek, de minden közös elemet megosztanak. A Azure Search OData kifejezésének legalapvetőbb részei a következők:

- A **mező elérési útja**, amely az index adott mezőire hivatkozik.
- **Konstansok**, amelyek egy bizonyos adattípushoz tartozó literál értékeket tartalmaznak.

> [!NOTE]
> A Azure Search terminológiája néhány módon különbözik a [OData standardtól](https://www.odata.org/documentation/) . Az Azure Search egyik **mezőjét** nevezzük a OData tulajdonságának , és hasonlóképpen a **mező elérési** útja és a **tulajdonság elérési útja**között. A Azure Searchban lévő **dokumentumokat** tartalmazó **indexet** általában a OData-ben **entitásokat**tartalmazó **entitásként** említik. Ez a hivatkozás a Azure Search terminológiát használja.

## <a name="field-paths"></a>Mező elérési útjai

A következő EBNF ([bővített Naur-űrlap](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form)) a mezők elérési útjának nyelvtanát határozzák meg.

<!-- Upload this EBNF using https://bottlecaps.de/rr/ui to create a downloadable railroad diagram. -->

```
field_path ::= identifier('/'identifier)*

identifier ::= [a-zA-Z_][a-zA-Z_0-9]*
```

Az interaktív szintaxis diagram is elérhető:

> [!div class="nextstepaction"]
> [Azure Search OData szintaxisának diagramja](https://azuresearch.github.io/odata-syntax-diagram/#field_path)

> [!NOTE]
> A teljes EBNF [Azure Search a OData kifejezés szintaxisát](search-query-odata-syntax-reference.md) ismertető témakörben talál.

A mező elérési útja egy vagy több, ferde vonallal elválasztott azonosítóból áll. Minden azonosító egy olyan karaktersorozat, amelynek egy ASCII betűvel vagy aláhúzással kell kezdődnie, és csak ASCII betűt, számjegyet vagy aláhúzást tartalmazhat. A betűk lehetnek a felső vagy a kisbetűs karakterek.

Az azonosítók egy adott mező nevére vagy egy, a szűrőben lévő [gyűjtemény kifejezés](search-query-odata-collection-operators.md) (`any` vagy `all`) kontextusában lévő Range **változóra** is hivatkozhatnak. A tartomány változó a gyűjtemény aktuális elemét jelképező hurok-változóhoz hasonlít. Összetett gyűjtemények esetén ez a változó egy objektumot jelöl, ezért a mező elérési útjaival a változó almezőire hivatkozhat. Ez hasonló a dot-jelölésekhez számos programozási nyelven.

A következő táblázat példákat mutat be a mezők elérési útjaira:

| Mező elérési útja | Leírás |
| --- | --- |
| `HotelName` | Az index legfelső szintű mezőjére hivatkozik. |
| `Address/City` | `City` Az index összetett mezőjének almezőjét jelöli. Ebben a példában `Edm.ComplexType` szereplő típusú. `Address` |
| `Rooms/Type` | `Type` Az index összetett gyűjtemény mezőjének almezőjét jelöli. Ebben a példában `Collection(Edm.ComplexType)` szereplő típusú. `Rooms` |
| `Stores/Address/Country` | `Country` Azindexösszetettgyűjteménymezőjénekalmezőjét`Address` jelöli. `Stores` típusa `Collection(Edm.ComplexType)` és típusa`Address`ebbenapéldában `Edm.ComplexType` |
| `room/Type` | `Type` A `room` tartomány változójának almezőjére hivatkozik, például a Filter kifejezésben.`Rooms/any(room: room/Type eq 'deluxe')` |
| `store/Address/Country` | `Country` `store` A tartomány változó almezőjét jelöli, például a szűrő kifejezésben. `Address``Stores/any(store: store/Address/Country eq 'Canada')` |

A mező elérési útjának jelentése a környezettől függően eltérő. A szűrők területen a mező elérési útja az aktuális dokumentumban lévő mező egy példányának értékére hivatkozik. Más környezetekben, például **$OrderBy**, **$Select**vagy a [teljes Lucene szintaxisban](query-lucene-syntax.md#bkmk_fields)található, mezőn belüli keresésben a mező elérési útja magára a mezőre hivatkozik. Ez a különbség bizonyos következményekkel jár, hogy miként használhatók a mezők elérési útjai a szűrőkben.

Vegye figyelembe a mező `Address/City`elérési útját. Egy szűrőben ez az aktuális dokumentum egyetlen városára vonatkozik, például: "San Francisco". Ezzel szemben `Rooms/Type` a `Type` sok szoba (például a "standard", az első szoba, a "Deluxe" a második Teremnél stb.) almezőjét jelenti. Mivel `Rooms/Type` a nem az almező `Type` *egyetlen példányára* hivatkozik, nem használható közvetlenül szűrőben. Ehelyett a szobatípus szűréséhez egy tartomány-változót használó [lambda kifejezést](search-query-odata-collection-operators.md) kellene használni, például:

    Rooms/any(room: room/Type eq 'deluxe')

Ebben a példában a tartomány változó `room` megjelenik a `room/Type` mező elérési útján. Így az aktuális dokumentumban található aktuális helyiség típusára hivatkozik.`room/Type` Ez az almező egyetlen példánya `Type` , így közvetlenül a szűrőben is használható.

### <a name="using-field-paths"></a>Mezők elérési útjának használata

A mező elérési útjait a [Azure Search API](https://docs.microsoft.com/rest/api/searchservice/)számos paramétere használja. A következő táblázat felsorolja az összes felhasználható helyet, valamint a használattal kapcsolatos korlátozásokat is:

| API | Paraméternév | Korlátozások |
| --- | --- | --- |
| Index [létrehozása](https://docs.microsoft.com/rest/api/searchservice/create-index) vagy [frissítése](https://docs.microsoft.com/rest/api/searchservice/update-index) | `suggesters/sourceFields` | Nincsenek |
| Index [létrehozása](https://docs.microsoft.com/rest/api/searchservice/create-index) vagy [frissítése](https://docs.microsoft.com/rest/api/searchservice/update-index) | `scoringProfiles/text/weights` | Csak **kereshető** mezőkre hivatkozhat |
| Index [létrehozása](https://docs.microsoft.com/rest/api/searchservice/create-index) vagy [frissítése](https://docs.microsoft.com/rest/api/searchservice/update-index) | `scoringProfiles/functions/fieldName` | Csak **szűrhető** mezőkre hivatkozhat |
| [Search](https://docs.microsoft.com/rest/api/searchservice/search-documents) | `search`Ha `queryType` a`full` | Csak **kereshető** mezőkre hivatkozhat |
| [Search](https://docs.microsoft.com/rest/api/searchservice/search-documents) | `facet` | Csak a **sokrétű** mezőkre hivatkozhat |
| [Search](https://docs.microsoft.com/rest/api/searchservice/search-documents) | `highlight` | Csak **kereshető** mezőkre hivatkozhat |
| [Search](https://docs.microsoft.com/rest/api/searchservice/search-documents) | `searchFields` | Csak **kereshető** mezőkre hivatkozhat |
| [Javaslat](https://docs.microsoft.com/rest/api/searchservice/suggestions) és [automatikus kiegészítés](https://docs.microsoft.com/rest/api/searchservice/autocomplete) | `searchFields` | Csak a [javaslat](index-add-suggesters.md) részét képező mezőkre hivatkozhat. |
| [Keresés](https://docs.microsoft.com/rest/api/searchservice/search-documents), [javaslat](https://docs.microsoft.com/rest/api/searchservice/suggestions)és [automatikus kiegészítés](https://docs.microsoft.com/rest/api/searchservice/autocomplete) | `$filter` | Csak **szűrhető** mezőkre hivatkozhat |
| [Keresés](https://docs.microsoft.com/rest/api/searchservice/search-documents) és [javaslat](https://docs.microsoft.com/rest/api/searchservice/suggestions) | `$orderby` | Csak **rendezhető** mezőkre hivatkozhat |
| [Keresés](https://docs.microsoft.com/rest/api/searchservice/search-documents), [javaslat](https://docs.microsoft.com/rest/api/searchservice/suggestions)és [Keresés](https://docs.microsoft.com/rest/api/searchservice/lookup-document) | `$select` | Csak lekérhető mezőkre hivatkozhat |

## <a name="constants"></a>Állandók

A OData konstansai egy adott [entitás adatmodell](https://docs.microsoft.com/dotnet/framework/data/adonet/entity-data-model) -(EDM-) típusának literális értékei. A Azure Search támogatott típusai listáját a [támogatott](https://docs.microsoft.com/rest/api/searchservice/supported-data-types) adattípusok részben tekintheti meg. A gyűjteményi típusok állandói nem támogatottak.

Az alábbi táblázat a Azure Search által támogatott adattípusok állandóit mutatja be:

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

A következő EBNF ([bővített Naur űrlap](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form)) a fenti táblázatban látható állandók többségének nyelvtanát határozza meg. A Geo-térbeli típusok nyelvtana a [Azure Search OData geo-térbeli függvényeknél](search-query-odata-geo-spatial-functions.md)található.

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

Az interaktív szintaxis diagram is elérhető:

> [!div class="nextstepaction"]
> [Azure Search OData szintaxisának diagramja](https://azuresearch.github.io/odata-syntax-diagram/#constant)

> [!NOTE]
> A teljes EBNF [Azure Search a OData kifejezés szintaxisát](search-query-odata-syntax-reference.md) ismertető témakörben talál.

## <a name="building-expressions-from-field-paths-and-constants"></a>Kifejezések kiépítése a mezők elérési útjaiból és konstansokból

A mezők elérési útjai és állandói a OData egyik legalapvetőbb részét képezik, de már maguk a teljes kifejezések. Valójában a Azure Search **$Select** paramétere nem más, mint a mezők elérési útjai vesszővel tagolt listája, és a **$OrderBy** nem sokkal bonyolultabb, mint a **$Select**. Ha úgy látja, hogy van egy típusú `Edm.Boolean` mező az indexben, akkor is írhat egy olyan szűrőt, amely nem más, mint a mező elérési útja. Az állandók `true` és `false` a szűrők is érvényesek.

Azonban a legtöbb esetben összetettebb kifejezésekre lesz szüksége, amelyek egynél több mezőre és állandóra hivatkoznak. Ezek a kifejezések a paramétertől függően különböző módokon vannak felépítve.

A következő EBNF ([bővített Naur űrlap](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form)) a **$Filter**, **$OrderBy**és **$Select** paraméterek nyelvtanát határozza meg. Ezek az egyszerűbb kifejezésekből épülnek fel, amelyek a mező elérési útjaira és állandóra vonatkoznak:

<!-- Upload this EBNF using https://bottlecaps.de/rr/ui to create a downloadable railroad diagram. -->

```
filter_expression ::= boolean_expression

order_by_expression ::= order_by_clause(',' order_by_clause)*

select_expression ::= '*' | field_path(',' field_path)*
```

Az interaktív szintaxis diagram is elérhető:

> [!div class="nextstepaction"]
> [Azure Search OData szintaxisának diagramja](https://azuresearch.github.io/odata-syntax-diagram/#filter_expression)

> [!NOTE]
> A teljes EBNF [Azure Search a OData kifejezés szintaxisát](search-query-odata-syntax-reference.md) ismertető témakörben talál.

A **$OrderBy** és a **$Select** paraméterek az egyszerűbb kifejezések vesszővel tagolt listája. A **$Filter** paraméter egy egyszerű alkifejezésből álló logikai kifejezés. Ezek az alkifejezések olyan logikai operátorokkal [ `and` `or` `not` ](search-query-odata-logical-operators.md)vannak kombinálva, mint a, a, [ `eq`és az `lt`összehasonlító `gt`](search-query-odata-comparison-operators.md)operátorok, például,, stb. operátorok, [ `any` például `all`és ](search-query-odata-collection-operators.md).

A **$Filter**, **$OrderBy**és **$Select** paramétereket részletesebben ismertetjük a következő cikkekben:

- [OData $filter szintaxis Azure Search](search-query-odata-filter.md)
- [OData $orderby szintaxis Azure Search](search-query-odata-orderby.md)
- [OData $select szintaxis Azure Search](search-query-odata-select.md)

## <a name="see-also"></a>Lásd még  

- [Sokoldalú Navigálás Azure Search](search-faceted-navigation.md)
- [Szűrők a Azure Searchban](search-filters.md)
- [Dokumentumok &#40;keresése Azure Search szolgáltatás REST API&#41;](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)
- [Lucene lekérdezési szintaxis](query-lucene-syntax.md)
- [Egyszerű lekérdezési szintaxis a Azure Searchban](query-simple-syntax.md)
