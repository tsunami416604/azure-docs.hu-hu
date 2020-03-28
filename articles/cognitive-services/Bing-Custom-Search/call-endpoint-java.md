---
title: 'Rövid útmutató: A Bing egyéni keresési végpontjának hívása Java használatával | Microsoft dokumentumok'
titleSuffix: Azure Cognitive Services
description: Ezzel a rövid útmutatóval megkezdheti a keresési eredmények kérését a Java-ban lévő Bing egyéni keresési példányából.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-custom-search
ms.topic: quickstart
ms.date: 03/24/2020
ms.author: aahi
ms.openlocfilehash: 02c86e5a4c1a04b98ebba73653980e8e5e00f645
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "80238891"
---
# <a name="quickstart-call-your-bing-custom-search-endpoint-using-java"></a>Rövid útmutató: A Bing egyéni keresési végpontjának hívása Java használatával

Ezzel a rövid útmutatóval megkezdheti a keresési eredmények keresését a Bing egyéni keresési példányából. Bár ez az alkalmazás Java nyelven íródott, a Bing Egyéni keresési API egy RESTful webszolgáltatás, amely kompatibilis a legtöbb programozási nyelvvel. A minta forráskódja elérhető a [GitHubon.](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/java/Search/BingCustomSearchv7.java)

## <a name="prerequisites"></a>Előfeltételek

- Bing egyéni keresési példánya. További információ: További információ [az első Bing egyéni keresési példány létrehozása](quick-start.md) című témakörben.

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

2. Hozzon létre `CustomSrchJava`egy osztály nevű , és hozzon létre változókat az előfizetési kulcs, egyéni keresési végpont, és a keresési példány egyéni konfigurációs azonosítóját. Használhatja az alábbi globális végpontot, vagy az [egyéni altartomány-végpontot,](../../cognitive-services/cognitive-services-custom-subdomains.md) amely az azure-portálon jelenik meg az erőforráshoz.
    ```java
    public class CustomSrchJava {
        static String host = "https://api.cognitive.microsoft.com";
        static String path = "/bingcustomsearch/v7.0/search";
        static String subscriptionKey = "YOUR-SUBSCRIPTION-KEY"; 
        static String customConfigId = "YOUR-CUSTOM-CONFIG-ID";
        static String searchTerm = "Microsoft";  
    ...
    ```

3. Hozzon létre `SearchResults` egy másik nevű osztályt, amely tartalmazza a Bing egyéni keresési példányának válaszát.

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

4. Hozzon létre `prettify()` egy nevű függvényt a JSON-válasz formázásához a Bing egyéni keresési API-ból.

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

1. Hozzon létre `SearchWeb()` egy kérelmet küldő függvényt, és visszaad egy `SearchResults` objektumot. Hozza létre a kérelem URL-címét az egyéni konfigurációs azonosító, a lekérdezés és a végpontadatainak kombinálásával. Adja hozzá az `Ocp-Apim-Subscription-Key` Előfizetés kulcsot a fejléchez.

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

2. Hozzon létre egy adatfolyamot, `SearchResults` és tárolja a JSON-választ egy objektumban.

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

3. Az alkalmazás fő módszerében `SearchWeb()` hívja meg a keresett kifejezést, 

    ```java
    System.out.println("\nJSON Response:\n");
    System.out.println(prettify(result.jsonResponse));
    ```

4. Futtassa a programot.
    
## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Egyéni keresési webalkalmazás létrehozása](./tutorials/custom-search-web-page.md)
