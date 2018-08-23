---
title: 'Gyors útmutató: A kérés és az SDK-t használó Node.js-ben rendszerképek szűrése'
description: Ebben a rövid útmutatóban a kérelem, és szűrheti a képeket, a Bing Image Search, a Node.js használata által visszaadott.
titleSuffix: Azure cognitive services
services: cognitive-services
author: mikedodaro
manager: rosh
ms.service: cognitive-services
ms.component: bing-image-search
ms.topic: article
ms.date: 02/12/2018
ms.author: v-gedod
ms.openlocfilehash: e88c045b220192a617e6b8caf5d8d53f70a25b5e
ms.sourcegitcommit: a2ae233e20e670e2f9e6b75e83253bd301f5067c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/13/2018
ms.locfileid: "41987534"
---
# <a name="quickstart-request-and-filter-images-using-the-sdk-and-nodejs"></a>Gyors útmutató: A kérés és az SDK és a Node.js használatával rendszerképek szűrése

Kép a Bing Search SDK tartalmazza a REST API, a lemezkép-lekérdezések és elemzési eredmények funkcióit. 

A [forráskódját csomópont Bing kép Search SDK-minták](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples/blob/master/Samples/imageSearch.js) Github érhető el.

## <a name="application-dependencies"></a>Alkalmazásfüggőségek

Állítsa be kép a Bing Search SDK használatával egy konzolalkalmazást, futtassa `npm install azure-cognitiveservices-imagesearch` a fejlesztési környezetben.

## <a name="image-search-client"></a>Kép keresési ügyfél
Get- [Cognitive Services hozzáférési kulcs](https://azure.microsoft.com/try/cognitive-services/) alatt *keresési*. Hozzon létre egy példányt a `CognitiveServicesCredentials`:
```
const CognitiveServicesCredentials = require('ms-rest-azure').CognitiveServicesCredentials;
let credentials = new CognitiveServicesCredentials('YOUR-ACCESS-KEY');
```
Ezután hozza létre az ügyfél:
```
const ImageSearchAPIClient = require('azure-cognitiveservices-imagesearch');
let client = new ImageSearchAPIClient(credentials);
```
Az ügyfél használja a keresést a lekérdezés szövege, ebben az esetben "El Capitan" segítségével:
```
client.imagesOperations.search('El Capitan', function (err, result, request, response) {
    if (err) throw err;
    console.log(result.value);
});

```
<!-- Need to sanitize result
The code prints `result.value` items to the console without parsing any text. The results will be:
- _type: 'ImageObjectElementType'

![Imageresults](media/node-sdk-quickstart-image-results.png)
-->

## <a name="next-steps"></a>További lépések

[A cognitive services Node.js SDK-minták](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples)