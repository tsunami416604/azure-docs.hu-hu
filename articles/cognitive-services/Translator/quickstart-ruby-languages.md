---
title: 'Rövid útmutató: Támogatott nyelvek lekérése – Translator Text, Ruby'
titleSuffix: Azure Cognitive Services
description: Ebben a rövid útmutatóban lekéri a fordításhoz, átíráshoz és szótárban való kereséshez támogatott nyelvek, valamint példák listáját a Translator Text API és a Ruby segítségével.
services: cognitive-services
author: noellelacharite
manager: cgronlun
ms.service: cognitive-services
ms.component: translator-text
ms.topic: quickstart
ms.date: 06/22/2018
ms.author: nolachar
ms.openlocfilehash: f000d2c4c1519f26b4d82a36803b86f016716c5e
ms.sourcegitcommit: f10653b10c2ad745f446b54a31664b7d9f9253fe
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/18/2018
ms.locfileid: "46121838"
---
# <a name="quickstart-get-supported-languages-with-ruby"></a>Rövid útmutató: Támogatott nyelvek lekérése a Ruby használatával

Ebben a rövid útmutatóban lekéri a fordításhoz, átíráshoz és szótárban való kereséshez támogatott nyelvek, valamint példák listáját a Translator Text API segítségével.

## <a name="prerequisites"></a>Előfeltételek

A kód futtatásához [Ruby 2.4](https://www.ruby-lang.org/en/downloads/) vagy újabb verzió szükséges.

A Translator Text API használatához szüksége van egy előfizetési kulcsra is. Lásd [a Translator Text API regisztrációját](translator-text-how-to-signup.md).

## <a name="languages-request"></a>Languages kérés

A következő kód lekéri a fordításhoz, átíráshoz és szótárban való kereséshez támogatott nyelveket és példákat a [Languages](./reference/v3-0-languages.md) metódussal.

1. Hozzon létre egy új Ruby-projektet a kedvenc kódszerkesztőjében.
2. Adja hozzá az alábbi kódot.
3. A `key` értéket cserélje le az előfizetéshez érvényes hozzáférési kulcsra.
4. Futtassa a programot.

```ruby
require 'net/https'
require 'uri'
require 'cgi'
require 'json'

# **********************************************
# *** Update or verify the following values. ***
# **********************************************

# Replace the key string value with your valid subscription key.
key = 'ENTER KEY HERE'

host = 'https://api.cognitive.microsofttranslator.com'
path = '/languages?api-version=3.0'

uri = URI (host + path)

request = Net::HTTP::Get.new(uri)
request['Ocp-Apim-Subscription-Key'] = key

response = Net::HTTP.start(uri.host, uri.port, :use_ssl => uri.scheme == 'https') do |http|
    http.request (request)
end

result = response.body.force_encoding("utf-8")

json = JSON.pretty_generate(JSON.parse(result))

output_path = 'output.txt'

File.open(output_path, 'w' ) do |output|
    output.print json
end
```

## <a name="languages-response"></a>Languages válasz

A rendszer JSON formátumban ad vissza egy sikeres választ a következő példában látható módon:

```json
{
  "translation": {
    "af": {
      "name": "Afrikaans",
      "nativeName": "Afrikaans",
      "dir": "ltr"
    },
    "ar": {
      "name": "Arabic",
      "nativeName": "العربية",
      "dir": "rtl"
    },
...
  },
  "transliteration": {
    "ar": {
      "name": "Arabic",
      "nativeName": "العربية",
      "scripts": [
        {
          "code": "Arab",
          "name": "Arabic",
          "nativeName": "العربية",
          "dir": "rtl",
          "toScripts": [
            {
              "code": "Latn",
              "name": "Latin",
              "nativeName": "اللاتينية",
              "dir": "ltr"
            }
          ]
        },
        {
          "code": "Latn",
          "name": "Latin",
          "nativeName": "اللاتينية",
          "dir": "ltr",
          "toScripts": [
            {
              "code": "Arab",
              "name": "Arabic",
              "nativeName": "العربية",
              "dir": "rtl"
            }
          ]
        }
      ]
    },
...
  },
  "dictionary": {
    "af": {
      "name": "Afrikaans",
      "nativeName": "Afrikaans",
      "dir": "ltr",
      "translations": [
        {
          "name": "English",
          "nativeName": "English",
          "dir": "ltr",
          "code": "en"
        }
      ]
    },
    "ar": {
      "name": "Arabic",
      "nativeName": "العربية",
      "dir": "rtl",
      "translations": [
        {
          "name": "English",
          "nativeName": "English",
          "dir": "ltr",
          "code": "en"
        }
      ]
    },
...
  }
}
```

## <a name="next-steps"></a>További lépések

A GitHubon megismerheti a rövid útmutató és egyebek mintakódját, beleértve a fordítást és az átírást is, valamint más Translator Text-projekteket.

> [!div class="nextstepaction"]
> [A Ruby-példák megismerése a GitHubon](https://aka.ms/TranslatorGitHub?type=&language=ruby)
