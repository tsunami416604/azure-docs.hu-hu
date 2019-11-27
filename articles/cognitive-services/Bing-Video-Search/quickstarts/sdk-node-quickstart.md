---
title: 'Gyors útmutató: videók keresése a Node. js-hez készült SDK-val – Bing Video Search'
titleSuffix: Azure Cognitive Services
description: Ez a rövid útmutató a video-keresési kérelmek küldésére használható a Node. js-hez készült Bing Video Search SDK-val
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-video-search
ms.topic: quickstart
ms.date: 07/18/2019
ms.author: aahi
ms.openlocfilehash: 5c8bd4ccadcc3c1947905e6bd74b48045a62ab57
ms.sourcegitcommit: f523c8a8557ade6c4db6be12d7a01e535ff32f32
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/22/2019
ms.locfileid: "74383738"
---
# <a name="quickstart-perform-a-video-search-with-the-bing-video-search-sdk-for-nodejs"></a>Gyors útmutató: videós keresés végrehajtása a Node. js-hez készült Bing Video Search SDK-val

Ezzel a rövid útmutatóval megkezdheti a hírek keresését a Node. js-hez készült Bing Video Search SDK-val. Habár a Bing Video Search REST API kompatibilis a legtöbb programozási nyelvvel, az SDK egyszerű módszert kínál a szolgáltatás integrálására az alkalmazásokba. A minta forráskódja a [githubon](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples/blob/master/Samples/videoSearch.js)található. További megjegyzéseket és funkciókat tartalmaz.

## <a name="prerequisites"></a>Előfeltételek

- [Node.js](https://www.nodejs.org/)

Konzol alkalmazás beállítása a Bing Video Search SDK használatával:
* `npm install ms-rest-azure` futtatása a fejlesztési környezetben.
* `npm install azure-cognitiveservices-videosearch` futtatása a fejlesztési környezetben.

[!INCLUDE [cognitive-services-bing-video-search-signup-requirements](../../../../includes/cognitive-services-bing-video-search-signup-requirements.md)]

## <a name="create-and-initialize-the-application"></a>Az alkalmazás létrehozása és inicializálása

1. Hozzon létre egy új JavaScript-fájlt a kedvenc IDE vagy szerkesztőben, és adjon hozzá egy `require()` utasítást a Bing Video Search SDK-hoz és `CognitiveServicesCredentials` modulhoz. Hozzon létre egy változót az előfizetési kulcshoz. 
    
    ```javascript
    const CognitiveServicesCredentials = require('ms-rest-azure').CognitiveServicesCredentials;
    const VideoSearchAPIClient = require('azure-cognitiveservices-videosearch');
    ```

2. Hozzon létre egy `CognitiveServicesCredentials`-példányt a kulccsal. Ezután a használatával hozza létre a videó-keresési ügyfél egy példányát.

    ```javascript
    let credentials = new CognitiveServicesCredentials('YOUR-ACCESS-KEY');
    let client = new VideoSearchAPIClient(credentials);
    ```

## <a name="send-the-search-request"></a>A keresési kérelem elküldése

1. A `client.videosOperations.search()` használatával küldhet keresési kéréseket a Bing Video Search API. A keresési eredmények visszaadásakor a `.then()` használatával naplózza az eredményt.
    
    ```javascript
    client.videosOperations.search('Interstellar Trailer').then((result) => {
        console.log(result.value);
    }).catch((err) => {
        throw err;
    });
    ```

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Egyoldalas Webalkalmazás létrehozása](../tutorial-bing-video-search-single-page-app.md)

## <a name="see-also"></a>Lásd még 

* [Mi a Bing Video Search API?](../overview.md)
* [Cognitive services .NET SDK-minták](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/BingSearchv7)