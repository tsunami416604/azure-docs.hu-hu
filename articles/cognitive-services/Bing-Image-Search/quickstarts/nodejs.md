---
title: 'Gyorsútmutató: Képek keresése a Bing Image Search REST API-val és a Node.js'
titleSuffix: Azure Cognitive Services
description: Ezzel a rövid útmutatóval képkeresési kérelmeket küldhet a Bing Image Search REST API-nak JavaScript használatával, és JSON-választ kaphat.
services: cognitive-services
documentationcenter: ''
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-image-search
ms.topic: quickstart
ms.date: 03/31/2020
ms.author: aahi
ms.custom: seodec2018
ms.openlocfilehash: 15757d606a846a2951bc5c15d8d5ef0dbfd7a2a1
ms.sourcegitcommit: efefce53f1b75e5d90e27d3fd3719e146983a780
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/01/2020
ms.locfileid: "80478593"
---
# <a name="quickstart-search-for-images-using-the-bing-image-search-rest-api-and-nodejs"></a>Gyorsútmutató: Képek keresése a Bing Image Search REST API-val és a Node.js

Ezzel a rövid útmutatóval elindíthatja a keresési kérelmek küldését a Bing Image Search API-nak. Ez a JavaScript-alkalmazás keresési lekérdezést küld az API-nak, és megjeleníti az első kép URL-címét az eredményekközött. Bár ez az alkalmazás Javascript nyelven íródott, az API egy RESTful webszolgáltatás, amely kompatibilis a legtöbb programozási nyelvvel.

A minta forráskódja további hibakezeléssel és megjegyzésekkel együtt elérhető a [GitHubon](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/nodejs/Search/BingImageSearchv7Quickstart.js).

## <a name="prerequisites"></a>Előfeltételek

