---
title: 'Rövid útmutató: Bing Autosuggest API'
titlesuffix: Azure Cognitive Services
description: Bemutatja a Bing Autosuggest API használatának első lépéseit.
services: cognitive-services
author: swhite-msft
manager: cgronlun
ms.service: cognitive-services
ms.component: bing-autosuggest
ms.topic: quickstart
ms.date: 04/15/2017
ms.author: scottwhi
ms.openlocfilehash: 446d271854a4e45bcea8c261a0dc078e549f8229
ms.sourcegitcommit: 26cc9a1feb03a00d92da6f022d34940192ef2c42
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/06/2018
ms.locfileid: "48830753"
---
# <a name="quickstart-making-your-first-autosuggest-query"></a>Rövid útmutató: Az első Autosuggest-lekérdezés elvégzése

Ahhoz, hogy elvégezhesse az első hívást, be kell szereznie egy Cognitive Services-előfizetői azonosítót. Az előfizetői azonosító beszerzéséhez lásd [A Cognitive Services kipróbálása](https://azure.microsoft.com/try/cognitive-services/?api=autosuggest-api) témakört.

A webes keresés eredményeinek lekéréséhez egy GET kérelmet kell küldeni a következő végpontra:

```http
https://api.cognitive.microsoft.com/bing/v5.0/Suggestions
```

> [!NOTE]
> 7-es előzetes verziójú végpont:
>
> ```http
> https://api.cognitive.microsoft.com/bing/v7.0/Suggestions
> ```

A kérelemnek a HTTPS protokollt kell használnia.

Javasoljuk, hogy minden kérelem egy kiszolgálóról induljon. Az azonosítónak egy ügyfélalkalmazás részeként való terjesztése több lehetőséget ad arra, hogy rosszindulatú külső felek hozzáférjenek az azonosítóhoz. Emellett a hívások kiszolgálóról való indítása egyetlen frissítési pontot teremt az API későbbi verziói számára.

A kérelemnek tartalmaznia kell a [q](https://docs.microsoft.com/rest/api/cognitiveservices/bing-autosuggest-api-v5-reference#query) lekérdezési paramétert, amely a felhasználó részleges keresési kifejezését adja meg. Nem kötelező, de a kérelemnek érdemes tartalmaznia egy [mkt](https://docs.microsoft.com/rest/api/cognitiveservices/bing-autosuggest-api-v5-reference#mkt) lekérdezési paramétert is, amely azonosítja a piacot, ahonnan eredményeket szeretnénk kapni. Az opcionális lekérdezési paraméterek listáját lásd a [lekérdezési paramétereket](https://docs.microsoft.com/rest/api/cognitiveservices/bing-autosuggest-api-v5-reference#query-parameters) ismertető cikkben. Minden lekérdezési paraméter értékének URL-kódolásúnak kell lennie.

A kérelemnek tartalmaznia kell az [Ocp-Apim-Subscription-Key](https://docs.microsoft.com/rest/api/cognitiveservices/bing-autosuggest-api-v5-reference#subscriptionkey) fejlécet. Nem kötelező, de javasolt a következő fejlécek megadása is:

- [User-Agent](https://docs.microsoft.com/rest/api/cognitiveservices/bing-autosuggest-api-v5-reference#useragent)
- [X-MSEdge-ClientID](https://docs.microsoft.com/rest/api/cognitiveservices/bing-autosuggest-api-v5-reference#clientid)
- [X-Search-ClientIP](https://docs.microsoft.com/rest/api/cognitiveservices/bing-autosuggest-api-v5-reference#clientip)
- [X-Search-Location](https://docs.microsoft.com/rest/api/cognitiveservices/bing-autosuggest-api-v5-reference#location)

Az ügyfél IP-címe és helye fontos a helyfüggő tartalmak visszaadása szempontjából.

Az összes kérelem- és válaszfejléc listáját lásd a [Fejlécek](https://docs.microsoft.com/rest/api/cognitiveservices/bing-autosuggest-api-v5-reference#headers) cikkben.

## <a name="the-request"></a>A kérelem

A kérelemnek tartalmaznia kell az összes javasolt lekérdezési paramétert és fejlécet. Ezt az API-t hívja meg minden alkalommal, amikor a felhasználó beír egy új karaktert a keresőmezőbe. A lekérdezési sztring teljessége hatással van az API által visszaadott javasolt lekérdezési kifejezések relevanciájára. Minél teljesebb a lekérdezési sztring, annál relevánsabb lesz a javasolt lekérdezési kifejezések listája. Az *s* sztringre például az API valószínűleg kevésbé releváns javaslatokat fog visszaadni, mint a *sailing dinghies* (vitorlás csónakok) sztringre. 

A következő példa egy olyan kérést mutat be, amely a *sail* sztringre adja vissza a javasolt lekérdezési sztringeket.

```http
GET https://api.cognitive.microsoft.com/bing/v5.0/suggestions?q=sail&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
X-MSEdge-ClientIP: 999.999.999.999  
X-Search-Location: lat:47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com  
```

> [!NOTE]
> 7-es előzetes verziójú kérelem:

> ```http
> GET https://api.cognitive.microsoft.com/bing/v7.0/suggestions?q=sail&mkt=en-us HTTP/1.1
> Ocp-Apim-Subscription-Key: 123456789ABCDE
> X-MSEdge-ClientIP: 999.999.999.999
> X-Search-Location: lat:47.60357;long:-122.3295;re:100
> X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>
> Host: api.cognitive.microsoft.com
> ```

Ha első alkalommal hívja meg bármelyik Bing API-t, ne használja az ügyfél-azonosító fejlécét. Csak akkor használja az ügyfél-azonosító fejlécét, ha korábban már meghívott egy Bing API-t, és visszakapott egy ügyfél-azonosítót a felhasználó és az eszköz kombinációjához.

Alább az előző kérelemre adott válasz látható. A válasz tartalmaz egy webes javaslatcsoportot a keresési lekérdezési javaslatok listájával. Minden javaslat tartalmaz egy `displayText`, `query` és `url` mezőt.

A `displayText` mező tartalmazza a javasolt lekérdezést, amelyet a keresőmező legördülő listájának feltöltéséhez használ. A válaszban szereplő összes javaslatot meg kell jelenítenie az adott sorrendben.  

Ha a felhasználó kiválaszt egy lekérdezést a legördülő listából, Ön használhatja a lekérdezési sztringet a `query` mezőben a [Bing Search API](../bing-web-search/search-the-web.md) meghívásához és a találatok megjelenítéséhez. Használhatja az URL-címet is az `url` mezőben, ha a felhasználót a Bing keresési eredményeinek oldalára szeretné küldeni.

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

Próbálja ki az API-t. Lépjen az [Autosuggest API-tesztelési konzolra](https://dev.cognitive.microsoft.com/docs/services/56c7694ecf5ff801a090fbd1/operations/56c769a2cf5ff801a090fbd2).

A válaszobjektumok feldolgozásával kapcsolatban lásd: [Javasolt keresés kifejezések lekérése](./get-suggested-search-terms.md).
