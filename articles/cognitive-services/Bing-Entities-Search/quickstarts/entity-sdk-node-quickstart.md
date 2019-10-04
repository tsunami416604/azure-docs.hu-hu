---
title: 'Gyors útmutató: Keresési kérelem küldése a Node. js-hez készült Bing Entity Search SDK-val'
titleSuffix: Azure Cognitive Services
description: Ez a rövid útmutató a Node. js-hez készült Bing Entity Search SDK-val rendelkező entitások keresésére használható.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-entity-search
ms.topic: quickstart
ms.date: 07/24/2019
ms.author: aahi
ms.openlocfilehash: 40c9062dba5eb3bbed6ee90bfdb0a74c1d6c11d5
ms.sourcegitcommit: 75a56915dce1c538dc7a921beb4a5305e79d3c7a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/24/2019
ms.locfileid: "68479030"
---
# <a name="quickstart-send-a-search-request-with-the-bing-entity-search-sdk-for-nodejs"></a>Gyors útmutató: Keresési kérelem küldése a Node. js-hez készült Bing Entity Search SDK-val

Ezzel a rövid útmutatóval megkezdheti az entitások keresését a Node. js-hez készült Bing Entity Search SDK-val. Habár a Bing Entity Search REST API kompatibilis a legtöbb programozási nyelvvel, az SDK egyszerű módszert kínál a szolgáltatás integrálására az alkalmazásokba. A minta forráskódja a [githubon](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples/blob/master/Samples/entitySearch.js)található.

## <a name="prerequisites"></a>Előfeltételek

* A [Node.js](https://nodejs.org/en/download/) legújabb verziója.

* A [Node. js-hez készült BING Entity Search SDK](https://www.npmjs.com/package/azure-cognitiveservices-entitysearch)

Az Bing Entity Search SDK telepítése:

1. Futtatás `npm install ms-rest-azure` a fejlesztési környezetben.
2. Futtatás `npm install azure-cognitiveservices-entitysearch` a fejlesztési környezetben.

[!INCLUDE [cognitive-services-bing-news-search-signup-requirements](../../../../includes/cognitive-services-bing-entity-search-signup-requirements.md)]


## <a name="create-and-initialize-the-application"></a>Az alkalmazás létrehozása és inicializálása

1. Hozzon létre egy új JavaScript-fájlt a kedvenc IDE vagy szerkesztőben, és adja hozzá az alábbi követelményeket. 
    
    ```javascript
    const CognitiveServicesCredentials = require('ms-rest-azure').CognitiveServicesCredentials;
    const EntitySearchAPIClient = require('azure-cognitiveservices-entitysearch');
    ```

2. Hozzon létre egy `CognitiveServicesCredentials` példányt az előfizetési kulcs használatával. Ezután hozza létre a keresési ügyfél egy példányát.

    ```javascript
    let credentials = new CognitiveServicesCredentials('YOUR-ACCESS-KEY');
    let entitySearchApiClient = new EntitySearchAPIClient(credentials);
    ```

## <a name="send-a-request-and-receive-a-response"></a>Kérelem küldése és válasz fogadása

1. Entitások keresési kérelmének küldése `entitiesOperations.search()`a szolgáltatással. A válasz fogadása után nyomtassa ki `queryContext`a, a visszaadott eredmények számát és az első eredmény leírását.
      
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

* [Mi a Bing Entity Search API?](../overview.md )