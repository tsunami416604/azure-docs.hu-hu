---
title: Entitás keresési SDK csomópont gyors üzembe helyezés |} Microsoft Docs
description: A telepítő entitás Search SDK konzolalkalmazást.
titleSuffix: Azure cognitive services
services: cognitive-services
author: mikedodaro
manager: rosh
ms.service: cognitive-services
ms.component: bing-entity-search
ms.topic: article
ms.date: 02/12/2018
ms.author: v-gedod
ms.openlocfilehash: 2904ecfed33334458f9b6a9ca2500cd0bfef13bc
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/01/2018
ms.locfileid: "35349775"
---
# <a name="entity-search-sdk-node-quickstart"></a>Entitás keresési SDK csomópont gyors üzembe helyezés

A Bing entitás keresési SDK tartalmazza a REST API entitás lekérdezések és elemzési eredmények funkcióit. 

A [forráskód C# Bing entitás keresési SDK minták](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples/blob/master/Samples/entitySearch.js) érhető el a Git központ.
## <a name="application-dependencies"></a>Alkalmazásfüggőségek

Egy konzolalkalmazást, a Bing entitás keresési SDK használatával beállításához futtassa `npm install azure-cognitiveservices-entitysearch` a fejlesztői környezetben.

## <a name="entity-search-client"></a>Entitás keresési ügyfél
Első egy [kognitív szolgáltatások hozzáférési kulcs](https://azure.microsoft.com/try/cognitive-services/) alatt *keresési*. Hozzon létre egy példányát a `CognitiveServicesCredentials`:
```
const CognitiveServicesCredentials = require('ms-rest-azure').CognitiveServicesCredentials;
let credentials = new CognitiveServicesCredentials('YOUR-ACCESS-KEY');
```
Ezt követően az ügyfél példányosítható, majd keresse meg eredmények:
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
A kód kinyomtatja `result.value` elemek elemzése szöveg nélkül a konzolon.  Az eredményeket, ha vannak ilyenek, kategóriánként tartalmazza:
- í_rja be: "Is"
- í_rja be: "ImageObject"

<!-- Removing until we can replace with a sanitized version.
![Entity results](media/entity-search-sdk-node-quickstart-results.png)
-->

## <a name="next-steps"></a>További lépések

[Kognitív szolgáltatások Node.js SDK-minták](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples)