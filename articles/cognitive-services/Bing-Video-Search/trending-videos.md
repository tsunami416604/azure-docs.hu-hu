---
title: A weben megkeresheti a videók trendjét a Bing Video Search API használatával
titleSuffix: Azure Cognitive Services
description: Megtudhatja, hogyan használhatja a Bing Video Search API a webes kereséshez a videók trendjeinek megjelenítéséhez.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-video-search
ms.topic: conceptual
ms.date: 01/31/2019
ms.author: scottwhi
ms.openlocfilehash: ea6f79bf6c305ecc07b3e684ede15ad439039e7e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2020
ms.locfileid: "68500609"
---
# <a name="get-trending-videos-with-the-bing-video-search-api"></a>Trendek a Bing Video Search API 

A Bing Video Search API lehetővé teszi, hogy megkeresse a mai trendek videóit a weben és különböző kategóriákban. 

## <a name="get-request"></a>GET kérés

A következő GET kérelem elküldésével megtekintheti a mai trendek videóit a Bing Video Search APIból:  
  
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

A következő piacok támogatják a trendi videókat.  
 
-   EN-AU (angol, Ausztrália)  
-   en-CA (angol, Kanada)  
-   en-GB (angol, Nagy-Britannia)  
-   EN-ID (angol, Indonézia)  
-   EN-IE (angol, Írország)  
-   EN-IN (angol, India)  
-   EN-NZ (angol, Új-Zéland)  
-   EN-PH (angol, Fülöp-szigetek)  
-   EN-SG (angol, Szingapúr)  
-   en-US (angol, Egyesült Államok)  
-   EN-WW (angol, globális összesített kód)  
-   EN-ZA (angol, Dél-Afrika)  
-   zh-CN (kínai, Kína)

## <a name="example-json-response"></a>Példa JSON-válaszra  

Az alábbi példa egy API-választ mutat be, amely a kategória és az Alkategória szerint felsorolt trendi videókat tartalmaz. A válasz szalagcím-videókat is tartalmaz, amelyek a legnépszerűbb videók, és egy vagy több kategóriából is származhatnak.  

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