---
title: Oldalakra keresztül a rendelkezésre álló hírek – Bing – Hírkeresés
titlesuffix: Azure Cognitive Services
description: Bemutatja, hogyan végig az összes új keresési Bing adhatnak vissza a hírek lap.
services: cognitive-services
author: swhite-msft
manager: cgronlun
ms.service: cognitive-services
ms.component: bing-news-search
ms.topic: conceptual
ms.date: 04/15/2017
ms.author: scottwhi
ms.openlocfilehash: fff1da15df2e690cd0b37bb82654a4d30159325a
ms.sourcegitcommit: 9eaf634d59f7369bec5a2e311806d4a149e9f425
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/05/2018
ms.locfileid: "48803392"
---
# <a name="paging-news"></a>Lapozófájl-hírek

A News Search API hívásakor a Bing eredmények listáját adja vissza. A lista egy teljes száma, amely fontos lehet a lekérdezési eredmények egy részét. Az első rendelkezésre álló válaszokat becsült teljes száma, a válasz-objektum eléréséhez [totalEstimatedMatches](https://docs.microsoft.com/rest/api/cognitiveservices/bing-news-api-v7-reference#news-totalmatches) mező.  
  
A következő példa bemutatja a `totalEstimatedMatches` mező, amely egy News válasz tartalmazza.  
  
```  
{  
    "_type" : "News",  
    "readLink" : "https:\/\/api.cognitive.microsoft.com\/bing\/v7\/news\/search?q=sailing+dinghies",  
    "totalEstimatedMatches" : 88400,  
    "value" : [...]  
}  
```  
  
Oldalon keresztül a rendelkezésre álló cikkeket, használja a [száma](https://docs.microsoft.com/rest/api/cognitiveservices/bing-news-api-v7-reference#count) és [eltolás](https://docs.microsoft.com/rest/api/cognitiveservices/bing-news-api-v7-reference#offset) lekérdezési paramétereket.  
  
A `count` paraméter adja meg a válaszban visszaadott eredmények száma. Az eredmények, amelyek a válaszban vonatkozó kérések maximális száma pedig a 100. Az alapértelmezett érték 10. A tényleges szám i lehet kisebb, mint a kért.

A `offset` paraméter adja meg a kihagyandó eredmények száma. A `offset` nulláról induló, és lehet kisebb, mint (`totalEstimatedMatches` - `count`).  

Ha meg szeretné jeleníteni a oldalanként 20 cikkeket, így állíthatja `count` 20-ra és `offset` 0 beolvasni az eredmények első oldala. Minden ezt követő laphoz, akkor növelni `offset` 20 (például 20, 40).  
  
Az alábbiakban látható egy példa, amely 20, 40 eltolástól kezdve hírcikkeket kér.  
  
```  
GET https://api.cognitive.microsoft.com/bing/v7.0/news/search?q=sailing+dinghies&count=20&offset=40&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
Host: api.cognitive.microsoft.com  
```  
  
Ha az alapértelmezett `count` értékét a megvalósítás esetében működik, csak adja meg a `offset` lekérdezési paraméter, a következő példában látható módon:  
  
```  
GET https://api.cognitive.microsoft.com/bing/v7.0/news/search?q=sailing+dinghies&offset=40&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
Host: api.cognitive.microsoft.com  
```  
  
> [!NOTE]
> Lapozófájl csak news search (Keresés/news /), és nem Népszerű témakörök (/ news/trendingtopics) vagy hírkategóriák vonatkozik (vagy hírek).