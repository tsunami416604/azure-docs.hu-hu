---
title: 'Gyors útmutató: az Bing Custom Search-végpont meghívása a C# használatával | Microsoft Docs'
titleSuffix: Azure Cognitive Services
description: Ezzel a rövid útmutatóval megkezdheti a keresési eredmények kérését a C#-beli Bing Custom Search-példányból.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-custom-search
ms.topic: quickstart
ms.date: 05/08/2020
ms.author: aahi
ms.custom: devx-track-csharp
ms.openlocfilehash: eae9565db5fd88a38343423422cfcc92a3fac33f
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/05/2020
ms.locfileid: "88936809"
---
# <a name="quickstart-call-your-bing-custom-search-endpoint-using-c"></a>Gyors útmutató: az Bing Custom Search-végpont meghívása C használatával # 

Ebből a rövid útmutatóból megtudhatja, hogyan kérheti le a Bing Custom Search-példány keresési eredményeit. Bár ez az alkalmazás C# nyelven íródott, a Bing Custom Search API egy REST-alapú webszolgáltatás, amely kompatibilis a legtöbb programozási nyelvvel. A minta forráskódja elérhető a [githubon](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/dotnet/Search/BingCustomSearchv7.cs).

## <a name="prerequisites"></a>Előfeltételek

- Egy Bing Custom Search példány. További információ: gyors útmutató [: az első Bing Custom Search példány létrehozása](quick-start.md).
- [Microsoft .net mag](https://www.microsoft.com/net/download/core).
- A [Visual Studio 2019 vagy újabb](https://www.visualstudio.com/downloads/)verziójának bármely kiadása.
- Ha Linux/MacOS rendszert használ, akkor az alkalmazás a [mono](https://www.mono-project.com/)használatával futtatható.
- A [Bing Custom Search](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Search.CustomSearch/2.0.0) NuGet-csomag. 

   A csomag telepítése a Visual Studióban: 
     1. Kattintson a jobb gombbal a projektre **megoldáskezelő**, majd válassza a **NuGet-csomagok kezelése**lehetőséget. 
     2. Keresse meg és válassza ki a *Microsoft. Azure. CognitiveServices. Search. CustomSearch*, majd telepítse a csomagot.

   A Bing Custom Search NuGet csomag telepítésekor a Visual Studio a következő csomagokat is telepíti:
     - **Microsoft.Rest.ClientRuntime**
     - **Microsoft. Rest. ClientRuntime. Azure**
     - **Newtonsoft.Json**


[!INCLUDE [cognitive-services-bing-custom-search-prerequisites](../../../includes/cognitive-services-bing-custom-search-signup-requirements.md)]

## <a name="create-and-initialize-the-application"></a>Az alkalmazás létrehozása és inicializálása

1. Hozzon létre egy új C# konzolos alkalmazást a Visual Studióban. Ezután adja hozzá a következő csomagokat a projekthez:

    ```csharp
    using System;
    using System.Net.Http;
    using System.Web;
    using Newtonsoft.Json;
    ```

2. Hozza létre a következő osztályokat a Bing Custom Search API által visszaadott keresési eredmények tárolásához:

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

3. A projekt fő metódusában hozza létre a következő változókat a Bing Custom Search API előfizetési kulcs, a keresési példány egyéni konfigurációs azonosítója és a keresési kifejezés alapján:

    ```csharp
    var subscriptionKey = "YOUR-SUBSCRIPTION-KEY";
    var customConfigId = "YOUR-CUSTOM-CONFIG-ID";
    var searchTerm = args.Length > 0 ? args[0]:"microsoft";
    ```

4. Hozza létre a kérelem URL-címét úgy, hogy hozzáfűzi a keresési kifejezést a `q=` lekérdezési paraméterhez, a keresési példány egyéni konfigurációs azonosítóját pedig a `customconfig=` paraméterhez. Válassza el a paramétereket egy jellel ( `&` ). A `url` változó értékeként használhatja a globális végpontot a következő kódban, vagy használhatja az erőforráshoz tartozó Azure Portalban megjelenő [Egyéni altartomány](../../cognitive-services/cognitive-services-custom-subdomains.md) -végpontot.

    ```csharp
    var url = "https://api.cognitive.microsoft.com/bingcustomsearch/v7.0/search?" +
                "q=" + searchTerm + "&" +
                "customconfig=" + customConfigId;
    ```

## <a name="send-and-receive-a-search-request"></a>Keresési kérelem küldése és fogadása 

1. Hozzon létre egy kérelem-ügyfelet, és adja hozzá az előfizetési kulcsot a `Ocp-Apim-Subscription-Key` fejléchez.

    ```csharp
    var client = new HttpClient();
    client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", subscriptionKey);
    ```

2. Hajtsa végre a keresési kérelmet, és kérje le a választ JSON-objektumként.

    ```csharp
    var httpResponseMessage = client.GetAsync(url).Result;
    var responseContent = httpResponseMessage.Content.ReadAsStringAsync().Result;
    BingCustomSearchResponse response = JsonConvert.DeserializeObject<BingCustomSearchResponse>(responseContent);
    ```
## <a name="process-and-view-the-results"></a>Dolgozza fel és tekintse meg az eredményeket

- Az egyes keresési eredményekkel kapcsolatos információk megjelenítéséhez, beleértve annak nevét, URL-címét és a weblap utolsó bejárásának dátumát, ismételje meg a választ.

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
> [Egyéni keresési Webalkalmazás létrehozása](./tutorials/custom-search-web-page.md)
