---
title: Lapok átBing News Search eredményeinek megjelenítése
titleSuffix: Azure Cognitive Services
description: Megtudhatja, hogyan tekintheti át a Bing News Search API által visszaadott híreket.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-news-search
ms.topic: conceptual
ms.date: 01/10/2019
ms.author: scottwhi
ms.openlocfilehash: e5f8dce1a03e44758eea737ad2da419fa67c36a2
ms.sourcegitcommit: 198c3a585dd2d6f6809a1a25b9a732c0ad4a704f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/23/2019
ms.locfileid: "68423741"
---
# <a name="how-to-page-through-news-search-results"></a>A Hírek keresési eredményeinek megtekintése

A News Search API meghívásakor a Bing a lekérdezéshez kapcsolódó eredmények listáját adja vissza. A rendelkezésre álló találatok becsült teljes számának beszerzéséhez nyissa meg a válasz objektum [totalEstimatedMatches](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-news-api-v7-reference#news-totalmatches) mezőjét.  
  
Az alábbi példa azt a `totalEstimatedMatches` mezőt mutatja be, amelyet a hírek válasza tartalmaz.  

```json
{  
    "_type" : "News",  
    "readLink" : "https:\/\/api.cognitive.microsoft.com\/bing\/v7\/news\/search?q=sailing+dinghies",  
    "totalEstimatedMatches" : 88400,  
    "value" : [...]  
}  
```  
  
Az elérhető cikkeken keresztüli lapon a [Count](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-news-api-v7-reference#count) és az [offset](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-news-api-v7-reference#offset) lekérdezési paramétereket használhatja.  
 

|Paraméter  |Leírás  |
|---------|---------|
|`count`     | Megadja a válaszban visszaadni kívánt eredmények számát. A válaszban megadható eredmények maximális száma 100. Az alapértelmezett érték 10. A ténylegesen leszállított szám a kértnél kevesebb lehet.        |
|`offset`     | A kihagyni kívánt eredmények számát adja meg. A `offset` nulla-alapú, és kisebbnek kell lennie`totalEstimatedMatches`, mint ( - `count`).          |

Ha például 20 cikket szeretne megjeleníteni oldalanként, akkor az eredmények első oldalának beszerzéséhez 20 `count` és `offset` 0 értéket kell beállítania. Minden további lap esetében 20 (például 20 `offset` , 40) értékkel növelhető.  
  
Az alábbi példa 20 újságcikket kér le, a 40-as eltolástól kezdődően.  

```
GET https://api.cognitive.microsoft.com/bing/v7.0/news/search?q=sailing+dinghies&count=20&offset=40&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
Host: api.cognitive.microsoft.com  
```  
  
Ha az alapértelmezett `count` érték a megvalósításhoz is működik, csak a `offset` lekérdezési paramétert adja meg az alábbi példában látható módon:  

```  
GET https://api.cognitive.microsoft.com/bing/v7.0/news/search?q=sailing+dinghies&offset=40&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
Host: api.cognitive.microsoft.com  
```  

> [!NOTE]
> A lapozás csak a hírek keresésére (/News/Search) vonatkozik, és nem a témakörökre (/News/trendingtopics) vagy a hírek kategóriájára (/News).

> [!NOTE]
> A `TotalEstimatedAnswers` mező az aktuális lekérdezéshez lekérhető keresési eredmények teljes számát adja meg.  A beállítás és `count` `offset` a paraméterek beállításakor `TotalEstimatedAnswers` a szám változhat. 
