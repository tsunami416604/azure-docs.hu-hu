---
title: Hogyan lapozza végig a Hírkeresési Bing-eredmények
titlesuffix: Azure Cognitive Services
description: Ismerje meg, hogyan keresztül, amely a Bing News Search API adja vissza a hírek lap.
services: cognitive-services
author: swhite-msft
manager: cgronlun
ms.service: cognitive-services
ms.component: bing-news-search
ms.topic: conceptual
ms.date: 01/10/2019
ms.author: scottwhi
ms.openlocfilehash: 9acf0c26707dab9de443b06e7314de6d77913777
ms.sourcegitcommit: c61777f4aa47b91fb4df0c07614fdcf8ab6dcf32
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/14/2019
ms.locfileid: "54258254"
---
# <a name="how-to-page-through-news-search-results"></a>Oldalakra hírkeresési eredményeket keresztül

A News Search API hívásakor a Bing, amely a lekérdezést a találatok listáját adja vissza. Az első rendelkezésre álló válaszokat becsült teljes száma, a válasz-objektum eléréséhez [totalEstimatedMatches](https://docs.microsoft.com/rest/api/cognitiveservices/bing-news-api-v7-reference#news-totalmatches) mező.  
  
A következő példa bemutatja a `totalEstimatedMatches` mező, amely egy News válasz tartalmazza.  

```json
{  
    "_type" : "News",  
    "readLink" : "https:\/\/api.cognitive.microsoft.com\/bing\/v7\/news\/search?q=sailing+dinghies",  
    "totalEstimatedMatches" : 88400,  
    "value" : [...]  
}  
```  
  
Oldalon keresztül a rendelkezésre álló cikkeket, használja a [száma](https://docs.microsoft.com/rest/api/cognitiveservices/bing-news-api-v7-reference#count) és [eltolás](https://docs.microsoft.com/rest/api/cognitiveservices/bing-news-api-v7-reference#offset) lekérdezési paramétereket.  
 

|Paraméter  |Leírás  |
|---------|---------|
|`count`     | A válaszban visszaadott eredmények számát adja meg. Az eredmények, amelyek a válaszban vonatkozó kérések maximális száma pedig a 100. Az alapértelmezett érték 10. A tényleges szám i lehet kisebb, mint a kért.        |
|`offset`     | Megadja a kihagyandó eredmények száma. A `offset` nulláról induló, és lehet kisebb, mint (`totalEstimatedMatches` - `count`).          |

Például, ha meg szeretné jeleníteni a oldalanként 20 cikkeket, így állíthatja be `count` 20-ra és `offset` 0 beolvasni az eredmények első oldala. Minden ezt követő laphoz, akkor növelni `offset` 20 (például 20, 40).  
  
Az alábbi példa lekéri 20, 40 eltolástól kezdve hírcikkeket.  

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

> [!NOTE]
> A `TotalEstimatedAnswers` mező elszámolás kérheti le az aktuális lekérdezés keresési eredmények teljes száma.  Ha `count` és `offset` paraméterek, a `TotalEstimatedAnswers` száma változhat. 