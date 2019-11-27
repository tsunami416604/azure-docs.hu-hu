---
title: 'Rövid útmutató: Hírek keresése a Node. js-hez készült SDK-val – Bing News Search'
titleSuffix: Azure Cognitive Services
description: Ezzel a rövid útmutatóval híreket kereshet a Node. js-hez készült Bing News Search SDK-val, és feldolgozhatja a választ.
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
# <a name="quickstart-perform-a-news-search-with-the-bing-news-search-sdk-for-nodejs"></a>Gyors útmutató: Hírek keresése a Node. js-hez készült Bing News Search SDK-val

Ezzel a rövid útmutatóval megkezdheti a hírek keresését a Node. js-hez készült Bing News Search SDK-val. Habár a Bing News Search REST API kompatibilis a legtöbb programozási nyelvvel, az SDK egyszerű módszert kínál a szolgáltatás integrálására az alkalmazásokba. A minta forráskódja a [githubon](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples/blob/master/Samples/newsSearch.js)található.

## <a name="prerequisites"></a>Előfeltételek

* [Node.js](https://nodejs.org/en/)

Konzol alkalmazás beállítása a Bing News Search SDK használatával:
1. `npm install ms-rest-azure` futtatása a fejlesztési környezetben.
2. `npm install azure-cognitiveservices-newssearch` futtatása a fejlesztési környezetben.


[!INCLUDE [cognitive-services-bing-news-search-signup-requirements](../../../includes/cognitive-services-bing-news-search-signup-requirements.md)]

## <a name="create-and-initialize-the-application"></a>Az alkalmazás létrehozása és inicializálása

1. Hozza létre a `CognitiveServicesCredentials` egy példányát. Hozzon létre változókat az előfizetési kulcshoz és egy keresési kifejezéshez.

    ```javascript
    const CognitiveServicesCredentials = require('ms-rest-azure').CognitiveServicesCredentials;
    let credentials = new CognitiveServicesCredentials('YOUR-ACCESS-KEY');
    let search_term = 'Winter Olympics'
    ```

2. az ügyfél példánya:
    
    ```javascript
    const NewsSearchAPIClient = require('azure-cognitiveservices-newssearch');
    let client = new NewsSearchAPIClient(credentials);
    ```

## <a name="send-a-search-query"></a>Keresési lekérdezés küldése

1. Használja az ügyfelet egy lekérdezési kifejezéssel való keresésre, ebben az esetben a "téli olimpia":
    
    ```javascript
    client.newsOperations.search(search_term).then((result) => {
        console.log(result.value);
    }).catch((err) => {
        throw err;
    });
    ```

A kód `result.value`-elemeket jelenít meg a konzolon bármilyen szövegelemzés nélkül. Az eredmények – ha egyáltalán van találat egy kategóriában – a következőket fogják tartalmazni:

- `_type: 'NewsArticle'`
- `_type: 'WebPage'`
- `_type: 'VideoObject'`
- `_type: 'ImageObject'`

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Egyoldalas webalkalmazás létrehozása](tutorial-bing-news-search-single-page-app.md)
