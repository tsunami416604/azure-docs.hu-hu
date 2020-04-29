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
ms.openlocfilehash: ac2f83c824014e16cfbe9ab18483b914ed8b077d
ms.sourcegitcommit: 34a6fa5fc66b1cfdfbf8178ef5cdb151c97c721c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "79136766"
---
Ezzel a rövid útmutatóval megkezdheti az entitások keresését a JavaScript Bing Entity Search ügyféloldali függvénytárával. Habár a Bing Entity Search REST API kompatibilis a legtöbb programozási nyelvvel, az ügyféloldali kódtár egyszerű módszert kínál a szolgáltatás integrálására az alkalmazásokba. A minta forráskódja a [githubon](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples/blob/master/Samples/entitySearch.js)található.

## <a name="prerequisites"></a>Előfeltételek

* A [Node.js](https://nodejs.org/en/download/) legújabb verziója.

* A [Node. js-hez készült BING Entity Search SDK](https://www.npmjs.com/package/@azure/cognitiveservices-entitysearch)

Az Bing Entity Search SDK telepítése:

1. Futtatás `npm install ms-rest-azure` a fejlesztési környezetben.
2. Futtatás `npm install @azure/cognitiveservices-entitysearch` a fejlesztési környezetben.

[!INCLUDE [cognitive-services-bing-news-search-signup-requirements](~/includes/cognitive-services-bing-entity-search-signup-requirements.md)]


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
> [Egyoldalas webalkalmazás létrehozása](../../tutorial-bing-entities-search-single-page-app.md)

* [Mi a Bing Entity Search API?](../../overview.md)