* A [Node.js](https://nodejs.org/en/download/) legújabb verziója.

* A [JavaScript lekérési kódtára](https://github.com/request/request)  

[!INCLUDE [cognitive-services-bing-image-search-signup-requirements](../../../../includes/cognitive-services-bing-image-search-signup-requirements.md)]

Lásd még: [Cognitive Services Pricing - Bing Search API](https://azure.microsoft.com/pricing/details/cognitive-services/search-api/).

## <a name="create-and-initialize-the-application"></a>Az alkalmazás létrehozása és inicializálása

1. Hozzon létre egy új JavaScript-fájlt a kedvenc IDE-környezetében vagy szerkesztőjében, és állítsa be a szigorúságot, a https-követelményeket.

    ```javascript
    'use strict';
    let https = require('https');
    ```

2. Hozza létre az API-végpont, az Image API keresési útvonala, az előfizetési kulcs és a keresőkifejezés változóit. `host`lehet az alábbi globális végpont, vagy az [egyéni altartomány](../../../cognitive-services/cognitive-services-custom-subdomains.md) végpont jatthatja az erőforrás azure portalján.

    ```javascript
    let subscriptionKey = 'enter key here';
    let host = 'api.cognitive.microsoft.com';
    let path = '/bing/v7.0/images/search';
    let term = 'tropical ocean';
    ```

## <a name="construct-the-search-request-and-query"></a>A keresési kérelem és a lekérdezés összeállítása.

1. Az előző lépés változóit használva formázza a keresési URL-címet az API-kérelemhez. A keresési kifejezést URL-kódolásúnak kell lennie, mielőtt elküldenék az API-nak.

    ```javascript
    let request_params = {
        method : 'GET',
        hostname : host,
        path : path + '?q=' + encodeURIComponent(search),
        headers : {
        'Ocp-Apim-Subscription-Key' : subscriptionKey,
        }
    };
    ```

2. A lekérési kódtár használatával küldje el a lekérdezést az API-nak. A `response_handler` a következő szakaszban lesz definiálva.
    ```javascript
    let req = https.request(request_params, response_handler);
    req.end();
    ```

## <a name="handle-and-parse-the-response"></a>A válasz kezelése és elemzése

1. definiáljon egy `response_handler` nevű függvényt, amely paraméterként a `response` HTTP-hívást használja. A funkción belül tegye a következő lépéseket:

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

    3. Ha egy **záró** jelző t jelez, a JSON-válasz első eredményét kapja meg. Nyomtassa ki az első kép URL-címét a visszaadott képek teljes számával együtt.

        ```javascript
        response.on('end', function () {
            let firstImageResult = imageResults.value[0];
            console.log(`Image result count: ${imageResults.value.length}`);
            console.log(`First image thumbnail url: ${firstImageResult.thumbnailUrl}`);
            console.log(`First image web search url: ${firstImageResult.webSearchUrl}`);
         });
        ```

## <a name="example-json-response"></a>Példa JSON-válaszra

A Bing Image Search API válaszai JSON formátumban érkeznek vissza. A mintaválasz egyetlen eredményre van csonkolva.

```json
{
"_type":"Images",
"instrumentation":{
    "_type":"ResponseInstrumentation"
},
"readLink":"images\/search?q=tropical ocean",
"webSearchUrl":"https:\/\/www.bing.com\/images\/search?q=tropical ocean&FORM=OIIARP",
"totalEstimatedMatches":842,
"nextOffset":47,
"value":[
    {
        "webSearchUrl":"https:\/\/www.bing.com\/images\/search?view=detailv2&FORM=OIIRPO&q=tropical+ocean&id=8607ACDACB243BDEA7E1EF78127DA931E680E3A5&simid=608027248313960152",
        "name":"My Life in the Ocean | The greatest WordPress.com site in ...",
        "thumbnailUrl":"https:\/\/tse3.mm.bing.net\/th?id=OIP.fmwSKKmKpmZtJiBDps1kLAHaEo&pid=Api",
        "datePublished":"2017-11-03T08:51:00.0000000Z",
        "contentUrl":"https:\/\/mylifeintheocean.files.wordpress.com\/2012\/11\/tropical-ocean-wallpaper-1920x12003.jpg",
        "hostPageUrl":"https:\/\/mylifeintheocean.wordpress.com\/",
        "contentSize":"897388 B",
        "encodingFormat":"jpeg",
        "hostPageDisplayUrl":"https:\/\/mylifeintheocean.wordpress.com",
        "width":1920,
        "height":1200,
        "thumbnail":{
        "width":474,
        "height":296
        },
        "imageInsightsToken":"ccid_fmwSKKmK*mid_8607ACDACB243BDEA7E1EF78127DA931E680E3A5*simid_608027248313960152*thid_OIP.fmwSKKmKpmZtJiBDps1kLAHaEo",
        "insightsMetadata":{
        "recipeSourcesCount":0,
        "bestRepresentativeQuery":{
            "text":"Tropical Beaches Desktop Wallpaper",
            "displayText":"Tropical Beaches Desktop Wallpaper",
            "webSearchUrl":"https:\/\/www.bing.com\/images\/search?q=Tropical+Beaches+Desktop+Wallpaper&id=8607ACDACB243BDEA7E1EF78127DA931E680E3A5&FORM=IDBQDM"
        },
        "pagesIncludingCount":115,
        "availableSizesCount":44
        },
        "imageId":"8607ACDACB243BDEA7E1EF78127DA931E680E3A5",
        "accentColor":"0050B2"
    }]
}
```

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Egyoldalas alkalmazás létrehozása](../tutorial-bing-image-search-single-page-app.md)

## <a name="see-also"></a>Lásd még

* [Mi a Bing Image Search?](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/overview)  
* [Online interaktív bemutató kipróbálása](https://azure.microsoft.com/services/cognitive-services/bing-image-search-api/) 
* A Bing Search API-k [díjszabási részletei.](https://azure.microsoft.com/pricing/details/cognitive-services/search-api/) 
* [Ingyenes Cognitive Services hozzáférési kulcs beszerzése](https://azure.microsoft.com/try/cognitive-services/?api=bing-image-search-api)  
* [Az Azure Cognitive Services dokumentációja](https://docs.microsoft.com/azure/cognitive-services)
* [Bing Image Search API – referencia](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference)
