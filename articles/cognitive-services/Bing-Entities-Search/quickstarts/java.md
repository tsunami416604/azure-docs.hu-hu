---
title: 'Gyors útmutató: keresési kérelem küldése a REST API Javával – Bing Entity Search'
titleSuffix: Azure Cognitive Services
description: Ezzel a rövid útmutatóval kérést küldhet a Bing Entity Search REST API Javával, és JSON-választ kap.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-entity-search
ms.topic: quickstart
ms.date: 12/11/2019
ms.author: aahi
ms.openlocfilehash: c4335e1ac0f0ffc7ee5570a8f0819827fc77dd18
ms.sourcegitcommit: 34a6fa5fc66b1cfdfbf8178ef5cdb151c97c721c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "75384151"
---
# <a name="quickstart-send-a-search-request-to-the-bing-entity-search-rest-api-using-java"></a>Gyors útmutató: keresési kérelem küldése a Bing Entity Search REST API Javával

Ezzel a rövid útmutatóval elvégezheti az első hívását a Bing Entity Search API, és megtekintheti a JSON-választ. Ez az egyszerű Java-alkalmazás egy Hírek keresési lekérdezést küld az API-nak, és megjeleníti a választ.

Bár ez az alkalmazás Java nyelven lett íródott, az API egy RESTful-webszolgáltatás, azaz kompatibilis a legtöbb programnyelvvel.

## <a name="prerequisites"></a>Előfeltételek

* A [Java fejlesztői készlet (JDK)](https://www.oracle.com/technetwork/java/javase/downloads/)
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

2. Egy új osztályban hozzon létre változókat az API-végponthoz, az előfizetési kulcshoz és egy keresési lekérdezéshez. Használhatja az alábbi globális végpontot, vagy az erőforráshoz tartozó Azure Portalban megjelenő [Egyéni altartomány](../../../cognitive-services/cognitive-services-custom-subdomains.md) végpontot.

   ```java
   public class EntitySearch {

      static String subscriptionKey = "ENTER KEY HERE";
    
        static String host = "https://api.cognitive.microsoft.com";
        static String path = "/bing/v7.0/entities";
    
        static String mkt = "en-US";
        static String query = "italian restaurant near me";
   //...
    
   ```

## <a name="construct-a-search-request-string"></a>Keresési kérelem karakterláncának létrehozása

1. Hozzon létre egy `search()` nevű függvényt, `String`amely egy JSON-t ad vissza. URL – kódolja a keresési lekérdezést, és adja hozzá egy Parameters karakterlánchoz a `&q=`következővel:. Adja hozzá a piacot a karakterlánchoz a következővel: `?mkt=`.
 
2. Hozzon létre egy URL-objektumot a gazdagép, az elérési út és a paraméterek karakterláncával.
    
    ```java
    //...
    public static String search () throws Exception {
        String encoded_query = URLEncoder.encode (query, "UTF-8");
        String params = "?mkt=" + mkt + "&q=" + encoded_query;
        URL url = new URL (host + path + params);
    //...
    ```
      
## <a name="send-a-search-request-and-receive-a-response"></a>Keresési kérelem küldése és válasz fogadása

1. A `search()` fent létrehozott függvényben hozzon létre egy `HttpsURLConnection` új objektumot `url.openCOnnection()`a paranccsal. Állítsa be a kérelem metódusát `GET`, és adja hozzá az előfizetési `Ocp-Apim-Subscription-Key` kulcsot a fejléchez.

    ```java
    //...
    HttpsURLConnection connection = (HttpsURLConnection) url.openConnection();
    connection.setRequestMethod("GET");
    connection.setRequestProperty("Ocp-Apim-Subscription-Key", subscriptionKey);
    connection.setDoOutput(true);
    //...
    ```

2. Hozzon létre `StringBuilder`egy újat. Az API `BufferedReader` - `InputStreamReader` válasz elolvasásához használja az új paramétert.  
    
    ```java
    //...
    StringBuilder response = new StringBuilder ();
    BufferedReader in = new BufferedReader(
        new InputStreamReader(connection.getInputStream()));
    //...
    ```

3. Hozzon `String` létre egy objektumot, amely a válaszát tárolja `BufferedReader`. Ismételje meg a szöveget, és fűzze hozzá az egyes sorokhoz a karakterláncot. Ezután zárjuk le az olvasót, és adja vissza a választ. 
    
    ```java
    String line;
    
    while ((line = in.readLine()) != null) {
      response.append(line);
    }
    in.close();
    
    return response.toString();
    ```

## <a name="format-the-json-response"></a>A JSON-válasz formázása

1. Hozzon létre egy nevű `prettify` új függvényt a JSON-válasz formázásához. Hozzon létre `JsonParser`egy újat, `parse()` és hívja meg a JSON-szöveget, és tárolja JSON-objektumként. 

2. Hozzon létre egy új `GsonBuilder()`, a Gson-kódtár használatával `setPrettyPrinting().create()` , és formázza a JSON-t. Ezután adja vissza.    
  
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

## <a name="call-the-search-function"></a>A Search függvény meghívása

1. A projekt fő metódusában hívja meg a függvényt `search()`, és `prettify()` formázza a szöveget.
    
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
* [Bing Entity Search API referenciája](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-entities-api-v7-reference)
