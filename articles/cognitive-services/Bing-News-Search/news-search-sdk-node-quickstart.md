---
title: 'Quickstart: Perform a news search using the SDK for Node.js - Bing News Search'
titleSuffix: Azure Cognitive Services
description: Use this quickstart to search for news using the Bing News Search SDK for Node.js, and process the response.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-news-search
ms.topic: quickstart
ms.date: 06/18/2019
ms.author: aahi
ms.custom: seodec2018
ms.openlocfilehash: 9bbad7685cf7d562189871bb5fa4a56d69287db4
ms.sourcegitcommit: f523c8a8557ade6c4db6be12d7a01e535ff32f32
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/22/2019
ms.locfileid: "74383888"
---
# <a name="quickstart-perform-a-news-search-with-the-bing-news-search-sdk-for-nodejs"></a>Quickstart: Perform a news search with the Bing News Search SDK for Node.js

Use this quickstart to begin searching for news with the Bing News Search SDK for Node.js. While Bing News Search has a REST API compatible with most programming languages, the SDK provides an easy way to integrate the service into your applications. The source code for this sample can be found on [GitHub](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples/blob/master/Samples/newsSearch.js).

## <a name="prerequisites"></a>Előfeltételek

* [Node.js](https://nodejs.org/en/)

To set up a console application using the Bing News Search SDK:
1. Run `npm install ms-rest-azure` in your development environment.
2. Run `npm install azure-cognitiveservices-newssearch` in your development environment.


[!INCLUDE [cognitive-services-bing-news-search-signup-requirements](../../../includes/cognitive-services-bing-news-search-signup-requirements.md)]

## <a name="create-and-initialize-the-application"></a>Az alkalmazás létrehozása és inicializálása

1. Hozza létre a `CognitiveServicesCredentials` egy példányát. Create variables for your subscription key, and a search term.

    ```javascript
    const CognitiveServicesCredentials = require('ms-rest-azure').CognitiveServicesCredentials;
    let credentials = new CognitiveServicesCredentials('YOUR-ACCESS-KEY');
    let search_term = 'Winter Olympics'
    ```

2. instantiate the client:
    
    ```javascript
    const NewsSearchAPIClient = require('azure-cognitiveservices-newssearch');
    let client = new NewsSearchAPIClient(credentials);
    ```

## <a name="send-a-search-query"></a>Send a search query

1. Use the client to search with a query term, in this case "Winter Olympics":
    
    ```javascript
    client.newsOperations.search(search_term).then((result) => {
        console.log(result.value);
    }).catch((err) => {
        throw err;
    });
    ```

A kód `result.value` elemeket ír ki a konzolra a szöveg elemzése nélkül. Az eredmények, ha vannak, kategóriánként a következőket tartalmazzák:

- `_type: 'NewsArticle'`
- `_type: 'WebPage'`
- `_type: 'VideoObject'`
- `_type: 'ImageObject'`

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Egyoldalas webalkalmazás létrehozása](tutorial-bing-news-search-single-page-app.md)
