---
title: Hasonló rendszerképek keresése az előző keresésekben képelemzési jogkivonatok és a Bing Visual Search API használatával
titleSuffix: Azure Cognitive Services
description: A korábbi keresések képeinek URL-címeinek beszerzéséhez használja a Bing Visual Search ügyféloldali függvénytárat.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-visual-search
ms.topic: tutorial
ms.date: 03/31/2020
ms.author: aahi
ms.custom: devx-track-csharp
ms.openlocfilehash: c03800696897dc6cbb4cd793879e734366829b7d
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/26/2020
ms.locfileid: "88925113"
---
# <a name="tutorial-find-similar-images-from-previous-searches-using-an-image-insights-token"></a>Oktatóanyag: hasonló rendszerképek keresése az előző keresésekben képelemzési jogkivonat használatával

Az Visual Search ügyféloldali kódtár lehetővé teszi, hogy az előző keresések alapján online megkeresse a képeket `ImageInsightsToken` . Ez az alkalmazás lekérdezi `ImageInsightsToken` és felhasználja a tokent egy későbbi keresés során. Ezután elküldi a a `ImageInsightsToken` bingnek, és olyan eredményeket ad vissza, amelyek tartalmazzák az online-ban található hasonló lemezképek Bing Search URL-címét és URL-címét.

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

## <a name="get-the-imageinsightstoken-from-the-bing-image-search-client-library"></a>A ImageInsightsToken beolvasása az Bing Image Search ügyféloldali könyvtárából

Ez az alkalmazás `ImageInsightsToken` a [Bing Image Search ügyfél-függvénytáron](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/image-search-sdk-quickstart)keresztül kapott beszerzést használja. Egy új C# Console-alkalmazásban hozzon létre egy ügyfelet az API-nak a használatával történő meghívásához `ImageSearchClient()` . Ezután használja a `SearchAsync()` lekérdezést:

```csharp
var client = new ImageSearchClient(new Microsoft.Azure.CognitiveServices.Search.ImageSearch.ApiKeyServiceClientCredentials(subKey));
var imageResults = client.Images.SearchAsync(query: "canadian rockies").Result;
Console.WriteLine("Search images for query \"canadian rockies\"");
```

Tárolja az első keresési eredményt a használatával `imageResults.Value.First()` , majd tárolja a rendszerkép betekintését `ImageInsightsToken` .

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

Ezt a `ImageInsightsToken` rendszer a kérelemben Bing Visual Search küldi el.

## <a name="add-the-imageinsightstoken-to-a-visual-search-request"></a>A ImageInsightsToken hozzáadása Visual Search kérelemhez

Adjon meg `ImageInsightsToken` egy Visual Search kérést úgy, hogy egy objektumot hoz létre `ImageInfo` a `ImageInsightsToken` Bing Visual Searchokból származó válaszokban szereplő objektumból.

```csharp
ImageInfo ImageInfo = new ImageInfo(imageInsightsToken: insightsTok);
```

## <a name="use-bing-visual-search-to-find-images-from-an-imageinsightstoken"></a>Képek keresése a ImageInsightsToken Bing Visual Search használatával

Az `VisualSearchRequest` objektum a keresendő rendszerképpel kapcsolatos információkat tartalmaz `ImageInfo` . A `VisualSearchMethodAsync()` metódus az eredményeket kéri le. Nem kell megadnia bináris képfájlt, mert a rendszerképet a jogkivonat jelképezi.

```csharp
VisualSearchRequest VisualSearchRequest = new VisualSearchRequest(ImageInfo);

var visualSearchResults = client.Images.VisualSearchMethodAsync(knowledgeRequest: VisualSearchRequest).Result;

```

## <a name="iterate-through-the-visual-search-results"></a>Iteráció a Visual Search eredményei között

A Visual Search eredményei `ImageTag` objektumok. Mindegy egyes címke `ImageAction` objektumok listáját tartalmazza. Mindegyik `ImageAction` tartalmaz egy `Data` mezőt, amely a művelet típusától függő értékek listáját tartalmazza. Megismételheti a objektumokat a `ImageTag` alkalmazásban `visualSearchResults.Tags` , például a `ImageAction` címkén belül. Az alábbi minta a műveletek részleteit nyomtatja ki `PagesIncluding` :

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

A tényleges képurl-címek műveleti típusokból való lekéréséhez olyan leadott műveletre van szükség `ActionType` `ImageModuleAction` , amely egy olyan elemet tartalmaz, amely tartalmazza az `Data` értékek listáját. Minden egyes érték egy kép URL-je.  A következő `PagesIncluding` művelet a műveletet írja be, `ImageModuleAction` és beolvassa az értékeket:

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

|ActionType  |URL-cím  |
|---------|---------|
|MoreSizes – > WebSearchUrl     |         |
|VisualSearch – > WebSearchUrl     |         |
|ImageById – > WebSearchUrl    |         |
|RelatedSearches – > WebSearchUrl:    |         |
|DocumentLevelSuggestions – > WebSearchUrl:     |         |
|TopicResults – > WebSearchUrl    | https: \/ /www.bing.com/cr?IG=3E32CC6CA5934FBBA14ABC3B2E4651F9&CID = 1BA795A21EAF6A63175699B71FC36B7C&Rd = 1&h = BcQifmzdKFyyBusjLxxgO42kzq1Geh7RucVVqvH-900&v = 1&r = HTTPS %3 a %2 f %2 f www. Bing. com% 2fdiscover% 2fcanadian% 2brocky&p = DevEx, 5823.1       |
|ImageResults – > WebSearchUrl    |  https: \/ /www.bing.com/cr?IG=3E32CC6CA5934FBBA14ABC3B2E4651F9&CID = 1BA795A21EAF6A63175699B71FC36B7C&Rd = 1&h = PV9GzMFOI0AHZp2gKeWJ8DcveSDRE3fP2jHDKMpJSU8&v = 1&r = HTTPS %3 a %2 f %2 f www. Bing. com% 2fimages% 2fsearch% 3fq% 3doutdoor&p = DevEx, 5831.1       |

Ahogy az a fentiekben is látható, a `TopicResults` és a `ImageResults` típusok a kapcsolódó képek lekérdezéseit tartalmazzák. A Bing keresési eredményeire mutató URL-címek.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Visual Search egyoldalas Webalkalmazás létrehozása](tutorial-bing-visual-search-single-page-app.md)
