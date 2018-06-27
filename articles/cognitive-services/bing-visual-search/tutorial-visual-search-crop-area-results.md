---
title: Bing Visual keresési SDK vágási területen eredmények oktatóanyag |} Microsoft Docs
description: Hogyan használható a Bing Visual keresési SDK segítségével kép URL-címei hasonló feltöltött képnek levágása.
services: cognitive-services
author: mikedodaro
manager: ronakshah
ms.service: cognitive-services
ms.component: bing-visual-search
ms.topic: article
ms.date: 06/20/2018
ms.author: rosh
ms.openlocfilehash: 9bc3c180f108025f442343d8c5356982a83826a6
ms.sourcegitcommit: 0408c7d1b6dd7ffd376a2241936167cc95cfe10f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/26/2018
ms.locfileid: "36958403"
---
# <a name="tutorial-bing-visual-search-sdk-image-crop-area-and-results"></a>Oktatóanyag: Bing Visual keresési SDK kép vágási területen és eredmények
A vizuális keresési SDK tartalmaz egy lehetőség, hogy a lemezkép kiválasztása és lemezképek online hasonló a nagyobb méretű kép vágási területen található.  Ebben a példában egy személy egy olyanra, amely a néhány személyt tartalmazza, a megjelenítő vágási területen adja meg.  A kód elküldi a vágási területen és a nagyobb méretű kép URL-CÍMÉT, és visszaadja az eredmények, amelyek tartalmazzák a Bing keresési URL-címek és online hasonló kép URL-címei.

## <a name="prerequisites"></a>Előfeltételek

