---
title: 'Rövid útmutató: Bing Spell Check API, Java'
titlesuffix: Azure Cognitive Services
description: Információk és kódminták segítségével ismerkedhet meg a Bing Spell Check API használatának első lépéseivel.
services: cognitive-services
author: v-jaswel
manager: cgronlun
ms.service: cognitive-services
ms.component: bing-spell-check
ms.topic: quickstart
ms.date: 09/14/2017
ms.author: v-jaswel
ms.openlocfilehash: 00e0b7db5bfc8b763d9b16524bd783601d1ec4d8
ms.sourcegitcommit: 9eaf634d59f7369bec5a2e311806d4a149e9f425
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/05/2018
ms.locfileid: "48800995"
---
# <a name="quickstart-for-bing-spell-check-api-with-java"></a>Rövid útmutató a Bing Spell Check API a Javával való használatához 

Ez a cikk bemutatja, hogyan használhatja a [Bing Spell Check API-t](https://azure.microsoft.com/services/cognitive-services/spell-check/) a Javával. A Spell Check API visszaadja a fel nem ismert szavak listáját a javasolt cserekifejezésekkel együtt. Általános esetben küld egy szöveget az API-nak, majd végrehajtja a javasolt cseréket a szövegben, vagy megmutatja azokat az alkalmazás felhasználójának, hogy ő dönthesse el, végre szeretné-e hajtani a cserét. Ez a cikk bemutatja, hogyan küldhet el egy olyan kérést, amely a „Hollo, wrld!” szöveget tartalmazza. A javasolt cserekifejezések a „Hello” és a „world” lesznek.

## <a name="prerequisites"></a>Előfeltételek

A kód lefordításához és futtatásához a [JDK 7 vagy 8](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html) telepítése szükséges. Ha van kedvence, használhat Java IDE-t vagy egy szövegszerkesztőt is.

Rendelkeznie kell egy, a **Bing Spell Check API 7-es verzióját** tartalmazó [Cognitive Services API-fiókkal](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account). Az [ingyenes próbaverzió](https://azure.microsoft.com/try/cognitive-services/#lang) elegendő ehhez a rövid útmutatóhoz. Szüksége lesz az ingyenes próbaverzió aktiválásakor kapott hozzáférési kulcsra, vagy beszerezhet egy fizetős előfizetői azonosítót az Azure-irányítópultról.

## <a name="get-spell-check-results"></a>Spell Check-eredmények lekérése

1. Hozzon létre egy új Java-projektet kedvenc IDE-környezetében.
2. Adja hozzá az alábbi kódot.
3. A `subscriptionKey` értéket cserélje le az előfizetéshez érvényes hozzáférési kulcsra.
4. Futtassa a programot.

```java
import java.io.*;
import java.net.*;
import javax.net.ssl.HttpsURLConnection;

public class HelloWorld {

    static String host = "https://api.cognitive.microsoft.com";
    static String path = "/bing/v7.0/spellcheck";

    // NOTE: Replace this example key with a valid subscription key.
    static String key = "ENTER KEY HERE";

    static String mkt = "en-US";
    static String mode = "proof";
    static String text = "Hollo, wrld!";

    public static void check () throws Exception {
        String params = "?mkt=" + mkt + "&mode=" + mode;
        URL url = new URL(host + path + params);
        HttpsURLConnection connection = (HttpsURLConnection) url.openConnection();
        connection.setRequestMethod("POST");
        connection.setRequestProperty("Content-Type", "application/x-www-form-urlencoded");
        connection.setRequestProperty("Content-Length", "" + text.length() + 5);
        connection.setRequestProperty("Ocp-Apim-Subscription-Key", key);
        connection.setDoOutput(true);

        DataOutputStream wr = new DataOutputStream(connection.getOutputStream());
        wr.writeBytes("text=" + text);
        wr.flush();
        wr.close();

        BufferedReader in = new BufferedReader(
        new InputStreamReader(connection.getInputStream()));
        String line;
        while ((line = in.readLine()) != null) {
            System.out.println(line);
        }
        in.close();
    }

    public static void main(String[] args) {
        try {
            check ();
        }
        catch (Exception e) {
            System.out.println (e);
        }
    }
}
```

**Válasz**

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
> [Bing Spell Check – oktatóanyag](../tutorials/spellcheck.md)

## <a name="see-also"></a>Lásd még

- [A Bing Spell Check áttekintése](../proof-text.md)
- [A Bing Spell Check API 7-es verzió referenciája](https://docs.microsoft.com/rest/api/cognitiveservices/bing-spell-check-api-v7-reference)
