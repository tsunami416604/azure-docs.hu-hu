---
title: OData-kifejezés szintaxisának leírása – Azure Search
description: A OData kifejezések formális nyelvtani és szintaxisi specifikációja Azure Search lekérdezésekben.
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
ms.openlocfilehash: 5d7e6456cd6a6648ff2ca38ecbb4f2de5479d7c9
ms.sourcegitcommit: bb8e9f22db4b6f848c7db0ebdfc10e547779cccc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/20/2019
ms.locfileid: "69647495"
---
# <a name="odata-expression-syntax-reference-for-azure-search"></a>Azure Search OData-kifejezés szintaxisának referenciája

A Azure Search [OData](https://docs.oasis-open.org/odata/odata/v4.01/odata-v4.01-part2-url-conventions.html) -kifejezéseket használ paraméterként az API-ban. A és `$orderby` `$filter` a paraméterek leggyakrabban a OData kifejezéseket használják. Ezek a kifejezések bonyolultak lehetnek, amelyek több záradékot, funkciót és operátort tartalmaznak. Az Azure Search REST API számos részében azonban még az egyszerű OData kifejezések is használatosak, például a tulajdonságok elérési útjai. Az elérésiút-kifejezések például az összetett mezők almezőire hivatkoznak az API-ban mindenhol, például ha az almezőket egy javaslatban, egy [](index-add-suggesters.md) [pontozási függvényben](index-add-scoring-profiles.md), a `$select` paraméterben vagy akár a [Lucene lekérdezések](query-lucene-syntax.md).

Ez a cikk a OData kifejezések ezen formáit mutatja be formális nyelvtan használatával. Létezik egy [interaktív diagram](#syntax-diagram) is, amely a nyelvtan vizuális megismerését segíti.

## <a name="formal-grammar"></a>Formális nyelvtan

A Azure Search által támogatott OData nyelv részhalmazát a EBNF ([bővített Naur Form](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form)) nyelvtan használatával tudjuk leírni. A szabályok "felülről lefelé" vannak felsorolva, kezdve a legbonyolultabb kifejezésekkel, és az összetettebb kifejezéseket lebontva. A fent láthatók a Azure Search REST API adott paramétereinek megfelelő nyelvtani szabályok:

- [`$filter`](search-query-odata-filter.md), amelyet a `filter_expression` szabály határoz meg.
- [`$orderby`](search-query-odata-orderby.md), amelyet a `order_by_expression` szabály határoz meg.
- [`$select`](search-query-odata-select.md), amelyet a `select_expression` szabály határoz meg.
- A `field_path` szabály által definiált mezők elérési útjai. A mező elérési útjai a teljes API-ban használatosak. Az indexek legfelső szintű mezőire, illetve egy vagy több [összetett mező](search-howto-complex-data-types.md) őseire hivatkozó almezőket is hivatkozhatnak.

Miután a EBNF egy böngészhető [szintaxisú diagram](https://en.wikipedia.org/wiki/Syntax_diagram) , amely lehetővé teszi, hogy interaktív módon vizsgálja meg a nyelvtant és a szabályok közötti kapcsolatokat.

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

A Azure Search által támogatott nyelvi nyelvtan vizuális megismeréséhez próbálkozzon az interaktív OData:

> [!div class="nextstepaction"]
> [Azure Search OData szintaxisának diagramja](https://azuresearch.github.io/odata-syntax-diagram/)

## <a name="see-also"></a>Lásd még  

- [Szűrők a Azure Searchban](search-filters.md)
- [Dokumentumok &#40;keresése Azure Search szolgáltatás REST API&#41;](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)
- [Lucene lekérdezési szintaxis](query-lucene-syntax.md)
- [Egyszerű lekérdezési szintaxis a Azure Searchban](query-simple-syntax.md)
