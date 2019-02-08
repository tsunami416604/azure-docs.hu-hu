---
title: 'Oktatóanyag: A Bing Visual Search SDK-val kép levágása'
description: A Bing Visual Search SDK segítségével mélyebb betekintés az adott ár lemezképen.
services: cognitive-services
author: mikedodaro
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-visual-search
ms.topic: article
ms.date: 06/20/2018
ms.author: rosh
ms.openlocfilehash: 6444a49a95ee53fb624501bc464f6378423e6e26
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/07/2019
ms.locfileid: "55863023"
---
# <a name="tutorial-crop-an-image-with-the-bing-visual-search-sdk-for-c"></a>Oktatóanyag: A Bing Visual Search készült SDK-val kép levágásaC#

A Bing Visual Search SDK hasonló online rendszerképek keresése előtt kép levágása teszi lehetővé. Ez az alkalmazás-lemezképről, több ember tartalmazó egyetlen személy körülvágja, és majd visszatér a keresési eredményeket tartalmazó található online-hoz hasonló képek.

A teljes forráskódot, az alkalmazás érhető el további hiba- és a jegyzetek [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/Tutorials/Bing-Visual-Search/BingVisualSearchCropImage.cs).

Ez az oktatóanyag bemutatja, hogyan lehet:

> [!div class="checklist"]
> * A Bing Visual Search SDK használatával egy kérelem küldése
> * Lemezkép keresése a Bing vizuális keresés egy adott területre levágása
> * Fogadása és válasz kezelésére is alkalmas
> * Műveleti elemek URL-címei a resposne megkeresése

## <a name="prerequisites"></a>Előfeltételek

