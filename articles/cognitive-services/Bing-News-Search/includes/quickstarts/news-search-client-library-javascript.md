---
title: Bing News Search JavaScript-ügyféltár – rövid útmutató
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 03/12/2020
ms.author: aahi
ms.openlocfilehash: 858e6b9e0e40ab988a4cdf04b31580c1ca28d40a
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "79503894"
---
Ezzel a rövid útmutatóval megkezdheti a hírek keresését a Bing News Search javascript-ügyfélkönyvtárában. Bár a Bing News Search a legtöbb programozási nyelvvel kompatibilis REST API-val rendelkezik, az ügyfélkódtár egyszerű módot kínál a szolgáltatás alkalmazásokba való integrálására. A minta forráskódja megtalálható a [GitHubon.](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples/blob/master/Samples/newsSearch.js)

## <a name="prerequisites"></a>Előfeltételek

* [Node.js](https://nodejs.org/en/)

Konzolalkalmazás beállítása a Bing News Search ügyféltár használatával:
1. Futtassa `npm install ms-rest-azure` a fejlesztői környezetben.
2. Futtassa `npm install azure-cognitiveservices-newssearch` a fejlesztői környezetben.


[!INCLUDE [cognitive-services-bing-news-search-signup-requirements](~/includes/cognitive-services-bing-news-search-signup-requirements.md)]

## <a name="create-and-initialize-the-application"></a>Az alkalmazás létrehozása és inicializálása

1. Hozza létre a `CognitiveServicesCredentials` egy példányát. Hozzon létre változókat az előfizetési kulcshoz és egy keresési kifejezést.

    ```javascript
    const CognitiveServicesCredentials = require('ms-rest-azure').CognitiveServicesCredentials;
    let credentials = new CognitiveServicesCredentials('YOUR-ACCESS-KEY');
    let search_term = 'Winter Olympics'
    ```

2. az ügyfél példányosítsa:
    
    ```javascript
    const NewsSearchAPIClient = require('azure-cognitiveservices-newssearch');
    let client = new NewsSearchAPIClient(credentials);
    ```

## <a name="send-a-search-query"></a>Keresési lekérdezés küldése

1. Használja az ügyfél keresni egy lekérdezés kifejezés, ebben az esetben a "Téli olimpia":
    
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
> [Egyoldalas webalkalmazás létrehozása](../../tutorial-bing-news-search-single-page-app.md)
