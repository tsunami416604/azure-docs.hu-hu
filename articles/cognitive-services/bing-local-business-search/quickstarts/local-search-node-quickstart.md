---
title: Quickstart - Send a query to the API using Node.js - Bing Local Business Search
titleSuffix: Azure Cognitive Services
description: Use this quickstart to begin sending requests to the Bing Local Business Search API, which is an Azure Cognitive Service.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-local-business
ms.topic: quickstart
ms.date: 09/13/2019
ms.author: aahi
ms.openlocfilehash: db9132936604e8eed17bbdf173ce1b766fb1b334
ms.sourcegitcommit: b77e97709663c0c9f84d95c1f0578fcfcb3b2a6c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/22/2019
ms.locfileid: "74326687"
---
# <a name="quickstart-send-a-query-to-the-bing-local-business-search-api-using-nodejs"></a>Quickstart: Send a query to the Bing Local Business Search API using Node.js

Use this quickstart to begin sending requests to the Bing Local Business Search API, which is an Azure Cognitive Service. While this simple application is written in Node.js, the API is a RESTful Web service compatible with any programming language capable of making HTTP requests and parsing JSON.

This example application gets local response data from the API for the search query `hotel in Bellevue`.

## <a name="prerequisites"></a>Előfeltételek

* A [Node.js](https://nodejs.org/en/download/) legújabb verziója.

* The [JavaScript Request Library](https://github.com/request/request)

You must have a [Cognitive Services API account](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) with Bing APIs. Az [ingyenes próbaverzió](https://azure.microsoft.com/try/cognitive-services/?api=bing-web-search-api) elegendő ehhez a rövid útmutatóhoz. Use the access key provided by the free trial.  See also [Cognitive Services Pricing - Bing Search API](https://azure.microsoft.com/pricing/details/cognitive-services/search-api/).

## <a name="code-scenario"></a>Kódforgatókönyv

The following code gets defines and sends the request. Implementálására a következő lépésekben kerül sor:

1. Deklarálja a változókat a végpont gazdagép és útvonal szerinti megadásához.
2. Specify the query, and add the query parameter.
3. Létrehoz egy függvényt a válasz kezeléséhez.
4. Define the Search function that creates the request and adds the Ocp-Apim-Subscription-Key header.
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

## <a name="next-steps"></a>Következő lépések

* [Local Business Search quickstart](local-quickstart.md)
* [Local Business Search Java quickstart](local-search-java-quickstart.md)
* [Local Business Search Python quickstart](local-search-python-quickstart.md)