Szüksége lesz [Visual Studio 2017](https://www.visualstudio.com/downloads/) Ez a kód futtatása Windows eléréséhez. (Az ingyenes közösségi Edition fog működni.)

Rendelkeznie kell egy [kognitív szolgáltatások API-fiók](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) Bing keresési API-khoz. A [ingyenes próbaverzió](https://azure.microsoft.com/try/cognitive-services/?api=bing-web-search-api) elegendő-e a gyors üzembe helyezés. Az elérési kulcsot, ha aktiválja az ingyenes próbaverzió, vagy egy fizetős kulcsot használhatja az Azure irányítópultról van szüksége.

## <a name="application-dependencies"></a>Alkalmazásfüggőségek
Egy konzolalkalmazást, a Bing webes keresés SDK használatával beállításához tallózással keresse meg a NuGet-csomagok kezelése lehetőséget a Visual Studio megoldáskezelőjében. Adja hozzá a Microsoft.Azure.CognitiveServices.Search.VisualSearch csomagot.

A Web-keresési SDK NuGet-csomag telepítése is telepíti a függőségek, beleértve:

* Microsoft.Rest.ClientRuntime
* Microsoft.Rest.ClientRuntime.Azure
* Newtonsoft.Json

## <a name="image-and-crop-area"></a>Kép és a vágás terület
A következő kép bemutatja az Microsoft vezető vezetőségi tagja.  A vizuális keresési SDK használatával, azt egy vágási területen, a lemezkép feltöltése és más lemezképek és a kiválasztott terület, a nagyobb méretű kép az entitást tartalmazó weblapokra.  Az entitás ebben az esetben egy személy.

![Microsoft vezető vezetőségi tagja](./media/MS_SrLeaders.jpg)

## <a name="specify-the-crop-area-as-imageinfo-in-visualsearchrequest"></a>A vágási területen adja meg a VisualSearchRequest ImageInfo
A példa egy vágási területen határoz meg felső bal és jobb koordináták csökkenthető a teljes kép százalékos előző kép.  Az alábbi kód létrehoz egy `ImageInfo` objektum a körülvágása területről, és betölti a `ImageInfo` az objektum egy `VisualSearchRequest`.  A `ImageInfo` objektum az az URL-címe, online is tartalmaz.

```
CropArea CropArea = new CropArea(top: (float)0.01, bottom: (float)0.30, left: (float)0.01, right: (float)0.20);
string imageURL = "https://docs.microsoft.com/en-us/azure/cognitive-services/bing-visual-search/media/ms_srleaders.jpg;
ImageInfo imageInfo = new ImageInfo(cropArea: CropArea, url: imageURL);

VisualSearchRequest visualSearchRequest = new VisualSearchRequest(imageInfo: imageInfo);
```
## <a name="search-for-images-similar-to-crop-area"></a>Keresse meg a képeket hasonló levágni terület
A `VisualSearchRequest` körülvágása terület információkat tartalmaz a lemezkép és az URL-CÍMÉT.  A `VisualSearchMethodAsync` metódus lekéri az eredményeket.
```
Console.WriteLine("\r\nSending visual search request with knowledgeRequest that contains URL and crop area");
var visualSearchResults = client.Images.VisualSearchMethodAsync(knowledgeRequest: visualSearchRequest).Result; 

```

## <a name="get-the-url-data-from-imagemoduleaction"></a>Az URL-cím adatokat lekérni ImageModuleAction
Visual a keresési találatok között `ImageTag` objektumok.  Minden tag listáját tartalmazza `ImageAction` objektumok.  Minden egyes `ImageAction` tartalmaz egy `Data` mező, amelyben függő művelet típusú értékek listája:

A különféle kaphat az alábbi kódra:
```
Console.WriteLine("\r\n" + "ActionType: " + i.ActionType + " -> WebSearchUrl: " + i.WebSearchUrl);

```
A teljes alkalmazás adja vissza:

* Művelettípushoz: PagesIncluding WebSearchURL:
* Művelettípushoz: WebSearchURL MoreSizes:
* Művelettípushoz: VisualSearch WebSearchURL:
* Művelettípushoz: ImageById WebSearchURL: 
* Művelettípushoz: RelatedSearches WebSearchURL:
* Művelettípushoz: Entitás -> WebSearchUrl: https://www.bing.com/cr?IG=E40D0E1A13404994ACB073504BC937A4&CID=03DCF882D7386A442137F49BD6596BEF&rd=1&h=BvvDoRtmZ35Xc_UZE4lZx6_eg7FHgcCkigU1D98NHQo&v=1&r=https%3a%2f%2fwww.bing.com%2fsearch%3fq%3dSatya%2bNadella&p=DevEx, 5380.1
* Művelettípushoz: TopicResults -> WebSearchUrl: https://www.bing.com/cr?IG=E40D0E1A13404994ACB073504BC937A4&CID=03DCF882D7386A442137F49BD6596BEF&rd=1&h=3QGtxPb3W9LemuHRxAlW4CW7XN4sPkUYCUynxAqI9zQ&v=1&r=https%3a%2f%2fwww.bing.com%2fdiscover%2fnadella%2bsatya&p=DevEx, 5382.1
* Művelettípushoz: ImageResults -> WebSearchUrl: https://www.bing.com/cr?IG=E40D0E1A13404994ACB073504BC937A4&CID=03DCF882D7386A442137F49BD6596BEF&rd=1&h=l-WNHO89Kkw69AmIGe2MhlUp6MxR6YsJszgOuM5sVLs&v=1&r=https%3a%2f%2fwww.bing.com%2fimages%2fsearch%3fq%3dSatya%2bNadella&p=DevEx, 5384.1

Ahogy az előző listában a `Entity` `ActionType` tartalmaz, amely egy felismerhető személy, hely vagy dolog információt ad vissza a Bing keresési lekérdezés.  A `TopicResults` és `ImageResults` típusok kapcsolódó lemezképek lekérdezéseket tartalmaznak. Bing keresési eredmények lista kapcsolat URL-címeket.


## <a name="pagesincluding-actiontype-urls-of-images-found-by-visual-search"></a>Visual található képek PagesIncluding művelettípus URL-címek

Olvasó típuskonverzió történt az aktuális kép URL-címeket igényel egy `ActionType` , `ImageModuleAction`, amely tartalmazza egy `Data` elem az értékek listáját.  Minden egyes érték kép URL-CÍMÉT.  A következő kiterjesztések a `PagesIncluding` művelet típusát `ImageModuleAction` és olvassa be az értékeket.
```
    if (i.ActionType == "PagesIncluding")
    {
        foreach(ImageObject o in (i as ImageModuleAction).Data.Value)
        {
            Console.WriteLine("ContentURL: " + o.ContentUrl);
        }
    }
```

## <a name="complete-code"></a>Teljes kód

A következő kódot az előző példákban futtatja. Küld lemezkép bináris a post kérelem törzsében, egy cropArea objektumot, és a Bing kinyomtatása együtt minden A(z) URL-címek keresése. Ha a művelettípus PagesIncluding, a kód lekér a ImageObject elemek ImageObject adataiban.  Az adatok értékek, amelyek weblapok kép URL-címek listáját tartalmazza.  Másolással illessze be az eredményül kapott Visual keresési URL-címek a böngésző eredmények megjelenítése. Másolja és illessze be a ContentUrl kép megjelenítéséhez böngészőben.

```
using System;
using System.IO;
using System.Linq;
using Microsoft.Azure.CognitiveServices.Search.VisualSearch;
using Microsoft.Azure.CognitiveServices.Search.VisualSearch.Models;
using Microsoft.Azure.CognitiveServices.Search.ImageSearch;

namespace VisualSearchFeatures
{
    class Program
    {
        static void Main(string[] args)
        {
            String subscriptionKey = "YOUR-ACCESS-KEY";

            VisualSearchImageBinaryWithCropArea(subscriptionKey);

            Console.WriteLine("Any key to quit...");
            Console.ReadKey();

        }

        public static void VisualSearchImageBinaryWithCropArea(string subscriptionKey)
        {
            var client = new VisualSearchAPI(new Microsoft.Azure.CognitiveServices.Search.VisualSearch.ApiKeyServiceClientCredentials(subscriptionKey));

            try
            {
                CropArea CropArea = new CropArea(top: (float)0.01, bottom: (float)0.30, left: (float)0.01, right: (float)0.20);
                
                // The ImageInfo struct specifies the crop area in the image and the URL of the larger image. 
                string imageURL = "https://docs.microsoft.com/en-us/azure/cognitive-services/bing-visual-search/media/ms_srleaders.jpg";
                ImageInfo imageInfo = new ImageInfo(cropArea: CropArea, url: imageURL);
                
                VisualSearchRequest visualSearchRequest = new VisualSearchRequest(imageInfo: imageInfo);

                var visualSearchResults = client.Images.VisualSearchMethodAsync(knowledgeRequest: visualSearchRequest).Result; 

                Console.WriteLine("\r\nVisual search request with image from URL and crop area combined in knowledgeRequest");

                if (visualSearchResults == null)
                {
                    Console.WriteLine("No visual search result data.");
                }
                else
                {
                    // List of tags
                    if (visualSearchResults.Tags.Count > 0)
                    {

                        foreach(ImageTag t in visualSearchResults.Tags)
                        {
                            foreach (ImageAction i in t.Actions)
                            { 
                                Console.WriteLine("\r\n" + "ActionType: " + i.ActionType + " -> WebSearchUrl: " + i.WebSearchUrl);

                                if (i.ActionType == "PagesIncluding")
                                {
                                    foreach(ImageObject o in (i as ImageModuleAction).Data.Value)
                                    {
                                        Console.WriteLine("ContentURL: " + o.ContentUrl);
                                    }
                                }
                            }

                        }

                    }
                    else
                    {
                        Console.WriteLine("Couldn't find image tags!");
                    }
                }
            }

            catch (Exception ex)
            {
                Console.WriteLine("Encountered exception. " + ex.Message);
            }
        }
    }
}

```
## <a name="next-steps"></a>További lépések
[Visual keresési válasz](https://docs.microsoft.com/en-us/azure/cognitive-services/bing-visual-search/overview#the-response)