---
title: Bing Entity Search JavaScript ügyféloldali kódtár gyors üzembe helyezése
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 03/06/2020
ms.author: aahi
ms.custom: devx-track-js
ms.openlocfilehash: 783896c4266159b167a95a2f8e9a8bb9edd8e86c
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/25/2020
ms.locfileid: "91277554"
---
Ezzel a rövid útmutatóval megkezdheti az entitások keresését a JavaScript Bing Entity Search ügyféloldali függvénytárával. Habár a Bing Entity Search REST API kompatibilis a legtöbb programozási nyelvvel, az ügyféloldali kódtár egyszerű módszert kínál a szolgáltatás integrálására az alkalmazásokba. A minta forráskódja a [githubon](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples/blob/master/Samples/entitySearch.js)található.

## <a name="prerequisites"></a>Előfeltételek

* A [Node.js](https://nodejs.org/en/download/) legújabb verziója.

* A [Node.jsBING Entity Search SDK ](https://www.npmjs.com/package/@azure/cognitiveservices-entitysearch)

Az Bing Entity Search SDK telepítése:

1. Futtatás a `npm install ms-rest-azure` fejlesztési környezetben.
2. Futtatás a `npm install @azure/cognitiveservices-entitysearch` fejlesztési környezetben.

[!INCLUDE [cognitive-services-bing-news-search-signup-requirements](~/includes/cognitive-services-bing-entity-search-signup-requirements.md)]


## <a name="create-and-initialize-the-application"></a>Az alkalmazás létrehozása és inicializálása

1. Hozzon létre egy új JavaScript-fájlt a kedvenc IDE vagy szerkesztőben, és adja hozzá az alábbi követelményeket.

    ```javascript
    const CognitiveServicesCredentials = require('ms-rest-azure').CognitiveServicesCredentials;
    const EntitySearchAPIClient = require('@azure/cognitiveservices-entitysearch');
    ```

2. Hozzon létre egy példányt az `CognitiveServicesCredentials` előfizetési kulcs használatával. Ezután hozza létre a keresési ügyfél egy példányát.

    ```javascript
    let credentials = new CognitiveServicesCredentials('YOUR-ACCESS-KEY');
    let entitySearchApiClient = new EntitySearchAPIClient(credentials);
    ```

## <a name="send-a-request-and-receive-a-response"></a>Kérelem küldése és válasz fogadása

1. Entitások keresési kérelmének küldése a szolgáltatással `entitiesOperations.search()` . A válasz fogadása után nyomtassa ki a `queryContext` , a visszaadott eredmények számát és az első eredmény leírását.

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
> [Egyoldalas webalkalmazás létrehozása](../../tutorial-bing-entities-search-single-page-app.md)

* [Mi a Bing Entity Search API?](../../overview.md)