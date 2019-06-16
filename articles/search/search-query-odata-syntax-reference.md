---
title: OData-kifejezés szintaxisának referenciája – Azure Search
description: Formális szintaxis és a szintaxis leírás OData-kifejezések az Azure Search-lekérdezéseket.
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
ms.openlocfilehash: cccfb749af07d1deeeda6e94de9c2cd5ce5396f3
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "67079664"
---
# <a name="odata-expression-syntax-reference-for-azure-search"></a>Az Azure Search OData kifejezés szintaxisának referenciája

Használja az Azure Search [OData-kifejezések](http://docs.oasis-open.org/odata/odata/v4.01/odata-v4.01-part2-url-conventions.html) paraméterként egész az API-t. Leggyakrabban, az OData-kifejezések használhatók a `$orderby` és `$filter` paramétereket. Ezek a kifejezések több szolgáló szerződéses klauzulák, függvényeket és operátorokat tartalmazó összetett lehet. Azonban, még akkor is egyszerű OData-kifejezések például elérési út szerepel az Azure Search REST API számos részein tulajdonság. Például elérésiút-kifejezésekben használhatók összetett mezők mindenhol az API-t, például amikor listaelem az optimálisnál mezők az alárendelt mezőkre hivatkozhatnak egy [javaslattevő](index-add-suggesters.md), amely egy [pontozó függvény](index-add-scoring-profiles.md), a `$select` paraméter , vagy akár [fielded keresés a Lucene-lekérdezésekre](query-lucene-syntax.md).

Ez a cikk ismerteti egy hivatalos szintaxis használatával az OData-kifejezések minden űrlapok. Emellett van egy [interaktív diagram](#syntax-diagram) vizuálisan Fedezze fel a szintaxis segítségével.

## <a name="formal-grammar"></a>Formális szintaxis

Az Azure Search használatával egy EBNF által támogatott OData nyelv részhalmazát is ismertetünk ([kiterjesztett Backus-Naur űrlap](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form)) szintaxis. Szabály szerepel a listában "fentről lefelé", kezdve a legösszetettebb kifejezések és bontásához őket, több primitív kifejezésekre. Az oldal tetején a nyelvtani szabályokat, amelyek megfelelnek az Azure Search REST API-specifikus paramétereket a következők:

- [`$filter`](search-query-odata-filter.md), határozzák meg a `filter_expression` szabály.
- [`$orderby`](search-query-odata-orderby.md), határozzák meg a `order_by_expression` szabály.
- [`$select`](search-query-odata-select.md), határozzák meg a `select_expression` szabály.
- Az elérési utak által meghatározott mezőben a `field_path` szabály. A mező elérési út szerepel az API teljes. Az index vagy legfelső szintű mezők vagy az alárendelt mezők közül egy vagy több is hivatkozhatnak [összetett mező](search-howto-complex-data-types.md) elődei.

Miután a EBNF egy tallózható [szintaxisdiagramja](https://en.wikipedia.org/wiki/Syntax_diagram) , amely lehetővé teszi, hogy meg a szintaxis és a kapcsolatokat a szabályok között.

<!-- Upload this EBNF using https://bottlecaps.de/rr/ui to create a downloadable railroad diagram. -->

```
/* Top-level rules */

filter_expression ::= boolean_expression

order_by_expression ::= order_by_clause(',' order_by_clause)*

select_expression ::= '*' | field_path(',' field_path)*

field_path ::= identifier('/'identifier)*


/* Shared base rules */

identifier ::= [a-zA-Z_][a-zA-Z_0-9]*


/* Rules for $orderby */

order_by_clause ::= (field_path | sortable_function) ('asc' | 'desc')?

sortable_function ::= geo_distance_call | 'search.score()'


/* Rules for $filter */

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

collection_filter_expression ::=
    field_path'/all(' lambda_expression ')'
    | field_path'/any(' lambda_expression ')'
    | field_path'/any()'

lambda_expression ::= identifier ':' boolean_expression

logical_expression ::=
    boolean_expression ('and' | 'or') boolean_expression
    | 'not' boolean_expression

comparison_expression ::= 
    variable_or_function comparison_operator constant | 
    constant comparison_operator variable_or_function

variable_or_function ::= variable | function_call

comparison_operator ::= 'gt' | 'lt' | 'ge' | 'le' | 'eq' | 'ne'


/* Rules for constants and literals */

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


/* Rules for functions */

function_call ::=
    geo_distance_call |
    boolean_function_call

geo_distance_call ::=
    'geo.distance(' variable ',' geo_point ')'
    | 'geo.distance(' geo_point ',' variable ')'

geo_point ::= "geography'POINT(" lon_lat ")'"

lon_lat ::= float_literal ' ' float_literal

boolean_function_call ::=
    geo_intersects_call |
    search_in_call |
    search_is_match_call

geo_intersects_call ::=
    'geo.intersects(' variable ',' geo_polygon ')'

/* You need at least four points to form a polygon, where the first and
last points are the same. */
geo_polygon ::=
    "geography'POLYGON((" lon_lat ',' lon_lat ',' lon_lat ',' lon_lat_list "))'"

lon_lat_list ::= lon_lat(',' lon_lat)*

search_in_call ::=
    'search.in(' variable ',' string_literal(',' string_literal)? ')'

/* Note that it is illegal to call search.ismatch or search.ismatchscoring
from inside a lambda expression. */
search_is_match_call ::=
    'search.ismatch'('scoring')?'(' search_is_match_parameters ')'

search_is_match_parameters ::=
    string_literal(',' string_literal(',' query_type ',' search_mode)?)?

query_type ::= "'full'" | "'simple'"

search_mode ::= "'any'" | "'all'"
```

## <a name="syntax-diagram"></a>Szintaxisdiagramja

Vizuálisan böngészhet az Azure Search által támogatott OData nyelvi szintaxis, próbálja ki interaktív szintaxisdiagramja:

> [!div class="nextstepaction"]
> [Azure Search OData szintaxisdiagramja](https://azuresearch.github.io/odata-syntax-diagram/)

## <a name="see-also"></a>Lásd még  

- [Szűrők az Azure Search szolgáltatásban](search-filters.md)
- [Dokumentumok keresése &#40;az Azure Search szolgáltatás REST API-ja&#41;](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)
- [Lucene lekérdezési szintaxis](query-lucene-syntax.md)
- [Az Azure Search egyszerű lekérdezési szintaxis](query-simple-syntax.md)
