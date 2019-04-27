---
title: 'Gyors útmutató: A szövegelemzési API meghívására Java használatával'
titleSuffix: Azure Cognitive Services
description: Get information és kód minták segítségével gyorsan Ismerkedés a szövegelemzési API-val az Azure Cognitive Servicesben.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: quickstart
ms.date: 04/16/2019
ms.author: aahi
ms.openlocfilehash: fc848feb3f9a0e1160a8e36014ca4a469f792c96
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2019
ms.locfileid: "60829344"
---
# <a name="quickstart-using-java-to-call-the-text-analytics-cognitive-service"></a>Gyors útmutató: A Text Analytics kognitív szolgáltatás hívásához Java használatával
<a name="HOLTop"></a>

Ez a cikk bemutatja, hogyan való [nyelvfelismerés](#Detect), [vélemények elemzése](#SentimentAnalysis), [kinyerheti a kulcskifejezéseket](#KeyPhraseExtraction), és [kapcsolt entitások azonosítása](#Entities) használatával a [Text Analytics API-k](//go.microsoft.com/fwlink/?LinkID=759711) Java használatával.

Az API-k műszaki dokumentációjáért lásd az [API-definíciókat](//go.microsoft.com/fwlink/?LinkID=759346).

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [cognitive-services-text-analytics-signup-requirements](../../../../includes/cognitive-services-text-analytics-signup-requirements.md)]

A regisztráció során létrejött [végponttal és hozzáférési kulccsal](../How-tos/text-analytics-how-to-access-key.md) is rendelkeznie kell.

<a name="Detect"></a>

## <a name="detect-language"></a>Nyelv felismerése

A nyelvi API-t észleli a szöveg nyelvének dokumentálja, használja a [nyelv észlelése metódus](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/56f30ceeeda5650db055a3c7).

1. Hozzon létre egy új Java-projektet a kedvenc ide-je (vagy az asztal új mappa). Hozzon létre egy osztályt `DetectLanguage.java`.
1. Adja hozzá a kódot lejjebb találja az osztályhoz.
1. Cserélje le a `accessKey` értéket a kulcsát, a Szövegelemzés előfizetéshez [Azure](https://ms.portal.azure.com).
1. Cserélje le a `host` helyét (jelenleg `westus`) a regisztrált régióra.
1. Ellenőrizze, hogy a [Gson](https://github.com/google/gson) telepített könyvtár.
1. Futtassa a programot az IDE-ben, vagy a parancssor használatával (a kód megjegyzéseket utasításait) futtatása.

```java
import java.io.*;
import java.net.*;
import java.util.*;
import javax.net.ssl.HttpsURLConnection;

/*
 * Gson: https://github.com/google/gson
 * Maven info:
 *     groupId: com.google.code.gson
 *     artifactId: gson
 *     version: 2.8.1
 *
 * Once you have compiled or downloaded gson-2.8.1.jar, assuming you have placed it in the
 * same folder as this file (DetectLanguage.java), you can compile and run this program at
 * the command line as follows.
 *
 * Execute the following two commands to build and run (change gson version if needed):
 * javac DetectLanguage.java -classpath .;gson-2.8.1.jar -encoding UTF-8
 * java -cp .;gson-2.8.1.jar DetectLanguage
 */
import com.google.gson.Gson;
import com.google.gson.GsonBuilder;
import com.google.gson.JsonObject;
import com.google.gson.JsonParser;

class Document {
    public String id, text;

    public Document(String id, String text){
        this.id = id;
        this.text = text;
    }
}

class Documents {
    public List<Document> documents;

    public Documents() {
        this.documents = new ArrayList<Document>();
    }
    public void add(String id, String text) {
        this.documents.add (new Document (id, text));
    }
}

public class DetectLanguage {

// ***********************************************
// *** Update or verify the following values. ***
// **********************************************

// Replace the accessKey string value with your valid access key.
    static String accessKey = "enter key here";

// Replace or verify the region.

// You must use the same region in your REST API call as you used to obtain your access keys.
// For example, if you obtained your access keys from the westus region, replace 
// "westcentralus" in the URI below with "westus".

// NOTE: Free trial access keys are generated in the westcentralus region, so if you are using
// a free trial access key, you should not need to change this region.
    static String host = "https://westus.api.cognitive.microsoft.com";

    static String path = "/text/analytics/v2.1/languages";
    
    public static String GetLanguage (Documents documents) throws Exception {
        String text = new Gson().toJson(documents);
        byte[] encoded_text = text.getBytes("UTF-8");

        URL url = new URL(host+path);
        HttpsURLConnection connection = (HttpsURLConnection) url.openConnection();
        connection.setRequestMethod("POST");
        connection.setRequestProperty("Content-Type", "text/json");
        connection.setRequestProperty("Ocp-Apim-Subscription-Key", accessKey);
        connection.setDoOutput(true);

        DataOutputStream wr = new DataOutputStream(connection.getOutputStream());
        wr.write(encoded_text, 0, encoded_text.length);
        wr.flush();
        wr.close();

        StringBuilder response = new StringBuilder ();
        BufferedReader in = new BufferedReader(
        new InputStreamReader(connection.getInputStream()));
        String line;
        while ((line = in.readLine()) != null) {
            response.append(line);
        }
        in.close();

        return response.toString();
    }

    public static String prettify(String json_text) {
        JsonParser parser = new JsonParser();
        JsonObject json = parser.parse(json_text).getAsJsonObject();
        Gson gson = new GsonBuilder().setPrettyPrinting().create();
        return gson.toJson(json);
    }

    public static void main (String[] args) {
        try {
            Documents documents = new Documents ();
            documents.add ("1", "This is a document written in English.");
            documents.add ("2", "Este es un document escrito en Español.");
            documents.add ("3", "这是一个用中文写的文件");

            String response = GetLanguage (documents);
            System.out.println (prettify (response));
        }
        catch (Exception e) {
            System.out.println (e);
        }
    }
}
```

### <a name="language-detection-response"></a>Nyelv észlelése válasz

A rendszer JSON formátumban ad vissza egy sikeres választ a következő példában látható módon: 

```json

{
   "documents": [
      {
         "id": "1",
         "detectedLanguages": [
            {
               "name": "English",
               "iso6391Name": "en",
               "score": 1.0
            }
         ]
      },
      {
         "id": "2",
         "detectedLanguages": [
            {
               "name": "Spanish",
               "iso6391Name": "es",
               "score": 1.0
            }
         ]
      },
      {
         "id": "3",
         "detectedLanguages": [
            {
               "name": "Chinese_Simplified",
               "iso6391Name": "zh_chs",
               "score": 1.0
            }
         ]
      }
   ],
   "errors": [

   ]
}
```
<a name="SentimentAnalysis"></a>

## <a name="analyze-sentiment"></a>Vélemények elemzése

A Sentiment Analysis API a szöveges bejegyzések hangulatát érzékeli a [Sentiment metódus](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/56f30ceeeda5650db055a3c9) használatával. A következő példa két dokumentumhoz rendel pontszámot, az egyik angol, a másik spanyol nyelvű.

1. Hozzon létre egy új Java-projektet a kedvenc ide-je (vagy az asztal új mappa). Hozzon létre egy osztályt, nevű `GetSentiment.java`.
1. Adja hozzá a kódot lejjebb találja az osztályhoz.
1. Cserélje le a `accessKey` értéket a kulcsát, a Szövegelemzés előfizetéshez [Azure](https://ms.portal.azure.com).
1. Cserélje le a `host` helyét (jelenleg `westus`) a regisztrált régióra.
1. Ellenőrizze, hogy a [Gson](https://github.com/google/gson) telepített könyvtár.
1. Futtassa a programot az IDE-ben, vagy a parancssor használatával (a kód megjegyzéseket utasításait) futtatása.

```java
import java.io.*;
import java.net.*;
import java.util.*;
import javax.net.ssl.HttpsURLConnection;

/*
 * Gson: https://github.com/google/gson
 * Maven info:
 *     groupId: com.google.code.gson
 *     artifactId: gson
 *     version: 2.8.1
 *
 * Once you have compiled or downloaded gson-2.8.1.jar, assuming you have placed it in the
 * same folder as this file (GetSentiment.java), you can compile and run this program at
 * the command line as follows.
 *
 * Execute the following two commands to build and run (change gson version if needed):
 * javac GetSentiment.java -classpath .;gson-2.8.1.jar -encoding UTF-8
 * java -cp .;gson-2.8.1.jar GetSentiment
 */
import com.google.gson.Gson;
import com.google.gson.GsonBuilder;
import com.google.gson.JsonObject;
import com.google.gson.JsonParser;

class Document {
    public String id, language, text;

    public Document(String id, String language, String text){
        this.id = id;
        this.language = language;
        this.text = text;
    }
}

class Documents {
    public List<Document> documents;

    public Documents() {
        this.documents = new ArrayList<Document>();
    }
    public void add(String id, String language, String text) {
        this.documents.add (new Document (id, language, text));
    }
}

public class GetSentiment {

// ***********************************************
// *** Update or verify the following values. ***
// **********************************************

// Replace the accessKey string value with your valid access key.
    static String accessKey = "enter key here";

// Replace or verify the region.

// You must use the same region in your REST API call as you used to obtain your access keys.
// For example, if you obtained your access keys from the westus region, replace 
// "westcentralus" in the URI below with "westus".

// NOTE: Free trial access keys are generated in the westcentralus region, so if you are using
// a free trial access key, you should not need to change this region.
    static String host = "https://westus.api.cognitive.microsoft.com";

    static String path = "/text/analytics/v2.1/sentiment";
    
    public static String getTheSentiment (Documents documents) throws Exception {
        String text = new Gson().toJson(documents);
        byte[] encoded_text = text.getBytes("UTF-8");

        URL url = new URL(host+path);
        HttpsURLConnection connection = (HttpsURLConnection) url.openConnection();
        connection.setRequestMethod("POST");
        connection.setRequestProperty("Content-Type", "text/json");
        connection.setRequestProperty("Ocp-Apim-Subscription-Key", accessKey);
        connection.setDoOutput(true);

        DataOutputStream wr = new DataOutputStream(connection.getOutputStream());
        wr.write(encoded_text, 0, encoded_text.length);
        wr.flush();
        wr.close();

        StringBuilder response = new StringBuilder ();
        BufferedReader in = new BufferedReader(
        new InputStreamReader(connection.getInputStream()));
        String line;
        while ((line = in.readLine()) != null) {
            response.append(line);
        }
        in.close();

        return response.toString();
    }

    public static String prettify(String json_text) {
        JsonParser parser = new JsonParser();
        JsonObject json = parser.parse(json_text).getAsJsonObject();
        Gson gson = new GsonBuilder().setPrettyPrinting().create();
        return gson.toJson(json);
    }

    public static void main (String[] args) {
        try {
            Documents documents = new Documents ();
            documents.add ("1", "en", "I really enjoy the new XBox One S. It has a clean look, it has 4K/HDR resolution and it is affordable.");
            documents.add ("2", "es", "Este ha sido un dia terrible, llegué tarde al trabajo debido a un accidente automobilistico.");

            String response = getTheSentiment (documents);
            System.out.println (prettify (response));
        }
        catch (Exception e) {
            System.out.println (e);
        }
    }
}
```

### <a name="sentiment-analysis-response"></a>Vélemények elemzése válasz

Az eredmény, ha azt sorolódik közelebb 1.0-s és a negatív közelebb van pontozását 0.0, ha pozitív mérjük.
A rendszer JSON formátumban ad vissza egy sikeres választ a következő példában látható módon:

```json
{
   "documents": [
      {
         "score": 0.99984133243560791,
         "id": "1"
      },
      {
         "score": 0.024017512798309326,
         "id": "2"
      },
   ],
   "errors": [   ]
}
```

<a name="KeyPhraseExtraction"></a>

## <a name="extract-key-phrases"></a>Kulcsszavak kinyerése

A Key Phrase Extraction API kulcskifejezéseket nyer ki a szöveges dokumentumokból a [Key Phrases metódus](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/56f30ceeeda5650db055a3c6) használatával. Az alábbi példa kulcskifejezéseket nyer ki angol és spanyol nyelvű dokumentumokhoz.

1. Hozzon létre egy új Java-projektet a kedvenc ide-je (vagy az asztal új mappa). Hozzon létre egy osztályt, nevű `GetKeyPhrases.java`.
1. Adja hozzá a kódot lejjebb találja az osztályhoz.
1. Cserélje le a `accessKey` értéket a kulcsát, a Szövegelemzés előfizetéshez [Azure](https://ms.portal.azure.com).
1. Cserélje le a `host` helyét (jelenleg `westus`) a regisztrált régióra.
1. Ellenőrizze, hogy a [Gson](https://github.com/google/gson) telepített könyvtár.
1. Futtassa a programot az IDE-ben, vagy a parancssor használatával (a kód megjegyzéseket utasításait) futtatása.

```java
import java.io.*;
import java.net.*;
import java.util.*;
import javax.net.ssl.HttpsURLConnection;

/*
 * Gson: https://github.com/google/gson
 * Maven info:
 *     groupId: com.google.code.gson
 *     artifactId: gson
 *     version: 2.8.1
 *
 * Once you have compiled or downloaded gson-2.8.1.jar, assuming you have placed it in the
 * same folder as this file (GetKeyPhrases.java), you can compile and run this program at
 * the command line as follows.
 *
 * Execute the following two commands to build and run (change gson version if needed):
 * javac GetKeyPhrases.java -classpath .;gson-2.8.1.jar -encoding UTF-8
 * java -cp .;gson-2.8.1.jar GetKeyPhrases
 */
import com.google.gson.Gson;
import com.google.gson.GsonBuilder;
import com.google.gson.JsonObject;
import com.google.gson.JsonParser;

class Document {
    public String id, language, text;

    public Document(String id, String language, String text){
        this.id = id;
        this.language = language;
        this.text = text;
    }
}

class Documents {
    public List<Document> documents;

    public Documents() {
        this.documents = new ArrayList<Document>();
    }
    public void add(String id, String language, String text) {
        this.documents.add (new Document (id, language, text));
    }
}

public class GetKeyPhrases {

// ***********************************************
// *** Update or verify the following values. ***
// **********************************************

// Replace the accessKey string value with your valid access key.
    static String accessKey = "enter key here";

// Replace or verify the region.

// You must use the same region in your REST API call as you used to obtain your access keys.
// For example, if you obtained your access keys from the westus region, replace 
// "westcentralus" in the URI below with "westus".

// NOTE: Free trial access keys are generated in the westcentralus region, so if you are using
// a free trial access key, you should not need to change this region.
    static String host = "https://westus.api.cognitive.microsoft.com";

    static String path = "/text/analytics/v2.1/keyPhrases";
    
    public static String GetKeyPhrases (Documents documents) throws Exception {
        String text = new Gson().toJson(documents);
        byte[] encoded_text = text.getBytes("UTF-8");

        URL url = new URL(host+path);
        HttpsURLConnection connection = (HttpsURLConnection) url.openConnection();
        connection.setRequestMethod("POST");
        connection.setRequestProperty("Content-Type", "text/json");
        connection.setRequestProperty("Ocp-Apim-Subscription-Key", accessKey);
        connection.setDoOutput(true);

        DataOutputStream wr = new DataOutputStream(connection.getOutputStream());
        wr.write(encoded_text, 0, encoded_text.length);
        wr.flush();
        wr.close();

        StringBuilder response = new StringBuilder ();
        BufferedReader in = new BufferedReader(
        new InputStreamReader(connection.getInputStream()));
        String line;
        while ((line = in.readLine()) != null) {
            response.append(line);
        }
        in.close();

        return response.toString();
    }

    public static String prettify(String json_text) {
        JsonParser parser = new JsonParser();
        JsonObject json = parser.parse(json_text).getAsJsonObject();
        Gson gson = new GsonBuilder().setPrettyPrinting().create();
        return gson.toJson(json);
    }

    public static void main (String[] args) {
        try {
            Documents documents = new Documents ();
            documents.add ("1", "en", "I really enjoy the new XBox One S. It has a clean look, it has 4K/HDR resolution and it is affordable.");
            documents.add ("2", "es", "Si usted quiere comunicarse con Carlos, usted debe de llamarlo a su telefono movil. Carlos es muy responsable, pero necesita recibir una notificacion si hay algun problema.");
            documents.add ("3", "en", "The Grand Hotel is a new hotel in the center of Seattle. It earned 5 stars in my review, and has the classiest decor I've ever seen.");

            String response = GetKeyPhrases (documents);
            System.out.println (prettify (response));
        }
        catch (Exception e) {
            System.out.println (e);
        }
    }
}
```

### <a name="key-phrase-extraction-response"></a>A kulcsfontosságú kifejezések kinyerése válasz

A rendszer JSON formátumban ad vissza egy sikeres választ a következő példában látható módon:

```json
{
   "documents": [
      {
         "keyPhrases": [
            "HDR resolution",
            "new XBox",
            "clean look"
         ],
         "id": "1"
      },
      {
         "keyPhrases": [
            "Carlos",
            "notificacion",
            "algun problema",
            "telefono movil"
         ],
         "id": "2"
      },
      {
         "keyPhrases": [
            "new hotel",
            "Grand Hotel",
            "review",
            "center of Seattle",
            "classiest decor",
            "stars"
         ],
         "id": "3"
      }
   ],
   "errors": [  ]
}
```
<a name="Entities"></a>

## <a name="identify-entities"></a>Entitások azonosítása

Az Entities API azonosítja a szöveges dokumentumok jól ismert entitásait az [Entities metódus](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-V2-1/operations/5ac4251d5b4ccd1554da7634) használatával. [Entitások](https://docs.microsoft.com/azure/cognitive-services/text-analytics/how-tos/text-analytics-how-to-entity-linking) kinyerheti a szöveget, például a "Egyesült Államok", majd biztosítson a típusa és/vagy a Wikipédia-hivatkozás esetében a szavak. A típus az "Egyesült Államok" `location`, míg a Wikipedia hivatkozása `https://en.wikipedia.org/wiki/United_States`.  Az alábbi példa angol nyelvű dokumentumok entitásait azonosítja.

1. Hozzon létre egy új Java-projektet a kedvenc ide-je (vagy az asztal új mappa). Hozzon létre egy osztályt, nevű `GetEntities.java`.
1. Adja hozzá a kódot lejjebb találja az osztályhoz.
1. Cserélje le a `accessKey` értéket a kulcsát, a Szövegelemzés előfizetéshez [Azure](https://ms.portal.azure.com).
1. Cserélje le a `host` helyét (jelenleg `westus`) a regisztrált régióra.
1. Ellenőrizze, hogy a [Gson](https://github.com/google/gson) telepített könyvtár.
1. Futtassa a programot az IDE-ben, vagy a parancssor használatával (a kód megjegyzéseket utasításait) futtatása.

```java
import java.io.*;
import java.net.*;
import java.util.*;
import javax.net.ssl.HttpsURLConnection;

/*
 * Gson: https://github.com/google/gson
 * Maven info:
 *     groupId: com.google.code.gson
 *     artifactId: gson
 *     version: 2.8.1
 *
 * Once you have compiled or downloaded gson-2.8.1.jar, assuming you have placed it in the
 * same folder as this file (GetEntities.java), you can compile and run this program at
 * the command line as follows.
 *
 * Execute the following two commands to build and run (change gson version if needed):
 * javac GetEntities.java -classpath .;gson-2.8.1.jar -encoding UTF-8
 * java -cp .;gson-2.8.1.jar GetEntities
 */
import com.google.gson.Gson;
import com.google.gson.GsonBuilder;
import com.google.gson.JsonObject;
import com.google.gson.JsonParser;

class Document {
    public String id, language, text;

    public Document(String id, String language, String text){
        this.id = id;
        this.language = language;
        this.text = text;
    }
}

class Documents {
    public List<Document> documents;

    public Documents() {
        this.documents = new ArrayList<Document>();
    }
    public void add(String id, String language, String text) {
        this.documents.add (new Document (id, language, text));
    }
}

public class GetEntities {

// ***********************************************
// *** Update or verify the following values. ***
// **********************************************

// Replace the accessKey string value with your valid access key.
    static String accessKey = "enter key here";

// Replace or verify the region.

// You must use the same region in your REST API call as you used to obtain your access keys.
// For example, if you obtained your access keys from the westus region, replace 
// "westcentralus" in the URI below with "westus".

// NOTE: Free trial access keys are generated in the westcentralus region, so if you are using
// a free trial access key, you should not need to change this region.
    static String host = "https://westus.api.cognitive.microsoft.com";

    static String path = "/text/analytics/v2.1/entities";
    
    public static String GetEntities (Documents documents) throws Exception {
        String text = new Gson().toJson(documents);
        byte[] encoded_text = text.getBytes("UTF-8");

        URL url = new URL(host+path);
        HttpsURLConnection connection = (HttpsURLConnection) url.openConnection();
        connection.setRequestMethod("POST");
        connection.setRequestProperty("Content-Type", "text/json");
        connection.setRequestProperty("Ocp-Apim-Subscription-Key", accessKey);
        connection.setDoOutput(true);

        DataOutputStream wr = new DataOutputStream(connection.getOutputStream());
        wr.write(encoded_text, 0, encoded_text.length);
        wr.flush();
        wr.close();

        StringBuilder response = new StringBuilder ();
        BufferedReader in = new BufferedReader(
        new InputStreamReader(connection.getInputStream()));
        String line;
        while ((line = in.readLine()) != null) {
            response.append(line);
        }
        in.close();

        return response.toString();
    }

    public static String prettify(String json_text) {
        JsonParser parser = new JsonParser();
        JsonObject json = parser.parse(json_text).getAsJsonObject();
        Gson gson = new GsonBuilder().setPrettyPrinting().create();
        return gson.toJson(json);
    }

    public static void main (String[] args) {
        try {
            Documents documents = new Documents ();
            documents.add ("1", "en", "Microsoft is an It company.");

            String response = GetEntities (documents);
            System.out.println (prettify (response));
        }
        catch (Exception e) {
            System.out.println (e);
        }
    }
}
```

### <a name="entity-extraction-response"></a>Entitáskinyerési válasz

A rendszer JSON formátumban ad vissza egy sikeres választ a következő példában látható módon:

```json
{  
   "documents":[  
      {  
         "id":"1",
         "entities":[  
            {  
               "name":"Microsoft",
               "matches":[  
                  {  
                     "wikipediaScore":0.20872054383103444,
                     "entityTypeScore":0.99996185302734375,
                     "text":"Microsoft",
                     "offset":0,
                     "length":9
                  }
               ],
               "wikipediaLanguage":"en",
               "wikipediaId":"Microsoft",
               "wikipediaUrl":"https://en.wikipedia.org/wiki/Microsoft",
               "bingId":"a093e9b9-90f5-a3d5-c4b8-5855e1b01f85",
               "type":"Organization"
            },
            {  
               "name":"Technology company",
               "matches":[  
                  {  
                     "wikipediaScore":0.82123868042800585,
                     "text":"It company",
                     "offset":16,
                     "length":10
                  }
               ],
               "wikipediaLanguage":"en",
               "wikipediaId":"Technology company",
               "wikipediaUrl":"https://en.wikipedia.org/wiki/Technology_company",
               "bingId":"bc30426e-22ae-7a35-f24b-454722a47d8f"
            }
         ]
      }
   ],
    "errors":[]
}
```

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Szövegelemzés a Power BI-jal](../tutorials/tutorial-power-bi-key-phrases.md)

## <a name="see-also"></a>Lásd még

 [A Text Analytics áttekintése](../overview.md)  
 [Gyakori kérdések (GYIK)](../text-analytics-resource-faq.md)
