---
title: Bing Custom Search-végpont
titleSuffix: Azure Cognitive Services
description: Személyre szabott keresési élményeket hozhat létre az Ön számára fontos témákhoz. A felhasználók az általuk fontos nak tartott tartalomhoz igazított keresési eredményeket láthatnak.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-custom-search
ms.topic: conceptual
ms.date: 03/04/2019
ms.author: aahi
ms.openlocfilehash: 03ec22caedd4e317b9e1fe781dc3d983febc7a6d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "74072786"
---
# <a name="custom-search"></a>Egyéni keresés
A Bing Custom Search szolgáltatással személyre szabott keresési funkciókat hozhat létre olyan témakörökhöz kapcsolódóan, amelyek jelentőséggel bírnak az Ön számára. A felhasználók az érdeklődési körükhöz igazított keresési eredményeket látnak ahelyett, hogy több oldalnyi irreleváns találatot kellene átnézniük.

## <a name="custom-search-endpoint"></a>Egyéni keresési végpont
Eredmények beszerezése a Bing egyéni `GET` keresési API használatával, küldjön egy kérelmet a következő végpontra. A fejlécek és az URL-paraméterek segítségével további specifikációkat határozhatja meg.

Végpont: A keresési javaslatokat JSON-eredményként adja vissza, amelyek `?q=""`relevánsak a felhasználó által definiált bemenethez.
```  
 GET https://api.cognitive.microsoft.com/bingcustomsearch/v7.0/search  
```

Az egyéni keresési források beállítását leíró példákat az [oktatóanyagban](https://docs.microsoft.com/azure/cognitive-services/bing-custom-search/tutorials/custom-search-web-page)talál. A fejlécekről, paraméterekről, piaci kódokról, válaszobjektumokról, hibákról stb. [Bing Custom Search API v7](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-custom-search-api-v7-reference)

## <a name="custom-search-response-json"></a>Egyéni keresési válasz JSON
Az egyéni keresési kérelem JSON-objektumokként adja vissza az eredményeket, lásd: [Válaszobjektumok](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-custom-search-api-v7-reference#response-objects). 

## <a name="custom-autosuggest"></a>Egyéni automatikus javaslat
Az egyéni automatikus javaslat API lehetővé teszi, hogy részleges keresési lekérdezési kifejezést küldjön a Bingnek, és visszakapja a konfigurálható javasolt lekérdezések listáját. Az egyéni automatikus javaslat, adja hozzá az API által visszaadott javaslatokat, és szükség esetén adja meg, hogy tartalmazza-e a Bing által létrehozott javaslatokat.

## <a name="custom-autosuggest-endpoint"></a>Egyéni automatikus javaslatvégpont
Egyéni lekérdezési javaslatok igényléséhez küldjön get-kérelmet a következő nek:

```
https://api.cognitive.microsoft.com/bingcustomsearch/v7.0/Suggestions
```  

Az egyéni javaslatok meghatározásáról az [Egyéni keresési javaslatok megadása című témakörben](define-custom-suggestions.md)talál további információt.

## <a name="custom-image-search"></a>Egyéni képkeresés
Az egyéni képkeresési API lehetővé teszi, hogy keresési lekérdezést küldjön a Bingnek, és visszakapja a releváns képek listáját az egyéni keresési példányból.

## <a name="custom-image-search-endpoint"></a>Egyéni képkeresési végpont
Ha képeket szeretne kérni az egyéni keresési példányból, küldjön GET-kérelmet a következő URL-címre:

```
https://api.cognitive.microsoft.com/bingcustomsearch/v7.0/images/search
```

Az egyéni keresési példányok konfigurálásáról [az Egyéni keresési élmény konfigurálása](https://docs.microsoft.com/azure/cognitive-services/bing-custom-search/define-your-custom-view)című témakörben talál további információt.

## <a name="next-steps"></a>További lépések
A **Bing** API-k támogatják azokat a keresési műveleteket, amelyek típusuk nak megfelelően adják vissza az eredményeket.Minden keresési végpont json válaszobjektumként adja vissza az eredményeket. Minden végpont támogatja azokat a lekérdezéseket, amelyek hosszúság, szélesség és keresési sugár szerint egy adott nyelvet és/vagy helyet adnak vissza.

Az egyes végpontok által támogatott paraméterekről az egyes típusok referenciaoldalain talál teljes körű tájékoztatást.
Az egyéni keresési API-t használó alapvető kérelmekre az [Egyéni keresés gyorsindítása című](https://docs.microsoft.com/azure/cognitive-services/bing-custom-search/) témakörben talál példákat.
