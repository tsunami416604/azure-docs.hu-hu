---
title: 'Gyors útmutató: Helyesírás-ellenőrzés a Bing Spell Check REST API és a Java'
titlesuffix: Azure Cognitive Services
description: Ismerkedés a Bing Spell Check REST API használatával a helyesírás-és nyelvtani.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-spell-check
ms.topic: quickstart
ms.date: 04/11/2019
ms.author: aahi
ms.openlocfilehash: a139d0558565114725c6198f64e139e5a5019c75
ms.sourcegitcommit: fec96500757e55e7716892ddff9a187f61ae81f7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2019
ms.locfileid: "59616695"
---
# <a name="quickstart-check-spelling-with-the-bing-spell-check-rest-api-and-java"></a>Gyors útmutató: Helyesírás-ellenőrzés a Bing Spell Check REST API és a Java

Ez a rövid útmutató segítségével, a Bing Spell Check REST API első hívását. Ez egyszerű Java-alkalmazás egy kérést küld az API-t, és javasolt javítások listáját adja vissza. Ezt az alkalmazást a Java nyelven van megírva, míg a API-ját egy REST-alapú webszolgáltatás szinte bármelyik programozási nyelvével kompatibilis. Az alkalmazás forráskódja elérhető a [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/java/Search/BingSpellCheckv7.java).

## <a name="prerequisites"></a>Előfeltételek

* A Java fejlesztési Kit(JDK), 7 vagy újabb.

* Importálás a [gson-2.8.5.jar](https://libraries.io/maven/com.google.code.gson%3Agson) vagy legfrissebb [Gson](https://github.com/google/gson) verzió. Parancssori végrehajtási, vegye fel a `.jar` a Java-mappába, a fő osztállyal.

[!INCLUDE [cognitive-services-bing-spell-check-signup-requirements](../../../../includes/cognitive-services-bing-spell-check-signup-requirements.md)]

## <a name="create-and-initialize-an-application"></a>Hozzon létre, és a egy alkalmazás inicializálása

1. Hozzon létre egy új Java-projektet a kedvenc IDE- vagy egy osztálynév tetszőleges szerkesztőben, és importálja a következő csomagok.

    ```java
    import java.io.*;
    import java.net.*;
    import com.google.gson.*;
    import javax.net.ssl.HttpsURLConnection;
    ```

2. Változók létrehozása az API-végpont állomás, elérési út és az előfizetési kulcs. Ezután hozzon létre változókat a piacra, helyesírás-ellenőrzés kívánt szöveg, és a karakterlánc a helyesírás-ellenőrzési mód.

    ```java
    static String host = "https://api.cognitive.microsoft.com";
    static String path = "/bing/v7.0/spellcheck";

    static String key = "<ENTER-KEY-HERE>";

    static String mkt = "en-US";
    static String mode = "proof";
    static String text = "Hollo, wrld!";
    ```

## <a name="create-and-send-an-api-request"></a>Hozzon létre, és a egy API-kérelem küldése

1. Hozzon létre egy függvényt, nevű `check()` hozhat létre, és az API-kérelem küldése. Benne kövesse az alábbi lépéseket. Hozzon létre egy karakterláncot a kérelem paramétereit. fűzze hozzá a `?mkt=` paramétert a piaci karakterláncot, és a `&mode=` paraméter a helyesírás-ellenőrzés módra.  

   ```java
   public static void check () throws Exception {
       String params = "?mkt=" + mkt + "&mode=" + mode;
      // add the rest of the code snippets here (except prettify() and main())...
   }
   ```

2. Hozzon létre egy URL-címet a végponti gazdagép elérési útvonalát, és a paraméterek karakterlánc kombinálásával. Hozzon létre egy új `HttpsURLConnection` objektum.

    ```java
    URL url = new URL(host + path + params);
    HttpsURLConnection connection = (HttpsURLConnection) url.openConnection();
    ```

3. Nyissa meg az URL-kapcsolatot. A kérelem módszert állítja be `POST`. Adja hozzá a kérelem paramétereit. Ne felejtse el hozzáadni az előfizetési kulcs, a `Ocp-Apim-Subscription-Key` fejléc.

    ```java
    connection.setRequestMethod("POST");
    connection.setRequestProperty("Content-Type", "application/x-www-form-urlencoded");
    connection.setRequestProperty("Ocp-Apim-Subscription-Key", key);
    connection.setDoOutput(true);
    ```

4. Hozzon létre egy új `DataOutputStream` objektumra, és az API-hoz való elküldéséhez.

    ```java
        DataOutputStream wr = new DataOutputStream(connection.getOutputStream());
        wr.writeBytes("text=" + text);
        wr.flush();
        wr.close();
    ```

## <a name="format-and-read-the-api-response"></a>Formázhatja és az API-válasz olvasása

1. Adja hozzá ezt a módszert az osztályhoz. A JSON-t egy olvashatóbb kimeneti formázza azt.

    ``` java
    // This function prettifies the json response.
    public static String prettify(String json_text) {
        JsonParser parser = new JsonParser();
        JsonElement json = parser.parse(json_text);
        Gson gson = new GsonBuilder().setPrettyPrinting().create();
        return gson.toJson(json);
    }

1. Create a `BufferedReader` and read the response from the API. Print it to the console.
    
    ```java
    BufferedReader in = new BufferedReader(
    new InputStreamReader(connection.getInputStream()));
    String line;
    while ((line = in.readLine()) != null) {
        System.out.println(prettify(line);
    }
    in.close();
    ```

## <a name="call-the-api"></a>Az API meghívása

Az alkalmazás fő függvényét a fent létrehozott check() metódust hívja.

    ```java
    public static void main(String[] args) {
        try {
            check();
        }
        catch (Exception e) {
            System.out.println (e);
        }
    }
    ```

## <a name="run-the-application"></a>Az alkalmazás futtatása

Hozhat létre, és futtassa a projektet.

Ha a parancssor használata esetén használatával a következő parancsokat az alkalmazás fordításához és futtatásához.

**Hozhat létre:**
```bash
javac -classpath .;gson-2.2.2.jar\* <CLASS_NAME>.java
```

**Futtassa:**
```bash
java -cp .;gson-2.2.2.jar\* <CLASS_NAME>
```

## <a name="example-json-response"></a>Példa JSON-válasz

A rendszer JSON formátumban ad vissza egy sikeres választ a következő példában látható módon:

```json
{
   "_type": "SpellCheck",
   "flaggedTokens": [
      {
         "offset": 0,
         "token": "Hollo",
         "type": "UnknownToken",
         "suggestions": [
            {
               "suggestion": "Hello",
               "score": 0.9115257530801
            },
            {
               "suggestion": "Hollow",
               "score": 0.858039839213461
            },
            {
               "suggestion": "Hallo",
               "score": 0.597385084464481
            }
         ]
      },
      {
         "offset": 7,
         "token": "wrld",
         "type": "UnknownToken",
         "suggestions": [
            {
               "suggestion": "world",
               "score": 0.9115257530801
            }
         ]
      }
   ]
}
```

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Egyoldalas webalkalmazás létrehozása](../tutorials/spellcheck.md)

- [Mi az a Bing Spell Check API?](../overview.md)
- [A Bing Spell Check API 7-es verzió referenciája](https://docs.microsoft.com/rest/api/cognitiveservices/bing-spell-check-api-v7-reference)
