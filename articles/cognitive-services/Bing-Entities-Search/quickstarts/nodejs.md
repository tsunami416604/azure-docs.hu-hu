---
title: 'Rövid útmutató: Keresési kérelem küldése a REST API-nak a Node.js használatával – Bing entitáskeresés'
titleSuffix: Azure Cognitive Services
description: Ezzel a rövid útmutatóval kérést küldhet a Bing Entity Search REST API-nak C# használatával, és JSON-választ kaphat.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-entity-search
ms.topic: quickstart
ms.date: 12/11/2019
ms.author: aahi
ms.openlocfilehash: f3585e96376a25721f478f9dd621835e75e3c600
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75448631"
---
# <a name="quickstart-send-a-search-request-to-the-bing-entity-search-rest-api-using-nodejs"></a>Gyorsútmutató: Keresési kérelem küldése a Bing entitás keresési REST API-j használatával Node.js

Ezzel a rövid útmutatóval első ként hívhatja meg a Bing Entity Search API-t, és megtekintheti a JSON-választ. Ez az egyszerű JavaScript-alkalmazás hírkeresési lekérdezést küld az API-nak, és megjeleníti a választ. A minta forráskódja elérhető a [GitHubon.](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/nodejs/Search/BingEntitySearchv7.js)

Bár ez az alkalmazás JavaScript nyelven íródott, az API egy RESTful webszolgáltatás, amely kompatibilis a legtöbb programozási nyelvvel.

## <a name="prerequisites"></a>Előfeltételek

* A [Node.js](https://nodejs.org/en/download/) legújabb verziója.

* A [JavaScript lekérési kódtára](https://github.com/request/request)

[!INCLUDE [cognitive-services-bing-news-search-signup-requirements](../../../../includes/cognitive-services-bing-entity-search-signup-requirements.md)]

## <a name="create-and-initialize-the-application"></a>Az alkalmazás létrehozása és inicializálása

1. Hozzon létre egy új JavaScript-fájlt a kedvenc IDE-környezetében vagy szerkesztőjében, és állítsa be a szigorúságot, a https-követelményeket.

    ```javaScript
    'use strict';
    let https = require ('https');
    ```

2. Hozzon létre változókat az API-végponthoz, az előfizetési kulcshoz és a keresési lekérdezéshez. Használhatja az alábbi globális végpontot, vagy az [egyéni altartomány-végpontot,](../../../cognitive-services/cognitive-services-custom-subdomains.md) amely az azure-portálon jelenik meg az erőforráshoz.

    ```javascript
    let subscriptionKey = 'ENTER YOUR KEY HERE';
    let host = 'api.cognitive.microsoft.com';
    let path = '/bing/v7.0/entities';
    
    let mkt = 'en-US';
    let q = 'italian restaurant near me';
    ```

3. Fűzze hozzá a piaci és lekérdezési paramétereket egy karakterlánchoz, amelynek neve `query`. Ügyeljen arra, hogy url-kódolni a lekérdezést. `encodeURI()`
    ```javascript 
    let query = '?mkt=' + mkt + '&q=' + encodeURI(q);
    ```

## <a name="handle-and-parse-the-response"></a>A válasz kezelése és elemzése

1. Adjon meg `response_handler` egy HTTP-hívást `response`, paraméterként használó függvényt. Ebben a funkcióban hajtsa végre a következő lépéseket:

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

    3. Ha egy **záró** jelző t jelez, elemezze a JSON-t, és nyomtassa ki.

        ```javascript
        response.on ('end', function () {
        let json = JSON.stringify(JSON.parse(body), null, '  ');
        console.log (json);
        });
        ```

## <a name="send-a-request"></a>Kérés küldése

1. Hozzon létre `Search` egy keresési kérelem küldésére hívott függvényt. Ebben hajtsa végre a következő lépéseket.

   1. Hozzon létre egy JSON-objektumot, `Get` amely tartalmazza a kérelem paramétereit: használja a metódushoz, és adja meg a gazdagép és az elérési út adatait. Adja hozzá az `Ocp-Apim-Subscription-Key` előfizetési kulcsot a fejléchez. 
   2. A `https.request()` kérelem elküldéséhez a korábban létrehozott válaszkezelővel és a keresési paraméterekkel küldheti el.
    
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

2. Hívja `Search()` meg a függvényt.

## <a name="example-json-response"></a>Példa JSON-válaszra

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
> [Egyoldalas webalkalmazás készítése](../tutorial-bing-entities-search-single-page-app.md)

* [Mi a Bing Entity Search API?](../overview.md )
* [Bing entitáskeresési API– referencia](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-entities-api-v7-reference)
