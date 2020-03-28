---
title: 'Rövid útmutató: Keresési kérelem küldése a REST API-nak a Python használatával – Bing entitáskeresés'
titleSuffix: Azure Cognitive Services
description: Ezzel a rövid útmutatóval kérést küldhet a Bing Entity Search REST API-nak a Python használatával, és json-választ kaphat.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-entity-search
ms.topic: quickstart
ms.date: 12/11/2019
ms.author: aahi
ms.openlocfilehash: 07b563308e80055d699d1cefeb3b2db71ffa4cd7
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75448614"
---
# <a name="quickstart-send-a-search-request-to-the-bing-entity-search-rest-api-using-python"></a>Rövid útmutató: Keresési kérelem küldése a Bing Entitás keresési REST API-jának a Python használatával

Ezzel a rövid útmutatóval első ként hívhatja meg a Bing Entity Search API-t, és megtekintheti a JSON-választ. Ez az egyszerű Python-alkalmazás hírkeresési lekérdezést küld az API-nak, és megjeleníti a választ. A minta forráskódja elérhető a [GitHubon.](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/python/Search/BingEntitySearchv7.py)

Bár ez az alkalmazás Python nyelven lett íródott, az API egy RESTful-webszolgáltatás, azaz kompatibilis a legtöbb programnyelvvel.

## <a name="prerequisites"></a>Előfeltételek

* [Piton](https://www.python.org/downloads/) 2.x vagy 3.x

[!INCLUDE [cognitive-services-bing-news-search-signup-requirements](../../../../includes/cognitive-services-bing-entity-search-signup-requirements.md)]

## <a name="create-and-initialize-the-application"></a>Az alkalmazás létrehozása és inicializálása

1. Hozzon létre egy új Python-fájlt a kedvenc IDE-ben vagy szerkesztőjében, és adja hozzá a következő importálást. Hozzon létre változókat az előfizetési kulcshoz, a végponthoz, a piachoz és egy keresési lekérdezéshez. Használhatja az alábbi globális végpontot, vagy az [egyéni altartomány-végpontot,](../../../cognitive-services/cognitive-services-custom-subdomains.md) amely az azure-portálon jelenik meg az erőforráshoz.

    ```python
    import http.client, urllib.parse
    import json
    
    subscriptionKey = 'ENTER YOUR KEY HERE'
    host = 'api.cognitive.microsoft.com'
    path = '/bing/v7.0/entities'
    mkt = 'en-US'
    query = 'italian restaurants near me'
    ```

2. Hozzon létre egy kérelem URL-t `?mkt=` a piaci változó nak a paraméterhez való hozzáfűzésével. Url-kódolja a lekérdezést, és `&q=` hozzáfűzi azt a paraméterhez. 
    
    ```python
    params = '?mkt=' + mkt + '&q=' + urllib.parse.quote (query)
    ```

## <a name="send-a-request-and-get-a-response"></a>Kérés küldése és válasz kérése

1. Hozzon létre `get_suggestions()`egy függvényt, amelynek neve . Ezután hajtsa végre a következő lépéseket.
   1. Adja hozzá az előfizetési `Ocp-Apim-Subscription-Key` kulcsot egy szótárhoz kulcsként.
   2. HTTPS-ügyfélobjektum létrehozására használható. `http.client.HTTPSConnection()` Kérés `GET` küldése `request()` az elérési úttal és a paraméterekkel, valamint a fejlécadatokkal.
   3. Tárolja a `getresponse()`választ a `response.read()`segítségével, és adja vissza.

      ```python
      def get_suggestions ():
       headers = {'Ocp-Apim-Subscription-Key': subscriptionKey}
       conn = http.client.HTTPSConnection (host)
       conn.request ("GET", path + params, None, headers)
       response = conn.getresponse ()
       return response.read()
      ```

2. Hívja `get_suggestions()`fel a hívást, és nyomtassa ki a json választ.

    ```python
    result = get_suggestions ()
    print (json.dumps(json.loads(result), indent=4))
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
