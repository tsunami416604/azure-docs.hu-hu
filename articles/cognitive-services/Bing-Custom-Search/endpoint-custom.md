---
title: Bing Custom Search-végpont
titleSuffix: Azure Cognitive Services
description: Az Bing Custom Search API végpont összefoglalása.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-custom-search
ms.topic: conceptual
ms.date: 03/04/2019
ms.author: aahi
ms.openlocfilehash: e3b544d8d655b653383cc999de233e2163433d85
ms.sourcegitcommit: 9dc7517db9c5817a3acd52d789547f2e3efff848
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/23/2019
ms.locfileid: "68405075"
---
# <a name="custom-search"></a>Egyéni keresés
A Bing Custom Search szolgáltatással személyre szabott keresési funkciókat hozhat létre olyan témakörökhöz kapcsolódóan, amelyek jelentőséggel bírnak az Ön számára. A felhasználók az érdeklődési körükhöz igazított keresési eredményeket látnak ahelyett, hogy több oldalnyi irreleváns találatot kellene átnézniük.

## <a name="custom-search-endpoint"></a>Egyéni keresési végpont
Ha a Bing Custom Search API használatával szeretne eredményeket kapni, küldjön `GET` egy kérelmet a következő végpontnak. A fejlécek és az URL-paraméterek használatával további specifikációkat határozhat meg.

Végpont A keresési javaslatokat adja vissza JSON-eredményekként, amelyek az által definiált felhasználó `?q=""`által megadott bemenetre vonatkoznak.
```  
 GET https://api.cognitive.microsoft.com/bingcustomsearch/v7.0/search  
```

Az egyéni keresési források beállítását ismertető példákat az oktatóanyagban talál. [](https://docs.microsoft.com/azure/cognitive-services/bing-custom-search/tutorials/custom-search-web-page) A fejlécekről, paraméterekről, piaci kódokról, válasz-objektumokról, hibákról és egyéb adatokról a [Bing Custom Search API v7](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-custom-search-api-v7-reference) -dokumentációban talál további információt.

## <a name="custom-search-response-json"></a>Egyéni keresési válasz JSON
Az egyéni keresési kérelem JSON-objektumokként adja vissza az eredményeket, lásd: [Response Objects](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-custom-search-api-v7-reference#response-objects). 

## <a name="custom-autosuggest"></a>Custom Autosuggest
Az egyéni automatikus kiegészítés API lehetővé teszi, hogy részleges keresési lekérdezési kifejezést küldjön a Bingnek, és lekérje a konfigurálható javasolt lekérdezések listáját. Az egyéni automatikus kiegészítéssel az API által visszaadott javaslatokat adhat hozzá, és opcionálisan megadhatja, hogy a Bing milyen javaslatokat tartalmazzon.

## <a name="custom-autosuggest-endpoint"></a>Egyéni automatikus javaslat végpontja
Ha egyéni lekérdezési javaslatokat szeretne kérni, küldjön egy GET kérelmet a következőre:

```
https://api.cognitive.microsoft.com/bingcustomsearch/v7.0/Suggestions
```  

További információ az egyéni javaslatok definiálásáról: [egyéni keresési javaslatok definiálása](define-custom-suggestions.md).

## <a name="custom-image-search"></a>Egyéni Image Search
Az egyéni Image Search API lehetővé teszi, hogy keresési lekérdezést küldjön a Bingnek, és lekérje az egyéni keresési példányból származó releváns rendszerképek listáját.

## <a name="custom-image-search-endpoint"></a>Egyéni Image Search végpont
Ha az egyéni keresési példányról szeretne képeket kérni, küldjön egy GET kérelmet a következő URL-címre:

```
https://api.cognitive.microsoft.com/bingcustomsearch/v7.0/images/search
```

Az egyéni keresési példány konfigurálásával kapcsolatos információkért lásd: [az egyéni keresési élmény konfigurálása](https://docs.microsoft.com/azure/cognitive-services/bing-custom-search/define-your-custom-view).

## <a name="next-steps"></a>További lépések
A **Bing** API-k olyan keresési műveleteket támogatnak, amelyek típusaik alapján adják vissza az eredményeket. A keresési végpontok az eredményeket JSON-válasz objektumokként adják vissza.  Minden végpont támogatja a lekérdezéseket, amelyek adott nyelvet és/vagy helyet adnak vissza a földrajzi hosszúság, a szélesség és a keresési sugár alapján.

Az egyes végpontok által támogatott paraméterekkel kapcsolatos részletes információkért tekintse meg az egyes típusok hivatkozási oldalait.
Az Custom Search APIt használó alapszintű kérelmekre vonatkozó Példákért lásd: [Egyéni keresés – gyors indítás](https://docs.microsoft.com/azure/cognitive-services/bing-custom-search/)
