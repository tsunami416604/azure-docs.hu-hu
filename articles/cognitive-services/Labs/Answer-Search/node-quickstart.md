---
title: 'Gyors útmutató: Projekt válaszkeresés, csomópont'
description: A Válaszkeresés projekt használatának első lépései a Node-dal.
services: cognitive-services
author: mikedodaro
manager: cgronlun
ms.service: cognitive-services
ms.component: answer-search
ms.topic: quickstart
ms.date: 04/13/2018
ms.author: rosh
ms.openlocfilehash: 3fd10bd08aa86458173dd1d88e2767f6f9ca4191
ms.sourcegitcommit: eecd816953c55df1671ffcf716cf975ba1b12e6b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/28/2019
ms.locfileid: "55104356"
---
# <a name="quickstart-project-answer-search-with-node"></a>Gyors útmutató: Válaszkeresés projekt csomópont

Az alábbi Node-példa létrehoz egy lekérdezést a Yosemite Nemzeti Parkkal kapcsolatos információk vonatkozásában.

## <a name="prerequisites"></a>Előfeltételek

Hozzáférési kulcs lekérése a [Cognitive Services Labs](https://aka.ms/answersearchsubscription) ingyenes próbaverziójához

Ez a példa a Node 8.9.4-es verzióját használja.

## <a name="code-scenario"></a>Kódforgatókönyv 

Az alábbi kód válaszokat kér le.
Implementálására a következő lépésekben kerül sor:
1. Deklarálja a változókat a végpont gazdagép és útvonal szerinti megadásához.
2. Megadja a lekérdezési URL-címet az előnézethez, és hozzáad egy lekérdezési paramétert.  
3. Létrehoz egy függvényt a válasz kezeléséhez.
4. Definiálja a kérést létrehozó és az *Ocp-Apim-Subscription-Key* fejlécet hozzáadó Search függvényt.
5. Futtatja a Search függvényt. 

Az útmutatóban használt teljes kód a következő:

```
'use strict';

let https = require('https');

// Replace the subscriptionKey string value with your valid subscription key.
let subscriptionKey = 'YOUR-SUBSCRIPTION-KEY'; 

let host = 'api.labs.cognitive.microsoft.com';
let path = '/answerSearch/v7.0/search';

let mkt = 'en-us';
let q = 'Yosemite National Park';

let params = '?q=' + encodeURI(q) + '&mkt=en-us';

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
- [C#-mintakód](c-sharp-quickstart.md)
- [Java – rövid útmutató](java-quickstart.md)
- [Python – rövid útmutató](python-quickstart.md)