---
title: 'Rövid útmutató: Helyesírás-ellenőrzés a REST API-val és a Pythonnal – Bing helyesírás-ellenőrzés'
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
ms.openlocfilehash: 6b0977628f7c3d971804d8597f42425608028081
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75448454"
---
# <a name="quickstart-check-spelling-with-the-bing-spell-check-rest-api-and-python"></a>Rövid útmutató: Helyesírás-ellenőrzés a Bing helyesírás-ellenőrző REST API-val és a Pythonnal

Ezzel a rövid útmutatóval elsőként hívhatja meg a Bing Helyesírás-ellenőrző REST API-t. Ez az egyszerű Python-alkalmazás kérést küld az API-nak, és visszaadja a javasolt javítások listáját. Bár ez az alkalmazás Python nyelven lett íródott, az API egy RESTful-webszolgáltatás, azaz kompatibilis a legtöbb programnyelvvel. Az alkalmazás forráskódja elérhető a [GitHubon](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/python/Search/BingEntitySearchv7.py)

## <a name="prerequisites"></a>Előfeltételek

* Python [3.x](https://www.python.org)

[!INCLUDE [cognitive-services-bing-spell-check-signup-requirements](../../../../includes/cognitive-services-bing-spell-check-signup-requirements.md)]

## <a name="initialize-the-application"></a>Az alkalmazás inicializálása

1. Hozzon létre egy új Python-fájlt a kedvenc IDE-ben vagy szerkesztőjében, és adja hozzá a következő importálási utasítást.

   ```python
   import requests
   import json
   ```

2. Hozzon létre változókat a helyesírás-ellenőrzéshez, az előfizetési kulcshoz és a Bing helyesírás-ellenőrzés végpontjához. Használhatja az alábbi globális végpontot, vagy az [egyéni altartomány-végpontot,](../../../cognitive-services/cognitive-services-custom-subdomains.md) amely az azure-portálon jelenik meg az erőforráshoz.

    ```python
    api_key = "<ENTER-KEY-HERE>"
    example_text = "Hollo, wrld" # the text to be spell-checked
    endpoint = "https://api.cognitive.microsoft.com/bing/v7.0/SpellCheck"
    ```

## <a name="create-the-parameters-for-the-request"></a>A kérelem paramétereinek létrehozása

1. Hozzon létre egy `text` új szótárt, amelynek kulcsa, a szöveg pedig az érték.

    ```python
    data = {'text': example_text}
    ```

2. Adja hozzá a kérelem paramétereit. Fűzze hozzá a `mkt=`piaci kódot után . A piaci kód az az ország, ahonnan a kérelmet benyújtod. A helyesírás-ellenőrzési módat is `&mode=`csatolja a után. A mód `proof` vagy (a legtöbb helyesírási/nyelvtani hibát elkap) vagy `spell` (a legtöbb helyesírást, de nem annyi nyelvtani hibát) kapja meg).

    ```python
    params = {
        'mkt':'en-us',
        'mode':'proof'
        }
    ```

3. Adja `Content-Type` hozzá a fejlécet és `Ocp-Apim-Subscription-Key` az előfizetési kulcsot a fejléchez.

    ```python
    headers = {
        'Content-Type': 'application/x-www-form-urlencoded',
        'Ocp-Apim-Subscription-Key': api_key,
        }
    ```

## <a name="send-the-request-and-read-the-response"></a>Küldje el a kérést, és olvassa el a választ

1. Küldje el a POST-kérelmet a kéréstár használatával.

    ```python
    response = requests.post(endpoint, headers=headers, params=params, data=data)
    ```

2. Szerezd meg a JSON választ, és nyomtassa ki.

    ```python
    json_response = response.json()
    print(json.dumps(json_response, indent=4))
    ```


## <a name="run-the-application"></a>Az alkalmazás futtatása

Ha a parancssort használja, az alkalmazás futtatásához használja a következő parancsot.

```bash
python <FILE_NAME>.py
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
