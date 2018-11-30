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
ms.openlocfilehash: f94e3b5a6070da5ef9510216abd3f52a958030c5
ms.sourcegitcommit: a08d1236f737915817815da299984461cc2ab07e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/26/2018
ms.locfileid: "52311381"
---
# <a name="quickstart-bing-entity-search-sdk-with-node"></a>Rövid útmutató: Bing Entity Search SDK csomóponttal

A Bing Entity Search SDK a REST API funkcióit biztosítja az entitáslekérdezésekhez és az eredmények elemzéséhez. 

A [C# Bing Entity Search SDK-minták forráskódja](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples/blob/master/Samples/entitySearch.js) elérhető a GitHubon.
## <a name="application-dependencies"></a>Alkalmazásfüggőségek
A [Cognitive Services hozzáférési kulcsát](https://azure.microsoft.com/try/cognitive-services/) a **Keresés** területen kérheti le.  Lásd még: [a Cognitive Services díjszabás – keresési Bing-API](https://azure.microsoft.com/pricing/details/cognitive-services/search-api/).

A Bing Entity Search SDK használatával egy konzolalkalmazást beállítása:
* Futtatás `npm install ms-rest-azure` a fejlesztési környezetben.
* Futtatás `npm install azure-cognitiveservices-entitysearch` a fejlesztési környezetben.

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
