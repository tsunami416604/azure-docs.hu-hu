---
title: A Bing Visual Search SDK vágási területen az oktatóanyag eredménye |} A Microsoft Docs
description: Hogyan lehet a képek URL-címek hasonló levágni a feltöltött kép területeinek lekérése a Bing Visual Search SDK használatával.
services: cognitive-services
author: mikedodaro
manager: ronakshah
ms.service: cognitive-services
ms.component: bing-visual-search
ms.topic: article
ms.date: 06/20/2018
ms.author: rosh
ms.openlocfilehash: dd51ed7c710cc51a9fe0e63e55aa0d2c4ea24bee
ms.sourcegitcommit: e2ea404126bdd990570b4417794d63367a417856
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/14/2018
ms.locfileid: "45574489"
---
# <a name="tutorial-bing-visual-search-sdk-image-crop-area-and-results"></a>Oktatóanyag: A Bing Visual Search SDK lemezkép körülvágása területről, és az eredmények
A Visual Search SDK tartalmaz egy beállítást válassza ki a kép egy adott területre, és-rendszerképek keresése online, amelyek hasonlóak a nagyobb méretű kép körülvágása területéhez.  Ebben a példában egy felhasználót több munkatársát is tartalmazó lemezképek a megjelenítő vágási területen megadja.  A kód elküldi a vágási területen és a nagyobb méretű kép URL-CÍMÉT, és a Bing Keresés URL-címek és található online-hoz hasonló képek URL-címeket tartalmazó eredményeket ad vissza.

## <a name="prerequisites"></a>Előfeltételek