* A [Visual Studio 2017](https://www.visualstudio.com/downloads/) bármely kiadása.
* Linux/MacOS rendszer esetében az alkalmazás a [Monóval](http://www.mono-project.com/) futtatható.
- Telepített [NuGet Custom Search](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Search.CustomSearch/1.2.0) csomag. 
    - A Visual Studio Megoldáskezelőjében kattintson a jobb gombbal a projektre, és válassza a `Manage NuGet Packages` lehetőséget a menüből. Telepítse az `Microsoft.Azure.CognitiveServices.Search.CustomSearch` csomagot. A NuGet Custom Search csomag telepítésekor a következő szerelvények is települnek:
        - Microsoft.Rest.ClientRuntime
        - Microsoft.Rest.ClientRuntime.Azure
        - Newtonsoft.Json

[!INCLUDE [cognitive-services-bing-image-search-signup-requirements](../../../includes/cognitive-services-bing-visual-search-signup-requirements.md)]

## <a name="specify-the-image-crop-area"></a>Adja meg a lemezkép körülvágása terület

Ez az alkalmazás egy adott területre a lemezkép a Microsoft termékigazgatókkal csapat levágja. Ez a terület körülvágása százalékos arányában a teljes kép megjelenjen felső – bal és jobb alsó koordináták segítségével van meghatározva.  

![A Microsoft felső vezetése](./media/MS_SrLeaders.jpg)

Ez a rendszerkép van csonkolva hozzon létre egy `ImageInfo` objektumot az körülvágása területről, és betöltése a `ImageInfo` az objektum egy `VisualSearchRequest`. A `ImageInfo` objektum is magában foglalja a kép URL-CÍMÉT.

```csharp
CropArea CropArea = new CropArea(top: (float)0.01, bottom: (float)0.30, left: (float)0.01, right: (float)0.20);
string imageURL = "https://docs.microsoft.com/azure/cognitive-services/bing-visual-search/media/ms_srleaders.jpg;
ImageInfo imageInfo = new ImageInfo(cropArea: CropArea, url: imageURL);

VisualSearchRequest visualSearchRequest = new VisualSearchRequest(imageInfo: imageInfo);
```

## <a name="search-for-images-similar-to-the-crop-area"></a>A vágási területen hasonló Képkeresés

a változó `VisualSearchRequest` tartalmaz információkat, a lemezkép körülvágása terület, és az URL-címét. A `VisualSearchMethodAsync()` metódus az eredményeket kéri le.

```csharp
Console.WriteLine("\r\nSending visual search request with knowledgeRequest that contains URL and crop area");
var visualSearchResults = client.Images.VisualSearchMethodAsync(knowledgeRequest: visualSearchRequest).Result; 

```

## <a name="get-the-url-data-from-imagemoduleaction"></a>Az URL-adatok lekérése az ImageModuleAction műveletből

A Bing vizuális keresési eredmények `ImageTag` objektumokat.  Mindegy egyes címke `ImageAction` objektumok listáját tartalmazza.  Minden egyes `ImageAction` tartalmaz egy `Data` mező, amely művelet típusától függenek értékek listáját.

Kinyomtathatja a különféle fenyegetési típusokat az alábbi kódra:

```csharp
Console.WriteLine("\r\n" + "ActionType: " + i.ActionType + " -> WebSearchUrl: " + i.WebSearchUrl);
```

A teljes alkalmazás a következőket adja vissza:


|ActionType  |URL-cím  | |
|---------|---------|---------|
|PagesIncluding WebSearchURL     |         |         
|MoreSizes WebSearchURL     |         |         
|VisualSearch WebSearchURL    |         |         
|ImageById WebSearchURL     |         |         
|RelatedSearches WebSearchURL     |         |         
|Entity -> WebSearchUrl     | https://www.bing.com/cr?IG=E40D0E1A13404994ACB073504BC937A4&CID=03DCF882D7386A442137F49BD6596BEF&rd=1&h=BvvDoRtmZ35Xc_UZE4lZx6_eg7FHgcCkigU1D98NHQo&v=1&r=https%3a%2f%2fwww.bing.com%2fsearch%3fq%3dSatya%2bNadella&p=DevEx,5380.1        |         
|TopicResults -> WebSearchUrl    |  https://www.bing.com/cr?IG=E40D0E1A13404994ACB073504BC937A4&CID=03DCF882D7386A442137F49BD6596BEF&rd=1&h=3QGtxPb3W9LemuHRxAlW4CW7XN4sPkUYCUynxAqI9zQ&v=1&r=https%3a%2f%2fwww.bing.com%2fdiscover%2fnadella%2bsatya&p=DevEx,5382.1        |         
|ImageResults -> WebSearchUrl    |  https://www.bing.com/cr?IG=E40D0E1A13404994ACB073504BC937A4&CID=03DCF882D7386A442137F49BD6596BEF&rd=1&h=l-WNHO89Kkw69AmIGe2MhlUp6MxR6YsJszgOuM5sVLs&v=1&r=https%3a%2f%2fwww.bing.com%2fimages%2fsearch%3fq%3dSatya%2bNadella&p=DevEx,5384.1        |         

A fent látható módon a `Entity` ActionType egy Bing keresési lekérdezést tartalmaz, amely egy könnyen felismerhető névre személy, hely vagy egy dolog kapcsolatos információkat ad vissza.  A `TopicResults` és az `ImageResults` típus a kapcsolódó képekre vonatkozó lekérdezéseket tartalmazza. A listában lévő URL-ek a Bing keresési eredményeire mutatnak.


## <a name="get-urls-for-pagesincluding-actiontype-images"></a>URL-címek lekérése PagesIncluding ActionType képek

A képek tényleges URL-jeinek a lekéréséhez szükség van egy olyan átalakításra, amely egy `ActionType` típust egy értéklistával rendelkező `Data` elemet tartalmazó `ImageModuleAction` műveletként olvas be.  Minden egyes érték egy kép URL-je.  Az alábbi kód átalakítja a `PagesIncluding` művelettípust az `ImageModuleAction` műveletté, és beolvassa az értékeket.

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
> [Egyoldalas webes alkalmazás készítése](tutorial-bing-visual-search-single-page-app.md)

[Visual Search válasz](https://docs.microsoft.com/azure/cognitive-services/bing-visual-search/overview)
