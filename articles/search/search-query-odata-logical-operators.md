---
title: OData logikai operátorhivatkozás
titleSuffix: Azure Cognitive Search
description: Szintaxis és hivatkozási dokumentáció az OData logikai operátorok, és vagy, és nem az Azure Cognitive Search lekérdezések.
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
ms.openlocfilehash: 2d3952f7d2adc26892cbebcd962f2ea25b86de7d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74113190"
---
# <a name="odata-logical-operators-in-azure-cognitive-search---and-or-not"></a>OData logikai operátorok az `and` `or`Azure Cognitive Search - , ,`not`

Az Azure Cognitive Search [oData-szűrőkifejezései](query-odata-filter-orderby-syntax.md) logikai `false`kifejezések, amelyek kiértékelése vagy `true` a. Összetett szűrőt úgy írhat, hogy [egyszerűbb szűrőket ír,](search-query-odata-comparison-operators.md) és a logikai operátorok segítségével a [Logikai algebrából](https://en.wikipedia.org/wiki/Boolean_algebra)ír:

- `and`: Bináris operátor, amely `true` azt a értéket értékeli, hogy `true`a bal és a jobb alkifejezése i.
- `or`: Bináris operátor, amely `true` azt a értéket értékeli, hogy `true`a bal vagy a jobb alkifejezése közül az egyik nek a lesz-e a eredménye.
- `not`: Olyan unáris operátor, amely azt a értéket értékeli, `true` hogy `false`a részkifejezése kiértékeli-e a értéket, és fordítva.

Ezek a [gyűjteményoperátorokkal `any` és `all` ](search-query-odata-collection-operators.md)a , lehetővé teszik olyan szűrők kialakítását, amelyek nagyon összetett keresési feltételeket fejezhetnek ki.

## <a name="syntax"></a>Szintaxis

A következő EBNF ([Extended Backus-Naur Form](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form)) határozza meg a logikai operátorokat használó OData-kifejezések nyelvtanát.

<!-- Upload this EBNF using https://bottlecaps.de/rr/ui to create a downloadable railroad diagram. -->

```
logical_expression ::=
    boolean_expression ('and' | 'or') boolean_expression
    | 'not' boolean_expression
```

Interaktív szintaktikai diagram is elérhető:

> [!div class="nextstepaction"]
> [OData-szintaktikai diagram az Azure Cognitive Search szolgáltatáshoz](https://azuresearch.github.io/odata-syntax-diagram/#logical_expression)

> [!NOTE]
> Lásd: [OData kifejezés szintaxis referencia az Azure Cognitive Search](search-query-odata-syntax-reference.md) a teljes EBNF.

A logikai kifejezéseknek két formája`and`/`or`van: bináris ( ), ahol két`not`részkifejezés van, és unary ( ), ahol csak egy van. Az alkifejezések bármilyen típusú logikai kifejezések lehetnek:

- Mezők vagy tartományváltozók típusúak`Edm.Boolean`
- Típusú értékeket `Edm.Boolean`visszaadó `geo.intersects` függvények, például vagy`search.ismatch`
- [Összehasonlító kifejezések](search-query-odata-comparison-operators.md), például`rating gt 4`
- [Gyűjteménykifejezések](search-query-odata-collection-operators.md), például`Rooms/any(room: room/Type eq 'Deluxe Room')`
- A logikai `true` konstansok vagy `false`.
- A használatával készült `and`egyéb `or`logikai `not`kifejezések , a és a.

> [!IMPORTANT]
> Vannak olyan helyzetek, amikor nem mindenfajta részkifejezés `and` / `or`használható a , különösen a lambda kifejezéseken belül. A részleteket az [Azure Cognitive Search OData-gyűjteményoperátorai](search-query-odata-collection-operators.md#limitations) az Azure Cognitive Search webhelyen találja.

### <a name="logical-operators-and-null"></a>Logikai operátorok és`null`

A legtöbb logikai kifejezés, például `null` a függvények és az összehasonlítások nem `null` tudnak értékeket `x and null` létrehozni, és a logikai operátorok nem alkalmazhatók közvetlenül a konstansra (például nem engedélyezett). A logikai mezők `null`azonban lehetnek , ezért tisztában `or`kell `not` lennie azzal, hogy a és az `and`operátorok hogyan viselkednek null jelenlétében. Ezt a következő táblázat foglalja össze, ahol `b` a `Edm.Boolean`mező típusa található:

| Kifejezés | Eredmény, `b` ha`null` |
| --- | --- |
| `b` | `false` |
| `not b` | `true` |
| `b eq true` | `false` |
| `b eq false` | `false` |
| `b eq null` | `true` |
| `b ne true` | `true` |
| `b ne false` | `true` |
| `b ne null` | `false` |
| `b and true` | `false` |
| `b and false` | `false` |
| `b or true` | `true` |
| `b or false` | `false` |

Amikor egy logikai `b` mező önmagában jelenik meg egy szűrőkifejezésben, `b eq true`úgy viselkedik, mintha meg lett volna írva , így ha `b` van, `null`a kifejezés eredménye `false`a . Hasonlóképpen, `not b` úgy viselkedik, mint `not (b eq true)`, `true`így értékeli a . Ily módon `null` a mezők ugyanúgy `false`viselkednek, mint a . Ez összhangban van azzal, ahogyan viselkednek, `and` `or`ha más kifejezésekkel kombinálják, és amint az a fenti táblázatban látható. Ennek ellenére a `false` (`b eq false`) közvetlen `false`összehasonlítás a következőpéldául: Más szóval, `null` nem `false`egyenlő , annak ellenére, hogy úgy viselkedik, mint a logikai kifejezések.

## <a name="examples"></a>Példák

Olyan dokumentumok `rating` egyeztetése, amelyeknél a mező 3 és 5 között van, a következőket is beleértve:

    rating ge 3 and rating le 5

Olyan dokumentumok egyeztetése, `ratings` amelyekben a mező minden eleme 3-nál kisebb vagy 5-nél nagyobb:

    ratings/all(r: r lt 3 or r gt 5)

Egyezik `location` azokkal a dokumentumokkal, amelyeknél a mező az adott sokszögen belül van, és a dokumentum nem tartalmazza a "nyilvános" kifejezést.

    geo.intersects(location, geography'POLYGON((-122.031577 47.578581, -122.031577 47.678581, -122.131577 47.678581, -122.031577 47.578581))') and not search.ismatch('public')

Match dokumentumok szállodák Vancouver, Kanada, ahol van egy deluxe szoba alapáron kevesebb, mint 160:

    Address/City eq 'Vancouver' and Address/Country eq 'Canada' and Rooms/any(room: room/Type eq 'Deluxe Room' and room/BaseRate lt 160)

## <a name="next-steps"></a>További lépések  

- [Szűrők az Azure Cognitive Search szolgáltatásban](search-filters.md)
- [Az Azure Cognitive Search OData-kifejezés nyelvének áttekintése](query-odata-filter-orderby-syntax.md)
- [Az Azure Cognitive Search OData-kifejezés szintaxisának hivatkozása](search-query-odata-syntax-reference.md)
- [Az Azure Cognitive Search REST API-&#41;&#40;dokumentumok keresése](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)
