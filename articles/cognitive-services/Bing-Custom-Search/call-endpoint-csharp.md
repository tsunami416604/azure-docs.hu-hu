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
ms.openlocfilehash: 32644fe0cf0a6e1666d2d1ee6efb826bf753f001
ms.sourcegitcommit: 58c5cd866ade5aac4354ea1fe8705cee2b50ba9f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/24/2018
ms.locfileid: "42814863"
---
# <a name="call-bing-custom-search-endpoint-c"></a>Hívást a Bing Custom Search végpont (C#)

Ez a rövid útmutató bemutatja, hogyan használatával C#, a Bing Custom Search-végpont meghívása az egyéni keresőpéldányok keresési eredmények kérhet. 

## <a name="prerequisites"></a>Előfeltételek

-  Egy használatra kész egyéni keresési példány. Lásd: [az első Bing Custom Search-példány létrehozása](quick-start.md).
-  [.NET core](https://www.microsoft.com/net/download/core) telepítve.
- A [Cognitive Services API-fiók](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) a **Bing Search APIs**. A [az ingyenes próbaidőszak](https://azure.microsoft.com/try/cognitive-services/?api=bing-custom-search) elegendő ehhez a gyors útmutatóhoz. Szüksége lesz a hozzáférési kulcsot, ha az ingyenes próbaverzió aktiválásának, vagy használhat egy díjköteles előfizetési kulcsot az Azure irányítópultján.  

  >[!NOTE]  
  >Bing Custom Search rendelkezik egy kulcsot az előzetes verzióhoz, legkésőbb 2017. október 15-meglévő ügyfeleink fogja tudni azok kulcsait használja, 2017. November 30-ig vagy addig, amíg azok ügynökönként lekérdezések engedélyezett maximális számát. Ezt követően azok kell migrálnia az Azure-on általánosan elérhető verziót. 
 
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

4. Másolja az alábbi kódot a program.cs fájlhoz.
5. Cserélje le **YOUR-SUBSCRIPTION-KEY** és **YOUR-CUSTOM-CONFIG-ID** konfigurációját ID azonosítójával.

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
6. Hozza létre az alkalmazást, az alábbi paranccsal. Megjegyzés: a parancs kimenete által hivatkozott dll elérési útja.

    <pre>
    dotnet build 
    </pre>
    
7. Futtassa az alkalmazást a következő parancs cseréje **elérési UTAT a kimeneti** az elérési útját a buildelési lépést hivatkozik.

    <pre>    
    dotnet **PATH TO OUTPUT**
    </pre>

## <a name="next-steps"></a>További lépések
- [A központi felhasználói felület konfigurálása](./hosted-ui.md)
- [Kiemeli a szöveget decoration jelölők használatával](./hit-highlighting.md)
- [Oldal weblapok](./page-webpages.md)
