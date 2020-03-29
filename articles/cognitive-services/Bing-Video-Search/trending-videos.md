---
title: Felkapott videók keresése az interneten a Bing Video Search API használatával
titleSuffix: Azure Cognitive Services
description: Megtudhatja, hogy a Bing Video Search API-val hogyan kereshet népszerű videókat az interneten.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-video-search
ms.topic: conceptual
ms.date: 01/31/2019
ms.author: scottwhi
ms.openlocfilehash: ea6f79bf6c305ecc07b3e684ede15ad439039e7e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "68500609"
---
# <a name="get-trending-videos-with-the-bing-video-search-api"></a>Népszerű videók beszereznie a Bing Video Search API-val 

A Bing Video Search API lehetővé teszi, hogy megtalálja a mai népszerű videókat az interneten keresztül és különböző kategóriákban. 

## <a name="get-request"></a>GET kérés

Ha a Bing Video Search API-ból szeretné lekérni a mai felkapott videókat, küldje el a következő GET-kérelmet:  
  
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

A következő piacok támogatják a felkapott videókat.  
 
-   en-AU (Angol, Ausztrália)  
-   en-CA (angol, Kanada)  
-   en-GB (angol, Nagy-Britannia)  
-   en-ID (Angol, Indonézia)  
-   en-IE (angol, Írország)  
-   en-IN (angol, India)  
-   en-NZ (angol, Új-Zéland)  
-   en-PH (Angol, Fülöp-szigetek)  
-   en-SG (angol, szingapúri)  
-   en-US (English, Amerikai Egyesült Államok)  
-   en-WW (angol, világszerte összesített kód)  
-   en-ZA (angol, Dél-Afrikai Köztársaság)  
-   zh-CN (kínai, Kína)

## <a name="example-json-response"></a>Példa JSON-válaszra  

A következő példa egy API-választ mutat be, amely felkapott videókat tartalmaz, amelyek kategória és alkategória szerint vannak felsorolva. A válasz banner videókat is tartalmaz, amelyek a legnépszerűbb felkapott videók, és egy vagy több kategóriából származhatnak.  

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
> [Videoelemzések lekérése](video-insights.md)