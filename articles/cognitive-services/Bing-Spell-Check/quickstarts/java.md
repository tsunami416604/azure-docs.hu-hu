---
title: 'Gyors útmutató: helyesírás ellenőrzése a REST API és a Java-Bing Spell Check'
titleSuffix: Azure Cognitive Services
description: Az Bing Spell Check REST API használatának első lépései a helyesírás és a nyelvtan ellenőrzéséhez.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-spell-check
ms.topic: quickstart
ms.date: 05/21/2020
ms.author: aahi
ms.openlocfilehash: f318a500bd4ce256690ff59f68d99af5d7a25d9e
ms.sourcegitcommit: 64fc70f6c145e14d605db0c2a0f407b72401f5eb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/27/2020
ms.locfileid: "83869805"
---
# <a name="quickstart-check-spelling-with-the-bing-spell-check-rest-api-and-java"></a>Gyors útmutató: helyesírás ellenőrzése a Bing Spell Check REST API és Javával

Ezzel a rövid útmutatóval megteheti az első hívást a Bing Spell Check REST API. Ez az egyszerű Java-alkalmazás egy kérelmet küld az API-nak, és a javasolt javítások listáját adja vissza. 

Bár ez az alkalmazás Java nyelven íródott, az API egy REST-alapú webszolgáltatás, amely kompatibilis a legtöbb programozási nyelvvel. Az alkalmazás forráskódja elérhető a [githubon](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/java/Search/BingSpellCheck.java).

## <a name="prerequisites"></a>Előfeltételek

* A Java Development Kit (JDK) 7-es vagy újabb verziója.

* Importálja a [gson-2.8.5. jar](https://libraries.io/maven/com.google.code.gson%3Agson) vagy a legújabb [gson](https://github.com/google/gson) -verziót. A parancssori végrehajtáshoz adja hozzá a parancsot a `.jar` Java-mappához a Main osztállyal.

[!INCLUDE [cognitive-services-bing-spell-check-signup-requirements](../../../../includes/cognitive-services-bing-spell-check-signup-requirements.md)]

## <a name="create-and-initialize-an-application"></a>Alkalmazás létrehozása és inicializálása

1. Hozzon létre egy új Java-projektet a kedvenc IDE-vagy szerkesztőjében a választott osztály nevével, majd importálja a következő csomagokat:

    ```java
    import java.io.*;
    import java.net.*;
    import com.google.gson.*;
    import javax.net.ssl.HttpsURLConnection;
    ```

2. Hozzon létre változókat az API-végpont gazdagépéhez, elérési útjához és előfizetési kulcsához. Ezután hozzon létre változókat a piacon, a helyesírás-ellenőrzéshez használni kívánt szöveget, valamint egy karakterláncot a helyesírás-ellenőrzési mód számára. Használhatja a globális végpontot a következő kódban, vagy használhatja az erőforráshoz tartozó Azure Portalban megjelenő [Egyéni altartomány](../../../cognitive-services/cognitive-services-custom-subdomains.md) -végpontot.

    ```java
    static String host = "https://api.cognitive.microsoft.com";
    static String path = "/bing/v7.0/spellcheck";

    static String key = "<ENTER-KEY-HERE>";

    static String mkt = "en-US";
    static String mode = "proof";
    static String text = "Hollo, wrld!";
    ```

## <a name="create-and-send-an-api-request"></a>API-kérelem létrehozása és elküldése

1. Hozzon létre egy nevű függvényt `check()` az API-kérelem létrehozásához és elküldéséhez. A függvényen belül adja hozzá a következő lépésekben megadott kódot. Karakterlánc létrehozása a kérés paramétereinek:

   a. Rendelje hozzá a piaci kódot a `mkt` paraméterhez az `=` operátorral. 

   b. Adja hozzá a `mode` paramétert a `&` kezelőhöz, majd rendelje hozzá a helyesírás-ellenőrzési módot. 

   ```java
   public static void check () throws Exception {
       String params = "?mkt=" + mkt + "&mode=" + mode;
      // add the rest of the code snippets here (except prettify() and main())...
   }
   ```

2. Hozzon létre egy URL-címet a végponti gazdagép, az elérési út és a paraméterek karakterláncának kombinálásával. Hozzon létre egy új `HttpsURLConnection` objektumot.

    ```java
    URL url = new URL(host + path + params);
    HttpsURLConnection connection = (HttpsURLConnection) url.openConnection();
    ```

3. Nyisson meg egy kapcsolódást az URL-címhez. Állítsa be a kérelem metódusát, `POST` és adja hozzá a kérelem paramétereit. Ügyeljen arra, hogy hozzáadja az előfizetési kulcsot a `Ocp-Apim-Subscription-Key` fejléchez.

    ```java
    connection.setRequestMethod("POST");
    connection.setRequestProperty("Content-Type", "application/x-www-form-urlencoded");
    connection.setRequestProperty("Ocp-Apim-Subscription-Key", key);
    connection.setDoOutput(true);
    ```

4. Hozzon létre egy új `DataOutputStream` objektumot, és küldje el a kérést az API-nak.

    ```java
        DataOutputStream wr = new DataOutputStream(connection.getOutputStream());
        wr.writeBytes("text=" + text);
        wr.flush();
        wr.close();
    ```

## <a name="format-and-read-the-api-response"></a>Az API-válasz formázása és olvasása

1. Adja hozzá a `prettify()` metódust a osztályhoz, amely egy olvashatóbb kimenetre formázza a JSON-t.

    ``` java
    // This function prettifies the json response.
    public static String prettify(String json_text) {
        JsonParser parser = new JsonParser();
        JsonElement json = parser.parse(json_text);
        Gson gson = new GsonBuilder().setPrettyPrinting().create();
        return gson.toJson(json);
    }
    ```

1. Hozzon létre egy `BufferedReader` elemet, és olvassa el a választ az API-ból. Nyomtassa ki a-konzolra.
    
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

Az alkalmazás fő függvényében hívja meg a `check()` korábban létrehozott metódust.
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

## <a name="run-the-application"></a>Alkalmazás futtatása

Hozza létre és futtassa a projektet. Ha a parancssort használja, az alábbi parancsokkal hozhatja létre és futtathatja az alkalmazást:

1. Hozza létre az alkalmazást:

   ```bash
   javac -classpath .;gson-2.2.2.jar\* <CLASS_NAME>.java
   ```

2. Futtassa az alkalmazást:

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
- [Bing Spell Check API v7-dokumentáció](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-spell-check-api-v7-reference)
