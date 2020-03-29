---
title: Kérések küldése a Bing Automatikus javaslat API-nak
titleSuffix: Azure Cognitive Services
description: A Bing Autosuggest API a keresési mezőben lévő részleges lekérdezési karakterlánc alapján adja vissza a javasolt lekérdezések listáját. További információ a kérelmek küldéséről.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-autosuggest
ms.topic: conceptual
ms.date: 06/27/2019
ms.author: scottwhi
ms.openlocfilehash: d479548e682e814345e13d9416d08ec453f90304
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "74072850"
---
# <a name="sending-requests-to-the-bing-autosuggest-api"></a>Kérések küldése a Bing Automatikus javaslat API-nak.

Ha az alkalmazás lekérdezéseket küld a Bing Search api-k bármelyikének, a Bing automatikus javaslat API-jával javíthatja a felhasználók keresési élményét. A Bing Autosuggest API a keresési mezőben lévő részleges lekérdezési karakterlánc alapján adja vissza a javasolt lekérdezések listáját. Mivel az alkalmazás keresőmezőjébe karaktereket ír be, javaslatokat jeleníthet meg egy legördülő listában. Ebből a cikkből további információ az API-ra vonatkozó kérelmek küldéséről. 

## <a name="bing-autosuggest-api-endpoint"></a>Bing automatikus api-végpontra való javaslata

A **Bing Autosuggest API** egy végpontot tartalmaz, amely egy részleges keresési kifejezés javasolt lekérdezéseinek listáját adja vissza.

Ha a Bing API használatával szeretne `GET` lekérni a javasolt lekérdezéseket, küldjön egy kérést a következő végpontra. A fejlécek és az URL-paraméterek segítségével további specifikációkat határozhatja meg.

**Végpont:** A keresési javaslatokat JSON-eredményként adja vissza, amelyek `?q=""`relevánsak a felhasználó által definiált bemenethez.

```http
GET https://api.cognitive.microsoft.com/bing/v7.0/Suggestions 
```

A fejlécekről, paraméterekről, piaci kódokról, válaszobjektumokról, hibákról stb. [Bing Autosuggest API v7](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-autosuggest-api-v7-reference)

A **Bing** API-k támogatják azokat a keresési műveleteket, amelyek típusuk nak megfelelően adják vissza az eredményeket.Minden keresési végpont json válaszobjektumként adja vissza az eredményeket.
Minden végpont támogatja azokat a lekérdezéseket, amelyek hosszúság, szélesség és keresési sugár szerint egy adott nyelvet és/vagy helyet adnak vissza.

