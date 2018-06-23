---
title: Kép keresési SDK csomópont gyors üzembe helyezés |} Microsoft Docs
description: A kép keresési telepítője SDK konzolalkalmazást.
titleSuffix: Azure cognitive services
services: cognitive-services
author: mikedodaro
manager: rosh
ms.service: cognitive-services
ms.component: bing-image-search
ms.topic: article
ms.date: 02/12/2018
ms.author: v-gedod
ms.openlocfilehash: e4c8303e39accbb7caec15c0ef47d701971ce632
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/23/2018
ms.locfileid: "35349454"
---
# <a name="image-search-sdk-node-quickstart"></a>Kép keresési SDK csomópont gyors üzembe helyezés

A Bing kép keresési SDK a REST API kép lekérdezések és elemzési eredmények funkcióit tartalmazza. 

A [forráskód csomópont Bing kép keresési SDK minták](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples/blob/master/Samples/imageSearch.js) érhető el a Git központ.

## <a name="application-dependencies"></a>Alkalmazásfüggőségek

Egy konzolalkalmazást, a Bing kép keresési SDK használatával beállításához futtassa `npm install azure-cognitiveservices-imagesearch` a fejlesztői környezetben.

## <a name="image-search-client"></a>Kép keresési ügyfél
Első egy [kognitív szolgáltatások hozzáférési kulcs](https://azure.microsoft.com/try/cognitive-services/) alatt *keresési*. Hozzon létre egy példányát a `CognitiveServicesCredentials`:
```
const CognitiveServicesCredentials = require('ms-rest-azure').CognitiveServicesCredentials;
let credentials = new CognitiveServicesCredentials('YOUR-ACCESS-KEY');
```
Az ügyfél ezután példányosítható:
```
const ImageSearchAPIClient = require('azure-cognitiveservices-imagesearch');
let client = new ImageSearchAPIClient(credentials);
```
A lekérdezésszöveg ebben az esetben "El Capitan" kereséséhez az ügyfél használni:
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

[Kognitív szolgáltatások Node.js SDK-minták](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples)