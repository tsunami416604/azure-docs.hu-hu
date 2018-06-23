---
title: Keresés a weben trendekkel lemezképek |} Microsoft Docs
description: Bemutatja, hogyan használja a képek Bing keresési API trendekkel képek a weben.
services: cognitive-services
author: swhite-msft
manager: ehansen
ms.assetid: EAB92D35-5C0B-4A0A-8F49-02DF7FAD44B4
ms.service: cognitive-services
ms.component: bing-image-search
ms.topic: article
ms.date: 04/15/2017
ms.author: scottwhi
ms.openlocfilehash: b12524cd4c1896501820209b3a45746b8f38b210
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/23/2018
ms.locfileid: "35346934"
---
# <a name="get-trending-images"></a>Trendekkel képek beolvasása  

Ahhoz, hogy az aktuális trendekkel lemezképek, a következő GET kérés küldése:  

```
GET https://api.cognitive.microsoft.com/bing/v7.0/images/trending?mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
X-MSEdge-ClientIP: 999.999.999.999  
X-Search-Location: lat:47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com  
```  

A trendek képek API jelenleg csak a következő piacon támogatja:  

- en-US (angol nyelvű, az Amerikai Egyesült Államok)  
- en-hitelesítésszolgáltató (angol nyelvű, Kanada)  
- en-AU (angol nyelvű, Ausztrália)  
- zh-CN (kínai, Kína)

A válasz tartalmazza a [TrendingImages](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#trendingimages) képek kategória szerint felsoroló objektum. A kategória `title` csoportosíthatja a felhasználói élmény a lemezképeket. A kategóriák a naponta módosíthatja.  
  
```json
{
    "_type" : "TrendingImages",  
    "categories" : [{  
        "title" : "Popular people searches",  
        "tiles" : [{  
            "query" : {  
                "text" : "Smith",  
                "displayText" : "Mr. Smith",  
                "webSearchUrl" : "https:\/\/www.bing.com\/images\/search?q=smith&FORM=..."
            },  
            "image" : {  
                "id" : "C3C60AE779A054D5CD80D3CACF0F3",  
                "thumbnailUrl" : "https:\/\/tse3.mm.bing.net\/th?id=OIP.M2532...",  
                "contentUrl" : "http:\/\/www.contoso.com.au\/assets\/Uploads\/smith-SH01.jpg",  
                "thumbnail" : {  
                    "width" : 288,  
                    "height" : 300  
                }  
            }  
        },  
        . . .  
        ]  
    },  
    . . .  
    {  
        "title" : "Popular Halloween searches",  
        "tiles" : [{  
            "query" : {  
                "text" : "Halloween costumes for adults",  
                "displayText" : "Halloween costumes for adults",  
                "webSearchUrl" : "https:\/\/www.bing.com\/images\/search?q=Halloween+costumes..."
            },  
            "image" : {  
                "id" : "0F3395F2983003F89DCEE711B55D7FA53E4",  
                "thumbnailUrl" : "https:\/\/tse4.mm.bing.net\/th?id=OIP.Me429c...",  
                "contentUrl" : "http:\/\/images.domain.com\/products\/8179\/1-1\/adult-squirrel...",  
                "thumbnail" : {  
                    "width" : 336,  
                    "height" : 480  
                }  
            }  
        }]  
    }]  
}  
```  
  
Mindegyik mozaiknál kép és a kapcsolódó rendszerképek beolvasása a beállításokat tartalmazza. Ahhoz, hogy a kapcsolódó képek, használhatja a lekérdezés `text` hívni a [kép Search API](./search-the-web.md) és a kapcsolódó képeket jeleníthessen meg magát. Vagy az URL-címet is használhat `webSearchUrl` érvénybe Bing tartozó képek keresési eredmények oldalát, a kapcsolódó lemezképet tartalmaz, amely a felhasználót. 

Ha felhívja a kép keresési API-t arra a kapcsolódó képek, állítsa be a [azonosító](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#id) lekérdezésparaméter a azonosító a `id` mező. Az azonosító megadása biztosítja, hogy a válasz tartalmazza-e a lemezkép (a válasz első kép) és a kapcsolódó ábráit. Is, beállíthatja a [q](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#q) lekérdezésparaméter a szöveg a `query` objektum `text` mező.

A következő példa bemutatja, hogyan előző trendek képek API-válaszból a John Smith kapcsolódó képek beolvasása a lemezkép-azonosító segítségével.

```  
GET https://api.cognitive.microsoft.com/bing/v7.0/images/search?q=Smith&id=77FDE4A1C6529A23C7CF0EC073FAA64843E828F2&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
X-MSEdge-ClientIP: 999.999.999.999  
X-Search-Location: lat:47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com  
```  
