---
title: 'Gyors útmutató: A Bing Image Search API-lekérdezések küldési keresési és'
titleSuffix: Azure Cognitive Services
description: Ez a rövid útmutató segítségével keresse meg és -rendszerképek keresése a weben a Bing Web Search API használatával.
services: cognitive-services
documentationcenter: ''
author: aahill
manager: cgronlun
ms.service: cognitive-services
ms.component: bing-image-search
ms.topic: article
ms.date: 9/07/2018
ms.author: aahi
ms.openlocfilehash: 22eb54f6adec0335dd89a5ae906117542bb11717
ms.sourcegitcommit: e2ea404126bdd990570b4417794d63367a417856
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/14/2018
ms.locfileid: "45580412"
---
# <a name="quickstart-send-search-queries-using-the-bing-image-search-api-and-c"></a>Gyors útmutató: Küldési keresési lekérdezések a Bing Image Search API és a C# használatával

Ez a rövid útmutató segítségével a Bing Image Search API az első hívását, és megtekintheti a JSON-válasz keresési eredményeket. Ez egyszerű C#-alkalmazás egy HTTP kép keresési lekérdezést küld az API-t, és a visszaadott első kép URL-címe.

Ezt az alkalmazást C# nyelven írt, míg a API-ját egy REST-alapú webszolgáltatás szinte bármelyik programozási nyelvével kompatibilis.

Az ehhez a mintához forráskódja elérhető [a Githubon](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/dotnet/Search/BingImageSearchv7Quickstart.cs) további hibakezelést és jegyzetek kódot.

## <a name="prerequisites"></a>Előfeltételek

* Bármely kiadása [Visual Studio 2017](https://www.visualstudio.com/downloads/).
* A [Json.NET](https://www.newtonsoft.com/json) framework NuGet-csomagként érhető el. 
* Linux/MacOS rendszeren használja, ha az alkalmazás használatával futtatható [Mono](http://www.mono-project.com/).

[!INCLUDE [cognitive-services-bing-image-search-signup-requirements](../../../../includes/cognitive-services-bing-image-search-signup-requirements.md)]

## <a name="create-and-initialize-a-project"></a>Hozzon létre, és a egy projektet inicializálása

1. Hozzon létre egy új konzol megoldást nevű `BingSearchApisQuickStart` a Visual Studióban. Ezután adja hozzá a következő névtereket a fő kódfájl.

    ```csharp
    using System;
    using System.Net;
    using System.IO;
    using System.Collections.Generic;
    using Newtonsoft.Json.Linq;
    ```

2. Az API-végpont, az előfizetési kulcs változók létrehozása és a keresési kifejezést.

    ```csharp
    //...
    namespace BingSearchApisQuickstart
    {
        class Program
        {
        // Replace the this string with your valid access key.
        const string subscriptionKey = "enter your key here";
        const string uriBase = "https://api.cognitive.microsoft.com/bing/v7.0/images/search";
        const string searchTerm = "tropical ocean";
    //...
    ```

## <a name="create-a-struct-to-format-the-bing-image-search-response"></a>Hozzon létre egy struct, formázhatja a Bing Képkeresés válasz

Adja meg egy `SearchResult` struct a eredményeit, és JSON-fejléc-információkat tartalmazza.
    
```csharp
    namespace BingSearchApisQuickstart
    {
        class Program
        {
        //...
            struct SearchResult
            {
                public String jsonResult;
                public Dictionary<String, String> relevantHeaders;
            }
//...
```

## <a name="create-a-method-to-send-search-requests"></a>Keresési kérelmek küldésére metódus létrehozása

Hozzon létre egy metódust `BingImageSearch` végez az API-hívás és a visszatérési típus beállítása a `SearchResult` struct, korábban létrehozott.

```csharp
//...
namespace BingSearchApisQuickstart
{
    //...
    class Program
    {
        //...
        static SearchResult BingImageSearch(string searchTerm)
        {
        }
//...
```

## <a name="create-and-handle-an-image-search-request"></a>Hozzon létre, és a egy rendszerképet a keresési kérelem kezelése
 
Az a `BingImageSearch` metódus, hajtsa végre az alábbi lépéseket.

1. Hozza létre a keresési kérés URI Azonosítóját. Vegye figyelembe, hogy a keresési kifejezés `toSearch` karakterláncot hozzáfűzésekor előtt kell formázni. 

    ```csharp
    static SearchResult BingImageSearch(string toSearch){

        var uriQuery = uriBase + "?q=" + Uri.EscapeDataString(toSearch);
    //...
    ```

2. A webes kérelem teljesítéséhez, és a válasz egy JSON-karakterlánc.

    ```csharp
    WebRequest request = WebRequest.Create(uriQuery);
    request.Headers["Ocp-Apim-Subscription-Key"] = subscriptionKey;
    HttpWebResponse response = (HttpWebResponse)request.GetResponseAsync().Result;
    string json = new StreamReader(response.GetResponseStream()).ReadToEnd();
    ```

3. Hozza létre a keresési eredmény objektumot, és csomagolja ki a Bing HTTP-fejléceket. Ezután térjen `searchResult`.

    ```csharp
    // Create the result object for return
    var searchResult = new SearchResult()
    {
        jsonResult = json,
        relevantHeaders = new Dictionary<String, String>()
    };

    // Extract Bing HTTP headers
    foreach (String header in response.Headers)
    {
        if (header.StartsWith("BingAPIs-") || header.StartsWith("X-MSEdge-"))
            searchResult.relevantHeaders[header] = response.Headers[header];
    }
    return searchResult;
    ```

## <a name="process-and-view-the-response"></a>Folyamat és a válasz megtekintése

1. A fő metódus hívása `BingImageSearch()` és a visszaadott válasz tárolása. Ezután deszerializálja a JSON-objektumba.

    ```csharp
    SearchResult result = BingImageSearch(searchTerm);
    //deserialize the JSON response from the Bing Image Search API
    dynamic jsonObj = Newtonsoft.Json.JsonConvert.DeserializeObject(result.jsonResult);
    ```

2. Az első visszaadott lemezképének lekérési `jsonObj`, majd nyomtassa ki a cím és a kép URL-CÍMÉT. 
    ```csharp
    var firstJsonObj = jsonObj["value"][0];
    Console.WriteLine("Title for the first image result: " + firstJsonObj["name"]+"\n");
    //After running the application, copy the output URL into a browser to see the image. 
    Console.WriteLine("URL for the first image result: " + firstJsonObj["webSearchUrl"]+"\n");
    ```  
       
3. Ellenőrizze, hogy az alkalmazás kódjának az előfizetési kulcs eltávolítása.

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