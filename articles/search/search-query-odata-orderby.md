---
title: OData rendelés – hivatkozás alapján
titleSuffix: Azure Cognitive Search
description: Szintaxissal és nyelvi dokumentációval az Azure Cognitive Search-lekérdezésekben használt Order by használatával.
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
ms.sourcegitcommit: 598c5a280a002036b1a76aa6712f79d30110b98d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/15/2019
ms.locfileid: "74113150"
---
# <a name="odata-orderby-syntax-in-azure-cognitive-search"></a>OData $orderby szintaxis az Azure-ban Cognitive Search

 A [OData **$OrderBy** paraméterrel](query-odata-filter-orderby-syntax.md) egyéni rendezési sorrendet alkalmazhat az Azure Cognitive Search keresési eredményeire. Ez a cikk részletesen ismerteti **$OrderBy** szintaxisát. A keresési eredmények megjelenítésével kapcsolatos további általános információkért lásd: a keresési eredmények **$OrderBy** használata [Az Azure-ban Cognitive Search](search-pagination-page-layout.md).

## <a name="syntax"></a>Szintaxis

A **$OrderBy** paraméter legfeljebb 32 **ORDER-BY záradék**vesszővel tagolt listáját fogadja el. Az ORDER-BY záradék szintaxisát a következő EBNF ([bővített Naur-űrlap](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form)) írja le:

<!-- Upload this EBNF using https://bottlecaps.de/rr/ui to create a downloadable railroad diagram. -->

```
order_by_clause ::= (field_path | sortable_function) ('asc' | 'desc')?

sortable_function ::= geo_distance_call | 'search.score()'
```

Az interaktív szintaxis diagram is elérhető:

> [!div class="nextstepaction"]
> [Az Azure Cognitive Search OData szintaxisának diagramja](https://azuresearch.github.io/odata-syntax-diagram/#order_by_clause)

> [!NOTE]
> Tekintse meg az [Azure Cognitive Search OData-kifejezés szintaxisának referenciáját](search-query-odata-syntax-reference.md) a teljes EBNF.

Az egyes záradékok rendezési feltételekkel rendelkeznek, opcionálisan egy rendezési irány után (`asc` növekvő vagy `desc` csökkenő sorrendben). Ha nem ad meg irányt, az alapértelmezett érték a növekvő. A rendezési feltétel lehet egy `sortable` mező elérési útja, vagy a [`geo.distance`](search-query-odata-geo-spatial-functions.md) vagy a [`search.score`](search-query-odata-search-score-function.md) függvények hívása.

Ha több dokumentumra ugyanazok a rendezési feltételek tartoznak, és a `search.score` függvény nincs használatban (például ha egy numerikus `Rating` mező alapján rendezi, és a három dokumentum minősítése 4), a kapcsolatok a dokumentum pontszáma szerint csökkenő sorrendben lesznek megszakítva. Ha a dokumentumok pontszámai megegyeznek (például ha nincs megadva teljes szöveges keresési lekérdezés a kérelemben), akkor a kötött dokumentumok relatív sorrendje nem kötelező.

Több rendezési feltételt is megadhat. A kifejezések sorrendje határozza meg a végső rendezési sorrendet. Például a pontszámok csökkenő rendezéséhez, amelyet a minősítés követ, a szintaxis `$orderby=search.score() desc,Rating desc`.

A **$orderby** `geo.distance` szintaxisa ugyanaz, mint **$Filter**. A **$orderby**`geo.distance` használatakor a mezőnek `Edm.GeographyPoint` típusúnak kell lennie, és `sortable`is kell lennie.

A **$orderby** `search.score` szintaxisa `search.score()`. A függvény `search.score` nem végez paramétereket.

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

- [Keresési eredmények használata az Azure-ban Cognitive Search](search-pagination-page-layout.md)
- [Az Azure Cognitive Search OData kifejezés nyelvének áttekintése](query-odata-filter-orderby-syntax.md)
- [Az Azure Cognitive Search OData-kifejezési szintaxisának referenciája](search-query-odata-syntax-reference.md)
- [Dokumentumok &#40;keresése az Azure Cognitive Search REST API&#41;](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)
