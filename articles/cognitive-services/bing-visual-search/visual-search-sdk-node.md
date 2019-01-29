---
title: 'Gyors útmutató: Lemezkép-elemzések, az a Bing Visual Search SDK for node.js használatával'
titleSuffix: Azure Cognitive Services
description: Töltsön fel egy képet, a Bing Visual Search SDK használatával, és nyerjen kapcsolatos.
services: cognitive-services
author: mikedodaro
manager: cgronlun
ms.service: cognitive-services
ms.subservice: bing-visual-search
ms.topic: quickstart
ms.date: 05/18/2018
ms.author: v-gedod
ms.openlocfilehash: fc38cc6aaffbe9353ab55dcee2b0ba73abb4ebd6
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/29/2019
ms.locfileid: "55171983"
---
# <a name="quickstart-get-image-insights-using-the-bing-visual-search-sdk-for-nodejs"></a>Gyors útmutató: Lemezkép-elemzések, az a Bing Visual Search SDK for node.js használatával

Ez a rövid útmutató segítségével hasznos képadatok lekérése a Bing Visual Search service, a Node.js SDK-val kezdeni. Míg a Bing Visual Search REST API-val kompatibilis szinte bármelyik programozási nyelvével, az SDK biztosít egy egyszerű módja annak, hogy a szolgáltatás integrálása az alkalmazásokba. Ez a minta forráskódja találhatók [GitHub](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples/blob/master/Samples/visualSearch.js). 

## <a name="prerequisites"></a>Előfeltételek
* [Node.js](https://www.nodejs.org/)
* A Bing Visual Search SDK-t, a node.js-ben
    * Állítsa be a Bing Visual Search SDK használatával egy konzolalkalmazást, futtassa a következő parancsokat:
        1. `npm install ms-rest-azure`
        2. `npm install azure-cognitiveservices-search-visualSearch`.


[!INCLUDE [cognitive-services-bing-image-search-signup-requirements](../../../includes/cognitive-services-bing-image-search-signup-requirements.md)]

<a name="client"></a>

## <a name="create-and-initialize-the-application"></a>Az alkalmazás létrehozása és inicializálása

1. Hozzon létre egy új JavaScript-fájlt a kedvenc integrált Fejlesztőkörnyezetével vagy szerkesztőjével, és adja hozzá az alábbi követelményeknek. Ezután hozzon létre változókat az előfizetési kulcs, egyéni konfiguráció azonosítója és a fájl elérési útját a feltölteni kívánt kép. 

    ```javascript
    const os = require("os");
    const async = require('async');
    const fs = require('fs');
    const Search = require('azure-cognitiveservices-visualsearch');
    const CognitiveServicesCredentials = require('ms-rest-azure').CognitiveServicesCredentials;
    
    let keyVar = 'YOUR-VISUAL-SEARCH-ACCESS-KEY';
    let credentials = new CognitiveServicesCredentials(keyVar);
    let filePath = "../Data/image.jpg";
    ```

2. Hozza létre az ügyfél.

    ```javascript
    let visualSearchApiClient = new Search.VisualSearchAPIClient(credentials);
    ```

## <a name="search-for-images"></a>Rendszerképek keresése

1. Használat `fs.createReadStream()` olvassa el a képfájlt, és hozhat létre változókat a keresési kérelmet, és az eredmények. Ezután használja az ügyfél kereshet a lemezképeket.

    ```javascript
    let fileStream = fs.createReadStream(filePath);
    let visualSearchRequest = JSON.stringify({});
    let visualSearchResults;
    try {
        visualSearchResults = await visualSearchApiClient.images.visualSearch({
            image: fileStream,
            knowledgeRequest: visualSearchRequest
        });
        console.log("Search visual search request with binary of image");
    } catch (err) {
        console.log("Encountered exception. " + err.message);
    }
    ```

2. Elemezze az előző lekérdezés eredményeit:

    ```javascript
    // Visual Search results
    if (visualSearchResults.image.imageInsightsToken) {
        console.log(`Uploaded image insights token: ${visualSearchResults.image.imageInsightsToken}`);
    }
    else {
        console.log("Couldn't find image insights token!");
    }
    
    // List of tags
    if (visualSearchResults.tags.length > 0) {
        let firstTagResult = visualSearchResults.tags[0];
        console.log(`Visual search tag count: ${visualSearchResults.tags.length}`);
    
        // List of actions in first tag
        if (firstTagResult.actions.length > 0) {
            let firstActionResult = firstTagResult.actions[0];
            console.log(`First tag action count: ${firstTagResult.actions.length}`);
            console.log(`First tag action type: ${firstActionResult.actionType}`);
        }
        else {
            console.log("Couldn't find tag actions!");
        }
    
    }
    else {
        console.log("Couldn't find image tags!");
    }
    
    ```

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Egyoldalas webes alkalmazás készítése](tutorial-bing-visual-search-single-page-app.md)
