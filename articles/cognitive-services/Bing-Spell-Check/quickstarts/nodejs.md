---
title: 'Gyors útmutató: helyesírás ellenőrzése a REST API és a Node. js-Bing Spell Check'
titleSuffix: Azure Cognitive Services
description: Ismerkedjen meg a Bing Spell Check REST API használatával a helyesírás és a nyelvtan ellenőrzéséhez ezzel a rövid útmutatóval.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-spell-check
ms.topic: quickstart
ms.date: 12/16/2019
ms.author: aahi
ms.openlocfilehash: 69c391e6c3f93a998ade7c5721a528d895f8df76
ms.sourcegitcommit: 34a6fa5fc66b1cfdfbf8178ef5cdb151c97c721c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "75382854"
---
# <a name="quickstart-check-spelling-with-the-bing-spell-check-rest-api-and-nodejs"></a>Gyors útmutató: helyesírás ellenőrzése a Bing Spell Check REST API és Node. js-sel

Ezzel a rövid útmutatóval megteheti az első hívást a Bing Spell Check REST API. Ez az egyszerű csomópont-alkalmazás egy kérelmet küld az API-nak, és visszaadja a nem felismerhető szavak listáját, amelyet a javasolt javítások követnek. Habár ez az alkalmazás a Node. js-ben íródott, az API egy REST-alapú webszolgáltatás, amely kompatibilis a legtöbb programozási nyelvvel. Az alkalmazás forráskódja elérhető a [githubon](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/nodejs/Search/BingSpellCheckv7.js).

## <a name="prerequisites"></a>Előfeltételek

* [Node. js 6](https://nodejs.org/en/download/) vagy újabb.

[!INCLUDE [cognitive-services-bing-spell-check-signup-requirements](../../../../includes/cognitive-services-bing-spell-check-signup-requirements.md)]


## <a name="create-and-initialize-a-project"></a>Projekt létrehozása és inicializálása

1. Hozzon létre egy új JavaScript-fájlt a kedvenc IDE vagy szerkesztőben. Állítsa be a szigorúság és a `https`szükséges értékeit. Ezután hozzon létre változókat az API-végpont gazdagépéhez, elérési útjához és az előfizetési kulcshoz. Használhatja az alábbi globális végpontot, vagy az erőforráshoz tartozó Azure Portalban megjelenő [Egyéni altartomány](../../../cognitive-services/cognitive-services-custom-subdomains.md) végpontot.

    ```javascript
    'use strict';
    let https = require ('https');

    let host = 'api.cognitive.microsoft.com';
    let path = '/bing/v7.0/spellcheck';
    let key = '<ENTER-KEY-HERE>';
    ```

2. Hozzon létre változókat a keresési paraméterekhez és az áttekinteni kívánt szöveghez. Adja hozzá a piac kódját `mkt=`a következő után:. A piaci kód az az ország, ahonnan a kérést elvégzi. Továbbá a helyesírás-ellenőrzési mód hozzáfűzése a következő `&mode=`után:. A mód vagy `proof` (a legtöbb helyesírási/nyelvtani hibát kigyűjti) vagy `spell` (a legtöbb helyesírási hiba, de nem annyi nyelvtani hiba).

    ```javascript
    let mkt = "en-US";
    let mode = "proof";
    let text = "Hollo, wrld!";
    let query_string = "?mkt=" + mkt + "&mode=" + mode;
    ```

## <a name="create-the-request-parameters"></a>A kérés paramétereinek létrehozása

A kérés paramétereinek létrehozásához hozzon létre egy új objektumot `POST` egy metódussal. Adja meg az elérési utat a végpont elérési útjának hozzáfűzésével és a lekérdezési karakterlánccal. Adja hozzá az előfizetési kulcsot `Ocp-Apim-Subscription-Key` a fejléchez.

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

Hozzon létre egy `response_handler` nevű függvényt, amely a JSON-választ az API-ból veszi át, és nyomtassa ki. Hozzon létre egy változót a válasz törzse számára. A válasz hozzáfűzése a `data` jelző fogadásakor, a használatával `response.on()`. Ha a `end` rendszer jelzőt fogad, nyomtassa ki a JSON-törzset a konzolon.

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

Hívja meg az API `https.request()` -t a kérés paramétereinek és a Response Handler használatával. Írja be a szöveget az API-ba, és utána fejezze be a kérést.

```javascript
let req = https.request (request_params, response_handler);
req.write ("text=" + text);
req.end ();
```


## <a name="run-the-application"></a>Az alkalmazás futtatása

Hozza létre és futtassa a projektet.

Ha a parancssort használja, az alábbi parancsokkal hozhatja létre és futtathatja az alkalmazást.

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
