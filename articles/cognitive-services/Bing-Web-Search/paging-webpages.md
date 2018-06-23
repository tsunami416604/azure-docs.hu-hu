---
title: Hogyan oldalon keresztül a rendelkezésre álló weblapok |} Microsoft Docs
description: Bemutatja, hogyan lapon összes Bing lépjen vissza a weblapokat.
services: cognitive-services
author: swhite-msft
manager: ehansen
ms.assetid: 26CA595B-0866-43E8-93A2-F2B5E09D1F3B
ms.service: cognitive-services
ms.component: bing-web-search
ms.topic: article
ms.date: 04/15/2017
ms.author: scottwhi
ms.openlocfilehash: bf29783246c603270d59b20b63027fccdbd45b89
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/23/2018
ms.locfileid: "35346970"
---
# <a name="paging-webpages"></a>Lapozófájl szolgáltatásának konfigurálása 

A webes keresés API hívásakor a Bing eredmények listáját adja vissza. A lista, amely fontos lehet a lekérdezési eredmények száma részhalmaza. Ahhoz, hogy a rendelkezésre álló eredmények becsült száma, a válasz-objektum eléréséhez [totalEstimatedMatches](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference#totalestimatedmatches) mező.  
  
Az alábbi példa azt mutatja meg a `totalEstimatedMatches` mező, amely a Webes válasz tartalmazza.  
  
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
  
Lapon keresztül a rendelkezésre álló weblapjait, használja a [száma](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference#count) és [eltolás](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference#offset) lekérdezési paramétert.  
  
A `count` paraméter határozza meg a válaszban szereplő vissza eredmények száma. Az eredmények, amelyek a válaszban szereplő vonatkozó kérések maximális száma érték az 50. Az alapértelmezett érték 10. A tényleges szám kézbesíteni lehet kisebb, mint a kért.

A `offset` paraméter határozza meg a kihagyandó eredmények számát. A `offset` Column, és kisebb, mint (`totalEstimatedMatches` - `count`).  
  
Ha 15 weblapok laponként megjelenítendő állíthat `count` 15 és `offset` 0 érték megadásával az első oldal az eredmények. Az összes azt követő lapon, akkor növelnie `offset` 15 (például 15, 30).  
  
Az alábbi példa kérelmek 15 weblapok 45 eltolástól kezdve.  
  
```  
GET https://api.cognitive.microsoft.com/bing/v7.0/search?q=sailing+dinghies&count=15&offset=45&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
Host: api.cognitive.microsoft.com  
```

Ha az alapértelmezett `count` érték működik, a megvalósítás, csak meg kell adnia a `offset` lekérdezési paraméter.  
  
```  
GET https://api.cognitive.microsoft.com/bing/v7.0/search?q=sailing+dinghies&offset=45&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
Host: api.cognitive.microsoft.com  
```

A webes keresés API eredmények, amelyek tartalmaznak weblapok és lemezképeket, videók és hírek tartalmazhat adja vissza. Lapon a keresési eredmények között, amikor lapozás a [WebAnswer](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference#webanswer) válasz és nem a más válaszok például képeket vagy híreket. Ha például `count` 50, vissza 50 weblap eredményt, de a válasz tartalmazhatja az egyéb válaszokat, valamint az eredmények. A válasz lehet, hogy például 15 képek és 4 írt hírcikkeket. Akkor is, hogy az eredmények lehet, hogy tartalmazzák a híreket az első lap, de nem a második lapján, vagy fordítva.   
    
Ha megadja a `responseFilter` lekérdezésparaméter és nem tartalmaznak weblapok a szűrőlistában, ne használjon a `count` és `offset` paraméterek.  
