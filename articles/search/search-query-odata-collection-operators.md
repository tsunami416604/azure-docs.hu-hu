---
title: OData-gyűjtemény operátorának hivatkozása
titleSuffix: Azure Cognitive Search
description: Amikor szűrőkifejezéseket hoz létre az Azure Cognitive Search-lekérdezésekben, használja az "any" és az "all" operátorokat lambda kifejezésekben, amikor a szűrő egy gyűjtemény vagy összetett gyűjteménymező.
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
ms.openlocfilehash: 54ddc8222816831b5b436297bbb1b40d03230f0c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74113230"
---
# <a name="odata-collection-operators-in-azure-cognitive-search---any-and-all"></a>Az Azure Cognitive Search oData-gyűjteményoperátorai – `any` és`all`

Az Azure Cognitive Search használatával használható [OData-szűrőkifejezés](query-odata-filter-orderby-syntax.md) írásakor gyakran hasznos a gyűjteménymezők szűrése. Ezt a és `any` `all` az operátorok segítségével érheti el.

## <a name="syntax"></a>Szintaxis

A következő EBNF ([Extended Backus-Naur Form](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form)) határozza meg `any` `all`a nyelvtant egy OData kifejezés, amely használja vagy .

<!-- Upload this EBNF using https://bottlecaps.de/rr/ui to create a downloadable railroad diagram. -->

```
collection_filter_expression ::=
    field_path'/all(' lambda_expression ')'
    | field_path'/any(' lambda_expression ')'
    | field_path'/any()'

lambda_expression ::= identifier ':' boolean_expression
```

Interaktív szintaktikai diagram is elérhető:

> [!div class="nextstepaction"]
> [OData-szintaktikai diagram az Azure Cognitive Search szolgáltatáshoz](https://azuresearch.github.io/odata-syntax-diagram/#collection_filter_expression)

> [!NOTE]
> Lásd: [OData kifejezés szintaxis referencia az Azure Cognitive Search](search-query-odata-syntax-reference.md) a teljes EBNF.

A kifejezéseknek három formája szűri a gyűjteményeket.

- Az első két iterate egy gyűjtemény területen, alkalmazása predikátum formájában adott lambda kifejezés minden eleme a gyűjtemény.
  - Egy kifejezés, amely `all` adja vissza, `true` ha a predikátum igaz a gyűjtemény minden eleme.
  - Egy olyan `any` `true` kifejezés, amely visszaadja, ha a predikátum a gyűjtemény legalább egy elemére igaz.
- A gyűjteményszűrő harmadik `any` formája lambda kifejezés nélkül ellenőrzi, hogy a gyűjteménymező üres-e. Ha a gyűjteménynek vannak `true`elemei, akkor visszaadja. Ha a gyűjtemény üres, `false`akkor visszatér .

A collection szűrőben lévő **lambda kifejezés** olyan, mint egy ciklus teste egy programozási nyelven. Ez határozza meg a változó, az úgynevezett **tartomány változó**, amely tartalmazza az aktuális eleme a gyűjtemény iteráció során. Azt is meghatározza egy másik logikai kifejezés, amely a szűrő feltételeket alkalmazni kell a tartomány változó minden eleme a gyűjtemény.

## <a name="examples"></a>Példák

Match dokumentumok, amelyek `tags` területén pontosan tartalmazza a string "wifi":

    tags/any(t: t eq 'wifi')

Olyan dokumentumok egyeztetése, `ratings` amelyeknél a mező minden eleme 3 és 5 közé esik, beleértve a következőket:

    ratings/all(r: r ge 3 and r le 5)

Olyan dokumentumok egyeztetése, amelyeknél `locations` a mezőben lévő földrajzi koordináták bármelyike az adott sokszögen belül van:

    locations/any(loc: geo.intersects(loc, geography'POLYGON((-122.031577 47.578581, -122.031577 47.678581, -122.131577 47.678581, -122.031577 47.578581))'))

Olyan dokumentumok `rooms` egyeztetése, ahol a mező üres:

    not rooms/any()

Match dokumentumok, ahol minden `rooms/amenities` szobában, a `rooms/baseRate` mező tartalmazza a "tv", és kevesebb, mint 100:

    rooms/all(room: room/amenities/any(a: a eq 'tv') and room/baseRate lt 100.0)

## <a name="limitations"></a>Korlátozások

A lambda kifejezések testében nem minden szűrőkifejezés érhető el. A korlátozások a szűrni kívánt gyűjteménymező adattípusától függően változnak. Az alábbi táblázat összefoglalja a korlátozásokat.

[!INCLUDE [Limitations on OData lambda expressions in Azure Cognitive Search](../../includes/search-query-odata-lambda-limitations.md)]

Ezekről a korlátozásokról és példákról további információt az [Azure Cognitive Search Gyűjteményszűrők hibaelhárítása című témakörében talál.](search-query-troubleshoot-collection-filters.md) A korlátozások létezésének okáról az [Azure Cognitive Search gyűjteményszűrőinek ismertetése](search-query-understand-collection-filters.md)című témakörben olvashat bővebben.

## <a name="next-steps"></a>További lépések  

- [Szűrők az Azure Cognitive Search szolgáltatásban](search-filters.md)
- [Az Azure Cognitive Search OData-kifejezés nyelvének áttekintése](query-odata-filter-orderby-syntax.md)
- [Az Azure Cognitive Search OData-kifejezés szintaxisának hivatkozása](search-query-odata-syntax-reference.md)
- [Az Azure Cognitive Search REST API-&#41;&#40;dokumentumok keresése](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)
