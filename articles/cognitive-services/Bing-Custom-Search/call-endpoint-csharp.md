---
title: Hívás végpont C# - Bing egyéni keresés – Microsoft kognitív Services segítségével
description: A gyors üzembe helyezés keresési eredmények kérhet az egyéni keresési példányát a C# hívni a Bing egyéni keresési végpont mutatja be.
services: cognitive-services
author: brapel
manager: ehansen
ms.service: cognitive-services
ms.component: bing-custom-search
ms.topic: article
ms.date: 05/07/2018
ms.author: v-brapel
ms.openlocfilehash: be4cc79d16b9a22124f16878b11ca04a916f98ae
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/23/2018
ms.locfileid: "35347855"
---
# <a name="call-bing-custom-search-endpoint-c"></a>Hívás Bing egyéni keresés végpont (C#)

A gyors üzembe helyezés keresési eredmények kérhet az egyéni keresési példányát a C# hívni a Bing egyéni keresési végpont mutatja be. 

## <a name="prerequisites"></a>Előfeltételek

-  Egy használatra kész egyéni keresési példányát. Lásd: [hozza létre az első Bing egyéni keresési példányát](quick-start.md).
-  [A .net core](https://www.microsoft.com/net/download/core) telepítve.
- A [kognitív szolgáltatások API-fiók](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) rendelkező **Bing keresési API-k**. A [ingyenes próbaverzió](https://azure.microsoft.com/try/cognitive-services/?api=bing-custom-search) elegendő-e a gyors üzembe helyezés. Az elérési kulcsot, ha aktiválja az ingyenes próbaverzió, vagy egy fizetős kulcsot használhatja az Azure irányítópultról van szüksége.  

  >[!NOTE]  
  >Meglévő Bing egyéni keresés ügyfelek rendelkező kiépítve előzetes kulcs, vagy azt megelőzően 2017. október 15 is kulcsokat kíván használni a November 30, 2017, vagy amíg azok kimerített engedélyezett lekérdezések maximális számát. Ezt követően kell az Azure-on általánosan elérhető verziójára való áttérést. 
 
## <a name="run-the-code"></a>A kód futtatása

Ez a példa futtatásához kövesse az alábbi lépéseket:

1. Hozzon létre egy mappát a kódot.
2. A parancssor vagy a Terminálszolgáltatások keresse meg az imént létrehozott mappára.
3. Futtassa az alábbi parancsot:
    ```
    dotnet new console -o BingCustomSearch
    cd BingCustomSearch
    dotnet add package Newtonsoft.Json
    dotnet restore
   ```

4. Másolja az alábbi kódot a program.cs fájlt.
5. Cserélje le **YOUR-ELŐFIZETÉS-kulcs** és **YOUR-egyéni-CONFIG-ID** a kulcs és a konfigurációs azonosítóval.

    ``` CSharp
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
6. Építenie az alkalmazást, az alábbi parancs segítségével. Megjegyzés: a parancs kimenete által hivatkozott dll elérési útja.
    <pre>
    dotnet build 
    </pre>
7. Futtassa az alkalmazást, használja a következő parancs cseréje **elérési UTAT a kimeneti** a következő elérési a build lépés hivatkozik.
    <pre>    
    dotnet **PATH TO OUTPUT**
    </pre>

## <a name="next-steps"></a>További lépések
- [A központi felhasználói felületi élmény konfigurálása](./hosted-ui.md)
- [Jelölje ki a szöveget decoration jelölők használatával](./hit-highlighting.md)
- [Lap szolgáltatásának konfigurálása](./page-webpages.md)
