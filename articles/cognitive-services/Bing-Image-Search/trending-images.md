---
title: Népszerű képek beszereznie a Bing Image Search API-val
titleSuffix: Azure Cognitive Services
description: Keresse meg a mai felkapott képeket az internetről a Bing Image Search API-val.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.assetid: EAB92D35-5C0B-4A0A-8F49-02DF7FAD44B4
ms.service: cognitive-services
ms.subservice: bing-image-search
ms.topic: conceptual
ms.date: 03/04/2019
ms.author: scottwhi
ms.custom: seodec2018
ms.openlocfilehash: 2936b94d7ba791b1a4e5a9b95aca3ca3ecdb5904
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "66383434"
---
# <a name="get-trending-images-from-the-web"></a>Népszerű képek beszereznie az internetről

A mai felkapott képek lekéréséhez küldje el a következő GET-kérést:  

```
GET https://api.cognitive.microsoft.com/bing/v7.0/images/trending?mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
X-MSEdge-ClientIP: 999.999.999.999  
X-Search-Location: lat:47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com  
```  

A Felkapott képek API jelenleg csak a következő piacokat támogatja:  

- en-US (English, Amerikai Egyesült Államok)  
- en-CA (angol, Kanada)  
- en-AU (Angol, Ausztrália)  
- zh-CN (kínai, Kína)

A válasz tartalmaz egy [Felkapott képek](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#trendingimages) objektumot, amely kategóriák szerint sorolja fel a képeket. A kategória segítségével `title` csoportosíthatja a képeket a felhasználói élményben. A kategóriák naponta változhatnak.  

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

Minden csempe tartalmaz egy képet, és a kapcsolódó képek beszerzésének lehetőségeit. A kapcsolódó képek lekérni, a `text` lekérdezés segítségével hívja meg a [Képkereső API-t,](./search-the-web.md) és jelenítse meg a kapcsolódó képeket saját maga. Vagy használhatja az URL-t, `webSearchUrl` hogy a felhasználó a Bing képek keresési eredményoldal, amely tartalmazza a kapcsolódó képeket.

Ha meghívja a Képkereső API-t a kapcsolódó lemezképek lehívásához, `id` állítsa be az [id](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#id) lekérdezési paramétert a mezőben lévő azonosítóra. Az azonosító megadása biztosítja, hogy a válasz tartalmazza a képet (ez az első kép a válaszban) és a kapcsolódó képeket. Állítsa be a [q](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference) lekérdezés paramétert `query` az `text` objektum mezőjében lévő szövegre is.

A következő példa bemutatja, hogyan használhatja a képazonosítót, hogy a kapcsolódó képeket Mr. Smith az előző Felkapott képek API-válasz.

```  
GET https://api.cognitive.microsoft.com/bing/v7.0/images/search?q=Smith&id=77FDE4A1C6529A23C7CF0EC073FAA64843E828F2&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
X-MSEdge-ClientIP: 999.999.999.999  
X-Search-Location: lat:47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com  
```  
