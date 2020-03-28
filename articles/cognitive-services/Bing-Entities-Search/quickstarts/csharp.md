---
title: 'Rövid útmutató: Keresési kérelem küldése a REST API-nak c# használatával – Bing entitáskeresés'
titleSuffix: Azure Cognitive Services
description: Ezzel a rövid útmutatóval kérést küldhet a Bing Entity Search REST API-nak C# használatával, és JSON-választ kaphat.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-entity-search
ms.topic: quickstart
ms.date: 12/11/2019
ms.author: aahi
ms.openlocfilehash: c343c160f67eda2dd390ffc39f3b4f1ff49cacb6
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75448668"
---
# <a name="quickstart-send-a-search-request-to-the-bing-entity-search-rest-api-using-c"></a>Rövid útmutató: Keresési kérelem küldése a Bing Entity Search REST API-nak a C használatával #

Ezzel a rövid útmutatóval első ként hívhatja meg a Bing Entity Search API-t, és megtekintheti a JSON-választ. Ez az egyszerű C# alkalmazás hírkeresési lekérdezést küld az API-nak, és megjeleníti a választ. Az alkalmazás forráskódja elérhető a [GitHubon.](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/dotnet/Search/BingEntitySearchv7.cs)

Bár ez az alkalmazás C# nyelven lett íródott, az API egy RESTful-webszolgáltatás, azaz kompatibilis a legtöbb programnyelvvel.


## <a name="prerequisites"></a>Előfeltételek

- A [Visual Studio 2017-es vagy újabb verzióinak](https://www.visualstudio.com/downloads/)bármely kiadása.

- A [Json.NET](https://www.newtonsoft.com/json) keretrendszer, amely NuGet-csomagként letölthető. A NuGet csomag telepítése a Visual Studio-ban:

   1. Kattintson a jobb gombbal a projektre a **Megoldáskezelőben.**
   2. Válassza **a NuGet-csomagok kezelése lehetőséget.**
   3. Keresse meg *a Newtonsoft.Json-t,* és telepítse a csomagot.

- Ha Linux/MacOS-t használ, ez az alkalmazás a [Mono](https://www.mono-project.com/)használatával futtatható.


[!INCLUDE [cognitive-services-bing-news-search-signup-requirements](../../../../includes/cognitive-services-bing-entity-search-signup-requirements.md)]

## <a name="create-and-initialize-a-project"></a>Projekt létrehozása és inicializálása

1. hozzon létre egy új C# konzolos megoldást a Visual Studióban. Ezután adja hozzá a következő névtereket a fő kódfájlhoz.
    
    ```csharp
    using Newtonsoft.Json;
    using System;
    using System.Net.Http;
    using System.Text;
    ```

2. Hozzon létre egy új osztályt, és adjon hozzá változókat az API-végponthoz, az előfizetési kulcshoz és a keresett lekérdezéshez. Használhatja az alábbi globális végpontot, vagy az [egyéni altartomány-végpontot,](../../../cognitive-services/cognitive-services-custom-subdomains.md) amely az azure-portálon jelenik meg az erőforráshoz.

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

## <a name="send-a-request-and-get-the-api-response"></a>Kérelem küldése és az API-válasz bekérése

1. Az osztályon belül hozzon `Search()`létre egy függvényt, amelynek neve . Hozzon `HttpClient` létre egy új objektumot, `Ocp-Apim-Subscription-Key` és adja hozzá az előfizetési kulcsot a fejléchez.

   1. Az állomás és az elérési út kombinálásával hozhatja létre a kérelem URI-ját. Ezután adja hozzá a piac, és URL-kódolja a lekérdezést.
   2. Várja, `client.GetAsync()` hogy http-választ kapjon, majd tárolja a `ReadAsStringAsync()`json választ a várakozással.
   3. Formázza a JSON-karakterláncot, `JsonConvert.DeserializeObject()` és nyomtassa ki a konzolra.

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

2. Az alkalmazás fő metódusában hívja `Search()` meg a függvényt.
    
    ```csharp
    static void Main(string[] args)
    {
        Search();
        Console.ReadLine();
    }
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

* [Mi a Bing Entity Search API?](../overview.md )
* [Bing entitáskeresési API– referencia](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-entities-api-v7-reference)
