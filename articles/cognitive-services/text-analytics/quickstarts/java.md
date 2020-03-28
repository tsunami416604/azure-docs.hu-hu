---
title: 'Rövid útmutató: A Text Analytics REST API-jának hívásához használja a Java-t'
titleSuffix: Azure Cognitive Services
description: Ez a rövid útmutató bemutatja, hogyan szerezhet be információkat és kódmintákat, hogy gyorsan elkezdhesse használni a Text Analytics API-t az Azure Cognitive Servicesben.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: quickstart
ms.date: 12/17/2019
ms.author: aahi
ms.custom: seo-java-july2019, seo-java-august2019
ms.openlocfilehash: 0541438659f25780be0c7bc1c87670cab6d7ca08
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75446321"
---
# <a name="quickstart-use-java-to-call-the-azure-text-analytics-cognitive-service"></a>Rövid útmutató: Java használatával hívja meg az Azure Text Analytics kognitív szolgáltatás
<a name="HOLTop"></a>

Ez a cikk [bemutatja, hogyan észlelheti a nyelvet,](#Detect)elemezheti a [véleményeket,](#SentimentAnalysis) [hogyan nyerheti ki a kulcsfontosságú kifejezéseket,](#KeyPhraseExtraction)és hogyan azonosíthatja a javakkal a [Text Analytics API-kat](//go.microsoft.com/fwlink/?LinkID=759711) használó [kapcsolt entitásokat.](#Entities)

[!INCLUDE [text-analytics-api-references](../includes/text-analytics-api-references.md)]

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [cognitive-services-text-analytics-signup-requirements](../../../../includes/cognitive-services-text-analytics-signup-requirements.md)]

