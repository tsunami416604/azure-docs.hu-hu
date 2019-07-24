---
title: 'Gyors útmutató: Az Bing Custom Search-végpont meghívása a használatával C# | Microsoft Docs'
titleSuffix: Azure Cognitive Services
description: Ezzel a rövid útmutatóval megkezdheti a keresési eredmények kérését C#a Bing Custom Search-példányában.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-custom-search
ms.topic: quickstart
ms.date: 07/15/2019
ms.author: maheshb
ms.openlocfilehash: c1b118ec08e00705ad3eab9141d116fb9e6ef257
ms.sourcegitcommit: 9dc7517db9c5817a3acd52d789547f2e3efff848
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/23/2019
ms.locfileid: "68405195"
---
# <a name="quickstart-call-your-bing-custom-search-endpoint-using-c"></a>Gyors útmutató: Bing Custom Search végpont meghívása a használatávalC# 

Ezzel a rövid útmutatóval megkezdheti a Bing Custom Search-példány keresési eredményeinek kérelmezését. Az alkalmazás beírása C#közben a BING Custom Search API egy REST-alapú webszolgáltatás, amely kompatibilis a legtöbb programozási nyelvvel. A minta forráskódja a [githubon](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/dotnet/Search/BingCustomSearchv7.cs)található.

## <a name="prerequisites"></a>Előfeltételek

- Egy Bing Custom Search példány. Lásd [: gyors útmutató: További információért hozza létre az](quick-start.md) első Bing Custom Search-példányát.
- Microsoft [.net Core](https://www.microsoft.com/net/download/core)
- A [Visual Studio 2019-es vagy újabb](https://www.visualstudio.com/downloads/) verziójának bármely kiadása
- Linux/MacOS rendszer esetében az alkalmazás a [Monóval](https://www.mono-project.com/) futtatható.
- A [Bing Custom Search](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Search.CustomSearch/1.2.0) NuGet-csomag. 
    - **Megoldáskezelő** a Visual Studióban kattintson a jobb gombbal a projektre, és válassza a **NuGet-csomagok kezelése** menüpontot a menüből. Telepítse az `Microsoft.Azure.CognitiveServices.Search.CustomSearch` csomagot. A NuGet Custom Search csomag telepítésekor a következő szerelvények is települnek:
        - Microsoft.Rest.ClientRuntime
        - Microsoft.Rest.ClientRuntime.Azure
        - Newtonsoft.Json

[!INCLUDE [cognitive-services-bing-custom-search-prerequisites](../../../includes/cognitive-services-bing-custom-search-signup-requirements.md)]

## <a name="create-and-initialize-the-application"></a>Az alkalmazás létrehozása és inicializálása

1. Hozzon létre C# egy új Console-alkalmazást a Visual Studióban. Ez után adja hozzá a projekthez az alábbi csomagokat.

    ```csharp
    using System;
    using System.Net.Http;
    using System.Web;
    using Newtonsoft.Json;
    ```

2. Hozza létre a következő osztályokat a Bing Custom Search API által visszaadott keresési eredmények tárolásához.

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

3. A projekt fő metódusában hozzon létre változókat a Bing Custom Search API előfizetési kulcs, a keresési példány egyéni konfigurációs azonosítója és egy keresési kifejezés alapján.

    ```csharp
    var subscriptionKey = "YOUR-SUBSCRIPTION-KEY";
    var customConfigId = "YOUR-CUSTOM-CONFIG-ID";
    var searchTerm = args.Length > 0 ? args[0]:"microsoft";
    ```

4. Hozza létre a kérelem URL-címét úgy, hogy hozzáfűzi `q=` a keresési kifejezést a lekérdezési paraméterhez, a keresési példány `customconfig=`egyéni konfigurációs azonosítóját pedig a következőre:. a paramétereket `&` a karakterrel válassza el. 

    ```csharp
    var url = "https://api.cognitive.microsoft.com/bingcustomsearch/v7.0/search?" +
                "q=" + searchTerm + "&" +
                "customconfig=" + customConfigId;
    ```

## <a name="send-and-receive-a-search-request"></a>Keresési kérelem küldése és fogadása 

1. Hozzon létre egy kérelem-ügyfelet, és adja hozzá `Ocp-Apim-Subscription-Key` az előfizetési kulcsot a fejléchez.

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

3. Az egyes keresési eredményekkel kapcsolatos információk megjelenítéséhez, beleértve annak nevét, URL-címét és a weblap utolsó bejárásának dátumát, ismételje meg a választ.

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
