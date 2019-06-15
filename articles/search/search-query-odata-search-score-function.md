---
title: OData search.score függvényhivatkozás – Azure Search
description: OData-search.score függvény az Azure Search-lekérdezéseket.
ms.date: 06/13/2019
services: search
ms.service: search
ms.topic: conceptual
author: brjohnstmsft
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
ms.openlocfilehash: dc444216c4677b9970b867e92aa5ae259a197220
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "67079690"
---
# <a name="odata-searchscore-function-in-azure-search"></a>OData `search.score` függvényt az Azure Search szolgáltatásban

Ha egy lekérdezést küld az Azure Search anélkül a [ **$orderby** paraméter](search-query-odata-orderby.md), térjen vissza az eredményeket relevanciapontszám szerint csökkenő sorrendben lesz rendezve. Még ha használ **$orderby**, alapértelmezés szerint a relevanciapontszám lesz használható ties érvényteleníteni. Azonban néha hasznos egy kezdeti rendezési feltételek, valamint más feltétel alapján a relevanciapontszám tie-megszakító adatokként. A `search.score` funkció lehetővé teszi, hogy ez.

## <a name="syntax"></a>Szintaxis

A szintaxist a `search.score` a **$orderby** van `search.score()`. A függvény `search.score` nem használ paramétereket. A használat a `asc` vagy `desc` rendezési sorrend megadása, csakúgy, mint bármely más záradékot a **$orderby** paraméter. Bárhol megjelenhet a rendezési feltételek listáját.

## <a name="example"></a>Példa

Rendezés csökkenő sorrendben a szállodák `search.score` és `rating`, majd a növekvő sorrendben távolság, a megadott koordináták, így között két hotels azonos besorolású, a legközelebbire első helyen szerepel:

    search.score() desc,rating desc,geo.distance(location, geography'POINT(-122.131577 47.678581)') asc

## <a name="next-steps"></a>További lépések  

- [Az Azure Search OData kifejezés nyelvi – áttekintés](query-odata-filter-orderby-syntax.md)
- [Az Azure Search OData kifejezés szintaxisának referenciája](search-query-odata-syntax-reference.md)
- [Dokumentumok keresése &#40;az Azure Search szolgáltatás REST API-ja&#41;](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)
