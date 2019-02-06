---
title: 'Gyors útmutató: A Bing Entity Search SDK-val keresési kérés küldése a node.js-ben'
titleSuffix: Azure Cognitive Services
description: Ez a rövid útmutató segítségével keresse meg a Bing Entity Search SDK-val entitásokat a node.js-ben
services: cognitive-services
author: mikedodaro
manager: cgronlun
ms.service: cognitive-services
ms.subservice: bing-entity-search
ms.topic: quickstart
ms.date: 02/01/2019
ms.author: v-gedod
ms.openlocfilehash: db906319c04c96fded7417c932beacf7feea5b5b
ms.sourcegitcommit: 039263ff6271f318b471c4bf3dbc4b72659658ec
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/06/2019
ms.locfileid: "55757463"
---
# <a name="quickstart-send-a-search-request-with-the-bing-entity-search-sdk-for-nodejs"></a>Gyors útmutató: A Bing Entity Search SDK-val keresési kérés küldése a node.js-ben

Ez a rövid útmutató segítségével kezdeni a keresést a Bing Entity Search SDK rendelkező entitások esetében a node.js-ben. Míg a Bing Entity Search REST API-val kompatibilis szinte bármelyik programozási nyelvével, az SDK biztosít egy egyszerű módja annak, hogy a szolgáltatás integrálása az alkalmazásokba. Ez a minta forráskódja találhatók [GitHub](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples/blob/master/Samples/entitySearch.js).

## <a name="prerequisites"></a>Előfeltételek

* A [Node.js](https://nodejs.org/en/download/) legújabb verziója.

* A [a Bing Entity Search SDK for node.js használatával](https://www.npmjs.com/package/azure-cognitiveservices-entitysearch)

A Bing Entity Search SDK telepítése:

1. Futtatás `npm install ms-rest-azure` a fejlesztési környezetben.
2. Futtatás `npm install azure-cognitiveservices-entitysearch` a fejlesztési környezetben.

[!INCLUDE [cognitive-services-bing-news-search-signup-requirements](../../../../includes/cognitive-services-bing-entity-search-signup-requirements.md)]


## <a name="create-and-initialize-the-application"></a>Az alkalmazás létrehozása és inicializálása

1. Hozzon létre egy új JavaScript-fájlt a kedvenc integrált Fejlesztőkörnyezetével vagy szerkesztőjével, és adja hozzá az alábbi követelményeknek. 
    
    ```javascript
    const CognitiveServicesCredentials = require('ms-rest-azure').CognitiveServicesCredentials;
    const EntitySearchAPIClient = require('azure-cognitiveservices-entitysearch');
    ```

2. Hozzon létre egy példányt `CognitiveServicesCredentials` az előfizetési kulcs használatával. Ezután hozzon létre egy példányt a keresési ügyfél vele.

    ```javascript
    let credentials = new CognitiveServicesCredentials('YOUR-ACCESS-KEY');
    let entitySearchApiClient = new EntitySearchAPIClient(credentials);
    ```

## <a name="send-a-request-and-receive-a-response"></a>A kéréseket küldeni és fogadni a választ

2. Az entitások keresési kérelem küldése `entitiesOperations.search()`. Miután kapott választ, nyomtassa ki a `queryContext`kapott találatok közül, és az első eredmény leírása számát.
      
    ```javascript
    entitySearchApiClient.entitiesOperations.search('seahawks').then((result) => {
        console.log(result.queryContext);
        console.log(result.entities.value);
        console.log(result.entities.value[0].description);
    }).catch((err) => {
        throw err;
    });
    ```

<!-- Removing until we can replace with a sanitized version.
![Entity results](media/entity-search-sdk-node-quickstart-results.png)
-->

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Egyoldalas webalkalmazás létrehozása](../tutorial-bing-entities-search-single-page-app.md)

* [Mi az a Bing Entity Search API?](../overview.md )