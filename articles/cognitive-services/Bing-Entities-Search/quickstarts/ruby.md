---
title: 'Gyors útmutató: Egy keresési kérelmet küld a Bing Entity Search REST API Ruby használatával'
titlesuffix: Azure Cognitive Services
description: Ez a rövid útmutató segítségével egy kérelmet küld a Bing Entity Search REST API Ruby használatával, és a egy JSON-választ kap.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-entity-search
ms.topic: quickstart
ms.date: 02/01/2019
ms.author: aahi
ms.openlocfilehash: 44dad6d0a6f11b84c4cd2a4470e9a286e1a9ea36
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/07/2019
ms.locfileid: "55866083"
---
# <a name="quickstart-for-bing-entity-search-api-with-ruby"></a>Rövid útmutató a Bing Entity Search API és a Ruby használatához

Ez a rövid útmutató segítségével a Bing Entity Search API az első hívását, és tekintse meg a JSON-választ. Az egyszerű Ruby-alkalmazás news search lekérdezést küld az API-t, és a válasz megjeleníti. Az alkalmazás forráskódja elérhető a [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/ruby/Search/BingEntitySearchv7.rb).

Bár ez az alkalmazás Ruby nyelven lett íródott, az API egy RESTful-webszolgáltatás, azaz kompatibilis a legtöbb programnyelvvel.

## <a name="prerequisites"></a>Előfeltételek

* [Ruby 2.4](https://www.ruby-lang.org/en/downloads/) vagy újabb.

[!INCLUDE [cognitive-services-bing-news-search-signup-requirements](../../../../includes/cognitive-services-bing-entity-search-signup-requirements.md)]

## <a name="create-and-initialize-the-application"></a>Az alkalmazás létrehozása és inicializálása

1. A kedvenc IDE vagy Kódszerkesztő hozzon létre egy news Ruby fájlt, és a következő csomagok importálásához.

    ```ruby
    require 'net/https'
    require 'cgi'
    require 'json'
    ```

2. Hozzon létre változókat az API-végpont, News search URL-cím, az előfizetési kulcs és a egy keresési lekérdezést.
    
    ```ruby
    host = 'https://api.cognitive.microsoft.com'
    path = '/bing/v7.0/entities'
    
    mkt = 'en-US'
    query = 'italian restaurants near me'
    ```

## <a name="format-and-make-an-api-request"></a>Formázás és API-kérelem létrehozása

1. Hozzon létre a kérelem paraméter karakterlánca a piaci változót, amelyeket a `?mkt=` paraméter. A lekérdezés kódolása, és fűzze hozzá a az `&q=` paraméter. Az API-gazdát, elérési út és a kérelem paramétereit, és -URI-objektumként leadott őket.

    ```ruby
    params = '?mkt=' + mkt + '&q=' + CGI.escape(query)
    uri = URI (host + path + params)
    ```

2. A kérelem létrehozásához használja az utolsó lépésben a változókat. Az előfizetési kulcs hozzáadása a `Ocp-Apim-Subscription-Key` fejléc.

    ```ruby
    request = Net::HTTP::Get.new(uri)
    request['Ocp-Apim-Subscription-Key'] = subscriptionKey
    ```

3. A kérelem elküldéséhez és a resposne nyomtatás

    ```ruby
    response = Net::HTTP.start(uri.host, uri.port, :use_ssl => uri.scheme == 'https') do |http|
        http.request (request)
    end

    puts JSON::pretty_generate (JSON (response.body))
    ```

## <a name="example-json-response"></a>Példa JSON-válasz

A rendszer JSON formátumban ad vissza egy sikeres választ a következő példában látható módon: 

```json
{
  "_type": "SearchResponse",
  "queryContext": {
    "originalQuery": "italian restaurant near me",
    "askUserForLocation": true
  },
  "places": {
    "value": [
      {
        "_type": "LocalBusiness",
        "webSearchUrl": "https://www.bing.com/search?q=sinful+bakery&filters=local...",
        "name": "Liberty's Delightful Sinful Bakery & Cafe",
        "url": "https://www.contoso.com/",
        "entityPresentationInfo": {
          "entityScenario": "ListItem",
          "entityTypeHints": [
            "Place",
            "LocalBusiness"
          ]
        },
        "address": {
          "addressLocality": "Seattle",
          "addressRegion": "WA",
          "postalCode": "98112",
          "addressCountry": "US",
          "neighborhood": "Madison Park"
        },
        "telephone": "(800) 555-1212"
      },

      . . .
      {
        "_type": "Restaurant",
        "webSearchUrl": "https://www.bing.com/search?q=Pickles+and+Preserves...",
        "name": "Munson's Pickles and Preserves Farm",
        "url": "http://www.princi.com/",
        "entityPresentationInfo": {
          "entityScenario": "ListItem",
          "entityTypeHints": [
            "Place",
            "LocalBusiness",
            "Restaurant"
          ]
        },
        "address": {
          "addressLocality": "Seattle",
          "addressRegion": "WA",
          "postalCode": "98101",
          "addressCountry": "US",
          "neighborhood": "Capitol Hill"
        },
        "telephone": "(800) 555-1212"
      },
      
      . . .
    ]
  }
}
```

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Egyoldalas webes alkalmazás készítése](../tutorial-bing-entities-search-single-page-app.md)

* [Mi az a Bing Entity Search API](../search-the-web.md)
* [A Bing Entity Search API-referencia](https://docs.microsoft.com/rest/api/cognitiveservices/bing-entities-api-v7-reference)
