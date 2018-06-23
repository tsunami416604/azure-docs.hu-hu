---
title: Entitások Search API – első lépések |} Microsoft Docs
description: Első lépések entitások Bing keresési API használatával jeleníti meg.
services: cognitive-services
author: swhite-msft
manager: ehansen
ms.assetid: B206A254-B7E9-49FF-AFD5-87B1E4D6D30B
ms.service: cognitive-services
ms.component: bing-entity-search
ms.topic: article
ms.date: 07/06/2017
ms.author: scottwhi
ms.openlocfilehash: 12031d2447920c7e2d6180f35cf4fb29aa1b6150
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/23/2018
ms.locfileid: "35346967"
---
# <a name="making-your-first-entities-request"></a>Az első entitások kérelem elvégzése

Az entitás Search API keresési lekérdezést küld a Bing, és lekérdezi az eredmények, amelyek tartalmazzák az entitásokat és helyek. Hely eredmények tartalmazzák a éttermekben, Szálloda vagy más helyi vállalatok számára. A helyek a lekérdezés adhat meg a helyi vállalati nevét, vagy azt (például a közeli éttermekben) listáját kérje meg. Entitás eredmények tartalmazza a személyek, helyek vagy dolgot. Ebben a környezetben helye sport létesítmények, állapotok, országok, stb. 

## <a name="first-steps"></a>Első lépések

