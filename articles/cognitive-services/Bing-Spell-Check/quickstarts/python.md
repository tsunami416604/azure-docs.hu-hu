---
title: 'Gyors útmutató: Helyesírás-ellenőrzés a Bing Spell Check REST API és a Python használatával'
titlesuffix: Azure Cognitive Services
description: Ismerkedés a Bing Spell Check REST API használatával a helyesírás-és nyelvtani.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-spell-check
ms.topic: quickstart
ms.date: 02/20/2019
ms.author: aahi
ms.openlocfilehash: 1cf46fd5ec55f0b240f6bb4adbe49c1344a4663b
ms.sourcegitcommit: 031e4165a1767c00bb5365ce9b2a189c8b69d4c0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/13/2019
ms.locfileid: "59547679"
---
# <a name="quickstart-check-spelling-with-the-bing-spell-check-rest-api-and-python"></a>Gyors útmutató: Helyesírás-ellenőrzés a Bing Spell Check REST API és a Python használatával

Ez a rövid útmutató segítségével, a Bing Spell Check REST API első hívását. Az egyszerű Python-alkalmazás egy kérést küld az API-t, és javasolt javítások listáját adja vissza. Bár ez az alkalmazás Python nyelven lett íródott, az API egy RESTful-webszolgáltatás, azaz kompatibilis a legtöbb programnyelvvel. Az alkalmazás forráskódja elérhető a [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/python/Search/BingEntitySearchv7.py)

## <a name="prerequisites"></a>Előfeltételek

* Python [3.x](https://www.python.org)

[!INCLUDE [cognitive-services-bing-spell-check-signup-requirements](../../../../includes/cognitive-services-bing-spell-check-signup-requirements.md)]

## <a name="initialize-the-application"></a>Az alkalmazás inicializálása

1. Hozzon létre egy új Python-fájlt a kedvenc integrált Fejlesztőkörnyezetével vagy szerkesztőjével, és adja hozzá a következő importálási utasítást.

   ```python
   import requests
   import json
   ```

2. Hozzon létre változókat a helyesírás-ellenőrzést, az előfizetési kulcs és a Bing helyesírás-ellenőrzési végpontra kívánt szöveg.

    ```python
    api_key = "<ENTER-KEY-HERE>"
    example_text = "Hollo, wrld" # the text to be spell-checked
    endpoint = "https://api.cognitive.microsoft.com/bing/v7.0/SpellCheck"
    ```

## <a name="create-the-parameters-for-the-request"></a>A kérelem paramétereinek létrehozása

1. Hozzon létre egy új szótár `text` a kulcsot, és a szöveges értéket.

    ```python
    data = {'text': example_text}
    ```

2. Adja hozzá a kérelem paramétereit. Fűzze hozzá a piaci kód után `mkt=`. A piaci kódja az ország, a kérést. Ezenkívül hozzáfűzéssel a helyesírás-ellenőrzése után `&mode=`. Módban, vagy `proof` (a legtöbb helyesírási vagy nyelvtani hibát kivételekkel) vagy `spell` (a legtöbb helyesírási, de nem annyi nyelvtani hibát kivételekkel).

    ```python
    params = {
        'mkt':'en-us',
        'mode':'proof'
        }
    ```

3. Adjon hozzá egy `Content-Type` fejléc és az előfizetési kulcs, a `Ocp-Apim-Subscription-Key` fejléc.

    ```python
    headers = {
        'Content-Type': 'application/x-www-form-urlencoded',
        'Ocp-Apim-Subscription-Key': api_key,
        }
    ```

## <a name="send-the-request-and-read-the-response"></a>A kérelem elküldéséhez és a válasz olvasása

1. A kérések kódtár használatával a POST-kérés küldése.

    ```python
    response = requests.post(endpoint, headers=headers, params=params, data=data)
    ```

2. A JSON-válasz lekérése, és nyomtassa ki.

    ```python
    json_response = response.json()
    print(json.dumps(json_response, indent=4))
    ```

## <a name="example-json-response"></a>Példa JSON-válasz

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
> [Hozzon létre egy egyoldalas webalkalmazást](../tutorials/spellcheck.md)

- [Mi az a Bing Spell Check API?](../overview.md)
- [A Bing Spell Check API 7-es verzió referenciája](https://docs.microsoft.com/rest/api/cognitiveservices/bing-spell-check-api-v7-reference)
