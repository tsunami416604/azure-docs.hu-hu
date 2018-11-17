---
title: Rövid útmutató – a helyi vállalati keresési Bing-API javával lekérdezés küldése |} A Microsoft Docs
titleSuffix: Azure Cognitive Services
description: Ez a cikk segítségével a helyi vállalati keresési Bing-API a Java használatának megkezdéséhez.
services: cognitive-services
author: mikedodaro
manager: rosh
ms.service: cognitive-services
ms.component: bing-local-business
ms.topic: article
ms.date: 11/01/2018
ms.author: rosh, v-gedod
ms.openlocfilehash: 5c31bcfaecb956bf0168a1485f3ee0fa985b9ceb
ms.sourcegitcommit: 7804131dbe9599f7f7afa59cacc2babd19e1e4b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/17/2018
ms.locfileid: "51852520"
---
# <a name="quickstart-send-a-query-to-the-bing-local-business-search-api-using-java"></a>Gyors útmutató: Egy lekérdezést küld a helyi vállalati keresési Bing-API Java használatával

Ez a rövid útmutató segítségével megkezdheti a kérések küldését a Bing helyi üzleti Search API, azaz az Azure Cognitive Services-szolgáltatás. Bár ez az egyszerű alkalmazás Java nyelven van megírva, az API-t olyan kompatibilis HTTP-kérelem indítására és JSON-elemzés minden programozási nyelvet a webes RESTful szolgáltatás.

Ez a példa az alkalmazás helyi érkezett válasz adatait lekérdezi az API-val, a keresési lekérdezés `hotel in Bellevue`.

## <a name="prerequisites"></a>Előfeltételek

* A [Java fejlesztési Kit(JDK)](https://www.oracle.com/technetwork/java/javase/downloads/index.html)

Egy Bing Search API-kat tartalmazó [Cognitive Services API-fiókkal](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) kell rendelkeznie. Az [ingyenes próbaverzió](https://azure.microsoft.com/try/cognitive-services/?api=bing-web-search-api) elegendő ehhez a rövid útmutatóhoz. A hozzáférési kulcsot biztosítunk az ingyenes próbaverzió aktiválásának kell.

Ez a példa az alkalmazás helyi válaszadatai olvas be a lekérdezést egy *szállodáját a Bellevue*.

## <a name="create-the-request"></a>A kérelem létrehozása 

Az alábbi kód létrehoz egy `WebRequest`, beállítja a hozzáférési kulcs fejléc, és hozzáad egy lekérdezési karakterláncot az "szállodáját a Bellevue".  Ezután elküldi a kérést, és hozzárendeli a választ egy sztringhez, hogy az tartalmazza a JSON-szöveget.

````
    // construct URL of search request (endpoint + query string)
     URL url = new URL(host + path + "?q=" +  URLEncoder.encode(searchQuery, "UTF-8") + "appid=AEA845921DC03F506DC317A90EDDBF33074523F7&market=en-us");
    HttpsURLConnection connection = (HttpsURLConnection)url.openConnection();
    //connection.setRequestProperty("Ocp-Apim-Subscription-Key", subscriptionKey);

    // receive JSON body
    InputStream stream = connection.getInputStream();
    String response = new Scanner(stream).useDelimiter("\\A").next();

    // construct result object for return
    SearchResults results = new SearchResults(new HashMap<String, String>(), response);
````

## <a name="run-the-complete-application"></a>A teljes alkalmazás futtatása

A helyi vállalati keresési Bing-API a Bing kereső eredményeket ad vissza.
1. Töltse le vagy telepítse a gson-kódtárat.
2. Hozzon létre egy új Java-projektet a kedvenc IDE-környezetében vagy szerkesztőjében.
3. Adja hozzá az alábbi kódot.
4. A subscriptionKey értékét cserélje le az előfizetéshez használható hozzáférési kulcsra.
5. Futtassa a programot.

````
package localSearch;
import java.net.*;
import java.util.*;
import java.io.*;
import javax.net.ssl.HttpsURLConnection;

/*
 * Gson: https://github.com/google/gson
 * Maven info:
 *     groupId: com.google.code.gson
 *     artifactId: gson
 *     version: 2.8.1
 *
 * Once you have compiled or downloaded gson-2.8.1.jar, assuming you have placed it in the
 * same folder as this file (localSearch.java), you can compile and run this program at
 * the command line as follows.
 *
 * javac localSearch.java -classpath .;gson-2.8.1.jar -encoding UTF-8
 * java -cp .;gson-2.8.1.jar localSearch
 */
import com.google.gson.Gson;
import com.google.gson.GsonBuilder;
import com.google.gson.JsonObject;
import com.google.gson.JsonParser;

public class LocalSearchCls {

    // ***********************************************
    // *** Update or verify the following values. ***
    // **********************************************

        // Replace the subscriptionKey string value with your valid subscription key.
        static String subscriptionKey = "YOUR-ACCESS-KEY";

        static String host = "https://api.cognitive.microsoft.com/bing";
        static String path = "/v7.0/search";

        static String searchTerm = "Hotel in Bellevue";

        public static SearchResults SearchLocal (String searchQuery) throws Exception {
            // construct URL of search request (endpoint + query string)
            URL url = new URL(host + path + "?q=" +  URLEncoder.encode(searchQuery, "UTF-8") + 
                         "&appid=" + subscriptionKey + "&market=en-us");
            HttpsURLConnection connection = (HttpsURLConnection)url.openConnection();
            //connection.setRequestProperty("Ocp-Apim-Subscription-Key", subscriptionKey);

            // receive JSON body
            InputStream stream = connection.getInputStream();
            String response = new Scanner(stream).useDelimiter("\\A").next();

            // construct result object for return
            SearchResults results = new SearchResults(new HashMap<String, String>(), response);

            // extract Bing-related HTTP headers
            Map<String, List<String>> headers = connection.getHeaderFields();
            for (String header : headers.keySet()) {
                if (header == null) continue;      // may have null key
                if (header.startsWith("BingAPIs-") || header.startsWith("X-MSEdge-")) {
                    results.relevantHeaders.put(header, headers.get(header).get(0));
                }
            }

            stream.close();
            return results;
        }

        // pretty-printer for JSON; uses GSON parser to parse and re-serialize
        public static String prettify(String json_text) {
            JsonParser parser = new JsonParser();
            JsonObject json = parser.parse(json_text).getAsJsonObject();
            Gson gson = new GsonBuilder().setPrettyPrinting().create();
            return gson.toJson(json);
        }

        public static void main (String[] args) {
            try {
                System.out.println("Searching the Web for: " + searchTerm);

                SearchResults result = SearchLocal(searchTerm);

                System.out.println("\nRelevant HTTP Headers:\n");
                for (String header : result.relevantHeaders.keySet())
                    System.out.println(header + ": " + result.relevantHeaders.get(header));

                System.out.println("\nJSON Response:\n");
                System.out.println(prettify(result.jsonResponse));
            }
            catch (Exception e) {
                e.printStackTrace(System.out);
                System.exit(1);
            }
        }
    }

    // Container class for search results encapsulates relevant headers and JSON data
    class SearchResults{
        HashMap<String, String> relevantHeaders;
        String jsonResponse;
        SearchResults(HashMap<String, String> headers, String json) {
            relevantHeaders = headers;
            jsonResponse = json;
        }
    }

````

## <a name="next-steps"></a>További lépések
- [Helyi üzleti keresés a rövid útmutató](local-quickstart.md)
- [Helyi üzleti Search-Node-quickstart](local-search-node-quickstart.md)
- [Helyi üzleti keresési Python a rövid útmutató](local-search-python-quickstart.md)