Szüksége lesz [Visual Studio 2017](https://www.visualstudio.com/downloads/) lekérni ezt a kódot, a Windows rendszerű. (Az ingyenes Community Edition is elegendő.)

Rendelkeznie kell egy [Cognitive Services API-fiók](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) Bing Search APIs használatát. A [az ingyenes próbaidőszak](https://azure.microsoft.com/try/cognitive-services/?api=bing-web-search-api) elegendő ehhez a gyors útmutatóhoz. Szüksége lesz a hozzáférési kulcsot, ha az ingyenes próbaverzió aktiválásának, vagy használhat egy díjköteles előfizetési kulcsot az Azure irányítópultján.

## <a name="application-dependencies"></a>Alkalmazásfüggőségek
Szeretne beállítani egy konzolalkalmazást, a Bing Web Search SDK használatával, keresse meg a NuGet-csomagok kezelése lehetőséget a Visual Studio Megoldáskezelőjében. Adja hozzá a Microsoft.Azure.CognitiveServices.Search.VisualSearch csomagot.

A Web-Search SDK NuGet-csomag telepítése is telepíti a függőségeket, többek között:

* Microsoft.Rest.ClientRuntime
* Microsoft.Rest.ClientRuntime.Azure
* Newtonsoft.Json

## <a name="image-and-crop-area"></a>Kép- és körülvágása terület
Az alábbi képen látható a Microsoft termékigazgatókkal csoportja.  Visual Search SDK használatával, azt egy körülvágása területre a Rendszerkép feltöltése és egyéb lemezképek és a kijelölt területen, a nagyobb méretű kép az entitást tartalmazó weblapokra.  Ebben az esetben az entitás egy személy.

![A Microsoft vezető vezetőségi](./media/MS_SrLeaders.jpg)

## <a name="specify-the-crop-area-as-imageinfo-in-visualsearchrequest"></a>Adja meg a körülvágása terület a VisualSearchRequest ImageInfo
A példa az előző képen, amely megadja a bal felső, és megfelelő koordináták csökkentheti a teljes képet százaléka alapján egy körülvágása területéhez.  Az alábbi kód létrehoz egy `ImageInfo` objektum a körülvágása területről, és betölti a `ImageInfo` az objektum egy `VisualSearchRequest`.  A `ImageInfo` objektum is online a kép URL-CÍMÉT tartalmazza.

```
CropArea CropArea = new CropArea(top: (float)0.01, bottom: (float)0.30, left: (float)0.01, right: (float)0.20);
string imageURL = "https://docs.microsoft.com/azure/cognitive-services/bing-visual-search/media/ms_srleaders.jpg;
ImageInfo imageInfo = new ImageInfo(cropArea: CropArea, url: imageURL);

VisualSearchRequest visualSearchRequest = new VisualSearchRequest(imageInfo: imageInfo);
```
## <a name="search-for-images-similar-to-crop-area"></a>Hasonló területen körülvágása Képkeresés
A `VisualSearchRequest` körülvágása terület információt tartalmaz a lemezkép és az URL-CÍMÉT.  A `VisualSearchMethodAsync` metódus lekéri az eredményeket.
```
Console.WriteLine("\r\nSending visual search request with knowledgeRequest that contains URL and crop area");
var visualSearchResults = client.Images.VisualSearchMethodAsync(knowledgeRequest: visualSearchRequest).Result; 

```

## <a name="get-the-url-data-from-imagemoduleaction"></a>Az URL-cím adatok bekérése ImageModuleAction
A vizuális keresési eredmények megfelelnek `ImageTag` objektumokat.  Minden címke egy listát tartalmaz `ImageAction` objektumokat.  Minden egyes `ImageAction` tartalmaz egy `Data` mező, amely művelet típusától függenek értékek listáját:

Megtekintheti a különféle fenyegetési típusokat az alábbi kódra:
```
Console.WriteLine("\r\n" + "ActionType: " + i.ActionType + " -> WebSearchUrl: " + i.WebSearchUrl);

```
A teljes alkalmazás adja vissza:

* ActionType: PagesIncluding WebSearchURL:
* ActionType: WebSearchURL MoreSizes:
* ActionType: VisualSearch WebSearchURL:
* ActionType: ImageById WebSearchURL: 
* ActionType: RelatedSearches WebSearchURL:
* ActionType: Entitás -> WebSearchUrl: https://www.bing.com/cr?IG=E40D0E1A13404994ACB073504BC937A4&CID=03DCF882D7386A442137F49BD6596BEF&rd=1&h=BvvDoRtmZ35Xc_UZE4lZx6_eg7FHgcCkigU1D98NHQo&v=1&r=https%3a%2f%2fwww.bing.com%2fsearch%3fq%3dSatya%2bNadella&p=DevEx, 5380.1
* ActionType: -> WebSearchUrl TopicResults: https://www.bing.com/cr?IG=E40D0E1A13404994ACB073504BC937A4&CID=03DCF882D7386A442137F49BD6596BEF&rd=1&h=3QGtxPb3W9LemuHRxAlW4CW7XN4sPkUYCUynxAqI9zQ&v=1&r=https%3a%2f%2fwww.bing.com%2fdiscover%2fnadella%2bsatya&p=DevEx, 5382.1
* ActionType: -> WebSearchUrl ImageResults: https://www.bing.com/cr?IG=E40D0E1A13404994ACB073504BC937A4&CID=03DCF882D7386A442137F49BD6596BEF&rd=1&h=l-WNHO89Kkw69AmIGe2MhlUp6MxR6YsJszgOuM5sVLs&v=1&r=https%3a%2f%2fwww.bing.com%2fimages%2fsearch%3fq%3dSatya%2bNadella&p=DevEx, 5384.1

Ahogy az előző listában a `Entity` `ActionType` a Bing keresési lekérdezést tartalmaz, amely egy könnyen felismerhető névre személy, hely vagy egy dolog kapcsolatos információkat ad vissza.  A `TopicResults` és `ImageResults` típusok kapcsolódó képek lekérdezéseket tartalmaz. A Bing keresési eredményeket a lista hivatkozás URL-címeit.


## <a name="pagesincluding-actiontype-urls-of-images-found-by-visual-search"></a>A képek vizuális keresés által talált PagesIncluding ActionType URL-címek

A tényleges kép URL-címeket első igényel, amely beolvas egy cast egy `ActionType` , `ImageModuleAction`, tartalmazza, amely egy `Data` elem az értékek listáját.  Minden egyes érték a kép URL-CÍMÉT.  A következő típuskonverziók a `PagesIncluding` művelet típusát `ImageModuleAction` és kiolvassa az értékeket.
```
    if (i.ActionType == "PagesIncluding")
    {
        foreach(ImageObject o in (i as ImageModuleAction).Data.Value)
        {
            Console.WriteLine("ContentURL: " + o.ContentUrl);
        }
    }
```

## <a name="complete-code"></a>Teljes kódját

Az alábbi kód az előző példák futtatja. Küld egy képet bináris a post-kérés törzse, mellett egy cropArea objektumot jelenít meg, a Bing search URL-címek minden végrehajtóosztály. Ha a ActionType PagesIncluding, a kód lekér ImageObject elemek ImageObject adatokban.  Az adatok értékeket, amelyek a weboldalakon képek URL-címek listáját tartalmazza.  Másolja és illessze be az eredményül kapott vizuális Keresés URL-címek a böngésző mutatkoznak. Elemek másolását és beillesztését ContentUrl a böngészőben a képek megjelenítése.

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
[Visual Search-válasz](https://docs.microsoft.com/azure/cognitive-services/bing-visual-search/overview#the-response)