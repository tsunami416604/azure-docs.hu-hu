---
title: 'Rövid útmutató: Videók keresése a REST API és a C# használatával – Bing videokeresés'
titleSuffix: Azure Cognitive Services
description: Ezzel a rövid útmutatóval videókeresési kérelmeket küldhet a Bing Video Search REST API-nak a C# használatával.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-video-search
ms.topic: quickstart
ms.date: 12/09/2019
ms.author: aahi
ms.openlocfilehash: 28c900adadf7d942c9e331e7b77a369db64acf55
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75382701"
---
# <a name="quickstart-search-for-videos-using-the-bing-video-search-rest-api-and-c"></a>Rövid útmutató: Videók keresése a Bing Video Search REST API-val és a C-vel #

Ezzel a rövid útmutatóval elsőként hívhatja meg a Bing Video Search API-t, és megtekintheti a JSON-válasz keresési eredményét. Ez az egyszerű C# alkalmazás http-videokeresési lekérdezést küld az API-nak, és megjeleníti a választ. Bár ez az alkalmazás C# nyelven lett íródott, az API egy RESTful-webszolgáltatás, azaz kompatibilis a legtöbb programnyelvvel.

A minta forráskódja elérhető [a GitHubon](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/dotnet/Search/BingVideoSearchv7.cs) további hibakezeléssel, funkciókkal és kódjegyzetekkel.

