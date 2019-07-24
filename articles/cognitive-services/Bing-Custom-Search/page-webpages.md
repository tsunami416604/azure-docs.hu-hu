---
title: Oldal elérhető weblapokon – Bing Custom Search
titleSuffix: Azure Cognitive Services
description: Azt mutatja be, hogyan lehet az összes olyan weboldalon átadni a lapot, amelyet Bing Custom Search visszatérhet.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-custom-search
ms.topic: conceptual
ms.date: 02/12/2019
ms.author: maheshb
ms.openlocfilehash: 13b4cef624c636b8935897338badf3349f27c7f5
ms.sourcegitcommit: 9dc7517db9c5817a3acd52d789547f2e3efff848
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/23/2019
ms.locfileid: "68405039"
---
# <a name="paging-webpages"></a>Lapozás weblapjai 

A Custom Search API meghívásakor a Bing visszaadja az eredmények listáját. A lista a lekérdezéshez esetlegesen szükséges eredmények teljes részhalmaza. A rendelkezésre álló találatok becsült teljes számának beszerzéséhez nyissa meg a válasz objektum [totalEstimatedMatches](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-custom-search-api-v7-reference#totalestimatedmatches) mezőjét.  
  
Az alábbi példa azt a `totalEstimatedMatches` mezőt mutatja be, amelyet a webes válasz tartalmaz.  
  
```  
{
    "_type" : "SearchResponse",
    "webPages" : {
        "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=3A43CA...",
        "totalEstimatedMatches" : 262000,
        "value" : [...]
    }
}  
```  
  
Az elérhető weblapokon keresztüli lapon a [Count](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-custom-search-api-v7-reference#count) és az [eltolás](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-custom-search-api-v7-reference#offset) lekérdezési paramétereket használhatja.  
  
A `count` paraméter határozza meg a válaszban visszaadni kívánt eredmények számát. A válaszban megadható eredmények maximális száma 50. Az alapértelmezett érték 10. A ténylegesen leszállított szám a kértnél kevesebb lehet.

A `offset` paraméter határozza meg a kihagyni kívánt eredmények számát. A `offset` nulla-alapú, és kisebbnek kell lennie`totalEstimatedMatches`, mint ( - `count`).  
  
Ha oldalanként 15 weboldalt szeretne megjeleníteni, a találatok első oldalának `count` lekéréséhez `offset` 15 és 0-ra értéket kell beállítani. Minden további oldalon 15 (például 15, `offset` 30) értékkel növelhető.  
  
Az alábbi példa azt mutatja be, hogy 15 weboldalt kér a 45 eltolástól kezdődően.  
  
```  
GET https://api.cognitive.microsoft.com/bingcustomsearch/v7.0/search?q=sailing+dinghies&count=15&offset=45&mkt=en-us&customConfig=123456 HTTP/1.1  
Ocp-Apim-Subscription-Key: <subscription ID>
Host: api.cognitive.microsoft.com  
```  

Ha az alapértelmezett `count` érték a megvalósításhoz is működik, csak a `offset` lekérdezési paramétert kell megadnia.  
  
```  
GET https://api.cognitive.microsoft.com/bingcustomsearch/v7.0/search?q=sailing+dinghies&offset=45&mkt=en-us&customConfig=123456 HTTP/1.1  
Ocp-Apim-Subscription-Key: <subscription ID>  
Host: api.cognitive.microsoft.com  
```  

> [!NOTE]
> A `TotalEstimatedAnswers` mező az aktuális lekérdezéshez lekérhető keresési eredmények teljes számát adja meg.  A beállítás és `count` `offset` a paraméterek beállításakor `TotalEstimatedAnswers` a szám változhat. 

