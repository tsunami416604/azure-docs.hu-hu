---
title: 'Gyors útmutató: Küldési keresési lekérdezések a Bing Image Search API és a Java'
titleSuffix: Azure Cognitive Services
description: Ez a rövid útmutató segítségével keresse meg és -rendszerképek keresése a weben a Bing Web Search API használatával.
services: cognitive-services
documentationcenter: ''
author: aahill
manager: cgronlun
ms.service: cognitive-services
ms.component: bing-image-search
ms.topic: article
ms.date: 8/20/2018
ms.author: aahi
ms.openlocfilehash: 519667af255e3c1b39d336e7a399ba215803daa5
ms.sourcegitcommit: e2ea404126bdd990570b4417794d63367a417856
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/14/2018
ms.locfileid: "45577479"
---
# <a name="quickstart-send-search-queries-using-the-bing-image-search-rest-api-and-java"></a>Gyors útmutató: Küldési keresési lekérdezések a Bing Image Search REST API és a Java használatával

Ez a rövid útmutató segítségével a Bing Image Search API az első hívását, és a egy JSON-választ kapnak. Ez egyszerű Java-alkalmazás egy keresési lekérdezést küld az API-t, és megjeleníti a nyers eredményeken.

Ezt az alkalmazást a Java nyelven van megírva, míg a API-ját egy REST-alapú webszolgáltatás szinte bármelyik programozási nyelvével kompatibilis.

Az ehhez a mintához forráskódja elérhető [a Githubon](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/java/Search/BingImageSearchv7Quickstart.java) további hibakezelést és jegyzetek kódot.

## <a name="prerequisites"></a>Előfeltételek

* A [Java fejlesztési Kit(JDK) 7 vagy 8](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html) 

