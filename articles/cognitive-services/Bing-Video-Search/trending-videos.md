---
title: A felkapott videókat a Bing Video Search API webes keresés
titlesuffix: Azure Cognitive Services
description: Ismerje meg, hogyan felkapott videók keresése a weben a Bing Videókeresési API használatával.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-video-search
ms.topic: conceptual
ms.date: 01/31/2019
ms.author: scottwhi
ms.openlocfilehash: 2aa3c0cd9112f2de9ccda818e74b0b2c377133bf
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/07/2019
ms.locfileid: "55869364"
---
# <a name="get-trending-videos-with-the-bing-video-search-api"></a>Felkapott videókat a Bing Video Search API az első 

A Bing Videókeresési API lehetővé teszi a weben, vagy különböző kategóriákban keresse meg a mai felkapott videók a. 

## <a name="get-request"></a>Kérelem beolvasása

A mai felkapott videókat a Bing Video Search API a lekéréséhez a következő GET kérelmet küldeni:  
  
```cURL
GET https://api.cognitive.microsoft.com/bing/v7.0/videos/trending?mkt=en-us HTTP/1.1
Ocp-Apim-Subscription-Key: 123456789ABCDE  
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)  
X-MSEdge-ClientIP: 999.999.999.999  
X-Search-Location: lat:47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com  
```

## <a name="market-support"></a>Piaci támogatás

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

## <a name="example-json-response"></a>Példa JSON-válasz  

Az alábbi példa bemutatja egy API-válasz, amely tartalmazza a felkapott videók, amelyek kategória és alkategória szerint vannak felsorolva. A válasz is tartalmaz a szalagcím videók, amelyek a legnépszerűbb felkapott videók, és a egy vagy több kategóriához is biztosítja.  

```json
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

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
[Videó elemzések lekérése](video-insights.md)