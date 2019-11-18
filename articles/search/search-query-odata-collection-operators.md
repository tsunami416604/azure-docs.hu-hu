---
title: OData-gyűjtemény kezelői referenciája
titleSuffix: Azure Cognitive Search
description: Ha szűrési kifejezéseket hoz létre az Azure Cognitive Search lekérdezésekben, használja a lambda kifejezések "any" és "összes" operátorát, ha a szűrő gyűjtemény vagy összetett gyűjtemény mezőn található.
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
ms.sourcegitcommit: 598c5a280a002036b1a76aa6712f79d30110b98d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/15/2019
ms.locfileid: "74113230"
---
# <a name="odata-collection-operators-in-azure-cognitive-search---any-and-all"></a>OData-gyűjtemény operátorok az Azure Cognitive Search-`any` és `all`

Ha egy [OData-szűrési kifejezést](query-odata-filter-orderby-syntax.md) ír az Azure Cognitive Search használatával, gyakran hasznos a begyűjtési mezők szűrése. Ezt a `any` és `all` operátorok segítségével érheti el.

## <a name="syntax"></a>Szintaxis

A következő EBNF ([bővített Naur-űrlap](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form)) egy olyan OData kifejezés nyelvtanát határozza meg, amely `any` vagy `all`t használ.

<!-- Upload this EBNF using https://bottlecaps.de/rr/ui to create a downloadable railroad diagram. -->

```
collection_filter_expression ::=
    field_path'/all(' lambda_expression ')'
    | field_path'/any(' lambda_expression ')'
    | field_path'/any()'

lambda_expression ::= identifier ':' boolean_expression
```

Az interaktív szintaxis diagram is elérhető:

> [!div class="nextstepaction"]
> [Az Azure Cognitive Search OData szintaxisának diagramja](https://azuresearch.github.io/odata-syntax-diagram/#collection_filter_expression)

> [!NOTE]
> Tekintse meg az [Azure Cognitive Search OData-kifejezés szintaxisának referenciáját](search-query-odata-syntax-reference.md) a teljes EBNF.

A gyűjtemények három formája létezik.

- Az első két iteráció egy gyűjtemény mező fölé, amely egy lambda kifejezés formájában megadott predikátumot alkalmaz a gyűjtemény minden elemére.
  - `all`t használó kifejezés `true`, ha a predikátum a gyűjtemény minden eleméhez igaz.
  - `any` értéket használó kifejezés `true`, ha a predikátum a gyűjtemény legalább egy eleménél igaz.
- A gyűjtési szűrő harmadik formája `any`t használ lambda kifejezés nélkül annak teszteléséhez, hogy egy gyűjtemény mező üres-e. Ha a gyűjteménynek bármilyen eleme van, a `true`adja vissza. Ha a gyűjtemény üres, akkor a `false`értéket adja vissza.

A gyűjtemény szűrőben lévő **lambda kifejezés** olyan, mint egy programozási nyelvben lévő hurok törzse. Definiál egy változót, amelynek neve a **Range változó**, amely a gyűjtemény aktuális elemét tárolja az iteráció során. Egy másik logikai kifejezést is definiál, amely a gyűjtemény egyes elemeinél a tartomány változóra alkalmazandó szűrési feltétel.

## <a name="examples"></a>Példák

Olyan dokumentumok egyeztetése, amelyek `tags` mező pontosan a "WiFi" karakterláncot tartalmazza:

    tags/any(t: t eq 'wifi')

Olyan dokumentumok egyeztetése, amelyekben a `ratings` mező minden eleme 3 és 5 közé esik, beleértve a következőket:

    ratings/all(r: r ge 3 and r le 5)

Azon dokumentumok egyeztetése, amelyekben a `locations` mezőben lévő földrajzi koordináták bármelyike a megadott sokszögen belül van:

    locations/any(loc: geo.intersects(loc, geography'POLYGON((-122.031577 47.578581, -122.031577 47.678581, -122.131577 47.678581, -122.031577 47.578581))'))

Olyan dokumentumok egyeztetése, amelyekben a `rooms` mező üres:

    not rooms/any()

A dokumentumok egyeztetése, ahol az összes szoba esetében a `rooms/amenities` mező a "TV"-t tartalmazza, a `rooms/baseRate` pedig kevesebb, mint 100:

    rooms/all(room: room/amenities/any(a: a eq 'tv') and room/baseRate lt 100.0)

## <a name="limitations"></a>Korlátozások

A szűrési kifejezések nem minden funkciója érhető el egy lambda kifejezés törzsében. A korlátozások a szűrni kívánt gyűjtési mező adattípusától függően eltérőek. Az alábbi táblázat a korlátozásokat foglalja össze.

[!INCLUDE [Limitations on OData lambda expressions in Azure Cognitive Search](../../includes/search-query-odata-lambda-limitations.md)]

További információ ezekről a korlátozásokról és példákról: [gyűjtési szűrők hibaelhárítása Az Azure Cognitive Searchban](search-query-troubleshoot-collection-filters.md). További részletes információk a korlátozások létezéséről: a [gyűjtemény szűrőinek ismertetése az Azure Cognitive Searchban](search-query-understand-collection-filters.md).

## <a name="next-steps"></a>További lépések  

- [Szűrők az Azure Cognitive Search](search-filters.md)
- [Az Azure Cognitive Search OData kifejezés nyelvének áttekintése](query-odata-filter-orderby-syntax.md)
- [Az Azure Cognitive Search OData-kifejezési szintaxisának referenciája](search-query-odata-syntax-reference.md)
- [Dokumentumok &#40;keresése az Azure Cognitive Search REST API&#41;](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)
