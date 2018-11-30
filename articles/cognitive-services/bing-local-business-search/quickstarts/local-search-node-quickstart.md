---
title: Rövid útmutató – a helyi vállalati keresési Bing-API a Node.js használata lekérdezés küldése |} A Microsoft Docs
titleSuffix: Azure Cognitive Services
description: A csomópont a helyi vállalati keresési Bing-API használatának megkezdéséhez.
services: cognitive-services
author: mikedodaro
manager: rosh
ms.service: cognitive-services
ms.component: bing-local-business
ms.topic: article
ms.date: 11/01/2018
ms.author: rosh, v-gedod
ms.openlocfilehash: d60f2aca912f279f84710863e5f6ce507de64cb0
ms.sourcegitcommit: a08d1236f737915817815da299984461cc2ab07e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/26/2018
ms.locfileid: "52309174"
---
# <a name="quickstart-send-a-query-to-the-bing-local-business-search-api-using-nodejs"></a>Gyors útmutató: Egy lekérdezést küld a helyi vállalati keresési Bing-API a Node.js használata

Ez a rövid útmutató segítségével megkezdheti a kérések küldését a Bing helyi üzleti Search API, azaz az Azure Cognitive Services-szolgáltatás. Bár ez az egyszerű alkalmazás Node.js-ben írt, az API-t olyan kompatibilis HTTP-kérelem indítására és JSON-elemzés minden programozási nyelvet a webes RESTful szolgáltatás.
 
Ez a példa az alkalmazás helyi érkezett válasz adatait lekérdezi az API-val, a keresési lekérdezés `hotel in Bellevue`.

## <a name="prerequisites"></a>Előfeltételek

* A [Node.js](https://nodejs.org/en/download/) legújabb verziója.

* A [kérelem JavaScript kódtár](https://github.com/request/request)

Rendelkeznie kell egy [Cognitive Services API-fiók](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) Bing API-kkal. Az [ingyenes próbaverzió](https://azure.microsoft.com/try/cognitive-services/?api=bing-web-search-api) elegendő ehhez a rövid útmutatóhoz. Az ingyenes próbaverzióra által biztosított hozzáférési kulcs használja.  Lásd még: [a Cognitive Services díjszabás – keresési Bing-API](https://azure.microsoft.com/pricing/details/cognitive-services/search-api/).

##<a name="code-scenario"></a>Kódforgatókönyv
A következő kód lekéri határozza meg, és elküldi a kérelmet. Implementálására a következő lépésekben kerül sor:

1. Deklarálja a változókat a végpont gazdagép és útvonal szerinti megadásához.
2. Adja meg a lekérdezést, és adja hozzá a lekérdezési paraméter. 
3. Létrehoz egy függvényt a válasz kezeléséhez.
4. Adja meg a keresési funkció, amely a kérést hoz létre, és hozzáadja az Ocp-Apim-Subscription-Key fejléc.
5. Futtatja a Search függvényt. 

Az útmutatóban használt teljes kód a következő:

````
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

````

## <a name="next-steps"></a>További lépések
- [Helyi üzleti keresés a rövid útmutató](local-quickstart.md)
- [Helyi üzleti keresési Java a rövid útmutató](local-search-java-quickstart.md)
- [Helyi üzleti keresési Python a rövid útmutató](local-search-python-quickstart.md)
