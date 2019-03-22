---
title: 'Gyors útmutató: Keressen videókat a Bing Video Search REST API és a Node.js használatával'
titlesuffix: Azure Cognitive Services
description: Ez a rövid útmutató segítségével videókeresési-kérelmeket küldjön a Bing Video Search REST API a JavaScript használatával.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-video-search
ms.topic: quickstart
ms.date: 01/31/2019
ms.author: aahi
ms.openlocfilehash: a4e3d37b2eb32fa0384986e37781f21b906bed42
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/18/2019
ms.locfileid: "58077309"
---
# <a name="quickstart-search-for-videos-using-the-bing-video-search-rest-api-and-nodejs"></a>Gyors útmutató: Keressen videókat a Bing Video Search REST API és a Node.js használatával

Ez a rövid útmutató segítségével első hívását, a Bing Videókeresési API és a keresési eredményeket a JSON-válasz megtekintéséhez. Az egyszerű JavaScript-alkalmazás egy HTTP-videókeresési lekérdezést küld az API-t, és a válasz megjeleníti. Bár ez az alkalmazás JavaScript nyelven van megírva, és használja a Node.js, az API-ját egy REST-alapú webszolgáltatás szinte bármelyik programozási nyelvével kompatibilis. A minta forráskódja további hibakezeléssel és kódmegjegyzésekkel együtt elérhető a [GitHubon](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/nodejs/Search/BingVideoSearchv7.js).

## <a name="prerequisites"></a>Előfeltételek

