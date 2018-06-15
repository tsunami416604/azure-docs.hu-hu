---
title: Videó keresési SDK csomópont gyors üzembe helyezés |} Microsoft Docs
description: Videó keresési SDK Konzolalkalmazás beállítása.
titleSuffix: Azure cognitive services
services: cognitive-services
author: mikedodaro
manager: rosh
ms.service: cognitive-services
ms.component: bing-video-search
ms.topic: article
ms.date: 02/12/2018
ms.author: v-gedod
ms.openlocfilehash: 5718c750288e0a5605db3296d2911cca5e03375c
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/01/2018
ms.locfileid: "35349783"
---
# <a name="video-search-sdk-node-quickstart"></a>Videó keresési SDK csomópont gyors üzembe helyezés

A Bing videó keresési SDK tartalmazza a REST API videó lekérdezések és elemzési eredmények funkcióit. 

A [forráskód csomópont Bing videó keresési SDK minták](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples/blob/master/Samples/videoSearch.js) érhető el a Git központ.

## <a name="application-dependencies"></a>Alkalmazásfüggőségek

Egy konzolalkalmazást, a Bing videó keresési SDK használatával beállításához futtassa `npm install azure-cognitiveservices-videosearch` a fejlesztői környezetben.

## <a name="video-search-client"></a>Videó keresési ügyfél
Első egy [kognitív szolgáltatások hozzáférési kulcs](https://azure.microsoft.com/try/cognitive-services/) alatt *keresési*. Hozzon létre egy példányát a `CognitiveServicesCredentials`:
```
const CognitiveServicesCredentials = require('ms-rest-azure').CognitiveServicesCredentials;
let credentials = new CognitiveServicesCredentials('YOUR-ACCESS-KEY');
```
Az ügyfél ezután példányosítható:
```
const VideoSearchAPIClient = require('azure-cognitiveservices-videosearch');
let client = new VideoSearchAPIClient(credentials);
```
Keresési eredmények.
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

[Kognitív szolgáltatások Node.js SDK-minták](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples)
