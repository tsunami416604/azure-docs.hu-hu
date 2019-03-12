---
title: 'Gyors útmutató: Egy keresési kérelmet küld a Bing Entity Search REST API Java használatával'
titlesuffix: Azure Cognitive Services
description: Ez a rövid útmutató segítségével egy kérelmet küld a Bing Entity Search REST API Java használatával, és a egy JSON-választ kap.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-entity-search
ms.topic: quickstart
ms.date: 02/01/2019
ms.author: aahi
ms.openlocfilehash: 4a8f4fb3605f254bb48005e944a49fe45395aaec
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/07/2019
ms.locfileid: "57542522"
---
# <a name="quickstart-send-a-search-request-to-the-bing-entity-search-rest-api-using-java"></a>Gyors útmutató: Egy keresési kérelmet küld a Bing Entity Search REST API Java használatával

Ez a rövid útmutató segítségével a Bing Entity Search API az első hívását, és tekintse meg a JSON-választ. Az egyszerű Java-alkalmazás news search lekérdezést küld az API-t, és a válasz megjeleníti.

Bár ez az alkalmazás Java nyelven lett íródott, az API egy RESTful-webszolgáltatás, azaz kompatibilis a legtöbb programnyelvvel.

## <a name="prerequisites"></a>Előfeltételek

* A [Java fejlesztési Kit(JDK)](https://www.oracle.com/technetwork/java/javase/downloads/)
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

2. Hozzon létre egy új osztályt, változókat, az API-végpont, az előfizetési kulcs és a egy keresési lekérdezést.

  ```java
  public class EntitySearch {

      static String subscriptionKey = "ENTER KEY HERE";
    
        static String host = "https://api.cognitive.microsoft.com";
        static String path = "/bing/v7.0/entities";
    
        static String mkt = "en-US";
        static String query = "italian restaurant near me";
  //...
    
  ```

## <a name="construct-a-search-request-string"></a>Egy keresési kérelmet karakterlánc létrehozása

1. Hozzon létre egy függvényt, nevű `search()` , amely adja vissza egy JSON `String`. URL-cím-keresési lekérdezés kódolása, és adja hozzá a paraméterek karakterlánc `&q=`. A karakterláncot ad hozzá a piaci `?mkt=`.
 
2. Hozzon létre egy URL-cím-objektumot a gazdagép elérési útja és paraméterek karakterláncok.
    
    ```java
    //...
    public static String search () throws Exception {
        String encoded_query = URLEncoder.encode (query, "UTF-8");
        String params = "?mkt=" + mkt + "&q=" + encoded_query;
        URL url = new URL (host + path + params);
    //...
    ```
      
## <a name="send-a-search-request-and-receive-a-response"></a>A keresési kéréseket küldeni és fogadni a választ

1. Az a `search()` függvény a fent létrehozott, hozzon létre egy új `HttpsURLConnection` rendelkező objektum `url.openCOnnection()`. A kérelem módszert állítja be `GET`, és adja hozzá az előfizetési kulcs, a `Ocp-Apim-Subscription-Key` fejléc.

    ```java
    //...
    HttpsURLConnection connection = (HttpsURLConnection) url.openConnection();
    connection.setRequestMethod("GET");
    connection.setRequestProperty("Ocp-Apim-Subscription-Key", subscriptionKey);
    connection.setDoOutput(true);
    //...
    ```

2. Hozzon létre egy új `StringBuilder`. Egy új `InputStreamReader` hárítható el paraméterként `BufferedReader` , olvassa el az API-válasz.  
    
    ```java
    //...
    StringBuilder response = new StringBuilder ();
    BufferedReader in = new BufferedReader(
        new InputStreamReader(connection.getInputStream()));
    //...
    ```

3. Hozzon létre egy `String` objektum válasza tárolására a `BufferedReader`. Azt végigléptetni, és minden egyes sor hozzáfűzése karakterláncot. Majd zárja be az olvasó, és a választ adja vissza. 
    
    ```java
    String line;
    
    while ((line = in.readLine()) != null) {
      response.append(line);
    }
    in.close();
    
    return response.toString();
    ```

## <a name="format-the-json-response"></a>A JSON-válasz formázása

1. Hozzon létre egy új függvényt nevű `prettify` formázhatja a JSON-választ. Hozzon létre egy új `JsonParser`, és hívja `parse()` a json-szöveget, és JSON-objektumként tárolja. 

2. A Gson könyvtár segítségével hozzon létre egy új `GsonBuilder()`, és használja `setPrettyPrinting().create()` a json formázását. Majd vissza.    
  
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

## <a name="call-the-search-function"></a>A search függvény hívása

1. A projekt, a fő metódus hívása `search()`, és használja `prettify()` formázhatja a szöveget.
    
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

## <a name="example-json-response"></a>Példa JSON-válasz

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

[Vissza a tetejére](#main)

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Egyoldalas webes alkalmazás készítése](../tutorial-bing-entities-search-single-page-app.md)

* [Mi az a Bing Entity Search API?](../overview.md )
* [A Bing Entity Search API-referencia](https://docs.microsoft.com/rest/api/cognitiveservices/bing-entities-api-v7-reference)
