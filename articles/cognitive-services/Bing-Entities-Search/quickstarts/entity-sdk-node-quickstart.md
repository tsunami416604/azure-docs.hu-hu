---
title: 'Gyors útmutató: keresési kérelem küldése a Node. js-hez készült SDK-val – Bing Entity Search'
titleSuffix: Azure Cognitive Services
description: Ez a rövid útmutató a Node. js-hez készült Bing Entity Search SDK-val rendelkező entitások keresésére használható.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-entity-search
ms.topic: quickstart
ms.date: 01/22/2020
ms.author: aahi
ms.openlocfilehash: 6ece3d7979dc80a2c6c576b3ce279d4fb9bc9472
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/24/2020
ms.locfileid: "76716391"
---
# <a name="quickstart-send-a-search-request-with-the-bing-entity-search-sdk-for-nodejs"></a>Gyors útmutató: keresési kérelem küldése a Node. js-hez készült Bing Entity Search SDK-val

Ezzel a rövid útmutatóval megkezdheti az entitások keresését a Node. js-hez készült Bing Entity Search SDK-val. Habár a Bing Entity Search REST API kompatibilis a legtöbb programozási nyelvvel, az SDK egyszerű módszert kínál a szolgáltatás integrálására az alkalmazásokba. A minta forráskódja a [githubon](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples/blob/master/Samples/entitySearch.js)található.

## <a name="prerequisites"></a>Előfeltételek

* A [Node.js](https://nodejs.org/en/download/) legújabb verziója.

* A [Node. js-hez készült BING Entity Search SDK](https://www.npmjs.com/package/@azure/cognitiveservices-entitysearch)

Az Bing Entity Search SDK telepítése:

1. `npm install ms-rest-azure` futtatása a fejlesztési környezetben.
2. `npm install @azure/cognitiveservices-entitysearch` futtatása a fejlesztési környezetben.

[!INCLUDE [cognitive-services-bing-news-search-signup-requirements](../../../../includes/cognitive-services-bing-entity-search-signup-requirements.md)]


## <a name="create-and-initialize-the-application"></a>Az alkalmazás létrehozása és inicializálása

1. Hozzon létre egy új JavaScript-fájlt a kedvenc IDE vagy szerkesztőben, és adja hozzá az alábbi követelményeket.

    ```javascript
    const CognitiveServicesCredentials = require('ms-rest-azure').CognitiveServicesCredentials;
    const EntitySearchAPIClient = require('@azure/cognitiveservices-entitysearch');
    ```

2. Hozzon létre egy `CognitiveServicesCredentials` példányt az előfizetési kulcs használatával. Ezután hozza létre a keresési ügyfél egy példányát.

    ```javascript
    let credentials = new CognitiveServicesCredentials('YOUR-ACCESS-KEY');
    let entitySearchApiClient = new EntitySearchAPIClient(credentials);
    ```

## <a name="send-a-request-and-receive-a-response"></a>Kérelem küldése és válasz fogadása

1. Entitások keresési kérelmének küldése `entitiesOperations.search()`sal. A válasz fogadása után nyomtassa ki a `queryContext`, a visszaadott eredmények számát és az első eredmény leírását.

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

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Egyoldalas webalkalmazás létrehozása](../tutorial-bing-entities-search-single-page-app.md)

* [Mi a Bing Entity Search API?](../overview.md )