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
ms.date: 4/02/2019
ms.author: scottwhi
ms.openlocfilehash: 2fe4e9dad0b198fe54e06ce07100d231f1f7d157
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/18/2019
ms.locfileid: "59046444"
---
# <a name="quickstart-get-image-insights-using-the-bing-visual-search-rest-api-and-java"></a>Gyors útmutató: Lemezkép-elemzések, a Bing Visual Search REST API és a Java használatával

Ez a rövid útmutató segítségével győződjön meg arról, az első, a Bing Visual Search API hívása, és tekintse meg az eredményeket. A Java-alkalmazás feltölt egy képet az API-hoz, és visszaadja az információkat jeleníti meg. Bár ez az alkalmazás Java nyelven van megírva, az API-t az szinte bármelyik programozási nyelvével kompatibilis webes RESTful szolgáltatás.

Tartalmaznia kell a helyi rendszerképet tölt fel, amikor az űrlap adatait a `Content-Disposition` fejléc. Be kell állítani a `name` paraméter "image", és állíthatja a `filename` bármilyen karakterlánc paramétert. Az űrlap a rendszerkép a bináris adatokat is tartalmazza. A maximális képméret feltöltheti az 1 MB.

```
--boundary_1234-abcd
Content-Disposition: form-data; name="image"; filename="myimagefile.jpg"

ÿØÿà JFIF ÖÆ68g-¤CWŸþ29ÌÄøÖ‘º«™æ±èuZiÀ)"óÓß°Î= ØJ9á+*G¦...

--boundary_1234-abcd--
```

## <a name="prerequisites"></a>Előfeltételek

* A [Java fejlesztői készlet (JDK) 7 vagy 8](https://aka.ms/azure-jdks)
* A [Gson Java-kódtár](https://github.com/google/gson)
* [Apache HttpComponents](https://hc.apache.org/downloads.cgi)

[!INCLUDE [cognitive-services-bing-visual-search-signup-requirements](../../../../includes/cognitive-services-bing-visual-search-signup-requirements.md)]

## <a name="create-and-initialize-a-project"></a>Projekt létrehozása és inicializálása

1. Hozzon létre egy új Java-projektet a kedvenc integrált Fejlesztőkörnyezetével vagy szerkesztőjével, és importálja a következő könyvtárak:

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

2. Változók létrehozása az API-végpont, előfizetési kulcsot, és a lemezkép elérési útja:

    ```java
    static String endpoint = "https://api.cognitive.microsoft.com/bing/v7.0/images/visualsearch";
    static String subscriptionKey = "your-key-here";
    static String imagePath = "path-to-your-image";
    ```

## <a name="create-the-json-parser"></a>A JSON-elemző létrehozása

Hozzon létre egy metódust, hogy a JSON-válasz az API-val további olvasható használatával `JsonParser`:

    ```java
    public static String prettify(String json_text) {
            JsonParser parser = new JsonParser();
            JsonObject json = parser.parse(json_text).getAsJsonObject();
            Gson gson = new GsonBuilder().setPrettyPrinting().create();
            return gson.toJson(json);
        }
    ```

## <a name="construct-the-search-request-and-query"></a>A keresési kérelem és a lekérdezés összeállítása

1. A fő módszer az alkalmazás, hozzon létre egy HTTP-ügyfelének használatával `HttpClientBuilder.create().build();`:

    ```java
    CloseableHttpClient httpClient = HttpClientBuilder.create().build();
    ```

2. Hozzon létre egy `HttpEntity` feltöltheti a rendszerképet az API-objektum:

    ```java
    HttpEntity entity = MultipartEntityBuilder
        .create()
        .addBinaryBody("image", new File(imagePath))
        .build();
    ```

3. Hozzon létre egy `httpPost` objektumot a végponthoz, és állítsa be a fejléc az előfizetési kulcs használata:

    ```java
    HttpPost httpPost = new HttpPost(endpoint);
    httpPost.setHeader("Ocp-Apim-Subscription-Key", subscriptionKey);
    httpPost.setEntity(entity);
    ```

## <a name="receive-and-process-the-json-response"></a>A JSON-válasz fogadása és feldolgozása

1. Használja a `HttpClient.execute()` módszer kérést küldhet az API-t, a válasz tárolása egy `InputStream` objektum:
    
    ```java
    HttpResponse response = httpClient.execute(httpPost);
    InputStream stream = response.getEntity().getContent();
    ```

2. A JSON-karakterlánc Store, és a válasz:

```java
String json = new Scanner(stream).useDelimiter("\\A").next();
System.out.println("\nJSON Response:\n");
System.out.println(prettify(json));
```

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Vizuális keresés egyoldalas webes alkalmazás készítése](../tutorial-bing-visual-search-single-page-app.md)
