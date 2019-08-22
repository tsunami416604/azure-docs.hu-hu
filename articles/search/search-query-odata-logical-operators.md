---
title: OData logikai operátor referenciája – Azure Search
description: OData logikai operátorok, és, vagy, és nem, Azure Search lekérdezésekben.
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
ms.openlocfilehash: bf4939a40a2fdf1c8fc6cf97beca0184b1604c98
ms.sourcegitcommit: bb8e9f22db4b6f848c7db0ebdfc10e547779cccc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/20/2019
ms.locfileid: "69647998"
---
# <a name="odata-logical-operators-in-azure-search---and-or-not"></a>Logikai operátorok OData a Azure Search `and`- `or`,,`not`

A Azure Search [OData-szűrési kifejezések](query-odata-filter-orderby-syntax.md) olyan logikai kifejezések, `true` amelyek `false`kiértékelése vagy. Az összetett szűrőket úgy írhatja be, hogy az [egyszerűbb szűrők](search-query-odata-comparison-operators.md) sorozatát írja le, és a logikai operátorok segítségével készíti el őket a logikai [algebra](https://en.wikipedia.org/wiki/Boolean_algebra)használatával:

- `and`: Egy bináris operátor, amely kiértékeli, hogy `true` a bal és a jobb oldali alkifejezések is `true`kiértékelésre kerülnek-e.
- `or`: Bináris operátor, amely kiértékeli `true` , hogy a bal vagy a jobb oldali alkifejezések valamelyike kiértékeli `true`-e a következőt:.
- `not`: Egyoperandusú operátor, amely kiértékeli `true` `false`, hogy az alkifejezés kiértékelése és fordítva.

Ezek a [gyűjtemény- `any` operátorokkal együtt, `all`és ](search-query-odata-collection-operators.md)lehetővé teszik olyan szűrők összeállítását, amelyek nagyon összetett keresési feltételeket tudnak kifejezni.

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
> [Azure Search OData szintaxisának diagramja](https://azuresearch.github.io/odata-syntax-diagram/#logical_expression)

> [!NOTE]
> A teljes EBNF [Azure Search a OData kifejezés szintaxisát](search-query-odata-syntax-reference.md) ismertető témakörben talál.

A logikai kifejezések két formája`and`létezik: bináris (/`or`), ahol két alkifejezés létezik, és az unáris (`not`), ahol csak egy szerepel. Az alkifejezések bármely típusú logikai kifejezés lehet:

- Mezők vagy tartomány típusú változók`Edm.Boolean`
- Függvények, amelyek típusú `Edm.Boolean`értékeket adnak vissza, `geo.intersects` például vagy`search.ismatch`
- [Összehasonlító kifejezések](search-query-odata-comparison-operators.md), például`rating gt 4`
- [Gyűjtési kifejezések](search-query-odata-collection-operators.md), például`Rooms/any(room: room/Type eq 'Deluxe Room')`
- A logikai literálok `true` vagy `false`.
- Egyéb, a, `and` `or`és `not`a használatával létrehozott logikai kifejezések.

> [!IMPORTANT]
> Vannak olyan helyzetek `and` `or`, amelyekben nem használhatók /az alkifejezések, különösen lambda kifejezéseken belül. Részletekért tekintse [meg a Azure Search OData-gyűjtemény operátorait](search-query-odata-collection-operators.md#limitations) .

### <a name="logical-operators-and-null"></a>Logikai operátorok és`null`

A legtöbb logikai kifejezés, például a függvények és az összehasonlítások nem hozhatnak létre `null` értékeket, és a logikai operátorok nem alkalmazhatók `x and null` közvetlenül a `null` konstansra (például nem megengedett). A logikai mezők azonban `null`lehetnek, ezért tisztában kell lennie azzal, hogy a, `and` `or`a és `not` a operátorok hogyan működnek a nullák jelenlétében. Ezt a következő táblázat foglalja össze, ahol `b` a egy típusú `Edm.Boolean`mező:

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

Ha egy logikai mező `b` önmagában egy szűrési kifejezésben jelenik meg, akkor úgy viselkedik, mintha megírták `b eq true`volna, tehát ha `b` van `null`, a kifejezés kiértékeli `false`a következőt:. Hasonlóképpen, `not b` úgy viselkedik `not (b eq true)`, hogy kiértékeli a `true`következőt:. Így a mezők ugyanúgy `null` viselkednek, mint `false`. Ez összhangban van azzal, ahogyan a és `and` `or`a használatával más kifejezésekkel kombinálva, ahogy az a fenti táblázatban is látható. Ennek ellenére a közvetlen összehasonlítás `false` () továbbra is kiértékeli a `false`következőt`b eq false`:. Más szóval, `null` nem egyenlő a `false`(z) értékkel, még akkor is, ha a logikai kifejezésekben hasonlóan viselkedik.

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

- [Szűrők a Azure Searchban](search-filters.md)
- [A OData kifejezés nyelvének áttekintése Azure Search](query-odata-filter-orderby-syntax.md)
- [Azure Search OData-kifejezés szintaxisának referenciája](search-query-odata-syntax-reference.md)
- [Dokumentumok &#40;keresése Azure Search szolgáltatás REST API&#41;](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)
