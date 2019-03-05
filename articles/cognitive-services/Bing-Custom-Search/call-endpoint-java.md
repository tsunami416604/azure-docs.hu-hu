---
title: 'Gyors útmutató: A Java használatával a Bing Custom Search-végpont meghívása |} A Microsoft Docs'
titlesuffix: Azure Cognitive Services
description: Ez a rövid útmutató segítségével megkezdheti a keresési eredmények kér a Bing Custom Search példány Java-környezetben.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-custom-search
ms.topic: quickstart
ms.date: 03/04/2019
ms.author: aahi
ms.openlocfilehash: fc1c7d2730d68fb8e3b51a39e0cb89b4098418d6
ms.sourcegitcommit: 8b41b86841456deea26b0941e8ae3fcdb2d5c1e1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/05/2019
ms.locfileid: "57337022"
---
# <a name="quickstart-call-your-bing-custom-search-endpoint-using-java"></a>Gyors útmutató: A Java használatával a Bing Custom Search-végpont meghívása

Ez a rövid útmutató segítségével megkezdheti a keresési eredmények kér a Bing Custom Search-példányt. Bár ez az alkalmazás Java nyelven van megírva, a Bing Custom Search API olyan kompatibilis szinte bármelyik programozási nyelvével webes RESTful szolgáltatás. A minta forráskódja a [GitHubon](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/java/Search/BingCustomSearchv7.java) érhető el.

## <a name="prerequisites"></a>Előfeltételek

- Bing Custom Search-példány. Lásd: [a rövid útmutató: Az első Bing Custom Search-példány létrehozása](quick-start.md) további információt.

- A legújabb [Java fejlesztői készlet](https://www.oracle.com/technetwork/java/javase/downloads/index.html)  

- A [Gson-kódtár](https://github.com/google/gson)

[!INCLUDE [cognitive-services-bing-custom-search-prerequisites](../../../includes/cognitive-services-bing-custom-search-signup-requirements.md)]

## <a name="create-and-initialize-the-application"></a>Az alkalmazás létrehozása és inicializálása

1. Hozzon létre egy új Java-projektet a kedvenc IDE-környezetében vagy szerkesztőjében, és importálja az alábbi kódtárakat.

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
    ```

2. Hozzon létre egy osztályt `CustomSrchJava`, és hozza létre a változókat az előfizetési kulcs, egyéni keresés végpont és a keresési példány egyéni konfiguráció azonosítója. 
    ```java
    public class CustomSrchJava {
        static String host = "https://api.cognitive.microsoft.com";
        static String path = "/bingcustomsearch/v7.0/search";
        static String subscriptionKey = "YOUR-SUBSCRIPTION-KEY"; 
        static String customConfigId = "YOUR-CUSTOM-CONFIG-ID";
        static String searchTerm = "Microsoft";  
    ...
    ```

3. Hozzon létre egy másik osztály nevű `SearchResults` tartalmazza a Bing Custom Search példány válaszát.

    ```csharp
    class SearchResults{
        HashMap<String, String> relevantHeaders;
        String jsonResponse;
        SearchResults(HashMap<String, String> headers, String json) {
            relevantHeaders = headers;
            jsonResponse = json;
        }
    }
    ```

4. Hozzon létre egy függvényt, nevű `prettify()` formázhatja a Bing Custom Search API a JSON-választ.

    ```java
        // pretty-printer for JSON; uses GSON parser to parse and re-serialize
        public static String prettify(String json_text) {
            JsonParser parser = new JsonParser();
            JsonObject json = parser.parse(json_text).getAsJsonObject();
            Gson gson = new GsonBuilder().setPrettyPrinting().create();
            return gson.toJson(json);
        }
    ```

## <a name="send-and-receive-a-search-request"></a>Küldeni és fogadni egy keresési kérelmet 

1. Hozzon létre egy függvényt, nevű `SearchWeb()` , amely elküld egy kérelmet, és adja vissza egy `SearchResults` objektum. A kérelem létrehozása egyéni konfigurációs Azonosítót, a lekérdezés és a végpont adatait kombinálásával URL-címét. Az előfizetési kulcs hozzáadása a `Ocp-Apim-Subscription-Key` fejléc.

    ```java
    public class CustomSrchJava {
    ...
        public static SearchResults SearchWeb (String searchQuery) throws Exception {
            // construct the URL for your search request (endpoint + query string)
            URL url = new URL(host + path + "?q=" +  URLEncoder.encode(searchTerm, "UTF-8") + "&CustomConfig=" + customConfigId);
            HttpsURLConnection connection = (HttpsURLConnection)url.openConnection();
            connection.setRequestProperty("Ocp-Apim-Subscription-Key", subscriptionKey);
    ...
    ```

2. Hozzon létre egy streamet, és tárolja a JSON-válasz egy `SearchResults` objektum.

    ```java
    public class CustomSrchJava {
    ...
        public static SearchResults SearchWeb (String searchQuery) throws Exception {
            ...
            // receive the JSON body
            InputStream stream = connection.getInputStream();
            String response = new Scanner(stream).useDelimiter("\\A").next();
        
            // construct result object for return
            SearchResults results = new SearchResults(new HashMap<String, String>(), response);
            
            stream.close();
            return results;
        }
    ```

3. A fő metódus az alkalmazás hívása `SearchWeb()` együtt a keresett kifejezés 

    ```java
    System.out.println("\nJSON Response:\n");
    System.out.println(prettify(result.jsonResponse));
    ```

4. Futtassa a programot.
    
## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Egyéni keresés webes alkalmazás készítése](./tutorials/custom-search-web-page.md)
