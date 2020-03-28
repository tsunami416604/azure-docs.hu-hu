---
title: 'Rövid útmutató: Keresési kérelem küldése a REST API-nak Java használatával – Bing entitáskeresés'
titleSuffix: Azure Cognitive Services
description: Ezzel a rövid útmutatóval kérést küldhet a Bing Entity Search REST API-nak Java használatával, és JSON-választ kaphat.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-entity-search
ms.topic: quickstart
ms.date: 12/11/2019
ms.author: aahi
ms.openlocfilehash: c4335e1ac0f0ffc7ee5570a8f0819827fc77dd18
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75384151"
---
# <a name="quickstart-send-a-search-request-to-the-bing-entity-search-rest-api-using-java"></a>Rövid útmutató: Keresési kérelem küldése a Bing Entity Search REST API-nak Java használatával

Ezzel a rövid útmutatóval első ként hívhatja meg a Bing Entity Search API-t, és megtekintheti a JSON-választ. Ez az egyszerű Java-alkalmazás hírkeresési lekérdezést küld az API-nak, és megjeleníti a választ.

Bár ez az alkalmazás Java nyelven lett íródott, az API egy RESTful-webszolgáltatás, azaz kompatibilis a legtöbb programnyelvvel.

## <a name="prerequisites"></a>Előfeltételek

