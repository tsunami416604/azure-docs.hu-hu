---
title: OData kifejezés szintaxisának hivatkozása
titleSuffix: Azure Cognitive Search
description: Az Azure Cognitive Search-lekérdezésekben az OData-kifejezések hivatalos nyelvtani és szintaxisspecifikációja.
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
ms.openlocfilehash: f3422fd10e062ae87bc165491e0d01ac2b4943d2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "72793232"
---
# <a name="odata-expression-syntax-reference-for-azure-cognitive-search"></a>Az Azure Cognitive Search OData-kifejezés szintaxisának hivatkozása

Az Azure Cognitive Search [odata-kifejezéseket](https://docs.oasis-open.org/odata/odata/v4.01/odata-v4.01-part2-url-conventions.html) használ paraméterekként az API-ban. Leggyakrabban az OData kifejezéseket használják `$orderby` a `$filter` és a paraméterekhez. Ezek a kifejezések összetettek lehetnek, több záradékot, függvényt és operátort tartalmaznak. Azonban még az egyszerű OData-kifejezések, például a tulajdonságelérési utak is használatosak az Azure Cognitive Search REST API számos részén. Az elérési út kifejezései például az összetett mezők almezőire vonatkoznak az API-ban, például amikor almezőket sorolnak fel egy [javaslatajánlóban,](index-add-suggesters.md)egy [pontozási függvényt](index-add-scoring-profiles.md), a `$select` paramétert, vagy akár a [Lucene-lekérdezésekben mezőre helyezett keresést.](query-lucene-syntax.md)

Ez a cikk az OData-kifejezések összes ilyen formáját ismerteti formális nyelvtan használatával. Van is egy [interaktív diagram,](#syntax-diagram) hogy segítsen vizuálisan felfedezni a nyelvtan.

## <a name="formal-grammar"></a>Formális nyelvtan

Az Azure Cognitive Search által támogatott OData nyelv részhalmazát eBNF ([Extended Backus-Naur Form](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form)) nyelvtan használatával tudjuk leírni. A szabályok "felülről lefelé" vannak felsorolva, kezdve a legösszetettebb kifejezésekkel, majd primitívebb kifejezésekre bontva azokat. A tetején vannak a nyelvtani szabályok, amelyek megfelelnek az Azure Cognitive Search REST API adott paramétereinek:

- [`$filter`](search-query-odata-filter.md), amelyet `filter_expression` a szabály határoz meg.
- [`$orderby`](search-query-odata-orderby.md), amelyet `order_by_expression` a szabály határoz meg.
- [`$select`](search-query-odata-select.md), amelyet `select_expression` a szabály határoz meg.
- A `field_path` szabály által meghatározott mezőútvonalak. A mezőútvonalak az API-ban használatosak. Hivatkozhatnak egy index legfelső szintű mezőire, vagy egy vagy több összetett mezőelődessel rendelkező almezőkre. [complex field](search-howto-complex-data-types.md)

Miután az EBNF egy böngészhető [szintaktikai diagram,](https://en.wikipedia.org/wiki/Syntax_diagram) amely lehetővé teszi, hogy interaktívan vizsgálja meg a nyelvtan és a kapcsolatok között a szabályokat.

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

## <a name="syntax-diagram"></a>Szintaxis diagram

Az Azure Cognitive Search által támogatott OData-nyelvnyelv-nyelvtan vizuális feltárásához próbálja ki az interaktív szintaxisdiagramot:

> [!div class="nextstepaction"]
> [OData-szintaktikai diagram az Azure Cognitive Search szolgáltatáshoz](https://azuresearch.github.io/odata-syntax-diagram/)

## <a name="see-also"></a>Lásd még  

- [Szűrők az Azure Cognitive Search szolgáltatásban](search-filters.md)
- [Az Azure Cognitive Search REST API-&#41;&#40;dokumentumok keresése](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)
- [Lucene lekérdezés szintaxisa](query-lucene-syntax.md)
- [Egyszerű lekérdezésszintaxis az Azure Cognitive Search szolgáltatásban](query-simple-syntax.md)
