---
title: 'Rövid útmutató: Végpont hívása a C# használatával – Bing Custom Search'
titlesuffix: Azure Cognitive Services
description: Ez a rövid útmutató bemutatja, hogyan kérhet le keresési eredményeket egyéni keresési példányokról a Bing Custom Search-végpont C#-pal való meghívásával.
services: cognitive-services
author: brapel
manager: cgronlun
ms.service: cognitive-services
ms.component: bing-custom-search
ms.topic: quickstart
ms.date: 05/07/2018
ms.author: v-brapel
ms.openlocfilehash: 1c3b1031c2d08b1f346216b54d351c99f01db933
ms.sourcegitcommit: 6f59cdc679924e7bfa53c25f820d33be242cea28
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/05/2018
ms.locfileid: "48814865"
---
# <a name="quickstart-call-bing-custom-search-endpoint-c"></a>Rövid útmutató: A Bing Custom Search-végpont meghívása (C#)

Ez a rövid útmutató bemutatja, hogyan kérhet le keresési eredményeket egyéni keresési példányokról a Bing Custom Search-végpont C#-pal való meghívásával. 

## <a name="prerequisites"></a>Előfeltételek

A rövid útmutató elvégzéséhez a következőkre lesz szüksége:

- Egy használatra kész egyéni keresési példány. Lásd: [Az első Bing Custom Search-példány létrehozása](quick-start.md).
- Telepített [.NET Core](https://www.microsoft.com/net/download/core).
- Egy előfizetői azonosító. Használhatja az [ingyenes próbaverzió](https://azure.microsoft.com/try/cognitive-services/?api=bing-custom-search) aktiválásakor kapott előfizetői azonosítót, vagy egy fizetős előfizetői azonosítót az Azure-irányítópultról (lásd: [Cognitive Services API-fiók](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account)).    


## <a name="run-the-code"></a>A kód futtatása

A minta futtatásához kövesse az alábbi lépéseket:

1. Hozzon létre egy mappát a kód számára.  
  
2. Egy parancssorban vagy terminálablakban lépjen a létrehozott mappába.  
  
3. Futtassa az alábbi parancsot:
    ```
    dotnet new console -o BingCustomSearch
    cd BingCustomSearch
    dotnet add package Newtonsoft.Json
    dotnet restore
    ```
  
4. Másolja az alábbi kódot a Program.cs fájlba. A **YOUR-SUBSCRIPTION-KEY** (előfizetői azonosító) és a **YOUR-CUSTOM-CONFIG-ID** (konfigurációs azonosító) helyére írja be saját adatait.

    ```csharp
    using System;
    using System.Net.Http;
    using System.Web;
    using Newtonsoft.Json;
    
    namespace bing_custom_search_example_dotnet
    {
        class Program
        {
            static void Main(string[] args)
            {
                var subscriptionKey = "YOUR-SUBSCRIPTION-KEY";
                var customConfigId = "YOUR-CUSTOM-CONFIG-ID";
                var searchTerm = args.Length > 0 ? args[0]: "microsoft";            
    
                var url = "https://api.cognitive.microsoft.com/bingcustomsearch/v7.0/search?" +
                    "q=" + searchTerm +
                    "&customconfig=" + customConfigId;
    
                var client = new HttpClient();
                client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", subscriptionKey);
                var httpResponseMessage = client.GetAsync(url).Result;
                var responseContent = httpResponseMessage.Content.ReadAsStringAsync().Result;
                BingCustomSearchResponse response = JsonConvert.DeserializeObject<BingCustomSearchResponse>(responseContent);
                
                for(int i = 0; i < response.webPages.value.Length; i++)
                {                
                    var webPage = response.webPages.value[i];
                    
                    Console.WriteLine("name: " + webPage.name);
                    Console.WriteLine("url: " + webPage.url);                
                    Console.WriteLine("displayUrl: " + webPage.displayUrl);
                    Console.WriteLine("snippet: " + webPage.snippet);
                    Console.WriteLine("dateLastCrawled: " + webPage.dateLastCrawled);
                    Console.WriteLine();
                }            
            }
        }
    
        public class BingCustomSearchResponse
        {        
            public string _type{ get; set; }            
            public WebPages webPages { get; set; }
        }
    
        public class WebPages
        {
            public string webSearchUrl { get; set; }
            public int totalEstimatedMatches { get; set; }
            public WebPage[] value { get; set; }        
        }
    
        public class WebPage
        {
            public string name { get; set; }
            public string url { get; set; }
            public string displayUrl { get; set; }
            public string snippet { get; set; }
            public DateTime dateLastCrawled { get; set; }
            public string cachedPageUrl { get; set; }
            public OpenGraphImage openGraphImage { get; set; }        
        }
        
        public class OpenGraphImage
        {
            public string contentUrl { get; set; }
            public int width { get; set; }
            public int height { get; set; }
        }
    }
    ```
6. Hozza létre az alkalmazást a következő paranccsal. Jegyezze fel a parancs kimenetében hivatkozott DLL elérési utat.

    <pre>
    dotnet build 
    </pre>
    
7. Futtassa az alkalmazást a következő paranccsal, a **PATH TO OUTPUT** helyére a 6. lépésben kapott DLL elérési utat behelyettesítve.

    <pre>    
    dotnet **PATH TO OUTPUT**
    </pre>

## <a name="next-steps"></a>További lépések
- [Az üzemeltetett felhasználói felületi élmény konfigurálása](./hosted-ui.md)
- [Díszítő megjelölések használata szövegkiemeléshez](./hit-highlighting.md)
- [Webhelyek lapozása](./page-webpages.md)
