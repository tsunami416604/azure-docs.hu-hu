---
title: OData-gyűjtemény operátor referencia – Azure Search
description: OData gyűjtemény operátorok, minden, és az Azure Search lekérdezések lambda-kifejezésekkel.
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
ms.openlocfilehash: 7afafe158732b14ebe314eeee5d015acddc55b72
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "67079937"
---
# <a name="odata-collection-operators-in-azure-search---any-and-all"></a>Az Azure Search - OData-gyűjtemény operátorok `any` és `all`

Írás esetén egy [OData szűrési kifejezés](query-odata-filter-orderby-syntax.md) szeretné használni az Azure Search szolgáltatással, gyakran hasznos mezők gyűjtemény szűrést. Ennek megvalósításával használatával a `any` és `all` operátorok.

## <a name="syntax"></a>Szintaxis

A következő EBNF ([kiterjesztett Backus-Naur űrlap](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form)) határozza meg, amely egy OData-kifejezésnek nyelvtani `any` vagy `all`.

<!-- Upload this EBNF using https://bottlecaps.de/rr/ui to create a downloadable railroad diagram. -->

```
collection_filter_expression ::=
    field_path'/all(' lambda_expression ')'
    | field_path'/any(' lambda_expression ')'
    | field_path'/any()'

lambda_expression ::= identifier ':' boolean_expression
```

Egy interaktív szintaxisdiagramja is érhető el:

> [!div class="nextstepaction"]
> [Azure Search OData szintaxisdiagramja](https://azuresearch.github.io/odata-syntax-diagram/#collection_filter_expression)

> [!NOTE]
> Lásd: [OData kifejezés szintaxisának referenciája az Azure Search](search-query-odata-syntax-reference.md) a teljes EBNF számára.

Nincsenek kifejezés három formáját gyűjtemények szűréséhez.

- Az első két ciklustevékenység egy gyűjtemény mezőt, a gyűjtemény összes eleme a lambda kifejezésnek formájában megadott predikátum alkalmazásával.
  - Egy kifejezés használatával `all` adja vissza `true` Ha a predikátum értéke igaz, a gyűjtemény minden eleméhez.
  - Egy kifejezés használatával `any` adja vissza `true` a predikátum legalább egy elem a gyűjtemény teljesülése esetén.
- A harmadik képernyő, gyűjtemény szűréséhez használt `any` nélkül lambda kifejezésnek annak megállapítására, hogy egy gyűjtemény mező értéke üres. Ha a gyűjtemény tartalmaz elemeket, akkor adja vissza `true`. Ha a gyűjtemény, az üres visszatérési `false`.

A **lambda kifejezésnek** egy gyűjtemény szűrő olyan, mint a programnyelvek a hurok törzsébe. Azt határozza meg egy változót nevű a **tartomány változót**, amely tárolja a gyűjtemény iteráció során aktuálního prvku. Egy másik logikai kifejezés, amely a szűrési feltételeket a alkalmazni a gyűjtemény összes eleme a tartomány változót is meghatározza.

## <a name="examples"></a>Példák

Egyezés azon dokumentumok `tags` mezőben pontosan a "Wi-Fi" karakterláncot tartalmazza:

    tags/any(t: t eq 'wifi')

Egyezés dokumentumok where minden eleme a `ratings` 3 és 5 között lehet közé esik mező:

    ratings/all(r: r ge 3 and r le 5)

Ahol a földrajzi bármelyikét koordinálja az egyezés dokumentumok a `locations` belül az adott sokszög mező:

    locations/any(loc: geo.intersects(loc, geography'POLYGON((-122.031577 47.578581, -122.031577 47.678581, -122.131577 47.678581, -122.031577 47.578581))'))

Egyezés dokumentálja a helyét a `rooms` mező értéke üres:

    not rooms/any()

Dokumentumok megfelelő, ha az összes termek a `rooms/amenities` mező tartalmazza a "tv" és a `rooms/baseRate` 100-nál kisebb van:

    rooms/all(room: room/amenities/any(a: a eq 'tv') and room/baseRate lt 100.0)

## <a name="limitations"></a>Korlátozások

Szűrési kifejezésekben nem minden funkciója nem érhető el, a törzsben lambda kifejezés. A korlátozások a gyűjtemény mezőt, amely a szűrni kívánt adatok típusától függően eltérőek. A következő táblázat összefoglalja a korlátozások.

[!INCLUDE [Limitations on OData lambda expressions in Azure Search](../../includes/search-query-odata-lambda-limitations.md)]

Ezeket a korlátozásokat, valamint a példák a további részletekért lásd: [hibáinak elhárítása az Azure Search szolgáltatásban a fájlgyűjtési szűrők](search-query-troubleshoot-collection-filters.md). További információt arról, hogy miért ezek a korlátozások létezik, lásd: [ismertetése az Azure Search szolgáltatásban a fájlgyűjtési szűrők](search-query-understand-collection-filters.md).

## <a name="next-steps"></a>További lépések  

- [Szűrők az Azure Search szolgáltatásban](search-filters.md)
- [Az Azure Search OData kifejezés nyelvi – áttekintés](query-odata-filter-orderby-syntax.md)
- [Az Azure Search OData kifejezés szintaxisának referenciája](search-query-odata-syntax-reference.md)
- [Dokumentumok keresése &#40;az Azure Search szolgáltatás REST API-ja&#41;](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)
