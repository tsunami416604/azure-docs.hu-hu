---
title: Hívás-válasz - Ruby gyors üzembe helyezés az Azure kognitív szolgáltatások, a Bing webes keresés API |} Microsoft Docs
description: Get információkat és a kód minták segítségével gyorsan használatának megkezdésében a webes Bing keresési API a Microsoft Azure kognitív Services.
services: cognitive-services
documentationcenter: ''
author: v-jerkin
ms.service: cognitive-services
ms.component: bing-web-search
ms.topic: article
ms.date: 9/18/2017
ms.author: v-jerkin
ms.openlocfilehash: b0f5c395fcdf043f4111f63ef16f0d33d5257e74
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/23/2018
ms.locfileid: "35347538"
---
# <a name="call-and-response-your-first-bing-web-search-query-in-ruby"></a>Hívás-válasz: a Ruby első Bing keresést a lekérdezés

A webes Bing keresési API vissza a találatok között, amely meghatározza a Bing kapcsolódik a lekérdezés a felhasználó Bing.com/Search hasonló élményt nyújt. Az eredmények lehet, hogy tartalmazzák a weblapok, képek, videók, híreket és entitások, kapcsolódó keresési lekérdezések, helyesen adta-e javításokat, időzónák, egység átalakítás, fordítások és számítások együtt. A kapott eredmények típusú azok relevanciájának és a réteg a Bing keresési API-k riasztásról alapulnak.

A cikk tartalmaz egy egyszerű konzolalkalmazást, amely a Bing webes API-t keresési lekérdezést hajt végre, és a visszaadott nyers keresési eredmények között, amelyek JSON formátumban jeleníti meg. Az alkalmazás íródott Ruby, amíg az API-t olyan kompatibilis bármely programozási nyelv, amely HTTP-kérelmeket, és elemezni a JSON a RESTful webes szolgáltatás. 

## <a name="prerequisites"></a>Előfeltételek

