---
title: GIF Képkeresés a Bing Image Search API használatával
titleSuffix: Azure Cognitive Services
description: A Bing Image Search API használatával .gif rendszerképek keresése a weben.
services: cognitive-services
author: MikeDodaro
manager: cgronlun
ms.service: cognitive-services
ms.component: bing-image-search
ms.topic: article
ms.date: 04/24/2018
ms.author: rosh
ms.custom: seodec2018
ms.openlocfilehash: 7536452d3ed0030b34e4f30deba56d6d8ae1957c
ms.sourcegitcommit: 1c1f258c6f32d6280677f899c4bb90b73eac3f2e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/11/2018
ms.locfileid: "53249311"
---
# <a name="search-for-gif-images"></a>GIF-rendszerképek keresése 

A Bing Image Search API lehetővé teszi, hogy a teljes webes a leginkább releváns .gif között is kereshet.  A fejlesztők beépíthetik vonzó GIF beszélgetés különböző forgatókönyvekben. 

A következő URL-címet az a lekérdezés animált .gif rendszerképeket.
````
https://api.cognitive.microsoft.com/bing/v7.0/images/search?q=interesting&imageType=AnimatedGif&mkt=en-us
````
A [q](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#query) paraméter adja meg a keresési feltételeket.  Az előző lekérdezés megad `animatedGif` használatával a [imageType](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#imagetype) szűrő a paramétere.

Példák az eredmények megtekintéséhez használja a következő URL-cím bing.com kereséséhez.
````
https://www.bing.com/images/search?q=interesting&qft=%20filterui%3Aphoto-animatedgif

````
## <a name="query-parameters"></a>Lekérdezési paraméterek

Lekérdezési paraméterek és beállítások kapcsolatos további információkért lásd: a [Image Search API-referencia](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#query-parameters). Egy példa a következő cím alatt [példában keressen a Java használatával animált gif](#gifExample).

## <a name="tips-and-suggestions"></a>Tippek és javaslatok

- Megadhat [maxFileSize](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#maxfilesize) és [minFileSize](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#minfilesize) paramétereket. Azt javasoljuk, hogy a beállítás a maxFileSize = 2000000, mint az indexben GIF többsége a 2MB.  Ez segít szabályozhatja az adatok mérete, ha a sávszélesség egy szempont, például mobil mobil forgatókönyveknél is.
- Mintavételezéskor a teljesítmény javítása érdekében töltse be a miniatűrre a forrás URL-címe betöltése előtt.  
- Első futtatási vagy alkotóelemeit lap felhasználói élményt, ahol még nem rendelkezik felhasználói lekérdezés, próbáljon a népszerű gif-keresések a segítségével a [népszerű API képeket](trending-images.md).
- A három beállítás érhető el a [biztonságos keresési](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#safesearch) paraméter.  A `strict` a beállítás letiltja a felnőtt tartalom.
- Lásd: [mkt](supported-countries-markets.md) nyelveket és a támogatott helyek teljes listáját.
- *AnimatedGifHttps* egyetlen értéket ad vissza animált gif-lemezképek, amelyek a https-címen. A biztonság érdekében a számos alkalmazás külső webes hivatkozások kapcsolatot igényelnek a https-kapcsolaton keresztül. Ha például az Apple App Store kapcsolódnia kell webszolgáltatások HTTPS protokollal, amely titkosítja a felhasználói adatok a biztonságos adatátvitelhez.

<a name="gifExample" />
## <a name="example-search-for-animated-gif-using-java"></a>A példában keressen a Java használatával animált gif

A következő URL-cím animált .gif képeket keres rá: `q=interesting`
````
https://api.cognitive.microsoft.com/bing/v7.0/images/search?q=interesting&imageType=AnimatedGif&mkt=en-us

````
Ahogy az az alábbi példában is látható, az URL-lekérdezés szükséges [Ocp-Apim-Subscription-Key](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#headers) fejléc.

A következő Java-példában összeállítja és elküldi a kérelmet.

````
package gifSearch;
import java.net.*;
import java.util.*;
import java.io.*;
import javax.net.ssl.HttpsURLConnection;

import com.google.gson.Gson;
import com.google.gson.GsonBuilder;
import com.google.gson.JsonObject;
import com.google.gson.JsonParser;

/*
 * Gson: https://github.com/google/gson
 * Maven info:
 *     groupId: com.google.code.gson
 *     artifactId: gson
 *     version: 2.8.1
 *
 * Once you have compiled or downloaded gson-2.8.1.jar, assuming you have placed it in the
 * same folder as this file (BingImageSearch.java), you can compile and run this program at
 * the command line as follows.
 *
 * javac GIFsearch.java -classpath .;gson-2.8.1.jar -encoding UTF-8
 * java -cp .;gson-2.8.1.jar GIFsearch
 */


public class GIFsearch {

    // Replace the subscriptionKey string value with your valid subscription key.
    static String subscriptionKey = "YOUR-ACCESS-KEY";

    static String host = "https://api.cognitive.microsoft.com";
    static String path = "/bing/v7.0/images/search";

    static String searchTerm = "interesting";

    public static SearchResults SearchImages (String searchQuery) throws Exception {
        // construct URL of search request (endpoint + query string)
        URL url = new URL(host + path + "?q=" +  URLEncoder.encode(searchQuery, "UTF-8") + "&imageType=AnimatedGif&mkt=en-us");
        HttpsURLConnection connection = (HttpsURLConnection)url.openConnection();
        connection.setRequestProperty("Ocp-Apim-Subscription-Key", subscriptionKey);

        // receive JSON body
        InputStream stream = connection.getInputStream();
        String response = new Scanner(stream).useDelimiter("\\A").next();

        // construct result object for return
        SearchResults results = new SearchResults(new HashMap<String, String>(), response);

        // extract Bing-related HTTP headers
        Map<String, List<String>> headers = connection.getHeaderFields();
        for (String header : headers.keySet()) {
            if (header == null) continue;      // may have null key
            if (header.startsWith("BingAPIs-") || header.startsWith("X-MSEdge-")) {
                results.relevantHeaders.put(header, headers.get(header).get(0));
            }
        }

        stream.close();
        return results;
    }

    // pretty-printer for JSON; uses GSON parser to parse and re-serialize
    public static String prettify(String json_text) {
        JsonParser parser = new JsonParser();
        JsonObject json = parser.parse(json_text).getAsJsonObject();
        Gson gson = new GsonBuilder().setPrettyPrinting().create();
        return gson.toJson(json);
    }

    public static void main (String[] args) {
        if (subscriptionKey.length() != 32) {
            System.out.println("Invalid Bing Search API subscription key!");
            System.out.println("Please paste yours into the source code.");
            System.exit(1);
        }

        try {
            System.out.println("Searching the Web for: " + searchTerm);

            SearchResults result = SearchImages(searchTerm);

            System.out.println("\nRelevant HTTP Headers:\n");
            for (String header : result.relevantHeaders.keySet())
                System.out.println(header + ": " + result.relevantHeaders.get(header));

            System.out.println("\nJSON Response:\n");
            System.out.println(prettify(result.jsonResponse));
        }
        catch (Exception e) {
            e.printStackTrace(System.out);
            System.exit(1);
        }
    }

}

//Container class for search results encapsulates relevant headers and JSON data
class SearchResults{
 HashMap<String, String> relevantHeaders;
 String jsonResponse;
 SearchResults(HashMap<String, String> headers, String json) {
     relevantHeaders = headers;
     jsonResponse = json;
 }
}

````

## <a name="results"></a>Results (Eredmények)
A kód lekéri a következő eredményeket JSON-objektumként:

```json
    {
      "webSearchUrl": "https://www.bing.com/images/search?view\u003ddetai...",
      "name": "Very Interesting GIF - Thats Very Interesting - ...",
      "thumbnailUrl": "https://tse1.mm.bing.net/th?id\u003dOIP.yJX6Vz345JPK...",
      "datePublished": "2017-03-12T01:35:00.0000000Z",
      "contentUrl": "https://media.contoso.co/images/c895fa573df8e493ca8d0dec7d93b/raw",
      "hostPageUrl": "https://www.contoso.co/view/thats-very-interesting-christi...",
      "contentSize": "1295633 B",
      "encodingFormat": "animatedgif",
      "hostPageDisplayUrl": "https://www.contoso.co/view/thats-very-christian...",
      "width": 440,
      "height": 186,
      "thumbnail": {
        "width": 474,
        "height": 200
      },
      "imageInsightsToken": "ccid_yJX6Vz34*mid_9FF0FFA42AADA1357F042443D2103B40EA...",
      "insightsMetadata": {
        "recipeSourcesCount": 0,
        "bestRepresentativeQuery": {
          "text": "That\u0027s Very Interesting",
          "displayText": "That\u0027s Very Interesting",
          "webSearchUrl": "https://www.bing.com/images/search?q\u003dThat..."
        },
        "pagesIncludingCount": 19,
        "availableSizesCount": 2
      },
      "imageId": "9FF0FFA42AADA1357F042443D21030EAAA225F",
      "accentColor": "62452D"
    },

```

## <a name="next-steps"></a>További lépések
- [C# – rövid útmutató](quickstarts/csharp.md)
- [Az oktatóanyag Képkeresés egyoldalas alkalmazás](tutorial-bing-image-search-single-page-app.md)
