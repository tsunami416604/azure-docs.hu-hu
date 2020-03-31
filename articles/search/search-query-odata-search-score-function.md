---
title: OData search.score függvény hivatkozás
titleSuffix: Azure Cognitive Search
description: Szintaxis és referencia dokumentáció a search.score függvény azure cognitive Search lekérdezések használatával.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74113134"
---
# <a name="odata-searchscore-function-in-azure-cognitive-search"></a>OData-függvény `search.score` az Azure Cognitive Search szolgáltatásban

Amikor egy lekérdezést küld az Azure Cognitive Search anélkül, hogy a [ **$orderby** paraméter,](search-query-odata-orderby.md)a visszaküldött eredmények lesznek csökkenő sorrendben relevancia-pontszám szerint lesz rendezve. Még akkor is, ha nem használja **$orderby**, a relevancia pontszám fogja használni, hogy megtörje kapcsolatok alapértelmezés szerint. Néha azonban hasznos a relevancia-pontszám kezdeti rendezési feltételként való használata, és néhány más feltétel a megszakító. A `search.score` funkció lehetővé teszi, hogy ezt.

## <a name="syntax"></a>Szintaxis

A **$orderby** `search.score` szintaxisa `search.score()`a . A `search.score` függvény nem vesz igénybe semmilyen paramétert. Használható a `asc` vagy `desc` rendezési sorrend meganykal, csakúgy, mint bármely más záradék a **$orderby** paraméterben. A rendezési feltételek listájában bárhol megjelenhet.

## <a name="example"></a>Példa

A szállodákat csökkenő `search.score` sorrendben rendezheti a szerint és `rating`a , majd növekvő sorrendben a megadott koordinátáktól való távolság szerint, hogy két azonos minősítésű szálloda között a legközelebbi legyen az első:

    search.score() desc,rating desc,geo.distance(location, geography'POINT(-122.131577 47.678581)') asc

## <a name="next-steps"></a>További lépések  

- [Az Azure Cognitive Search OData-kifejezés nyelvének áttekintése](query-odata-filter-orderby-syntax.md)
- [Az Azure Cognitive Search OData-kifejezés szintaxisának hivatkozása](search-query-odata-syntax-reference.md)
- [Keresési dokumentumok &#40;Azure Cognitive Search EST API&#41;](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)