Az egyes végpontok által támogatott paraméterekről az egyes típusok referenciaoldalain talál teljes körű tájékoztatást.
Az Automatikus javaslat API-t használó alapvető kérelmekpéldáit az [Automatikus javaslati rövidútmutatók című témakörben talál.](https://docs.microsoft.com/azure/cognitive-services/Bing-Autosuggest)

## <a name="bing-autosuggest-api-requests"></a>Bing automatikus api-kérelmek

> [!NOTE]
> * A Bing Autosuggest API-hoz érkező kérelmeknek a HTTPS protokollt kell használniuk.

Javasoljuk, hogy minden kérelem egy kiszolgálóról induljon. A kulcs ügyfélalkalmazás részeként történő terjesztése több lehetőséget biztosít a rosszindulatú, harmadik féltől származó hozzáférésre. Ezenkívül a kiszolgálóról történő hívások egyetlen frissítési pontot biztosít a jövőbeli frissítésekhez.

A kérelemnek tartalmaznia kell a [q](https://docs.microsoft.com/rest/api/cognitiveservices/bing-autosuggest-api-v5-reference#query) lekérdezési paramétert, amely a felhasználó részleges keresési kifejezését adja meg. Nem kötelező, de a kérelemnek érdemes tartalmaznia egy [mkt](https://docs.microsoft.com/rest/api/cognitiveservices/bing-autosuggest-api-v5-reference#mkt) lekérdezési paramétert is, amely azonosítja a piacot, ahonnan eredményeket szeretnénk kapni. Az opcionális lekérdezési paraméterek listáját lásd a [lekérdezési paramétereket](https://docs.microsoft.com/rest/api/cognitiveservices/bing-autosuggest-api-v5-reference#query-parameters) ismertető cikkben. Minden lekérdezési paraméter értékének URL-kódolásúnak kell lennie.

A kérelemnek tartalmaznia kell az [Ocp-Apim-Subscription-Key](https://docs.microsoft.com/rest/api/cognitiveservices/bing-autosuggest-api-v5-reference#subscriptionkey) fejlécet. Nem kötelező, de javasolt a következő fejlécek megadása is:

- [User-Agent](https://docs.microsoft.com/rest/api/cognitiveservices/bing-autosuggest-api-v5-reference#useragent)
- [X-MSEdge-ClientID](https://docs.microsoft.com/rest/api/cognitiveservices/bing-autosuggest-api-v5-reference#clientid)
- [X-Search-ClientIP](https://docs.microsoft.com/rest/api/cognitiveservices/bing-autosuggest-api-v5-reference#clientip)
- [X-Search-Location](https://docs.microsoft.com/rest/api/cognitiveservices/bing-autosuggest-api-v5-reference#location)

Az ügyfél IP-címe és helye fontos a helyfüggő tartalmak visszaadása szempontjából.

Az összes kérelem- és válaszfejléc listáját lásd a [Fejlécek](https://docs.microsoft.com/rest/api/cognitiveservices/bing-autosuggest-api-v5-reference#headers) cikkben.

> [!NOTE]
> Amikor a Bing Autosuggest API-t JavaScript-ről hívja meg, a böngésző beépített biztonsági funkciói megakadályozhatják a fejlécek értékeinek elérését.

A probléma megoldásához a Bing Autosuggest API-kérelmet cors-proxyn keresztül teheti meg. Az ilyen proxy válasza `Access-Control-Expose-Headers` rendelkezik egy fejléccel, amely fehéríti a válaszfejléceket, és elérhetővé teszi őket a JavaScript számára.

Könnyen telepíthet CORS proxyt, hogy [oktatóalkalmazásunk](../tutorials/autosuggest.md) hozzáférhessen az opcionális ügyfélfejlécekhez. Első lépésként [telepítse a Node.js-t](https://nodejs.org/en/download/), ha még nem tette meg. Ezután írja be a következő parancsot a parancssorba.

    npm install -g cors-proxy-server

Ezután módosítsa a Bing Autosuggest API végpontját a HTML-fájlban a következőkre:

    http://localhost:9090/https://api.cognitive.microsoft.com/bing/v7.0/Suggestions

Végül indítsa el a CORS-proxyt a következő paranccsal:

    cors-proxy-server

Ne zárja be a parancsablakot, amíg használja az oktatóalkalmazást; az ablak bezárása leállítja a proxyt. A bővíthető HTTP-fejlécek szakaszában, a keresési eredmények alatt, most már az `X-MSEdge-ClientID` fejléc is megjelenik, és ellenőrizheti, hogy ugyanaz a fejléc szerepel-e minden kérésnél.

A kérelmeknek tartalmazniuk kell az összes javasolt lekérdezési paramétert és fejlécet. 

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

A következő webes javaslatcsoport választ ad a fenti kérésre. A csoport tartalmazza a keresési lekérdezési javaslatok listáját, `query`minden `url` javaslattal együtt, beleértve a `displayText`, és a mezőt.

A `displayText` mező tartalmazza a javasolt lekérdezést, amelyet a keresőmező legördülő listájának feltöltéséhez használ. A válaszban szereplő összes javaslatot meg kell jelenítenie az adott sorrendben.  

Ha a felhasználó kiválaszt egy lekérdezést a legördülő listából, akkor a [Bing Search API-k](https://docs.microsoft.com/azure/cognitive-services/bing-web-search/bing-api-comparison?toc=%2Fen-us%2Fazure%2Fcognitive-services%2Fbing-autosuggest%2Ftoc.json&bc=%2Fen-us%2Fazure%2Fbread%2Ftoc.json) egyikének hívására és az eredmények `url` saját kezű megjelenítésére, vagy a visszaadott mező használatával elküldheti a felhasználót a Bing találati oldalára.

[!INCLUDE [cognitive-services-bing-url-note](../../../../includes/cognitive-services-bing-url-note.md)]

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

- [Mi a Bing Autosuggest?](../get-suggested-search-terms.md)
- [A Bing Autosuggest API 7-es verziójának referenciája](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-autosuggest-api-v7-reference)
- [Javasolt keresési kifejezések beszerzése a Bing Autosuggest API-ból](get-suggestions.md)
