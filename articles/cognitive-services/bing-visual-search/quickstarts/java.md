---
title: Java gyors üzembe helyezés, a Bing Visual keresés API |} Microsoft Docs
titleSuffix: Bing Web Search APIs - Cognitive Services
description: Bemutatja, hogyan lemezkép feltöltése a Bing Visual keresési API-hoz, és vissza a képet kaphat.
services: cognitive-services
author: swhite-msft
manager: rosh
ms.service: cognitive-services
ms.technology: bing-visual-search
ms.topic: article
ms.date: 5/16/2018
ms.author: scottwhi
ms.openlocfilehash: 8160302faa373d69b65afe6b68a8efb44442850d
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/23/2018
ms.locfileid: "35349374"
---
# <a name="your-first-bing-visual-search-query-in-java"></a>A Java első Bing Visual keresési lekérdezés

Bing Visual Search API, amely megadja a képfájl információt ad vissza. A kép URL-címe, lexikális elem, vagy egy kép feltöltésével egy insights segítségével megadhatja a lemezképet. További információ a kapcsolókról: [Bing Visual Search API újdonságai?](../overview.md) Ez a cikk bemutatja, hogy egy kép feltöltésével. Egy kép feltöltésével olyan mobil forgatókönyveknél, ahol készíthet egy képet arról a jól ismert jellegzetes, és arra vonatkozó információval segítségnyújtáshoz hasznos lehet. Például a feltárása a jellegzetes kapcsolatos trivia tartalmazhatnak. 

Ha feltölti egy helyi lemezképet, a következő jeleníti meg az űrlap adatait meg kell adni a FELADÁS egy vagy több törzsében. Az űrlap adatait tartalmaznia kell a tartalom-szabályozó fejlécben. A `name` paraméter "kép" értékre kell állítani, és a `filename` paraméter bármilyen karakterlánc lehet beállítani. A képernyő tartalmát a bináris kép. A maximális kép lehet, hogy feltölti mérete 1 MB. 

```
--boundary_1234-abcd
Content-Disposition: form-data; name="image"; filename="myimagefile.jpg"

ÿØÿà JFIF ÖÆ68g-¤CWŸþ29ÌÄøÖ‘º«™æ±èuZiÀ)"óÓß°Î= ØJ9á+*G¦...

--boundary_1234-abcd--
```

A cikk tartalmaz egy egyszerű konzolalkalmazást, amely a Bing Visual keresési API-kérést küld, és a JSON-keresési eredményeit jeleníti meg. Az alkalmazás Java nyelven van megírva, amíg az API-t olyan kompatibilis bármely programozási nyelv, amely HTTP-kérelmeket, és elemezni a JSON a RESTful webes szolgáltatás. 


## <a name="prerequisites"></a>Előfeltételek

Szüksége lesz [JDK 7 vagy 8](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html) fordításához, és futtassa a következő kódot. A Java IDE lehet használni, ha kedvenc, de elegendő egy szövegszerkesztőben.

A gyors üzembe helyezés, a segítségével egy [ingyenes próbaverzió](https://azure.microsoft.com/try/cognitive-services/?api=bing-web-search-api) előfizetés vagy egy fizetős billentyűt.

## <a name="running-the-application"></a>Az alkalmazás futtatása

A következő bemutatja, hogyan feltölti a lemezképet, a Java MultipartEntityBuilder használatával.

Az alkalmazás futtatásához kövesse az alábbi lépéseket:

1. Töltse le és telepítse a [gson könyvtár](https://github.com/google/gson). Is elérhető, Maven keresztül.
2. Hozzon létre egy új Java-projektet a kedvenc IDE vagy szerkesztő.
3. A megadott kód hozzáadása nevű fájlba `VisualSearch.java`.
4. Cserélje le a `subscriptionKey` az Előfizetés kulcs értékének.
4. Cserélje le a `imagePath` a kép töltse fel az elérési úttal rendelkező értékhez.
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

[Mélyebb betekintés insights token használatával kép](../use-insights-token.md)  
[Bing Visual keresési alkalmazás oktatóanyag](../tutorial-bing-visual-search-single-page-app.md)  
[Bing Visual keresési áttekintése](../overview.md)  
[Próbálja ki](https://aka.ms/bingvisualsearchtryforfree)  
[Egy ingyenes próba hozzáférési kulcs beszerzése](https://azure.microsoft.com/try/cognitive-services/?api=bing-visual-search-api)  
[Bing Visual keresési API-referencia](https://aka.ms/bingvisualsearchreferencedoc)

