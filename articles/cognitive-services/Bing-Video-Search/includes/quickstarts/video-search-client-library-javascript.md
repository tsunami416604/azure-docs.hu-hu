---
title: Bing Video Search JavaScript-ügyféltár – rövid útmutató
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 03/19/2020
ms.author: aahi
ms.openlocfilehash: b642d981b79d13857881ec8cc5796e6365003ace
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "80289762"
---
Ezzel a rövid útmutatóval megkezdheti a hírek keresését a Bing Video Search ügyfélkódtárjavaScripthez. Bár a Bing Video Search a legtöbb programozási nyelvvel kompatibilis REST API-val rendelkezik, az ügyfélkódtár egyszerű módot kínál a szolgáltatás alkalmazásokba való integrálására. A minta forráskódja megtalálható a [GitHubon.](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples/blob/master/Samples/videoSearch.js) Több jegyzetet és funkciót tartalmaz.

## <a name="prerequisites"></a>Előfeltételek

- [Node.js](https://www.nodejs.org/)

Konzolalkalmazás beállítása a Bing Video Search ügyféltár használatával:
* Futtassa `npm install ms-rest-azure` a fejlesztői környezetben.
* Futtassa `npm install azure-cognitiveservices-videosearch` a fejlesztői környezetben.

[!INCLUDE [cognitive-services-bing-video-search-signup-requirements](~/includes/cognitive-services-bing-video-search-signup-requirements.md)]

## <a name="create-and-initialize-the-application"></a>Az alkalmazás létrehozása és inicializálása

1. Hozzon létre egy új JavaScript-fájlt kedvenc `require()` IDE-jében vagy szerkesztőjében, és adjon hozzá egy utasítást a Bing Video Search ügyfélkönyvtárhoz és `CognitiveServicesCredentials` -modulhoz. Hozzon létre egy változót az előfizetési kulcshoz. 
    
    ```javascript
    const CognitiveServicesCredentials = require('ms-rest-azure').CognitiveServicesCredentials;
    const VideoSearchAPIClient = require('azure-cognitiveservices-videosearch');
    ```

2. Hozzon létre `CognitiveServicesCredentials` egy példányt a kulccsal. Ezután használja a videokereső ügyfél egy példányának létrehozásához.

    ```javascript
    let credentials = new CognitiveServicesCredentials('YOUR-ACCESS-KEY');
    let client = new VideoSearchAPIClient(credentials);
    ```

## <a name="send-the-search-request"></a>A keresési kérelem elküldése

1. Keresési `client.videosOperations.search()` kérelmet küldhet a Bing Video Search API-nak. A keresési eredmények visszaadásakor naplózza `.then()` az eredményt.
    
    ```javascript
    client.videosOperations.search('Interstellar Trailer').then((result) => {
        console.log(result.value);
    }).catch((err) => {
        throw err;
    });
    ```

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Egyoldalas webalkalmazás létrehozása](../../tutorial-bing-video-search-single-page-app.md)

## <a name="see-also"></a>Lásd még 

* [Mi az a Bing Video Search API?](../../overview.md)
* [Kognitív szolgáltatások .NET SDK-minták](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/BingSearchv7)