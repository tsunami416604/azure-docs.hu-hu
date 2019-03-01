---
title: Kérések küldését a Bing Autosuggest API
titlesuffix: Azure Cognitive Services
description: Ismerje meg, hogyan-kérelmeket küldjön a Bing Autosuggest API.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-autosuggest
ms.topic: quickstart
ms.date: 02/20/2019
ms.author: scottwhi
ms.openlocfilehash: 597ef48fd7499a9d33b214b182d6dd1354756cdf
ms.sourcegitcommit: 15e9613e9e32288e174241efdb365fa0b12ec2ac
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/28/2019
ms.locfileid: "57011877"
---
# <a name="sending-requests-to-the-bing-autosuggest-api"></a>A Bing Autosuggest API kérések küldését.

Ha az alkalmazás lekérdezéseket küld a Bing Search APIs valamelyik, a Bing Autosuggest API segítségével a felhasználók keresési élmény javításához. A Bing Autosuggest API kifejezést a keresőmezőbe a részleges lekérdezési karakterlánc alapján javasolt lekérdezések listáját adja vissza. A keresőmezőbe az alkalmazás megadott karakterek, mivel javaslatok megjelenítheti egy legördülő listában. Ez a cikk segítségével további tudnivalók a kérések küldését az API-t.

## <a name="bing-autosuggest-api-endpoint"></a>A Bing Autosuggest API-végpont

A **a Bing Autosuggest API** tartalmaz egy végpont, amely egy részleges keresési kifejezést a javasolt lekérdezések listáját adja vissza.

Javasolt lekérdezések, a Bing API-val beszerzéséhez küldjön egy `GET` kérelem a következő végpont. A fejlécek és URL-paraméterek használatával további specifikációk meghatározása.

**Végpont:** Értéket ad vissza JSON eredményként, amely a felhasználói bevitel határozzák meg a keresési javaslatok `?q=""`.

```http
GET https://api.cognitive.microsoft.com/bing/v7.0/Suggestions 
```

