---
title: Bing Visual keresési SDK ImageInsightsToken oktatóanyag |} Microsoft Docs
description: Megtudhatja, hogyan használható a Bing Visual keresési SDK képek ImageInsightsToken által megadott URL-címek lekérése.
services: cognitive-services
author: mikedodaro
manager: ronakshah
ms.service: cognitive-services
ms.component: bing-visual-search
ms.topic: article
ms.date: 06/21/2018
ms.author: rosh
ms.openlocfilehash: 578fa90f504920030b488d2b8fa3a2d0232cccce
ms.sourcegitcommit: 6eb14a2c7ffb1afa4d502f5162f7283d4aceb9e2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/25/2018
ms.locfileid: "36753661"
---
# <a name="tutorial-bing-visual-search-sdk-imageinsightstoken-and-results"></a>Oktatóanyag: Bing Visual keresési SDK ImageInsightsToken és eredmények
A vizuális keresési SDK tartalmaz egy lehetőség, hogy a képek online visszaadó előző keresési található egy `ImageInsightsToken`.  Ez a példa lekérdezi egy `ImageInsightsToken` és a későbbi keresési használja a jogkivonatot.  A kód elküldi a `ImageInsightsToken` el a Bing és értéket ad vissza, amely tartalmazza a Bing keresési URL-címek és online hasonló kép URL-címei.

## <a name="prerequisites"></a>Előfeltételek
Visual Studio 2017. Ha szükséges, hogy ingyenes közösségi verziója letölthető innen: https://www.visualstudio.com/vs/community/.
SDK-hívások hitelesítése kognitív szolgáltatások API-kulcs szükséges. Regisztráljon egy ingyenes próba kulcsot. A Próbakulcs jó másodpercenként egy hívás a hét napja. Éles környezetben vásároljon egy hozzáférési kulcsot. Lásd: is díjszabási információkat.
A .NET core SDK, .net core 1.1-es alkalmazások futási képességét. Itt kaphat mag, a keretrendszer és a futtatókörnyezet: https://www.microsoft.com/net/download/.

##<a name="application-dependencies"></a>Alkalmazásfüggőségek
Egy konzolalkalmazást, a Bing webes keresés SDK használatával beállításához tallózással keresse meg a NuGet-csomagok kezelése lehetőséget a Visual Studio megoldáskezelőjében. Adja hozzá:
* Microsoft.Azure.CognitiveServices.Search.VisualSearch
* Microsoft.Azure.CognitiveServices.Search.ImageSearchpackage csomagok.

A Web-keresési SDK NuGet-csomag telepítése is telepíti a függőségek, beleértve:

* Microsoft.Rest.ClientRuntime
* Microsoft.Rest.ClientRuntime.Azure
* Newtonsoft.Json

