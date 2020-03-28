---
title: 'Rövid útmutató: A Bing egyéni keresési végpontjának hívása C# használatával | Microsoft dokumentumok'
titleSuffix: Azure Cognitive Services
description: Ezzel a rövid útmutatóval megkezdheti a keresési eredmények keresését a Bing egyéni keresési példányától c#-ban.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-custom-search
ms.topic: quickstart
ms.date: 03/24/2020
ms.author: aahi
ms.openlocfilehash: 7ea8b272871e681bd9caacf8cf1a84eb91d8849d
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "80238911"
---
# <a name="quickstart-call-your-bing-custom-search-endpoint-using-c"></a>Rövid útmutató: A Bing egyéni keresési végpontjának hívása c használatával # 

Ezzel a rövid útmutatóval megkezdheti a keresési eredmények keresését a Bing egyéni keresési példányából. Bár ez az alkalmazás C#-ban íródott, a Bing Egyéni keresési API egy RESTful webszolgáltatás, amely a legtöbb programozási nyelvvel kompatibilis. A minta forráskódja megtalálható a [GitHubon.](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/dotnet/Search/BingCustomSearchv7.cs)

## <a name="prerequisites"></a>Előfeltételek

- Bing egyéni keresési példánya. További információ: További információ [az első Bing egyéni keresési példány létrehozása](quick-start.md) című témakörben.
- Microsoft [.NET Core](https://www.microsoft.com/net/download/core)
- A [Visual Studio 2019-es vagy újabb](https://www.visualstudio.com/downloads/) verzióinak bármely kiadása
- Linux/MacOS rendszer esetében az alkalmazás a [Monóval](https://www.mono-project.com/) futtatható.
- A [Bing egyéni keresés](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Search.CustomSearch/1.2.0) Nuget csomag. 
    - A Visual Studio **Megoldáskezelőjében** kattintson a jobb gombbal a projektre, és válassza a menü **NuGet csomagok kezelése parancsát.** Telepítse az `Microsoft.Azure.CognitiveServices.Search.CustomSearch` csomagot. A NuGet Custom Search csomag telepítésekor a következő szerelvények is települnek:
        - Microsoft.Rest.ClientRuntime
        - Microsoft.Rest.ClientRuntime.Azure
        - Newtonsoft.Json

[!INCLUDE [cognitive-services-bing-custom-search-prerequisites](../../../includes/cognitive-services-bing-custom-search-signup-requirements.md)]

## <a name="create-and-initialize-the-application"></a>Az alkalmazás létrehozása és inicializálása

1. Hozzon létre egy új C# konzolalkalmazást a Visual Studióban. Ez után adja hozzá a projekthez az alábbi csomagokat.

    ```csharp
    using System;
    using System.Net.Http;
    using System.Web;
    using Newtonsoft.Json;
    ```

2. Hozza létre a következő osztályokat a Bing egyéni keresési API által visszaadott keresési eredmények tárolásához.

    ```csharp
    public class BingCustomSearchResponse {        
        public string _type{ get; set; }            
        public WebPages webPages { get; set; }
    }

    public class WebPages {
        public string webSearchUrl { get; set; }
        public int totalEstimatedMatches { get; set; }
        public WebPage[] value { get; set; }        
    }

    public class WebPage {
        public string name { get; set; }
        public string url { get; set; }
        public string displayUrl { get; set; }
        public string snippet { get; set; }
        public DateTime dateLastCrawled { get; set; }
        public string cachedPageUrl { get; set; }
    }
    ```

3. A projekt fő módszerében hozzon létre változókat a Bing Egyéni Keresési API-előfizetési kulcsához, a keresési példány egyéni konfigurációs azonosítójához és egy keresési kifejezéshez.

    ```csharp
    var subscriptionKey = "YOUR-SUBSCRIPTION-KEY";
    var customConfigId = "YOUR-CUSTOM-CONFIG-ID";
    var searchTerm = args.Length > 0 ? args[0]:"microsoft";
    ```

4. A kérelem URL-címét úgy hozhatja `q=` létre, hogy hozzáfűzi a keresési `customconfig=`kifejezést a lekérdezési paraméterhez, és a keresési példány egyéni konfigurációazonosítóját a hoz. a paramétereket egy `&` karakterrel válassza el. `url`lehet az alábbi globális végpont, vagy az [egyéni altartomány](../../cognitive-services/cognitive-services-custom-subdomains.md) végpont jatthatja az erőforrás azure portalján.

    ```csharp
    var url = "https://api.cognitive.microsoft.com/bingcustomsearch/v7.0/search?" +
                "q=" + searchTerm + "&" +
                "customconfig=" + customConfigId;
    ```

## <a name="send-and-receive-a-search-request"></a>Keresési kérelem küldése és fogadása 

1. Hozzon létre egy kérelem-ügyfél, `Ocp-Apim-Subscription-Key` és adja hozzá az előfizetési kulcsot a fejléchez.

    ```csharp
    var client = new HttpClient();
    client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", subscriptionKey);
    ```

2. Hajtsa végre a keresési kérelmet, és a válasz json-objektumként.

    ```csharp
    var httpResponseMessage = client.GetAsync(url).Result;
    var responseContent = httpResponseMessage.Content.ReadAsStringAsync().Result;
    BingCustomSearchResponse response = JsonConvert.DeserializeObject<BingCustomSearchResponse>(responseContent);
    ```
   ## <a name="process-and-view-the-results"></a>Dolgozza fel és tekintse meg az eredményeket

3. A válaszobjektum on-rése az egyes keresési eredményekkel kapcsolatos információk megjelenítéséhez, beleértve a nevét, az URL-címét és a weblap legutóbbi feltérképezésének dátumát.

    ```csharp
    for(int i = 0; i < response.webPages.value.Length; i++) {                
        var webPage = response.webPages.value[i];
        
        Console.WriteLine("name: " + webPage.name);
        Console.WriteLine("url: " + webPage.url);                
        Console.WriteLine("displayUrl: " + webPage.displayUrl);
        Console.WriteLine("snippet: " + webPage.snippet);
        Console.WriteLine("dateLastCrawled: " + webPage.dateLastCrawled);
        Console.WriteLine();
    }
    Console.WriteLine("Press any key to exit...");
    Console.ReadKey();
    ```

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Egyéni keresési webalkalmazás létrehozása](./tutorials/custom-search-web-page.md)
