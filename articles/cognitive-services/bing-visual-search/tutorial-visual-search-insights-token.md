---
title: Hasonló képek keresése a korábbi keresésekből a képelemzési tokenek és a Bing Visual Search API használatával
titleSuffix: Azure Cognitive Services
description: A Bing Visual Search ügyféltár segítségével leszeretné késelni a korábbi keresésekből származó képek URL-címeit.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-visual-search
ms.topic: tutorial
ms.date: 03/31/2020
ms.author: aahi
ms.openlocfilehash: ad24a8a194a11c3fd5f7f77ea8c52197d5438edc
ms.sourcegitcommit: efefce53f1b75e5d90e27d3fd3719e146983a780
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/01/2020
ms.locfileid: "80477920"
---
# <a name="tutorial-find-similar-images-from-previous-searches-using-an-image-insights-token"></a>Oktatóanyag: Hasonló képek keresése a korábbi keresésekből egy képelemzési token használatával

A Visual Search ügyfélkönyvtár lehetővé teszi, hogy a `ImageInsightsToken`korábbi keresésekből származó képeket online keresse meg, amelyek a . Ez az `ImageInsightsToken` alkalmazás lekérést kap egy, és használja a jogkivonatot egy későbbi keresés. Ezután elküldi `ImageInsightsToken` a Bing, és visszaadja az eredményeket, amelyek tartalmazzák a Bing Keresési URL-eket és URL-eket hasonló képek et talált online.

Az oktatóanyag teljes forráskódja további hibakezeléssel és jegyzetekkel található a [GitHubon.](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/Tutorials/Bing-Visual-Search/BingVisualSearchInsightsTokens.cs)

## <a name="prerequisites"></a>Előfeltételek