## <a name="get-the-imageinsightstoken-from-image-search"></a>A ImageInsightsToken beszerezni lemezkép keresése
Ez a példa egy `ImageInsightsToken` révén a következő metódust.  Ez a hívás kapcsolatos további információkért lásd: [kép keresési SDK C# gyors üzembe helyezés](https://docs.microsoft.com/en-us/azure/cognitive-services/bing-image-search/image-search-sdk-quickstart).

A kód "Kanadai Rockies" lekérdezés eredményeit az keres, és egy ImageInsightsToken lekérdezi. Hogy kiírja az első kép insights token, miniatűr URL-címet, és a tartalom URL-címe.  A metódus visszaadja a `ImageInsightsToken`Visual keresési kérelem későbbi használatra.

```
        private static String ImageResults(String subKey)
        {
            String insightTok = "None";
            try
            {
                var client = new ImageSearchAPI(new Microsoft.Azure.CognitiveServices.Search.ImageSearch.ApiKeyServiceClientCredentials(subKey)); //
                var imageResults = client.Images.SearchAsync(query: "canadian rockies").Result;
                Console.WriteLine("Search images for query \"canadian rockies\"");

                if (imageResults == null)
                {
                    Console.WriteLine("No image result data.");
                }
                else
                {
                    // Image results
                    if (imageResults.Value.Count > 0)
                    {
                        var firstImageResult = imageResults.Value.First();

                        Console.WriteLine($"\r\nImage result count: {imageResults.Value.Count}");
                        insightTok = firstImageResult.ImageInsightsToken;
                        Console.WriteLine($"First image insights token: {firstImageResult.ImageInsightsToken}");  
                        Console.WriteLine($"First image thumbnail url: {firstImageResult.ThumbnailUrl}");
                        Console.WriteLine($"First image content url: {firstImageResult.ContentUrl}");
                    }
                    else
                    {
                        insightTok = "None found";
                        Console.WriteLine("Couldn't find image results!");
                    }
                }
            }

            catch (Exception ex)
            {
                Console.WriteLine("\r\nEncountered exception. " + ex.Message);
            }

            return insightTok;
        }
```

## <a name="specify-the-imageinsightstoken-for-visual-search-request"></a>Adja meg a ImageInsightsToken Visual keresési kérelem
A példa az előző metódus által visszaadott insights jogkivonat. Az alábbi kód létrehoz egy `ImageInfo` objektum a `ImageInsightsToken` és betölti az ImageInfo objektumot egy `VisualSearchRequest`. Adja meg `ImageInsightsToken` a egy `ImageInfo` a a `VisualSearchRequest`

```
ImageInfo ImageInfo = new ImageInfo(imageInsightsToken: insightsTok);
```
## <a name="use-visual-search-to-find-images-from-an-imageinsightstoken"></a>Lemezképek keresésére egy ImageInsightsToken az Visual keresés használata
A `VisualSearchRequest` keresendő lemezképére vonatkozó információ tartalmazza a `ImageInfo` objektum.  A `VisualSearchMethodAsync` metódus lekéri az eredményeket.
```
// An image binary is not necessary here, as the image is specified by insights token.
VisualSearchRequest VisualSearchRequest = new VisualSearchRequest(ImageInfo);

var visualSearchResults = client.Images.VisualSearchMethodAsync(knowledgeRequest: VisualSearchRequest).Result;
Console.WriteLine("\r\nVisual search request with knowledgeRequest");

```

## <a name="get-the-url-data-from-imagemoduleaction"></a>Az URL-cím adatokat lekérni ImageModuleAction
Visual a keresési találatok között `ImageTag` objektumok.  Minden tag listáját tartalmazza `ImageAction` objektumok.  Minden egyes `ImageAction` tartalmaz egy `Data` mező, amelyben függő művelet típusú értékek listája:

A különféle kaphat az alábbi kódra:
```
Console.WriteLine("\r\n" + "ActionType: " + i.ActionType + " -> WebSearchUrl: " + i.WebSearchUrl);

```
A teljes alkalmazás adja vissza:

* Művelettípushoz: MoreSizes -> WebSearchUrl:
* Művelettípushoz: VisualSearch WebSearchUrl ->:
* Művelettípushoz: ImageById WebSearchUrl ->:
* Művelettípushoz: RelatedSearches WebSearchUrl ->:
* Művelettípushoz: DocumentLevelSuggestions WebSearchUrl ->:
* Művelettípushoz: TopicResults -> WebSearchUrl: https://www.bing.com/cr?IG=3E32CC6CA5934FBBA14ABC3B2E4651F9&CID=1BA795A21EAF6A63175699B71FC36B7C&rd=1&h=BcQifmzdKFyyBusjLxxgO42kzq1Geh7RucVVqvH-900&v=1&r=https%3a%2f%2fwww.bing.com%2fdiscover%2fcanadian%2brocky&p=DevEx, 5823.1
* Művelettípushoz: ImageResults -> WebSearchUrl: https://www.bing.com/cr?IG=3E32CC6CA5934FBBA14ABC3B2E4651F9&CID=1BA795A21EAF6A63175699B71FC36B7C&rd=1&h=PV9GzMFOI0AHZp2gKeWJ8DcveSDRE3fP2jHDKMpJSU8&v=1&r=https%3a%2f%2fwww.bing.com%2fimages%2fsearch%3fq%3doutdoor&p=DevEx, 5831.1

Ahogy az előző listában a `TopicResults` és `ImageResults` típusok kapcsolódó lemezképek lekérdezéseket tartalmaznak. Bing keresési eredmények lista kapcsolat URL-címeket.


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
Ezek adattípusokkal kapcsolatban további információkért lásd: [képek - Visual keresési](https://docs.microsoft.com/en-us/rest/api/cognitiveservices/bingvisualsearch/images/visualsearch).
## <a name="complete-code"></a>Teljes kód

A következő kódot az előző példákban futtatja. Elküldi a `ImageInsightsToken` egy post kérelemben. Ezt követően, hogy kiírja a Bing minden a(z) URL-címek keresése. Ha a művelettípus `PagesIncluding`, a kód jogosultságot kap a `ImageObject` elemeknek `Data`.  A `Data` értékek, amelyek weblapok kép URL-címek listáját tartalmazza.  Másolással illessze be az eredményül kapott Visual keresési URL-címek a böngésző eredmények megjelenítése. Másolja és illessze be a ContentUrl kép megjelenítéséhez böngészőben.

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

            insightsToken = ImageResults(subscriptionKey);

            VisualSearchInsightsToken(subscriptionKey, insightsToken);

            Console.WriteLine("Any key to quit...");
            Console.ReadKey();

        }

        // Searches for results on query (Canadian Rockies) and gets an ImageInsightsToken.
        // Also prints first image insights token, thumbnail url, and image content url.
        private static String ImageResults(String subKey)
        {
            String insightTok = "None";
            try
            {
                var client = new ImageSearchAPI(new Microsoft.Azure.CognitiveServices.Search.ImageSearch.ApiKeyServiceClientCredentials(subKey)); //
                var imageResults = client.Images.SearchAsync(query: "canadian rockies").Result;
                Console.WriteLine("Search images for query \"canadian rockies\"");

                if (imageResults == null)
                {
                    Console.WriteLine("No image result data.");
                }
                else
                {
                    // Image results
                    if (imageResults.Value.Count > 0)
                    {
                        var firstImageResult = imageResults.Value.First();

                        Console.WriteLine($"\r\nImage result count: {imageResults.Value.Count}");
                        insightTok = firstImageResult.ImageInsightsToken;
                        Console.WriteLine($"First image insights token: {firstImageResult.ImageInsightsToken}");  
                        Console.WriteLine($"First image thumbnail url: {firstImageResult.ThumbnailUrl}");
                        Console.WriteLine($"First image content url: {firstImageResult.ContentUrl}");
                    }
                    else
                    {
                        insightTok = "None found";
                        Console.WriteLine("Couldn't find image results!");
                    }
                }
            }

            catch (Exception ex)
            {
                Console.WriteLine("\r\nEncountered exception. " + ex.Message);
            }

            return insightTok;
        }


        // This method will get Visual Search results from an imageInsightsToken obtained from the return value of the ImageResults method.
        // The method includes imageInsightsToken the in a knowledgeRequest parameter, along with a cropArea object. 
        // It prints out the imageInsightsToken uploaded in the request.
        // Finally the example prints URLs of images found using the imageInsightsToken.

        public static void VisualSearchInsightsToken(string subscriptionKey, string insightsTok)
        {
            var client = new VisualSearchAPI(new Microsoft.Azure.CognitiveServices.Search.VisualSearch.ApiKeyServiceClientCredentials(subscriptionKey));

            try
            {
                // The image can be specified via an insights token, in the ImageInfo object.
                ImageInfo ImageInfo = new ImageInfo(imageInsightsToken: insightsTok);

                // An image binary is not necessary here, as the image is specified by insights token.
                VisualSearchRequest VisualSearchRequest = new VisualSearchRequest(ImageInfo);

                var visualSearchResults = client.Images.VisualSearchMethodAsync(knowledgeRequest: VisualSearchRequest).Result;
                Console.WriteLine("\r\nVisual search request with imageInsightsToken and knowledgeRequest");

                if (visualSearchResults == null)
                {
                    Console.WriteLine("No visual search result data.");
                }
                else
                {
                    // Visual Search results
                    if (visualSearchResults.Image?.ImageInsightsToken != null)
                    {
                        Console.WriteLine($"Uploaded image insights token: {insightsTok}");
                    }
                    else
                    {
                        Console.WriteLine("Couldn't find image insights token!");
                    }

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
[Visual keresési válasz](https://review.docs.microsoft.com/en-us/azure/cognitive-services/bing-visual-search/overview?branch=pr-en-us-44614#the-response)