---
title: Webes keresés SDK csomópont gyors üzembe helyezés |} Microsoft Docs
description: A webes keresés telepítője SDK konzolalkalmazást.
titleSuffix: Azure cognitive services
services: cognitive-services
author: mikedodaro
manager: rosh
ms.service: cognitive-services
ms.component: bing-web-search
ms.topic: article
ms.date: 02/12/2018
ms.author: v-gedod
ms.openlocfilehash: 44f7f97f6c442df3fbb1e5e08189b8db7d4b9db0
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/23/2018
ms.locfileid: "35349483"
---
# <a name="web-search-sdk-node-quickstart"></a>Webes keresés SDK csomópont gyors üzembe helyezés

A Bing webes keresés SDK tartalmazza a REST API webkiszolgáló lekérdezések és elemzési eredmények funkcióit.

A [forráskód csomópont Bing webes keresés SDK minták](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples/blob/master/Samples/webSearch.js) érhető el a Git központ.

## <a name="application-dependencies"></a>Alkalmazásfüggőségek

Egy konzolalkalmazást, a Bing webes keresés SDK használatával beállításához futtassa `npm install azure-cognitiveservices-websearch` a fejlesztői környezetben.

## <a name="web-search-client"></a>Keresési webügyfél
Első egy [kognitív szolgáltatások hozzáférési kulcs](https://azure.microsoft.com/try/cognitive-services/) alatt *keresési*. Hozzon létre egy példányát a `CognitiveServicesCredentials`:
```
const CognitiveServicesCredentials = require('ms-rest-azure').CognitiveServicesCredentials;
let credentials = new CognitiveServicesCredentials('YOUR-ACCESS-KEY');
```
Az ügyfél ezután példányosítható:
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
A kód kinyomtatja `result.value` elemek elemzése szöveg nélkül a konzolon.  Az eredményeket, ha vannak ilyenek, kategóriánként tartalmazza:
- í_rja be: "ImageObject"
- í_rja be: "NewsArticle"
- í_rja be: "WebPage"
- í_rja be: "VideoObjectElementType"

<!-- Remove until this can be replaced with a sanitized version.
![Video results](media/web-search-sdk-node-results.png)
-->

## <a name="next-steps"></a>További lépések

[Kognitív szolgáltatások Node.js SDK-minták](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples)
