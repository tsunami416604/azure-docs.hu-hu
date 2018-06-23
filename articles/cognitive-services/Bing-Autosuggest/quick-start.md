---
title: Automatikus kiegészítési API gyors üzembe helyezés |} Microsoft Docs
description: Első lépések a Bing automatikus kiegészítési API használatával jeleníti meg.
services: cognitive-services
author: swhite-msft
manager: ehansen
ms.assetid: 1482E781-7352-4A3F-B1D5-B896381348C4
ms.service: cognitive-services
ms.component: bing-autosuggest
ms.topic: article
ms.date: 04/15/2017
ms.author: scottwhi
ms.openlocfilehash: a7b54a1fb0b7c76eb72097357a6b51aa02e6e2fd
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/23/2018
ms.locfileid: "35349003"
---
# <a name="making-your-first-autosuggest-query"></a>Az első automatikus javaslatokba lekérdezés végrehajtása

Mielőtt az első hívás, kell egy kognitív szolgáltatások előfizetés kulcs beszerzése. Ahhoz, hogy a kulcs, lásd: [kognitív szolgáltatások próbálja](https://azure.microsoft.com/try/cognitive-services/?api=autosuggest-api).

Ahhoz, hogy a webes keresés eredményeit, elküldése egy GET kérelmet a következő végponthoz:

```http
https://api.cognitive.microsoft.com/bing/v5.0/Suggestions
```

> [!NOTE]
> V7 előnézeti végpont:
>
> ```http
> https://api.cognitive.microsoft.com/bing/v7.0/Suggestions
> ```

A kérelmet HTTPS protokollt kell használnia.

Azt javasoljuk, hogy az összes kérelem-kiszolgálótól származik. A kulcs terjesztése ügyfélalkalmazás részeként egy rosszindulatú külső elérésére több lehetőséget biztosít. Is, a kiszolgálótól érkező hívás egyetlen frissítési pontot biztosít az API-t a jövőbeli verzióiban.

A kérelemnek meg kell adnia a [q](https://docs.microsoft.com/rest/api/cognitiveservices/bing-autosuggest-api-v5-reference#query) lekérdezési paraméter, amely tartalmazza a felhasználó részleges keresési kifejezés. Bár nem kötelező, a kérelem is kell megadnia a [mkt](https://docs.microsoft.com/rest/api/cognitiveservices/bing-autosuggest-api-v5-reference#mkt) lekérdezési paraméter, amely azonosítja a piacon, ha azt szeretné, hogy az eredmények származnia. Nem kötelező lekérdezési paraméterek listáját lásd: [lekérdezésparamétereket](https://docs.microsoft.com/rest/api/cognitiveservices/bing-autosuggest-api-v5-reference#query-parameters). Az összes lekérdezés paraméterértékek URL-kódolású kell lennie.

A kérelemnek meg kell adnia a [Ocp-Apim-előfizetés-kulcs](https://docs.microsoft.com/rest/api/cognitiveservices/bing-autosuggest-api-v5-reference#subscriptionkey) fejléc. Bár nem kötelező, meg hosszúan is adja meg a következő fejléc:

- [Felhasználói ügynök](https://docs.microsoft.com/rest/api/cognitiveservices/bing-autosuggest-api-v5-reference#useragent)
- [X-MSEdge-ClientID](https://docs.microsoft.com/rest/api/cognitiveservices/bing-autosuggest-api-v5-reference#clientid)
- [X-keresési-Ügyfélip](https://docs.microsoft.com/rest/api/cognitiveservices/bing-autosuggest-api-v5-reference#clientip)
- [X keresése](https://docs.microsoft.com/rest/api/cognitiveservices/bing-autosuggest-api-v5-reference#location)

Az ügyfél IP-cím és a hely fejlécek fontosak hely kompatibilis tartalom visszaküldésével.

Az összes kérés- és válaszfejlécekről listájáért lásd: [fejlécek](https://docs.microsoft.com/rest/api/cognitiveservices/bing-autosuggest-api-v5-reference#headers).

## <a name="the-request"></a>A kérelem

A kérelem tartalmaznia kell a javasolt lekérdezés-paraméterek és a fejlécet. Ez az API meghívta minden alkalommal, amikor a felhasználó beír egy új karakter a keresési mezőbe. A lekérdezési karakterlánc hatások relevanciájának a javasolt lekérdezés feltételeket, amelyek az API-t teljességéről adja vissza. Minél több fejezze be a lekérdezési karakterláncot, a több megfelelő listáját, hogy a lekérdezés feltételeket javasolt. Például, ha a javaslatok, amelyek az API-t a térhetnek vissza *s* valószínűleg a kevésbé fontos, mint a adja vissza a lekérdezések *kihajózás dinghies*. 

A következő példa bemutatja a kérelmeket, amelyek adja vissza a javasolt lekérdezési karakterláncokban használható *hajóznak*.

```http
GET https://api.cognitive.microsoft.com/bing/v5.0/suggestions?q=sail&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
X-MSEdge-ClientIP: 999.999.999.999  
X-Search-Location: lat:47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com  
```

> [!NOTE]
> V7 Preview kérelem:

> ```http
> GET https://api.cognitive.microsoft.com/bing/v7.0/suggestions?q=sail&mkt=en-us HTTP/1.1
> Ocp-Apim-Subscription-Key: 123456789ABCDE
> X-MSEdge-ClientIP: 999.999.999.999
> X-Search-Location: lat:47.60357;long:-122.3295;re:100
> X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>
> Host: api.cognitive.microsoft.com
> ```

Ha most először hívja a Bing API-k bármelyikét, az ügyfél-azonosító fejléc nem tartalmaz. Az ügyfél Tevékenységazonosító fejlécet csak tartalmazzák, ha korábban már hívott a Bing API és a Bing visszaadott egy ügyfél-Azonosítót, a felhasználó és eszköz kombinációja.

Az alábbiakban látható a fenti kérelemre adott válasz. A válasz keresési lekérdezés javaslatok listáját tartalmazó webes javaslat csoportot tartalmazza. Minden javaslat tartalmaz egy `displayText`, `query`, és `url` mező.

A `displayText` mező tartalmazza a javasolt lekérdezés szeretné használni a keresőmezőbe legördülő lista feltöltéséhez. Meg kell jelenítenie minden javaslat, amely a válasz tartalmazza, és a megadott sorrendben.  

Ha a felhasználó egy lekérdezést a legördülő listából gombra kattint, a lekérdezési karakterláncok használhatja a `query` mező hívni a [Bing keresési API](../bing-web-search/search-the-web.md) és megjeleníteni az eredményeket. Másik lehetőségként használhatja az URL-címet a `url` Ha szeretne küldeni a felhasználónak a Bing keresési eredmények.

```  
BingAPIs-TraceId: 76DD2C2549B94F9FB55B4BD6FEB6AC
X-MSEdge-ClientID: 1C3352B306E669780D58D607B96869
BingAPIs-Market: en-US

{
    "_type" : "Suggestions",
    "queryContext" : {
        "originalQuery" : "sail"
    },
    "suggestionGroups" : [{
        "name" : "Web",
        "searchSuggestions" : [{
            "url" : "https:\/\/www.bing.com\/search?q=sailing+lessons+seattle&FORM=USBAPI",
            "displayText" : "sailing lessons seattle",
            "query" : "sailing lessons seattle",
            "searchKind" : "WebSearch"
        },
        {
            "url" : "https:\/\/www.bing.com\/search?q=sailor+moon+news&FORM=USBAPI",
            "displayText" : "sailor moon news",
            "query" : "sailor moon news",
            "searchKind" : "WebSearch"
        },
        {
            "url" : "https:\/\/www.bing.com\/search?q=sailor+jack%27s+lincoln+city&FORM=USBAPI",
            "displayText" : "sailor jack's lincoln city",
            "query" : "sailor jack's lincoln city",
            "searchKind" : "WebSearch"
        },
        {
            "url" : "https:\/\/www.bing.com\/search?q=sailing+anarchy&FORM=USBAPI",
            "displayText" : "sailing anarchy",
            "query" : "sailing anarchy",
            "searchKind" : "WebSearch"
        },
        {
            "url" : "https:\/\/www.bing.com\/search?q=sailboats+for+sale&FORM=USBAPI",
            "displayText" : "sailboats for sale",
            "query" : "sailboats for sale",
            "searchKind" : "WebSearch"
        },
        {
            "url" : "https:\/\/www.bing.com\/search?q=sailstn.mylabsplus.com&FORM=USBAPI",
            "displayText" : "sailstn.mylabsplus.com",
            "query" : "sailstn.mylabsplus.com",
            "searchKind" : "WebSearch"
        },
        {
            "url" : "https:\/\/www.bing.com\/search?q=sailusfood&FORM=USBAPI",
            "displayText" : "sailusfood",
            "query" : "sailusfood",
            "searchKind" : "WebSearch"
        },
        {
            "url" : "https:\/\/www.bing.com\/search?q=sailboats+for+sale+seattle&FORM=USBAPI",
            "displayText" : "sailboats for sale seattle",
            "query" : "sailboats for sale seattle",
            "searchKind" : "WebSearch"
        }]
    }]
}
```

## <a name="next-steps"></a>További lépések

Próbálja ki az API-t. Ugrás a [automatikus kiegészítési API-tesztelési konzol](https://dev.cognitive.microsoft.com/docs/services/56c7694ecf5ff801a090fbd1/operations/56c769a2cf5ff801a090fbd2).

A válasz objektumok fel kapcsolatos részletekért lásd: [javasolt keresőkifejezéseket első](./get-suggested-search-terms.md).
