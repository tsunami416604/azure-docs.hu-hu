---
title: A OData-gyűjtemény operátorának referenciája – Azure Search
description: A OData-gyűjtemény operátorai, a Azure Search-lekérdezések bármely és összes és lambda kifejezése.
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
ms.openlocfilehash: e057d0b57162d10aab13d8b1f77e0eaddca2ec2a
ms.sourcegitcommit: bb8e9f22db4b6f848c7db0ebdfc10e547779cccc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/20/2019
ms.locfileid: "69647635"
---
# <a name="odata-collection-operators-in-azure-search---any-and-all"></a>OData-gyűjtési operátorok `any` a Azure Search-ben és`all`

Ha egy [OData-szűrési kifejezést](query-odata-filter-orderby-syntax.md) ír a Azure Search használatával, gyakran hasznos a begyűjtési mezők szűrése. Ezt a és `any` `all` a operátorok használatával érheti el.

## <a name="syntax"></a>Szintaxis

A következő EBNF ([bővített Naur-űrlap](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form)) a vagy a által használt `any` OData- `all`kifejezés nyelvtanát határozza meg.

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
> [Azure Search OData szintaxisának diagramja](https://azuresearch.github.io/odata-syntax-diagram/#collection_filter_expression)

> [!NOTE]
> A teljes EBNF [Azure Search a OData kifejezés szintaxisát](search-query-odata-syntax-reference.md) ismertető témakörben talál.

A gyűjtemények három formája létezik.

- Az első két iteráció egy gyűjtemény mező fölé, amely egy lambda kifejezés formájában megadott predikátumot alkalmaz a gyűjtemény minden elemére.
  - A visszatérést `all` `true` használó kifejezés, ha a predikátum a gyűjtemény minden eleme esetében igaz.
  - A visszatérést `any` `true` használó kifejezés, ha a predikátum a gyűjtemény legalább egy eleménél igaz.
- A gyűjtési szűrő harmadik formája lambda `any` kifejezés nélkül működik annak teszteléséhez, hogy egy gyűjtemény mező üres-e. Ha a gyűjteménynek bármilyen eleme van, a `true`függvény visszaadja. Ha a gyűjtemény üres, a függvény visszaadja `false`.

A gyűjtemény szűrőben lévő **lambda kifejezés** olyan, mint egy programozási nyelvben lévő hurok törzse. Definiál egy változót, amelynek neve a **Range változó**, amely a gyűjtemény aktuális elemét tárolja az iteráció során. Egy másik logikai kifejezést is definiál, amely a gyűjtemény egyes elemeinél a tartomány változóra alkalmazandó szűrési feltétel.

## <a name="examples"></a>Példák

Olyan dokumentumok egyeztetése, amelyek `tags` mezője pontosan a "WiFi" karakterláncot tartalmazza:

    tags/any(t: t eq 'wifi')

Olyan dokumentumok egyeztetése, amelyekben `ratings` a mező minden eleme 3 és 5 közé esik, beleértve a következőket:

    ratings/all(r: r ge 3 and r le 5)

Olyan dokumentumok egyeztetése, amelyekben a `locations` mező egyik földrajzi koordinátái a megadott sokszögen belül vannak:

    locations/any(loc: geo.intersects(loc, geography'POLYGON((-122.031577 47.578581, -122.031577 47.678581, -122.131577 47.678581, -122.031577 47.578581))'))

Olyan dokumentumok egyeztetése `rooms` , amelyekben a mező üres:

    not rooms/any()

A dokumentumok egyeztetése az összes szobában, `rooms/amenities` a mező a "TV" `rooms/baseRate` kifejezést tartalmazza, és kevesebb, mint 100:

    rooms/all(room: room/amenities/any(a: a eq 'tv') and room/baseRate lt 100.0)

## <a name="limitations"></a>Korlátozások

A szűrési kifejezések nem minden funkciója érhető el egy lambda kifejezés törzsében. A korlátozások a szűrni kívánt gyűjtési mező adattípusától függően eltérőek. Az alábbi táblázat a korlátozásokat foglalja össze.

[!INCLUDE [Limitations on OData lambda expressions in Azure Search](../../includes/search-query-odata-lambda-limitations.md)]

További információ ezekről a korlátozásokról és példákról: [gyűjtési szűrők hibaelhárítása Azure Searchban](search-query-troubleshoot-collection-filters.md). További részletes információk a korlátozások létezéséről: a [gyűjtemény szűrőinek ismertetése a Azure Searchban](search-query-understand-collection-filters.md).

## <a name="next-steps"></a>További lépések  

- [Szűrők a Azure Searchban](search-filters.md)
- [A OData kifejezés nyelvének áttekintése Azure Search](query-odata-filter-orderby-syntax.md)
- [Azure Search OData-kifejezés szintaxisának referenciája](search-query-odata-syntax-reference.md)
- [Dokumentumok &#40;keresése Azure Search szolgáltatás REST API&#41;](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)
