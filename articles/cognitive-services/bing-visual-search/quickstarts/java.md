---
title: 'Gyors útmutató: Lemezkép-elemzések, a Bing Visual Search REST API és a Java használatával'
titleSuffix: Azure Cognitive Services
description: Megtudhatja, hogyan kaphat elemzési információkat, és töltsön fel egy képet, a Bing Visual Search API.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-visual-search
ms.topic: quickstart
ms.date: 5/16/2018
ms.author: scottwhi
ms.openlocfilehash: 79b118c0a4fd28eacf24dc63f04f36314807b41a
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/07/2019
ms.locfileid: "57531088"
---
# <a name="quickstart-get-image-insights-using-the-bing-visual-search-rest-api-and-java"></a>Gyors útmutató: Lemezkép-elemzések, a Bing Visual Search REST API és a Java használatával

Ez a rövid útmutató segítségével a Bing Visual Search API az első hívását, és megtekintheti a keresési eredmények között. Ez egyszerű C# alkalmazás feltölt egy képet az API-hoz, és a visszaadott kapcsolatos információkat jeleníti meg. Bár ez az alkalmazás Java nyelven lett íródott, az API egy RESTful-webszolgáltatás, azaz kompatibilis a legtöbb programnyelvvel.

Ha feltöltenek egy helyi lemezképet, az űrlapadatok tartalmaznia kell a tartalom-szabályozó fejléc. A `name` paraméter értéke „image” legyen, a `filename` paraméter értéke viszont bármilyen sztring lehet. Az űrlap tartalmát a kép bináris adatai adják. A legnagyobb feltölthető képméret 1 MB.

```
--boundary_1234-abcd
Content-Disposition: form-data; name="image"; filename="myimagefile.jpg"

ÿØÿà JFIF ÖÆ68g-¤CWŸþ29ÌÄøÖ‘º«™æ±èuZiÀ)"óÓß°Î= ØJ9á+*G¦...

--boundary_1234-abcd--
```

## <a name="prerequisites"></a>Előfeltételek

* A [Java fejlesztői készlet (JDK) 7-es vagy 8-as verziója](https://aka.ms/azure-jdks)
* A [Gson-kódtár](https://github.com/google/gson)
* [Apache HttpComponents](https://hc.apache.org/downloads.cgi)


[!INCLUDE [cognitive-services-bing-visual-search-signup-requirements](../../../../includes/cognitive-services-bing-visual-search-signup-requirements.md)]

## <a name="create-and-initialize-a-project"></a>Projekt létrehozása és inicializálása

1. Hozzon létre egy új Java-projektet a kedvenc IDE-környezetében vagy szerkesztőjében, és importálja az alábbi kódtárakat.

    ```java
    import java.util.*;
    import java.io.*;
    import com.google.gson.Gson;
    import com.google.gson.GsonBuilder;
    import com.google.gson.JsonObject;
    import com.google.gson.JsonParser;
    
    // HttpClient libraries
    
    import org.apache.http.HttpEntity;
    import org.apache.http.HttpResponse;
    import org.apache.http.client.methods.HttpPost;
    import org.apache.http.entity.ContentType;
    import org.apache.http.entity.mime.MultipartEntityBuilder;
    import org.apache.http.impl.client.CloseableHttpClient;
    import org.apache.http.impl.client.HttpClientBuilder;
    ```

2. Változók létrehozása az API-végpont, előfizetési kulcsot, és a lemezkép elérési útja. 

    ```java
    static String endpoint = "https://api.cognitive.microsoft.com/bing/v7.0/images/visualsearch";
    static String subscriptionKey = "your-key-here";
    static String imagePath = "path-to-your-image";
    ```

## <a name="create-the-json-parser"></a>A JSON-elemző létrehozása

Hozzon létre egy metódust, hogy a JSON-válasz az API-val további olvasható használatával `JsonParser`.

    ```java
    public static String prettify(String json_text) {
            JsonParser parser = new JsonParser();
            JsonObject json = parser.parse(json_text).getAsJsonObject();
            Gson gson = new GsonBuilder().setPrettyPrinting().create();
            return gson.toJson(json);
        }
    ```

## <a name="construct-the-search-request-and-query"></a>A keresési kérelem és a lekérdezés összeállítása

1. A fő módszer az alkalmazás, hozzon létre egy Http-ügyfelének használatával `HttpClientBuilder.create().build();`.

    ```java
    CloseableHttpClient httpClient = HttpClientBuilder.create().build();
    ```

2. Hozzon létre egy `HttpEntity` paranccsal töltse fel a rendszerképet az API-t.

    ```java
    HttpEntity entity = MultipartEntityBuilder
        .create()
        .addBinaryBody("image", new File(imagePath))
        .build();
    ```

3. Hozzon létre egy `httpPost` objektumot a végponthoz, és állítsa be a fejléc az előfizetési kulcs használatára.

    ```java
    HttpPost httpPost = new HttpPost(endpoint);
    httpPost.setHeader("Ocp-Apim-Subscription-Key", subscriptionKey);
    httpPost.setEntity(entity);
    ```

## <a name="receive-and-process-the-json-response"></a>A JSON-válasz fogadása és feldolgozása

1. Használat `HttpClient.execute()` kérést küldhet az API-t, és a válasz a tárolása egy `InputStream` objektum.
    
    ```java
    HttpResponse response = httpClient.execute(httpPost);
    InputStream stream = response.getEntity().getContent();
    ```

2. A JSON-karakterlánc Store, és nyomtassa ki a választ.

```java
String json = new Scanner(stream).useDelimiter("\\A").next();
System.out.println("\nJSON Response:\n");
System.out.println(prettify(json));
```

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Egyéni keresés webes alkalmazás készítése](../tutorial-bing-visual-search-single-page-app.md)
