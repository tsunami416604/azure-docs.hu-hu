---
title: Keresés a weben trendekkel videók |} Microsoft Docs
description: Bemutatja, hogyan kereshet a weben trendekkel videók a videó Bing keresési API segítségével.
services: cognitive-services
author: swhite-msft
manager: ehansen
ms.assetid: 897A28A3-0980-484E-814F-FFE1D5C885E6
ms.service: cognitive-services
ms.component: bing-video-search
ms.topic: article
ms.date: 04/15/2017
ms.author: scottwhi
ms.openlocfilehash: 8db7fcf77042631260b4b165bd3d44053827f3ce
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/23/2018
ms.locfileid: "35346895"
---
# <a name="get-trending-videos"></a>Trendekkel videók beolvasása  

Ahhoz, hogy az aktuális trendekkel videók, a következő GET kérés küldése:  
  
```
GET https://api.cognitive.microsoft.com/bing/v7.0/videos/trending?mkt=en-us HTTP/1.1
Ocp-Apim-Subscription-Key: 123456789ABCDE  
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)  
X-MSEdge-ClientIP: 999.999.999.999  
X-Search-Location: lat:47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com  
```

A következő piacok trendekkel videók támogatja.  
 
-   en-AU (angol nyelvű, Ausztrália)  
-   en-hitelesítésszolgáltató (angol nyelvű, Kanada)  
-   en-GB (angol nyelvű, hazai)  
-   en-azonosító (angol nyelvű, indonéziai)  
-   en-IE (angol nyelvű, Írországban)  
-   en-IN (angol nyelvű, India)  
-   en-NZ (angol nyelvű, Új-Zéland)  
-   en-PH (angol nyelvű, Fülöp-szigeteki)  
-   en-SG (angol nyelvű, szingapúri)  
-   en-US (angol nyelvű, az Amerikai Egyesült Államok)  
-   en-hh (angol, Worldwide oldalon találhatóak összesített kód)  
-   en-ZA (angol nyelvű, Dél-afrikai)  
-   zh-CN (kínai, Kína)

  
A következő példa bemutatja egy választ, amely trendekkel videók tartalmazza.  

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
A válasz kategória és alkategória videók listáját tartalmazza. Például ha kategóriák listája tartalmazott zene videók kategóriát, és egy alkategóriáját felső volt, létrehozhat egy felső zene videók kategóriát a felhasználói élményt nyújt. Ezután használhatja a `thumbnailUrl`, `displayText`, és `webSearchUrl` mezők egy kattintható csempe kategóriákban (például felső zene videók) létrehozásához. Amikor a felhasználó a csempére kattint, azok még fordítani Bing a videó böngésző ahol a videó lejátszása.

A válasz a szalagcím videók, amelyek a legnépszerűbb trendekkel videók is tartalmaz. A szalagcím videókhoz, amelyeket a kategóriák közül legalább egyet származhatnak.  
  