További információk a fejlécek, paraméterek, piaci kódok, válaszobjektumok, hibák stb, tekintse meg a [a Bing Autosuggest API 7-es verziója](https://docs.microsoft.com/rest/api/cognitiveservices/bing-autosuggest-api-v7-reference) referencia.

A **Bing** API-kat támogatja a keresési műveleteket, amelyek a típusuk eredményeket adja vissza. Végpontok keresése az összes eredmény JSON-válasz objektumként adja vissza.
Minden végpontok lekérdezések támogatására, amelyeket egy adott nyelvhez és/vagy a helyet a földrajzi hosszúság, szélesség és keresési radius vissza.

Minden végpont által támogatott paraméterekkel kapcsolatos részletes információkért tekintse meg az egyes referenciaoldalait.
Az automatikus kiegészítési API-t használó alapszintű kérések példákért lásd [Autosuggest útmutatóink](https://docs.microsoft.com/azure/cognitive-services/Bing-Autosuggest).

## <a name="bing-autosuggest-api-requests"></a>A Bing Autosuggest API-kérelmek

> [!NOTE]
> A Bing Autosuggest API kéréseket a HTTPS protokollt kell használnia.

Javasoljuk, hogy minden kérelem egy kiszolgálóról induljon. A kulcs terjesztése egy ügyfélalkalmazás részeként további lehetőség rosszindulatú külső-hozzáférést biztosít. Ezenkívül kiszolgálótól érkező hívások egyetlen frissítési pontot biztosít a jövőbeli frissítések.

A kérelemnek tartalmaznia kell a [q](https://docs.microsoft.com/rest/api/cognitiveservices/bing-autosuggest-api-v5-reference#query) lekérdezési paramétert, amely a felhasználó részleges keresési kifejezését adja meg. Nem kötelező, de a kérelemnek érdemes tartalmaznia egy [mkt](https://docs.microsoft.com/rest/api/cognitiveservices/bing-autosuggest-api-v5-reference#mkt) lekérdezési paramétert is, amely azonosítja a piacot, ahonnan eredményeket szeretnénk kapni. Az opcionális lekérdezési paraméterek listáját lásd a [lekérdezési paramétereket](https://docs.microsoft.com/rest/api/cognitiveservices/bing-autosuggest-api-v5-reference#query-parameters) ismertető cikkben. Minden lekérdezési paraméter értékének URL-kódolásúnak kell lennie.

A kérelemnek tartalmaznia kell az [Ocp-Apim-Subscription-Key](https://docs.microsoft.com/rest/api/cognitiveservices/bing-autosuggest-api-v5-reference#subscriptionkey) fejlécet. Nem kötelező, de javasolt a következő fejlécek megadása is:

- [User-Agent](https://docs.microsoft.com/rest/api/cognitiveservices/bing-autosuggest-api-v5-reference#useragent)
- [X-MSEdge-ClientID](https://docs.microsoft.com/rest/api/cognitiveservices/bing-autosuggest-api-v5-reference#clientid)
- [X-Search-ClientIP](https://docs.microsoft.com/rest/api/cognitiveservices/bing-autosuggest-api-v5-reference#clientip)
- [X-Search-Location](https://docs.microsoft.com/rest/api/cognitiveservices/bing-autosuggest-api-v5-reference#location)

Az ügyfél IP-címe és helye fontos a helyfüggő tartalmak visszaadása szempontjából.

Az összes kérelem- és válaszfejléc listáját lásd a [Fejlécek](https://docs.microsoft.com/rest/api/cognitiveservices/bing-autosuggest-api-v5-reference#headers) cikkben.

> [!NOTE]
> Amikor a Bing Autosuggest API hívása JavaScript, a böngésző beépített biztonsági funkciókat előfordulhat, hogy megakadályozzák a fér hozzá a következő fejlécek értékeit.

A probléma megoldásához, a Bing Autosuggest API-kérelem CORS proxyn keresztül teheti meg. Ilyen proxyn válasza rendelkezik egy `Access-Control-Expose-Headers` fejléc adott listáinak válaszfejlécek, és elérhetővé teszi azokat a JavaScript.

Egyszerű, hogy a CORS-proxy telepítéséhez a [oktatóanyag alkalmazása](../tutorials/autosuggest.md) eléréséhez a választható ügyféltanúsítvány-fejléceket. Első lépésként [telepítse a Node.js-t](https://nodejs.org/en/download/), ha még nem tette meg. Majd adja meg a következő parancsot a parancssorba.

    npm install -g cors-proxy-server

Ezután módosítsa a Bing Autosuggest API-végpont lévő a HTML-fájl:

    http://localhost:9090/https://api.cognitive.microsoft.com/bing/v7.0/Suggestions

Végül indítsa el a CORS-proxyt a következő paranccsal:

    cors-proxy-server

Ne zárja be a parancsablakot, amíg használja az oktatóalkalmazást; az ablak bezárása leállítja a proxyt. A bővíthető HTTP-fejlécek szakaszában, a keresési eredmények alatt, most már az `X-MSEdge-ClientID` fejléc is megjelenik, és ellenőrizheti, hogy ugyanaz a fejléc szerepel-e minden kérésnél.

Kérelmek tartalmaznia kell az összes javasolt lekérdezési paraméterek és fejléceket. 

A következő példa egy olyan kérést mutat be, amely a *sail* sztringre adja vissza a javasolt lekérdezési sztringeket.

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

Ha a felhasználó egy lekérdezést a legördülő listából, ezzel az egyik hívja a [Bing Search APIs](https://docs.microsoft.com/azure/cognitive-services/bing-web-search/bing-api-comparison?toc=%2Fen-us%2Fazure%2Fcognitive-services%2Fbing-autosuggest%2Ftoc.json&bc=%2Fen-us%2Fazure%2Fbread%2Ftoc.json) megjeleníti az eredményeket, és a Bing eredménye lapot a visszaadott küldenie a felhasználót `url` mező. Az alábbi példában a Bing Web Search API.

```json
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

- [Mi az a Bing Autosuggest?](../get-suggested-search-terms.md)
- [A Bing Autosuggest API 7-es verziójának referenciája](https://docs.microsoft.com/rest/api/cognitiveservices/bing-autosuggest-api-v7-reference)
- [Javasolt keresőkifejezéseket lekérése a Bing Autosuggest API](get-suggestions.md)
