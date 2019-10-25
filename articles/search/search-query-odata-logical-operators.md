---
title: OData logikai operátor referenciája
titleSuffix: Azure Cognitive Search
description: OData logikai operátorok, és, vagy, és nem, az Azure Cognitive Search lekérdezésekben.
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
ms.openlocfilehash: 4e016047d66e49f17c08d4b92a1c865f4b63e39b
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/23/2019
ms.locfileid: "72793322"
---
# <a name="odata-logical-operators-in-azure-cognitive-search---and-or-not"></a>OData logikai operátorok az Azure Cognitive Search-`and`, `or`, `not`

Az Azure Cognitive Search [OData](query-odata-filter-orderby-syntax.md) olyan logikai kifejezések, amelyek kiértékelése `true` vagy `false`. Az összetett szűrőket úgy írhatja be, hogy az [egyszerűbb szűrők](search-query-odata-comparison-operators.md) sorozatát írja le, és a logikai operátorok segítségével készíti el őket a logikai [algebra](https://en.wikipedia.org/wiki/Boolean_algebra)használatával:

- `and`: egy bináris operátor, amely kiértékeli a `true`, ha a bal és a jobb oldali alkifejezések kiértékelése `true`.
- `or`: egy bináris operátor, amely kiértékeli, hogy `true`, ha a bal vagy a jobb oldali alkifejezések valamelyike `true`.
- `not`: egy egyoperandusú operátor, amely kiértékeli, hogy `true`, ha az alkifejezés kiértékelése `false`, és fordítva.

Ezek a [`any` és `all`összegyűjtési operátorokkal ](search-query-odata-collection-operators.md)együtt lehetővé teszik olyan szűrők összeállítását, amelyek nagyon összetett keresési feltételeket tudnak kifejezni.

## <a name="syntax"></a>Szintaxis

A következő EBNF ([bővített Naur űrlap](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form)) a logikai operátorokat használó OData-kifejezések nyelvtanát határozza meg.

<!-- Upload this EBNF using https://bottlecaps.de/rr/ui to create a downloadable railroad diagram. -->

```
logical_expression ::=
    boolean_expression ('and' | 'or') boolean_expression
    | 'not' boolean_expression
```

Az interaktív szintaxis diagram is elérhető:

> [!div class="nextstepaction"]
> [Az Azure Cognitive Search OData szintaxisának diagramja](https://azuresearch.github.io/odata-syntax-diagram/#logical_expression)

> [!NOTE]
> Tekintse meg az [Azure Cognitive Search OData-kifejezés szintaxisának referenciáját](search-query-odata-syntax-reference.md) a teljes EBNF.

A logikai kifejezések két formája létezik: bináris (`and`/`or`), ahol két alkifejezés és egyoperandusú (`not`) szerepel, ahol csak egy szerepel. Az alkifejezések bármely típusú logikai kifejezés lehet:

- Mezők vagy tartomány típusú változók `Edm.Boolean`
- Olyan függvények, amelyek `Edm.Boolean`típusú értékeket adnak vissza, például `geo.intersects` vagy `search.ismatch`
- [Összehasonlító kifejezések](search-query-odata-comparison-operators.md), például `rating gt 4`
- [Gyűjtési kifejezések](search-query-odata-collection-operators.md), például `Rooms/any(room: room/Type eq 'Deluxe Room')`
- A logikai literál `true` vagy `false`.
- Más, `and`, `or`és `not`használatával létrehozott logikai kifejezések.

> [!IMPORTANT]
> Vannak olyan helyzetek, amelyekben nem használhatók az alkifejezések `and`/`or`, különösen lambda kifejezéseken belül. Részletekért lásd: [OData Collection Operators in Azure Cognitive Search](search-query-odata-collection-operators.md#limitations) .

### <a name="logical-operators-and-null"></a>Logikai operátorok és `null`

A legtöbb logikai kifejezés, például a függvények és az összehasonlítások nem képesek `null` értékeket létrehozni, és a logikai operátorok nem alkalmazhatók közvetlenül a `null` literálra (például `x and null` nem engedélyezett). A logikai mezők azonban `null`is lehetnek, ezért tisztában kell lennie azzal, hogy a `and`, a `or`és a `not` operátorok hogyan viselkednek a nullák jelenlétében. Ezt a következő táblázat foglalja össze, ahol a `b` `Edm.Boolean`típusú mező:

| Kifejezés | Eredmény, ha a `b` `null` |
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

Ha egy logikai mező `b` egy szűrési kifejezésben, akkor úgy viselkedik, mintha `b eq true`lett volna, így ha `b` `null`, a kifejezés kiértékelése `false`. Hasonlóképpen, `not b` úgy viselkedik, mint `not (b eq true)`, ezért a `true`kiértékelése. Így `null` a mezők ugyanúgy viselkednek, mint `false`. Ez összhangban van azzal, hogy hogyan viselkednek, ha a fenti táblázatban látható módon más kifejezésekkel kombinálva `and` és `or`használatával. Ennek ellenére a `false` (`b eq false`) közvetlen összevetése továbbra is kiértékeli `false`. Más szóval, `null` nem egyenlő a `false`val, bár logikai kifejezésekben hasonlóan viselkedik.

## <a name="examples"></a>Példák

Olyan dokumentumok egyeztetése, amelyekben a `rating` mező 3 és 5 közötti, beleértve a következőket:

    rating ge 3 and rating le 5

Azon dokumentumok egyeztetése, amelyekben a `ratings` mező minden eleme 3 vagy nagyobb, mint 5:

    ratings/all(r: r lt 3 or r gt 5)

Azon dokumentumok egyeztetése, amelyekben a `location` mező a megadott sokszögen belül van, és a dokumentum nem tartalmazza a "Public" kifejezést.

    geo.intersects(location, geography'POLYGON((-122.031577 47.578581, -122.031577 47.678581, -122.131577 47.678581, -122.031577 47.578581))') and not search.ismatch('public')

A következőhöz tartozó dokumentumok egyeztetése: Vancouver, Kanada, ahol van egy Deluxe szoba, amelynek alapdíja 160-nál kisebb:

    Address/City eq 'Vancouver' and Address/Country eq 'Canada' and Rooms/any(room: room/Type eq 'Deluxe Room' and room/BaseRate lt 160)

## <a name="next-steps"></a>Következő lépések  

- [Szűrők az Azure Cognitive Search](search-filters.md)
- [Az Azure Cognitive Search OData kifejezés nyelvének áttekintése](query-odata-filter-orderby-syntax.md)
- [Az Azure Cognitive Search OData-kifejezési szintaxisának referenciája](search-query-odata-syntax-reference.md)
- [Dokumentumok &#40;keresése az Azure Cognitive Search REST API&#41;](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)
