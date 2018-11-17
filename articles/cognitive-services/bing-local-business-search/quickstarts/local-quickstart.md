---
title: Rövid útmutató – a helyi vállalati keresési Bing-API a lekérdezés küldése C# |} A Microsoft Docs
titleSuffix: Azure Cognitive Services
description: Ez a cikk segítségével a helyi vállalati keresési Bing-API a használatának megkezdéséhez C#.
services: cognitive-services
author: mikedodaro
manager: rosh
ms.service: cognitive-services
ms.component: bing-local-business
ms.topic: article
ms.date: 11/01/2018
ms.author: rosh, v-gedod
ms.openlocfilehash: 99a9ab2ae1ed102459d2e13f060eebd08bb0ec75
ms.sourcegitcommit: 7804131dbe9599f7f7afa59cacc2babd19e1e4b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/17/2018
ms.locfileid: "51853574"
---
# <a name="quickstart-send-a-query-to-the-bing-local-business-search-api-in-c"></a>Gyors útmutató: A helyi vállalati keresési Bing-API a lekérdezés küldéseC#

Ez a rövid útmutató segítségével megkezdheti a kérések küldését a Bing helyi üzleti Search API, azaz az Azure Cognitive Services-szolgáltatás. Bár ez az egyszerű alkalmazás nyelven van megírva C#, az API egy olyan webes RESTful szolgáltatás kompatibilis HTTP-kérelem indítására és JSON-elemzés minden programozási nyelvet.

Ez a példa az alkalmazás helyi érkezett válasz adatait lekérdezi az API-val, a keresési lekérdezés `hotel in Bellevue`.

## <a name="prerequisites"></a>Előfeltételek

