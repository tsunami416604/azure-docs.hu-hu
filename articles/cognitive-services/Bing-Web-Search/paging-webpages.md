---
title: Hogyan lapozza végig a keresési eredmények – a Bing Web Search API
titleSuffix: Azure Cognitive Services
description: Tudnivalók a Bing Web Search API a keresési eredmények között.
services: cognitive-services
author: swhite-msft
manager: cgronlun
ms.assetid: 26CA595B-0866-43E8-93A2-F2B5E09D1F3B
ms.service: cognitive-services
ms.subservice: bing-web-search
ms.topic: conceptual
ms.date: 08/20/2018
ms.author: aahi
ms.openlocfilehash: 1ff4b7aa804dc3576462b3a30b94fdab8e1945e1
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/29/2019
ms.locfileid: "55164282"
---
# <a name="how-to-page-through-results-from-the-bing-web-search-api"></a>A Bing Web Search API az eredmények között

A Web Search API hívásakor a Bing-eredmények listáját adja vissza. A lista egy teljes száma, amely fontos lehet a lekérdezési eredmények egy részét. Az első rendelkezésre álló válaszokat becsült teljes száma, a válasz-objektum eléréséhez [totalEstimatedMatches](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference#totalestimatedmatches) mező.  

A következő példa bemutatja a `totalEstimatedMatches` mező, amely egy webes válasz tartalmazza.  

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

Oldalon keresztül a rendelkezésre álló weboldalakat, használja a [száma](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference#count) és [eltolás](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference#offset) lekérdezési paramétereket.  

A `count` paraméter adja meg a válaszban visszaadott eredmények száma. Az eredmények, amelyek a válaszban vonatkozó kérések maximális száma érték az 50. Az alapértelmezett érték 10. A tényleges szám i lehet kisebb, mint a kért.

A `offset` paraméter adja meg a kihagyandó eredmények száma. A `offset` nulláról induló, és lehet kisebb, mint (`totalEstimatedMatches` - `count`).  

Ha azt szeretné, egy-egy lapon 15 megjelenítésénél, így állíthatja `count` 15-re és `offset` 0 beolvasni az eredmények első oldala. Minden ezt követő laphoz, akkor növelni `offset` 15 (például 15, 30).  

Az alábbi példa lekéri 45 eltolástól kezdve 15 weblapok.  

```  
GET https://api.cognitive.microsoft.com/bing/v7.0/search?q=sailing+dinghies&count=15&offset=45&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
Host: api.cognitive.microsoft.com  
```

Ha az alapértelmezett `count` érték a megvalósítás esetében működik, csak meg kell adnia a `offset` lekérdezési paraméter.  

```  
GET https://api.cognitive.microsoft.com/bing/v7.0/search?q=sailing+dinghies&offset=45&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
Host: api.cognitive.microsoft.com  
```

A Web Search API, amelyek közé tartozik a weblapok és képek, videók és hírek tartalmazhatnak eredményt adja vissza. Ha a keresési eredmények oldalon lapozás a [WebAnswer](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference#webanswer) válasz és nem a többi válaszok például képeket vagy híreket. Például, ha a beállított `count` 50, vissza 50 weblap eredményeket kap, de a válasz tartalmazhat további válaszokat is az eredményeket. A válasz tartalmazhat például 15 képek és 4 híreket. Is lehetőség, hogy előfordulhat, hogy az eredmények tartalmazzák a híreket az első oldal, de nem a második oldal, vagy fordítva.   

Ha megad a `responseFilter` lekérdezési paraméter, és nem szerepel a lista weblapok, ne használja a `count` és `offset` paramétereket. 

> [!NOTE]
> A `TotalEstimatedAnswers` mező elszámolás kérheti le az aktuális lekérdezés keresési eredmények teljes száma.  Ha `count` és `offset` paraméterek, a `TotalEstimatedAnswers` száma változhat. 
