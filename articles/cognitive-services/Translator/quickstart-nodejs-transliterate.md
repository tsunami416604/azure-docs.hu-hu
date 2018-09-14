---
title: 'Translator Text: Szövegátalakítási szkript a Node.js használatával | Microsoft Docs'
titleSuffix: Microsoft Cognitive Services
description: Ebben a rövid útmutatóban szöveget alakít át egy nyelven egy szkriptből egy másikba a Translator Text API segítségével és a Node.js használatával a Cognitive Servicesben.
services: cognitive-services
author: noellelacharite
manager: nolachar
ms.service: cognitive-services
ms.component: translator-text
ms.topic: quickstart
ms.date: 06/21/2018
ms.author: nolachar
ms.openlocfilehash: b16af911e5822deaa7cc7bcfe792245ae154eb26
ms.sourcegitcommit: 65b399eb756acde21e4da85862d92d98bf9eba86
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/22/2018
ms.locfileid: "43769594"
---
# <a name="quickstart-transliterate-text-with-nodejs"></a>Rövid útmutató: Szöveg átírása a Node.js használatával

Ebben a rövid útmutatóban szöveget alakít át egy nyelven egy szkriptből egy másikba a Translator Text API segítségével.

## <a name="prerequisites"></a>Előfeltételek

A kód futtatásához [Node.js 6](https://nodejs.org/en/download/) szükséges.

A Translator Text API használatához szüksége van egy előfizetési kulcsra is. Lásd [a Translator Text API regisztrációját](translator-text-how-to-signup.md).

## <a name="transliterate-request"></a>Transliterate kérés

A következő egy szöveget alakít át egy nyelven egy szkriptből egy másik szkriptbe a [Transliterate](./reference/v3-0-transliterate.md) metódussal.

1. Hozzon létre egy új Node.js-projektet a kedvenc kódszerkesztőjében.
2. Adja hozzá az alábbi kódot.
3. A `subscriptionKey` értéket cserélje le az előfizetéshez érvényes hozzáférési kulcsra.
4. Futtassa a programot.

```javascript
'use strict';

let fs = require ('fs');
let https = require ('https');

// **********************************************
// *** Update or verify the following values. ***
// **********************************************

// Replace the subscriptionKey string value with your valid subscription key.
let subscriptionKey = 'ENTER KEY HERE';

let host = 'api.cognitive.microsofttranslator.com';
let path = '/transliterate?api-version=3.0';

// Transliterate text in Japanese from Japanese script (i.e. Hiragana/Katakana/Kanji) to Latin script.
let params = '&language=ja&fromScript=jpan&toScript=latn';

// Transliterate "good afternoon".
let text = 'こんにちは';

let response_handler = function (response) {
    let body = '';
    response.on ('data', function (d) {
        body += d;
    });
    response.on ('end', function () {
        let json = JSON.stringify(JSON.parse(body), null, 4);
        console.log(json);
    });
    response.on ('error', function (e) {
        console.log ('Error: ' + e.message);
    });
};

let get_guid = function () {
  return 'xxxxxxxx-xxxx-4xxx-yxxx-xxxxxxxxxxxx'.replace(/[xy]/g, function(c) {
    var r = Math.random() * 16 | 0, v = c == 'x' ? r : (r & 0x3 | 0x8);
    return v.toString(16);
  });
}

let Transliterate = function (content) {
    let request_params = {
        method : 'POST',
        hostname : host,
        path : path + params,
        headers : {
            'Content-Type' : 'application/json',
            'Ocp-Apim-Subscription-Key' : subscriptionKey,
            'X-ClientTraceId' : get_guid (),
        }
    };

    let req = https.request (request_params, response_handler);
    req.write (content);
    req.end ();
}

let content = JSON.stringify ([{'Text' : text}]);

Transliterate (content);
```

## <a name="transliterate-response"></a>Transliterate válasz

A rendszer JSON formátumban ad vissza egy sikeres választ a következő példában látható módon:

```json
[
  {
    "text": "konnnichiha",
    "script": "latn"
  }
]
```

## <a name="next-steps"></a>További lépések

A GitHubon megismerheti a rövid útmutató és egyebek mintakódját, beleértve a fordítást és a nyelvfelismerést is, valamint más Translator Text-projekteket.

> [!div class="nextstepaction"]
> [A Node.js-példák megismerése a GitHubon](https://aka.ms/TranslatorGitHub?type=&language=javascript)
