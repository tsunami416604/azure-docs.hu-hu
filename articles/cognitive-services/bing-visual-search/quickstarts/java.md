---
title: 'Gyors útmutató: képelemzések beolvasása a REST API és a Java-Bing Visual Search használatával'
titleSuffix: Azure Cognitive Services
description: Megtudhatja, hogyan tölthet fel képet a Bing Visual Search API, és hogyan szerezhet be információkat.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-visual-search
ms.topic: quickstart
ms.date: 05/22/2020
ms.custom: devx-track-java
ms.author: scottwhi
ms.openlocfilehash: 6e0e0cc2513b1c2a5f89e61984331399ebae269a
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/05/2020
ms.locfileid: "87320442"
---
# <a name="quickstart-get-image-insights-using-the-bing-visual-search-rest-api-and-java"></a>Gyors útmutató: képelemzések lekérése a Bing Visual Search REST API és a Java használatával

Ezzel a rövid útmutatóval megteheti az első hívást a Bing Visual Search API. Ez a Java-alkalmazás feltölt egy rendszerképet az API-hoz, és megjeleníti a visszaadott adatokat. Bár ez az alkalmazás Java nyelven íródott, az API egy REST-alapú webszolgáltatás, amely kompatibilis a legtöbb programozási nyelvvel.

## <a name="prerequisites"></a>Előfeltételek

* A [Java fejlesztői készlet (JDK) 7 vagy 8](https://aka.ms/azure-jdks)
* A [Gson Java-könyvtára](https://github.com/google/gson)
* [Apache HttpComponents](https://hc.apache.org/downloads.cgi)

[!INCLUDE [cognitive-services-bing-visual-search-signup-requirements](../../../../includes/cognitive-services-bing-visual-search-signup-requirements.md)]

## <a name="create-and-initialize-a-project"></a>Projekt létrehozása és inicializálása

1. Hozzon létre egy új Java-projektet a kedvenc IDE vagy szerkesztőben, és importálja a következő könyvtárakat:

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

2. Hozzon létre változókat az API-végponthoz, az előfizetési kulcshoz és a rendszerkép elérési útjához. Az érték esetében használhatja `endpoint` a globális végpontot a következő kódban, vagy használhatja az erőforráshoz tartozó Azure Portalban megjelenő [Egyéni altartomány](../../../cognitive-services/cognitive-services-custom-subdomains.md) -végpontot.

    ```java
    static String endpoint = "https://api.cognitive.microsoft.com/bing/v7.0/images/visualsearch";
    static String subscriptionKey = "your-key-here";
    static String imagePath = "path-to-your-image";
    ```

    
3. Helyi rendszerkép feltöltésekor az űrlapon szerepelnie kell a `Content-Disposition` fejlécnek. Állítsa a `name` paramétert a "rendszerkép" értékre, és állítsa a `filename` paramétert a rendszerkép fájlnevére. Az űrlap tartalma tartalmazza a rendszerkép bináris értékeit. A feltölthető maximális képméret 1 MB lehet.
    
    ```
    --boundary_1234-abcd
    Content-Disposition: form-data; name="image"; filename="myimagefile.jpg"
    
    ÿØÿà JFIF ÖÆ68g-¤CWŸþ29ÌÄøÖ‘º«™æ±èuZiÀ)"óÓß°Î= ØJ9á+*G¦...
    
    --boundary_1234-abcd--
    ```

## <a name="create-the-json-parser"></a>A JSON-elemző létrehozása

Hozzon létre egy metódust, amely a JSON-választ az API-tól olvashatóbb módon teszi elérhetővé a használatával `JsonParser` .

```java
public static String prettify(String json_text) {
        JsonParser parser = new JsonParser();
        JsonObject json = parser.parse(json_text).getAsJsonObject();
        Gson gson = new GsonBuilder().setPrettyPrinting().create();
        return gson.toJson(json);
    }
```

## <a name="construct-the-search-request-and-query"></a>A keresési kérelem és a lekérdezés összeállítása

1. Az alkalmazás fő metódusában hozzon létre egy HTTP-ügyfelet a használatával `HttpClientBuilder.create().build();` .

    ```java
    CloseableHttpClient httpClient = HttpClientBuilder.create().build();
    ```

2. Hozzon létre egy `HttpEntity` objektumot a RENDSZERKÉP API-ba való feltöltéséhez.

    ```java
    HttpEntity entity = MultipartEntityBuilder
        .create()
        .addBinaryBody("image", new File(imagePath))
        .build();
    ```

3. Hozzon létre egy `httpPost` objektumot a végponttal, és állítsa be a fejlécet az előfizetési kulcs használatára.

    ```java
    HttpPost httpPost = new HttpPost(endpoint);
    httpPost.setHeader("Ocp-Apim-Subscription-Key", subscriptionKey);
    httpPost.setEntity(entity);
    ```

## <a name="receive-and-process-the-json-response"></a>A JSON-válasz fogadása és feldolgozása

1. A `HttpClient.execute()` metódus használatával küldjön egy kérést az API-nak, és tárolja a választ egy `InputStream` objektumban.
    
    ```java
    HttpResponse response = httpClient.execute(httpPost);
    InputStream stream = response.getEntity().getContent();
    ```

2. Tárolja a JSON-karakterláncot, és nyomtassa ki a választ.

    ```java
    String json = new Scanner(stream).useDelimiter("\\A").next();
    System.out.println("\nJSON Response:\n");
    System.out.println(prettify(json));
    ```

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Visual Search egyoldalas Webalkalmazás létrehozása](../tutorial-bing-visual-search-single-page-app.md)
