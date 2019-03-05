---
title: 'Gyors útmutató: Call a Node.js használatával a Bing Custom Search-végponthoz |} A Microsoft Docs'
titlesuffix: Azure Cognitive Services
description: Ez a rövid útmutató segítségével megkezdheti a keresési eredmények kér a Bing Custom Search példány Node.js használatával
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-custom-search
ms.topic: quickstart
ms.date: 03/04/2018
ms.author: aahi
ms.openlocfilehash: 65b0e6e9fb6f04d4536b642fe58da4e6c0f1aa69
ms.sourcegitcommit: 8b41b86841456deea26b0941e8ae3fcdb2d5c1e1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/05/2019
ms.locfileid: "57336581"
---
# <a name="quickstart-call-your-bing-custom-search-endpoint-using-nodejs"></a>Gyors útmutató: Call a Node.js használatával a Bing Custom Search-végpont

Ez a rövid útmutató segítségével megkezdheti a keresési eredmények kér a Bing Custom Search-példányt. Bár ez az alkalmazás JavaScript nyelven van megírva, a Bing Custom Search API olyan kompatibilis szinte bármelyik programozási nyelvével webes RESTful szolgáltatás. A minta forráskódja a [GitHubon](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/nodejs/Search/BingCustomSearchv7.js) érhető el.

## <a name="prerequisites"></a>Előfeltételek

- Bing Custom Search-példány. Lásd: [a rövid útmutató: Az első Bing Custom Search-példány létrehozása](quick-start.md) további információt.

- [Node.js](https://www.nodejs.org/)

- A [kérelem JavaScript kódtár](https://github.com/request/request)

[!INCLUDE [cognitive-services-bing-custom-search-prerequisites](../../../includes/cognitive-services-bing-custom-search-signup-requirements.md)]

## <a name="create-and-initialize-the-application"></a>Az alkalmazás létrehozása és inicializálása

1. Hozzon létre egy új JavaScript-fájlt a kedvenc integrált Fejlesztőkörnyezetével vagy szerkesztőjével, és adja hozzá a `require()` a kérelmek Library utasítás. Hozzon létre változókat az előfizetési kulcs, egyéni konfiguráció azonosítója és a egy keresési kifejezést. 

    ```javascript
    var request = require("request");
    
    var subscriptionKey = 'YOUR-SUBSCRIPTION-KEY';
    var customConfigId = 'YOUR-CUSTOM-CONFIG-ID';
    var searchTerm = 'microsoft';
    ```

## <a name="send-and-receive-a-search-request"></a>Küldeni és fogadni egy keresési kérelmet 

1. Hozzon létre egy változót a a kérelemben küldött adatok tárolására. Hozza létre a kérelem URL-CÍMÉT a keresett kifejezés hozzáfűzésével a `q=` lekérdezési paraméter, és a keresési példány egyéni konfiguráció azonosítója a `customconfig=`. a paraméterek a egy `&` karakter. 

    ```javascript
    var info = {
        url: 'https://api.cognitive.microsoft.com/bingcustomsearch/v7.0/search?' + 
            'q=' + searchTerm + "&" +
            'customconfig=' + customConfigId,
        headers: {
            'Ocp-Apim-Subscription-Key' : subscriptionKey
        }
    }
    ```

1. A kérelem JavaScript-kódtár használatával egy keresési kérelmet küld a Bing Custom Search-példányt, és nyomtassa ki az eredményeket, beleértve a neve, URL-cím és a dátum a weblap lett utoljára bejárt információt.

    ```javascript
    request(info, function(error, response, body){
            var searchResponse = JSON.parse(body);
            for(var i = 0; i < searchResponse.webPages.value.length; ++i){
                var webPage = searchResponse.webPages.value[i];
                console.log('name: ' + webPage.name);
                console.log('url: ' + webPage.url);
                console.log('displayUrl: ' + webPage.displayUrl);
                console.log('snippet: ' + webPage.snippet);
                console.log('dateLastCrawled: ' + webPage.dateLastCrawled);
                console.log();
            }
    ```

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Egyéni keresés webes alkalmazás készítése](./tutorials/custom-search-web-page.md)