Szüksége lesz [Ruby 2.4-es vagy újabb](https://www.ruby-lang.org/en/downloads/) a példakódot futtatásához.

Rendelkeznie kell egy [kognitív szolgáltatások API-fiók](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) rendelkező **Bing keresési API-k**. A [ingyenes próbaverzió](https://azure.microsoft.com/try/cognitive-services/?api=bing-web-search-api) elegendő-e a gyors üzembe helyezés. Az elérési kulcsot, ha aktiválja az ingyenes próbaverzió, vagy egy fizetős kulcsot használhatja az Azure irányítópultról van szüksége.

## <a name="running-the-application"></a>Az alkalmazás futtatása

Az alkalmazás futtatásához kövesse az alábbi lépéseket.

1. Hozzon létre egy új Ruby-projektet a kedvenc IDE vagy szerkesztő.
2. Adja hozzá a megadott kódot.
3. Cserélje le a `accessKey` hívóbetű érvényes az előfizetéshez tartozó értéket.
4. Futtassa a programot.

```ruby
require 'net/https'
require 'uri'
require 'json'

# **********************************************
# *** Update or verify the following values. ***
# **********************************************

# Replace the accessKey string value with your valid access key.
accessKey = "enter key here"

# Verify the endpoint URI.  At this writing, only one endpoint is used for Bing
# search APIs.  In the future, regional endpoints may be available.  If you
# encounter unexpected authorization errors, double-check this value against
# the endpoint for your Bing Web search instance in your Azure dashboard.

uri  = "https://api.cognitive.microsoft.com"
path = "/bing/v7.0/search"

term = "Microsoft Cognitive Services"

if accessKey.length != 32 then
    puts "Invalid Bing Search API subscription key!"
    puts "Please paste yours into the source code."
    abort
end

uri = URI(uri + path + "?q=" + URI.escape(term))

puts "Searching the Web for: " + term

request = Net::HTTP::Get.new(uri)
request['Ocp-Apim-Subscription-Key'] = accessKey

response = Net::HTTP.start(uri.host, uri.port, :use_ssl => uri.scheme == 'https') do |http|
    http.request(request)
end

puts "\nRelevant Headers:\n\n"
response.each_header do |key, value|
    # header names are coerced to lowercase
    if key.start_with?("bingapis-") or key.start_with?("x-msedge-") then
        puts key + ": " + value
    end
end

puts "\nJSON Response:\n\n"
puts JSON::pretty_generate(JSON(response.body))
```

## <a name="json-response"></a>JSON-válasz

A következő mintát választ. A JSON hosszát korlátozásához csak egyetlen eredmény látható, és a választ más részei le lettek rövidítve. 

```json
{
  "_type": "SearchResponse",
  "queryContext": {
    "originalQuery": "Microsoft Cognitive Services"
  },
  "webPages": {
    "webSearchUrl": "https://www.bing.com/search?q=Microsoft+cognitive+services",
    "totalEstimatedMatches": 22300000,
    "value": [
      {
        "id": "https://api.cognitive.microsoft.com/api/v7/#WebPages.0",
        "name": "Microsoft Cognitive Services",
        "url": "https://www.microsoft.com/cognitive-services",
        "displayUrl": "https://www.microsoft.com/cognitive-services",
        "snippet": "Knock down barriers between you and your ideas. Enable natural and contextual interaction with tools that augment users' experiences via the power of machine-based AI. Plug them in and bring your ideas to life.",
        "deepLinks": [
          {
            "name": "Face API",
            "url": "https://azure.microsoft.com/services/cognitive-services/face/",
            "snippet": "Add facial recognition to your applications to detect, identify, and verify faces using a Face API from Microsoft Azure. ... Cognitive Services; Face API;"
          },
          {
            "name": "Text Analytics",
            "url": "https://azure.microsoft.com/services/cognitive-services/text-analytics/",
            "snippet": "Cognitive Services; Text Analytics API; Text Analytics API . Detect sentiment, ... you agree that Microsoft may store it and use it to improve Microsoft services, ..."
          },
          {
            "name": "Computer Vision API",
            "url": "https://azure.microsoft.com/services/cognitive-services/computer-vision/",
            "snippet": "Extract the data you need from images using optical character recognition and image analytics with Computer Vision APIs from Microsoft Azure."
          },
          {
            "name": "Emotion",
            "url": "https://www.microsoft.com/cognitive-services/en-us/emotion-api",
            "snippet": "Cognitive Services Emotion API - microsoft.com"
          },
          {
            "name": "Bing Speech API",
            "url": "https://azure.microsoft.com/services/cognitive-services/speech/",
            "snippet": "Add speech recognition to your applications, including text to speech, with a speech API from Microsoft Azure. ... Cognitive Services; Bing Speech API;"
          },
          {
            "name": "Get Started for Free",
            "url": "https://azure.microsoft.com/services/cognitive-services/",
            "snippet": "Add vision, speech, language, and knowledge capabilities to your applications using intelligence APIs and SDKs from Cognitive Services."
          }
        ]
      }
    ]
  },
  "relatedSearches": {
    "id": "https://api.cognitive.microsoft.com/api/v7/#RelatedSearches",
    "value": [
      {
        "text": "microsoft bot framework",
        "displayText": "microsoft bot framework",
        "webSearchUrl": "https://www.bing.com/search?q=microsoft+bot+framework"
      },
      {
        "text": "microsoft cognitive services youtube",
        "displayText": "microsoft cognitive services youtube",
        "webSearchUrl": "https://www.bing.com/search?q=microsoft+cognitive+services+youtube"
      },
      {
        "text": "microsoft cognitive services search api",
        "displayText": "microsoft cognitive services search api",
        "webSearchUrl": "https://www.bing.com/search?q=microsoft+cognitive+services+search+api"
      },
      {
        "text": "microsoft cognitive services news",
        "displayText": "microsoft cognitive services news",
        "webSearchUrl": "https://www.bing.com/search?q=microsoft+cognitive+services+news"
      },
      {
        "text": "ms cognitive service",
        "displayText": "ms cognitive service",
        "webSearchUrl": "https://www.bing.com/search?q=ms+cognitive+service"
      },
      {
        "text": "microsoft cognitive services text analytics",
        "displayText": "microsoft cognitive services text analytics",
        "webSearchUrl": "https://www.bing.com/search?q=microsoft+cognitive+services+text+analytics"
      },
      {
        "text": "microsoft cognitive services toolkit",
        "displayText": "microsoft cognitive services toolkit",
        "webSearchUrl": "https://www.bing.com/search?q=microsoft+cognitive+services+toolkit"
      },
      {
        "text": "microsoft cognitive services api",
        "displayText": "microsoft cognitive services api",
        "webSearchUrl": "https://www.bing.com/search?q=microsoft+cognitive+services+api"
      }
    ]
  },
  "rankingResponse": {
    "mainline": {
      "items": [
        {
          "answerType": "WebPages",
          "resultIndex": 0,
          "value": {
            "id": "https://api.cognitive.microsoft.com/api/v7/#WebPages.0"
          }
        }
      ]
    },
    "sidebar": {
      "items": [
        {
          "answerType": "RelatedSearches",
          "value": {
            "id": "https://api.cognitive.microsoft.com/api/v7/#RelatedSearches"
          }
        }
      ]
    }
  }
}
```

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Bing webes keresés alkalmazás oktatóanyag](../tutorial-bing-web-search-single-page-app.md)

## <a name="see-also"></a>Lásd még 

[Bing webes keresés – áttekintés](../overview.md)  
[Próbálja ki](https://azure.microsoft.com/services/cognitive-services/bing-web-search-api/)  
[Egy ingyenes próba hozzáférési kulcs beszerzése](https://azure.microsoft.com/try/cognitive-services/?api=bing-web-search-api)  
[Bing webes keresési API-referencia](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference)
