---
title: A Bing Visual Search API a Java rövid |} A Microsoft Docs
titleSuffix: Bing Web Search APIs - Cognitive Services
description: Bemutatja, hogyan tölthet fel képeket a Bing Visual Search API, majd a kép információival.
services: cognitive-services
author: swhite-msft
manager: rosh
ms.service: cognitive-services
ms.technology: bing-visual-search
ms.topic: article
ms.date: 5/16/2018
ms.author: scottwhi
ms.openlocfilehash: 41e0855b126ca6e54d0a487a88fe59a0be6f72f6
ms.sourcegitcommit: 0b05bdeb22a06c91823bd1933ac65b2e0c2d6553
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/17/2018
ms.locfileid: "39071995"
---
# <a name="your-first-bing-visual-search-query-in-java"></a>Az első a Bing vizuális keresési lekérdezés Java nyelven

A Bing Visual Search API az Ön által megadott kép adatait adja vissza. Megadhatja a kép URL-címét a képet, egy jogkivonat, vagy egy kép feltöltésével insights használatával. Ezek a beállítások kapcsolatos információkért lásd: [Mi az a Bing Visual Search API?](../overview.md) Ez a cikk bemutatja a kép feltöltése. Kép feltöltése mobil forgatókönyveknél, ahol, egy jól ismert tereptárgyak egy képet, majd az információk hasznosak lehetnek. Az insights például magukban foglalhatják a tereptárgyak kapcsolatos trivia. 

Ha a helyi rendszerképet tölt fel, az alábbiakban látható az űrlapadatok szerepelnie kell a bejegyzés törzse. Az űrlap adatait tartalmaznia kell a tartalom-szabályozó fejléc. A `name` paraméter "image" értékre kell állítani, és a `filename` paraméter bármilyen karakterlánc lehet beállítani. A képernyő tartalmát a rendszerkép a bináris. A maximális képméret tölthet fel érték 1 MB. 

```
--boundary_1234-abcd
Content-Disposition: form-data; name="image"; filename="myimagefile.jpg"

ÿØÿà JFIF ÖÆ68g-¤CWŸþ29ÌÄøÖ‘º«™æ±èuZiÀ)"óÓß°Î= ØJ9á+*G¦...

--boundary_1234-abcd--
```

Ez a cikk tartalmaz egy egyszerű konzolalkalmazást, amely a Bing Visual Search API-kérést küld, és a JSON keresési eredményeit jeleníti meg. Ez az alkalmazás Java nyelven van megírva, míg a API-ját kompatibilis minden programozási nyelvet, amely JSON elemzése és a HTTP-kéréseket a webes RESTful szolgáltatás. 


## <a name="prerequisites"></a>Előfeltételek

Szüksége lesz [JDK 7 vagy 8](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html) fordítsa le és futtassa ezt a kódot. Ha kedvencként, de egy szövegszerkesztőben elegendő használhatja a Java ide Környezethez.

Használhatja az ebben a rövid útmutatóban egy [az ingyenes próbaidőszak](https://azure.microsoft.com/try/cognitive-services/?api=bing-web-search-api) előfizetési kulcs vagy egy fizetős kulcsot.

## <a name="running-the-application"></a>Az alkalmazás futtatása

A következő bemutatja, hogyan töltse fel a rendszerképet MultipartEntityBuilder a Java használatával.

Az alkalmazás futtatásához kövesse az alábbi lépéseket:

1. Töltse le és telepítse a [gson könyvtár](https://github.com/google/gson). Emellett szerezheti be, Maven-n keresztül.
2. Hozzon létre egy új Java-projektet a kedvenc integrált Fejlesztőkörnyezetével vagy szerkesztőjével a.
3. A megadott kód hozzáadása egy nevű fájl `VisualSearch.java`.
4. Cserélje le a `subscriptionKey` az előfizetési kulcs-érték.
4. Cserélje le a `imagePath` a Rendszerkép feltöltése az elérési úttal rendelkező értékhez.
5. Futtassa a programot.


```java
package uploadimage;

import java.util.*;
import java.io.*;

/*
 * Gson: https://github.com/google/gson
 * Maven info:
 *     groupId: com.google.code.gson
 *     artifactId: gson
 *     version: 2.8.2
 *
 * Once you have compiled or downloaded gson-2.8.2.jar, assuming you have placed it in the
 * same folder as this file (BingImageSearch.java), you can compile and run this program at
 * the command line as follows.
 *
 * javac BingImageSearch.java -classpath .;gson-2.8.2.jar -encoding UTF-8
 * java -cp .;gson-2.8.2.jar BingImageSearch
 */

import com.google.gson.Gson;
import com.google.gson.GsonBuilder;
import com.google.gson.JsonObject;
import com.google.gson.JsonParser;

// http://hc.apache.org/downloads.cgi (HttpComponents Downloads) HttpClient 4.5.5

import org.apache.http.HttpEntity;
import org.apache.http.HttpResponse;
import org.apache.http.client.methods.HttpPost;
import org.apache.http.entity.ContentType;
import org.apache.http.entity.mime.MultipartEntityBuilder;
import org.apache.http.impl.client.CloseableHttpClient;
import org.apache.http.impl.client.HttpClientBuilder;


public class UploadImage2 {

    static String endpoint = "https://api.cognitive.microsoft.com/bing/v7.0/images/visualsearch";
    static String subscriptionKey = "<yoursubscriptionkeygoeshere";
    static String imagePath = "<pathtoyourimagetouploadgoeshere>";

    /**
     * @param args the command line arguments
     */
    public static void main(String[] args) {
        
        CloseableHttpClient httpClient = HttpClientBuilder.create().build();
        
        try {
            HttpEntity entity = MultipartEntityBuilder
                .create()
                .addBinaryBody("image", new File(imagePath))
                .build();

            HttpPost httpPost = new HttpPost(endpoint);
            httpPost.setHeader("Ocp-Apim-Subscription-Key", subscriptionKey);
            httpPost.setEntity(entity);
            HttpResponse response = httpClient.execute(httpPost);

            InputStream stream = response.getEntity().getContent();
            String json = new Scanner(stream).useDelimiter("\\A").next();

            System.out.println("\nJSON Response:\n");
            System.out.println(prettify(json));
        }
        catch (IOException e)
        {
            e.printStackTrace(System.out);
            System.exit(1);
        }
        catch (Exception e) {
            e.printStackTrace(System.out);
            System.exit(1);
        }
    }
    
    // pretty-printer for JSON; uses GSON parser to parse and re-serialize
    public static String prettify(String json_text) {
        JsonParser parser = new JsonParser();
        JsonObject json = parser.parse(json_text).getAsJsonObject();
        Gson gson = new GsonBuilder().setPrettyPrinting().create();
        return gson.toJson(json);
    }
    
}
```

## <a name="next-steps"></a>További lépések

[Lemezkép insights token használatával kapcsolatos elemzések lekérése](../use-insights-token.md)  
[A Bing Visual Search Rendszerkép feltöltése az oktatóanyag](../tutorial-visual-search-image-upload.md)
[Bing Visual Search egyoldalas alkalmazás oktatóanyaga](../tutorial-bing-visual-search-single-page-app.md)  
[Bing vizuális keresés áttekintése](../overview.md)  
[Próbálja ki](https://aka.ms/bingvisualsearchtryforfree)  
[Ingyenes próba hozzáférési kulcs lekérése](https://azure.microsoft.com/try/cognitive-services/?api=bing-visual-search-api)  
[A Bing Visual Search API-referencia](https://aka.ms/bingvisualsearchreferencedoc)