* A [Gson könyvtár](https://github.com/google/gson)

[!INCLUDE [cognitive-services-bing-image-search-signup-requirements](../../../../includes/cognitive-services-bing-image-search-signup-requirements.md)]

## <a name="create-and-initialize-a-project"></a>Hozzon létre, és a egy projektet inicializálása

1. Hozzon létre egy új Java-projektet a kedvenc integrált Fejlesztőkörnyezetével vagy szerkesztőjével, és importálja a következő kódtárakra.

    ```java
    import java.net.*;
    import java.util.*;
    import java.io.*;
    import javax.net.ssl.HttpsURLConnection;
    import com.google.gson.Gson;
    import com.google.gson.GsonBuilder;
    import com.google.gson.JsonObject;
    import com.google.gson.JsonParser;
    ```

2. Az API-végpont, az előfizetési kulcs változók létrehozása és a keresési kifejezést.

    ```java
    static String subscriptionKey = "enter key here";
    static String host = "https://api.cognitive.microsoft.com";
    static String path = "/bing/v7.0/images/search";
    static String searchTerm = "tropical ocean";
    ```

## <a name="construct-the-search-request-and-query"></a>A keresési kérelmek és a lekérdezés létrehozása

1. Az utolsó lépésben a változók használatával az API-kérelem egy keresés URL-cím formátuma. Vegye figyelembe, hogy a keresett kifejezés URL-kódolású előtt kell hozzáfűzésekor a kérelemre.

    ```java
    // construct the search request URL (in the form of endpoint + query string)
    URL url = new URL(host + path + "?q=" +  URLEncoder.encode(searchQuery, "UTF-8"));
    HttpsURLConnection connection = (HttpsURLConnection)url.openConnection();
    connection.setRequestProperty("Ocp-Apim-Subscription-Key", subscriptionKey);
    ```

## <a name="receive-and-process-the-json-response"></a>Fogadják és feldolgozzák a JSON-válasz

1. A JSON-válasz fogadása a Bing Image Search API, és hozhat létre az eredményobjektum.

    ```java
    // receive JSON body
    InputStream stream = connection.getInputStream();
    String response = new Scanner(stream).useDelimiter("\\A").next();
    // construct result object for return
    SearchResults results = new SearchResults(new HashMap<String, String>(), response);
    ```
2. A Bing-kapcsolódó HTTP-fejlécek JSON-törzse egymástól
    ```java
    // extract Bing-related HTTP headers
    Map<String, List<String>> headers = connection.getHeaderFields();
    for (String header : headers.keySet()) {
        if (header == null) continue;      // may have null key
        if (header.startsWith("BingAPIs-") || header.startsWith("X-MSEdge-")) {
            results.relevantHeaders.put(header, headers.get(header).get(0));
        }
    }
    ```

3. Zárja be a streamet, és a válasz elemzéséhez. Eredményül kapott keresési találatok és a miniatűr összesített számát az első képen eredmény URL-címe. 

    ```java
    stream.close();
    JsonParser parser = new JsonParser();
    JsonObject json = parser.parse(result.jsonResponse).getAsJsonObject();
    //get the first image result from the JSON object, along with the total 
    //number of images returned by the Bing Image Search API. 
    String total = json.get("totalEstimatedMatches").getAsString();
    JsonArray results = json.getAsJsonArray("value");
    JsonObject first_result = (JsonObject)results.get(0);
    String resultURL = first_result.get("thumbnailUrl").getAsString();
    ```
4. Ne feledje, az alkalmazáskódban az előfizetési kulcs eltávolítása.

## <a name="json-response"></a>JSON-válasz

A Bing Image Search API érkező válaszokat a rendszer JSON formátumban adja vissza. Ez a minta-válasz a rendszer csonkolta egyetlen eredmény megjelenítéséhez.

```json
{
"_type":"Images",
"instrumentation":{
    "_type":"ResponseInstrumentation"
},
"readLink":"images\/search?q=tropical ocean",
"webSearchUrl":"https:\/\/www.bing.com\/images\/search?q=tropical ocean&FORM=OIIARP",
"totalEstimatedMatches":842,
"nextOffset":47,
"value":[
    {
        "webSearchUrl":"https:\/\/www.bing.com\/images\/search?view=detailv2&FORM=OIIRPO&q=tropical+ocean&id=8607ACDACB243BDEA7E1EF78127DA931E680E3A5&simid=608027248313960152",
        "name":"My Life in the Ocean | The greatest WordPress.com site in ...",
        "thumbnailUrl":"https:\/\/tse3.mm.bing.net\/th?id=OIP.fmwSKKmKpmZtJiBDps1kLAHaEo&pid=Api",
        "datePublished":"2017-11-03T08:51:00.0000000Z",
        "contentUrl":"https:\/\/mylifeintheocean.files.wordpress.com\/2012\/11\/tropical-ocean-wallpaper-1920x12003.jpg",
        "hostPageUrl":"https:\/\/mylifeintheocean.wordpress.com\/",
        "contentSize":"897388 B",
        "encodingFormat":"jpeg",
        "hostPageDisplayUrl":"https:\/\/mylifeintheocean.wordpress.com",
        "width":1920,
        "height":1200,
        "thumbnail":{
        "width":474,
        "height":296
        },
        "imageInsightsToken":"ccid_fmwSKKmK*mid_8607ACDACB243BDEA7E1EF78127DA931E680E3A5*simid_608027248313960152*thid_OIP.fmwSKKmKpmZtJiBDps1kLAHaEo",
        "insightsMetadata":{
        "recipeSourcesCount":0,
        "bestRepresentativeQuery":{
            "text":"Tropical Beaches Desktop Wallpaper",
            "displayText":"Tropical Beaches Desktop Wallpaper",
            "webSearchUrl":"https:\/\/www.bing.com\/images\/search?q=Tropical+Beaches+Desktop+Wallpaper&id=8607ACDACB243BDEA7E1EF78127DA931E680E3A5&FORM=IDBQDM"
        },
        "pagesIncludingCount":115,
        "availableSizesCount":44
        },
        "imageId":"8607ACDACB243BDEA7E1EF78127DA931E680E3A5",
        "accentColor":"0050B2"
    }
}
```

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Bing – Képkeresés egyoldalas alkalmazás oktatóanyag](../tutorial-bing-image-search-single-page-app.md)

## <a name="see-also"></a>Lásd még 

* [Mi az a Bing Képkeresés?](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/overview)  
* [Próbálja ki az online interaktív bemutatót](https://azure.microsoft.com/services/cognitive-services/bing-image-search-api/)  
* [Ingyenesen a Cognitive Services hozzáférési kulcs lekérése](https://azure.microsoft.com/try/cognitive-services/?api=bing-image-search-api)  
* [Az Azure Cognitive Services – dokumentáció](https://docs.microsoft.com/azure/cognitive-services)
* [A Bing Image Search API-referencia](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference)