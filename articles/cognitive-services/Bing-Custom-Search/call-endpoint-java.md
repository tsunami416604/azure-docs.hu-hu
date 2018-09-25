---
title: Végpont meghívása a Microsoft Cognitive Services – Bing egyéni keresés – Java használatával
description: 'Ez a rövid útmutató bemutatja, hogyan keresési eredmények kérhet az egyéni keresőpéldányok: a Bing Custom Search-végpont meghívása a Java használatával.'
services: cognitive-services
author: brapel
manager: ehansen
ms.service: cognitive-services
ms.component: bing-custom-search
ms.topic: conceptual
ms.date: 05/07/2018
ms.author: v-brapel
ms.openlocfilehash: 7ef4de749d5b9152bbe043a26d3c60fe7f09f869
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/24/2018
ms.locfileid: "46951814"
---
# <a name="call-bing-custom-search-endpoint-java"></a>Hívást a Bing Custom Search-végpont (Java)

Ez a rövid útmutató bemutatja, hogyan kérhet a keresési eredmények a Java használatával hívja meg a Bing Custom Search-végpont egyéni keresési példány. 

## <a name="prerequisites"></a>Előfeltételek

A rövid útmutató elvégzéséhez a következőkre lesz szüksége:

- Egy használatra kész egyéni keresési példány. Lásd: [az első Bing Custom Search-példány létrehozása](quick-start.md).
- [Java](https://www.java.com) telepítve.
- Egy előfizetési kulcsot. Is kap egy előfizetési kulcsot, aktiválásakor a [az ingyenes próbaidőszak](https://azure.microsoft.com/try/cognitive-services/?api=bing-custom-search), vagy használhat egy díjköteles előfizetési kulcsot az Azure irányítópultján (lásd: [Cognitive Services API-fiók](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account)).    

## <a name="run-the-code"></a>A kód futtatása

Ez a példa futtatásához kövesse az alábbi lépéseket:

1. A Java ide Környezethez választott hozzon létre egy csomagot.  
  
2. Hozzon létre egy fájlt CustomSrchJava.java a csomagban, és másolja az alábbi kódot a fájlba. Cserélje le **YOUR-SUBSCRIPTION-KEY** és **YOUR-CUSTOM-CONFIG-ID** az előfizetési kulcs és a konfiguráció azonosítója.  
  
    ```java
    import java.io.InputStream;
    import java.net.URL;
    import java.net.URLEncoder;
    import java.util.HashMap;
    import java.util.List;
    import java.util.Map;
    import java.util.Scanner;
    
    import javax.net.ssl.HttpsURLConnection;
    
    import com.google.gson.Gson;
    import com.google.gson.GsonBuilder;
    import com.google.gson.JsonObject;
    import com.google.gson.JsonParser;
    
    public class CustomSrchJava {       
        static String host = "https://api.cognitive.microsoft.com";
        static String path = "/bingcustomsearch/v7.0/search";
        static String subscriptionKey = "YOUR-SUBSCRIPTION-KEY"; 
        static String customConfigId = "YOUR-CUSTOM-CONFIG-ID";  
    
        static String searchTerm = "Microsoft";  // Replace with search term specific to your search scenario.
    
        public static SearchResults SearchWeb (String searchQuery) throws Exception {
            // construct URL of search request (endpoint + query string)
            URL url = new URL(host + path + "?q=" +  URLEncoder.encode(searchTerm, "UTF-8") + "&CustomConfig=" + customConfigId);
            HttpsURLConnection connection = (HttpsURLConnection)url.openConnection();
            connection.setRequestProperty("Ocp-Apim-Subscription-Key", subscriptionKey);
    
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
            if (subscriptionKey.length() != 32) {
                System.out.println("Invalid Custom Search subscription key!");
                System.out.println("Please paste yours into the source code.");
                System.exit(1);
            }
    
            try {
                System.out.println("Searching your slice of the Web for: " + searchTerm);
    
                SearchResults result = SearchWeb(searchTerm);
    
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
    ```  
  
4. Futtassa a programot.
    
## <a name="next-steps"></a>További lépések
- [A központi felhasználói felület konfigurálása](./hosted-ui.md)
- [Kiemeli a szöveget decoration jelölők használatával](./hit-highlighting.md)
- [Oldal weblapok](./page-webpages.md)