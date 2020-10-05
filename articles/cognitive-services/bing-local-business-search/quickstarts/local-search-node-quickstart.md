---
title: Gyors útmutató – lekérdezés küldése az API-nak a Node.js-Bing helyi üzleti keresés használatával
titleSuffix: Azure Cognitive Services
description: Ezzel a rövid útmutatóval megkezdheti a kérések küldését a Bing local Business Search API számára, amely egy Azure kognitív szolgáltatás.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-local-business
ms.topic: quickstart
ms.date: 05/12/2020
ms.author: aahi
ms.custom: devx-track-js
ms.openlocfilehash: 8c59263768b548ad3d59830c93b397203f3ce45c
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/05/2020
ms.locfileid: "91277070"
---
# <a name="quickstart-send-a-query-to-the-bing-local-business-search-api-using-nodejs"></a>Gyors útmutató: lekérdezés küldése a Bing helyi üzleti keresési API-nak a Node.js használatával

Ebből a rövid útmutatóból megtudhatja, hogyan küldhet kéréseket a Bing helyi üzleti keresési API-nak, amely egy Azure kognitív szolgáltatás. Bár ez az egyszerű alkalmazás Node.jsban van megírva, az API egy REST-alapú webszolgáltatás, amely kompatibilis a HTTP-kérelmeket és a JSON-elemzést lehetővé tevő programozási nyelvekkel.

Ez a példában szereplő alkalmazás helyi válaszüzeneteket olvas be az API-ból egy keresési lekérdezéshez.

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés – [hozzon létre egyet ingyen](https://azure.microsoft.com/free/cognitive-services/)
* A [Node.js](https://nodejs.org/en/download/) legújabb verziója.
* A [JavaScript-kérelem könyvtára](https://github.com/request/request).
* Ha már rendelkezik Azure-előfizetéssel, <a href="https://portal.azure.com/#create/Microsoft.CognitiveServicesBingSearch-v7"  title=" hozzon létre egy Bing Search erőforrást, "  target="_blank"> és hozzon létre egy Bing Search-erőforrást <span class="docon docon-navigate-external x-hidden-focus"></span> </a> a Azure Portal a kulcs és a végpont beszerzéséhez. Az üzembe helyezést követően kattintson **az erőforrás keresése**elemre.


## <a name="code-scenario"></a>Kódforgatókönyv

A következő kód határozza meg és küldi el a kérelmet, amely a következő lépések végrehajtásával valósítható meg:

1. Deklarálja a változókat a végpont gazdagép és útvonal szerinti megadásához.
2. Adja meg a lekérdezést, és adja hozzá a lekérdezési paramétert.
3. Létrehoz egy függvényt a válasz kezeléséhez.
4. Adja meg a kérelmet létrehozó keresési függvényt, és adja meg a `Ocp-Apim-Subscription-Key` fejlécet.
5. Futtassa a Search függvényt.


```javascript
'use strict';

let https = require('https');

// Replace the subscriptionKey string value with your valid subscription key.
let subscriptionKey = 'your-access-key';

let host = 'api.cognitive.microsoft.com/bing';
let path = '/v7.0/localbusinesses/search';

let mkt = 'en-US';
let q = 'hotel in Bellevue';

let params = '?q=' + encodeURI(q) + "&mkt=" + mkt;

let response_handler = function (response) {
    let body = '';
    response.on('data', function (d) {
        body += d;
    });
    response.on('end', function () {
        let body_ = JSON.parse(body);
        let body__ = JSON.stringify(body_, null, '  ');
        console.log(body__);
    });
    response.on('error', function (e) {
        console.log('Error: ' + e.message);
    });
};

let Search = function () {
    let request_params = {
        method: 'GET',
        hostname: host,
        path: path + params,
        headers: {
            'Ocp-Apim-Subscription-Key': subscriptionKey,
        }
    };

    let req = https.request(request_params, response_handler);
    req.end();
}

Search();

```

## <a name="next-steps"></a>További lépések

* [Helyi üzleti keresés C# rövid útmutató](local-quickstart.md)
* [Helyi üzleti keresés – Java rövid útmutató](local-search-java-quickstart.md)
* [Helyi üzleti keresés – Python rövid útmutató](local-search-python-quickstart.md)
