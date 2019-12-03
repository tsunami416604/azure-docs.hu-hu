---
title: Hasonló képek keresése az előző keresésekben a ImageInsightsToken-Bing Visual Search használatával
titleSuffix: Azure Cognitive Services
description: A ImageInsightsToken által megadott rendszerképek URL-címeinek beszerzéséhez használja a Bing Visual Search SDK-t.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-visual-search
ms.topic: tutorial
ms.date: 11/29/2019
ms.author: aahi
ms.openlocfilehash: dff96b19f40c2d897b6a018a4c46cec60f8aa201
ms.sourcegitcommit: 48b7a50fc2d19c7382916cb2f591507b1c784ee5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/02/2019
ms.locfileid: "74689307"
---
# <a name="find-similar-images-from-previous-searches-using-imageinsightstoken"></a>Hasonló képek keresése az előző keresésekben a ImageInsightsToken használatával

A Visual Search SDK lehetővé teszi, hogy az előző, `ImageInsightsToken`okat visszaadó keresésekkel online képeket keressen. Ez az alkalmazás beolvas egy `ImageInsightsToken`, és a tokent használja egy későbbi keresésben. Ezután elküldi a `ImageInsightsToken` a Bingnek, és olyan eredményeket ad vissza, amelyek tartalmazzák Bing Search URL-címeket és a hasonló rendszerképek URL-címeit.

Az oktatóanyag teljes forráskódja a [githubon](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/Tutorials/Bing-Visual-Search/BingVisualSearchInsightsTokens.cs)található további hibakezelés és jegyzetek használatával érhető el.

## <a name="prerequisites"></a>Előfeltételek

