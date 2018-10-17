---
title: 'Rövid útmutató: Alternatív fordítások lekérése – Translator Text, Ruby'
titleSuffix: Azure Cognitive Services
description: Ebben a rövid útmutatóban a kifejezések alternatív fordításait és példáit keresi meg a szövegkörnyezetben a Translator Text API segítségével és Ruby használatával.
services: cognitive-services
author: noellelacharite
manager: cgronlun
ms.service: cognitive-services
ms.component: translator-text
ms.topic: quickstart
ms.date: 06/22/2018
ms.author: nolachar
ms.openlocfilehash: 7cd34e3482df666de8b974380302ee6b4c592496
ms.sourcegitcommit: f10653b10c2ad745f446b54a31664b7d9f9253fe
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/18/2018
ms.locfileid: "46122807"
---
# <a name="quickstart-find-alternate-translations-and-usage-with-ruby"></a>Rövid útmutató: Alternatív fordítások és használat lekérése a Ruby használatával

Ebben a rövid útmutatóban a kifejezések lehetséges alternatív fordításaival kapcsolatos részleteket, valamint az alternatív fordítások használatának példáit keresi meg a Translator Text API segítségével.

## <a name="prerequisites"></a>Előfeltételek

A kód futtatásához [Ruby 2.4](https://www.ruby-lang.org/en/downloads/) vagy újabb verzió szükséges.

A Translator Text API használatához szüksége van egy előfizetési kulcsra is. Lásd [a Translator Text API regisztrációját](translator-text-how-to-signup.md).

## <a name="dictionary-lookup-request"></a>Dictionary Lookup kérés

A következő egy szó alternatív fordításait kéri le a [Dictionary Lookup](./reference/v3-0-dictionary-lookup.md) metódussal.

1. Hozzon létre egy új Ruby-projektet a kedvenc kódszerkesztőjében.
2. Adja hozzá az alábbi kódot.
3. A `key` értéket cserélje le az előfizetéshez érvényes hozzáférési kulcsra.
4. Futtassa a programot.

```ruby
require 'net/https'
require 'uri'
require 'cgi'
require 'json'
require 'securerandom'

# **********************************************
# *** Update or verify the following values. ***
# **********************************************

# Replace the key string value with your valid subscription key.
key = 'ENTER KEY HERE'

host = 'https://api.cognitive.microsofttranslator.com'
path = '/dictionary/lookup?api-version=3.0'

params = '&from=en&to=fr'

uri = URI (host + path + params)

text = 'great'

content = '[{"Text" : "' + text + '"}]'

request = Net::HTTP::Post.new(uri)
request['Content-type'] = 'application/json'
request['Content-length'] = content.length
request['Ocp-Apim-Subscription-Key'] = key
request['X-ClientTraceId'] = SecureRandom.uuid
request.body = content

response = Net::HTTP.start(uri.host, uri.port, :use_ssl => uri.scheme == 'https') do |http|
    http.request (request)
end

result = response.body.force_encoding("utf-8")

json = JSON.pretty_generate(JSON.parse(result))
puts json
```

## <a name="dictionary-lookup-response"></a>Dictionary Lookup válasz

A rendszer JSON formátumban ad vissza egy sikeres választ a következő példában látható módon:

```json
[
  {
    "normalizedSource": "great",
    "displaySource": "great",
    "translations": [
      {
        "normalizedTarget": "grand",
        "displayTarget": "grand",
        "posTag": "ADJ",
        "confidence": 0.2783,
        "prefixWord": "",
        "backTranslations": [
          {
            "normalizedText": "great",
            "displayText": "great",
            "numExamples": 15,
            "frequencyCount": 34358
          },
          {
            "normalizedText": "big",
            "displayText": "big",
            "numExamples": 15,
            "frequencyCount": 21770
          },
...
        ]
      },
      {
        "normalizedTarget": "super",
        "displayTarget": "super",
        "posTag": "ADJ",
        "confidence": 0.1514,
        "prefixWord": "",
        "backTranslations": [
          {
            "normalizedText": "super",
            "displayText": "super",
            "numExamples": 15,
            "frequencyCount": 12023
          },
          {
            "normalizedText": "great",
            "displayText": "great",
            "numExamples": 15,
            "frequencyCount": 10931
          },
...
        ]
      },
...
    ]
  }
]
```

## <a name="dictionary-examples-request"></a>Dictionary Examples kérés

A következő a szótárban lévő kifejezések használatának szövegkörnyezetbe foglalt példáit kéri le a [Dictionary Examples](./reference/v3-0-dictionary-examples.md) metódussal.

1. Hozzon létre egy új Ruby-projektet a kedvenc kódszerkesztőjében.
2. Adja hozzá az alábbi kódot.
3. A `key` értéket cserélje le az előfizetéshez érvényes hozzáférési kulcsra.
4. Futtassa a programot.

```ruby
require 'net/https'
require 'uri'
require 'cgi'
require 'json'
require 'securerandom'

# **********************************************
# *** Update or verify the following values. ***
# **********************************************

# Replace the key string value with your valid subscription key.
key = 'ENTER KEY HERE'

host = 'https://api.cognitive.microsofttranslator.com'
path = '/dictionary/examples?api-version=3.0'

params = '&from=en&to=fr'

uri = URI (host + path + params)

text = 'great'
translation = 'formidable'

content = '[{"Text" : "' + text + '", "Translation" : "' + translation + '"}]'

request = Net::HTTP::Post.new(uri)
request['Content-type'] = 'application/json'
request['Content-length'] = content.length
request['Ocp-Apim-Subscription-Key'] = key
request['X-ClientTraceId'] = SecureRandom.uuid
request.body = content

response = Net::HTTP.start(uri.host, uri.port, :use_ssl => uri.scheme == 'https') do |http|
    http.request (request)
end

result = response.body.force_encoding("utf-8")

json = JSON.pretty_generate(JSON.parse(result))
puts json
```

## <a name="dictionary-examples-response"></a>Dictionary Examples válasz

A rendszer JSON formátumban ad vissza egy sikeres választ a következő példában látható módon:

```json
[
  {
    "normalizedSource": "great",
    "normalizedTarget": "formidable",
    "examples": [
      {
        "sourcePrefix": "You have a ",
        "sourceTerm": "great",
        "sourceSuffix": " expression there.",
        "targetPrefix": "Vous avez une expression ",
        "targetTerm": "formidable",
        "targetSuffix": "."
      },
      {
        "sourcePrefix": "You played a ",
        "sourceTerm": "great",
        "sourceSuffix": " game today.",
        "targetPrefix": "Vous avez été ",
        "targetTerm": "formidable",
        "targetSuffix": "."
      },
...
    ]
  }
]
```

## <a name="next-steps"></a>További lépések

A GitHubon megismerheti a rövid útmutató és egyebek mintakódját, beleértve a fordítást és az átírást is, valamint más Translator Text-projekteket.

> [!div class="nextstepaction"]
> [A Ruby-példák megismerése a GitHubon](https://aka.ms/TranslatorGitHub?type=&language=ruby)
