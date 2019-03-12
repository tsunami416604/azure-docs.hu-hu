---
title: 'Gyors útmutató: Egy keresési kérelmet küld a Bing Entity Search REST API a Node.js használata'
titlesuffix: Azure Cognitive Services
description: Ez a rövid útmutató segítségével egy kérelmet küld a Bing Entity Search REST API használatával C#, és a egy JSON-választ kapnak.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-entity-search
ms.topic: quickstart
ms.date: 02/01/2019
ms.author: aahi
ms.openlocfilehash: eff9375919fa946bc74fa6db15cddbebff348717
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/07/2019
ms.locfileid: "57541162"
---
# <a name="quickstart-send-a-search-request-to-the-bing-entity-search-rest-api-using-nodejs"></a>Gyors útmutató: Egy keresési kérelmet küld a Bing Entity Search REST API a Node.js használata

Ez a rövid útmutató segítségével a Bing Entity Search API az első hívását, és tekintse meg a JSON-választ. Az egyszerű JavaScript-alkalmazás news search lekérdezést küld az API-t, és a válasz megjeleníti. A minta forráskódja a [GitHubon](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/nodejs/Search/BingEntitySearchv7.js) érhető el.

Az alkalmazás JavaScript nyelven van megírva, míg a API-ját egy REST-alapú webszolgáltatás szinte bármelyik programozási nyelvével kompatibilis.

## <a name="prerequisites"></a>Előfeltételek

* A [Node.js](https://nodejs.org/en/download/) legújabb verziója.

* A [kérelem JavaScript kódtár](https://github.com/request/request)

[!INCLUDE [cognitive-services-bing-news-search-signup-requirements](../../../../includes/cognitive-services-bing-entity-search-signup-requirements.md)]

## <a name="create-and-initialize-the-application"></a>Az alkalmazás létrehozása és inicializálása

1. Hozzon létre egy új JavaScript-fájlt a kedvenc IDE-környezetében vagy szerkesztőjében, és állítsa be a szigorúságot, a https-követelményeket.

    ```javaScript
    'use strict';
    let https = require ('https');
    ```

2. Az API-végpont, az előfizetési kulcs és a keresési lekérdezés változókat létrehozni.

    ```javascript
    let subscriptionKey = 'ENTER YOUR KEY HERE';
    let host = 'api.cognitive.microsoft.com';
    let path = '/bing/v7.0/entities';
    
    let mkt = 'en-US';
    let q = 'italian restaurant near me';
    ```

3. A piaci és a lekérdezési paraméterek hozzáfűzése egy karakterlánc nevű `query`. Ügyeljen arra, hogy url-kódolása a lekérdezés `encodeURI()`.
    ```javascript 
    let query = '?mkt=' + mkt + '&q=' + encodeURI(q);
    ```

## <a name="handle-and-parse-the-response"></a>A válasz kezelése és elemzése

1. nevű függvény meghatározása `response_handler` egy HTTP-hívással túlterhelés `response`, paraméterként. Ez a függvény belül hajtsa végre az alábbi lépéseket:

    1. Definiáljon egy változót, amely a JSON-válasz törzsét tartalmazza majd.  
        ```javascript
        let response_handler = function (response) {
            let body = '';
        };
        ```

    2. Tárolja a válasz törzsét, ha az **adat** jelölő megjelenik
        ```javascript
        response.on('data', function (d) {
            body += d;
        });
        ```

    3. Ha egy **záró** jelző leállítási jelzést kapott, a JSON elemzése, és nyomtassa ki.

        ```javascript
        response.on ('end', function () {
        let json = JSON.stringify(JSON.parse(body), null, '  ');
        console.log (json);
        });
        ```

## <a name="send-a-request"></a>Kérés küldése

1. Hozzon létre egy függvényt, nevű `Search` egy keresési kérelmet küldeni. Hajtsa végre az alábbi lépéseket.

    1. A kérelem paramétereit tartalmazó JSON-objektum létrehozása: használata `Get` módban, és adja hozzá a gazdagépet és az elérési út adatait. Az előfizetési kulcs hozzáadása a `Ocp-Apim-Subscription-Key` fejléc. 
    2. Használat `https.request()` a korábban létrehozott válasz kezelő és a keresési paraméterek a kérelem elküldéséhez.
    
    ```javascript
    let Search = function () {
        let request_params = {
            method : 'GET',
            hostname : host,
            path : path + query,
            headers : {
                'Ocp-Apim-Subscription-Key' : subscriptionKey,
            }
        };
    
        let req = https.request (request_params, response_handler);
        req.end ();
    }
    ```

2. Hívja a `Search()` függvény.

## <a name="example-json-response"></a>Példa JSON-válasz

A rendszer JSON formátumban ad vissza egy sikeres választ a következő példában látható módon: 

```json
{
  "_type": "SearchResponse",
  "queryContext": {
    "originalQuery": "italian restaurant near me",
    "askUserForLocation": true
  },
  "places": {
    "value": [
      {
        "_type": "LocalBusiness",
        "webSearchUrl": "https://www.bing.com/search?q=sinful+bakery&filters=local...",
        "name": "Liberty's Delightful Sinful Bakery & Cafe",
        "url": "https://www.contoso.com/",
        "entityPresentationInfo": {
          "entityScenario": "ListItem",
          "entityTypeHints": [
            "Place",
            "LocalBusiness"
          ]
        },
        "address": {
          "addressLocality": "Seattle",
          "addressRegion": "WA",
          "postalCode": "98112",
          "addressCountry": "US",
          "neighborhood": "Madison Park"
        },
        "telephone": "(800) 555-1212"
      },

      . . .
      {
        "_type": "Restaurant",
        "webSearchUrl": "https://www.bing.com/search?q=Pickles+and+Preserves...",
        "name": "Munson's Pickles and Preserves Farm",
        "url": "https://www.princi.com/",
        "entityPresentationInfo": {
          "entityScenario": "ListItem",
          "entityTypeHints": [
            "Place",
            "LocalBusiness",
            "Restaurant"
          ]
        },
        "address": {
          "addressLocality": "Seattle",
          "addressRegion": "WA",
          "postalCode": "98101",
          "addressCountry": "US",
          "neighborhood": "Capitol Hill"
        },
        "telephone": "(800) 555-1212"
      },
      
      . . .
    ]
  }
}
```

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Egyoldalas webes alkalmazás készítése](../tutorial-bing-entities-search-single-page-app.md)

* [Mi az a Bing Entity Search API?](../overview.md )
* [A Bing Entity Search API-referencia](https://docs.microsoft.com/rest/api/cognitiveservices/bing-entities-api-v7-reference)
