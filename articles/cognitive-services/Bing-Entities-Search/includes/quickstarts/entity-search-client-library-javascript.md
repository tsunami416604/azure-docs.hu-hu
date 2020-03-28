---
title: Bing entity Search JavaScript-ügyféltár – rövid útmutató
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 03/06/2020
ms.author: aahi
ms.openlocfilehash: ac2f83c824014e16cfbe9ab18483b914ed8b077d
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "79136766"
---
Ezzel a rövid útmutatóval megkezdheti az entitások keresését a Bing Entity Search ügyfélkódtárjavaScripthez. Bár a Bing Entity Search a legtöbb programozási nyelvvel kompatibilis REST API-val rendelkezik, az ügyfélkódtár egyszerű módot kínál a szolgáltatás alkalmazásokba való integrálására. A minta forráskódja megtalálható a [GitHubon.](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples/blob/master/Samples/entitySearch.js)

## <a name="prerequisites"></a>Előfeltételek

* A [Node.js](https://nodejs.org/en/download/) legújabb verziója.

* A [Bing entitás Keresés SDK a Node.js](https://www.npmjs.com/package/@azure/cognitiveservices-entitysearch)

A Bing Entity Search SDK telepítése:

1. Futtassa `npm install ms-rest-azure` a fejlesztői környezetben.
2. Futtassa `npm install @azure/cognitiveservices-entitysearch` a fejlesztői környezetben.

[!INCLUDE [cognitive-services-bing-news-search-signup-requirements](~/includes/cognitive-services-bing-entity-search-signup-requirements.md)]


## <a name="create-and-initialize-the-application"></a>Az alkalmazás létrehozása és inicializálása

1. Hozzon létre egy új JavaScript-fájlt kedvenc IDE-jében vagy szerkesztőjében, és adja hozzá a következő követelményeket.

    ```javascript
    const CognitiveServicesCredentials = require('ms-rest-azure').CognitiveServicesCredentials;
    const EntitySearchAPIClient = require('@azure/cognitiveservices-entitysearch');
    ```

2. Hozzon létre `CognitiveServicesCredentials` egy példányt az előfizetési kulcs használatával. Ezután hozza létre a keresési ügyfél egy példányát vele.

    ```javascript
    let credentials = new CognitiveServicesCredentials('YOUR-ACCESS-KEY');
    let entitySearchApiClient = new EntitySearchAPIClient(credentials);
    ```

## <a name="send-a-request-and-receive-a-response"></a>Kérés küldése és válasz fogadása

1. Entitáskeresési kérelem küldése `entitiesOperations.search()`a segítségével. A válasz kézhezvétele `queryContext`után nyomtassa ki a , a visszaadott eredmények számát és az első eredmény leírását.

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