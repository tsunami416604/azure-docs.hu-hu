---
title: Végpont meghívása a C# – Bing egyéni keresés – Microsoft Cognitive Services használatával
description: Ez a rövid útmutató bemutatja, hogyan használatával C#, a Bing Custom Search-végpont meghívása az egyéni keresőpéldányok keresési eredmények kérhet.
services: cognitive-services
author: brapel
manager: ehansen
ms.service: cognitive-services
ms.component: bing-custom-search
ms.topic: article
ms.date: 05/07/2018
ms.author: v-brapel
ms.openlocfilehash: ed00b75fa956d0197d3672d84b097f99ec3c35ec
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/24/2018
ms.locfileid: "46956385"
---
# <a name="call-bing-custom-search-endpoint-c"></a>Hívást a Bing Custom Search végpont (C#)

Ez a rövid útmutató bemutatja, hogyan kérhet a keresési eredmények a C# használatával hívja meg a Bing Custom Search-végpont egyéni keresési példány. 

## <a name="prerequisites"></a>Előfeltételek

A rövid útmutató elvégzéséhez a következőkre lesz szüksége:

- Egy használatra kész egyéni keresési példány. Lásd: [az első Bing Custom Search-példány létrehozása](quick-start.md).
- [.NET core](https://www.microsoft.com/net/download/core) telepítve.
- Egy előfizetési kulcsot. Is kap egy előfizetési kulcsot, aktiválásakor a [az ingyenes próbaidőszak](https://azure.microsoft.com/try/cognitive-services/?api=bing-custom-search), vagy használhat egy díjköteles előfizetési kulcsot az Azure irányítópultján (lásd: [Cognitive Services API-fiók](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account)).    


## <a name="run-the-code"></a>A kód futtatása

Ez a példa futtatásához kövesse az alábbi lépéseket:

1. Hozzon létre egy mappát a kódot.  
  
2. A parancssorba vagy terminálba keresse meg az imént létrehozott mappára.  
  
3. Futtassa az alábbi parancsot:
    ```
    dotnet new console -o BingCustomSearch
    cd BingCustomSearch
    dotnet add package Newtonsoft.Json
    dotnet restore
    ```
  
4. Másolja az alábbi kódot a program.cs fájlhoz. Cserélje le **YOUR-SUBSCRIPTION-KEY** és **YOUR-CUSTOM-CONFIG-ID** az előfizetési kulcs és a konfiguráció azonosítója.

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
6. Hozza létre az alkalmazást, az alábbi paranccsal. Megjegyzés: a parancs kimenete által hivatkozott DLL elérési útja.

    <pre>
    dotnet build 
    </pre>
    
7. Futtassa az alkalmazást a következő parancs cseréje **elérési UTAT a kimeneti** hivatkozott 6. lépésben, az dll-fájl elérési útját.

    <pre>    
    dotnet **PATH TO OUTPUT**
    </pre>

## <a name="next-steps"></a>További lépések
- [A központi felhasználói felület konfigurálása](./hosted-ui.md)
- [Kiemeli a szöveget decoration jelölők használatával](./hit-highlighting.md)
- [Oldal weblapok](./page-webpages.md)
