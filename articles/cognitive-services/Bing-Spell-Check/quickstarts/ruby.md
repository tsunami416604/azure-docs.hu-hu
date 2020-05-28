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
ms.date: 05/21/2020
ms.author: aahi
ms.openlocfilehash: fae466124244f5d2b04ad6e59681011b9c5ba974
ms.sourcegitcommit: fc718cc1078594819e8ed640b6ee4bef39e91f7f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/27/2020
ms.locfileid: "83993524"
---
# <a name="quickstart-check-spelling-with-the-bing-spell-check-rest-api-and-ruby"></a>Gyors útmutató: helyesírás ellenőrzése a Bing Spell Check REST API és a Ruby

Ezzel a rövid útmutatóval elvégezheti az első hívását a Bing Spell Check REST API a Ruby használatával. Ez az egyszerű alkalmazás egy kérelmet küld az API-nak, és a javasolt javítások listáját adja vissza. 

Bár ez az alkalmazás a Rubyban van megírva, az API egy REST-alapú webszolgáltatás, amely a legtöbb programozási nyelvvel kompatibilis. Az alkalmazás forráskódja elérhető a [githubon](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/ruby/Search/BingSpellCheckv7.rb)

## <a name="prerequisites"></a>Előfeltételek

* [Ruby 2,4](https://www.ruby-lang.org/en/downloads/) vagy újabb.

[!INCLUDE [cognitive-services-bing-spell-check-signup-requirements](../../../../includes/cognitive-services-bing-spell-check-signup-requirements.md)]


## <a name="create-and-initialize-the-application"></a>Az alkalmazás létrehozása és inicializálása

1. Hozzon létre egy új Ruby-fájlt a kedvenc szerkesztőjében vagy az IDE-ben, és adja hozzá a következő követelményeket: 

    ```ruby
    require 'net/http'
    require 'uri'
    require 'json'
    ```

2. Hozzon létre változókat az előfizetési kulcshoz, a végpont URI azonosítóhoz és az elérési úthoz. Használhatja a globális végpontot a következő kódban, vagy használhatja az erőforráshoz tartozó Azure Portalban megjelenő [Egyéni altartomány](../../../cognitive-services/cognitive-services-custom-subdomains.md) -végpontot. Hozza létre a kérelmek paramétereit:

   1. Rendelje hozzá a piaci kódot a `mkt` paraméterhez az `=` operátorral. A piaci kód annak az országnak/régiónak a kódja, amelyre a kérést végzi. 

   1. Adja hozzá a `mode` paramétert a `&` kezelőhöz, majd rendelje hozzá a helyesírás-ellenőrzési módot. A mód lehet (a `proof` legtöbb helyesírási/nyelvtani hibát felhasználhatja) `spell` , vagy (a legtöbb helyesírási hibát kigyűjti, de nem annyi nyelvtani hibát). 

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

2. Hozzon létre egy kérelmet a korábban létrehozott URI használatával. Adja hozzá a kulcsot a `Ocp-Apim-Subscription-Key` fejléchez.

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

## <a name="run-the-application"></a>Alkalmazás futtatása

Hozza létre és futtassa a projektet. Ha a parancssort használja, a következő paranccsal futtathatja az alkalmazást:

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
- [Bing Spell Check API v7-dokumentáció](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-spell-check-api-v7-reference)
