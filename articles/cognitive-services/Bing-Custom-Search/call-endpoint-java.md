---
title: 'Gyors útmutató: az Bing Custom Search-végpont meghívása a Java használatával | Microsoft Docs'
titleSuffix: Azure Cognitive Services
description: Ezzel a rövid útmutatóval megkezdheti a Bing Custom Search-példány keresési eredményeinek megadását javában.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-custom-search
ms.topic: quickstart
ms.date: 03/24/2020
ms.author: aahi
ms.openlocfilehash: 02c86e5a4c1a04b98ebba73653980e8e5e00f645
ms.sourcegitcommit: 34a6fa5fc66b1cfdfbf8178ef5cdb151c97c721c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "80238891"
---
# <a name="quickstart-call-your-bing-custom-search-endpoint-using-java"></a>Rövid útmutató: az Bing Custom Search-végpont meghívása a Javával

Ezzel a rövid útmutatóval megkezdheti a Bing Custom Search-példány keresési eredményeinek kérelmezését. Habár ez az alkalmazás Java nyelven íródott, a Bing Custom Search API egy REST-alapú webszolgáltatás, amely kompatibilis a legtöbb programozási nyelvvel. A minta forráskódja elérhető a [githubon](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/java/Search/BingCustomSearchv7.java).

## <a name="prerequisites"></a>Előfeltételek

- Egy Bing Custom Search példány. További információért tekintse [meg a rövid útmutató: az első Bing Custom Search példány létrehozása](quick-start.md) című témakört.

- A legújabb [Java Development Kit](https://www.oracle.com/technetwork/java/javase/downloads/index.html)  

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

2. Hozzon létre egy `CustomSrchJava`nevű osztályt, és hozzon létre változókat az előfizetési kulcs, az egyéni keresési végpont és a keresési példány egyéni konfigurációs azonosítója alapján. Használhatja az alábbi globális végpontot, vagy az erőforráshoz tartozó Azure Portalban megjelenő [Egyéni altartomány](../../cognitive-services/cognitive-services-custom-subdomains.md) végpontot.
    ```java
    public class CustomSrchJava {
        static String host = "https://api.cognitive.microsoft.com";
        static String path = "/bingcustomsearch/v7.0/search";
        static String subscriptionKey = "YOUR-SUBSCRIPTION-KEY"; 
        static String customConfigId = "YOUR-CUSTOM-CONFIG-ID";
        static String searchTerm = "Microsoft";  
    ...
    ```

3. Hozzon létre egy `SearchResults` másik, nevű osztályt, amely tartalmazza a Bing Custom Search-példány válaszát.

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

4. Hozzon létre egy `prettify()` nevű függvényt, hogy FORMÁZZA a JSON-választ a Bing Custom Search API.

    ```java
        // pretty-printer for JSON; uses GSON parser to parse and re-serialize
        public static String prettify(String json_text) {
            JsonParser parser = new JsonParser();
            JsonObject json = parser.parse(json_text).getAsJsonObject();
            Gson gson = new GsonBuilder().setPrettyPrinting().create();
            return gson.toJson(json);
        }
    ```

## <a name="send-and-receive-a-search-request"></a>Keresési kérelem küldése és fogadása 

1. Hozzon létre egy `SearchWeb()` nevű függvényt, amely egy kérelmet `SearchResults` küld, és visszaad egy objektumot. Hozza létre a kérelem URL-címét az egyéni konfigurációs azonosító, a lekérdezés és a végpont adatainak kombinálásával. Adja hozzá az előfizetési kulcsot `Ocp-Apim-Subscription-Key` a fejléchez.

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

2. Hozzon létre egy streamet, és tárolja a `SearchResults` JSON-választ egy objektumban.

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

3. Az alkalmazás fő metódusában hívja `SearchWeb()` meg a keresési kifejezést, 

    ```java
    System.out.println("\nJSON Response:\n");
    System.out.println(prettify(result.jsonResponse));
    ```

4. Futtassa a programot.
    
## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Egyéni keresési Webalkalmazás létrehozása](./tutorials/custom-search-web-page.md)
