---
title: OData keresés. score-függvény referenciája
titleSuffix: Azure Cognitive Search
description: OData Search. score függvény az Azure Cognitive Search lekérdezésekben.
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
ms.openlocfilehash: 500ac4f3a44d54e367ddc4ee5efc9514d603cab6
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/23/2019
ms.locfileid: "72793275"
---
# <a name="odata-searchscore-function-in-azure-cognitive-search"></a>OData `search.score` függvény az Azure-ban Cognitive Search

Ha a [ **$OrderBy** paraméter](search-query-odata-orderby.md)nélkül küld egy Azure Cognitive Search-lekérdezést, a visszaadott eredmények csökkenő sorrendben lesznek rendezve a relevancia pontszám szerint. Még ha a **$OrderBy**is használja, a rendszer alapértelmezés szerint a relevancia pontszámát fogja használni. Néha azonban hasznos lehet a relevancia pontszámának kezdeti rendezési feltételként való használata, valamint a döntetlen-megszakító más feltételeinek megadása. A `search.score` függvény ezt lehetővé teszi.

## <a name="syntax"></a>Szintaxis

A **$orderby** `search.score` szintaxisa `search.score()`. A `search.score` függvény nem végez paramétereket. A `asc` vagy `desc` rendezési megrendelést is használhatja, ugyanúgy, mint a **$OrderBy** paraméter bármely más záradéka. A rendezési feltételek listájában bárhol megjelenhet.

## <a name="example"></a>Példa

A szállodát csökkenő sorrendbe rendezheti `search.score` és `rating`szerint, majd növekvő sorrendben, a megadott koordináták távolsága alapján, így két, azonos minősítéssel rendelkező, egymással azonos minősítéssel rendelkező Hotel között, a legközelebb látható az első:

    search.score() desc,rating desc,geo.distance(location, geography'POINT(-122.131577 47.678581)') asc

## <a name="next-steps"></a>Következő lépések  

- [Az Azure Cognitive Search OData kifejezés nyelvének áttekintése](query-odata-filter-orderby-syntax.md)
- [Az Azure Cognitive Search OData-kifejezési szintaxisának referenciája](search-query-odata-syntax-reference.md)
- [Dokumentumok &#40;keresése az Azure Cognitive Search est API-val&#41;](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)
