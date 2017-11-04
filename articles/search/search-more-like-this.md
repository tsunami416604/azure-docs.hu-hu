---
title: "az Azure Search (előzetes verzió) moreLikeThis |} Microsoft Docs"
description: "A moreLikeThis (előzetes verzió) szolgáltatás, az Azure Search REST API felfedett előzetes dokumentációjában talál."
services: search
documentationCenter: na
authors: mhko
manager: jlembicz
editor: na
ms.service: search
ms.devlang: rest-api
ms.workload: search
ms.topic: article
ms.tgt_pltfrm: na
ms.date: 10/27/2016
ms.author: nateko
ms.openlocfilehash: 20f6a1166d47799f15275cb5b6a9ea4934ae57e2
ms.sourcegitcommit: f8437edf5de144b40aed00af5c52a20e35d10ba1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/03/2017
---
# <a name="morelikethis-in-azure-search-preview"></a>az Azure Search (előzetes verzió) moreLikeThis

`moreLikeThis=[key]`a lekérdezési paraméter a [Search API](https://docs.microsoft.com/rest/api/searchservice/search-documents). Megadja a `moreLikeThis` keresési lekérdezés paramétere, dokumentumok, a dokumentum kulcs által megadott dokumentum hasonló találja. Ha a keresési kérelem rendelkező `moreLikeThis`, lekérdezés jön létre a keresési feltételeket az adott dokumentum kinyert dokumentum legjobban leíró. A létrehozott lekérdezést ezután a keresési kérés segítségével történik. Alapértelmezés szerint az összes `searchable` mezők minősülnek, kivéve, ha a `searchFields` paraméter használatával korlátozhatja a mezőket. A `moreLikeThis` paraméter nem használható a keresési paraméterrel `search=[string]`.

## <a name="examples"></a>Példák 

Az alábbiakban van például egy moreLikeThis lekérdezés. A lekérdezés talál dokumentumok, amelynek leírása mezők hasonlóak leginkább a forrásdokumentum által megadott mező a `moreLikeThis` paraméter.

```  
Get /indexes/hotels/docs?moreLikeThis=1002&searchFields=description&api-version=2016-09-01-Preview
```  

```  
POST /indexes/hotels/docs/search?api-version=2016-09-01-Preview
    {  
      "moreLikeThis": "1002",  
      "searchFields": "description"  
    }  
```  

## <a name="feature-availability"></a>Szolgáltatások rendelkezésre állása

A moreLikeThis funkció jelenleg előzetes verzióban érhetők, és csak akkor támogatott a minta api-verzióit, a `2015-02-28-Preview` és `2016-09-01-Preview`. Az API-verzió van megadva a kérés, mert is lehet kombinálni általánosan elérhető (GA), és ugyanahhoz az alkalmazáshoz az API-k előzetes. Azonban előnézeti API-k nincsenek az SLA-t és a szolgáltatások módosíthatja, ezért nem javasoljuk azok az éles környezetben.