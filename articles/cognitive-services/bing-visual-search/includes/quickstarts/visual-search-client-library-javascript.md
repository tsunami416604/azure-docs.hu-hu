---
title: Bing Visual Search JavaScript ügyféloldali kódtár gyors üzembe helyezése
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 03/26/2020
ms.author: aahi
ms.custom: devx-track-js
ms.openlocfilehash: 8b69dbbf84bf2a15200b0d58f57f6b5448bf13ce
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/25/2020
ms.locfileid: "91326838"
---
Ezzel a rövid útmutatóval megkezdheti a rendszerképek elemzését a Bing Visual Search szolgáltatásból a JavaScript ügyféloldali kódtár használatával. Habár a Bing Visual Search REST API kompatibilis a legtöbb programozási nyelvvel, az ügyféloldali kódtár egyszerű módszert kínál a szolgáltatás integrálására az alkalmazásokba. A minta forráskódja a [githubon](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples/blob/master/Samples/visualSearch.js)található. 

[Dokumentáció](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-visualsearch/?view=azure-node-latest)  |  [Könyvtár forráskódja](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/cognitiveservices/cognitiveservices-visualsearch)  |  [Csomag (NPM)](https://www.npmjs.com/package/@azure/cognitiveservices-visualsearch)  |  [Példák](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples/)

## <a name="prerequisites"></a>Előfeltételek
* [Node.js](https://www.nodejs.org/)
* A JavaScripthez készült Bing Visual Search ügyféloldali kódtár
    * A következő parancsok futtatásával állíthatja be a konzol alkalmazást a Bing Visual Search ügyféloldali kódtár használatával:
        1. `npm install ms-rest-azure`
        2. `npm install azure-cognitiveservices-visualsearch`.


[!INCLUDE [cognitive-services-bing-visual-search-signup-requirements](~/includes/cognitive-services-bing-visual-search-signup-requirements.md)]

<a name="client"></a>

## <a name="create-and-initialize-the-application"></a>Az alkalmazás létrehozása és inicializálása

1. Hozzon létre egy új JavaScript-fájlt a kedvenc IDE vagy szerkesztőben, és adja hozzá az alábbi követelményeket. Ezután hozzon létre változókat az előfizetési kulcshoz, az egyéni konfigurációs AZONOSÍTÓhoz és a feltölteni kívánt rendszerkép elérési útjához. 

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

2. Hozza létre az ügyfelet.

    ```javascript
    let visualSearchClient = new Search.VisualSearchClient(credentials);
    ```

## <a name="search-for-images"></a>Képek keresése

1. A használatával `fs.createReadStream()` beolvashatja a képfájlt, és változókat hozhat létre a keresési kérelemhez és az eredményekhez. Ezután használja az ügyfelet a képek kereséséhez.

    ```javascript
    let fileStream = fs.createReadStream(filePath);
    let visualSearchRequest = JSON.stringify({});
    let visualSearchResults;
    try {
        visualSearchResults = await visualSearchClient.images.visualSearch({
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

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Egyoldalas webalkalmazás készítése](../../tutorial-bing-visual-search-single-page-app.md)
