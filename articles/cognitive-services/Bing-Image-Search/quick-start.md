---
title: Lemezképek Search API – első lépések |} Microsoft Docs
description: Ismerkedés a képek Bing keresési API használatával jeleníti meg.
services: cognitive-services
author: swhite-msft
manager: ehansen
ms.assetid: BE2B2F8C-20B5-4E0B-AEC8-EAD505BA4C85
ms.service: cognitive-services
ms.component: bing-image-search
ms.topic: article
ms.date: 04/15/2017
ms.author: scottwhi
ms.openlocfilehash: 9e211cf5acd17ab80948d0b7161bdd2a9220c4a6
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/23/2018
ms.locfileid: "35347031"
---
# <a name="your-first-images-search-query"></a>Az első lemezképek keresési lekérdezés

Mielőtt az első hívás, a Bing keresési kognitív szolgáltatások előfizetés kulcs lekérése kell. Ahhoz, hogy a kulcs, lásd: [kognitív szolgáltatások próbálja](https://azure.microsoft.com/try/cognitive-services/?api=bing-image-search-api).

Ahhoz, hogy a kép találatok, elküldése egy GET kérelmet a következő végponthoz:  
  
```
https://api.cognitive.microsoft.com/bing/v7.0/images/search
```
  
A kérelmet HTTPS protokollt kell használnia.

Azt javasoljuk, hogy az összes kérelem-kiszolgálótól származik. A kulcs terjesztése ügyfélalkalmazás részeként egy rosszindulatú külső elérésére több lehetőséget biztosít. Is, a kiszolgálótól érkező hívás egyetlen frissítési pontot biztosít az API-t a jövőbeli verzióiban.

A kérelemnek meg kell adnia a [q](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#query) lekérdezési paraméter, amely tartalmazza a felhasználó keresési kifejezés. Bár nem kötelező, a kérelem is kell megadnia a [mkt](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#mkt) lekérdezési paraméter, amely azonosítja a piacon, ha azt szeretné, hogy az eredmények származnia. Nem kötelező listájának lekérdezési paramétert, mint `freshness` és `size`, lásd: [lekérdezésparamétereket](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#query-parameters). Az összes lekérdezés paraméterértékek URL-kódolású kell lennie.  
  
A kérelemnek meg kell adnia a [Ocp-Apim-előfizetés-kulcs](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#subscriptionkey) fejléc. Bár nem kötelező, meg hosszúan is adja meg a következő fejléc:  
  
-   [Felhasználói ügynök](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#useragent)  
-   [X-MSEdge-ClientID](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#clientid)  
-   [X-keresési-Ügyfélip](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#clientip)  
-   [X keresése](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#location)  

Az ügyfél IP-cím és a hely fejlécek fontosak hely kompatibilis tartalom visszaküldésével.  

Az összes kérés- és válaszfejlécekről listájáért lásd: [fejlécek](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#headers).

## <a name="the-request"></a>A kérelem

Az alábbiakban látható a keresési kérelmeket, amelyek a javasolt lekérdezés-paraméterek és a fejlécek tartalmazza. Ha az első alkalommal a bármely, a Bing API-k hívása, nem tartalmaznak, az ügyfél Tevékenységazonosító fejlécet. Az ügyfél-azonosító csak tartalmazzák, ha korábban már hívott a Bing API és a Bing visszaadott egy ügyfél-Azonosítót, a felhasználó és eszköz kombinációja. 
  
```  
GET https://api.cognitive.microsoft.com/bing/v7.0/images/search?q=sailing+dinghies&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
X-MSEdge-ClientIP: 999.999.999.999  
X-Search-Location: lat:47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com  
```  

Az alábbiakban látható a korábbi kérelemre adott válasz.

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

Próbálja ki az API-t. Ugrás a [keresési API-tesztelési konzol kép](https://dev.cognitive.microsoft.com/docs/services/8336afba49a84475ba401758c0dbf749/operations/571fab09dbe2d933e891028f). 

A válasz objektumok fel kapcsolatos részletekért lásd: [a webes keresés](./search-the-web.md).

További információk a nyerek betekintést az adatokba, amelyek közé tartoznak a lemezkép vagy személyek felismert az ábrán például kép: [kép Insights](./image-insights.md).  
  
A közösségi média vannak trendek lemezképeket kapcsolatos részletekért lásd: [trendek képek](./trending-images.md).  
