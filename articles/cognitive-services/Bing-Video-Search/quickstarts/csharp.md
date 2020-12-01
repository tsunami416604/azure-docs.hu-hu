---
title: 'Gyors útmutató: videók keresése a REST API és C#-Bing Video Search használatával'
titleSuffix: Azure Cognitive Services
description: Ezzel a rövid útmutatóval videó-keresési kéréseket küldhet a Bing Video Search REST API C# használatával.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-video-search
ms.topic: quickstart
ms.date: 10/22/2020
ms.author: clschott
ms.custom: devx-track-csharp
ms.openlocfilehash: 24e172edd47bd859e530536671b5e8df7e0d4c8f
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/30/2020
ms.locfileid: "96352558"
---
# <a name="quickstart-search-for-videos-using-the-bing-video-search-rest-api-and-c"></a>Gyors útmutató: videók keresése a Bing Video Search REST API és C használatával #

> [!WARNING]
> Bing Search API-k átkerülnek a Cognitive Servicesról Bing Search szolgáltatásokra. **2020. október 30-ig** a Bing Search új példányait az [itt](/bing/search-apis/bing-web-search/create-bing-search-service-resource)ismertetett eljárás követésével kell kiépíteni.
> A Cognitive Services használatával kiépített Bing Search API-k a következő három évben vagy a Nagyvállalati Szerződés végéig lesz támogatva, attól függően, hogy melyik történik először.
> Az áttelepítési utasításokért lásd: [Bing Search Services](/bing/search-apis/bing-web-search/create-bing-search-service-resource).

Ezzel a rövid útmutatóval megteheti az első hívást a Bing Video Search API. Ez az egyszerű C#-alkalmazás egy HTTP-videó keresési lekérdezést küld az API-nak, és megjeleníti a JSON-választ. Bár ez az alkalmazás C# nyelven íródott, az API egy REST-alapú webszolgáltatás, amely kompatibilis a legtöbb programozási nyelvvel.

A minta forráskódja elérhető [a githubon](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/dotnet/Search/BingVideoSearchv7.cs) további hibakezelés, funkciók és kódok megjegyzésekkel.

## <a name="prerequisites"></a>Előfeltételek

A .NET Core futtatásához be kell állítania a gépet. A telepítési utasításokat a [.net Core letöltések](https://dotnet.microsoft.com/download) lapon találja. Ezt az alkalmazást Windows, Linux, macOS vagy Docker-tárolóban is futtathatja. Telepítenie kell a kedvenc Kódszerkesztő alkalmazást. Az alábbi leírások a [Visual Studio Code](https://code.visualstudio.com/)-ot használják, amely egy nyílt forráskódú, platformfüggetlen szerkesztő. Azonban bármilyen eszközt használhat, amely kényelmesen használható.

[!INCLUDE [cognitive-services-bing-video-search-signup-requirements](../../../../includes/cognitive-services-bing-video-search-signup-requirements.md)]

## <a name="create-and-initialize-a-project"></a>Projekt létrehozása és inicializálása

Első lépésként hozzon létre egy új alkalmazást. Nyisson meg egy parancssort, és hozzon létre egy új könyvtárat az alkalmazáshoz. Legyen az aktuális könyvtár. Írja be a következő parancsot egy konzol ablakába:

```dotnetcli
dotnet new console --name VideoSearchClient
```

A fő metódus tetején fel kell vennie a következő `using` irányelvet, hogy a C# fordító felismerje a feladat-és JSON-típusokat:

```csharp
using System;
using System.Net.Http;
using System.Threading.Tasks;
using System.Collections.Generic;
using System.Text.Json;
using System.Text.Json.Serialization;
```

Adja hozzá a változókat az előfizetési kulcshoz, a végponthoz és a keresési kifejezéshez. Az érték esetében használhatja `uriBase` a globális végpontot a következő kódban, vagy használhatja az erőforráshoz tartozó Azure Portalban megjelenő [Egyéni altartomány](../../../cognitive-services/cognitive-services-custom-subdomains.md) -végpontot.

```csharp
// Replace the accessKey string value with your valid access key.
const string _accessKey = "enter your key here";

// Or use the custom subdomain endpoint displayed in the Azure portal for your resource.
const string _uriBase = "https://api.cognitive.microsoft.com/bing/v7.0/videos/search";

const string _searchTerm = "kittens";
```

Ezután frissítse a Main metódust, hogy az aszinkron metódusok is használhatók legyenek. Adja hozzá az aszinkron módosítót, és módosítsa a visszatérési típust a feladatra.

```csharp
static async Task Main(string[] args)
{
    
}
```

Most már van olyan programja, amely nem működik, de aszinkron módon működik. Javítsa ki.

## <a name="create-a-data-structure-to-hold-the-bing-video-search-api-response"></a>Adatstruktúra létrehozása a Bing Video Search API-válasz tárolására

Definiáljon egy `SearchResult` és `Video` osztályt a videó keresési eredményeinek tárolásához. Később további tulajdonságokat is hozzáadhat, ha a JSON-eredményből más mezőkre van szüksége.

```cscharp
class SearchResult
{
    [JsonPropertyName("totalEstimatedMatches")]
    public int TotalEstimatedMatches { get; set; }

    [JsonPropertyName("value")]
    public List<Video> Videos { get; set; }
}

class Video
{
    [JsonPropertyName("name")]
    public string Name { get; set; }

    [JsonPropertyName("description")]
    public string Description { get; set; }

    [JsonPropertyName("thumbnailUrl")]
    public string ThumbnailUrl { get; set; }

    [JsonPropertyName("contentUrl")]
    public string ContentUrl { get; set; }
}
```

## <a name="create-and-handle-a-video-search-request"></a>Videó-keresési kérelem létrehozása és kezelése

Az `HttpClient` API-hívás végrehajtásához használjuk. Először hozzá kell adnia a fejlécet `Ocp-Apim-Subscription-Key` és a hozzáférési kulcsot. 

```csharp
using var client = new HttpClient();
client.BaseAddress = new Uri(_uriBase);
client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", _accessKey);
```

Hozza létre a keresési kérés URI-ját. Formázza a keresési kifejezést, `_searchTerm` mielőtt hozzáfűzi a karakterlánchoz.

```csharp
var response = await client.GetAsync($"?q={Uri.EscapeDataString(_searchTerm)}");
```

## <a name="process-the-result"></a>Az eredmény feldolgozása

Ha a válasz sikeres volt, feldolgozhatjuk a JSON-adatfeldolgozást. A JSON-karakterlánc deszerializálása a `SearchResult` korábban létrehozott. Hurkot az eredményhez (ha van), és nyomtassa ki az eredményt a-konzolra.

```csharp
if (response.IsSuccessStatusCode)
{
    var json = await response.Content.ReadAsStringAsync();
    var result = JsonSerializer.Deserialize<SearchResult>(json);

    foreach (var video in result.Videos)
    {
        Console.WriteLine($"Name: {video.Name}");
        Console.WriteLine($"ContentUrl: {video.ContentUrl}");
        Console.WriteLine();
    }
}
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