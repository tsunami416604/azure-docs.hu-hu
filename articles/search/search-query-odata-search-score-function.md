---
title: OData keresés. pontszám függvény hivatkozása – Azure Search
description: OData Search. score függvény Azure Search lekérdezésekben.
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
ms.openlocfilehash: b6bf56a61ca685b306a15e474623336216ba531b
ms.sourcegitcommit: bb8e9f22db4b6f848c7db0ebdfc10e547779cccc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/20/2019
ms.locfileid: "69647518"
---
# <a name="odata-searchscore-function-in-azure-search"></a>OData `search.score` függvény Azure Search

Ha a [ **$OrderBy** paraméter](search-query-odata-orderby.md)nélkül küld egy Azure Search lekérdezést, a visszatérési eredmények csökkenő sorrendben lesznek rendezve a relevancia pontszám szerint. Még ha a **$OrderBy**is használja, a rendszer alapértelmezés szerint a relevancia pontszámát fogja használni. Néha azonban hasznos lehet a relevancia pontszámának kezdeti rendezési feltételként való használata, valamint a döntetlen-megszakító más feltételeinek megadása. A `search.score` függvény lehetővé teszi ezt.

## <a name="syntax"></a>Szintaxis

A `search.score` **$OrderBy** szintaxisaakövetkező:.`search.score()` A függvény `search.score` nem végez paramétereket. A (z) vagy `asc` `desc` a rendezési sorrend megadásával is használható, ugyanúgy, mint a **$OrderBy** paraméter bármely más záradéka. A rendezési feltételek listájában bárhol megjelenhet.

## <a name="example"></a>Példa

Rendezheti a szállodákat csökkenő sorrendben `search.score` , `rating`a és a alapján, majd növekvő sorrendben, a megadott koordináták távolsága alapján, hogy két, azonos minősítéssel rendelkező, egymással azonos minősítéssel rendelkező Hotel között szerepeljenek a legközelebb:

    search.score() desc,rating desc,geo.distance(location, geography'POINT(-122.131577 47.678581)') asc

## <a name="next-steps"></a>További lépések  

- [A OData kifejezés nyelvének áttekintése Azure Search](query-odata-filter-orderby-syntax.md)
- [Azure Search OData-kifejezés szintaxisának referenciája](search-query-odata-syntax-reference.md)
- [Dokumentumok &#40;keresése Azure Search szolgáltatás REST API&#41;](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)
