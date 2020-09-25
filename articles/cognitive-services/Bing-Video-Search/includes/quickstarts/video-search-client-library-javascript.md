---
title: Bing Video Search JavaScript ügyféloldali kódtár gyors üzembe helyezése
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 03/19/2020
ms.author: aahi
ms.custom: devx-track-js
ms.openlocfilehash: da45a404c52551e27ffc84d2a0c3e1d08f817593
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/25/2020
ms.locfileid: "91328741"
---
Ezzel a rövid útmutatóval megkezdheti a hírek keresését a JavaScripthez készült Bing Video Search ügyféloldali kódtár használatával. Habár a Bing Video Search REST API kompatibilis a legtöbb programozási nyelvvel, az ügyféloldali kódtár egyszerű módszert kínál a szolgáltatás integrálására az alkalmazásokba. A minta forráskódja a [githubon](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples/blob/master/Samples/videoSearch.js)található. További megjegyzéseket és funkciókat tartalmaz.

## <a name="prerequisites"></a>Előfeltételek

- [Node.js](https://www.nodejs.org/)

Console-alkalmazás beállítása a Bing Video Search ügyféloldali kódtár használatával:
* Futtatás a `npm install ms-rest-azure` fejlesztési környezetben.
* Futtatás a `npm install azure-cognitiveservices-videosearch` fejlesztési környezetben.

[!INCLUDE [cognitive-services-bing-video-search-signup-requirements](~/includes/cognitive-services-bing-video-search-signup-requirements.md)]

## <a name="create-and-initialize-the-application"></a>Az alkalmazás létrehozása és inicializálása

1. Hozzon létre egy új JavaScript-fájlt a kedvenc IDE vagy szerkesztőben, és adjon hozzá egy `require()` utasítást a Bing Video Search ügyféloldali függvénytárához és a `CognitiveServicesCredentials` modulhoz. Hozzon létre egy változót az előfizetési kulcshoz. 
    
    ```javascript
    const CognitiveServicesCredentials = require('ms-rest-azure').CognitiveServicesCredentials;
    const VideoSearchAPIClient = require('azure-cognitiveservices-videosearch');
    ```

2. Hozzon létre egy példányt a `CognitiveServicesCredentials` kulcsával. Ezután a használatával hozza létre a videó-keresési ügyfél egy példányát.

    ```javascript
    let credentials = new CognitiveServicesCredentials('YOUR-ACCESS-KEY');
    let client = new VideoSearchAPIClient(credentials);
    ```

## <a name="send-the-search-request"></a>A keresési kérelem elküldése

1. A használatával `client.videosOperations.search()` keresési kérést küldhet a Bing Video Search APInak. A keresési eredmények visszaadásakor a paranccsal `.then()` naplózhatja az eredményt.
    
    ```javascript
    client.videosOperations.search('Interstellar Trailer').then((result) => {
        console.log(result.value);
    }).catch((err) => {
        throw err;
    });
    ```

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Egyoldalas Webalkalmazás létrehozása](../../tutorial-bing-video-search-single-page-app.md)

## <a name="see-also"></a>Lásd még 

* [Mi az a Bing Video Search API?](../../overview.md)
* [A kognitív szolgáltatások .NET SDK-minták](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/BingSearchv7)