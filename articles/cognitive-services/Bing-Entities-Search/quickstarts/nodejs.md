---
title: 'Gyors útmutató: keresési kérelem küldése a REST APInak Node.js-Bing Entity Search használatával'
titleSuffix: Azure Cognitive Services
description: Ezzel a rövid útmutatóval kérést küldhet a Bing Entity Search REST API a Node.js használatával, és JSON-választ kap.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-entity-search
ms.topic: quickstart
ms.date: 05/08/2020
ms.author: aahi
ms.custom: devx-track-js
ms.openlocfilehash: b9311329ea4115d49f36dd7d39782bbd748a356b
ms.sourcegitcommit: 6172a6ae13d7062a0a5e00ff411fd363b5c38597
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/11/2020
ms.locfileid: "97106104"
---
# <a name="quickstart-send-a-search-request-to-the-bing-entity-search-rest-api-using-nodejs"></a>Gyors útmutató: keresési kérelem küldése a Bing Entity Search REST API a Node.js használatával

> [!WARNING]
> Bing Search API-k átkerülnek a Cognitive Servicesról Bing Search szolgáltatásokra. **2020. október 30-ig** a Bing Search új példányait az [itt](/bing/search-apis/bing-web-search/create-bing-search-service-resource)ismertetett eljárás követésével kell kiépíteni.
> A Cognitive Services használatával kiépített Bing Search API-k a következő három évben vagy a Nagyvállalati Szerződés végéig lesz támogatva, attól függően, hogy melyik történik először.
> Az áttelepítési utasításokért lásd: [Bing Search Services](/bing/search-apis/bing-web-search/create-bing-search-service-resource).

Ezzel a rövid útmutatóval elvégezheti az első hívását a Bing Entity Search API, és megtekintheti a JSON-választ. Ez az egyszerű JavaScript-alkalmazás egy Hírek keresési lekérdezést küld az API-nak, és megjeleníti a választ. A minta forráskódja elérhető a [githubon](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/nodejs/Search/BingEntitySearchv7.js).

Bár ez az alkalmazás JavaScript nyelven íródott, az API egy REST-alapú webszolgáltatás, amely kompatibilis a legtöbb programozási nyelvvel.

## <a name="prerequisites"></a>Előfeltételek

* A [Node.js](https://nodejs.org/en/download/) legújabb verziója.

* A [JavaScript-kérelem könyvtára](https://github.com/request/request).

[!INCLUDE [cognitive-services-bing-news-search-signup-requirements](../../../../includes/cognitive-services-bing-entity-search-signup-requirements.md)]

## <a name="create-and-initialize-the-application"></a>Az alkalmazás létrehozása és inicializálása

1. Hozzon létre egy új JavaScript-fájlt a kedvenc IDE vagy szerkesztőben, és állítsa be a szigorú és a HTTPS-követelményeket.

    ```javaScript
    'use strict';
    let https = require ('https');
    ```

2. Hozzon létre változókat az API-végponthoz, az előfizetési kulcshoz és a keresési lekérdezéshez. Használhatja a globális végpontot a következő kódban, vagy használhatja az erőforráshoz tartozó Azure Portalban megjelenő [Egyéni altartomány](../../../cognitive-services/cognitive-services-custom-subdomains.md) -végpontot.

    ```javascript
    let subscriptionKey = 'ENTER YOUR KEY HERE';
    let host = 'api.cognitive.microsoft.com';
    let path = '/bing/v7.0/entities';
    
    let mkt = 'en-US';
    let q = 'italian restaurant near me';
    ```

3. Fűzze hozzá a piacot és a lekérdezési paramétereket egy nevű karakterlánchoz `query` . Ügyeljen arra, hogy URL-kódolással kódolja a lekérdezést `encodeURI()` .
    ```javascript 
    let query = '?mkt=' + mkt + '&q=' + encodeURI(q);
    ```

## <a name="handle-and-parse-the-response"></a>A válasz kezelése és elemzése

1. Definiáljon egy nevű függvényt, `response_handler()` amely egy http-hívást ( `response` paraméterként) használ. 

2. A függvényen belül Definiáljon egy változót, amely tartalmazza a JSON-válasz törzsét.  
    ```javascript
    let response_handler = function (response) {
        let body = '';
    };
    ```

3. A válasz törzsét tárolja a `data` jelző meghívásakor.
    ```javascript
    response.on('data', function (d) {
        body += d;
    });
    ```

4. Ha jelzőt `end` jelez, elemezze a JSON-t, és nyomtassa ki.

    ```javascript
    response.on ('end', function () {
    let json = JSON.stringify(JSON.parse(body), null, '  ');
    console.log (json);
    });
    ```

## <a name="send-a-request"></a>Kérés küldése

1. Hozzon létre egy nevű függvényt a `Search()` keresési kérelem elküldéséhez. Ebben az esetben hajtsa végre a következő lépéseket:

2. A függvényen belül hozzon létre egy JSON-objektumot, amely tartalmazza a kérelmek paramétereit. Használja `Get` a metódust, és adja hozzá a gazdagép és az elérési út adatait. Adja hozzá az előfizetési kulcsot a `Ocp-Apim-Subscription-Key` fejléchez. 

3. A paranccsal `https.request()` elküldheti a kérést a korábban létrehozott válasz-kezelővel és a keresési paraméterekkel.
    
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
> [Egyoldalas webalkalmazás készítése](../tutorial-bing-entities-search-single-page-app.md)

* [Mi a Bing Entity Search API?](../overview.md )
* [Bing Entity Search API hivatkozás](/rest/api/cognitiveservices-bingsearch/bing-entities-api-v7-reference).
