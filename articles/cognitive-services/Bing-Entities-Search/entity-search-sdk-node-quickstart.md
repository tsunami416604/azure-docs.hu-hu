---
title: 'Rövid útmutató: Bing Entity Search SDK, csomópont'
titleSuffix: Azure Cognitive Services
description: Az Entity Search SDK konzolalkalmazás csomóponttal beállítása.
services: cognitive-services
author: mikedodaro
manager: cgronlun
ms.service: cognitive-services
ms.component: bing-entity-search
ms.topic: quickstart
ms.date: 02/12/2018
ms.author: v-gedod
ms.openlocfilehash: 1f2a5f6a1473cde40928ada6e30f6bd9b780543d
ms.sourcegitcommit: 6f59cdc679924e7bfa53c25f820d33be242cea28
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/05/2018
ms.locfileid: "48814882"
---
# <a name="quickstart-bing-entity-search-sdk-with-node"></a>Rövid útmutató: Bing Entity Search SDK csomóponttal

A Bing Entity Search SDK a REST API funkcióit biztosítja az entitáslekérdezésekhez és az eredmények elemzéséhez. 

A [C# Bing Entity Search SDK-minták forráskódja](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples/blob/master/Samples/entitySearch.js) elérhető a GitHubon.
## <a name="application-dependencies"></a>Alkalmazásfüggőségek

Ha a Bing Entity Search SDK-val szeretne beállítani egy konzolalkalmazást, futtassa a fejlesztési környezetben a következőt: `npm install azure-cognitiveservices-entitysearch`.

## <a name="entity-search-client"></a>Entity Search-ügyfél
A [Cognitive Services hozzáférési kulcsát](https://azure.microsoft.com/try/cognitive-services/) a *Keresés* területen kérheti le. Hozza létre a `CognitiveServicesCredentials` egy példányát:
```
const CognitiveServicesCredentials = require('ms-rest-azure').CognitiveServicesCredentials;
let credentials = new CognitiveServicesCredentials('YOUR-ACCESS-KEY');
```
Ezután hozza létre az ügyfélpéldányt, és keresse meg az eredményeket:
```
const EntitySearchAPIClient = require('azure-cognitiveservices-entitysearch');

let entitySearchApiClient = new EntitySearchAPIClient(credentials);

entitySearchApiClient.entitiesOperations.search('seahawks').then((result) => {
    console.log(result.queryContext);
    console.log(result.entities.value);
    console.log(result.entities.value[0].description);
}).catch((err) => {
    throw err;
});

```
A kód `result.value` elemeket ír ki a konzolra a szöveg elemzése nélkül.  Az eredmények, ha vannak, kategóriánként a következőket tartalmazzák:
- _type: 'Thing'
- _type: 'ImageObject'

<!-- Removing until we can replace with a sanitized version.
![Entity results](media/entity-search-sdk-node-quickstart-results.png)
-->

## <a name="next-steps"></a>További lépések

[Cognitive Services Node.js SDK-minták](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples)