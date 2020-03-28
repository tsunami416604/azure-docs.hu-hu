---
title: 'Rövid útmutató: Keresési kérelem küldése a REST API-nak a Ruby – Bing entity search használatával'
titleSuffix: Azure Cognitive Services
description: Ezzel a rövid útmutatóval kérést küldhet a Bing Entity Search REST API-nak a Ruby használatával, és JSON-választ kaphat.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-entity-search
ms.topic: quickstart
ms.date: 12/11/2019
ms.author: aahi
ms.openlocfilehash: 69e4d992e2ef89b4d3d9408d6e50591fb8166c79
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75385779"
---
# <a name="quickstart-for-bing-entity-search-api-with-ruby"></a>Rövid útmutató a Bing Entity Search API és a Ruby használatához

Ezzel a rövid útmutatóval első ként hívhatja meg a Bing Entity Search API-t, és megtekintheti a JSON-választ. Ez az egyszerű Ruby alkalmazás hírkeresési lekérdezést küld az API-nak, és megjeleníti a választ. Az alkalmazás forráskódja elérhető a [GitHubon.](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/ruby/Search/BingEntitySearchv7.rb)

Bár ez az alkalmazás Ruby nyelven lett íródott, az API egy RESTful-webszolgáltatás, azaz kompatibilis a legtöbb programnyelvvel.

## <a name="prerequisites"></a>Előfeltételek

* [Ruby 2.4](https://www.ruby-lang.org/en/downloads/) vagy újabb.

[!INCLUDE [cognitive-services-bing-news-search-signup-requirements](../../../../includes/cognitive-services-bing-entity-search-signup-requirements.md)]

## <a name="create-and-initialize-the-application"></a>Az alkalmazás létrehozása és inicializálása

1. A kedvenc IDE vagy kódszerkesztő, hozzon létre egy hírekEt Ruby fájlt, és importálja a következő csomagokat.

    ```ruby
    require 'net/https'
    require 'cgi'
    require 'json'
    ```

2. Hozzon létre változókat az API-végponthoz, a hírek keresési URL-címéhez, az előfizetési kulcshoz és egy keresési lekérdezéshez. Használhatja az alábbi globális végpontot, vagy az [egyéni altartomány-végpontot,](../../../cognitive-services/cognitive-services-custom-subdomains.md) amely az azure-portálon jelenik meg az erőforráshoz.
    
    ```ruby
    host = 'https://api.cognitive.microsoft.com'
    path = '/bing/v7.0/entities'
    
    mkt = 'en-US'
    query = 'italian restaurants near me'
    ```

## <a name="format-and-make-an-api-request"></a>Formázás és API-kérelem létrehozása

1. Hozza létre a paraméterek karakterláncát a kérelemhez `?mkt=` a piaci változó nak a paraméterhez való hozzáfűzésével. Kódolja a lekérdezést, és `&q=` fűzze hozzá a paraméterhez. Egyesítse az API-állomást, az elérési utat és a kérés paramétereit, és adja őket URI-objektumként.

    ```ruby
    params = '?mkt=' + mkt + '&q=' + CGI.escape(query)
    uri = URI (host + path + params)
    ```

2. Használja a változók az utolsó lépésben a kérelem létrehozásához. Adja hozzá az `Ocp-Apim-Subscription-Key` előfizetési kulcsot a fejléchez.

    ```ruby
    request = Net::HTTP::Get.new(uri)
    request['Ocp-Apim-Subscription-Key'] = subscriptionKey
    ```

3. A kérés elküldése és a válasz nyomtatása

    ```ruby
    response = Net::HTTP.start(uri.host, uri.port, :use_ssl => uri.scheme == 'https') do |http|
        http.request (request)
    end

    puts JSON::pretty_generate (JSON (response.body))
    ```

## <a name="example-json-response"></a>Példa JSON-válaszra

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
        "url": "https://www.princi.com/",
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
> [Egyoldalas webalkalmazás készítése](../tutorial-bing-entities-search-single-page-app.md)

* [Mi a Bing Entity Search API](../search-the-web.md)
* [Bing entitáskeresési API– referencia](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-entities-api-v7-reference)
