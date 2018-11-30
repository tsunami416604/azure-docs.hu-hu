---
title: 'Rövid útmutató: Bing Spell Check API, Ruby'
titlesuffix: Azure Cognitive Services
description: Információ és kódminták segítségével ismerkedhet meg a Bing Spell Check API használatának alapjaival.
services: cognitive-services
author: aahill
manager: cgronlun
ms.service: cognitive-services
ms.component: bing-spell-check
ms.topic: quickstart
ms.date: 09/14/2017
ms.author: aahi
ms.openlocfilehash: 9c044dd7404f0d317b4bc8ab39ea949a95573573
ms.sourcegitcommit: a08d1236f737915817815da299984461cc2ab07e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/26/2018
ms.locfileid: "52311789"
---
# <a name="quickstart-for-bing-spell-check-api-with-ruby"></a>Rövid útmutató a Bing Spell Check API és a Ruby használatához 

Ez a cikk bemutatja, hogyan használhatja a [Bing Spell Check API-t](https://azure.microsoft.com/services/cognitive-services/spell-check/)  a Rubyval. A Spell Check API visszaadja a fel nem ismert szavak listáját a javasolt cserekifejezésekkel együtt. Általános esetben küld egy szöveget az API-nak, majd végrehajtja a javasolt cseréket a szövegben, vagy megmutatja azokat az alkalmazás felhasználójának, hogy ő dönthesse el, végre szeretné-e hajtani a cserét. Ez a cikk bemutatja, hogyan küldhet el egy olyan kérést, amely a „Hollo, wrld!” szöveget tartalmazza. A javasolt cserekifejezés a „Hello” és a „world” lesz.

## <a name="prerequisites"></a>Előfeltételek

A kód futtatásához a [Ruby 2.4-es](https://www.ruby-lang.org/en/downloads/) vagy újabb verziója szükséges.

Rendelkeznie kell egy, a **Bing Spell Check API 7-es verzióját** tartalmazó [Cognitive Services API-fiókkal](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account). Az [ingyenes próbaverzió](https://azure.microsoft.com/try/cognitive-services/#lang) elegendő ehhez a rövid útmutatóhoz. Szüksége lesz az ingyenes próbaverzió aktiválásakor kapott hozzáférési kulcsra, vagy beszerezhet egy fizetős előfizetői azonosítót az Azure-irányítópultról. Lásd még: [a Cognitive Services díjszabás – keresési Bing-API](https://azure.microsoft.com/pricing/details/cognitive-services/search-api/).

## <a name="get-spell-check-results"></a>Spell Check-eredmények lekérése

1. Hozzon létre egy új Ruby-projektet a kedvenc IDE-környezetében.
2. Adja hozzá az alábbi kódot.
3. A `key` értéket cserélje le az előfizetéshez érvényes hozzáférési kulcsra.
4. Futtassa a programot.

```ruby
require 'net/http'
require 'uri'
require 'json'

uri = 'https://api.cognitive.microsoft.com'
path = '/bing/v7.0/spellcheck?'
params = 'mkt=en-us&mode=proof'

uri = URI(uri + path + params)
uri.query = URI.encode_www_form({
    # Request parameters
    'text' => 'Hollo, wrld!'
})

# NOTE: Replace this example key with a valid subscription key.
key = 'ENTER KEY HERE'

# The headers in the following example 
# are optional but should be considered as required:
#
# X-MSEdge-ClientIP: 999.999.999.999  
# X-Search-Location: lat: +90.0000000000000;long: 00.0000000000000;re:100.000000000000
# X-MSEdge-ClientID: <Client ID from Previous Response Goes Here>
#
# See below for more information.

request = Net::HTTP::Post.new(uri)
request['Content-Type'] = "application/x-www-form-urlencoded"

request['Ocp-Apim-Subscription-Key'] = key

response = Net::HTTP.start(uri.host, uri.port, :use_ssl => uri.scheme == 'https') do |http|
    http.request(request)
end

result = JSON.pretty_generate(JSON.parse(response.body))
puts result
```

**Válasz**

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
> [A Bing Spell Check oktatóanyaga](../tutorials/spellcheck.md)

## <a name="see-also"></a>Lásd még

- [A Bing Spell Check áttekintése](../proof-text.md)
- [A Bing Spell Check API 7-es verzió referenciája](https://docs.microsoft.com/rest/api/cognitiveservices/bing-spell-check-api-v7-reference)