* A [Visual Studio 2019](https://www.visualstudio.com/downloads/)bármely kiadása.
* Ha Linux/MacOS-t használ, futtathatja ezt az alkalmazást a [Mono](https://www.mono-project.com/)használatával.
* A NuGet Visual Search és Image Search csomagok.
    - A Visual Studio Megoldáskezelőjében kattintson a jobb gombbal a projektre, és válassza a menü **NuGet csomagok kezelése parancsát.** Telepítse `Microsoft.Azure.CognitiveServices.Search.CustomSearch` a csomagot, `Microsoft.Azure.CognitiveServices.Search.ImageSearch` és a csomagot. A NuGet csomagok telepítése a következőket is telepíti:
        - Microsoft.Rest.ClientRuntime
        - Microsoft.Rest.ClientRuntime.Azure
        - Newtonsoft.Json


[!INCLUDE [cognitive-services-bing-visual-search-signup-requirements](../../../includes/cognitive-services-bing-visual-search-signup-requirements.md)]

## <a name="get-the-imageinsightstoken-from-the-bing-image-search-client-library"></a>Az ImageInsightsToken beszereznie a Bing képkereső ügyféltárból

Ez az `ImageInsightsToken` alkalmazás a [Bing Image Search ügyfélkönyvtáron](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/image-search-sdk-quickstart)keresztül kapott alkalmazást használja. Egy új C# konzolalkalmazásban hozzon létre egy `ImageSearchClient()`ügyfelet az API-nak a használatával történő hívásához. Ezután `SearchAsync()` használja a lekérdezéssel:

```csharp
var client = new ImageSearchClient(new Microsoft.Azure.CognitiveServices.Search.ImageSearch.ApiKeyServiceClientCredentials(subKey));
var imageResults = client.Images.SearchAsync(query: "canadian rockies").Result;
Console.WriteLine("Search images for query \"canadian rockies\"");
```

Az első keresési `imageResults.Value.First()`eredményt a használatával tárolhatja, `ImageInsightsToken`majd a képbetekintést.

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

A `ImageInsightsToken` rendszer kérésként elküldi a Bing Vizuális keresésnek.

## <a name="add-the-imageinsightstoken-to-a-visual-search-request"></a>Az ImageInsightsToken hozzáadása vizuális keresési kérelemhez

Adja `ImageInsightsToken` meg a Vizuális keresési `ImageInfo` kérelemhez `ImageInsightsToken` való t, ha a Bing Visual Search válaszaiból létrehoz egy objektumot.

```csharp
ImageInfo ImageInfo = new ImageInfo(imageInsightsToken: insightsTok);
```

## <a name="use-bing-visual-search-to-find-images-from-an-imageinsightstoken"></a>ImageInsightsToken képeinek keresése a Bing Visual Search segítségével

Az `VisualSearchRequest` objektum információkat tartalmaz `ImageInfo` a keresett képről. A `VisualSearchMethodAsync()` metódus az eredményeket kéri le. Nem kell megadnia a lemezkép bináris, mivel a lemezképet képviseli a jogkivonat.

```csharp
VisualSearchRequest VisualSearchRequest = new VisualSearchRequest(ImageInfo);

var visualSearchResults = client.Images.VisualSearchMethodAsync(knowledgeRequest: VisualSearchRequest).Result;

```

## <a name="iterate-through-the-visual-search-results"></a>A Vizuális keresés találatai között

A Visual Search eredményei `ImageTag` objektumok. Mindegy egyes címke `ImageAction` objektumok listáját tartalmazza. Mindegyik `ImageAction` tartalmaz `Data` egy mezőt, amely a művelet típusától függő értékek listája. Tudod iterate keresztül `ImageTag` az `visualSearchResults.Tags`objektumokat, például, és kap a `ImageAction` tag benne. Az alábbi minta a `PagesIncluding` következő műveletek részleteit nyomtatja ki:

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

### <a name="pagesincluding-actiontypes"></a>Műveletektípusait tartalmazó lapok

A tényleges kép URL-címeinek művelettípusokból való `ActionType` `ImageModuleAction`beolvasásához `Data` olyan szereposztásszükséges, amely beolvassa az as - értéket tartalmazó elemet. Minden egyes érték egy kép URL-je.  A következő vet `PagesIncluding` a `ImageModuleAction` művelet típus, és beolvassa az értékeket:

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

|ActionType  |URL-cím  | |
|---------|---------|---------|
|MoreSizes -> WebSearchUrl     |         |
|Vizuális keresés -> WebSearchUrl     |         |
|ImageById -> WebSearchUrl    |         |
|RelatedSearches -> WebSearchUrl:    |         |
|DocumentLevelSuggestions -> WebSearchUrl:     |         |
|TopicResults -> WebSearchUrl    | https:\//www.bing.com/cr?IG=3E32CC6CA5934FBBA14ABC3B2E4651F9&CID=1BA795A21EAF6A63175699B71FC36B7C&rd=1&h=BcQifmzdKFyyBusjLxxgO42kzq 1Geh7RucVVqvH-900&v=1&r=https%3a%2f%2fwww.bing.com%2fdiscover%2fcanadian%2brocky&p=DevEx,5823.1       |
|ImageResults -> WebSearchUrl    |  https:\//www.bing.com/cr?IG=3E32CC6CA5934FBBA14ABC3B2E4651F9&CID=1BA795A21EAF6A63175699B71FC36B7C&rd=1&h=PV9GzMFOI0AHZp2gKeWJ8DcveS DRERE3fP2jHDKMpJSU8&v=1&r=https%3a%2f%2fwww.bing.com%2fimages%2fsearch%3fq%3doutdoor&p=DevEx,5831.1       |

Amint az `TopicResults` a `ImageResults` fentiekben látható, a és a típusok a kapcsolódó képek lekérdezéseit tartalmazzák. Az URL-címek a Bing keresési eredményeire mutatnak.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Vizuális keresés létrehozása egyoldalas webalkalmazáslétrehozása](tutorial-bing-visual-search-single-page-app.md)
