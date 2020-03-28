---
title: 'Rövid útmutató: Keresési lekérdezések javaslata a Bing Autosuggest REST API-val és a Java-val'
titleSuffix: Azure Cognitive Services
description: Ismerje meg, hogyan kezdheti el gyorsan a keresési kifejezések et valós időben javasolni a Bing Autosuggest API-val.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-autosuggest
ms.topic: quickstart
ms.date: 03/24/2020
ms.author: aahi
ms.openlocfilehash: b3f279ea50e9923e63f7d6090f4dbaca939eb16c
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "80238973"
---
# <a name="quickstart-suggest-search-queries-with-the-bing-autosuggest-rest-api-and-java"></a>Rövid útmutató: Keresési lekérdezések javaslata a Bing Autosuggest REST API-val és a Java-val


Ezzel a rövid útmutatóval kezdeményezhet hívásokat a Bing Autosuggest API-hoz, és megkezdheti a JSON-választ. Ez az egyszerű Java-alkalmazás részleges keresési lekérdezést küld az API-nak, és javaslatokat ad vissza a kereséshez. Bár ez az alkalmazás Java nyelven lett íródott, az API egy RESTful-webszolgáltatás, azaz kompatibilis a legtöbb programnyelvvel. A minta forráskódja elérhető a [GitHubon](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/java/Search/BingAutosuggestv7.java)

## <a name="prerequisites"></a>Előfeltételek

