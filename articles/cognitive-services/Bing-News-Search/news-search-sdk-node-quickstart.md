---
title: 'Rövid útmutató: Bing News Search SDK, Node'
titleSuffix: Azure Cognitive Services
description: Bing News Search SDK-konzolalkalmazás beállítása
services: cognitive-services
author: mikedodaro
manager: cgronlun
ms.service: cognitive-services
ms.component: bing-news-search
ms.topic: quickstart
ms.date: 02/12/2018
ms.author: v-gedod
ms.openlocfilehash: bc168cf696d6280ce4c0e7cb46f90af4a2ad7aa0
ms.sourcegitcommit: db2cb1c4add355074c384f403c8d9fcd03d12b0c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/15/2018
ms.locfileid: "51686490"
---
# <a name="quickstart-bing-news-search-sdk-with-node"></a>Rövid útmutató: A Bing News Search SDK és a Node használata

A Bing News Search SDK a REST API funkcióit biztosítja a hírek lekérdezéséhez és az eredmények elemzéséhez. 

A [Node Bing News Search SDK-minták forráskódja](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples/blob/master/Samples/newsSearch.js) elérhető a GitHubon.

## <a name="application-dependencies"></a>Alkalmazásfüggőségek

Beállítása egy konzolalkalmazást, a Bing News Search SDK használatával:
* Futtatás `npm install ms-rest-azure` a fejlesztési környezetben.
* Futtatás `npm install azure-cognitiveservices-newssearch` a fejlesztési környezetben.

## <a name="news-search-client"></a>News Search-ügyfél
A [Cognitive Services hozzáférési kulcsát](https://azure.microsoft.com/try/cognitive-services/) a *Keresés* területen kérheti le. Hozza létre a `CognitiveServicesCredentials` egy példányát:
```
const CognitiveServicesCredentials = require('ms-rest-azure').CognitiveServicesCredentials;
let credentials = new CognitiveServicesCredentials('YOUR-ACCESS-KEY');
```
Ezután példányosítsa az ügyfelet:
```
const NewsSearchAPIClient = require('azure-cognitiveservices-newssearch');
let client = new NewsSearchAPIClient(credentials);
```
Az ügyféllel keressen rá a lekérdezési szövegre, ami ebben az esetben a „Winter Olympics” (téli olimpia):
```
client.newsOperations.search('Winter Olympics').then((result) => {
    console.log(result.value);
}).catch((err) => {
    throw err;
});

```
A kód `result.value`-elemeket jelenít meg a konzolon bármilyen szövegelemzés nélkül. Az eredmények – ha egyáltalán van találat egy kategóriában – a következőket fogják tartalmazni:
- _type: 'NewsArticle'
- _type: 'WebPage'
- _type: 'VideoObject'
- _type: 'ImageObject'

<!-- Remove until we can replace with santized version
![News results](media/node-sdk-quickstart-results.png)
-->

## <a name="next-steps"></a>További lépések

[Cognitive Services Node.js SDK-minták](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples)
