---
title: A Microsoft kognitív szolgáltatásokban projekt válasz keresése csomópont gyors üzembe helyezés |} Microsoft Docs
description: Projekt válasz keresése, Microsoft Azure kognitív Services az első lépéseiben.
services: cognitive-services
author: mikedodaro
ms.service: cognitive-services
ms.technology: project-answer-search
ms.topic: article
ms.date: 04/13/2018
ms.author: rosh, v-gedod
ms.openlocfilehash: 36b2709d39230aae7929164ba4c9306f57043b43
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/23/2018
ms.locfileid: "35348599"
---
# <a name="project-answer-search-node-quickstart"></a>Projekt válasz keresési csomópont gyors üzembe helyezés

A következő csomópont példa létrehoz egy lekérdezést Yosemite National Park kapcsolatos információkat.

## <a name="prerequisites"></a>Előfeltételek

Az ingyenes próbaidőszakra hozzáférési kulcs beszerzése [kognitív szolgáltatások Labs](https://aka.ms/answersearchsubscription)

Ez a példa csomópont v8.9.4

## <a name="code-scenario"></a>Kód forgatókönyv 

A következő kód jogosultságot kap a válaszokat.
Az alábbi lépéseket a megvalósított:
1. Deklarálja a változókat, adja meg a végpont által állomás és az elérési utat.
2. Adja meg a lekérdezés URL-címe az előzetes, és adja hozzá a lekérdezési paramétert.  
3. A válasz kezelő függvény létrehozása.
4. Adja meg a keresési funkció, amely hozza létre a kérelmet, és hozzáadja a *Ocp-Apim-előfizetés-kulcs* fejléc.
5. Futtassa a keresési funkciót. 

A teljes kód látható ebben a bemutatóban a következőképpen:

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
- [C#-példakód](c-sharp-quickstart.md)
- [Java gyors üzembe helyezés](java-quickstart.md)
- [Python gyors üzembe helyezés](python-quickstart.md)