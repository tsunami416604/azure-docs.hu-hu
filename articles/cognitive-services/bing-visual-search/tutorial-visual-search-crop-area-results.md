---
title: 'Oktatóanyag: Képkörülvágási terület és eredmények – Bing Visual Search'
description: Útmutató a Bing Visual Search SDK használatához, amellyel lekérheti a feltöltött kép körülvágási területéhez hasonló képek URL-címét.
services: cognitive-services
author: mikedodaro
manager: cgronlun
ms.service: cognitive-services
ms.component: bing-visual-search
ms.topic: tutorial
ms.date: 06/20/2018
ms.author: rosh
ms.openlocfilehash: 66e17c00da898e575bb858dbe16a35d1c44a2780
ms.sourcegitcommit: ad08b2db50d63c8f550575d2e7bb9a0852efb12f
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/26/2018
ms.locfileid: "47226910"
---
# <a name="tutorial-bing-visual-search-sdk-image-crop-area-and-results"></a>Oktatóanyag: Bing Visual Search SDK – képkörülvágási terület és eredmények
A Visual Search SDK lehetővé teszi egy kép adott területének kijelölését, és olyan képek online keresését, amelyek hasonlóak a nagyobb kép körülvágási területéhez.  Ez a példa olyan körülvágási területet határoz meg, amelyen egy több személyt ábrázoló képen látható személy van.  A kód elküldi a körülvágási területet és a nagyobb kép URL-címét, majd visszaadja a Bing Search URL-eket és a keresés során talált hasonló képek URL-jét tartalmazó eredményeket.

## <a name="prerequisites"></a>Előfeltételek

