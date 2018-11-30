---
title: 'Rövid útmutató: Bing Entity Search API, Node.js'
titlesuffix: Azure Cognitive Services
description: Információk és kódminták segítségével ismerkedhet meg a Bing Entity Search API használatának első lépéseivel.
services: cognitive-services
author: aahill
manager: cgronlun
ms.service: cognitive-services
ms.component: bing-entity-search
ms.topic: quickstart
ms.date: 11/28/2017
ms.author: aahi
ms.openlocfilehash: 80af035d3d3f4c530e417669c2fa9acad39eb355
ms.sourcegitcommit: a08d1236f737915817815da299984461cc2ab07e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/26/2018
ms.locfileid: "52315987"
---
# <a name="quickstart-for-bing-entity-search-api-with-nodejs"></a>Rövid útmutató a Bing Entity Search API és a Node.js használatához

Ez a cikk bemutatja, hogyan használható a [a Bing Entity Search](https://docs.microsoft.com/azure/cognitive-services/bing-entities-search/search-the-web) API a node.js használatával.

## <a name="prerequisites"></a>Előfeltételek

A kód futtatásához [Node.js 6](https://nodejs.org/en/download/) szükséges.

Rendelkeznie kell egy [Cognitive Services API-fiókkal](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account), amely tartalmazza a **Bing Entity Search API-t**. Az [ingyenes próbaverzió](https://azure.microsoft.com/try/cognitive-services/?api=bing-entity-search-api) elegendő ehhez a rövid útmutatóhoz. Szüksége lesz az ingyenes próbaverzió aktiválásakor kapott hozzáférési kulcsra, vagy beszerezhet egy fizetős előfizetői azonosítót az Azure-irányítópultról.  Lásd még: [a Cognitive Services díjszabás – keresési Bing-API](https://azure.microsoft.com/pricing/details/cognitive-services/search-api/).

## <a name="search-entities"></a>Entitások keresése

Az alkalmazás futtatásához kövesse az alábbi lépéseket.

1. Hozzon létre egy új Node.js-projektet kedvenc IDE-környezetében.
2. Adja hozzá az alábbi kódot.
3. A `key` értéket cserélje le az előfizetéshez érvényes hozzáférési kulcsra.
4. Futtassa a programot.

```nodejs
'use strict';

let https = require ('https');

// **********************************************
// *** Update or verify the following values. ***
// **********************************************

// Replace the subscriptionKey string value with your valid subscription key.
let subscriptionKey = 'ENTER KEY HERE';

let host = 'api.cognitive.microsoft.com';
let path = '/bing/v7.0/entities';

let mkt = 'en-US';
let q = 'italian restaurant near me';

let params = '?mkt=' + mkt + '&q=' + encodeURI(q);

let response_handler = function (response) {
    let body = '';
    response.on ('data', function (d) {
        body += d;
    });
    response.on ('end', function () {
        let body_ = JSON.parse (body);
        let body__ = JSON.stringify (body_, null, '  ');
        console.log (body__);
    });
    response.on ('error', function (e) {
        console.log ('Error: ' + e.message);
    });
};

let Search = function () {
    let request_params = {
        method : 'GET',
        hostname : host,
        path : path + params,
        headers : {
            'Ocp-Apim-Subscription-Key' : subscriptionKey,
        }
    };

    let req = https.request (request_params, response_handler);
    req.end ();
}

Search ();
```

**Válasz**

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
        "url": "http://www.princi.com/",
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

[Vissza a tetejére](#HOLTop)

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Bing Entity Search-oktatóanyag](../tutorial-bing-entities-search-single-page-app.md)
> [A Bing Entity Search áttekintése](../search-the-web.md )
> [API-referencia](https://docs.microsoft.com/rest/api/cognitiveservices/bing-entities-api-v7-reference)
