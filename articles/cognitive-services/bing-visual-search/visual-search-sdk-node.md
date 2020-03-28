---
title: 'Rövid útmutató: Képelemzési információk beszereznie a Node.js SDK használatával – Bing Vizuális keresés'
titleSuffix: Azure Cognitive Services
description: Ezzel a rövid útmutatóval megkezdheti a rendszerelemzéseket a Bing Visual Search szolgáltatásból a Node.js SDK használatával.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-visual-search
ms.topic: quickstart
ms.date: 12/17/2019
ms.author: aahi
ms.openlocfilehash: d99aa2d2827716b2b04d059e47d9768eef8cd690
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77485097"
---
# <a name="quickstart-get-image-insights-using-the-bing-visual-search-sdk-for-nodejs"></a>Rövid útmutató: Képelemzési információk beszereznie a Bing Visual Search SDK segítségével a Node.js

Ezzel a rövid útmutatóval megkezdheti a rendszerelemzéseket a Bing Visual Search szolgáltatásból a Node.js SDK használatával. Bár a Bing Visual Search a legtöbb programozási nyelvvel kompatibilis REST API-val rendelkezik, az SDK segítségével egyszerűen integrálhatja a szolgáltatást az alkalmazásokba. A minta forráskódja megtalálható a [GitHubon.](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples/blob/master/Samples/visualSearch.js) 

## <a name="prerequisites"></a>Előfeltételek
* [Node.js](https://www.nodejs.org/)
* A Bing Visual Search SDK a Node.js
    * Ha a Bing Visual Search SDK használatával szeretne beállítani egy konzolalkalmazást, futtassa a következő parancsokat:
        1. `npm install ms-rest-azure`
        2. `npm install azure-cognitiveservices-visualsearch`.


[!INCLUDE [cognitive-services-bing-visual-search-signup-requirements](../../../includes/cognitive-services-bing-visual-search-signup-requirements.md)]

<a name="client"></a>

## <a name="create-and-initialize-the-application"></a>Az alkalmazás létrehozása és inicializálása

1. Hozzon létre egy új JavaScript-fájlt kedvenc IDE-jében vagy szerkesztőjében, és adja hozzá a következő követelményeket. Ezután hozzon létre változókat az előfizetési kulcshoz, az egyéni konfigurációazonosítóhoz és a feltölteni kívánt lemezkép fájlelérési útjához. 

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

2. Az ügyfél példányosítsa meg.

    ```javascript
    let visualSearchClient = new Search.VisualSearchClient(credentials);
    ```

## <a name="search-for-images"></a>Képek keresése

1. Olvasásra használható `fs.createReadStream()` a képfájlban, és változókat hozhat létre a keresési kérelemhez és az eredményekhez. Ezután használja az ügyfelet a képek kereséséhez.

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
> [Egyoldalas webalkalmazás készítése](tutorial-bing-visual-search-single-page-app.md)