A regisztráció során létrejött [végponttal és hozzáférési kulccsal](../../cognitive-services-apis-create-account.md#get-the-keys-for-your-resource) is rendelkeznie kell.

<a name="Detect"></a>

## <a name="detect-language"></a>Nyelv felismerése

A Nyelvfelismerés API a [Nyelv észlelése módszerrel](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/56f30ceeeda5650db055a3c7)észleli a szöveges dokumentum nyelvét.

1. Hozzon létre egy új Java-projektet a kedvenc IDE-ben (vagy új mappában az asztalon). Hozzon létre `DetectLanguage.java`egy nevű osztályt.
1. Add hozzá az alább megadott kódot az osztályodhoz.
1. Másolja a Text Analytics-kulcsot és a végpontot a kódba. 
1. Győződjön meg arról, hogy a [Gson](https://github.com/google/gson) könyvtár telepítve van.
1. Futtassa a programot az IDE-ben, vagy használja a parancssort a futtatáshoz (a kódmegjegyzésekben található utasítások).

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
    static String subscription_key_var;
    static String subscription_key;
    static String endpoint_var;
    static String endpoint;

    public static void Initialize () throws Exception {
        subscription_key = "<paste-your-text-analytics-key-here>";
        endpoint = "<paste-your-text-analytics-endpoint-here>";
    }

    static String path = "/text/analytics/v2.1/languages";
    
    public static String GetLanguage (Documents documents) throws Exception {
        String text = new Gson().toJson(documents);
        byte[] encoded_text = text.getBytes("UTF-8");

        URL url = new URL(endpoint+path);
        HttpsURLConnection connection = (HttpsURLConnection) url.openConnection();
        connection.setRequestMethod("POST");
        connection.setRequestProperty("Content-Type", "text/json");
        connection.setRequestProperty("Ocp-Apim-Subscription-Key", subscription_key);
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
            Initialize();

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

### <a name="language-detection-response"></a>Nyelvfelismerési válasz

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

1. Hozzon létre egy új Java-projektet a kedvenc IDE-ben (vagy új mappában az asztalon). Hozzon létre egy `GetSentiment.java`osztályt a nevű ben.
1. Add hozzá az alább megadott kódot az osztályodhoz.
1. Másolja a Text Analytics-kulcsot és a végpontot a kódba.
1. Győződjön meg arról, hogy a [Gson](https://github.com/google/gson) könyvtár telepítve van.
1. Futtassa a programot az IDE-ben, vagy használja a parancssort a futtatáshoz (a kódmegjegyzésekben található utasítások).

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
    static String subscription_key_var;
    static String subscription_key;
    static String endpoint_var;
    static String endpoint;

    public static void Initialize () throws Exception {
        subscription_key = "<paste-your-text-analytics-key-here>";
        endpoint = "<paste-your-text-analytics-endpoint-here>";
    }

    static String path = "/text/analytics/v2.1/sentiment";
    
    public static String getTheSentiment (Documents documents) throws Exception {
        String text = new Gson().toJson(documents);
        byte[] encoded_text = text.getBytes("UTF-8");

        URL url = new URL(endpoint+path);
        HttpsURLConnection connection = (HttpsURLConnection) url.openConnection();
        connection.setRequestMethod("POST");
        connection.setRequestProperty("Content-Type", "text/json");
        connection.setRequestProperty("Ocp-Apim-Subscription-Key", subscription_key);
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
            Initialize();

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

### <a name="sentiment-analysis-response"></a>Hangulatelemzési válasz

Az eredmény akkor lesz pozitív, ha az 1.0-hoz közelebb ért, és negatív, ha közelebb van a 0.0-hoz.
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

1. Hozzon létre egy új Java-projektet a kedvenc IDE-ben (vagy új mappában az asztalon). Hozzon létre benne `GetKeyPhrases.java`egy osztályt, amelynek neve .
1. Add hozzá az alább megadott kódot az osztályodhoz.
1. Másolja a Text Analytics-kulcsot és a végpontot a kódba. 
1. Győződjön meg arról, hogy a [Gson](https://github.com/google/gson) könyvtár telepítve van.
1. Futtassa a programot az IDE-ben, vagy használja a parancssort a futtatáshoz (a kódmegjegyzésekben található utasítások).

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
    static String subscription_key_var;
    static String subscription_key;
    static String endpoint_var;
    static String endpoint;

    public static void Initialize () throws Exception {
        subscription_key = "<paste-your-text-analytics-key-here>";
        endpoint = "<paste-your-text-analytics-endpoint-here>";
    }

    static String path = "/text/analytics/v2.1/keyPhrases";
    
    public static String GetKeyPhrases (Documents documents) throws Exception {
        String text = new Gson().toJson(documents);
        byte[] encoded_text = text.getBytes("UTF-8");

        URL url = new URL(endpoint+path);
        HttpsURLConnection connection = (HttpsURLConnection) url.openConnection();
        connection.setRequestMethod("POST");
        connection.setRequestProperty("Content-Type", "text/json");
        connection.setRequestProperty("Ocp-Apim-Subscription-Key", subscription_key);
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
            Initialize();

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

### <a name="key-phrase-extraction-response"></a>Kulcskifejezés-kinyerési válasz

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

Az Entities API azonosítja a szöveges dokumentumok jól ismert entitásait az [Entities metódus](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-V2-1/operations/5ac4251d5b4ccd1554da7634) használatával. [Az entitások kinyerik](https://docs.microsoft.com/azure/cognitive-services/text-analytics/how-tos/text-analytics-how-to-entity-linking) a szavakat a szövegből, például az "Egyesült Államok", majd megadják a szó(ok) típusát és/vagy Wikipédia-hivatkozását. Az "Egyesült Államok" `location`típusa a , míg `https://en.wikipedia.org/wiki/United_States`a Wikipédiára mutató hivatkozás .  Az alábbi példa angol nyelvű dokumentumok entitásait azonosítja.

1. Hozzon létre egy új Java-projektet a kedvenc IDE-ben (vagy új mappában az asztalon). Hozzon létre egy `GetEntities.java`osztályt a nevű ben.
1. Add hozzá az alább megadott kódot az osztályodhoz.
1. Másolja a Text Analytics-kulcsot és a végpontot a kódba. 
1. Győződjön meg arról, hogy a [Gson](https://github.com/google/gson) könyvtár telepítve van.
1. Futtassa a programot az IDE-ben, vagy használja a parancssort a futtatáshoz (a kódmegjegyzésekben található utasítások).

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
    static String subscription_key_var;
    static String subscription_key;
    static String endpoint_var;
    static String endpoint;

    public static void Initialize () throws Exception {
        subscription_key = "<paste-your-text-analytics-key-here>";
        endpoint = "<paste-your-text-analytics-endpoint-here>";
    }

    static String path = "/text/analytics/v2.1/entities";
    
    public static String GetEntities (Documents documents) throws Exception {
        String text = new Gson().toJson(documents);
        byte[] encoded_text = text.getBytes("UTF-8");

        URL url = new URL(endpoint+path);
        HttpsURLConnection connection = (HttpsURLConnection) url.openConnection();
        connection.setRequestMethod("POST");
        connection.setRequestProperty("Content-Type", "text/json");
        connection.setRequestProperty("Ocp-Apim-Subscription-Key", subscription_key);
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
            Initialize();

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
> [Szövegelemzés a Power BI-val](../tutorials/tutorial-power-bi-key-phrases.md)

## <a name="see-also"></a>Lásd még

 [Szövegelemzés – áttekintés](../overview.md)  
 [Gyakori kérdések (GYIK)](../text-analytics-resource-faq.md)
