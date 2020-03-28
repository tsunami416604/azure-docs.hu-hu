---
title: 'Rövid útmutató: Videók keresése a REST API és a Node.js használatával – Bing videokeresés'
titleSuffix: Azure Cognitive Services
description: Ezzel a rövid útmutatóval videókeresési kérelmeket küldhet a Bing Video Search REST API-nak JavaScript használatával.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-video-search
ms.topic: quickstart
ms.date: 12/09/2019
ms.author: aahi
ms.openlocfilehash: 6ae8afefae9a539812748c0ae5380ddaf1fb084c
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75382667"
---
# <a name="quickstart-search-for-videos-using-the-bing-video-search-rest-api-and-nodejs"></a>Rövid útmutató: Videók keresése a Bing Video Search REST API-val és a Node.js használatával

Ezzel a rövid útmutatóval elsőként hívhatja meg a Bing Video Search API-t, és megtekintheti a JSON-válasz keresési eredményét. Ez az egyszerű JavaScript-alkalmazás http-videokeresési lekérdezést küld az API-nak, és megjeleníti a választ. Míg ez az alkalmazás javascriptben íródott, és node.js-t használ, az API egy RESTful webszolgáltatás, amely kompatibilis a legtöbb programozási nyelvvel. A minta forráskódja további hibakezeléssel és kódmegjegyzésekkel együtt elérhető a [GitHubon](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/nodejs/Search/BingVideoSearchv7.js).

## <a name="prerequisites"></a>Előfeltételek

* [Node.js](https://nodejs.org/en/download/)

* A JavaScript kérése modulja
    * Ezt a modult a`npm install request`

[!INCLUDE [cognitive-services-bing-video-search-signup-requirements](../../../../includes/cognitive-services-bing-video-search-signup-requirements.md)]

## <a name="initialize-the-application"></a>Az alkalmazás inicializálása

1. Hozzon létre egy új JavaScript fájlt a kedvenc IDE vagy szerkesztő. Állítsa be a szigort, és adja hozzá a következő követelményt:

    ```javascript
    'use strict';
    let https = require('https');
    ```

2. Hozzon létre változókat az API-végponthoz, az előfizetési kulcshoz és a keresési kifejezéshez. `host`lehet az alábbi globális végpont, vagy az [egyéni altartomány](../../../cognitive-services/cognitive-services-custom-subdomains.md) végpont jatthatja az erőforrás azure portalján.

    ```javascript
    let subscriptionKey = 'enter key here';
    let host = 'api.cognitive.microsoft.com';
    let path = '/bing/v7.0/videos/search';
    let term = 'kittens';
    ```

## <a name="create-a-response-handler"></a>Válaszkezelő létrehozása

1. Hozzon létre `response_handler` egy függvényt, amelyet az API-ból JSON-válasz készítésére hívnak. Hozzon létre egy változót a választörzsszámára. Fűzze hozzá `data` a választ, `response.on()`ha jelző t kap a használatával.

    ```javascript
    let response_handler = function (response) {
        let body = '';
        response.on('data', function (d) {
            body += d;
        });
    };
    ```
    
   1. Ha `end` jelzésre kerül, a binghez kapcsolódó fejlécek tárolására használható `response.on()` (a vagy `bingapis` `x-msedge-`a kezdővel kezdődik). Ezután elemezd a JSON-t a `JSON.parse()` `JSON.stringify()`használatával, alakítsa át karakterláncgá a használatával, és nyomtassa ki.

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

## <a name="create-and-send-the-search-request"></a>Keresési kérelem létrehozása és elküldése

1. Hozzon létre `bing_video_search()`egy függvényt, amelynek neve . Adja hozzá a kérelem paramétereit, beleértve az állomásnevét és a fejléceket. Kódolja a keresési kifejezést, és fűzze `?q=` hozzá az elérési út paraméteréhez a paraméterrel. Ezután küldje `req.end()`el a kérést a segítségével.

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
> [Egyoldalas webalkalmazás készítése](../tutorial-bing-video-search-single-page-app.md)

## <a name="see-also"></a>Lásd még 

 [Mi az a Bing Video Search API?](../overview.md)
