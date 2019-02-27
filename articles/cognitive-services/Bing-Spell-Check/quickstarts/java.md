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
ms.date: 02/20/2019
ms.author: aahi
ms.openlocfilehash: d2905d05dce48b705de44780425ed2b55b02555c
ms.sourcegitcommit: 24906eb0a6621dfa470cb052a800c4d4fae02787
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/27/2019
ms.locfileid: "56888984"
---
# <a name="quickstart-check-spelling-with-the-bing-spell-check-rest-api-and-java"></a>Gyors útmutató: Helyesírás-ellenőrzés a Bing Spell Check REST API és a Java

Ez a rövid útmutató segítségével, a Bing Spell Check REST API első hívását. Ez egyszerű Java-alkalmazás egy kérést küld az API-t, és javasolt javítások listáját adja vissza. Ezt az alkalmazást a Java nyelven van megírva, míg a API-ját egy REST-alapú webszolgáltatás szinte bármelyik programozási nyelvével kompatibilis. Az alkalmazás forráskódja elérhető a [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/java/Search/BingSpellCheckv7.java).

## <a name="prerequisites"></a>Előfeltételek

A Java fejlesztési Kit(JDK), 7 vagy újabb.

[!INCLUDE [cognitive-services-bing-spell-check-signup-requirements](../../../../includes/cognitive-services-bing-spell-check-signup-requirements.md)]


## <a name="create-and-initialize-an-application"></a>Hozzon létre, és a egy alkalmazás inicializálása

1. Hozzon létre egy új Java-projektet a kedvenc integrált Fejlesztőkörnyezetével vagy szerkesztőjével, és a következő csomagok importálásához.

    ```java
    import java.io.*;
    import java.net.*;
    import javax.net.ssl.HttpsURLConnection;
    ```

2. Változók létrehozása az API-végpont állomás, elérési út és az előfizetési kulcs. Ezután hozzon létre változókat a piacra, helyesírás-ellenőrzés kívánt szöveg, és a karakterlánc a helyesírás-ellenőrzési mód.

    ```java
    static String host = "https://api.cognitive.microsoft.com";
    static String path = "/bing/v7.0/spellcheck";

    static String key = "ENTER YOUR KEY HERE";

    static String mkt = "en-US";
    static String mode = "proof";
    static String text = "Hollo, wrld!";
    ```

## <a name="create-and-send-an-api-request"></a>Hozzon létre, és a egy API-kérelem küldése

1. Hozzon létre egy függvényt, nevű `check()` hozhat létre, és az API-kérelem küldése. Benne kövesse az alábbi lépéseket. Hozzon létre egy karakterláncot a kérelem paramétereit. fűzze hozzá a `?mkt=` paramétert a piaci karakterláncot, és a `&mode=` paraméter a helyesírás-ellenőrzés módra.  

   ```java
   public static void check () throws Exception {
       String params = "?mkt=" + mkt + "&mode=" + mode;
   //...
   }
   ```

2. Hozzon létre egy URL-címet a végponti gazdagép elérési útvonalát, és a paraméterek karakterlánc kombinálásával. Hozzon létre egy új `HttpsURLConnection` obejct.

    ```java
    URL url = new URL(host + path + params);
    HttpsURLConnection connection = (HttpsURLConnection) 
    ```

3. Nyissa meg az URL-kapcsolatot. A kérelem módszert állítja be `POST`. Adja hozzá a kérelem paramétereit. Ne felejtse el hozzáadni az előfizetési kulcs, a `Ocp-Apim-Subscription-Key` fejléc. 

    ```java
    url.openConnection();
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

## <a name="read-the-response"></a>A válasz olvasása

1. Hozzon létre egy `BufferedReader` és olvassa el a választ az API-ból. Nyomtassa ki a konzolhoz.
    
    ```java
    BufferedReader in = new BufferedReader(
    new InputStreamReader(connection.getInputStream()));
    String line;
    while ((line = in.readLine()) != null) {
        System.out.println(line);
    }
    in.close();
    ```

2. Az alkalmazás fő függvényét hívja meg a fent létrehozott függvényt. 

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
