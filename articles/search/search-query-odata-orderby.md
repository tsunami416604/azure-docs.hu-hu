---
title: OData rendelési hivatkozás
titleSuffix: Azure Cognitive Search
description: Szintaxis és nyelvi referencia dokumentáció az Azure Cognitive Search-lekérdezések rendelési szolgáltatásának kezeléséhez.
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
ms.openlocfilehash: 99ec639b88f3334530243242aadfa0ab52a40df0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74113150"
---
# <a name="odata-orderby-syntax-in-azure-cognitive-search"></a>Az OData $orderby szintaxist az Azure Cognitive Search szolgáltatásban

 Az [OData **$orderby** paraméter](query-odata-filter-orderby-syntax.md) rel egyéni rendezési sorrendet alkalmazhat a keresési eredményekhez az Azure Cognitive Search szolgáltatásban. Ez a cikk részletesen ismerteti **a $orderby** szintaxisát. A keresési eredmények **bemutatásakor a $orderby** használatáról a Keresési eredmények használata az Azure Cognitive Search alkalmazásban című témakörben olvashat [bővebben.](search-pagination-page-layout.md)

## <a name="syntax"></a>Szintaxis

A **$orderby** paraméter legfeljebb 32 **rendelési záradékot**tartalmazó, vesszővel tagolt listát fogad el . A rendelési záradék szintaxisát a következő EBNF[(Extended Backus-Naur Form):](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form)

<!-- Upload this EBNF using https://bottlecaps.de/rr/ui to create a downloadable railroad diagram. -->

```
order_by_clause ::= (field_path | sortable_function) ('asc' | 'desc')?

sortable_function ::= geo_distance_call | 'search.score()'
```

Interaktív szintaktikai diagram is elérhető:

> [!div class="nextstepaction"]
> [OData-szintaktikai diagram az Azure Cognitive Search szolgáltatáshoz](https://azuresearch.github.io/odata-syntax-diagram/#order_by_clause)

> [!NOTE]
> Lásd: [OData kifejezés szintaxis referencia az Azure Cognitive Search](search-query-odata-syntax-reference.md) a teljes EBNF.

Minden záradéknak van rendezési feltétele,`asc` amelyet egy `desc` rendezési irány követ (növekvő vagy csökkenő). Ha nem ad meg irányt, az alapértelmezett érték növekvő. A rendezési feltételek lehetnek `sortable` egy mező elérési útja, vagy a függvények [`geo.distance`](search-query-odata-geo-spatial-functions.md) vagy a függvények hívása. [`search.score`](search-query-odata-search-score-function.md)

Ha több dokumentum nak ugyanaz `search.score` a rendezési feltétele, és a függvény nincs `Rating` használva (például ha numerikus mező szerint rendez, és három dokumentum mindegyike 4-es minősítéssel rendelkezik), a kapcsolatok csökkenő sorrendben lesznek törve a dokumentum pontszáma szerint. Ha a dokumentum pontszámok azonosak (például, ha nincs teljes szöveges keresési lekérdezés a kérelemben megadva), akkor a csatolt dokumentumok relatív sorrendje meghatározatlan.

Több rendezési feltételt is megadhat. A kifejezések sorrendje határozza meg a végső rendezési sorrendet. Ha például a csökkenő pontszámot, majd a Minősítést `$orderby=search.score() desc,Rating desc`szeretné rendezni, a szintaxis a .

A **$orderby** `geo.distance` szintaxisa megegyezik a **$filter.** A `geo.distance` **$orderby**használata esetén annak a mezőnek, `Edm.GeographyPoint` amelyre vonatkozik, típusnak kell lennie, és annak is lennie `sortable`kell.

A **$orderby** `search.score` szintaxisa `search.score()`a . A `search.score` függvény nem vesz igénybe paramétereket.

## <a name="examples"></a>Példák

A szállodák rendezése alapdíj szerint:

    $orderby=BaseRate asc

Rendezze a szállodákat minősítés szerint, majd az alapdíj szerint növekvő (ne feledje, hogy a növekvő az alapértelmezett):

    $orderby=Rating desc,BaseRate

Rendezze a szállodákat minősítés szerint, majd a megadott koordinátáktól való távolság szerint:

    $orderby=Rating desc,geo.distance(Location, geography'POINT(-122.131577 47.678581)') asc

A hoteleket csökkenő sorrendben rendezheti a search.score és rating szerint, majd növekvő sorrendben az adott koordinátáktól való távolság szerint. Két azonos relevancia-pontszámmal és értékeléssel rendelkező szálloda között a legközelebbi szerepel először:

    $orderby=search.score() desc,Rating desc,geo.distance(Location, geography'POINT(-122.131577 47.678581)') asc

## <a name="next-steps"></a>További lépések  

- [A keresési eredmények működése az Azure Cognitive Search szolgáltatásban](search-pagination-page-layout.md)
- [Az Azure Cognitive Search OData-kifejezés nyelvének áttekintése](query-odata-filter-orderby-syntax.md)
- [Az Azure Cognitive Search OData-kifejezés szintaxisának hivatkozása](search-query-odata-syntax-reference.md)
- [Az Azure Cognitive Search REST API-&#41;&#40;dokumentumok keresése](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)
