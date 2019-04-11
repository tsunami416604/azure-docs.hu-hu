---
title: 'Gyors útmutató: Egy keresési kérelmet küld a Bing Entity Search REST API használatávalC#'
titlesuffix: Azure Cognitive Services
description: Ez a rövid útmutató segítségével egy kérelmet küld a Bing Entity Search REST API használatával C#, és a egy JSON-választ kapnak.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-entity-search
ms.topic: quickstart
ms.date: 03/12/2019
ms.author: aahi
ms.openlocfilehash: d31be245d906ba0405a44d4482272051982c943c
ms.sourcegitcommit: 6e32f493eb32f93f71d425497752e84763070fad
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/10/2019
ms.locfileid: "59469148"
---
# <a name="quickstart-send-a-search-request-to-the-bing-entity-search-rest-api-using-c"></a>Gyors útmutató: Egy keresési kérelmet küld a Bing Entity Search REST API használatávalC#

Ez a rövid útmutató segítségével a Bing Entity Search API az első hívását, és tekintse meg a JSON-választ. Ez egyszerű C# alkalmazás news search lekérdezést küld az API-t, és megjeleníti a választ. Az alkalmazás forráskódja elérhető a [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/dotnet/Search/BingEntitySearchv7.cs).

Bár ez az alkalmazás C# nyelven lett íródott, az API egy RESTful-webszolgáltatás, azaz kompatibilis a legtöbb programnyelvvel.


## <a name="prerequisites"></a>Előfeltételek

* A [Visual Studio 2017](https://www.visualstudio.com/downloads/) bármely kiadása.
* A [Json.NET](https://www.newtonsoft.com/json) keretrendszer, amely NuGet-csomagként letölthető.
    * A NuGet-csomag telepítése a Visual studióban:
        1. A Megoldáskezelőben kattintson a jobb gombbal
        2. Kattintson a **NuGet-csomagok kezelése...**
        3. Keresse meg **newtonsoft.json** és telepítse a csomagot

* Linux/MacOS rendszer esetében az alkalmazás a [Monóval](https://www.mono-project.com/) futtatható.


[!INCLUDE [cognitive-services-bing-news-search-signup-requirements](../../../../includes/cognitive-services-bing-entity-search-signup-requirements.md)]

## <a name="create-and-initialize-a-project"></a>Projekt létrehozása és inicializálása

1. Hozzon létre egy új C# konzol megoldást a Visual Studióban. Ezután adja hozzá a következő névtereket a fő kódfájlhoz.
    
    ```csharp
    using Newtonsoft.Json;
    using System;
    using System.Net.Http;
    using System.Text;
    ```

2. Hozzon létre egy új osztályt, és adja hozzá az API-végpont, az előfizetési kulcs és a keresett lekérdezés változókat.

    ```csharp
    namespace EntitySearchSample
    {
        class Program
        {
            static string host = "https://api.cognitive.microsoft.com";
            static string path = "/bing/v7.0/entities";
    
            static string market = "en-US";
    
            // NOTE: Replace this example key with a valid subscription key.
            static string key = "ENTER YOUR KEY HERE";
    
            static string query = "italian restaurant near me";
        //...
        }
    }
    ```

## <a name="send-a-request-and-get-the-api-response"></a>Egy kérelem küldése, és az API-válasz lekérése

1. Az osztályon belül hozzon létre egy függvényt, nevű `Search()`. Hozzon létre egy új `HttpClient` objektumot, és adja hozzá az előfizetési kulcs, a `Ocp-Apim-Subscription-Key` fejléc.

   1. A gazdagép és az elérési út egyesítésével hozhatnak létre a kérés URI Azonosítóját. Majd adja hozzá a piaci, és URL-kódolása a lekérdezést.
   2. Await `client.GetAsync()` és a egy HTTP-válaszának lekérdezése majd tárolni a json-választ vár `ReadAsStringAsync()`.
   3. A JSON-karakterláncot formázni `JsonConvert.DeserializeObject()` és kinyomtathatja a konzolhoz.

      ```csharp
      async static void Search()
      {
       //...
       HttpClient client = new HttpClient();
       client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", key);

       string uri = host + path + "?mkt=" + market + "&q=" + System.Net.WebUtility.UrlEncode(query);

       HttpResponseMessage response = await client.GetAsync(uri);

       string contentString = await response.Content.ReadAsStringAsync();
       dynamic parsedJson = JsonConvert.DeserializeObject(contentString);
       Console.WriteLine(parsedJson);
      }
      ```

2. Az alkalmazás fő metódust, hívja a `Search()` függvény.
    
    ```csharp
    static void Main(string[] args)
    {
        Search();
        Console.ReadLine();
    }
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

* [Mi az a Bing Entity Search API?](../overview.md )
* [A Bing Entity Search API-referencia](https://docs.microsoft.com/rest/api/cognitiveservices/bing-entities-api-v7-reference)
