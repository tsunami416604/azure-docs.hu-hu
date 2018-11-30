---
title: 'Rövid útmutató: Vizuális keresési lekérdezés létrehozása Java nyelven – Bing Visual Search'
titleSuffix: Azure Cognitive Services
description: Bemutatja, hogyan tölthet fel egy képet a Bing Visual Search API-ba, és kaphat a képpel kapcsolatos megállapításokat.
services: cognitive-services
author: swhite-msft
manager: cgronlun
ms.service: cognitive-services
ms.component: bing-visual-search
ms.topic: quickstart
ms.date: 5/16/2018
ms.author: scottwhi
ms.openlocfilehash: c1b63b12a48f5ccfb1a396ffa9282249b03893fe
ms.sourcegitcommit: 5aed7f6c948abcce87884d62f3ba098245245196
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/28/2018
ms.locfileid: "52445174"
---
# <a name="quickstart-your-first-bing-visual-search-query-in-java"></a>Rövid útmutató: Az első Bing Visual Search-lekérdezés létrehozása Java nyelven

A Bing Visual Search API információkat ad vissza egy Ön által megadott képről. A kép feltöltéséhez használhatja a kép URL-címét, egy megállapítási jogkivonatot, vagy feltöltheti magát a képet. Információ ezekről a lehetőségekről: [Mi a Bing Visual Search API?](../overview.md) Ez a cikk egy kép feltöltését mutatja be. A képfeltöltés olyan mobil forgatókönyveknél lehet hasznos, amikor képet készít egy ismert nevezetességről, amelyről információt szeretne kapni. Az információk között szerepelhetnek például az adott nevezetességre vonatkozó érdekességek. 

Ha helyi képet tölt fel, az alábbiakban láthatja a POST-törzsben kötelezően megadandó űrlapadatokat. Az űrlapadatoknak tartalmazniuk kell a Tartalom-Témakör fejlécet. A `name` paraméter értéke „image” legyen, a `filename` paraméter értéke viszont bármilyen sztring lehet. Az űrlap tartalmát a kép bináris adatai adják. A legnagyobb feltölthető képméret 1 MB. 

```
--boundary_1234-abcd
Content-Disposition: form-data; name="image"; filename="myimagefile.jpg"

ÿØÿà JFIF ÖÆ68g-¤CWŸþ29ÌÄøÖ‘º«™æ±èuZiÀ)"óÓß°Î= ØJ9á+*G¦...

--boundary_1234-abcd--
```

Ez a cikk egy egyszerű konzolalkalmazást ismertet, amely Bing Visual Search API-kérést küld, majd megjeleníti a JSON-keresés eredményeit. Bár ez az alkalmazás Java nyelven lett megírva, maga az API viszont egy RESTful-webszolgáltatás, azaz kompatibilis minden olyan programozási nyelvvel, amely képes HTTP-kérések kezdeményezésére és JSON-elemzésre. 


## <a name="prerequisites"></a>Előfeltételek
Ebben a rövid, szüksége lesz egy előfizetést, S9 árkategória elindításához, ahogyan [Cognitive Services díjszabás – keresési Bing-API](https://azure.microsoft.com/en-us/pricing/details/cognitive-services/search-api/). 

Előfizetés indítása az Azure Portalon:
1. A szövegmezőbe, amely szerint az Azure portal tetején adja meg a "BingSearchV7" `Search resources, services, and docs`.  
2. Marketplace-en a legördülő listában, válassza a `Bing Search v7`.
3. Adja meg `Name` az új erőforrás.
4. Válassza ki `Pay-As-You-Go` előfizetés.
5. Válassza ki `S9` tarifacsomag.
6. Kattintson a `Enable` az előfizetés indításához.

A kód lefordításához és futtatásához a [JDK 7 vagy 8](https://aka.ms/azure-jdks) telepítése szükséges. Ha van kedvence, használhat Java IDE-t vagy egy szövegszerkesztőt is.

## <a name="running-the-application"></a>Az alkalmazás futtatása

Az alábbiakban láthatja, hogyan töltheti fel a képet a MultipartEntityBuilder használatával Java nyelven.

Az alkalmazás futtatásához kövesse az alábbi lépéseket:

1. Töltse le vagy telepítse a [Gson-kódtárat](https://github.com/google/gson). Ezt beszerezheti a Mavenen keresztül is.
2. Hozzon létre egy új Java-projektet a kedvenc IDE-környezetében vagy szerkesztőjében.
3. Adja hozzá a megadott kódot egy `VisualSearch.java` nevű fájlban.
4. Cserélje le a `subscriptionKey` értéket saját előfizetői azonosítójára.
4. Az `imagePath` értéket cserélje le a feltölteni kívánt kép elérési útjára.
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

[Képpel kapcsolatos információk lekérése Insights-jogkivonat segítségével](../use-insights-token.md)  
[Bing Visual Search képfeltöltés – oktatóanyag](../tutorial-visual-search-image-upload.md)
[Bing Visual Search egyoldalas alkalmazás – oktatóanyag](../tutorial-bing-visual-search-single-page-app.md)  
[Bing Visual Search – áttekintés](../overview.md)  
[Kipróbálás](https://aka.ms/bingvisualsearchtryforfree)  
[Ingyenes próbaverzióhoz tartozó hozzáférési kulcs lekérése](https://azure.microsoft.com/try/cognitive-services/?api=bing-visual-search-api)  
[Bing Visual Search API – referencia](https://aka.ms/bingvisualsearchreferencedoc)

