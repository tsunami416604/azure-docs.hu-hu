---
title: Hírek keresési SDK csomópont gyors üzembe helyezés |} Microsoft Docs
description: Állítsa be a hírek keresési SDK Konzolalkalmazás
titleSuffix: Azure cognitive services
services: cognitive-services
author: mikedodaro
manager: rosh
ms.service: cognitive-services
ms.component: bing-news-search
ms.topic: article
ms.date: 02/12/2018
ms.author: v-gedod
ms.openlocfilehash: 4ae99aa100b697a0dd75863c6f0c3c556dfa3d21
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/23/2018
ms.locfileid: "35349474"
---
# <a name="news-search-sdk-node-quickstart"></a>Hírek keresési SDK csomópont gyors üzembe helyezés

A Bing hírek keresési SDK tartalmazza a REST API hírek lekérdezések és elemzési eredmények funkcióit. 

A [forráskód csomópont Bing hírek keresési SDK minták](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples/blob/master/Samples/newsSearch.js) érhető el a Git központ.

## <a name="application-dependencies"></a>Alkalmazásfüggőségek

Egy konzolalkalmazást, a Bing hírek keresési SDK használatával beállításához futtassa `npm install azure-cognitiveservices-newssearch` a fejlesztői környezetben.

## <a name="news-search-client"></a>Hírek keresési ügyfél
Első egy [kognitív szolgáltatások hozzáférési kulcs](https://azure.microsoft.com/try/cognitive-services/) alatt *keresési*. Hozzon létre egy példányát a `CognitiveServicesCredentials`:
```
const CognitiveServicesCredentials = require('ms-rest-azure').CognitiveServicesCredentials;
let credentials = new CognitiveServicesCredentials('YOUR-ACCESS-KEY');
```
Az ügyfél ezután példányosítható:
```
const NewsSearchAPIClient = require('azure-cognitiveservices-newssearch');
let client = new NewsSearchAPIClient(credentials);
```
A lekérdezésszöveg ebben az esetben "Téli olimpia" kereséséhez az ügyfél használni:
```
client.newsOperations.search('Winter Olympics').then((result) => {
    console.log(result.value);
}).catch((err) => {
    throw err;
});

```
A kód kinyomtatja `result.value` elemek elemzése szöveg nélkül a konzolon. Az eredményeket, ha vannak ilyenek, kategóriánként tartalmazza:
- í_rja be: "NewsArticle"
- í_rja be: "WebPage"
- í_rja be: "VideoObject"
- í_rja be: "ImageObject"

<!-- Remove until we can replace with santized version
![News results](media/node-sdk-quickstart-results.png)
-->

## <a name="next-steps"></a>További lépések

[Kognitív szolgáltatások Node.js SDK-minták](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples)
