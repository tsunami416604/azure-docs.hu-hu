---
title: 'Gyors útmutató: Helyesírás ellenőrzése a Bing Spell Check REST API és a Ruby'
titleSuffix: Azure Cognitive Services
description: Az Bing Spell Check REST API használatának első lépései a helyesírás és a nyelvtan ellenőrzéséhez.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-spell-check
ms.topic: quickstart
ms.date: 09/13/2019
ms.author: aahi
ms.openlocfilehash: bf038b97335db20349577f754bfa41e1b98ee9b7
ms.sourcegitcommit: 1752581945226a748b3c7141bffeb1c0616ad720
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/14/2019
ms.locfileid: "70996744"
---
# <a name="quickstart-check-spelling-with-the-bing-spell-check-rest-api-and-ruby"></a>Gyors útmutató: Helyesírás ellenőrzése a Bing Spell Check REST API és a Ruby

Ezzel a rövid útmutatóval elvégezheti az első hívását a Bing Spell Check REST API a Ruby használatával. Ez az egyszerű alkalmazás egy kérelmet küld az API-nak, és visszaadja a nem felismerhető szavak listáját, amelyet a javasolt javítások követnek. Bár ez az alkalmazás Ruby nyelven lett íródott, az API egy RESTful-webszolgáltatás, azaz kompatibilis a legtöbb programnyelvvel. Az alkalmazás forráskódja elérhető a [githubon](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/ruby/Search/BingSpellCheckv7.rb)

## <a name="prerequisites"></a>Előfeltételek

* [Ruby 2,4](https://www.ruby-lang.org/en/downloads/) vagy újabb.

[!INCLUDE [cognitive-services-bing-spell-check-signup-requirements](../../../../includes/cognitive-services-bing-spell-check-signup-requirements.md)]


## <a name="create-and-initialize-the-application"></a>Az alkalmazás létrehozása és inicializálása

1. Hozzon létre egy új Ruby-fájlt a kedvenc szerkesztőjében vagy az IDE-ben, és adja hozzá az alábbi követelményeket. 

    ```javascript
    require 'net/http'
    require 'uri'
    require 'json'
    ```

2. Hozzon létre változókat az előfizetési kulcshoz, a végpont URI azonosítóhoz és elérési úthoz. Hozza létre a kérelmek paramétereit úgy, `mkt=` hogy hozzáfűzi a paramétert `&mode` a piacához, valamint a `proof` Proof módba.

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
