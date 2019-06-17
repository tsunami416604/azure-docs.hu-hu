---
title: OData-rendezés referencia – Azure Search
description: OData-nyelvi dokumentáció az Azure Search-lekérdezéseket az order by szintaxist.
ms.date: 06/13/2019
services: search
ms.service: search
ms.topic: conceptual
author: Brjohnstmsft
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
ms.openlocfilehash: 1ced35dc73e6d596fbeda32590ab0b69df396c5c
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "67079755"
---
# <a name="odata-orderby-syntax-in-azure-search"></a>Az Azure Search OData $orderby-szintaxis

 Használhatja a [OData **$orderby** paraméter](query-odata-filter-orderby-syntax.md) a alkalmazni az Azure Search keresési eredmények egyéni rendezési sorrendet. Ez a cikk ismerteti a szintaxisa **$orderby** részletesen. Használatával kapcsolatos további általános információt **$orderby** keresési eredmények szerkesztésekor lásd [dolgozni a keresési eredmények az Azure Search](search-pagination-page-layout.md).

## <a name="syntax"></a>Szintaxis

A **$orderby** paraméter legfeljebb 32 vesszővel tagolt listáját fogadja el **order by záradékok**. Order by záradék szintaxisa a következő EBNF le ([kiterjesztett Backus-Naur űrlap](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form)):

<!-- Upload this EBNF using https://bottlecaps.de/rr/ui to create a downloadable railroad diagram. -->

```
order_by_clause ::= (field_path | sortable_function) ('asc' | 'desc')?

sortable_function ::= geo_distance_call | 'search.score()'
```

Egy interaktív szintaxisdiagramja is érhető el:

> [!div class="nextstepaction"]
> [Azure Search OData szintaxisdiagramja](https://azuresearch.github.io/odata-syntax-diagram/#order_by_clause)

> [!NOTE]
> Lásd: [OData kifejezés szintaxisának referenciája az Azure Search](search-query-odata-syntax-reference.md) a teljes EBNF számára.

Minden egyes záradék rendelkezik rendezési feltételek után a Rendezés iránya (`asc` értéket növekvő vagy `desc` értéket csökkenő). Ha nem ad meg egy irányát, az alapértelmezett növekvő. A rendezési feltételek lehet elérési útját egy `sortable` Pole nebo hívása célpontjának a [ `geo.distance` ](search-query-odata-geo-spatial-functions.md) vagy a [ `search.score` ](search-query-odata-search-score-function.md) funkciók.

Ha több dokumentumot rendezési ugyanezeket a feltételeket és a `search.score` függvény nem használható (például akkor, ha egy numerikus szerinti rendezés `Rating` mező és három dokumentumok összes rendelkezik egy minősítése 4), ties oszlanak dokumentum pontszám szerint csökkenő sorrendben. A dokumentum pontszámok azonosak (például, ha nincs megadva a kérelemben szereplő teljes szöveges keresési lekérdezés), majd a kapcsolt dokumentumok relatív sorrendjének esetén meghatározatlan.

Több rendezési feltételt is megadhat. Kifejezések sorrendjét a végső rendezési sorrend határozza meg. Például csökkenő pontszám, kiegészítve a minősítést, szerint rendezheti a szintaxis lenne `$orderby=search.score() desc,Rating desc`.

A szintaxist a `geo.distance` a **$orderby** van ugyanaz, mint az **$filter**. Használata esetén `geo.distance` a **$orderby**, amelyekre vonatkozik a mező típusúnak kell lennie `Edm.GeographyPoint` kell lennie, és `sortable`.

A szintaxist a `search.score` a **$orderby** van `search.score()`. A függvény `search.score` nem használ paramétereket.

## <a name="examples"></a>Példák

Alapdíj szerint növekvő rendezés hotels:

    $orderby=BaseRate asc

A "Hotels" minősítés, majd az alapdíj mellett szerint növekvő szerint csökkenő rendezés (ne feledje, hogy a növekvő sorrendben az alapértelmezett):

    $orderby=Rating desc,BaseRate

A "Hotels" minősítés, akkor az adott koordinátái a távolság szerint növekvő szerint csökkenő rendezési:

    $orderby=Rating desc,geo.distance(Location, geography'POINT(-122.131577 47.678581)') asc

Rendezés csökkenő sorrendben search.score és minősítése, majd a növekvő sorrendben távolság szerint az adott koordinátái a "Hotels". Két szállodák azonos relevancia alapján végzett pontszámokat és -minősítések, között a legközelebbire az első helyen szerepel:

    $orderby=search.score() desc,Rating desc,geo.distance(Location, geography'POINT(-122.131577 47.678581)') asc

## <a name="next-steps"></a>További lépések  

- [Hogyan használható a keresési eredmények az Azure Search szolgáltatásban](search-pagination-page-layout.md)
- [Az Azure Search OData kifejezés nyelvi – áttekintés](query-odata-filter-orderby-syntax.md)
- [Az Azure Search OData kifejezés szintaxisának referenciája](search-query-odata-syntax-reference.md)
- [Dokumentumok keresése &#40;az Azure Search szolgáltatás REST API-ja&#41;](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)
