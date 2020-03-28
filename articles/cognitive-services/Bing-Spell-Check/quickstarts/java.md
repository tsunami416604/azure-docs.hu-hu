---
title: 'Rövid útmutató: Helyesírás-ellenőrzés a REST API-val és a Java - Bing helyesírás-ellenőrzéssel'
titleSuffix: Azure Cognitive Services
description: Első lépések a Bing Helyesírás-ellenőrző REST API-val a helyesírás és a nyelvhelyesség ellenőrzéséhez.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-spell-check
ms.topic: quickstart
ms.date: 12/16/2019
ms.author: aahi
ms.openlocfilehash: 43c528a1e9e46a67e895679b1a0fd04fef8900a7
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75382959"
---
# <a name="quickstart-check-spelling-with-the-bing-spell-check-rest-api-and-java"></a>Rövid útmutató: Helyesírás-ellenőrzés a Bing helyesírás-ellenőrző REST API-val és a Java-val

Ezzel a rövid útmutatóval elsőként hívhatja meg a Bing Helyesírás-ellenőrző REST API-t. Ez az egyszerű Java-alkalmazás kérelmet küld az API-nak, és visszaadja a javasolt javítások listáját. Bár ez az alkalmazás Java nyelven íródott, az API egy RESTful webszolgáltatás, amely kompatibilis a legtöbb programozási nyelvvel. Az alkalmazás forráskódja elérhető a [GitHubon.](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/java/Search/BingSpellCheckv7.java)

## <a name="prerequisites"></a>Előfeltételek

* A Java Development Kit(JDK) 7 vagy újabb.

* Importálja a [gson-2.8.5.jar](https://libraries.io/maven/com.google.code.gson%3Agson) vagy a legfrissebb [Gson](https://github.com/google/gson) verzió. A parancssori végrehajtáshoz adja hozzá a `.jar` főosztályt tartalmazó Java mappához.

[!INCLUDE [cognitive-services-bing-spell-check-signup-requirements](../../../../includes/cognitive-services-bing-spell-check-signup-requirements.md)]

## <a name="create-and-initialize-an-application"></a>Alkalmazás létrehozása és inicializálása

1. Hozzon létre egy új Java Projektet kedvenc IDE-jében vagy szerkesztőjében az Ön által választott osztálynévvel, majd importálja a következő csomagokat.

    ```java
    import java.io.*;
    import java.net.*;
    import com.google.gson.*;
    import javax.net.ssl.HttpsURLConnection;
    ```

2. Hozzon létre változókat az API-végpont gazdagépéhez, elérési útjának és az előfizetési kulcshoz. Ezután hozzon létre változókat a piachoz, a helyesírás-ellenőrzéshez kívánt szöveghez, és egy karakterláncot a helyesírás-ellenőrzési módhoz. Használhatja az alábbi globális végpontot, vagy az [egyéni altartomány-végpontot,](../../../cognitive-services/cognitive-services-custom-subdomains.md) amely az azure-portálon jelenik meg az erőforráshoz.

    ```java
    static String host = "https://api.cognitive.microsoft.com";
    static String path = "/bing/v7.0/spellcheck";

    static String key = "<ENTER-KEY-HERE>";

    static String mkt = "en-US";
    static String mode = "proof";
    static String text = "Hollo, wrld!";
    ```

## <a name="create-and-send-an-api-request"></a>API-kérelem létrehozása és küldése

1. Hozzon létre `check()` egy függvényt, amelynek célja az API-kérelem létrehozása és küldése. Belül kövesse ezeket a lépéseket. Hozzon létre egy karakterláncot a kérelem paramétereihez. fűzze `?mkt=` hozzá a paramétert a `&mode=` piaci karakterlánchoz, és a paramétert a helyesírás-ellenőrző módhoz.  

   ```java
   public static void check () throws Exception {
       String params = "?mkt=" + mkt + "&mode=" + mode;
      // add the rest of the code snippets here (except prettify() and main())...
   }
   ```

2. Hozzon létre egy URL-címet a végpontállomás, az elérési út és a paraméterek karakterláncának kombinálásával. Hozzon `HttpsURLConnection` létre egy új objektumot.

    ```java
    URL url = new URL(host + path + params);
    HttpsURLConnection connection = (HttpsURLConnection) url.openConnection();
    ```

3. Nyisson meg egy kapcsolatot az URL-címmel. Állítsa a kérésmetódust a beállításra. `POST` Adja hozzá a kérelem paramétereit. Győződjön meg arról, `Ocp-Apim-Subscription-Key` hogy hozzáadja az előfizetési kulcsot a fejléchez.

    ```java
    connection.setRequestMethod("POST");
    connection.setRequestProperty("Content-Type", "application/x-www-form-urlencoded");
    connection.setRequestProperty("Ocp-Apim-Subscription-Key", key);
    connection.setDoOutput(true);
    ```

4. Hozzon `DataOutputStream` létre egy új objektumot, és küldje el a kérelmet az API-nak.

    ```java
        DataOutputStream wr = new DataOutputStream(connection.getOutputStream());
        wr.writeBytes("text=" + text);
        wr.flush();
        wr.close();
    ```

## <a name="format-and-read-the-api-response"></a>Az API-válasz formázása és olvasása

1. Adja hozzá ezt a módszert az osztályhoz. Formázza a JSON egy olvashatóbb kimenet.

    ``` java
    // This function prettifies the json response.
    public static String prettify(String json_text) {
        JsonParser parser = new JsonParser();
        JsonElement json = parser.parse(json_text);
        Gson gson = new GsonBuilder().setPrettyPrinting().create();
        return gson.toJson(json);
    }
    ```

1. Hozzon `BufferedReader` létre egy, és olvassa el a választ az API-ból. Nyomtassa ki a konzolra.
    
    ```java
    BufferedReader in = new BufferedReader(
    new InputStreamReader(connection.getInputStream()));
    String line;
    while ((line = in.readLine()) != null) {
        System.out.println(prettify(line));
    }
    in.close();
    ```

## <a name="call-the-api"></a>Az API meghívása

A fő funkciója az alkalmazás, hívja meg a check() módszer a fent létrehozott.
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

Készítse el és futtassa a projektet.

Ha a parancssort használja, az alkalmazás létrehozásához és futtatásához használja az alábbi parancsokat.

**Épít:**
```bash
javac -classpath .;gson-2.2.2.jar\* <CLASS_NAME>.java
```

**Fuss:**
```bash
java -cp .;gson-2.2.2.jar\* <CLASS_NAME>
```

## <a name="example-json-response"></a>Példa JSON-válaszra

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

- [Mi a Bing Spell Check API?](../overview.md)
- [A Bing Spell Check API 7-es verzió referenciája](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-spell-check-api-v7-reference)
