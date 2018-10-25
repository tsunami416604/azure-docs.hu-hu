---
title: 'Rövid útmutató: Válaszkeresés projekt, Node'
description: A Válaszkeresés projekt használatának első lépései a Node-dal.
services: cognitive-services
author: mikedodaro
manager: cgronlun
ms.service: cognitive-services
ms.component: answer-search
ms.topic: quickstart
ms.date: 04/13/2018
ms.author: rosh
ms.openlocfilehash: 1afd029803fc7d2709a9a9abe840db6d7f52498d
ms.sourcegitcommit: 62759a225d8fe1872b60ab0441d1c7ac809f9102
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/19/2018
ms.locfileid: "49465746"
---
# <a name="quickstart-project-answer-search-with-node"></a>Rövid útmutató: A Válaszkeresés projekt használata a Node-dal

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

````
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

````

## <a name="next-steps"></a>További lépések
- [C#-mintakód](c-sharp-quickstart.md)
- [Java – rövid útmutató](java-quickstart.md)
- [Python – rövid útmutató](python-quickstart.md)