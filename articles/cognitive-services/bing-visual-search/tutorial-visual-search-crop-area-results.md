---
title: 'Oktatóanyag: Kép körülvágása a Bing Visual Search SDK-val'
description: A Bing Visual Search SDK segítségével elemzéseket kaphat egy kép adott területéből.
services: cognitive-services
titleSuffix: Azure Cognitive Services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-visual-search
ms.topic: tutorial
ms.date: 03/31/2019
ms.author: aahi
ms.openlocfilehash: 4778a4089c7374c1ac6a9312064dcfb1e0325b63
ms.sourcegitcommit: efefce53f1b75e5d90e27d3fd3719e146983a780
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/01/2020
ms.locfileid: "80478496"
---
# <a name="tutorial-crop-an-image-with-the-bing-visual-search-sdk-for-c"></a>Oktatóanyag: Kép körülvágása a Bing Visual Search SDK for C segítségével #

A Bing Visual Search SDK lehetővé teszi a képek körülvágását, mielőtt hasonló online képeket találna. Ez az alkalmazás növények egyetlen személy egy képet tartalmazó több ember, majd visszaadja a keresési eredményeket tartalmazó hasonló képeket talált online.

Az alkalmazás teljes forráskódja további hibakezeléssel és jegyzetekkel érhető el a [GitHubon.](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/Tutorials/Bing-Visual-Search/BingVisualSearchCropImage.cs)

Ez az oktatóanyag bemutatja, hogyan:

> [!div class="checklist"]
> * Kérelem küldése a Bing Visual Search SDK használatával
> * Képterület körülvágása a Bing Visual Search segítségével
> * A válasz fogadása és kezelése
> * A műveletelemek URL-címeinek megkeresése a válaszban

## <a name="prerequisites"></a>Előfeltételek