* A [Visual Studio 2019](https://www.visualstudio.com/downloads/)bármely kiadása.
* Ha Linux/MacOS rendszert használ, akkor az alkalmazást a [mono](https://www.mono-project.com/)használatával futtathatja.
* A NuGet Visual Search és Image Search csomagokat.
    - Az Megoldáskezelő a Visual Studióban kattintson a jobb gombbal a projektre, és válassza a **NuGet-csomagok kezelése** menüpontot a menüből. Telepítse a `Microsoft.Azure.CognitiveServices.Search.CustomSearch` csomagot és a `Microsoft.Azure.CognitiveServices.Search.ImageSearch` csomagot. A NuGet-csomagok telepítése a következőket is telepíti:
        - Microsoft.Rest.ClientRuntime
        - Microsoft.Rest.ClientRuntime.Azure
        - Newtonsoft.Json


[!INCLUDE [cognitive-services-bing-visual-search-signup-requirements](../../../includes/cognitive-services-bing-visual-search-signup-requirements.md)]

## <a name="get-the-imageinsightstoken-from-the-bing-image-search-sdk"></a>A ImageInsightsToken beszerzése a Bing Image Search SDK-ból

Ez az alkalmazás az [BING Image Search SDK](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/image-search-sdk-quickstart)-val beszerzett `ImageInsightsToken` használ. Egy új C# konzol alkalmazásban hozzon létre egy ügyfelet az API meghívásához `ImageSearchClient()`használatával. Ezután használja a `SearchAsync()`t a lekérdezéssel:

```csharp
var client = new ImageSearchClient(new Microsoft.Azure.CognitiveServices.Search.ImageSearch.ApiKeyServiceClientCredentials(subKey));
var imageResults = client.Images.SearchAsync(query: "canadian rockies").Result;
Console.WriteLine("Search images for query \"canadian rockies\"");
```

Tárolja az első keresési eredményt `imageResults.Value.First()`használatával, majd tárolja a képelemzés `ImageInsightsToken`ét.

```csharp
String insightTok = "None";
if (imageResults.Value.Count > 0)
{
    var firstImageResult = imageResults.Value.First();
    insightTok = firstImageResult.ImageInsightsToken;
}
else
{
    insightTok = "None found";
    Console.WriteLine("Couldn't find image results!");
}
```

Ezt a `ImageInsightsToken` küldi el a rendszer a kérések Bing Visual Search.

## <a name="add-the-imageinsightstoken-to-a-visual-search-request"></a>A ImageInsightsToken hozzáadása Visual Search kérelemhez

Visual Search kérelem `ImageInsightsToken`ának megadása egy `ImageInfo` objektum létrehozásával az Bing Visual Search által küldött válaszokban szereplő `ImageInsightsToken`.

```csharp
ImageInfo ImageInfo = new ImageInfo(imageInsightsToken: insightsTok);
```

## <a name="use-bing-visual-search-to-find-images-from-an-imageinsightstoken"></a>Képek keresése a ImageInsightsToken Bing Visual Search használatával

A `VisualSearchRequest` objektum a keresendő `ImageInfo` rendszerképpel kapcsolatos információkat tartalmaz. A `VisualSearchMethodAsync()` metódus az eredményeket kéri le. Nem kell megadnia bináris képfájlt, mert a rendszerképet a jogkivonat jelképezi.

```csharp
VisualSearchRequest VisualSearchRequest = new VisualSearchRequest(ImageInfo);

var visualSearchResults = client.Images.VisualSearchMethodAsync(knowledgeRequest: VisualSearchRequest).Result;

```

## <a name="iterate-through-the-visual-search-results"></a>Iteráció a Visual Search eredményei között

A Visual Search eredményei `ImageTag` objektumok. Mindegy egyes címke `ImageAction` objektumok listáját tartalmazza. Minden `ImageAction` tartalmaz egy `Data` mezőt, amely a művelet típusától függő értékek listáját tartalmazza. Megismételheti a `visualSearchResults.Tags``ImageTag` objektumait, például lekérheti a `ImageAction` címkét. Az alábbi minta a `PagesIncluding` műveletek részleteit nyomtatja ki:

```csharp
if (visualSearchResults.Tags.Count > 0)
{
    // List of tags
    foreach (ImageTag t in visualSearchResults.Tags)
    {
        foreach (ImageAction i in t.Actions)
        {
            Console.WriteLine("\r\n" + "ActionType: " + i.ActionType + " WebSearchURL: " + i.WebSearchUrl);

            if (i.ActionType == "PagesIncluding")
            {
                foreach (ImageObject o in (i as ImageModuleAction).Data.Value)
                {
                    Console.WriteLine("ContentURL: " + o.ContentUrl);
                }
            }
        }
    }
}
```

### <a name="pagesincluding-actiontypes"></a>PagesIncluding ActionTypes

A tényleges képurl-címek műveleti típusokból való lekéréséhez olyan leadott műveletre van szükség, amely egy `ActionType` beolvasása `ImageModuleAction`, amely egy `Data` elemet tartalmaz az értékek listájával. Minden egyes érték egy kép URL-je.  A következőkben a `PagesIncluding` Művelettípus `ImageModuleAction`, és beolvassa az értékeket:

```csharp
    if (i.ActionType == "PagesIncluding")
    {
        foreach(ImageObject o in (i as ImageModuleAction).Data.Value)
        {
            Console.WriteLine("ContentURL: " + o.ContentUrl);
        }
    }
```

További információk ezekről az adattípusokról: [Képek – Visual Search](https://docs.microsoft.com/rest/api/cognitiveservices/bingvisualsearch/images/visualsearch).

## <a name="returned-urls"></a>Visszaadott URL-címek

A teljes alkalmazás a következő URL-címeket adja vissza:

|Művelettípus  |URL-cím  | |
|---------|---------|---------|
|MoreSizes – > WebSearchUrl     |         |
|VisualSearch – > WebSearchUrl     |         |
|ImageById – > WebSearchUrl    |         |
|RelatedSearches – > WebSearchUrl:    |         |
|DocumentLevelSuggestions – > WebSearchUrl:     |         |
|TopicResults – > WebSearchUrl    | https:\//www.bing.com/cr?IG=3E32CC6CA5934FBBA14ABC3B2E4651F9&CID=1BA795A21EAF6A63175699B71FC36B7C&rd=1&h=BcQifmzdKFyyBusjLxxgO42kzq1Geh7RucVVqvH-900&v=1&r=https%3a%2f%2fwww.bing.com%2fdiscover%2fcanadian%2brocky&p=DevEx,5823.1       |
|ImageResults – > WebSearchUrl    |  https:\//www.bing.com/cr?IG=3E32CC6CA5934FBBA14ABC3B2E4651F9&CID=1BA795A21EAF6A63175699B71FC36B7C&rd=1&h=PV9GzMFOI0AHZp2gKeWJ8DcveSDRE3fP2jHDKMpJSU8&v=1&r=https%3a%2f%2fwww.bing.com%2fimages%2fsearch%3fq%3doutdoor&p=DevEx,5831.1       |

Ahogy az a fentiekben látható, a `TopicResults` és a `ImageResults` típus a kapcsolódó képek lekérdezéseit tartalmazza. A Bing keresési eredményeire mutató URL-címek.

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Visual Search egyoldalas Webalkalmazás létrehozása](tutorial-bing-visual-search-single-page-app.md)
