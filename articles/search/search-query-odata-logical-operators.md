---
title: OData logikai operátor referencia – Azure Search
description: OData logikai operátorokat, és, vagy, és nem, az Azure Search-lekérdezéseket.
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
ms.openlocfilehash: de93765117b4cafe70e5ad277e32ca0a1fa8d90a
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "67079768"
---
# <a name="odata-logical-operators-in-azure-search---and-or-not"></a>Logikai operátorok OData az Azure Search - `and`, `or`, `not`

[OData szűrési kifejezésekben](query-odata-filter-orderby-syntax.md) vannak az Azure Search szolgáltatásban a logikai kifejezésen, amelyek `true` vagy `false`. Egy összetett szűrési írhat egy sorozatát írásával [egyszerűbb szűrők](search-query-odata-comparison-operators.md) összeállítása azokat a logikai operátorokat és [logikai algebra](https://en.wikipedia.org/wiki/Boolean_algebra):

- `and`: Egy bináris operátor visszaadó `true` Ha mind a bal és jobb az alkifejezések kiértékelése `true`.
- `or`: Egy bináris operátor visszaadó `true` Ha a bal vagy jobb alkifejezések egyik értékelődik ki `true`.
- `not`: Egy egyoperandusú operátorral visszaadó `true` Ha az alárendelt kifejezésében `false`, és fordítva.

Ezek együtt a [gyűjtemény operátorok `any` és `all` ](search-query-odata-collection-operators.md), ahol összeállíthatja a szűrőket, amelyek nagyon összetett keresési feltételek fejezhető ki.

## <a name="syntax"></a>Szintaxis

A következő EBNF ([kiterjesztett Backus-Naur űrlap](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form)) határozza meg a szintaxis egy OData-kifejezés, amely a logikai operátorokat használja.

<!-- Upload this EBNF using https://bottlecaps.de/rr/ui to create a downloadable railroad diagram. -->

```
logical_expression ::=
    boolean_expression ('and' | 'or') boolean_expression
    | 'not' boolean_expression
```

Egy interaktív szintaxisdiagramja is érhető el:

> [!div class="nextstepaction"]
> [Azure Search OData szintaxisdiagramja](https://azuresearch.github.io/odata-syntax-diagram/#logical_expression)

> [!NOTE]
> Lásd: [OData kifejezés szintaxisának referenciája az Azure Search](search-query-odata-syntax-reference.md) a teljes EBNF számára.

Nincsenek logikai kifejezéseket kétféle: bináris (`and`/`or`), ahol két az alkifejezések és egyoperandusú (`not`), ahol csak egy. Az alkifejezések logikai kifejezéseket bármilyen típusú lehet:

- Típusú mezők és tartomány változók `Edm.Boolean`
- Típusú értékek visszatérő függvények `Edm.Boolean`, mint például `geo.intersects` vagy `search.ismatch`
- [Összehasonlítási kifejezésekben](search-query-odata-comparison-operators.md), például `rating gt 4`
- [Gyűjtemény kifejezések](search-query-odata-collection-operators.md), például `Rooms/any(room: room/Type eq 'Deluxe Room')`
- A logikai literálok `true` vagy `false`.
- Más logikai kifejezések jönnek létre `and`, `or`, és `not`.

> [!IMPORTANT]
> Bizonyos esetekben, ahol alárendelt kifejezés nem minden típusú használható `and` / `or`, különösen belül lambda-kifejezésekkel. Lásd: [az Azure Search OData-gyűjtemény operátorok](search-query-odata-collection-operators.md#limitations) részleteiről.

### <a name="logical-operators-and-null"></a>Logikai operátorok és `null`

A legtöbb logikai kifejezésen, például a functions és a összehasonlítások nem tud `null` értékeket és a logikai operátor nem alkalmazható a `null` közvetlenül konstans (például `x and null` nem engedélyezett). Azonban lehet, logikai mezőket `null`, tehát meg kell tudni, hogy a `and`, `or`, és `not` operátorok viselkednek zajok mellett null értékű. Ez az alábbi táblázat foglalja össze, `b` típusú mező `Edm.Boolean`:

| kifejezés | Eredmény amikor `b` van `null` |
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

Ha egy logikai típusú mező `b` jelenik meg saját maga a szűrőkifejezést úgy viselkedik, mintha kellett lett írva `b eq true`, tehát ha `b` van `null`, a kifejezés eredménye `false`. Ehhez hasonlóan `not b` viselkedik `not (b eq true)`, így az eredmény `true`. Ezzel a módszerrel `null` mezők viselkedik, mint `false`. Megfelel az egyéb kifejezések használatával kombinálva módjára `and` és `or`, ahogyan az a fenti táblázatban. Annak ellenére, hogy ez, és a egy közvetlen összehasonlítása `false` (`b eq false`) továbbra is kiértékelik `false`. Más szóval `null` nem egyenlő `false`, annak ellenére, hogy a művelet viselkedése azt a logikai kifejezésen.

## <a name="examples"></a>Példák

Egyezés dokumentálja a helyét a `rating` mező hossza 3 és 5 között lehet között:

    rating ge 3 and rating le 5

Egyezés dokumentumok where összes elemét a `ratings` mezőt a rendszer kevesebb mint 3 vagy 5-nél nagyobb:

    ratings/all(r: r lt 3 or r gt 5)

Egyezés dokumentálja a helyét a `location` mező az adott sokszög belül, és a dokumentum nem tartalmaz "public" kifejezés.

    geo.intersects(location, geography'POLYGON((-122.031577 47.578581, -122.031577 47.678581, -122.131577 47.678581, -122.031577 47.578581))') and not search.ismatch('public')

A "Hotels" Vancouver, Kanada dokumentumokat ahol van egy base-deluxe szoba értékelje 160-nál kisebb:

    Address/City eq 'Vancouver' and Address/Country eq 'Canada' and Rooms/any(room: room/Type eq 'Deluxe Room' and room/BaseRate lt 160)

## <a name="next-steps"></a>További lépések  

- [Szűrők az Azure Search szolgáltatásban](search-filters.md)
- [Az Azure Search OData kifejezés nyelvi – áttekintés](query-odata-filter-orderby-syntax.md)
- [Az Azure Search OData kifejezés szintaxisának referenciája](search-query-odata-syntax-reference.md)
- [Dokumentumok keresése &#40;az Azure Search szolgáltatás REST API-ja&#41;](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)
