---
title: Keresse meg a korábbi kereséseket ImageInsightsToken – a Bing Visual Search használatával hasonló képek
titlesuffix: Azure Cognitive Services
description: A Bing Visual Search SDK használatával a képek ImageInsightsToken által megadott URL-címek lekérése.
services: cognitive-services
author: mikedodaro
manager: cgronlun
ms.service: cognitive-services
ms.component: bing-visual-search
ms.topic: article
ms.date: 06/21/2018
ms.author: rosh
ms.openlocfilehash: 609e164209bb2c920a36f293cee146cfece15fb5
ms.sourcegitcommit: 21466e845ceab74aff3ebfd541e020e0313e43d9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/21/2018
ms.locfileid: "53742609"
---
# <a name="find-similar-images-from-previous-searches-using-imageinsightstoken"></a>Keresse meg a korábbi kereséseket ImageInsightsToken használatával hasonló képek

A Visual Search SDK lehetővé teszi, hogy a korábbi kereséseket visszaadó online rendszerképek keresése egy `ImageInsightsToken`.  Ez az alkalmazás lekéri az `ImageInsightsToken` és használja a jogkivonatot egy későbbi keresés. Ezután elküldi a `ImageInsightsToken` a Bing és az értéket ad vissza, például a Bing Keresés URL-címek és a található online-hoz hasonló képek URL-címei eredmények.

A teljes forráskódot, ehhez a mintához található további hibakezelést és jegyzetek [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/Tutorials/Bing-Visual-Search/BingVisualSearchInisghtsTokens.cs).

## <a name="prerequisites"></a>Előfeltételek

* A [Visual Studio 2017](https://www.visualstudio.com/downloads/) bármely kiadása.
* Linux/MacOS rendszer esetében az alkalmazás a [Monóval](http://www.mono-project.com/) futtatható.
* A NuGet vizuális keresésre és Képkeresés csomagokat. 
    - A Visual Studio Megoldáskezelőjében kattintson a jobb gombbal a projektre, és válassza a `Manage NuGet Packages` lehetőséget a menüből. Telepítse a `Microsoft.Azure.CognitiveServices.Search.CustomSearch` csomagot, és a `Microsoft.Azure.CognitiveServices.Search.ImageSearch` csomagot. A NuGet-csomagokat is telepíti a következő:
        - Microsoft.Rest.ClientRuntime
        - Microsoft.Rest.ClientRuntime.Azure
        - Newtonsoft.Json


[!INCLUDE [cognitive-services-bing-visual-search-signup-requirements](../../../includes/cognitive-services-bing-visual-search-signup-requirements.md)]

## <a name="get-the-imageinsightstoken-from-the-bing-image-search-sdk"></a>A Bing Képkeresés SDK-t a ImageInsightsToken lekérése

Ez az alkalmazás használ egy `ImageInsightsToken` kombináljuk az [Bing kép Search SDK](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/image-search-sdk-quickstart). Az új C# -Konzolalkalmazás, hozzon létre egy ügyfél hívja az API használatával `ImageSearchAPI()`. Ezután `SearchAsync()` a lekérdezést.

```csharp
var client = new ImageSearchAPI(new Microsoft.Azure.CognitiveServices.Search.ImageSearch.ApiKeyServiceClientCredentials(subKey)); //
var imageResults = client.Images.SearchAsync(query: "canadian rockies").Result;
Console.WriteLine("Search images for query \"canadian rockies\"");
```

Az első keresési eredmény használatával Store `imageResults.Value.First()`, és biztonságosan tárolja a lemezképet elemzés `ImageInsightsToken`. 

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

Ez `ImageInsightsToken` küld a Bing vizuális keresés a kérelemben.

## <a name="add-the-imageinsightstoken-to-a-visual-search-request"></a>Adja hozzá a ImageInsightsToken vizuális keresésre vonatkozó kérésre

Adja meg a `ImageInsightsToken` létrehozásával vizuális keresési kérelmek egy `ImageInfo` objektum a `ImageInsightsToken` a Bing Visual Search érkező válaszokat tartalmazza. 

```csharp
ImageInfo ImageInfo = new ImageInfo(imageInsightsToken: insightsTok);
```

## <a name="use-bing-visual-search-to-find-images-from-an-imageinsightstoken"></a>A Bing Visual Search használatával egy ImageInsightsToken származó rendszerképek keresése

A `VisualSearchRequest` objektum tartalmazza a rendszerképet kapcsolatos információkat `ImageInfo` keresése. A `VisualSearchMethodAsync()` metódus az eredményeket kéri le. Nem kell egy kép bináris, adja meg a kép a token által jelölt.

```csharp
VisualSearchRequest VisualSearchRequest = new VisualSearchRequest(ImageInfo);

var visualSearchResults = client.Images.VisualSearchMethodAsync(knowledgeRequest: VisualSearchRequest).Result;

```

## <a name="iterate-through-the-visual-search-results"></a>A vizuális keresési eredmények iterálódnak

A Visual Search eredményei `ImageTag` objektumok.  Mindegy egyes címke `ImageAction` objektumok listáját tartalmazza.  Minden egyes `ImageAction` tartalmaz egy `Data` mező, amely művelet típusától függenek értékek listáját. Is iterálódnak a `ImageTag` az objektumok `visualSearchResults.Tags`, a példány és a get a `ImageAction` címke benne. Az alábbi mintát jelenít meg az adatait `PagesIncluding` műveleteket.

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

A tényleges kép URL-címek lekérése művelettípusok igényel, amely beolvas egy cast- `ActionType` , `ImageModuleAction`, tartalmazza, amely egy `Data` elem az értékek listáját.  Minden egyes érték egy kép URL-je.  Az alábbi kód átalakítja a `PagesIncluding` művelettípust az `ImageModuleAction` műveletté, és beolvassa az értékeket.

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
|MoreSizes WebSearchUrl ->     |         |         
|VisualSearch WebSearchUrl ->     |         |         
|ImageById WebSearchUrl ->    |         |         
|RelatedSearches WebSearchUrl ->:    |         |         
|DocumentLevelSuggestions WebSearchUrl ->:     |         |         
|TopicResults WebSearchUrl ->    | https://www.bing.com/cr?IG=3E32CC6CA5934FBBA14ABC3B2E4651F9&CID=1BA795A21EAF6A63175699B71FC36B7C&rd=1&h=BcQifmzdKFyyBusjLxxgO42kzq1Geh7RucVVqvH-900&v=1&r=https%3a%2f%2fwww.bing.com%2fdiscover%2fcanadian%2brocky&p=DevEx, 5823.1       |         
|ImageResults WebSearchUrl ->    |  https://www.bing.com/cr?IG=3E32CC6CA5934FBBA14ABC3B2E4651F9&CID=1BA795A21EAF6A63175699B71FC36B7C&rd=1&h=PV9GzMFOI0AHZp2gKeWJ8DcveSDRE3fP2jHDKMpJSU8&v=1&r=https%3a%2f%2fwww.bing.com%2fimages%2fsearch%3fq%3doutdoor&p=DevEx, 5831.1       |         

A fent látható módon a `TopicResults` és `ImageResults` típusok kapcsolódó képek lekérdezéseket tartalmaz. Az URL-címek hivatkozás a Bing keresési eredményeket.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Egyoldalas webes alkalmazás készítése](tutorial-bing-visual-search-single-page-app.md)