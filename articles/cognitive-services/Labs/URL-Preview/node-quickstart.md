---
title: 'Rövid útmutató: Az URL-cím előnézete projekt, Node.js'
titlesuffix: Azure Cognitive Services
description: Az URL-előnézet használatának első lépései a Microsoft Cognitive Servicesben az Azure-ban.
services: cognitive-services
author: mikedodaro
manager: cgronlun
ms.service: cognitive-services
ms.component: project-url-preview
ms.topic: quickstart
ms.date: 03/16/2018
ms.author: rosh
ms.openlocfilehash: b8410127ed9a0444ab400eb3242868a85bd5b2c9
ms.sourcegitcommit: 55952b90dc3935a8ea8baeaae9692dbb9bedb47f
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2018
ms.locfileid: "48883567"
---
# <a name="quickstart-url-preview-with-nodejs"></a>Rövid útmutató: Az URL-cím előnézete Node.js nyelven 

Az alábbi Node-példa létrehozza a SwiftKey webhely URL-előnézetét: https://swiftkey.com/en.

## <a name="prerequisites"></a>Előfeltételek

Kérjen hozzáférési kulcsot a [Cognitive Services Labs](https://aka.ms/answersearchsubscription) ingyenes próbaverziójához

## <a name="code-scenario"></a>Kódforgatókönyv 

Az alábbi kód egy URL-cím előnézeti adatait kéri le.
Implementálására a következő lépésekben kerül sor:
1. Deklarálni kell a változókat a végpont megadásához gazdagép és elérési út szerint.
2. Meg kell adni az előnézetben megtekintendő lekérdezési URL-címet, és meg kell adni hozzá egy lekérdezési paramétert.  
3. Létre kell hozni egy, a választ kezelő függvényt.
4. Definiálni kell a kérést létrehozó és az *Ocp-Apim-Subscription-Key* fejlécet hozzáadó Search függvényt.
5. Futtatni kell a Search függvényt. 

Az útmutatóban használt teljes kód a következő:

````
'use strict';

let https = require('https');

// Replace the subscriptionKey string value with your valid subscription key.
let subscriptionKey = 'YOUR-ACCESS-KEY'; 
let host = 'api.labs.cognitive.microsoft.com';
let path = '/urlpreview/v7.0/search';

let mkt = 'en-US';
let q = 'https://swiftkey.com/';

let params = '?q=' + encodeURI(q);

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
- [C#-mintakód](csharp.md)
- [Java – rövid útmutató](java-quickstart.md)
- [JavaScript – rövid útmutató](javascript.md)
- [Python – rövid útmutató](python-quickstart.md)