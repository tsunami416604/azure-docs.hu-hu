---
title: Hogyan oldalon keresztül a rendelkezésre álló hírcikkeket |} Microsoft Docs
description: Bemutatja, hogyan összes a Bing visszaadó hírcikkeket lapon.
services: cognitive-services
author: swhite-msft
manager: ehansen
ms.assetid: EA388F72-FA43-493B-967C-9560B3243C62
ms.service: cognitive-services
ms.component: bing-news-search
ms.topic: article
ms.date: 04/15/2017
ms.author: scottwhi
ms.openlocfilehash: 2c90d468536f0864d7deac073667e29e9a54692f
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/23/2018
ms.locfileid: "35346927"
---
# <a name="paging-news"></a>Lapozófájl hírek

Hívja a hírek keresési API-t, a Bing eredmények listáját adja vissza. A lista, amely fontos lehet a lekérdezési eredmények száma részhalmaza. Ahhoz, hogy a rendelkezésre álló eredmények becsült száma, a válasz-objektum eléréséhez [totalEstimatedMatches](https://docs.microsoft.com/rest/api/cognitiveservices/bing-news-api-v7-reference#news-totalmatches) mező.  
  
Az alábbi példa azt mutatja meg a `totalEstimatedMatches` egy hírek választ tartalmazó mezőt.  
  
```  
{  
    "_type" : "News",  
    "readLink" : "https:\/\/api.cognitive.microsoft.com\/bing\/v7\/news\/search?q=sailing+dinghies",  
    "totalEstimatedMatches" : 88400,  
    "value" : [...]  
}  
```  
  
Lapon keresztül a rendelkezésre álló cikkeket, használja a [száma](https://docs.microsoft.com/rest/api/cognitiveservices/bing-news-api-v7-reference#count) és [eltolás](https://docs.microsoft.com/rest/api/cognitiveservices/bing-news-api-v7-reference#offset) lekérdezési paramétert.  
  
A `count` paraméter határozza meg a válaszban szereplő vissza eredmények száma. Az eredmények, amelyek a válaszban szereplő vonatkozó kérések maximális száma: 100. Az alapértelmezett érték 10. A tényleges szám kézbesíteni lehet kisebb, mint a kért.

A `offset` paraméter határozza meg a kihagyandó eredmények számát. A `offset` Column, és kisebb, mint (`totalEstimatedMatches` - `count`).  

Ha 20 cikkek laponként megjelenítendő állíthat `count` 20 és `offset` 0 érték megadásával az első oldal az eredmények. Az összes azt követő lapon, akkor növelnie `offset` 20 (például 20, 40).  
  
A következő 20, 40 eltolástól kezdve hírcikkeket kérő példáját mutatja be.  
  
```  
GET https://api.cognitive.microsoft.com/bing/v7.0/news/search?q=sailing+dinghies&count=20&offset=40&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
Host: api.cognitive.microsoft.com  
```  
  
Ha az alapértelmezett `count` értékét a megvalósítás működik, csak adja meg a `offset` lekérdezésparaméter a következő példában látható módon:  
  
```  
GET https://api.cognitive.microsoft.com/bing/v7.0/news/search?q=sailing+dinghies&offset=40&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
Host: api.cognitive.microsoft.com  
```  
  
> [!NOTE]
> Lapozófájl csak hírek search (Keresés/hírek /), nem pedig trendekkel kapcsolatos témakörök (/ hírek/trendingtopics) vagy új kategóriák vonatkozik (/ Hírek).