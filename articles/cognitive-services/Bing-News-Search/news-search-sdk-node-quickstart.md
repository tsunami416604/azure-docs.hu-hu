---
title: 'Gyors útmutató: Hajtsa végre a hírkeresési – a Bing News Search SDK for node.js használatával'
titleSuffix: Azure Cognitive Services
description: Ez a rövid útmutató segítségével hírkeresés a Bing News Search SDK használata a node.js-ben, és a válasz feldolgozása.
services: cognitive-services
author: mikedodaro
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-news-search
ms.topic: quickstart
ms.date: 01/10/2019
ms.author: v-gedod
ms.custom: seodec2018
ms.openlocfilehash: 71d42f1f7627b7f223f92c264cfb23b829a229dd
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/06/2019
ms.locfileid: "57444752"
---
# <a name="quickstart-perform-a-news-search-with-the-bing-news-search-sdk-for-nodejs"></a>Gyors útmutató: Hajtsa végre a hírkeresési Bing News Search SDK-val a node.js-ben

Ez a rövid útmutató segítségével kezdeni a keresést hírkeresés a Bing News Search SDK-val, a node.js-ben. Míg a Bing News Search REST API-val kompatibilis szinte bármelyik programozási nyelvével, az SDK biztosít egy egyszerű módja annak, hogy a szolgáltatás integrálása az alkalmazásokba. Ez a minta forráskódja találhatók [GitHub](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples/blob/master/Samples/newsSearch.js).

## <a name="prerequisites"></a>Előfeltételek

* [Node.js](https://nodejs.org/en/)

Beállítása egy konzolalkalmazást, a Bing News Search SDK használatával:
1. Futtatás `npm install ms-rest-azure` a fejlesztési környezetben.
2. Futtatás `npm install azure-cognitiveservices-newssearch` a fejlesztési környezetben.


[!INCLUDE [cognitive-services-bing-news-search-signup-requirements](../../../includes/cognitive-services-bing-news-search-signup-requirements.md)]

## <a name="create-and-initialize-the-application"></a>Az alkalmazás létrehozása és inicializálása

1. Hozza létre a `CognitiveServicesCredentials` egy példányát. Hozzon létre változókat az előfizetési kulcs és a egy keresési kifejezést.

    ```javascript
    const CognitiveServicesCredentials = require('ms-rest-azure').CognitiveServicesCredentials;
    let credentials = new CognitiveServicesCredentials('YOUR-ACCESS-KEY');
    let search_term = 'Winter Olympics'
    ```

2. Hozza létre az ügyfél:
    
    ```javascript
    const NewsSearchAPIClient = require('azure-cognitiveservices-newssearch');
    let client = new NewsSearchAPIClient(credentials);
    ```

## <a name="send-a-search-query"></a>Keresési lekérdezés küldése

3. Az ügyfél használja a keresést a lekérdezés kifejezés ebben az esetben "Es téli Olimpián" segítségével:
    
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
[Egyoldalas webalkalmazás létrehozása](tutorial-bing-news-search-single-page-app.md)