Mielőtt az első hívás, kell egy kognitív szolgáltatások előfizetés kulcs beszerzése. Ahhoz, hogy a kulcs, lásd: [kognitív szolgáltatások próbálja](https://azure.microsoft.com/try/cognitive-services/?api=bing-entities-search-api). (Ha az entitások Search API nem felső részén látható, kattintson a **keresési** lapra, és görgessen lefelé, amíg meg nem jelenik azt.)

## <a name="the-endpoint"></a>A végpont

Entitás, és helyezze el a keresési eredmények között, GET kérés küldése a következő végponthoz:  
  
```
https://api.cognitive.microsoft.com/bing/v7.0/entities
```

A kérelmet HTTPS protokollt kell használnia.

Azt javasoljuk, hogy az összes kérelem-kiszolgálótól származik. A kulcs terjesztése ügyfélalkalmazás részeként rosszindulatú harmadik fél elérésére több lehetőséget biztosít a. Is, a kiszolgálótól érkező hívás egyetlen frissítési pontot biztosít az API-t a jövőbeli verzióiban.

## <a name="specifying-query-parameters-and-headers"></a>Lekérdezés-paraméterek és a fejlécek megadása

A kérelemnek meg kell adnia a [q](https://docs.microsoft.com/rest/api/cognitiveservices/bing-entities-api-v7-reference#query) lekérdezési paraméter, amely tartalmazza a felhasználó keresési kifejezés. A kérelem is meg kell adnia a [mkt](https://docs.microsoft.com/rest/api/cognitiveservices/bing-entities-api-v7-reference#mkt) lekérdezési paraméter, amely azonosítja a piacon, ha azt szeretné, hogy az eredmények származnia. Nem kötelező lekérdezési paraméterek listáját lásd: [lekérdezésparamétereket](https://docs.microsoft.com/rest/api/cognitiveservices/bing-entities-api-v7-reference#query-parameters). URL-címet az összes lekérdezési paraméterek kódolása.  
  
A kérelemnek meg kell adnia a [Ocp-Apim-előfizetés-kulcs](https://docs.microsoft.com/rest/api/cognitiveservices/bing-entities-api-v7-reference#subscriptionkey) fejléc. Bár nem kötelező, meg hosszúan is adja meg a következő fejléc:  
  
-   [Felhasználói ügynök](https://docs.microsoft.com/rest/api/cognitiveservices/bing-entities-api-v7-reference#useragent)  
-   [X-MSEdge-ClientID](https://docs.microsoft.com/rest/api/cognitiveservices/bing-entities-api-v7-reference#clientid)  
-   [X-MSEdge-Ügyfélip](https://docs.microsoft.com/rest/api/cognitiveservices/bing-entities-api-v7-reference#clientip)  
-   [X keresése](https://docs.microsoft.com/rest/api/cognitiveservices/bing-entities-api-v7-reference#location)  

Az ügyfél IP-cím és a hely fejlécek fontosak hely kompatibilis tartalom visszaküldésével.  

Az összes kérés- és válaszfejlécekről listájáért lásd: [fejlécek](https://docs.microsoft.com/rest/api/cognitiveservices/bing-entities-api-v7-reference#headers).

## <a name="the-request"></a>A kérelem

Az alábbi példában látható, amely tartalmazza a javasolt lekérdezés-paraméterek és a fejlécek entitások kérelmet. 

```  
GET https://api.cognitive.microsoft.com/bing/v7.0/entities?q=mount+rainier&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)  
X-Search-ClientIP: 999.999.999.999  
X-Search-Location: lat:47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com  
```  

Ha most először hívja a Bing API-k bármelyikét, az ügyfél-azonosító fejléc nem tartalmaz. Az ügyfél-azonosító csak tartalmazzák, ha korábban már hívott a Bing API és a Bing visszaadott egy ügyfél-Azonosítót, a felhasználó és eszköz kombinációja.

## <a name="the-response"></a>A válasz

Az alábbiakban látható a korábbi kérelemre adott válasz. A példában is látható a Bing-specifikus response fejlécekkel együtt. A válasz objektumra vonatkozó információkért lásd: [SearchResponse](https://docs.microsoft.com/rest/api/cognitiveservices/bing-entities-api-v7-reference#searchresponse).

```
BingAPIs-TraceId: 76DD2C2549B94F9FB55B4BD6FEB6AC
X-MSEdge-ClientID: 1C3352B306E669780D58D607B96869
BingAPIs-Market: en-US

{
    "_type" : "SearchResponse",
    "queryContext" : {
        "originalQuery" : "mount rainier"
    },
    "entities" : {
        "queryScenario" : "DominantEntity",
        "value" : [{
            "contractualRules" : [{
                "_type" : "ContractualRules\/LicenseAttribution",
                "targetPropertyName" : "description",
                "mustBeCloseToContent" : true,
                "license" : {
                    "name" : "CC-BY-SA",
                    "url" : "http:\/\/creativecommons.org\/licenses\/by-sa\/3.0\/"
                },
                "licenseNotice" : "Text under CC-BY-SA license"
            },
            {
                "_type" : "ContractualRules\/LinkAttribution",
                "targetPropertyName" : "description",
                "mustBeCloseToContent" : true,
                "text" : "en.wikipedia.org",
                "url" : "http:\/\/en.wikipedia.org\/wiki\/Mount_Rainier"
            },
            {
                "_type" : "ContractualRules\/MediaAttribution",
                "targetPropertyName" : "image",
                "mustBeCloseToContent" : true,
                "url" : "http:\/\/en.wikipedia.org\/wiki\/Mount_Rainier"
            }],
            "webSearchUrl" : "https:\/\/www.bing.com\/search?q=Mount%20Rainier...",
            "name" : "Mount Rainier",
            "image" : {
                "name" : "Mount Rainier",
                "thumbnailUrl" : "https:\/\/www.bing.com\/th?id=A21890c0e1f...",
                "provider" : [{
                    "_type" : "Organization",
                    "url" : "http:\/\/en.wikipedia.org\/wiki\/Mount_Rainier"
                }],
                "hostPageUrl" : "http:\/\/upload.wikimedia.org\/wikipedia...",
                "width" : 110,
                "height" : 110
            },
            "description" : "Mount Rainier, Mount Tacoma, or Mount Tahoma is the highest...",
            "entityPresentationInfo" : {
                "entityScenario" : "DominantEntity",
                "entityTypeHints" : ["Attraction"],
                "entityTypeDisplayHint" : "Mountain"
            },
            "bingId" : "9ae3e6ca-81ea-6fa1-ffa0-42e1d78906"
        }]
    }
}
```



## <a name="next-steps"></a>További lépések

Próbálja ki az API-t. Ugrás a [entitások keresési API-tesztelési konzol](https://dev.cognitive.microsoft.com/docs/services/7a3fb374be374859a823b79fd938cc65/). 

A válasz objektumok fel kapcsolatos részletekért lásd: [entitás és a helyek a webes keresés](./search-the-web.md).

Olvassa el [Bing használatát és a megjelenített követelmények](./use-display-requirements.md) , nem megszakítja a keresési eredmények használatára vonatkozó szabályok egyike.
