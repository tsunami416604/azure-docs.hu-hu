---
title: Gyorsútmutató – Lekérdezés küldése az API-nak a Node.js használatával – Bing helyi üzleti keresés
titleSuffix: Azure Cognitive Services
description: Ezzel a rövid útmutatóval megkezdheti a kérelmek küldését a Bing Helyi üzleti keresési API-nak, amely egy Azure Cognitive Service.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-local-business
ms.topic: quickstart
ms.date: 11/29/2019
ms.author: aahi
ms.openlocfilehash: beab594126ce292ea1fc47e399a12274dbb31aa3
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74665695"
---
# <a name="quickstart-send-a-query-to-the-bing-local-business-search-api-using-nodejs"></a>Rövid útmutató: Lekérdezés küldése a Bing helyi üzleti keresési API-nak a Node.js használatával

Ezzel a rövid útmutatóval megkezdheti a kérelmek küldését a Bing Helyi üzleti keresési API-nak, amely egy Azure Cognitive Service. Bár ez az egyszerű alkalmazás a Node.js fájlban íródott, az API egy RESTful webszolgáltatás, amely kompatibilis bármely programozási nyelvvel, amely képes HTTP-kérelmeket készíteni és elemezni a JSON-t.

Ez a példaalkalmazás helyi válaszadatokat kér `hotel in Bellevue`le az API-ból a keresési lekérdezéshez.

## <a name="prerequisites"></a>Előfeltételek

* A [Node.js](https://nodejs.org/en/download/) legújabb verziója.

* A [JavaScript lekérési kódtára](https://github.com/request/request)

A Bing [API-kkal rendelkező Cognitive Services API-fiókkal](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) kell rendelkeznie. Az [ingyenes próbaverzió](https://azure.microsoft.com/try/cognitive-services/?api=bing-web-search-api) elegendő ehhez a rövid útmutatóhoz. Használja az ingyenes próbaverzió által biztosított hozzáférési kulcsot.  Lásd még: [Cognitive Services Pricing - Bing Search API](https://azure.microsoft.com/pricing/details/cognitive-services/search-api/).

## <a name="code-scenario"></a>Kódforgatókönyv

A következő kód határozza meg, és elküldi a kérelmet. Implementálására a következő lépésekben kerül sor:

1. Deklarálja a változókat a végpont gazdagép és útvonal szerinti megadásához.
2. Adja meg a lekérdezést, és adja hozzá a lekérdezési paramétert.
3. Létrehoz egy függvényt a válasz kezeléséhez.
4. Definiálja a kérést létrehozó és az Ocp-Apim-Subscription-Key fejlécet hozzáadó Search függvényt.
5. Futtatja a Search függvényt.

Az útmutatóban használt teljes kód a következő:

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

* [Helyi üzleti keresés rövid útmutató](local-quickstart.md)
* [Helyi üzleti keresés Java – rövid útmutató](local-search-java-quickstart.md)
* [Helyi üzleti keresés Python rövid útmutató](local-search-python-quickstart.md)
