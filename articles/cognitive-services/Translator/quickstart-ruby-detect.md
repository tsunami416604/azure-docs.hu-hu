---
title: 'Gyors útmutató: Azonosíthatja a szöveg nyelvét Ruby – Translator Text API'
titleSuffix: Azure Cognitive Services
description: Ebben a rövid útmutatóban felismeri a forrásszöveg nyelvét a Translator Text API és Ruby segítségével.
services: cognitive-services
author: erhopf
manager: cgronlun
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: quickstart
ms.date: 06/22/2018
ms.author: erhopf
ms.openlocfilehash: d710f1ae1df04cf5e97a3583a0cbe9a7c59abdb3
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/31/2019
ms.locfileid: "55461134"
---
# <a name="quickstart-identify-language-from-text-with-the-translator-text-rest-api-ruby"></a>Gyors útmutató: A Translator Text REST API (Ruby) szöveg nyelvét azonosítása

Ebben a rövid útmutatóban felismeri a forrásszöveg nyelvét a Translator Text API segítségével.

## <a name="prerequisites"></a>Előfeltételek

A kód futtatásához [Ruby 2.4](https://www.ruby-lang.org/en/downloads/) vagy újabb verzió szükséges.

A Translator Text API használatához szüksége van egy előfizetési kulcsra is. Lásd [a Translator Text API regisztrációját](translator-text-how-to-signup.md).

## <a name="detect-request"></a>Detect kérés

A következő kód felismeri a forrásszöveg nyelvét a [Detect](./reference/v3-0-detect.md) metódussal.

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
path = '/detect?api-version=3.0'

uri = URI (host + path)

text = 'Salve, mondo!'

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

## <a name="detect-response"></a>Detect válasz

A rendszer JSON formátumban ad vissza egy sikeres választ a következő példában látható módon:

```json
[
  {
    "language": "it",
    "score": 1.0,
    "isTranslationSupported": true,
    "isTransliterationSupported": false,
    "alternatives": [
      {
        "language": "pt",
        "score": 1.0,
        "isTranslationSupported": true,
        "isTransliterationSupported": false
      },
      {
        "language": "en",
        "score": 1.0,
        "isTranslationSupported": true,
        "isTransliterationSupported": false
      }
    ]
  }
]
```

## <a name="next-steps"></a>További lépések

A GitHubon megismerheti a rövid útmutató és egyebek mintakódját, beleértve a fordítást és az átírást is, valamint más Translator Text-projekteket.

> [!div class="nextstepaction"]
> [A Ruby-példák megismerése a GitHubon](https://aka.ms/TranslatorGitHub?type=&language=ruby)
