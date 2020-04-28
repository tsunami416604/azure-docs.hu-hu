---
title: OData logikai operátor referenciája
titleSuffix: Azure Cognitive Search
description: Szintaxis és dokumentáció a OData logikai operátorok, valamint, illetve, illetve az Azure Cognitive Search-lekérdezések használata esetén.
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "74113190"
---
# <a name="odata-logical-operators-in-azure-cognitive-search---and-or-not"></a>OData logikai operátorok az Azure- `and`ban `or`Cognitive Search-,,`not`

Az Azure Cognitive Search [OData](query-odata-filter-orderby-syntax.md) olyan logikai kifejezések, amelyek kiértékelése `true` vagy `false`. Az összetett szűrőket úgy írhatja be, hogy az [egyszerűbb szűrők](search-query-odata-comparison-operators.md) sorozatát írja le, és a logikai operátorok segítségével készíti el őket a logikai [algebra](https://en.wikipedia.org/wiki/Boolean_algebra)használatával:

- `and`: Egy bináris operátor, amely kiértékeli, hogy `true` a bal és a jobb oldali alkifejezések is kiértékelésre kerülnek-e. `true`
- `or`: Egy bináris operátor, amely kiértékeli, hogy `true` a bal vagy a jobb oldali alkifejezések valamelyike `true`kiértékeli-e.
- `not`: Egy egyoperandusú operátor, amely `true` kiértékeli `false`, hogy az alkifejezés kiértékelése és fordítva.

Ezek a [gyűjtemény-operátorokkal `any` együtt, `all`és ](search-query-odata-collection-operators.md)lehetővé teszik olyan szűrők összeállítását, amelyek nagyon összetett keresési feltételeket tudnak kifejezni.

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

A logikai kifejezések két formája`and`/`or`létezik: bináris (), ahol két alkifejezés létezik, és az unáris (`not`), ahol csak egy szerepel. Az alkifejezések bármely típusú logikai kifejezés lehet:

- Mezők vagy tartomány típusú változók`Edm.Boolean`
- Függvények, amelyek típusú `Edm.Boolean`értékeket adnak vissza, például `geo.intersects` vagy`search.ismatch`
- [Összehasonlító kifejezések](search-query-odata-comparison-operators.md), például`rating gt 4`
- [Gyűjtési kifejezések](search-query-odata-collection-operators.md), például`Rooms/any(room: room/Type eq 'Deluxe Room')`
- A logikai literálok `true` vagy `false`.
- Egyéb, a, `and` `or`és `not`a használatával létrehozott logikai kifejezések.

> [!IMPORTANT]
> Vannak olyan helyzetek `and` / `or`, amelyekben nem használhatók az alkifejezések, különösen lambda kifejezéseken belül. Részletekért lásd: [OData Collection Operators in Azure Cognitive Search](search-query-odata-collection-operators.md#limitations) .

### <a name="logical-operators-and-null"></a>Logikai operátorok és`null`

A legtöbb logikai kifejezés, például a függvények és az összehasonlítások nem hozhatnak létre `null` értékeket, és a logikai operátorok nem alkalmazhatók közvetlenül a `x and null` `null` konstansra (például nem megengedett). A logikai mezők azonban `null`lehetnek, ezért tisztában kell lennie azzal, hogy a, `and` `or`a és `not` a operátorok hogyan működnek a nullák jelenlétében. Ezt a következő táblázat foglalja össze, ahol `b` a egy típusú `Edm.Boolean`mező:

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

Ha egy logikai mező `b` önmagában egy szűrési kifejezésben jelenik meg, akkor úgy viselkedik, mintha `b eq true`megírták volna, tehát ha `b` van `null`, a kifejezés kiértékeli `false`a következőt:. Hasonlóképpen, `not b` úgy viselkedik `not (b eq true)`, hogy kiértékeli a `true`következőt:. Így a mezők ugyanúgy `null` viselkednek, mint `false`. Ez összhangban van azzal, ahogyan a és `and` `or`a használatával más kifejezésekkel kombinálva, ahogy az a fenti táblázatban is látható. Ennek ellenére a közvetlen összehasonlítás `false` () továbbra is kiértékeli a`b eq false` `false`következőt:. Más szóval, `null` nem egyenlő a `false`(z) értékkel, még akkor is, ha a logikai kifejezésekben hasonlóan viselkedik.

## <a name="examples"></a>Példák

Olyan dokumentumok egyeztetése `rating` , amelyekben a mező 3 és 5 közötti, beleértve a következőket:

    rating ge 3 and rating le 5

Olyan dokumentumok egyeztetése, amelyekben `ratings` a mező minden eleme kisebb, mint 3, vagy több mint 5:

    ratings/all(r: r lt 3 or r gt 5)

Egyezteti a dokumentumokat `location` , ahol a mező a megadott sokszögen belül van, és a dokumentum nem tartalmazza a "Public" kifejezést.

    geo.intersects(location, geography'POLYGON((-122.031577 47.578581, -122.031577 47.678581, -122.131577 47.678581, -122.031577 47.578581))') and not search.ismatch('public')

A következőhöz tartozó dokumentumok egyeztetése: Vancouver, Kanada, ahol van egy Deluxe szoba, amelynek alapdíja 160-nál kisebb:

    Address/City eq 'Vancouver' and Address/Country eq 'Canada' and Rooms/any(room: room/Type eq 'Deluxe Room' and room/BaseRate lt 160)

## <a name="next-steps"></a>További lépések  

- [Szűrők az Azure Cognitive Search](search-filters.md)
- [Az Azure Cognitive Search OData kifejezés nyelvének áttekintése](query-odata-filter-orderby-syntax.md)
- [Az Azure Cognitive Search OData-kifejezési szintaxisának referenciája](search-query-odata-syntax-reference.md)
- [Dokumentumok keresése &#40;Azure Cognitive Search REST API&#41;](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)