## <a name="prerequisites"></a>Előfeltételek
* A [Visual Studio 2017-es vagy újabb verzióinak](https://www.visualstudio.com/downloads/)bármely kiadása.
* A [Json.NET](https://www.newtonsoft.com/json) keretrendszer, amely NuGet-csomagként letölthető.
* Linux/MacOS rendszer esetében az alkalmazás a [Monóval](https://www.mono-project.com/) futtatható.

[!INCLUDE [cognitive-services-bing-video-search-signup-requirements](../../../../includes/cognitive-services-bing-video-search-signup-requirements.md)]

## <a name="create-and-initialize-a-project"></a>Projekt létrehozása és inicializálása

1. Hozzon létre egy új konzolmegoldást a Visual Studióban. Ezután adja hozzá a következő névtereket a fő kódfájlhoz.

    ```csharp
    using System;
    using System.Text;
    using System.Net;
    using System.IO;
    using System.Collections.Generic;
    ```

2. Adjon hozzá változókat az előfizetési kulcshoz, a végponthoz és a keresési kifejezéshez. `uriBase`lehet az alábbi globális végpont, vagy az [egyéni altartomány](../../../cognitive-services/cognitive-services-custom-subdomains.md) végpont jatthatja az erőforrás azure portalján.

    ```csharp
    const string accessKey = "enter your key here";
    const string uriBase = "https://api.cognitive.microsoft.com/bing/v7.0/videos/search";
    const string searchTerm = "kittens";
    ```

### <a name="create-a-struct-to-format-the-bing-video-search-api-response"></a>Struct létrehozása a Bing Video Search API-válasz formázásához

1. Definiáljon egy `SearchResult` struktúrát, amely a képkeresés eredményeit, valamint a fejlécadatokat tartalmazza.

    ```csharp
    struct SearchResult
        {
            public String jsonResult;
            public Dictionary<String, String> relevantHeaders;
        }
    ```

## <a name="create-and-handle-a-video-search-request"></a>Videokeresési kérelem létrehozása és kezelése

Hozzon létre egy `BingVideoSearch` nevű metódust, amely meghívja az API-t, majd állítsa a visszatérési típust a korábban létrehozott `SearchResult` struktúrára. A módszerben hajtsa végre a következő lépéseket:

1. Hozza létre a keresési kérés URI-ját. Vegye figyelembe, hogy a Keresés keresési kifejezést a karakterlánchoz való hozzáfűzés előtt formázni kell.

    ```csharp
    
    static SearchResult BingVideoSearch(string toSearch){
    
        var uriQuery = uriBase + "?q=" + Uri.EscapeDataString(toSearch);
    //...
    ```

2. A webes kérés végrehajtása a `Ocp-Acpim-Subscription-Key` kulcs fejléchez `HttpWebResponse` való hozzáadásával és az API-válasz tárolására egy objektum használatával. Ezután `StreamReader` használja a a JSON-karakterlánc bekapásához.

    ```csharp
    //...
    WebRequest request = HttpWebRequest.Create(uriQuery);
    request.Headers["Ocp-Apim-Subscription-Key"] = accessKey;
    HttpWebResponse response = (HttpWebResponse)request.GetResponseAsync().Result;
    string json = new StreamReader(response.GetResponseStream()).ReadToEnd();
    //...
    ```

## <a name="process-the-result"></a>Az eredmény feldolgozása

1. Hozza létre a keresési eredmény objektumát, és nyerje ki a Bing HTTP-fejléceit. Ezután `searchResult` adja vissza az objektumot. 

    ```csharp
    var searchResult = new SearchResult();
    searchResult.jsonResult = json;
    searchResult.relevantHeaders = new Dictionary<String, String>();

    // Extract Bing HTTP headers
    foreach (String header in response.Headers)
    {
        if (header.StartsWith("BingAPIs-") || header.StartsWith("X-MSEdge-"))
            searchResult.relevantHeaders[header] = response.Headers[header];
    }
    return searchResult;
    ```

2. Ezután kinyomtathatja a választ.

    ```csharp
    Console.WriteLine(result.jsonResult);
    ```

## <a name="example-json-response"></a>Példa JSON-válaszra 

A rendszer JSON formátumban ad vissza egy sikeres választ a következő példában látható módon:

```json
{
    "_type": "Videos",
    "instrumentation": {},
    "readLink": "https://api.cognitive.microsoft.com/api/v7/videos/search?q=kittens",
    "webSearchUrl": "https://www.bing.com/videos/search?q=kittens",
    "totalEstimatedMatches": 1000,
    "value": [
        {
            "webSearchUrl": "https://www.bing.com/videos/search?q=kittens&view=...",
            "name": "Top 10 cute kitten videos compilation",
            "description": "HELP HOMELESS ANIMALS AND WIN A PRIZE BY CHOOSING...",
            "thumbnailUrl": "https://tse4.mm.bing.net/th?id=OVP.n1aE_Oikl4MtzBb...",
            "datePublished": "2014-11-12T22:47:36.0000000",
            "publisher": [
                {
                    "name": "Fabrikam"
                }
            ],
            "creator": {
                "name": "Marcus Appel"
            },
            "isAccessibleForFree": true,
            "contentUrl": "https://www.fabrikam.com/watch?v=8HVWitAW-Qg",
            "hostPageUrl": "https://www.fabrikam.com/watch?v=8HVWitAW-Qg",
            "encodingFormat": "h264",
            "hostPageDisplayUrl": "https://www.fabrikam.com/watch?v=8HVWitAW-Qg",
            "width": 480,
            "height": 360,
            "duration": "PT3M52S",
            "motionThumbnailUrl": "https://tse4.mm.bing.net/th?id=OM.j4QyJAENJphdZQ_1501386166&pid=Api",
            "embedHtml": "<iframe width=\"1280\" height=\"720\" src=\"https://www.fabrikam.com/embed/8HVWitAW-Qg?autoplay=1\" frameborder=\"0\" allowfullscreen></iframe>",
            "allowHttpsEmbed": true,
            "viewCount": 7513633,
            "thumbnail": {
                "width": 300,
                "height": 168
            },
            "videoId": "655D98260D012432848F6558260D012432848F",
            "allowMobileEmbed": true,
            "isSuperfresh": false
        },
        . . .
    ],
    "nextOffset": 36,
    "queryExpansions": [
        {
            "text": "Kittens Meowing",
            "displayText": "Meowing",
            "webSearchUrl": "https://www.bing.com/videos/search?q=Kittens+Meowing...",
            "searchLink": "https://api.cognitive.microsoft.com/api/v7/videos/search...",
            "thumbnail": {
                "thumbnailUrl": "https://tse3.mm.bing.net/th?q=Kittens+Meowing&pid..."
            }
        },
        {
            "text": "Funny Kittens",
            "displayText": "Funny",
            "webSearchUrl": "https://www.bing.com/videos/search?q=Funny+Kittens...",
            "searchLink": "https://api.cognitive.microsoft.com/api/v7/videos/search...",
            "thumbnail": {
                "thumbnailUrl": "https://tse3.mm.bing.net/th?q=Funny+Kittens&..."
            }
        },
        . . .
    ],
    "pivotSuggestions": [
        {
            "pivot": "kittens",
            "suggestions": [
                {
                    "text": "Cat",
                    "displayText": "Cat",
                    "webSearchUrl": "https://www.bing.com/videos/search?q=Cat...",
                    "searchLink": "https://api.cognitive.microsoft.com/api/v7/videos/search?...",
                    "thumbnail": {
                        "thumbnailUrl": "https://tse3.mm.bing.net/th?q=Cat&pid=Api..."
                    }
                },
                {
                    "text": "Feral Cat",
                    "displayText": "Feral Cat",
                    "webSearchUrl": "https://www.bing.com/videos/search?q=Feral+Cat...",
                    "searchLink": "https://api.cognitive.microsoft.com/api/v7/videos/search...",
                    "thumbnail": {
                        "thumbnailUrl": "https://tse3.mm.bing.net/th?q=Feral+Cat&pid=Api&..."
                    }
                }
            ]
        }
    ],
    "relatedSearches": [
        {
            "text": "Kittens Being Born",
            "displayText": "Kittens Being Born",
            "webSearchUrl": "https://www.bing.com/videos/search?q=Kittens+Being+Born...",
            "searchLink": "https://api.cognitive.microsoft.com/api/v7/videos/search?...",
            "thumbnail": {
                "thumbnailUrl": "https://tse1.mm.bing.net/th?q=Kittens+Being+Born&pid=..."
            }
        },
        . . .
    ]
}
```

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Egyoldalas webalkalmazás készítése](../tutorial-bing-video-search-single-page-app.md)

## <a name="see-also"></a>Lásd még 

 [Mi az a Bing Video Search API?](../overview.md)
