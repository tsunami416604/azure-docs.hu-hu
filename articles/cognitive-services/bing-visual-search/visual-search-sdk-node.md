---
title: 'Gyors útmutató: képelemzések lekérése a Node. js-hez készült Bing Visual Search SDK-val'
titleSuffix: Azure Cognitive Services
description: Ezzel a rövid útmutatóval megkezdheti a rendszerképek elemzését a Bing Visual Search szolgáltatásból a Node. js SDK használatával.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-visual-search
ms.topic: quickstart
ms.date: 07/15/2019
ms.author: aahi
ms.openlocfilehash: fd69179c98e999323936daf7e83b165126c20381
ms.sourcegitcommit: 598c5a280a002036b1a76aa6712f79d30110b98d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/15/2019
ms.locfileid: "74111592"
---
# <a name="quickstart-get-image-insights-using-the-bing-visual-search-sdk-for-nodejs"></a>Gyors útmutató: képelemzések lekérése a Node. js-hez készült Bing Visual Search SDK-val

Ezzel a rövid útmutatóval megkezdheti a rendszerképek elemzését a Bing Visual Search szolgáltatásból a Node. js SDK használatával. Habár a Bing Visual Search REST API kompatibilis a legtöbb programozási nyelvvel, az SDK egyszerű módszert kínál a szolgáltatás integrálására az alkalmazásokba. A minta forráskódja a [githubon](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples/blob/master/Samples/visualSearch.js)található. 

## <a name="prerequisites"></a>Előfeltételek
* [Node.js](https://www.nodejs.org/)
* A Node. js-hez készült Bing Visual Search SDK
    * Ha a Bing Visual Search SDK-val szeretné beállítani a konzol alkalmazást, futtassa a következő parancsokat:
        1. `npm install ms-rest-azure`
        2. `npm install azure-cognitiveservices-search-visualSearch`.


[!INCLUDE [cognitive-services-bing-image-search-signup-requirements](../../../includes/cognitive-services-bing-image-search-signup-requirements.md)]

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

1. A `fs.createReadStream()` használatával olvashatja el a képfájlt, és változókat hozhat létre a keresési kérelemhez és az eredményekhez. Ezután használja az ügyfelet a képek kereséséhez.

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

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Egyoldalas Webalkalmazás létrehozása](tutorial-bing-visual-search-single-page-app.md)