* A [Visual Studio 2019](https://www.visualstudio.com/downloads/)bármely kiadása.
* Linux/MacOS rendszer esetében az alkalmazás a [Monóval](https://www.mono-project.com/) futtatható.
* Telepített [NuGet Custom Search](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Search.CustomSearch/1.2.0) csomag.
    - A Visual Studio Megoldáskezelőjében kattintson a jobb gombbal a projektre, és válassza a menü **NuGet csomagok kezelése parancsát.** Telepítse az `Microsoft.Azure.CognitiveServices.Search.CustomSearch` csomagot. A NuGet Custom Search csomag telepítésekor a következő szerelvények is települnek:
        - Microsoft.Rest.ClientRuntime
        - Microsoft.Rest.ClientRuntime.Azure
        - Newtonsoft.Json

[!INCLUDE [cognitive-services-bing-image-search-signup-requirements](../../../includes/cognitive-services-bing-visual-search-signup-requirements.md)]

## <a name="specify-the-image-crop-area"></a>A kép körülvágási területének megadása

Ez az alkalmazás növények területe ez a kép a Microsoft vezető vezetői csapat. Ezt a vágási területet a bal felső és a jobb alsó koordináták kal definiálják, amelyek a teljes kép százalékában jelennek meg:  

![A Microsoft felső vezetése](./media/MS_SrLeaders.jpg)

Ezt a képet úgy `ImageInfo` vágja le a program, hogy `ImageInfo` létrehoz `VisualSearchRequest`egy objektumot a vágási területről, és betölti az objektumot egy betöltésére. Az `ImageInfo` objektum tartalmazza a kép URL-címét is:

```csharp
CropArea CropArea = new CropArea(top: (float)0.01, bottom: (float)0.30, left: (float)0.01, right: (float)0.20);
string imageURL = "https://docs.microsoft.com/azure/cognitive-services/bing-visual-search/media/ms_srleaders.jpg";
ImageInfo imageInfo = new ImageInfo(cropArea: CropArea, url: imageURL);

VisualSearchRequest visualSearchRequest = new VisualSearchRequest(imageInfo: imageInfo);
```

## <a name="search-for-images-similar-to-the-crop-area"></a>A vágási területhez hasonló képek keresése

A `VisualSearchRequest` változó a kép vágási területéről és URL-címéről tartalmaz információkat. A `VisualSearchMethodAsync()` módszer megkapja az eredményeket:

```csharp
Console.WriteLine("\r\nSending visual search request with knowledgeRequest that contains URL and crop area");
var visualSearchResults = client.Images.VisualSearchMethodAsync(knowledgeRequest: visualSearchRequest).Result;

```

## <a name="get-the-url-data-from-imagemoduleaction"></a>Az URL-adatok beszerezése innen:`ImageModuleAction`

A Bing Visual `ImageTag` Search találatai objektumok. Mindegy egyes címke `ImageAction` objektumok listáját tartalmazza. Mindegyik `ImageAction` tartalmaz `Data` egy mezőt, amely a művelet típusától függő értékek listája.

A különböző típusokat a következő kóddal nyomtathatja ki:

```csharp
Console.WriteLine("\r\n" + "ActionType: " + i.ActionType + " -> WebSearchUrl: " + i.WebSearchUrl);
```

A teljes alkalmazás a következőket adja vissza:

|ActionType  |URL-cím  | |
|---------|---------|---------|
|OldalakBeleértve WebSearchURL     |         |
|MoreSizes WebSearchURL     |         |  
|VisualSearch WebSearchURL    |         |
|ImageById WebSearchURL     |         |  
|RelatedSearches WebSearchURL     |         |
|Entitás -> WebSearchUrl     | https\://www.bing.com/cr?IG=E40D0E1A13404994ACB073504BC937A4&CID=03DCF882D7386A442137F49BD6596BEF&rd=1&h=BvvDoRtmZ35Xc_UZE4lZx6_eg7FHgcCkigU1D98NHQo&v=1&r=https%3a%2f%2fwww.bing.com%2fsearch%3fq%3dSatya%2bNadella&=DevEx,5380.1        |
|TopicResults -> WebSearchUrl    |  https\://www.bing.com/cr?IG=E40D0E1A13404994ACB073504BC937A4&CID=03DCF882D7386A442137F49BD6596BEF&rd=1&h=3QGtxPb3W9LemuHRxAlW4CW7XN4s PkUYCUynxAqI9zQ&v=1&r=https%3a%2f%2fwww.bing.com%2fdiscover%2fnadella%2bsatya&p=DevEx,5382.1        |
|ImageResults -> WebSearchUrl    |  https\://www.bing.com/cr?IG=E40D0E1A13404994ACB073504BC937A4&CID=03DCF82D7386A442137F49BD6596BEF&rd=1&h=l-WNHO89Kkw69AmIGe2MhlUp6MxR6Ys JszgOuM5sVLs&v=1&r=https%3a%2f%2fwww.bing.com%2fimages%2fsearch%3fq%3dSatya%2bNadella&p=DevEx,5384.1        |

Amint fent `Entity` látható, az ActionType tartalmaz egy Bing keresési lekérdezést, amely információt ad vissza egy felismerhető személy, hely vagy dolog. A `TopicResults` és az `ImageResults` típus a kapcsolódó képekre vonatkozó lekérdezéseket tartalmazza. A listában lévő URL-ek a Bing keresési eredményeire mutatnak.

## <a name="get-urls-for-pagesincluding-actiontype-images"></a>Képek URL-címének `PagesIncluding` `ActionType` beszereznie

A képek tényleges URL-jeinek a lekéréséhez szükség van egy olyan átalakításra, amely egy `ActionType` típust egy értéklistával rendelkező `Data` elemet tartalmazó `ImageModuleAction` műveletként olvas be. Minden egyes érték egy kép URL-je. A következő vet `PagesIncluding` a `ImageModuleAction` művelet típus, és beolvassa az értékeket:

```csharp
    if (i.ActionType == "PagesIncluding")
    {
        foreach(ImageObject o in (i as ImageModuleAction).Data.Value)
        {
            Console.WriteLine("ContentURL: " + o.ContentUrl);
        }
    }
```

## <a name="next-steps"></a>További lépések
> [!div class="nextstepaction"]
> [Vizuális keresés létrehozása egyoldalas webalkalmazáslétrehozása](tutorial-bing-visual-search-single-page-app.md)

## <a name="see-also"></a>Lásd még
> [Mi az a Bing Visual Search API?](https://docs.microsoft.com/azure/cognitive-services/bing-visual-search/overview)
