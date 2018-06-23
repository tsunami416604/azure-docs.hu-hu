---
title: Első .gif képek - Microsoft kognitív szolgáltatások |} Microsoft Docs
description: Hogyan lehet .gif képek kapcsolatban további információkat a kép Bing keresési API segítségével.
services: cognitive-services
author: MikeDodaro
manager: rosh
ms.service: cognitive-services
ms.component: bing-image-search
ms.topic: article
ms.date: 04/24/2018
ms.author: rosh, v-gedod
ms.openlocfilehash: 10e922b0cd15868bfe8f09b3846c76a368052e69
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/23/2018
ms.locfileid: "35349326"
---
# <a name="search-for-gif-images"></a>.Gif képek keresése
A kép Bing keresési API lehetővé teszi a leginkább releváns .gif képek a teljes weben is kereshet.  A fejlesztők a különböző beszélgetés forgatókönyvekben kommunikáció zajlik GIF integrálva. 

A következő URL-címet az a lekérdezés animált .gif lemezképek.
````
https://api.cognitive.microsoft.com/bing/v7.0/images/search?q=interesting&imageType=AnimatedGif&mkt=en-us
````
A [q](https://docs.microsoft.com/en-us/rest/api/cognitiveservices/bing-images-api-v7-reference#query) paraméter határozza meg a keresési feltételeket.  Az előző lekérdezés is megadja `animatedGif` használatával a [imageType](https://docs.microsoft.com/en-us/rest/api/cognitiveservices/bing-images-api-v7-reference#imagetype) paraméter szűréséhez.

Példák az eredmények megtekintéséhez használja a következő URL-cím bing.com kereséséhez.
````
https://www.bing.com/images/search?q=interesting&qft=%20filterui%3Aphoto-animatedgif 

````
## <a name="query-parameters"></a>Lekérdezési paraméterek

Lekérdezés-paraméterek és beállítások kapcsolatos további információkért tekintse meg a [kép keresési API-referencia](https://docs.microsoft.com/en-us/rest/api/cognitiveservices/bing-images-api-v7-reference#query-parameters). A következő egy példa a cím alatt [például keressen rá a Java használatával animált gif](#gifExample).

## <a name="tips-and-suggestions"></a>Tippek és javaslatok

- Megadhat [maxFileSize](https://docs.microsoft.com/en-us/rest/api/cognitiveservices/bing-images-api-v7-reference#maxfilesize) és [minFileSize](https://docs.microsoft.com/en-us/rest/api/cognitiveservices/bing-images-api-v7-reference#minfilesize) paraméterek. Javasoljuk, hogy a maxFileSize beállítása = 2000000, mivel az indexben GIF többsége a 2MB.  Azt is lehetővé teszi az adatok mérete szabályozására, ha sávszélesség problémát jelent, többek között a mobil cellás forgatókönyvek.
- Észlelt a teljesítmény javítása érdekében betölteni a miniatűr a forrás URL-címe betöltése előtt.  
- Első futtatásakor vagy a követően lap felhasználói ahol még nem rendelkezik a felhasználói lekérdezésekhez, próbálja ki a trendekkel gif-keresések használhatja a a [trendek lemezképek API](trending-images.md).
- A három beállítás a [biztonságos keresés](https://docs.microsoft.com/en-us/rest/api/cognitiveservices/bing-images-api-v7-reference#safesearch) paraméter.  A `strict` beállítás tiltja a felnőtt tartalom. 
- Lásd: [mkt](supported-countries-markets.md) nyelvet és a támogatott helyek teljes listáját.
- *AnimatedGifHttps* csak beolvasása animált gif-lemezképeket, amelyek a https-címen. A biztonság érdekében a számos alkalmazás külső webes hivatkozások kapcsolatot igényelnek a HTTPS-kapcsolaton keresztül. Például az Apple App Store kapcsolat webszolgáltatásokhoz HTTPS, amely titkosítja a felhasználói adatok a biztonságos adatátvitelhez szükséges.

<a name="gifExample" />
## <a name="example-search-for-animated-gif-using-java"></a>Animált gif Java használatával például keresése

Animált .gif képek keresi meg a következő URL-címe: `q=interesting`
````
https://api.cognitive.microsoft.com/bing/v7.0/images/search?q=interesting&imageType=AnimatedGif&mkt=en-us

````
Az alábbi példában látható, az URL-lekérdezés szükséges [Ocp-Apim-előfizetés-kulcs](https://docs.microsoft.com/en-us/rest/api/cognitiveservices/bing-images-api-v7-reference#headers) fejléc.

A következő Java-példában alapszik, és elküldi a kérelmet.

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
A kód az alábbi eredményeket kap a JSON-objektumként:

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
- [C# gyors üzembe helyezés](quickstarts/csharp.md)
- [Útmutató kép keresőalkalmazás egyoldalas](tutorial-bing-image-search-single-page-app.md)