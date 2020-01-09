---
title: 'Gyors útmutató: keresési kérelem küldése a REST API Node. js használatával – Bing Entity Search'
titleSuffix: Azure Cognitive Services
description: Ezzel a rövid útmutatóval kérést küldhet a Bing Entity Search REST API a C#használatával, és JSON-választ kap.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-entity-search
ms.topic: quickstart
ms.date: 12/11/2019
ms.author: aahi
ms.openlocfilehash: f3585e96376a25721f478f9dd621835e75e3c600
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/25/2019
ms.locfileid: "75448631"
---
# <a name="quickstart-send-a-search-request-to-the-bing-entity-search-rest-api-using-nodejs"></a>Gyors útmutató: keresési kérelem küldése a Bing Entity Search REST API Node. js használatával

Ezzel a rövid útmutatóval elvégezheti az első hívását a Bing Entity Search API, és megtekintheti a JSON-választ. Ez az egyszerű JavaScript-alkalmazás egy Hírek keresési lekérdezést küld az API-nak, és megjeleníti a választ. A minta forráskódja a [GitHubon](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/nodejs/Search/BingEntitySearchv7.js) érhető el.

Az alkalmazás JavaScriptben való megírásakor az API egy REST-alapú webszolgáltatás, amely kompatibilis a legtöbb programozási nyelvvel.

## <a name="prerequisites"></a>Előfeltételek

* A [Node.js](https://nodejs.org/en/download/) legújabb verziója.

* A [JavaScript-kérelem könyvtára](https://github.com/request/request)

[!INCLUDE [cognitive-services-bing-news-search-signup-requirements](../../../../includes/cognitive-services-bing-entity-search-signup-requirements.md)]

## <a name="create-and-initialize-the-application"></a>Az alkalmazás létrehozása és inicializálása

1. Hozzon létre egy új JavaScript-fájlt a kedvenc IDE-környezetében vagy szerkesztőjében, és állítsa be a szigorúságot, a https-követelményeket.

    ```javaScript
    'use strict';
    let https = require ('https');
    ```

2. Hozzon létre változókat az API-végponthoz, az előfizetési kulcshoz és a keresési lekérdezéshez. Használhatja az alábbi globális végpontot, vagy az erőforráshoz tartozó Azure Portalban megjelenő [Egyéni altartomány](../../../cognitive-services/cognitive-services-custom-subdomains.md) végpontot.

    ```javascript
    let subscriptionKey = 'ENTER YOUR KEY HERE';
    let host = 'api.cognitive.microsoft.com';
    let path = '/bing/v7.0/entities';
    
    let mkt = 'en-US';
    let q = 'italian restaurant near me';
    ```

3. Fűzze hozzá a piacot és a lekérdezési paramétereket `query`nevű karakterlánchoz. Ügyeljen arra, hogy URL-kódolással kódolja a lekérdezést `encodeURI()`.
    ```javascript 
    let query = '?mkt=' + mkt + '&q=' + encodeURI(q);
    ```

## <a name="handle-and-parse-the-response"></a>A válasz kezelése és elemzése

1. Definiáljon egy `response_handler` nevű függvényt, amely egy HTTP-hívást `response`paraméterként. A függvényen belül hajtsa végre a következő lépéseket:

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

    3. Ha a rendszer jelzi a **záró** jelzőt, elemzi a JSON-t, majd kinyomtatja azt.

        ```javascript
        response.on ('end', function () {
        let json = JSON.stringify(JSON.parse(body), null, '  ');
        console.log (json);
        });
        ```

## <a name="send-a-request"></a>Kérés küldése

1. Hozzon létre egy `Search` nevű függvényt egy keresési kérelem elküldéséhez. Ebben az esetben hajtsa végre a következő lépéseket.

   1. Hozzon létre egy JSON-objektumot, amely a kérelem paramétereit tartalmazza: használja `Get` a metódushoz, és adja hozzá a gazdagép és az elérési út adatait. Adja hozzá az előfizetési kulcsot a `Ocp-Apim-Subscription-Key` fejléchez. 
   2. A `https.request()` segítségével küldje el a kérést a korábban létrehozott Response Handler és a keresési paraméterek használatával.
    
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

2. Hívja meg a `Search()` függvényt.

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

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Egyoldalas Webalkalmazás létrehozása](../tutorial-bing-entities-search-single-page-app.md)

* [Mi a Bing Entity Search API?](../overview.md )
* [Bing Entity Search API referenciája](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-entities-api-v7-reference)
