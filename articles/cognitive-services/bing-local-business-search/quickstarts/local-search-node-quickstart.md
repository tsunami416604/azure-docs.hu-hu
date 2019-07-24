---
title: Rövid útmutató – lekérdezés küldése a Bing helyi üzleti keresési API-nak a Node. js használatával
titleSuffix: Azure Cognitive Services
description: Kezdje el használni a Bing local Business Search API-t a csomóponton.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: quickstart
ms.date: 11/01/2018
ms.author: rosh
ms.openlocfilehash: 0c91a66539bc276fdf2e6e4056f3b67f770398b8
ms.sourcegitcommit: 198c3a585dd2d6f6809a1a25b9a732c0ad4a704f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/23/2019
ms.locfileid: "68423336"
---
# <a name="quickstart-send-a-query-to-the-bing-local-business-search-api-using-nodejs"></a>Gyors útmutató: Lekérdezés küldése a Bing helyi üzleti keresési API-nak a Node. js használatával

Ezzel a rövid útmutatóval megkezdheti a kérések küldését a Bing local Business Search API számára, amely egy Azure kognitív szolgáltatás. Habár ez az egyszerű alkalmazás a Node. js-ben íródott, az API egy REST-alapú webszolgáltatás, amely kompatibilis a HTTP-kérelmeket és a JSON-elemzést lehetővé tevő programozási nyelvekkel.

Ez a példában szereplő alkalmazás a keresési lekérdezés `hotel in Bellevue`API-ból érkező helyi válaszüzeneteket kéri le.

## <a name="prerequisites"></a>Előfeltételek

* A [Node.js](https://nodejs.org/en/download/) legújabb verziója.

* A [JavaScript-kérelem könyvtára](https://github.com/request/request)

A Bing API-kkal [Cognitive Services API](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) -fiókkal kell rendelkeznie. Az [ingyenes próbaverzió](https://azure.microsoft.com/try/cognitive-services/?api=bing-web-search-api) elegendő ehhez a rövid útmutatóhoz. Használja az ingyenes próbaverzió által biztosított hozzáférési kulcsot.  Lásd még: [a Cognitive Services díjszabás – keresési Bing-API](https://azure.microsoft.com/pricing/details/cognitive-services/search-api/).

## <a name="code-scenario"></a>Kódforgatókönyv

A következő kód meghatározza és elküldi a kérést. Implementálására a következő lépésekben kerül sor:

1. Deklarálja a változókat a végpont gazdagép és útvonal szerinti megadásához.
2. Adja meg a lekérdezést, és adja hozzá a lekérdezési paramétert.
3. Létrehoz egy függvényt a válasz kezeléséhez.
4. Adja meg a kérelmet létrehozó keresési függvényt, és adja meg a OCP-APIM-Subscription-Key fejlécet.
5. Futtatja a Search függvényt.

Az útmutatóban használt teljes kód a következő:

```
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

* [Helyi üzleti keresés – rövid útmutató](local-quickstart.md)
* [Helyi üzleti keresés – Java rövid útmutató](local-search-java-quickstart.md)
* [Helyi üzleti keresés – Python rövid útmutató](local-search-python-quickstart.md)