* [Node.js](https://nodejs.org/en/download/)

* A JavaScript-kérelem modul
    * Ez a modul segítségével lehet telepíteni `npm install request`

[!INCLUDE [cognitive-services-bing-video-search-signup-requirements](../../../../includes/cognitive-services-bing-video-search-signup-requirements.md)]

## <a name="initialize-the-application"></a>Az alkalmazás inicializálása

1. Hozzon létre egy új JavaScript-fájlt a kedvenc integrált Fejlesztőkörnyezetével vagy szerkesztőjével. Adja meg a szigorúsága, és adja hozzá az alábbi követelményeknek:

    ```javascript
    'use strict';
    let https = require('https');
    ```

2. Az API-végpont, előfizetési kulcs és a keresési kifejezés változók létrehozása.

    ```javascript
    let subscriptionKey = 'enter key here';
    let host = 'api.cognitive.microsoft.com';
    let path = '/bing/v7.0/videos/search';
    let term = 'kittens';
    ```

## <a name="create-a-response-handler"></a>Válaszkezelő létrehozása

1. Hozzon létre egy függvényt, nevű `response_handler` érvénybe egy JSON-választ, az API-ból. Hozzon létre egy változót a válasz törzse. A válasz hozzáfűzése amikor egy `data` jelző érkezik, használatával `response.on()`.

    ```javascript
    let response_handler = function (response) {
        let body = '';
        response.on('data', function (d) {
            body += d;
        });
    };
    ```
    
   1. Amikor `end` van jelzést, használjon `response.on()` tárolásához, a bing kapcsolatos fejlécek (kezdve `bingapis` vagy `x-msedge-`). Majd elemezni a JSON használatával `JSON.parse()`, átalakíthatja egy karakterláncot `JSON.stringify()`, és nyomtassa ki.

       ```javascript
       response.on('end', function () {
           for (var header in response.headers)
               // header keys are lower-cased by Node.js
               if (header.startsWith("bingapis-") || header.startsWith("x-msedge-"))
                    console.log(header + ": " + response.headers[header]);
           body = JSON.stringify(JSON.parse(body), null, '  ');
           //JSON Response body
           console.log(body);
       });
       ```

# <a name="create-and-send-the-search-request"></a>Hozzon létre, és a keresési kérelem küldése

1. Hozzon létre egy függvényt, nevű `bing_video_search()`. Adja hozzá a kérést, beleértve a gazdagépnév és a fejlécek paramétereit. Kódolás a keresési kifejezést, és hozzáfűzi a-path paraméterrel a `?q=` paraméter. Küldje el a kérelmet, `req.end()`.

    ```javascript
    let bing_video_search = function (search_term) {
      console.log('Searching videos for: ' + term);
      let request_params = {
            method : 'GET',
            hostname : host,
            path : path + '?q=' + encodeURIComponent(search_term),
            headers : {
                'Ocp-Apim-Subscription-Key' : subscriptionKey,
            }
        };
        let req = https.request(request_params, response_handler);
        req.end();
    }
    ```

## <a name="json-response"></a>JSON-válasz

A rendszer JSON formátumban ad vissza egy sikeres választ a következő példában látható módon: 

```json
{
    "_type": "Videos",
    "instrumentation": {},
    "readLink": "https://api.cognitive.microsoft.com/api/v7/videos/search?q=kittens",
    "webSearchUrl": "https://www.bing.com/videos/search?q=kittens",
    "totalEstimatedMatches": 1000,
    "value": [
        {
            "webSearchUrl": "https://www.bing.com/videos/search?q=kittens&view=...",
            "name": "Top 10 cute kitten videos compilation",
            "description": "HELP HOMELESS ANIMALS AND WIN A PRIZE BY CHOOSING...",
            "thumbnailUrl": "https://tse4.mm.bing.net/th?id=OVP.n1aE_Oikl4MtzBb...",
            "datePublished": "2014-11-12T22:47:36.0000000",
            "publisher": [
                {
                    "name": "Fabrikam"
                }
            ],
            "creator": {
                "name": "Marcus Appel"
            },
            "isAccessibleForFree": true,
            "contentUrl": "https://www.fabrikam.com/watch?v=8HVWitAW-Qg",
            "hostPageUrl": "https://www.fabrikam.com/watch?v=8HVWitAW-Qg",
            "encodingFormat": "h264",
            "hostPageDisplayUrl": "https://www.fabrikam.com/watch?v=8HVWitAW-Qg",
            "width": 480,
            "height": 360,
            "duration": "PT3M52S",
            "motionThumbnailUrl": "https://tse4.mm.bing.net/th?id=OM.j4QyJAENJphdZQ_1501386166&pid=Api",
            "embedHtml": "<iframe width=\"1280\" height=\"720\" src=\"https://www.fabrikam.com/embed/8HVWitAW-Qg?autoplay=1\" frameborder=\"0\" allowfullscreen></iframe>",
            "allowHttpsEmbed": true,
            "viewCount": 7513633,
            "thumbnail": {
                "width": 300,
                "height": 168
            },
            "videoId": "655D98260D012432848F6558260D012432848F",
            "allowMobileEmbed": true,
            "isSuperfresh": false
        },
        . . .
    ],
    "nextOffset": 36,
    "queryExpansions": [
        {
            "text": "Kittens Meowing",
            "displayText": "Meowing",
            "webSearchUrl": "https://www.bing.com/videos/search?q=Kittens+Meowing...",
            "searchLink": "https://api.cognitive.microsoft.com/api/v7/videos/search...",
            "thumbnail": {
                "thumbnailUrl": "https://tse3.mm.bing.net/th?q=Kittens+Meowing&pid..."
            }
        },
        {
            "text": "Funny Kittens",
            "displayText": "Funny",
            "webSearchUrl": "https://www.bing.com/videos/search?q=Funny+Kittens...",
            "searchLink": "https://api.cognitive.microsoft.com/api/v7/videos/search...",
            "thumbnail": {
                "thumbnailUrl": "https://tse3.mm.bing.net/th?q=Funny+Kittens&..."
            }
        },
        . . .
    ],
    "pivotSuggestions": [
        {
            "pivot": "kittens",
            "suggestions": [
                {
                    "text": "Cat",
                    "displayText": "Cat",
                    "webSearchUrl": "https://www.bing.com/videos/search?q=Cat...",
                    "searchLink": "https://api.cognitive.microsoft.com/api/v7/videos/search?...",
                    "thumbnail": {
                        "thumbnailUrl": "https://tse3.mm.bing.net/th?q=Cat&pid=Api..."
                    }
                },
                {
                    "text": "Feral Cat",
                    "displayText": "Feral Cat",
                    "webSearchUrl": "https://www.bing.com/videos/search?q=Feral+Cat...",
                    "searchLink": "https://api.cognitive.microsoft.com/api/v7/videos/search...",
                    "thumbnail": {
                        "thumbnailUrl": "https://tse3.mm.bing.net/th?q=Feral+Cat&pid=Api&..."
                    }
                }
            ]
        }
    ],
    "relatedSearches": [
        {
            "text": "Kittens Being Born",
            "displayText": "Kittens Being Born",
            "webSearchUrl": "https://www.bing.com/videos/search?q=Kittens+Being+Born...",
            "searchLink": "https://api.cognitive.microsoft.com/api/v7/videos/search?...",
            "thumbnail": {
                "thumbnailUrl": "https://tse1.mm.bing.net/th?q=Kittens+Being+Born&pid=..."
            }
        },
        . . .
    ]
}
```

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Egyoldalas webes alkalmazás készítése](../tutorial-bing-video-search-single-page-app.md)

## <a name="see-also"></a>Lásd még 

 [Mi az a Bing Videókeresési API?](../overview.md)
