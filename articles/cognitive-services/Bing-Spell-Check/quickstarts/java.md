---
title: 'Gyors útmutató: Helyesírás ellenőrzése a Bing Spell Check REST API és a Javával'
titleSuffix: Azure Cognitive Services
description: Az Bing Spell Check REST API használatának első lépései a helyesírás és a nyelvtan ellenőrzéséhez.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-spell-check
ms.topic: quickstart
ms.date: 04/11/2019
ms.author: aahi
ms.openlocfilehash: 47b47a91b0cd223a17d6779d7f0db7cdb7c88a0b
ms.sourcegitcommit: 07700392dd52071f31f0571ec847925e467d6795
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/28/2019
ms.locfileid: "70123302"
---
# <a name="quickstart-check-spelling-with-the-bing-spell-check-rest-api-and-java"></a>Gyors útmutató: Helyesírás ellenőrzése a Bing Spell Check REST API és a Javával

Ezzel a rövid útmutatóval megteheti az első hívást a Bing Spell Check REST API. Ez az egyszerű Java-alkalmazás egy kérelmet küld az API-nak, és a javasolt javítások listáját adja vissza. Habár ez az alkalmazás Java nyelven íródott, az API egy REST-alapú webszolgáltatás, amely kompatibilis a legtöbb programozási nyelvvel. Az alkalmazás forráskódja elérhető a githubon. [](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/java/Search/BingSpellCheckv7.java)

## <a name="prerequisites"></a>Előfeltételek

* A Java Development Kit (JDK) 7-es vagy újabb verziója.

* Importálja a [gson-2.8.5. jar](https://libraries.io/maven/com.google.code.gson%3Agson) vagy a legújabb [gson](https://github.com/google/gson) -verziót. A parancssori végrehajtáshoz adja hozzá a `.jar` parancsot a Java-mappához a fő osztállyal.

[!INCLUDE [cognitive-services-bing-spell-check-signup-requirements](../../../../includes/cognitive-services-bing-spell-check-signup-requirements.md)]

## <a name="create-and-initialize-an-application"></a>Alkalmazás létrehozása és inicializálása

1. Hozzon létre egy új Java-projektet a kedvenc IDE-vagy szerkesztőjében a választott osztálynév használatával, majd importálja a következő csomagokat.

    ```java
    import java.io.*;
    import java.net.*;
    import com.google.gson.*;
    import javax.net.ssl.HttpsURLConnection;
    ```

2. Hozzon létre változókat az API-végpont gazdagépéhez, elérési útjához és előfizetési kulcsához. Ezután hozzon létre változókat a piacra, a helyesírás-ellenőrzéshez használni kívánt szövegre, valamint egy karakterláncot a helyesírás-ellenőrzési módhoz.

    ```java
    static String host = "https://api.cognitive.microsoft.com";
    static String path = "/bing/v7.0/spellcheck";

    static String key = "<ENTER-KEY-HERE>";

    static String mkt = "en-US";
    static String mode = "proof";
    static String text = "Hollo, wrld!";
    ```

## <a name="create-and-send-an-api-request"></a>API-kérelem létrehozása és elküldése

1. Hozzon létre egy `check()` nevű függvényt az API-kérelem létrehozásához és elküldéséhez. Ebben az esetben kövesse az alábbi lépéseket. Hozzon létre egy karakterláncot a kérés paramétereinek. fűzze hozzá `?mkt=` a paramétert a piaci karakterlánchoz, `&mode=` és a paramétert a helyesírás-ellenőrzési módba.  

   ```java
   public static void check () throws Exception {
       String params = "?mkt=" + mkt + "&mode=" + mode;
      // add the rest of the code snippets here (except prettify() and main())...
   }
   ```

2. Hozzon létre egy URL-címet a végpont-gazdagép, az elérési út és a paraméterek karakterláncának kombinálásával. Hozzon létre `HttpsURLConnection` egy új objektumot.

    ```java
    URL url = new URL(host + path + params);
    HttpsURLConnection connection = (HttpsURLConnection) url.openConnection();
    ```

3. Nyisson meg egy kapcsolódást az URL-címhez. A kérelem metódusának `POST`beállítása a következőre:. Adja meg a kérés paramétereit. Ügyeljen arra, hogy hozzáadja az előfizetési kulcsot `Ocp-Apim-Subscription-Key` a fejléchez.

    ```java
    connection.setRequestMethod("POST");
    connection.setRequestProperty("Content-Type", "application/x-www-form-urlencoded");
    connection.setRequestProperty("Ocp-Apim-Subscription-Key", key);
    connection.setDoOutput(true);
    ```

4. Hozzon létre `DataOutputStream` egy új objektumot, és küldje el a kérést az API-nak.

    ```java
        DataOutputStream wr = new DataOutputStream(connection.getOutputStream());
        wr.writeBytes("text=" + text);
        wr.flush();
        wr.close();
    ```

## <a name="format-and-read-the-api-response"></a>Az API-válasz formázása és olvasása

1. Adja hozzá ezt a metódust a osztályhoz. A JSON-t egy olvashatóbb kimenetre formázza.

    ``` java
    // This function prettifies the json response.
    public static String prettify(String json_text) {
        JsonParser parser = new JsonParser();
        JsonElement json = parser.parse(json_text);
        Gson gson = new GsonBuilder().setPrettyPrinting().create();
        return gson.toJson(json);
    }
    ```

1. Hozzon `BufferedReader` létre egy elemet, és olvassa el a választ az API-ból. Nyomtassa ki a-konzolra.
    
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

Az alkalmazás fő függvényében hívja meg a fent létrehozott ellenőrzési () metódust.
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

Hozza létre és futtassa a projektet.

Ha a parancssort használja, az alábbi parancsokkal hozhatja létre és futtathatja az alkalmazást.

**Építeni**
```bash
javac -classpath .;gson-2.2.2.jar\* <CLASS_NAME>.java
```

**Futtassa**
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
