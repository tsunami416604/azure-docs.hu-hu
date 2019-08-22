---
title: OData sorrend – hivatkozás alapján – Azure Search
description: OData nyelvi referenciája Azure Search lekérdezésekben a sorrend szintaxisa.
ms.date: 06/13/2019
services: search
ms.service: search
ms.topic: conceptual
author: Brjohnstmsft
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
ms.openlocfilehash: 8ee44549931100a1affa5e2bb9e5cda904c05ed1
ms.sourcegitcommit: bb8e9f22db4b6f848c7db0ebdfc10e547779cccc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/20/2019
ms.locfileid: "69647538"
---
# <a name="odata-orderby-syntax-in-azure-search"></a>OData $orderby szintaxis Azure Search

 A [OData **$OrderBy** paraméterrel](query-odata-filter-orderby-syntax.md) egyéni rendezési sorrendet alkalmazhat a keresési eredményekhez Azure Searchban. Ez a cikk részletesen ismerteti **$OrderBy** szintaxisát. A keresési eredmények megjelenítésével kapcsolatos további általános információkért lásd: a [keresési eredmények](search-pagination-page-layout.md) **$orderby** használata a Azure Searchban.

## <a name="syntax"></a>Szintaxis

A **$OrderBy** paraméter legfeljebb 32 **ORDER-BY záradék**vesszővel tagolt listáját fogadja el. Az ORDER-BY záradék szintaxisát a következő EBNF ([bővített Naur-űrlap](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form)) írja le:

<!-- Upload this EBNF using https://bottlecaps.de/rr/ui to create a downloadable railroad diagram. -->

```
order_by_clause ::= (field_path | sortable_function) ('asc' | 'desc')?

sortable_function ::= geo_distance_call | 'search.score()'
```

Az interaktív szintaxis diagram is elérhető:

> [!div class="nextstepaction"]
> [Azure Search OData szintaxisának diagramja](https://azuresearch.github.io/odata-syntax-diagram/#order_by_clause)

> [!NOTE]
> A teljes EBNF [Azure Search a OData kifejezés szintaxisát](search-query-odata-syntax-reference.md) ismertető témakörben talál.

Az egyes záradékok rendezési feltételekkel rendelkeznek, opcionálisan egy rendezési irány után`asc` ( `desc` növekvő vagy csökkenő sorrendben). Ha nem ad meg irányt, az alapértelmezett érték a növekvő. A rendezési feltétel lehet egy `sortable` mező elérési útja vagy a [`geo.distance`](search-query-odata-geo-spatial-functions.md) vagy a [`search.score`](search-query-odata-search-score-function.md) függvények egyik hívása is.

Ha több dokumentumra ugyanazok a rendezési feltételek tartoznak `search.score` , és a függvény nincs használatban (például ha egy numerikus `Rating` mező alapján rendezi a sort, és a három dokumentum minősítése 4), a kapcsolatok a dokumentum pontszáma szerint csökkenő sorrendben lesznek megszakítva. Ha a dokumentumok pontszámai megegyeznek (például ha nincs megadva teljes szöveges keresési lekérdezés a kérelemben), akkor a kötött dokumentumok relatív sorrendje nem kötelező.

Több rendezési feltételt is megadhat. A kifejezések sorrendje határozza meg a végső rendezési sorrendet. Például a pontszám csökkenő rendezéséhez, amelyet a minősítés követ, a szintaxis `$orderby=search.score() desc,Rating desc`a következő:.

A `geo.distance` **$OrderBy** szintaxisa megegyezik a **$Filter**. `geo.distance` **$OrderBy**használatakor a mezőnek, amelyre az vonatkozik, típusnak `Edm.GeographyPoint` kell lennie, és azt is `sortable`tartalmaznia kell.

A `search.score` **$OrderBy** szintaxisaakövetkező:.`search.score()` A függvény `search.score` nem végez paramétereket.

## <a name="examples"></a>Példák

A hotelek növekvő sorrendbe állítása alapdíj alapján:

    $orderby=BaseRate asc

Rendezheti a szállodákat a minősítés alapján csökkenő sorrendben, majd az alaparány szerint növekvő értéket (ne feledje, hogy az emelkedő az alapértelmezett):

    $orderby=Rating desc,BaseRate

Rendezheti a szállodákat a minősítés alapján csökkenő sorrendben, majd a megadott koordináták távolsága alapján:

    $orderby=Rating desc,geo.distance(Location, geography'POINT(-122.131577 47.678581)') asc

A szállodákat csökkenő sorrendbe rendezheti kereséssel. pontszám és értékelés, majd növekvő sorrendben, a megadott koordináták távolsága alapján. A legközelebb álló pontszámok és minősítések két, egymással azonos vonatkozású, a legközelebb található:

    $orderby=search.score() desc,Rating desc,geo.distance(Location, geography'POINT(-122.131577 47.678581)') asc

## <a name="next-steps"></a>További lépések  

- [Keresési eredmények használata Azure Search](search-pagination-page-layout.md)
- [A OData kifejezés nyelvének áttekintése Azure Search](query-odata-filter-orderby-syntax.md)
- [Azure Search OData-kifejezés szintaxisának referenciája](search-query-odata-syntax-reference.md)
- [Dokumentumok &#40;keresése Azure Search szolgáltatás REST API&#41;](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)
