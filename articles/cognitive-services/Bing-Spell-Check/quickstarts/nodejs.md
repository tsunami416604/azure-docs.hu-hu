---
title: 'Rövid útmutató: Helyesírás-ellenőrzés a REST API-val és a Node.js-szel – Bing helyesírás-ellenőrzés'
titleSuffix: Azure Cognitive Services
description: Ezzel a rövid útmutatóval első lépések a Bing Helyesírás-ellenőrző REST API-val ellenőrizheti a helyesírást és a nyelvhelyességet.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-spell-check
ms.topic: quickstart
ms.date: 12/16/2019
ms.author: aahi
ms.openlocfilehash: 69c391e6c3f93a998ade7c5721a528d895f8df76
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75382854"
---
# <a name="quickstart-check-spelling-with-the-bing-spell-check-rest-api-and-nodejs"></a>Rövid útmutató: Helyesírás-ellenőrzés a Bing helyesírás-ellenőrző REST API-val és a Node.js

Ezzel a rövid útmutatóval elsőként hívhatja meg a Bing Helyesírás-ellenőrző REST API-t. Ez az egyszerű csomópontalkalmazás kérelmet küld az API-nak, és visszaadja a nem felismert szavak listáját, majd a javasolt javításokat. Bár ez az alkalmazás node.js nyelven íródott, az API egy RESTful webszolgáltatás, amely kompatibilis a legtöbb programozási nyelvvel. Az alkalmazás forráskódja elérhető a [GitHubon.](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/nodejs/Search/BingSpellCheckv7.js)

## <a name="prerequisites"></a>Előfeltételek

* [Node.js 6](https://nodejs.org/en/download/) vagy újabb.

[!INCLUDE [cognitive-services-bing-spell-check-signup-requirements](../../../../includes/cognitive-services-bing-spell-check-signup-requirements.md)]


## <a name="create-and-initialize-a-project"></a>Projekt létrehozása és inicializálása

1. Hozzon létre egy új JavaScript fájlt a kedvenc IDE vagy szerkesztő. Állítsa be a szigort, és igényel . `https` Ezután hozzon létre változókat az API-végpont gazdagépéhez, elérési útjának és előfizetési kulcsához. Használhatja az alábbi globális végpontot, vagy az [egyéni altartomány-végpontot,](../../../cognitive-services/cognitive-services-custom-subdomains.md) amely az azure-portálon jelenik meg az erőforráshoz.

    ```javascript
    'use strict';
    let https = require ('https');

    let host = 'api.cognitive.microsoft.com';
    let path = '/bing/v7.0/spellcheck';
    let key = '<ENTER-KEY-HERE>';
    ```

2. Hozzon létre változókat a keresési paraméterekhez és az ellenőrizni kívánt szöveghez. Fűzze hozzá a `mkt=`piaci kódot után . A piaci kód az az ország, ahonnan a kérelmet benyújtod. A helyesírás-ellenőrzési módat is `&mode=`csatolja a után. A mód `proof` vagy (a legtöbb helyesírási/nyelvtani hibát elkap) vagy `spell` (a legtöbb helyesírást, de nem annyi nyelvtani hibát) kapja meg).

    ```javascript
    let mkt = "en-US";
    let mode = "proof";
    let text = "Hollo, wrld!";
    let query_string = "?mkt=" + mkt + "&mode=" + mode;
    ```

## <a name="create-the-request-parameters"></a>A kérelemparamétereinek létrehozása

Hozzon létre a kérelem paramétereit `POST` egy metódussal létrehozott új objektummal. Adja hozzá az elérési utat a végpont elérési útjának és a lekérdezési karakterlánc hozzáfűzésével. Adja hozzá az `Ocp-Apim-Subscription-Key` előfizetési kulcsot a fejléchez.

```javascript
let request_params = {
   method : 'POST',
   hostname : host,
   path : path + query_string,
   headers : {
   'Content-Type' : 'application/x-www-form-urlencoded',
   'Content-Length' : text.length + 5,
      'Ocp-Apim-Subscription-Key' : key,
   }
};
```

## <a name="create-a-response-handler"></a>Válaszkezelő létrehozása

Hozzon létre `response_handler` egy függvényt, amelynek célja, hogy a JSON-választ az API-ból, és nyomtassa ki. Hozzon létre egy változót a választörzsszámára. Fűzze hozzá `data` a választ, `response.on()`ha jelző t kap a használatával. Ha `end` zászló érkezik, nyomtassa ki a JSON-törzset a konzolra.

```javascript
let response_handler = function (response) {
    let body = '';
    response.on ('data', function (d) {
        body += d;
    });
    response.on ('end', function () {
        let body_ = JSON.parse (body);
        console.log (body_);
    });
    response.on ('error', function (e) {
        console.log ('Error: ' + e.message);
    });
};
```

## <a name="send-the-request"></a>A kérelem elküldése

Hívja meg `https.request()` az API-t a kérelem paramétereivel és a válaszkezelővel. Írja be a szöveget az API-ba, és ezt követően fejezze be a kérést.

```javascript
let req = https.request (request_params, response_handler);
req.write ("text=" + text);
req.end ();
```


## <a name="run-the-application"></a>Az alkalmazás futtatása

Készítse el és futtassa a projektet.

Ha a parancssort használja, az alkalmazás létrehozásához és futtatásához használja az alábbi parancsokat.

```bash
node <FILE_NAME>.js
```


## <a name="example-json-response"></a>Példa JSON-válaszra

A rendszer JSON formátumban ad vissza egy sikeres választ a következő példában látható módon:

```json
{
   "_type": "SpellCheck",
   "flaggedTokens": [
      {
         "offset": 0,
         "token": "Hollo",
         "type": "UnknownToken",
         "suggestions": [
            {
               "suggestion": "Hello",
               "score": 0.9115257530801
            },
            {
               "suggestion": "Hollow",
               "score": 0.858039839213461
            },
            {
               "suggestion": "Hallo",
               "score": 0.597385084464481
            }
         ]
      },
      {
         "offset": 7,
         "token": "wrld",
         "type": "UnknownToken",
         "suggestions": [
            {
               "suggestion": "world",
               "score": 0.9115257530801
            }
         ]
      }
   ]
}
```

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Egyoldalas webalkalmazás létrehozása](../tutorials/spellcheck.md)

- [Mi a Bing Spell Check API?](../overview.md)
- [A Bing Spell Check API 7-es verzió referenciája](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-spell-check-api-v7-reference)
