---
title: 'Gyors útmutató: Keresse meg a képeket, a Bing kép Search SDK és a Node.js használatával'
description: Ebben a rövid útmutatóban a kérelem, és szűrheti a képeket, a Bing Image Search, a Node.js használata által visszaadott.
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: cagronlund
ms.service: cognitive-services
ms.component: bing-image-search
ms.topic: article
ms.date: 08/28/2018
ms.author: aahi
ms.openlocfilehash: ebe6629344c076119c0bfdaee76a69df6274ca28
ms.sourcegitcommit: e2ea404126bdd990570b4417794d63367a417856
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/14/2018
ms.locfileid: "45572583"
---
# <a name="quickstart-search-for-images-with-the-bing-image-search-sdk-and-nodejs"></a>Gyors útmutató: Keresse meg a Bing kép Search SDK és a Node.js-lemezképek

Ez a rövid útmutató segítségével győződjön meg arról, az első képkeresési Bing kép Search SDK használatával, amely egy burkoló a API-hoz, és ugyanazokat a szolgáltatásokat tartalmazza. Ez egyszerű JavaScript-alkalmazását egy kép keresési lekérdezést küld, elemzi a JSON-válasz és URL-címét adja vissza az első képet jeleníti meg.

Ehhez a mintához forráskódja elérhető a [GitHub](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples/blob/master/Samples/imageSearch.js) további hibakezelést és jegyzetek.

## <a name="prerequisites"></a>Előfeltételek

* A [a Cognitive Services Image Search SDK for node.js használatával](https://www.npmjs.com/package/azure-cognitiveservices-imagesearch)
    * Telepítés használatával `npm install azure-cognitiveservices-imagesearch`
* A [Node.js az Azure Rest](https://www.npmjs.com/package/ms-rest-azure) modul
    * Telepítés használatával `npm install ms-rest-azure`

[!INCLUDE [cognitive-services-bing-image-search-signup-requirements](../../../includes/cognitive-services-bing-image-search-signup-requirements.md)]

## <a name="create-and-initialize-the-application"></a>Hozzon létre, és az alkalmazás inicializálása

1. Hozzon létre egy új JavaScript-fájlt a kedvenc integrált Fejlesztőkörnyezetével vagy szerkesztőjével, és állítsa be a szigorúsága, https és egyéb követelményeket.

    ```javascript
    'use strict';
    https = require('https');
    const Search = require('azure-cognitiveservices-imagesearch');
    const CognitiveServicesCredentials = require('ms-rest-azure').CognitiveServicesCredentials;
    ```

2. A projekt fő metódusban hozzon létre változókat az érvényes előfizetési kulcs, a kép a Bing és a egy keresési kifejezés által visszaadott eredményeket. Ezután hozza létre a lemezkép keresési ügyfél, a kulcs használatával.

    ```javascript
    //replace this value with your valid subscription key.
    let serviceKey = "ENTER YOUR KEY HERE";

    //the search term for the request 
    let searchTerm = "canadian rockies";

    //instantiate the the image search client 
    let credentials = new CognitiveServicesCredentials(serviceKey);
    let imageSearchApiClient = new Search.ImageSearchAPIClient(credentials);

    ```

## <a name="create-an-asynchronous-helper-function"></a>Egy aszinkron segítő függvény létrehozása

1. Hozzon létre egy függvényt hívja az ügyfél aszinkron módon történik, és a választ adja a Bing Képkeresés szolgáltatásból.  
    ```javascript
    //a helper function to perform an async call to the Bing Image Search API
    const sendQuery = async () => {
        return await imageSearchApiClient.imagesOperations.search(searchTerm);
    };
    ```
## <a name="send-a-query-and-handle-the-response"></a>Lekérdezi és kezelni a válasz 

1. A segédfüggvény és leíró hívja a `promise` a lemezkép eredményeket, a válasz elemzése.

    A válasz tartalmazza a keresési eredmények, ha az első eredmény tárolja, és nyomtassa ki a részleteket, például a Miniatűr URL-cím, az eredeti URL-CÍMÉT, teljes számával együtt adja vissza a lemezképeket.  
    ```javascript
    sendQuery().then(imageResults => {
        if (imageResults == null) {
        console.log("No image results were found.");
        }
        else {
            console.log(`Total number of images returned: ${imageResults.value.length}`);
            let firstImageResult = imageResults.value[0];
            //display the details for the first image result. After running the application,
            //you can copy the resulting URLs from the console into your browser to view the image. 
            console.log(`Total number of images found: ${imageResults.value.length}`);
            console.log(`Copy these URLs to view the first image returned:`);
            console.log(`First image thumbnail url: ${firstImageResult.thumbnailUrl}`);
            console.log(`First image content url: ${firstImageResult.contentUrl}`); 
        }
      })
      .catch(err => console.error(err))
    ```

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Bing – Képkeresés egyoldalas alkalmazás oktatóanyag](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/tutorial-bing-image-search-single-page-app)

## <a name="see-also"></a>Lásd még 

* [Mi az a Bing Képkeresés?](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/overview)  
* [Próbálja ki az online interaktív bemutatót](https://azure.microsoft.com/services/cognitive-services/bing-image-search-api/)  
* [Ingyenesen a Cognitive Services hozzáférési kulcs lekérése](https://azure.microsoft.com/try/cognitive-services/?api=bing-image-search-api) 
* [Az Azure Cognitive Services SDK for node.js-minták](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples) 
* [Az Azure Cognitive Services – dokumentáció](https://docs.microsoft.com/azure/cognitive-services)
* [A Bing Image Search API-referencia](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference)