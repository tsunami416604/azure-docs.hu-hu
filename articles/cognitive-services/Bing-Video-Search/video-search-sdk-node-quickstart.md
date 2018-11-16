---
title: 'Rövid útmutató: Bing Video Search SDK, Node'
titleSuffix: Azure Cognitive Services
description: A Bing Video Search SDK konzolalkalmazás beállítása.
services: cognitive-services
author: mikedodaro
manager: cgronlun
ms.service: cognitive-services
ms.component: bing-video-search
ms.topic: quickstart
ms.date: 02/12/2018
ms.author: rosh
ms.openlocfilehash: 9a2544fe5a3c0bac763b9aee79c36893ec56f351
ms.sourcegitcommit: db2cb1c4add355074c384f403c8d9fcd03d12b0c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/15/2018
ms.locfileid: "51686687"
---
# <a name="quickstart-bing-video-search-sdk-with-node"></a>Rövid útmutató: Bing Video Search SDK Node használatával

A Bing Video Search SDK a REST API funkcióit biztosítja a videolekérdezésekhez és az eredmények elemzéséhez. 

A [Node Bing Video Search SDK-minták forráskódja](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples/blob/master/Samples/videoSearch.js) elérhető a GitHubon.

## <a name="application-dependencies"></a>Alkalmazásfüggőségek

A Bing Video Search SDK használatával egy konzolalkalmazást beállítása:
* Futtatás `npm install ms-rest-azure` a fejlesztési környezetben.
* Futtatás `npm install azure-cognitiveservices-videosearch` a fejlesztési környezetben.

## <a name="video-search-client"></a>Video Search-ügyfél
A [Cognitive Services hozzáférési kulcsát](https://azure.microsoft.com/try/cognitive-services/) a *Keresés* területen kérheti le. Hozza létre a `CognitiveServicesCredentials` egy példányát:
```
const CognitiveServicesCredentials = require('ms-rest-azure').CognitiveServicesCredentials;
let credentials = new CognitiveServicesCredentials('YOUR-ACCESS-KEY');
```
Ezután példányosítsa az ügyfelet:
```
const VideoSearchAPIClient = require('azure-cognitiveservices-videosearch');
let client = new VideoSearchAPIClient(credentials);
```
Keressen rá az eredményekre.
```
client.videosOperations.search('Interstellar Trailer').then((result) => {
    console.log(result.value);
}).catch((err) => {
    throw err;
});

```

<!-- Remove until the response can be replace with a sanitized version.
The code prints `result.value` items to the console without parsing any text. The results will be:
- _type: 'VideoObjectElementType'

![Video results](media/video-search-sdk-node-results.png)
-->

## <a name="next-steps"></a>További lépések

[Cognitive Services Node.js SDK-minták](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples)