* A [Java Fejlesztői Készlet (JDK)](https://www.oracle.com/technetwork/java/javase/downloads/)
* A [Gson-kódtár](https://github.com/google/gson)

[!INCLUDE [cognitive-services-bing-news-search-signup-requirements](../../../../includes/cognitive-services-bing-autosuggest-signup-requirements.md)]

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
    ```

2. Hozzon létre változókat az előfizetési kulcshoz, az API-állomáshoz és elérési úthoz, a [piaci kódhoz](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-autosuggest-api-v7-reference#market-codes)és egy keresési lekérdezéshez. Használhatja az alábbi globális végpontot, vagy az [egyéni altartomány-végpontot,](../../../cognitive-services/cognitive-services-custom-subdomains.md) amely az azure-portálon jelenik meg az erőforráshoz.
    
    ```java
    static String subscriptionKey = "enter key here";
    static String host = "https://api.cognitive.microsoft.com";
    static String path = "/bing/v7.0/Suggestions";
    static String mkt = "en-US";
    static String query = "sail";
    ```


## <a name="format-the-response"></a>A válasz formázása

Hozzon létre `prettify()` egy nevű módszert a Bing Video API-ból visszaadott válasz formázásához. A Gson-könyvtár `JsonParser` segítségével vegyen fel egy JSON-karakterláncot, és alakítsa át objektummá. Ezután `GsonBuilder()` `toJson()` használja, és hozza létre a formázott karakterláncot.

```java
// pretty-printer for JSON; uses GSON parser to parse and re-serialize
public static String prettify(String json_text) {
    JsonParser parser = new JsonParser();
    JsonObject json = parser.parse(json_text).getAsJsonObject();
    Gson gson = new GsonBuilder().setPrettyPrinting().create();
    return gson.toJson(json);
}
```

## <a name="construct-and-send-the-search-request"></a>A keresési kérelem megépítése és elküldése

1. Hozzon létre `get_suggestions()` egy új metódust, amelynek neve megvan, és hajtsa végre a következő lépéseket:

   1. az API-állomás, az elérési út és a keresési lekérdezés kódolása segítségével hozhatja létre a kérés URL-címét. Ügyeljen arra, hogy url-kódolni a lekérdezést, mielőtt hozzáfűzi azt. Hozzon létre egy paraméterkarakterláncot a lekérdezéshez `mkt=` a piaci kód nak `q=` a paraméterhez és a lekérdezéshez való hozzáfűzésével.
    
      ```java
  
      public static String get_suggestions () throws Exception {
         String encoded_query = URLEncoder.encode (query, "UTF-8");
         String params = "?mkt=" + mkt + "&q=" + encoded_query;
         //...
      }
      ```
    
   2. Hozzon létre egy új URL-címet a kérelemhez a fent létrehozott API-állomással, elérési úttal és paraméterekkel. 
    
       ```java
       //...
       URL url = new URL (host + path + params);
       //...
       ```
    
   3. Hozzon `HttpsURLConnection` létre egy `openConnection()` objektumot, és használja a kapcsolatot. Állítsa be a `GET`kérelem módszert , és `Ocp-Apim-Subscription-Key` adja hozzá az előfizetési kulcsot a fejléchez.

      ```java
       //...
       HttpsURLConnection connection = (HttpsURLConnection) url.openConnection();
       connection.setRequestMethod("GET");
       connection.setRequestProperty("Ocp-Apim-Subscription-Key", subscriptionKey);
       connection.setDoOutput(true);
       //...
      ```

   4. Olvassa el az API-válasz ban a `StringBuilder`. A válasz rögzítése után zárja `InputStreamReader` be az adatfolyamot, és adja vissza a választ.

       ```java
       //...
       StringBuilder response = new StringBuilder ();
       BufferedReader in = new BufferedReader(
       new InputStreamReader(connection.getInputStream()));
       String line;
       while ((line = in.readLine()) != null) {
         response.append(line);
       }
       in.close();
    
       return response.toString();
       ```

2. Az alkalmazás fő funkciójában `get_suggestions()`hívja meg a `prettify()`programot, és nyomtassa ki a választ a használatával.
    
    ```java
    public static void main(String[] args) {
      try {
        String response = get_suggestions ();
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
  "_type": "Suggestions",
  "queryContext": {
    "originalQuery": "sail"
  },
  "suggestionGroups": [
    {
      "name": "Web",
      "searchSuggestions": [
        {
          "url": "https://www.bing.com/cr?IG\u003d2ACC4FE8B02F4AACB9182A6502B0E556\u0026CID\u003d1D546424A4CB64AF2D386F26A5CD6583\u0026rd\u003d1\u0026h\u003dgvtP9TS9NwhajSapY2Se6y1eCbP2fq_GiP2n-cxi6OY\u0026v\u003d1\u0026r\u003dhttps%3a%2f%2fwww.bing.com%2fsearch%3fq%3dsailrite%26FORM%3dUSBAPI\u0026p\u003dDevEx,5003.1",
          "displayText": "sailrite",
          "query": "sailrite",
          "searchKind": "WebSearch"
        },
        {
          "url": "https://www.bing.com/cr?IG\u003d2ACC4FE8B02F4AACB9182A6502B0E556\u0026CID\u003d1D546424A4CB64AF2D386F26A5CD6583\u0026rd\u003d1\u0026h\u003dBTS0G6AakxntIl9rmbDXtk1n6rQpsZZ99aQ7ClE7dTY\u0026v\u003d1\u0026r\u003dhttps%3a%2f%2fwww.bing.com%2fsearch%3fq%3dsail%2bsand%2bpoint%26FORM%3dUSBAPI\u0026p\u003dDevEx,5004.1",
          "displayText": "sail sand point",
          "query": "sail sand point",
          "searchKind": "WebSearch"
        },
        {
          "url": "https://www.bing.com/cr?IG\u003d2ACC4FE8B02F4AACB9182A6502B0E556\u0026CID\u003d1D546424A4CB64AF2D386F26A5CD6583\u0026rd\u003d1\u0026h\u003dc0QOA_j6swCZJy9FxqOwke2KslJE7ZRmMooGClAuCpY\u0026v\u003d1\u0026r\u003dhttps%3a%2f%2fwww.bing.com%2fsearch%3fq%3dsailboats%2bfor%2bsale%26FORM%3dUSBAPI\u0026p\u003dDevEx,5005.1",
          "displayText": "sailboats for sale",
          "query": "sailboats for sale",
          "searchKind": "WebSearch"
        },
        {
          "url": "https://www.bing.com/cr?IG\u003d2ACC4FE8B02F4AACB9182A6502B0E556\u0026CID\u003d1D546424A4CB64AF2D386F26A5CD6583\u0026rd\u003d1\u0026h\u003dmnMdREUH20SepmHQH1zlh9Hy_w7jpOlZFm3KG2R_BoA\u0026v\u003d1\u0026r\u003dhttps%3a%2f%2fwww.bing.com%2fsearch%3fq%3dsailing%2banarchy%26FORM%3dUSBAPI\u0026p\u003dDevEx,5006.1",
          "displayText": "sailing anarchy",
          "query": "sailing anarchy",
          "searchKind": "WebSearch"
        },
        {
          "url": "https://www.bing.com/cr?IG\u003d2ACC4FE8B02F4AACB9182A6502B0E556\u0026CID\u003d1D546424A4CB64AF2D386F26A5CD6583\u0026rd\u003d1\u0026h\u003dWLFO-B1GG5qtBGnoU1Bizz02YKkg5fgAQtHwhXn4z8I\u0026v\u003d1\u0026r\u003dhttps%3a%2f%2fwww.bing.com%2fsearch%3fq%3dsailpoint%26FORM%3dUSBAPI\u0026p\u003dDevEx,5007.1",
          "displayText": "sailpoint",
          "query": "sailpoint",
          "searchKind": "WebSearch"
        },
        {
          "url": "https://www.bing.com/cr?IG\u003d2ACC4FE8B02F4AACB9182A6502B0E556\u0026CID\u003d1D546424A4CB64AF2D386F26A5CD6583\u0026rd\u003d1\u0026h\u003dquBMwmKlGwqC5wAU0K7n416plhWcR8zQCi7r-Fw9Y0w\u0026v\u003d1\u0026r\u003dhttps%3a%2f%2fwww.bing.com%2fsearch%3fq%3dsailflow%26FORM%3dUSBAPI\u0026p\u003dDevEx,5008.1",
          "displayText": "sailflow",
          "query": "sailflow",
          "searchKind": "WebSearch"
        },
        {
          "url": "https://www.bing.com/cr?IG\u003d2ACC4FE8B02F4AACB9182A6502B0E556\u0026CID\u003d1D546424A4CB64AF2D386F26A5CD6583\u0026rd\u003d1\u0026h\u003d0udadFl0gCTKCp0QmzQTXS3_y08iO8FpwsoKPHPS6kw\u0026v\u003d1\u0026r\u003dhttps%3a%2f%2fwww.bing.com%2fsearch%3fq%3dsailboatdata%26FORM%3dUSBAPI\u0026p\u003dDevEx,5009.1",
          "displayText": "sailboatdata",
          "query": "sailboatdata",
          "searchKind": "WebSearch"
        },
        {
          "url": "https://www.bing.com/cr?IG\u003d2ACC4FE8B02F4AACB9182A6502B0E556\u0026CID\u003d1D546424A4CB64AF2D386F26A5CD6583\u0026rd\u003d1\u0026h\u003deSSt0MRSbl2V0RFPSuVd-gC7fGOT4717pz55EBUgPec\u0026v\u003d1\u0026r\u003dhttps%3a%2f%2fwww.bing.com%2fsearch%3fq%3dsailor%2b2025%26FORM%3dUSBAPI\u0026p\u003dDevEx,5010.1",
          "displayText": "sailor 2025",
          "query": "sailor 2025",
          "searchKind": "WebSearch"
        }
      ]
    }
  ]
}
```

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Egyoldalas webalkalmazás létrehozása](../tutorials/autosuggest.md)

- [Mi a Bing Autosuggest?](../get-suggested-search-terms.md)
- [A Bing Autosuggest API 7-es verziójának referenciája](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-autosuggest-api-v7-reference)
