---
title: az Azure Search (előzetes verzió) – Azure Search moreLikeThis
description: A moreLikeThis (előzetes verzió) szolgáltatáshoz, az Azure Search REST API-ban előzetes dokumentációjában talál.
author: brjohnstmsft
manager: jlembicz
services: search
ms.service: search
ms.devlang: rest-api
ms.topic: conceptual
ms.date: 10/27/2016
ms.author: brjohnst
ms.custom: seodec2018
ms.openlocfilehash: 1b91fbbcc025456b48ac8fcfcb3f286ede893541
ms.sourcegitcommit: eb9dd01614b8e95ebc06139c72fa563b25dc6d13
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/12/2018
ms.locfileid: "53314499"
---
# <a name="morelikethis-in-azure-search-preview"></a>moreLikeThis az Azure Search (előzetes verzió)

`moreLikeThis=[key]` a lekérdezési paraméter a [Search API](https://docs.microsoft.com/rest/api/searchservice/search-documents). Adja meg a `moreLikeThis` paramétert a keresési lekérdezésben, dokumentumok, a dokumentum a dokumentumkulcsot által megadott hasonló találja. Ha egy keresési kérelmet a `moreLikeThis`, egy lekérdezés jön létre keresési feltételeket az adott dokumentum kinyert legjobban leíró dokumentum. A létrehozott lekérdezést a keresés kérés majd szolgál. Alapértelmezésben az összes `searchable` mezők számítanak, kivéve, ha a `searchFields` paraméter használható mezők korlátozása. A `moreLikeThis` paraméter nem használható a keresési paraméterrel, `search=[string]`.

## <a name="examples"></a>Példák 

Az alábbi, például egy moreLikeThis lekérdezés. A lekérdezés talál, amelynek leírás mezők leginkább hasonlítanak, a mező a forrás-dokumentum által megadott a dokumentumok a `moreLikeThis` paraméter.

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

A moreLikeThis funkció jelenleg előzetes verzióban és csak az előzetes api-verziók, a támogatott `2015-02-28-Preview` és `2016-09-01-Preview`. Mivel az API-verzió van megadva a kérelem, kombinálhatja általánosan elérhető (GA), és előzetes verziójú API-k ugyanabban az alkalmazásban lehetőség. Azonban API-k nem az SLA-t és a szolgáltatások előzetes változhatnak, ezért nem javasoljuk az éles alkalmazásokban használja őket.