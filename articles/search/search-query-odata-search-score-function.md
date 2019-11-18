---
title: OData keresés. score-függvény referenciája
titleSuffix: Azure Cognitive Search
description: Szintaxis és dokumentáció a Search. score függvény Azure Cognitive Search lekérdezésekben való használatához.
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
ms.openlocfilehash: 2439d4f03184f8dbb85b229b3908dff95013b4bc
ms.sourcegitcommit: 598c5a280a002036b1a76aa6712f79d30110b98d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/15/2019
ms.locfileid: "74113134"
---
# <a name="odata-searchscore-function-in-azure-cognitive-search"></a>OData `search.score` függvény az Azure-ban Cognitive Search

Ha a [ **$OrderBy** paraméter](search-query-odata-orderby.md)nélkül küld egy Azure Cognitive Search-lekérdezést, a visszaadott eredmények csökkenő sorrendben lesznek rendezve a relevancia pontszám szerint. Még ha a **$OrderBy**is használja, a rendszer alapértelmezés szerint a relevancia pontszámát fogja használni. Néha azonban hasznos lehet a relevancia pontszámának kezdeti rendezési feltételként való használata, valamint a döntetlen-megszakító más feltételeinek megadása. A `search.score` függvény ezt lehetővé teszi.

## <a name="syntax"></a>Szintaxis

A **$orderby** `search.score` szintaxisa `search.score()`. A `search.score` függvény nem végez paramétereket. A `asc` vagy `desc` rendezési megrendelést is használhatja, ugyanúgy, mint a **$OrderBy** paraméter bármely más záradéka. A rendezési feltételek listájában bárhol megjelenhet.

## <a name="example"></a>Példa

A szállodát csökkenő sorrendbe rendezheti `search.score` és `rating`szerint, majd növekvő sorrendben, a megadott koordináták távolsága alapján, így két, azonos minősítéssel rendelkező, egymással azonos minősítéssel rendelkező Hotel között, a legközelebb látható az első:

    search.score() desc,rating desc,geo.distance(location, geography'POINT(-122.131577 47.678581)') asc

## <a name="next-steps"></a>További lépések  

- [Az Azure Cognitive Search OData kifejezés nyelvének áttekintése](query-odata-filter-orderby-syntax.md)
- [Az Azure Cognitive Search OData-kifejezési szintaxisának referenciája](search-query-odata-syntax-reference.md)
- [Dokumentumok &#40;keresése az Azure Cognitive Search est API-val&#41;](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)