* A [Visual Studio 2017](https://www.visualstudio.com/downloads/) bármely kiadása.
* Linux/MacOS rendszer esetében az alkalmazás a [Monóval](http://www.mono-project.com/) futtatható.

Egy Bing Search API-kat tartalmazó [Cognitive Services API-fiókkal](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) kell rendelkeznie. Az [ingyenes próbaverzió](https://azure.microsoft.com/try/cognitive-services/?api=bing-web-search-api) elegendő ehhez a rövid útmutatóhoz. A hozzáférési kulcsot biztosítunk az ingyenes próbaverzió aktiválásának kell.

## <a name="create-the-request"></a>A kérelem létrehozása 

Az alábbi kód létrehoz egy `WebRequest`, beállítja a hozzáférési kulcs fejléc, és hozzáad egy lekérdezési karakterláncot az "a Bellevue éttermi".  Ezután elküldi a kérést, és hozzárendeli a választ egy sztringhez, hogy az tartalmazza a JSON-szöveget.

````
    // Replace the accessKey string value with your valid access key.
    const string accessKey = "enter key here";

    const string uriBase = "https://api.cognitive.microsoft.com/bing/v7.0/search";   

    const string searchTerm = "restaurant in Bellevue";
    // Construct the URI of the search request
    var uriQuery = uriBase + "?q=" + Uri.EscapeDataString(searchQuery) + "appid=" + accessKey;

    // Run the Web request and get response.
    WebRequest request = HttpWebRequest.Create(uriQuery);
    //request.Headers["Ocp-Apim-Subscription-Key"] = accessKey; 

    HttpWebResponse response = (HttpWebResponse)request.GetResponseAsync().Result;
    string json = new StreamReader(response.GetResponseStream()).ReadToEnd();
````

## <a name="run-the-complete-application"></a>A teljes alkalmazás futtatása

A helyi vállalati keresési Bing-API a Bing kereső honosított keresési eredményeket adja vissza.
1. Hozzon létre egy új konzolmegoldást a Visual Studióban (a Community Edition kiadás is használható).
2. Cserélje le a Program.cs fájl tartalmát az alábbi kódra.
3. AccessKey értékét cserélje le az előfizetéshez tartozó érvényes hozzáférési kulcs.
4. Futtassa a programot.

````
using System;
using System.Collections.Generic;
using System.Text;
using System.Net;
using System.IO;

namespace localSearch
{
    class Program
    {
        // **********************************************
        // *** Update or verify the following values. ***
        // **********************************************

        // Replace the accessKey string value with your valid access key.
        const string accessKey = "enter key here";

        const string uriBase = "https://api.cognitive.microsoft.com/bing/v7.0/search";   

        const string searchTerm = "restaurant in Bellevue";

        // Returns search results including relevant headers
        struct SearchResult
        {
            public String jsonResult;
            public Dictionary<String, String> relevantHeaders;
        }

        static void Main()
        {
            Console.OutputEncoding = System.Text.Encoding.UTF8;
            Console.WriteLine("Searching locally for: " + searchTerm);

            SearchResult result = BingLocalSearch(searchTerm);

            Console.WriteLine("\nRelevant HTTP Headers:\n");
            foreach (var header in result.relevantHeaders)
                Console.WriteLine(header.Key + ": " + header.Value);

            Console.WriteLine("\nJSON Response:\n");
            Console.WriteLine(JsonPrettyPrint(result.jsonResult));

            Console.Write("\nPress Enter to exit ");
            Console.ReadLine();
        }

        /// <summary>
        /// Performs a Bing Local business search and return the results as a SearchResult.
        /// </summary>
        static SearchResult BingLocalSearch(string searchQuery)
        {
            // Construct the URI of the search request
            var uriQuery = uriBase + "?q=" + Uri.EscapeDataString(searchQuery) + 
                                "&appid=" + accessKey + "&market=en-us";

            // Perform the Web request and get the response
            WebRequest request = HttpWebRequest.Create(uriQuery);
            //request.Headers["Ocp-Apim-Subscription-Key"] = accessKey; 

            HttpWebResponse response = (HttpWebResponse)request.GetResponseAsync().Result;
            string json = new StreamReader(response.GetResponseStream()).ReadToEnd();

            // Create result object for return
            var searchResult = new SearchResult();
            searchResult.jsonResult = json;
            searchResult.relevantHeaders = new Dictionary<String, String>();

            // Extract Bing HTTP headers
            foreach (String header in response.Headers)
            {
                if (header.StartsWith("BingAPIs-") || header.StartsWith("X-MSEdge-"))
                    searchResult.relevantHeaders[header] = response.Headers[header];
            }

            return searchResult;
        }

        /// <summary>
        /// Formats the given JSON string by adding line breaks and indents.
        /// </summary>
        /// <param name="json">The raw JSON string to format.</param>
        /// <returns>The formatted JSON string.</returns>
        static string JsonPrettyPrint(string json)
        {
            if (string.IsNullOrEmpty(json))
                return string.Empty;

            json = json.Replace(Environment.NewLine, "").Replace("\t", "");

            StringBuilder sb = new StringBuilder();
            bool quote = false;
            bool ignore = false;
            int offset = 0;
            int indentLength = 3;

            foreach (char ch in json)
            {
                switch (ch)
                {
                    case '"':
                        if (!ignore) quote = !quote;
                        break;
                    case '\'':
                        if (quote) ignore = !ignore;
                        break;
                }

                if (quote)
                    sb.Append(ch);
                else
                {
                    switch (ch)
                    {
                        case '{':
                        case '[':
                            sb.Append(ch);
                            sb.Append(Environment.NewLine);
                            sb.Append(new string(' ', ++offset * indentLength));
                            break;
                        case '}':
                        case ']':
                            sb.Append(Environment.NewLine);
                            sb.Append(new string(' ', --offset * indentLength));
                            sb.Append(ch);
                            break;
                        case ',':
                            sb.Append(ch);
                            sb.Append(Environment.NewLine);
                            sb.Append(new string(' ', offset * indentLength));
                            break;
                        case ':':
                            sb.Append(ch);
                            sb.Append(' ');
                            break;
                        default:
                            if (ch != ' ') sb.Append(ch);
                            break;
                    }
                }
            }

            return sb.ToString().Trim();
        }
    }
}

````

## <a name="next-steps"></a>További lépések
- [Helyi üzleti keresési Java a rövid útmutató](local-search-java-quickstart.md)
- [Helyi üzleti Search-Node-quickstart](local-search-node-quickstart.md)
- [Helyi üzleti keresési Python a rövid útmutató](local-search-python-quickstart.md)
