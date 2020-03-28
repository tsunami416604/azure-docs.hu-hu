---
title: 'Rövid útmutató: Képelemzésbe való betekintés a REST API és a Java használatával – Bing Visual Search'
titleSuffix: Azure Cognitive Services
description: Megtudhatja, hogy miként tölthet fel képet a Bing Visual Search API-ba, és hogyan kaphat betekintést.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-visual-search
ms.topic: quickstart
ms.date: 12/17/2019
ms.author: scottwhi
ms.openlocfilehash: fe323fc27062ad1bee9abdfaf3408430e28523a9
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75446627"
---
# <a name="quickstart-get-image-insights-using-the-bing-visual-search-rest-api-and-java"></a>Rövid útmutató: Képelemzésbe való betekintés a Bing Visual Search REST API-val és a Java-val

Ezzel a rövid útmutatóval elsőalkalommal hívhatja meg a Bing Visual Search API-t, és megtekintheti az eredményeket. Ez a Java alkalmazás feltölt egy képet az API-ba, és megjeleníti az általa visszaadott információkat. Bár ez az alkalmazás Java nyelven íródott, az API egy RESTful webszolgáltatás, amely kompatibilis a legtöbb programozási nyelvvel.

## <a name="prerequisites"></a>Előfeltételek

* A [Java Development Kit (JDK) 7 vagy 8](https://aka.ms/azure-jdks)
* A [Gson Java könyvtár](https://github.com/google/gson)
* [Apache HttpComponents](https://hc.apache.org/downloads.cgi)

[!INCLUDE [cognitive-services-bing-visual-search-signup-requirements](../../../../includes/cognitive-services-bing-visual-search-signup-requirements.md)]

## <a name="create-and-initialize-a-project"></a>Projekt létrehozása és inicializálása

1. Hozzon létre egy új Java-projektet kedvenc IDE-jében vagy szerkesztőjében, és importálja a következő könyvtárakat:

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

2. Hozzon létre változókat az API-végponthoz, az előfizetési kulcshoz és a lemezkép elérési úthoz. `endpoint`lehet az alábbi globális végpont, vagy az erőforráshoz az Azure Portalon megjelenő [egyéni altartomány-végpont:](../../../cognitive-services/cognitive-services-custom-subdomains.md)

    ```java
    static String endpoint = "https://api.cognitive.microsoft.com/bing/v7.0/images/visualsearch";
    static String subscriptionKey = "your-key-here";
    static String imagePath = "path-to-your-image";
    ```

    
    Helyi kép feltöltésekénél az űrlapadatoknak tartalmazniuk kell a `Content-Disposition` fejlécet. Be kell `name` állítania a paraméter "kép", `filename` és beállíthatja a paramétert bármilyen karakterláncot. Az űrlap tartalma tartalmazza a kép bináris adatait. A feltölthető maximális képméret 1 MB.
    
    ```
    --boundary_1234-abcd
    Content-Disposition: form-data; name="image"; filename="myimagefile.jpg"
    
    ÿØÿà JFIF ÖÆ68g-¤CWŸþ29ÌÄøÖ‘º«™æ±èuZiÀ)"óÓß°Î= ØJ9á+*G¦...
    
    --boundary_1234-abcd--
    ```

## <a name="create-the-json-parser"></a>A JSON-elemző létrehozása

Hozzon létre egy módszert, amely lehetővé teszi, `JsonParser`hogy a JSON-válasz az API-ból olvashatóbb legyen a következő vel:

```java
public static String prettify(String json_text) {
        JsonParser parser = new JsonParser();
        JsonObject json = parser.parse(json_text).getAsJsonObject();
        Gson gson = new GsonBuilder().setPrettyPrinting().create();
        return gson.toJson(json);
    }
```

## <a name="construct-the-search-request-and-query"></a>A keresési kérelem és a lekérdezés összeállítása

1. Az alkalmazás fő metódusában hozzon létre `HttpClientBuilder.create().build();`egy HTTP-ügyfélt a következő használatával:

    ```java
    CloseableHttpClient httpClient = HttpClientBuilder.create().build();
    ```

2. Hozzon `HttpEntity` létre egy objektumot, amely feltölti a képet az API-ba:

    ```java
    HttpEntity entity = MultipartEntityBuilder
        .create()
        .addBinaryBody("image", new File(imagePath))
        .build();
    ```

3. Hozzon `httpPost` létre egy objektumot a végponttal, és állítsa be a fejlécet az előfizetési kulcs használatára:

    ```java
    HttpPost httpPost = new HttpPost(endpoint);
    httpPost.setHeader("Ocp-Apim-Subscription-Key", subscriptionKey);
    httpPost.setEntity(entity);
    ```

## <a name="receive-and-process-the-json-response"></a>A JSON-válasz fogadása és feldolgozása

1. A `HttpClient.execute()` metódus segítségével küldjön egy kérelmet az API-nak, és tárolja a választ egy `InputStream` objektumban:
    
    ```java
    HttpResponse response = httpClient.execute(httpPost);
    InputStream stream = response.getEntity().getContent();
    ```

2. A JSON-karakterlánc tárolása és a válasz nyomtatása:

    ```java
    String json = new Scanner(stream).useDelimiter("\\A").next();
    System.out.println("\nJSON Response:\n");
    System.out.println(prettify(json));
    ```

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Visual Search egyoldalas webalkalmazás létrehozása](../tutorial-bing-visual-search-single-page-app.md)