* A [Java Fejlesztői Készlet (JDK)](https://www.oracle.com/technetwork/java/javase/downloads/)
* A [Gson-kódtár](https://github.com/google/gson)


[!INCLUDE [cognitive-services-bing-news-search-signup-requirements](../../../../includes/cognitive-services-bing-entity-search-signup-requirements.md)]

## <a name="create-and-initialize-a-project"></a>Projekt létrehozása és inicializálása

1. Hozzon létre egy új Java-projektet a kedvenc IDE-környezetében vagy szerkesztőjében, és importálja az alábbi kódtárakat.

   ```java
   import java.io.*;
   import java.net.*;
   import java.util.*;
   import javax.net.ssl.HttpsURLConnection;
   import com.google.gson.Gson;
   import com.google.gson.GsonBuilder;
   import com.google.gson.JsonObject;
   import com.google.gson.JsonParser;
   import com.google.gson.Gson;
   import com.google.gson.GsonBuilder;
   import com.google.gson.JsonObject;
   import com.google.gson.JsonParser;
   ```

2. Egy új osztályban hozzon létre változókat az API-végponthoz, az előfizetési kulcshoz és egy keresési lekérdezéshez. Használhatja az alábbi globális végpontot, vagy az [egyéni altartomány-végpontot,](../../../cognitive-services/cognitive-services-custom-subdomains.md) amely az azure-portálon jelenik meg az erőforráshoz.

   ```java
   public class EntitySearch {

      static String subscriptionKey = "ENTER KEY HERE";
    
        static String host = "https://api.cognitive.microsoft.com";
        static String path = "/bing/v7.0/entities";
    
        static String mkt = "en-US";
        static String query = "italian restaurant near me";
   //...
    
   ```

## <a name="construct-a-search-request-string"></a>Keresési kérelem karakterláncának összeállítása

1. Hozzon létre `search()` egy JSON `String`függvényt. url-kódolja a keresési lekérdezést, és adja `&q=`hozzá egy paraméterkarakterlánchoz a segítségével. Adja hozzá a piacot a karakterlánchoz. `?mkt=`
 
2. Hozzon létre egy URL-objektumot a gazdagép, az elérési út és a paraméterek karakterláncaival.
    
    ```java
    //...
    public static String search () throws Exception {
        String encoded_query = URLEncoder.encode (query, "UTF-8");
        String params = "?mkt=" + mkt + "&q=" + encoded_query;
        URL url = new URL (host + path + params);
    //...
    ```
      
## <a name="send-a-search-request-and-receive-a-response"></a>Keresési kérelem küldése és válasz fogadása

1. A `search()` fenti függvényben hozzon `HttpsURLConnection` létre `url.openCOnnection()`egy új objektumot a segítségével. Állítsa be a `GET`kérelem módszert , és `Ocp-Apim-Subscription-Key` adja hozzá az előfizetési kulcsot a fejléchez.

    ```java
    //...
    HttpsURLConnection connection = (HttpsURLConnection) url.openConnection();
    connection.setRequestMethod("GET");
    connection.setRequestProperty("Ocp-Apim-Subscription-Key", subscriptionKey);
    connection.setDoOutput(true);
    //...
    ```

2. Hozzon `StringBuilder`létre egy újat. Használjon `InputStreamReader` új paraméterként az API-válasz olvasásához `BufferedReader` példányosításkor.  
    
    ```java
    //...
    StringBuilder response = new StringBuilder ();
    BufferedReader in = new BufferedReader(
        new InputStreamReader(connection.getInputStream()));
    //...
    ```

3. Hozzon `String` létre egy objektumot `BufferedReader`a válasz tárolására a ból. Végighaladjon rajta, és fűzjön hozzá minden sort a karakterlánchoz. Ezután zárja be az olvasót, és küldje vissza a választ. 
    
    ```java
    String line;
    
    while ((line = in.readLine()) != null) {
      response.append(line);
    }
    in.close();
    
    return response.toString();
    ```

## <a name="format-the-json-response"></a>A JSON-válasz formázása

1. Hozzon létre `prettify` egy új függvényt, amelynek célja a JSON-válasz formázása. Hozzon `JsonParser`létre egy `parse()` újat, és hívja meg a json szöveget, és tárolja JSON-objektumként. 

2. A Gson-könyvtár segítségével `GsonBuilder()`hozzon `setPrettyPrinting().create()` létre egy újat, és formázza a jsont. Akkor adja vissza.    
  
   ```java
   //...
   public static String prettify (String json_text) {
    JsonParser parser = new JsonParser();
    JsonObject json = parser.parse(json_text).getAsJsonObject();
    Gson gson = new GsonBuilder().setPrettyPrinting().create();
    return gson.toJson(json);
   }
   //...
   ```

## <a name="call-the-search-function"></a>A keresési funkció felhívása

1. A projekt fő metódusából `search()`hívja meg `prettify()` a programot, és formázza a szöveget.
    
    ```java
        public static void main(String[] args) {
            try {
                String response = search ();
                System.out.println (prettify (response));
            }
            catch (Exception e) {
                System.out.println (e);
            }
        }
    ```

## <a name="example-json-response"></a>Példa JSON-válaszra

A rendszer JSON formátumban ad vissza egy sikeres választ a következő példában látható módon: 

```json
{
  "_type": "SearchResponse",
  "queryContext": {
    "originalQuery": "italian restaurant near me",
    "askUserForLocation": true
  },
  "places": {
    "value": [
      {
        "_type": "LocalBusiness",
        "webSearchUrl": "https://www.bing.com/search?q=sinful+bakery&filters=local...",
        "name": "Liberty's Delightful Sinful Bakery & Cafe",
        "url": "https://www.contoso.com/",
        "entityPresentationInfo": {
          "entityScenario": "ListItem",
          "entityTypeHints": [
            "Place",
            "LocalBusiness"
          ]
        },
        "address": {
          "addressLocality": "Seattle",
          "addressRegion": "WA",
          "postalCode": "98112",
          "addressCountry": "US",
          "neighborhood": "Madison Park"
        },
        "telephone": "(800) 555-1212"
      },

      . . .
      {
        "_type": "Restaurant",
        "webSearchUrl": "https://www.bing.com/search?q=Pickles+and+Preserves...",
        "name": "Munson's Pickles and Preserves Farm",
        "url": "https://www.princi.com/",
        "entityPresentationInfo": {
          "entityScenario": "ListItem",
          "entityTypeHints": [
            "Place",
            "LocalBusiness",
            "Restaurant"
          ]
        },
        "address": {
          "addressLocality": "Seattle",
          "addressRegion": "WA",
          "postalCode": "98101",
          "addressCountry": "US",
          "neighborhood": "Capitol Hill"
        },
        "telephone": "(800) 555-1212"
      },
      
      . . .
    ]
  }
}
```

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Egyoldalas webalkalmazás készítése](../tutorial-bing-entities-search-single-page-app.md)

* [Mi a Bing Entity Search API?](../overview.md )
* [Bing entitáskeresési API– referencia](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-entities-api-v7-reference)
