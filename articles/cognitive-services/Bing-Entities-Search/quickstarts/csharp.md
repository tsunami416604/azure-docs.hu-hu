---
title: 'Gyors útmutató: keresési kérelem küldése a REST API C#-Bing Entity Search használatával'
titleSuffix: Azure Cognitive Services
description: Ezzel a rövid útmutatóval kérést küldhet a Bing Entity Search REST API C# használatával, és JSON-választ kap.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-entity-search
ms.topic: quickstart
ms.date: 10/19/2020
ms.author: aahi
ms.custom: devx-track-csharp
ms.openlocfilehash: a0a77faba971f328c6ae1c5a03f8faf7ccfcf60d
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/30/2020
ms.locfileid: "96351486"
---
# <a name="quickstart-send-a-search-request-to-the-bing-entity-search-rest-api-using-c"></a>Gyors útmutató: keresési kérelem küldése a Bing Entity Search REST API C használatával #

> [!WARNING]
> Bing Search API-k átkerülnek a Cognitive Servicesról Bing Search szolgáltatásokra. **2020. október 30-ig** a Bing Search új példányait az [itt](/bing/search-apis/bing-web-search/create-bing-search-service-resource)ismertetett eljárás követésével kell kiépíteni.
> A Cognitive Services használatával kiépített Bing Search API-k a következő három évben vagy a Nagyvállalati Szerződés végéig lesz támogatva, attól függően, hogy melyik történik először.
> Az áttelepítési utasításokért lásd: [Bing Search Services](/bing/search-apis/bing-web-search/create-bing-search-service-resource).

Ezzel a rövid útmutatóval elvégezheti az első hívását a Bing Entity Search API, és megtekintheti a JSON-választ. Ez az egyszerű C#-alkalmazás egy Hírek keresési lekérdezést küld az API-nak, és megjeleníti a választ. Az alkalmazás forráskódja elérhető a [githubon](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/dotnet/Search/BingEntitySearchv7.cs).

Bár ez az alkalmazás C# nyelven íródott, az API egy REST-alapú webszolgáltatás, amely kompatibilis a legtöbb programozási nyelvvel.


## <a name="prerequisites"></a>Előfeltételek

- A [Visual Studio 2017 vagy újabb](https://www.visualstudio.com/downloads/)verziójának bármely kiadása.
- Ha Linux vagy MacOS rendszert használ, a [Visual Studio Code](https://code.visualstudio.com/) és a [.net Core](/dotnet/core/install/macos) használatával is követheti ezt a rövid útmutatót
- [Ingyenes Azure-fiók](https://azure.microsoft.com/free/dotnet)


[!INCLUDE [cognitive-services-bing-news-search-signup-requirements](../../../../includes/cognitive-services-bing-entity-search-signup-requirements.md)]

## <a name="create-and-initialize-a-project"></a>Projekt létrehozása és inicializálása

1. Hozzon létre egy új C# konzolos megoldást a Visual Studióban. 
1. Adja hozzá a [Newtonsoft.Jst a](https://www.nuget.org/packages/Newtonsoft.Json/) NuGet-csomaghoz.
    1. Kattintson a jobb gombbal a projektre **megoldáskezelő**.
    2. Válassza a **NuGet-csomagok kezelése** lehetőséget.
    3. Keresse meg és válassza ki a *Newtonsoft.Js*, majd telepítse a csomagot.
1. Ezután adja hozzá a következő névtereket a fő kódhoz:
    
    ```csharp
    using Newtonsoft.Json;
    using System;
    using System.Net.Http;
    using System.Text;
    ```

2. Hozzon létre egy új osztályt, és adjon hozzá változókat az API-végponthoz, az előfizetési kulcshoz és a keresett lekérdezéshez. Használhatja a globális végpontot a következő kódban, vagy használhatja az erőforráshoz tartozó Azure Portalban megjelenő [Egyéni altartomány](../../../cognitive-services/cognitive-services-custom-subdomains.md) -végpontot.

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

## <a name="send-a-request-and-get-the-api-response"></a>Kérelem küldése és az API-válasz beszerzése

1. Hozzon létre egy nevű függvényt a osztályon belül `Search()` . A függvényen belül hozzon létre egy új `HttpClient` objektumot, és adja hozzá az előfizetési kulcsot a `Ocp-Apim-Subscription-Key` fejléchez.

2. Hozza létre a kérelem URI-JÁT a gazdagép és az elérési út kombinálásával. Ezután adja hozzá a piacot és az URL-címet – kódolja a lekérdezést.

3. Várja `client.GetAsync()` meg, amíg http-választ kap, majd a várakozással tárolja a JSON-választ `ReadAsStringAsync()` .

4. Formázza a JSON-karakterláncot, `JsonConvert.DeserializeObject()` és nyomtassa ki a konzolra.

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

5. Az `Main()` alkalmazás metódusában hívja meg a `Search()` függvényt.
    
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
* [Bing Entity Search API hivatkozás](/rest/api/cognitiveservices-bingsearch/bing-entities-api-v7-reference).