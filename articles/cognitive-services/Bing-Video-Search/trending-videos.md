---
title: Felkapott videók – a Bing Video Search keresése a weben
titlesuffix: Azure Cognitive Services
description: Bemutatja, hogyan felkapott videók keresése a weben a Bing Videókeresési API használatával.
services: cognitive-services
author: swhite-msft
manager: cgronlun
ms.service: cognitive-services
ms.subservice: bing-video-search
ms.topic: conceptual
ms.date: 04/15/2017
ms.author: scottwhi
ms.openlocfilehash: 71dd888199e9e630835c4916d35f6308431bab62
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/29/2019
ms.locfileid: "55203555"
---
# <a name="get-trending-videos"></a>Népszerű videók lekérése  

A mai felkapott videók lekéréséhez a következő GET kérelmet küldeni:  
  
```
GET https://api.cognitive.microsoft.com/bing/v7.0/videos/trending?mkt=en-us HTTP/1.1
Ocp-Apim-Subscription-Key: 123456789ABCDE  
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)  
X-MSEdge-ClientIP: 999.999.999.999  
X-Search-Location: lat:47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com  
```

A következő piacok felkapott videók támogatja.  
 
-   en-Ausztrália (angol nyelven, Ausztrália)  
-   en-CA (English, Canada)  
-   en-GB (angol nyelven, Nagy-Britannia)  
-   en-azonosító (angol nyelven, Indonézia)  
-   en – IE (angol nyelven, Írország)  
-   en-IN (angol nyelven, India)  
-   en-NZ (angol nyelven, Új-Zéland)  
-   en-PH (angol nyelven, Fülöp-szigetek)  
-   en-SG (angol nyelven, Szingapúr)  
-   en-US (angol nyelven, Egyesült Államok)  
-   en-WW (angol nyelven, Worldwide oldalon találhatóak összesített kód)  
-   en-ZA (angol nyelven, Dél-Afrika)  
-   zh-CN (kínai, Kína)

  
Az alábbi példa bemutatja egy választ, amely tartalmazza a felkapott videók.  

```  
{  
    "_type" : "TrendingVideos",  
    "bannerTiles" : [
        {  
            "query" : {  
                "text" : "Hello - Smith",  
                "displayText" : "Hello - Smith",  
                "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=3E8F5..."
            },  
            "image" : {  
                "description" : "Image from: contosowallpapers.com",  
                "thumbnailUrl" : "https:\/\/tse4.mm.bing.net\/th?id=RsA%2fdPlTmx4zS...",  
                "headLine" : "\"Hello\" is a song by...",  
                "contentUrl" : "http:\/\/www.contosowallpapers.com\/wp-content..."  
            }  
        },  
        . . .  
    ],  
    "categories" : [
        {  
            "title" : "Music videos",  
            "subcategories" : [
                {  
                    "tiles" : [
                        {  
                            "query" : {  
                                "text" : "Song Title - Artist Name",  
                                "displayText" : "Song Title - Artist Name",  
                                "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=3E8F5..."
                            },  
                            "image" : {  
                                "description" : "Image from: contoso.com",  
                                "thumbnailUrl" : "https:\/\/tse2.mm.bing.net\/th?id=...",  
                                "contentUrl" : "http:\/\/images6.contoso.com\/image..."  
                            }  
                        },  
                        . . .  
                    ],
                    "title" : "Top "  
                },
                {
                    "tiles" : [...],
                    "title" : "Trending"
                },
                . . .
            ],  
        },
        {
            "title" : "Viral videos",
            "subcategories" : [
                {
                    "tiles" : [...],
                    "title" : "Trending"
                },
                . . .
            ],  
        },
        . . .  
    ]  
}  
  
```  
A válasz tartalmazza a videók kategória és alkategória szerint. Például ha a kategóriák listájában szereplő zene videók kategóriát, és egyik alkategóriája felső volt, létrehozhat egy felső zene videók kategóriát a felhasználói élmény. Ezután használhatja a `thumbnailUrl`, `displayText`, és `webSearchUrl` mezők minden kategóriában (például felső zene videók) kattintható tartalmazó csempe létrehozása. Amikor a felhasználó a csempére kattint, akkor átkerül Bing video böngésző, a videó lejátszás.

A válasz a szalagcím videók, amelyek a legnépszerűbb felkapott videókat is tartalmaz. A szalagcím videók származhatnak a kategóriák közül legalább egyet.  
  
