---
title: 'Gyors útmutató: helyesírás ellenőrzése a REST API és a Ruby-Bing Spell Check'
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
ms.openlocfilehash: 89a2a345e2a4e3ca1be31297e614e86f800e6316
ms.sourcegitcommit: 34a6fa5fc66b1cfdfbf8178ef5cdb151c97c721c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "75448433"
---
# <a name="quickstart-check-spelling-with-the-bing-spell-check-rest-api-and-ruby"></a>Gyors útmutató: helyesírás ellenőrzése a Bing Spell Check REST API és a Ruby

Ezzel a rövid útmutatóval elvégezheti az első hívását a Bing Spell Check REST API a Ruby használatával. Ez az egyszerű alkalmazás egy kérelmet küld az API-nak, és visszaadja a nem felismerhető szavak listáját, amelyet a javasolt javítások követnek. Bár ez az alkalmazás Ruby nyelven lett íródott, az API egy RESTful-webszolgáltatás, azaz kompatibilis a legtöbb programnyelvvel. Az alkalmazás forráskódja elérhető a [githubon](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/ruby/Search/BingSpellCheckv7.rb)

## <a name="prerequisites"></a>Előfeltételek

* [Ruby 2,4](https://www.ruby-lang.org/en/downloads/) vagy újabb.

[!INCLUDE [cognitive-services-bing-spell-check-signup-requirements](../../../../includes/cognitive-services-bing-spell-check-signup-requirements.md)]


## <a name="create-and-initialize-the-application"></a>Az alkalmazás létrehozása és inicializálása

1. Hozzon létre egy új Ruby-fájlt a kedvenc szerkesztőjében vagy az IDE-ben, és adja hozzá az alábbi követelményeket. 

    ```ruby
    require 'net/http'
    require 'uri'
    require 'json'
    ```

2. Hozzon létre változókat az előfizetési kulcshoz, a végpont URI azonosítóhoz és elérési úthoz. Hozza létre a kérelmek paramétereit úgy, `mkt=` hogy hozzáfűzi a paramétert `&mode` a piacához, valamint a `proof` Proof módba. Használhatja az alábbi globális végpontot, vagy az erőforráshoz tartozó Azure Portalban megjelenő [Egyéni altartomány](../../../cognitive-services/cognitive-services-custom-subdomains.md) végpontot.

    ```ruby
    key = 'ENTER YOUR KEY HERE'
    uri = 'https://api.cognitive.microsoft.com'
    path = '/bing/v7.0/spellcheck?'
    params = 'mkt=en-us&mode=proof'
    ```

## <a name="send-a-spell-check-request"></a>Helyesírás-ellenőrzési kérelem küldése

1. Hozzon létre egy URI-t a gazdagép URI-ja, elérési útja és paramétereinek karakterlánca alapján. Állítsa be a lekérdezést úgy, hogy tartalmazza a helyesírás-ellenőrzéshez használni kívánt szöveget.

   ```ruby
   uri = URI(uri + path + params)
   uri.query = URI.encode_www_form({
      # Request parameters
   'text' => 'Hollo, wrld!'
   })
   ```

2. Hozzon létre egy kérelmet a fent létrehozott URI használatával. Adja hozzá a kulcsot a `Ocp-Apim-Subscription-Key` fejléchez.

    ```ruby
    request = Net::HTTP::Post.new(uri)
    request['Content-Type'] = "application/x-www-form-urlencoded"
    request['Ocp-Apim-Subscription-Key'] = key
    ```

3. Küldje el a kérést.

    ```ruby
    response = Net::HTTP.start(uri.host, uri.port, :use_ssl => uri.scheme == 'https') do |http|
        http.request(request)
    end
    ```

4. Kérje le a JSON-választ, és nyomtassa ki a konzolra. 

    ```ruby
    result = JSON.pretty_generate(JSON.parse(response.body))
    puts result
    ```

## <a name="run-the-application"></a>Az alkalmazás futtatása

Hozza létre és futtassa a projektet.

Ha a parancssort használja, futtassa az alábbi parancsot az alkalmazás futtatásához.

```bash
ruby <FILE_NAME>.rb
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
