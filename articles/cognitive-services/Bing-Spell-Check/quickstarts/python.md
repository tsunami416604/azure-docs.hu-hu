---
title: 'Gyors útmutató: Helyesírás ellenőrzése a Bing Spell Check REST API és a Python'
titleSuffix: Azure Cognitive Services
description: Az Bing Spell Check REST API használatának első lépései a helyesírás és a nyelvtan ellenőrzéséhez.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-spell-check
ms.topic: quickstart
ms.date: 02/20/2019
ms.author: aahi
ms.openlocfilehash: 90d026c12007d74de176d222e11b48d15c174855
ms.sourcegitcommit: a0b37e18b8823025e64427c26fae9fb7a3fe355a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/25/2019
ms.locfileid: "68500984"
---
# <a name="quickstart-check-spelling-with-the-bing-spell-check-rest-api-and-python"></a>Gyors útmutató: Helyesírás ellenőrzése a Bing Spell Check REST API és a Python

Ezzel a rövid útmutatóval megteheti az első hívást a Bing Spell Check REST API. Ez az egyszerű Python-alkalmazás kérelmet küld az API-nak, és a javasolt javítások listáját adja vissza. Bár ez az alkalmazás Python nyelven lett íródott, az API egy RESTful-webszolgáltatás, azaz kompatibilis a legtöbb programnyelvvel. Az alkalmazás forráskódja elérhető a [githubon](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/python/Search/BingEntitySearchv7.py)

## <a name="prerequisites"></a>Előfeltételek

* Python [3. x](https://www.python.org)

[!INCLUDE [cognitive-services-bing-spell-check-signup-requirements](../../../../includes/cognitive-services-bing-spell-check-signup-requirements.md)]

## <a name="initialize-the-application"></a>Az alkalmazás inicializálása

1. Hozzon létre egy új Python-fájlt a kedvenc IDE vagy szerkesztőben, és adja hozzá a következő importálási utasítást.

   ```python
   import requests
   import json
   ```

2. Hozzon létre változókat a helyesírás-ellenőrzéshez használni kívánt szöveghez, az előfizetési kulcshoz és a Bing Spell Check végponthoz.

    ```python
    api_key = "<ENTER-KEY-HERE>"
    example_text = "Hollo, wrld" # the text to be spell-checked
    endpoint = "https://api.cognitive.microsoft.com/bing/v7.0/SpellCheck"
    ```

## <a name="create-the-parameters-for-the-request"></a>A kérelem paramétereinek létrehozása

1. Hozzon létre egy új `text` szótárt a kulcs és a szöveg értékként.

    ```python
    data = {'text': example_text}
    ```

2. Adja hozzá a kérelem paramétereit. Adja hozzá a piac kódját `mkt=`a következő után:. A piaci kód az az ország, ahonnan a kérést elvégzi. Továbbá a helyesírás-ellenőrzési mód hozzáfűzése a következő `&mode=`után:. A `proof` mód vagy (a legtöbb helyesírási/nyelvtani hibát kigyűjti) vagy `spell` (a legtöbb helyesírási hiba, de nem annyi nyelvtani hiba).

    ```python
    params = {
        'mkt':'en-us',
        'mode':'proof'
        }
    ```

3. Adjon hozzá `Content-Type` egy fejlécet és egy előfizetési kulcsot `Ocp-Apim-Subscription-Key` a fejléchez.

    ```python
    headers = {
        'Content-Type': 'application/x-www-form-urlencoded',
        'Ocp-Apim-Subscription-Key': api_key,
        }
    ```

## <a name="send-the-request-and-read-the-response"></a>A kérelem elküldése és a válasz elolvasása

1. Küldje el a POST kérést a kérelmek könyvtárának használatával.

    ```python
    response = requests.post(endpoint, headers=headers, params=params, data=data)
    ```

2. Kérje le a JSON-választ, és nyomtassa ki.

    ```python
    json_response = response.json()
    print(json.dumps(json_response, indent=4))
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
> [Egyoldalas webes alkalmazás létrehozása](../tutorials/spellcheck.md)

- [Mi a Bing Spell Check API?](../overview.md)
- [A Bing Spell Check API 7-es verzió referenciája](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-spell-check-api-v7-reference)
