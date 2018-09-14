---
title: 'Gyors útmutató: Küldési keresési lekérdezéseket a Bing Image Search API a Node.js használata'
titleSuffix: Azure Cognitive Services
description: Ez a rövid útmutató segítségével keresse meg és -rendszerképek keresése a weben a Bing Web Search API használatával.
services: cognitive-services
documentationcenter: ''
author: aahill
manager: cgronlun
ms.service: cognitive-services
ms.component: bing-image-search
ms.topic: article
ms.date: 8/20/2018
ms.author: aahi
ms.openlocfilehash: 43f0cfec6aa2d4263b6a627736c2a432b2943145
ms.sourcegitcommit: e2ea404126bdd990570b4417794d63367a417856
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/14/2018
ms.locfileid: "45576646"
---
# <a name="quickstart-send-search-queries-using-the-bing-image-search-rest-api-and-nodejs"></a>Gyors útmutató: Küldési keresési lekérdezések a Bing Image Search REST API és a Node.js használatával

Ez a rövid útmutató segítségével a Bing Image Search API az első hívását, és a egy JSON-választ kapnak. Az egyszerű JavaScript-alkalmazását egy keresési lekérdezést küld az API-t, és megjeleníti a nyers eredményeken.

Bár ez az alkalmazás JavaScript nyelven van megírva, és a Node.js, az API-ben fut, egy REST-alapú webes szolgáltatás kompatibilis szinte bármelyik programozási nyelvével.

Az ehhez a mintához forráskódja elérhető [a Githubon](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/nodejs/Search/BingImageSearchv7Quickstart.js) további hibakezelést és jegyzetek kódot.

## <a name="prerequisites"></a>Előfeltételek

* A legújabb [Node.js](https://nodejs.org/en/download/).

* A [kérelem JavaScript kódtár](https://github.com/request/request)
[!INCLUDE [cognitive-services-bing-image-search-signup-requirements](../../../../includes/cognitive-services-bing-image-search-signup-requirements.md)]

## <a name="create-and-initialize-the-application"></a>Hozzon létre, és az alkalmazás inicializálása

1. Hozzon létre egy új JavaScript-fájlt a kedvenc integrált Fejlesztőkörnyezetével vagy szerkesztőjével, és állítsa be a szigorúsága és a https követelményeket.

    ```javascript
    'use strict';
    let https = require('https');
    ```

2. Az API-végpont, kép API keresési útvonalat, az előfizetési kulcs változók létrehozása és a keresési kifejezést.
    ```javascript
    let subscriptionKey = 'enter key here';
    let host = 'api.cognitive.microsoft.com';
    let path = '/bing/v7.0/images/search';
    let term = 'tropical ocean';
    ```

## <a name="construct-the-search-request-and-query"></a>A keresési kérelmek és lekérdezési hozhatnak létre.

1. Az utolsó lépésben a változók használatával az API-kérelem egy keresés URL-cím formátuma. Vegye figyelembe, hogy a keresett kifejezés URL-kódolású előtt kell az API felé.

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

2. A kérelem library használatával a lekérdezés küldése az API-hoz. `response_handler` a következő szakaszban meghatározva.
    ```javascript
    let req = https.request(request_params, response_handler);
    req.end();
    ```

## <a name="handle-and-parse-the-response"></a>Kezelni, és a válasz elemzéséhez

1. nevű függvény meghatározása `response_handler` egy HTTP-hívással túlterhelés `response`, paraméterként. Ez a függvény belül hajtsa végre az alábbi lépéseket:
    
    1. Adjon meg egy változót, a JSON-válasz törzse tartalmazza.  
        ```javascript
        let response_handler = function (response) {
            let body = '';
        };
        ```

    2. A válasz törzse Store során a **adatok** jelző nevezzük. 
        ```javascript
        response.on('data', function (d) {
            body += d;
        });
        ```

    3. Ha egy **záró** jelző adja, a dolgozhatók fel a JSON és a URL obrázku nyomtatható visszaadott képek teljes számával együtt.
    
        ```javascript
        response.on('end', function () {
            let firstImageResult = imageResults.value[0];
            console.log(`Image result count: ${imageResults.value.length}`);
            console.log(`First image thumbnail url: ${firstImageResult.thumbnailUrl}`);
            console.log(`First image web search url: ${firstImageResult.webSearchUrl}`);
         });
        ```

## <a name="json-response"></a>JSON-válasz

A Bing Image Search API érkező válaszokat a rendszer JSON formátumban adja vissza. Ez a minta-válasz a rendszer csonkolta egyetlen eredmény megjelenítéséhez.

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
    }
}
```

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Bing – Képkeresés egyoldalas alkalmazás oktatóanyag](../tutorial-bing-image-search-single-page-app.md)

## <a name="see-also"></a>Lásd még 

* [Mi az a Bing Képkeresés?](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/overview)  
* [Próbálja ki az online interaktív bemutatót](https://azure.microsoft.com/services/cognitive-services/bing-image-search-api/)  
* [Ingyenesen a Cognitive Services hozzáférési kulcs lekérése](https://azure.microsoft.com/try/cognitive-services/?api=bing-image-search-api)  
* [Az Azure Cognitive Services – dokumentáció](https://docs.microsoft.com/azure/cognitive-services)
* [A Bing Image Search API-referencia](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference)