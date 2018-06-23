---
title: Bing helyesírás ellenőrizze API – első lépések |} Microsoft Docs
description: Első lépések a Bing helyesírás-ellenőrzése API használatával jeleníti meg.
services: cognitive-services
author: swhite-msft
manager: ehansen
ms.assetid: AF8EB1F0-386D-4555-9354-735611435F04
ms.service: cognitive-services
ms.component: bing-spell-check
ms.topic: article
ms.date: 06/21/2016
ms.author: scottwhi
ms.openlocfilehash: cae8353e5be6e70eca90e5995b29b6774fc7d6a9
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/23/2018
ms.locfileid: "35346951"
---
# <a name="your-first-spell-check-request"></a>Az első helyesírás kérés ellenőrzéséhez.

Mielőtt az első hívás, kell egy kognitív szolgáltatások előfizetés kulcs beszerzése. Ahhoz, hogy a kulcs, lásd: [kognitív szolgáltatások próbálja](https://azure.microsoft.com/try/cognitive-services/?api=spellcheck-api).

Ellenőrizze a helyesírást és nyelvtani hibákat egy szöveges karakterlánc, elküldése egy GET kérelmet a következő végponthoz:  
  
```
https://api.cognitive.microsoft.com/bing/v5.0/spellcheck
```

> [!NOTE]
> V7 előnézeti végpont:
> 
> ```
> https://api.cognitive.microsoft.com/bing/v7.0/spellcheck
> ```  
  
A kérelmet HTTPS protokollt kell használnia.

Azt javasoljuk, hogy az összes kérelem-kiszolgálótól származik. A kulcs terjesztése ügyfélalkalmazás részeként egy rosszindulatú külső elérésére több lehetőséget biztosít. Is, a kiszolgálótól érkező hívás egyetlen frissítési pontot biztosít az API-t a jövőbeli verzióiban.

A kérelemnek meg kell adnia a [szöveg](https://docs.microsoft.com/rest/api/cognitiveservices/bing-spell-check-api-v5-reference#text) lekérdezési paraméter, amely igazolása a szöveges karakterláncot tartalmaz. Bár nem kötelező, a kérelem is kell megadnia a [mkt](https://docs.microsoft.com/rest/api/cognitiveservices/bing-spell-check-api-v5-reference#mkt) lekérdezési paraméter, amely azonosítja a piacon, ha azt szeretné, hogy az eredmények származnia. Nem kötelező listájának lekérdezési paramétert, mint `mode`, lásd: [lekérdezésparamétereket](https://docs.microsoft.com/rest/api/cognitiveservices/bing-spell-check-api-v5-reference#query-parameters). Az összes lekérdezés paraméterértékek URL-kódolású kell lennie.  
  
A kérelemnek meg kell adnia a [Ocp-Apim-előfizetés-kulcs](https://docs.microsoft.com/rest/api/cognitiveservices/bing-spell-check-api-v5-reference#subscriptionkey) fejléc. Bár nem kötelező, meg hosszúan is adja meg a következő fejléc:  
  
-   [Felhasználói ügynök](https://docs.microsoft.com/rest/api/cognitiveservices/bing-spell-check-api-v5-reference#useragent)  
-   [X-MSEdge-ClientID](https://docs.microsoft.com/rest/api/cognitiveservices/bing-spell-check-api-v5-reference#clientid)  
-   [X-keresési-Ügyfélip](https://docs.microsoft.com/rest/api/cognitiveservices/bing-spell-check-api-v5-reference#clientip)  
-   [X keresése](https://docs.microsoft.com/rest/api/cognitiveservices/bing-spell-check-api-v5-reference#location)  

Az összes kérés- és válaszfejlécekről listájáért lásd: [fejlécek](https://docs.microsoft.com/rest/api/cognitiveservices/bing-spell-check-api-v5-reference#headers).

## <a name="the-request"></a>A kérelem

Az alábbiakban látható a kérelmeket, amelyek a javasolt lekérdezés-paraméterek és a fejlécek tartalmazza. Ha most először hívja a Bing API-k bármelyikét, az ügyfél-azonosító fejléc nem tartalmaz. Az ügyfél-azonosító csak tartalmazzák, ha korábban már hívott a Bing API és a Bing visszaadott egy ügyfél-Azonosítót, a felhasználó és eszköz kombinációja. 
  
```  
GET https://api.cognitive.microsoft.com/bing/v5.0/spellcheck?text=when+its+your+turn+turn,+john,+come+runing&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)  
X-Search-ClientIP: 999.999.999.999  
X-Search-Location: lat:47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com  
```  

> [!NOTE]
> V7 Preview kérelem:

> ```  
> GET https://api.cognitive.microsoft.com/bing/v7.0/spellcheck?text=when+its+your+turn+turn,+john,+come+runing&mkt=en-us HTTP/1.1
> Ocp-Apim-Subscription-Key: 123456789ABCDE  
> X-MSEdge-ClientIP: 999.999.999.999  
> X-Search-Location: lat:47.60357;long:-122.3295;re:100  
> X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
> Host: api.cognitive.microsoft.com  
> ```  

Az alábbiakban látható a korábbi kérelemre adott válasz. A példában is látható a Bing-specifikus response fejlécekkel együtt.

```
BingAPIs-TraceId: 76DD2C2549B94F9FB55B4BD6FEB6AC
X-MSEdge-ClientID: 1C3352B306E669780D58D607B96869
BingAPIs-Market: en-US

{  
    "_type" : "SpellCheck",  
    "flaggedTokens" : [{  
        "offset" : 5,  
        "token" : "its",  
        "type" : "UnknownToken",  
        "suggestions" : [{  
            "suggestion" : "it's",  
            "score" : 1  
        }]  
    },  
    {  
        "offset" : 25,  
        "token" : "john",  
        "type" : "UnknownToken",  
        "suggestions" : [{  
            "suggestion" : "John",  
            "score" : 1  
        }]  
    },  
    {  
        "offset" : 19,  
        "token" : "turn",  
        "type" : "RepeatedToken",  
        "suggestions" : [{  
            "suggestion" : "",  
            "score" : 1  
        }]  
    },  
    {  
        "offset" : 35,  
        "token" : "runing",  
        "type" : "UnknownToken",  
        "suggestions" : [{  
            "suggestion" : "running",  
            "score" : 1  
        }]  
    }]  
}  
```  


## <a name="next-steps"></a>További lépések

Próbálja ki az API-t. Ugrás a [helyesírás-ellenőrzés API tesztelési konzol](https://dev.cognitive.microsoft.com/docs/services/56e73033cf5ff80c2008c679/operations/57855119bca1df1c647bc358). 

A válasz objektumok fel kapcsolatos részletekért lásd: [helyesírás ellenőrzés szövegeit tartalmazó karakterláncok](./proof-text.md).

