---
title: Gyors üzembe helyezési Search API képeket |} A Microsoft Docs
description: Bemutatja, hogyan képeket a Bing Search API használatának első lépéseit.
services: cognitive-services
author: swhite-msft
manager: ehansen
ms.assetid: BE2B2F8C-20B5-4E0B-AEC8-EAD505BA4C85
ms.service: cognitive-services
ms.component: bing-image-search
ms.topic: article
ms.date: 04/15/2017
ms.author: scottwhi
ms.openlocfilehash: 5742efb2dca02565735cf308d9ede9316ce6d12e
ms.sourcegitcommit: a08d1236f737915817815da299984461cc2ab07e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/26/2018
ms.locfileid: "52314864"
---
# <a name="your-first-images-search-query"></a>Az első képkeresési lekérdezés

Ahhoz, hogy elvégezhesse az első hívást, be kell szereznie egy Bing Search Cognitive Services-előfizetési azonosítót. Az előfizetői azonosító beszerzéséhez lásd [A Cognitive Services kipróbálása](https://azure.microsoft.com/try/cognitive-services/?api=bing-image-search-api) témakört.  Lásd még: [a Cognitive Services díjszabás – keresési Bing-API](https://azure.microsoft.com/pricing/details/cognitive-services/search-api/).

Eredményeit lekéréséhez GET kérést szeretne küldeni a következő végpont:  
  
```
https://api.cognitive.microsoft.com/bing/v7.0/images/search
```
  
A kérelemnek a HTTPS protokollt kell használnia.

Javasoljuk, hogy minden kérelem egy kiszolgálóról induljon. Az azonosítónak egy ügyfélalkalmazás részeként való terjesztése több lehetőséget ad arra, hogy rosszindulatú külső felek hozzáférjenek az azonosítóhoz. Emellett a hívások kiszolgálóról való indítása egyetlen frissítési pontot teremt az API későbbi verziói számára.

A kérelemnek tartalmaznia kell a [q](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#query) lekérdezési paramétert, amely a felhasználó keresési kifejezését adja meg. Nem kötelező, de a kérelemnek érdemes tartalmaznia egy [mkt](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#mkt) lekérdezési paramétert is, amely azonosítja a piacot, ahonnan eredményeket szeretnénk kapni. Az opcionális lekérdezési paraméterek (például `freshness` és `size`) listáját lásd a [lekérdezési paramétereket](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#query-parameters) ismertető cikkben. Minden lekérdezési paraméter értékének URL-kódolásúnak kell lennie.  
  
A kérelemnek tartalmaznia kell az [Ocp-Apim-Subscription-Key](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#subscriptionkey) fejlécet. Nem kötelező, de javasolt a következő fejlécek megadása is:  
  
-   [User-Agent](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#useragent)  
-   [X-MSEdge-ClientID](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#clientid)  
-   [X-Search-ClientIP](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#clientip)  
-   [X-Search-Location](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#location)  

Az ügyfél IP-címe és helye fontos a helyfüggő tartalmak visszaadása szempontjából.  

Az összes kérelem- és válaszfejléc listáját lásd a [Fejlécek](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#headers) cikkben.

## <a name="the-request"></a>A kérelem

Az alábbiakban egy olyan keresési kérelem látható, amely az összes javasolt lekérdezési paramétert és fejlécet tartalmazza. Ha most először bármely, a Bing API-k hívása idő, az ügyfél-ID fejléc nem tartalmazza. Csak akkor használja az ügyfél-azonosítót, ha korábban már meghívott egy Bing API-t, és visszakapott egy ügyfél-azonosítót a felhasználó és az eszköz kombinációjához. 
  
```  
GET https://api.cognitive.microsoft.com/bing/v7.0/images/search?q=sailing+dinghies&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
X-MSEdge-ClientIP: 999.999.999.999  
X-Search-Location: lat:47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com  
```  

Az alábbiakban az előző kérelemre adott válasz látható.

```json
{
    "_type" : "Images",
    "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=73118C8B4E3...",
    "totalEstimatedMatches" : 838,
    "value" : [
        {
            "name" : "File:Rich Passage Minto Sailing Dinghy.jpg - Wikipedia",
            "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=73118C8B4E3...",
            "thumbnailUrl" : "https:\/\/tse1.mm.bing.net\/th?id=OIP.GNarK7m...",
            "datePublished" : "2011-10-29T11:26:00",
            "contentUrl" : "http:\/\/www.bing.com\/cr?IG=73118C8B4E3D4C3...",
            "hostPageUrl" : "http:\/\/www.bing.com\/cr?IG=73118C8B4E3D4C3687...",
            "contentSize" : "79239 B",
            "encodingFormat" : "jpeg",
            "hostPageDisplayUrl" : "en.wikipedia.org\/wiki\/File:Rich_Passage...",
            "width" : 526,
            "height" : 688,
            "thumbnail" : {
                "width" : 229,
                "height" : 300
            },
            "imageInsightsToken" : "ccid_GNarK7ma*mid_CCF85447ADA6...",
            "insightsSourcesSummary" : {
                "shoppingSourcesCount" : 0,
                "recipeSourcesCount" : 0
            },
            "imageId" : "CCF85447ADA6FFF9E96E7DF0B796F7A86E34593",
            "accentColor" : "376094"
        },
        . . .
    ],
    "queryExpansions" : [
        {
            "text" : "Small Sailing Dinghies",
            "displayText" : "Small",
            "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=73118C8B4...",
            "searchLink" : "https:\/\/api.cognitive.microsoft.com\/api...",
            "thumbnail" : {
                "thumbnailUrl" : "https:\/\/tse2.mm.bing.net\/th?q=..."
            }
        },
        . . .
    ],
    "nextOffset" : 10,
    "pivotSuggestions" : [
        {
            "pivot" : "sailing",
            "suggestions" : []
        },
        {
            "pivot" : "dinghies",
            "suggestions" : [
                {
                    "text" : "Sailing Tender",
                    "displayText" : "Tender",
                    "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=73118...",
                    "searchLink" : "https:\/\/api.cognitive.microsoft.com\/api...",
                    "thumbnail" : {
                        "thumbnailUrl" : "https:\/\/tse2.mm.bing.net\/th?q=..."
                    }
                },
                . . .
            ]
        }
    ],
    "displayShoppingSourcesBadges" : false,
    "displayRecipeSourcesBadges" : true
}
```

## <a name="next-steps"></a>További lépések

Próbálja ki az API-t. Lépjen a [Image Search API-tesztelési konzol](https://dev.cognitive.microsoft.com/docs/services/8336afba49a84475ba401758c0dbf749/operations/571fab09dbe2d933e891028f). 

A válaszobjektumok feldolgozásával kapcsolatban lásd: [Keresés az interneten](./search-the-web.md).

Adatok elemzésének, amelyek közé tartozik a lemezkép vagy személyek sikerült felismerni az ábrán például egy képet kapcsolatos részletekért lásd: [képadatok](./image-insights.md).  
  
Rendszerkép található, a közösségi oldalakon pedig kedvelheti kapcsolatos részletekért lásd: [népszerű képek](./trending-images.md).  