A kód Windows rendszeren történő futtatásához [Visual Studio 2017](https://www.visualstudio.com/downloads/) szükséges. (Az ingyenes Community Edition is elegendő.)

Egy Bing Search API-kat tartalmazó [Cognitive Services API-fiókkal](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) kell rendelkeznie. Az [ingyenes próbaverzió](https://azure.microsoft.com/try/cognitive-services/?api=bing-web-search-api) elegendő ehhez a rövid útmutatóhoz. Szüksége lesz az ingyenes próbaverzió aktiválásakor kapott hozzáférési kulcsra, vagy beszerezhet egy fizetős előfizetői azonosítót az Azure-irányítópultról.

## <a name="application-dependencies"></a>Alkalmazásfüggőségek
Ha a Bing Web Search SDK-val szeretne beállítani egy konzolalkalmazást, keresse meg a NuGet-csomagok kezelése lehetőséget a Visual Studio Megoldáskezelőjében. Adja hozzá a Microsoft.Azure.CognitiveServices.Search.VisualSearch csomagot.

A NuGet Web Search SDK-csomag telepítésekor függőségeket is telepít, például:

* Microsoft.Rest.ClientRuntime
* Microsoft.Rest.ClientRuntime.Azure
* Newtonsoft.Json

## <a name="image-and-crop-area"></a>Kép és körülvágási terület
Az alábbi képen a Microsoft felső vezetése látható.  A Visual Search SDK használatával feltöltjük a kép egy körülvágási területét, és más olyan képeket és weboldalakat keresünk, amelyek tartalmazzák a nagyobb kép kijelölt területén található entitást.  Ebben az esetben az entitás egy személy.

![A Microsoft felső vezetése](./media/MS_SrLeaders.jpg)

## <a name="specify-the-crop-area-as-imageinfo-in-visualsearchrequest"></a>A körülvágási terület megadása ImageInfo objektumként a VisualSearchRequest kérésben
Ez a példa az előző kép egy körülvágási területét használja, amely a teljes kép százalékaként határozza meg a bal felső és a jobb alsó koordinátákat.  A következő kód létrehoz egy `ImageInfo` objektumot a körülvágási területből, és betölti az `ImageInfo` objektumot egy `VisualSearchRequest` kérésbe.  Az `ImageInfo` objektum az online kép URL-jét is tartalmazza.

```
CropArea CropArea = new CropArea(top: (float)0.01, bottom: (float)0.30, left: (float)0.01, right: (float)0.20);
string imageURL = "https://docs.microsoft.com/azure/cognitive-services/bing-visual-search/media/ms_srleaders.jpg;
ImageInfo imageInfo = new ImageInfo(cropArea: CropArea, url: imageURL);

VisualSearchRequest visualSearchRequest = new VisualSearchRequest(imageInfo: imageInfo);
```
## <a name="search-for-images-similar-to-crop-area"></a>A körülvágási területhez hasonló képek keresése
A `VisualSearchRequest` a képpel és annak URL-jével kapcsolatos körülvágásiterület-információkat tartalmaz.  A `VisualSearchMethodAsync` metódus az eredményeket kéri le.
```
Console.WriteLine("\r\nSending visual search request with knowledgeRequest that contains URL and crop area");
var visualSearchResults = client.Images.VisualSearchMethodAsync(knowledgeRequest: visualSearchRequest).Result; 

```

## <a name="get-the-url-data-from-imagemoduleaction"></a>Az URL-adatok lekérése az ImageModuleAction műveletből
A Visual Search eredményei `ImageTag` objektumok.  Mindegy egyes címke `ImageAction` objektumok listáját tartalmazza.  Mindegy egyes `ImageAction` egy `Data` mezőt tartalmaz, amely a művelet típusától függő értékeket sorolja fel:

A különféle típusokat az alábbi kóddal kérheti le:
```
Console.WriteLine("\r\n" + "ActionType: " + i.ActionType + " -> WebSearchUrl: " + i.WebSearchUrl);

```
A teljes alkalmazás a következőket adja vissza:

* ActionType: PagesIncluding WebSearchURL:
* ActionType: MoreSizes WebSearchURL:
* ActionType: VisualSearch WebSearchURL:
* ActionType: ImageById WebSearchURL: 
* ActionType: RelatedSearches  WebSearchURL:
* ActionType: Entity -> WebSearchUrl: https://www.bing.com/cr?IG=E40D0E1A13404994ACB073504BC937A4&CID=03DCF882D7386A442137F49BD6596BEF&rd=1&h=BvvDoRtmZ35Xc_UZE4lZx6_eg7FHgcCkigU1D98NHQo&v=1&r=https%3a%2f%2fwww.bing.com%2fsearch%3fq%3dSatya%2bNadella&p=DevEx,5380.1
* ActionType: TopicResults -> WebSearchUrl: https://www.bing.com/cr?IG=E40D0E1A13404994ACB073504BC937A4&CID=03DCF882D7386A442137F49BD6596BEF&rd=1&h=3QGtxPb3W9LemuHRxAlW4CW7XN4sPkUYCUynxAqI9zQ&v=1&r=https%3a%2f%2fwww.bing.com%2fdiscover%2fnadella%2bsatya&p=DevEx,5382.1
* ActionType: ImageResults -> WebSearchUrl: https://www.bing.com/cr?IG=E40D0E1A13404994ACB073504BC937A4&CID=03DCF882D7386A442137F49BD6596BEF&rd=1&h=l-WNHO89Kkw69AmIGe2MhlUp6MxR6YsJszgOuM5sVLs&v=1&r=https%3a%2f%2fwww.bing.com%2fimages%2fsearch%3fq%3dSatya%2bNadella&p=DevEx,5384.1

Ahogy a fenti listában látható, az `Entity` `ActionType` egy Bing Search-lekérdezést tartalmaz, amely a felismerhető személyről, helyről és tárgyról ad vissza információkat.  A `TopicResults` és az `ImageResults` típus a kapcsolódó képekre vonatkozó lekérdezéseket tartalmazza. A listában lévő URL-ek a Bing keresési eredményeire mutatnak.


## <a name="pagesincluding-actiontype-urls-of-images-found-by-visual-search"></a>A Visual Search által talált képek PagesIncluding ActionType URL-jei

A képek tényleges URL-jeinek a lekéréséhez szükség van egy olyan átalakításra, amely egy `ActionType` típust egy értéklistával rendelkező `Data` elemet tartalmazó `ImageModuleAction` műveletként olvas be.  Minden egyes érték egy kép URL-je.  Az alábbi kód átalakítja a `PagesIncluding` művelettípust az `ImageModuleAction` műveletté, és beolvassa az értékeket.
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

A következő kód futtatja az előző példákat. Elküld egy képbinárist a POST kérés törzsében egy cropArea objektummal, és megjeleníti a Bing Search URL-eket minden ActionType típusra vonatkozóan. Ha az ActionType értéke PagesIncluding, a kód lekéri az ImageObject Data ImageObject elemeit.  A Data objektum egy értéklistát tartalmaz, amelyek a weboldalakon található képek URL-jei.  Másolja, majd illessze be a Visual Search által visszaadott URL-eket a böngészőbe az eredmények megjelenítéséhez. Másolja, majd illessze be a ContentUrl elemeket a böngészőbe a képek megjelenítéséhez.

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
                string imageURL = "https://docs.microsoft.com/azure/cognitive-services/bing-visual-search/media/ms_srleaders.jpg";
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
[Visual Search válasz](https://docs.microsoft.com/azure/cognitive-services/bing-visual-search/overview#the-response)