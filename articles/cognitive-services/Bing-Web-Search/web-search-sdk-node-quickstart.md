---
title: 'Gyors útmutató: A Bing Web Search SDK használata a node.js-ben'
description: A telepítő a webes keresés SDK konzolalkalmazást.
titleSuffix: Azure cognitive services
services: cognitive-services
author: mikedodaro
manager: rosh
ms.service: cognitive-services
ms.component: bing-web-search
ms.topic: article
ms.date: 08/16/2018
ms.author: v-gedod, erhopf
ms.openlocfilehash: e25c295fc0fc144110325d3c494a513ea35aeb05
ms.sourcegitcommit: f1e6e61807634bce56a64c00447bf819438db1b8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/24/2018
ms.locfileid: "42888590"
---
# <a name="quickstart-use-the-bing-web-search-sdk-for-nodejs"></a>Gyors útmutató: A Bing Web Search SDK használata a node.js-ben

A Bing Web Search SDK tartalmazza a REST API, webes lekérdezések és az elemzési eredmények funkcióit.

A [forráskódját csomópont a Bing Web Search SDK-minták](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples/blob/master/Samples/webSearch.js) elérhető a Githubon.

## <a name="application-dependencies"></a>Alkalmazásfüggőségek

Állítsa be a Bing Web Search SDK használatával egy konzolalkalmazást, futtassa `npm install azure-cognitiveservices-websearch` a fejlesztési környezetben.

## <a name="web-search-client"></a>Webes keresés ügyfél
Get- [Cognitive Services előfizetési kulcs](https://azure.microsoft.com/try/cognitive-services/) alatt *keresési*. Hozzon létre egy példányt a `CognitiveServicesCredentials`:
```
const CognitiveServicesCredentials = require('ms-rest-azure').CognitiveServicesCredentials;
let credentials = new CognitiveServicesCredentials('YOUR-ACCESS-KEY');
```
Ezután hozza létre az ügyfél:
```
const WebSearchAPIClient = require('azure-cognitiveservices-websearch');
let webSearchApiClient = new WebSearchAPIClient(credentials);
```
Keresési eredmények:
```
webSearchApiClient.web.search('seahawks').then((result) => {
    let properties = ["images", "webPages", "news", "videos"];
    for (let i = 0; i < properties.length; i++) {
        if (result[properties[i]]) {
            console.log(result[properties[i]].value);
        } else {
            console.log(`No ${properties[i]} data`);
        }
    }
}).catch((err) => {
    throw err;
})

```
A kód nyomtatása `result.value` elemek nélkül, szöveg elemzése a konzolhoz.  Az eredményeket, ha van ilyen, kategóriánként a következőket tartalmazzák:
- í_rja be: "ImageObject"
- í_rja be: "NewsArticle"
- í_rja be: "WebPage"
- í_rja be: "VideoObjectElementType"

<!-- Remove until this can be replaced with a sanitized version.
![Video results](media/web-search-sdk-node-results.png)
-->

## <a name="next-steps"></a>További lépések

[A cognitive services Node.js SDK-minták](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples)
