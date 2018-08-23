---
title: A Bing Visual Search SDK ImageInsightsToken oktatóanyag |} A Microsoft Docs
description: Hogyan lehet a Bing Visual Search SDK használatával a képek ImageInsightsToken által megadott URL-címek lekérése.
services: cognitive-services
author: mikedodaro
manager: ronakshah
ms.service: cognitive-services
ms.component: bing-visual-search
ms.topic: article
ms.date: 06/21/2018
ms.author: rosh
ms.openlocfilehash: 8f6e7f7e88ae78fe7e8a9be4adefd689dd26d0f9
ms.sourcegitcommit: 7b845d3b9a5a4487d5df89906cc5d5bbdb0507c8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/14/2018
ms.locfileid: "41987640"
---
# <a name="tutorial-bing-visual-search-sdk-imageinsightstoken-and-results"></a>Oktatóanyag: A Bing Visual Search SDK ImageInsightsToken és eredmények
A Visual Search SDK tartalmaz egy beállítást, az előző keresési visszaadó online rendszerképek keresése egy `ImageInsightsToken`.  Ez a példa lekéri egy `ImageInsightsToken` és használja a jogkivonatot egy későbbi keresés.  A kód elküldi a `ImageInsightsToken` Bing és az értéket ad vissza, a Bing Keresés URL-címek és található online-hoz hasonló képek URL-címeket tartalmazó eredmények.

## <a name="prerequisites"></a>Előfeltételek
Visual Studio 2017. Ha szükséges, hogy ingyenes közösségi verziója letölthető innen: https://www.visualstudio.com/vs/community/.
Hitelesítés az SDK-hívásokat a cognitive services API-kulcs szükséges. Regisztráljon egy ingyenes próba kulcsot. A Próbakulcs jó másodpercenként egy hívás a hét napja. A termelési forgatókönyvekhez vásároljon egy hozzáférési kulcsot. Lásd még a díjszabásról.
Lehetővé teszi a .NET core SDK-t, .net core 1.1-es alkalmazások futtatásához. Itt megtekintheti mag, keretrendszer és a futtatókörnyezet: https://www.microsoft.com/net/download/.

##<a name="application-dependencies"></a>Alkalmazásfüggőségek
Szeretne beállítani egy konzolalkalmazást, a Bing Web Search SDK használatával, keresse meg a NuGet-csomagok kezelése lehetőséget a Visual Studio Megoldáskezelőjében. Adja hozzá:
* Microsoft.Azure.CognitiveServices.Search.VisualSearch
* Microsoft.Azure.CognitiveServices.Search.ImageSearchpackage csomagok.

A Web-Search SDK NuGet-csomag telepítése is telepíti a függőségeket, többek között:

* Microsoft.Rest.ClientRuntime
* Microsoft.Rest.ClientRuntime.Azure
* Newtonsoft.Json

## <a name="get-the-imageinsightstoken-from-image-search"></a>A ImageInsightsToken kérhet Képkeresés
Ez a példa egy `ImageInsightsToken` kaphatják meg a következő metódust.  Ez a hívás kapcsolatos további információkért lásd: [kép Search SDK C# gyorsútmutató](https://docs.microsoft.com/en-us/azure/cognitive-services/bing-image-search/image-search-sdk-quickstart).

A kód "Kanadai Rockies" a lekérdezés eredménye keresi, és egy ImageInsightsToken beolvasása. Jelenít meg az első rendszerkép insights token, a Miniatűr URL-címet, és a tartalom URL-címe.  A metódus visszaadja a `ImageInsightsToken`egy későbbi vizuális keresés kérése a használatra.

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

## <a name="specify-the-imageinsightstoken-for-visual-search-request"></a>Adja meg a vizuális keresési kérelem ImageInsightsToken
Ez a példa az insights-jogkivonatot az előző metódus által visszaadott használja. Az alábbi kód létrehoz egy `ImageInfo` objektum a `ImageInsightsToken` és betölti az ImageInfo objektumot egy `VisualSearchRequest`. Adja meg `ImageInsightsToken` a egy `ImageInfo` számára a `VisualSearchRequest`

```
ImageInfo ImageInfo = new ImageInfo(imageInsightsToken: insightsTok);
```
## <a name="use-visual-search-to-find-images-from-an-imageinsightstoken"></a>Vizuális keresés használatával egy ImageInsightsToken származó rendszerképek keresése
A `VisualSearchRequest` a lemezképet keresse meg az információkat tartalmaz a `ImageInfo` objektum.  A `VisualSearchMethodAsync` metódus lekéri az eredményeket.
```
// An image binary is not necessary here, as the image is specified by insights token.
VisualSearchRequest VisualSearchRequest = new VisualSearchRequest(ImageInfo);

var visualSearchResults = client.Images.VisualSearchMethodAsync(knowledgeRequest: VisualSearchRequest).Result;
Console.WriteLine("\r\nVisual search request with knowledgeRequest");

```

## <a name="get-the-url-data-from-imagemoduleaction"></a>Az URL-cím adatok bekérése ImageModuleAction
A vizuális keresési eredmények megfelelnek `ImageTag` objektumokat.  Minden címke egy listát tartalmaz `ImageAction` objektumokat.  Minden egyes `ImageAction` tartalmaz egy `Data` mező, amely művelet típusától függenek értékek listáját:

Megtekintheti a különféle fenyegetési típusokat az alábbi kódra:
```
Console.WriteLine("\r\n" + "ActionType: " + i.ActionType + " -> WebSearchUrl: " + i.WebSearchUrl);

```
A teljes alkalmazás adja vissza:

* ActionType: MoreSizes WebSearchUrl ->:
* ActionType: VisualSearch WebSearchUrl ->:
* ActionType: ImageById WebSearchUrl ->:
* ActionType: RelatedSearches WebSearchUrl ->:
* ActionType: DocumentLevelSuggestions WebSearchUrl ->:
* ActionType: -> WebSearchUrl TopicResults: https://www.bing.com/cr?IG=3E32CC6CA5934FBBA14ABC3B2E4651F9&CID=1BA795A21EAF6A63175699B71FC36B7C&rd=1&h=BcQifmzdKFyyBusjLxxgO42kzq1Geh7RucVVqvH-900&v=1&r=https%3a%2f%2fwww.bing.com%2fdiscover%2fcanadian%2brocky&p=DevEx, 5823.1
* ActionType: -> WebSearchUrl ImageResults: https://www.bing.com/cr?IG=3E32CC6CA5934FBBA14ABC3B2E4651F9&CID=1BA795A21EAF6A63175699B71FC36B7C&rd=1&h=PV9GzMFOI0AHZp2gKeWJ8DcveSDRE3fP2jHDKMpJSU8&v=1&r=https%3a%2f%2fwww.bing.com%2fimages%2fsearch%3fq%3doutdoor&p=DevEx, 5831.1

Ahogy az előző listában a `TopicResults` és `ImageResults` típusok kapcsolódó képek lekérdezéseket tartalmaz. A Bing keresési eredményeket a lista hivatkozás URL-címeit.


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
Ezeken az adattípusokon kapcsolatos további információkért lásd: [rendszerképek – vizuális keresés](https://docs.microsoft.com/en-us/rest/api/cognitiveservices/bingvisualsearch/images/visualsearch).
## <a name="complete-code"></a>Teljes kódját

Az alábbi kód az előző példák futtatja. Elküldi a `ImageInsightsToken` egy post-kérés. Ezután jelenít meg a Bing minden ActionType URL-címek keresése. Ha a ActionType `PagesIncluding`, a kód lekéri a `ImageObject` elemeket `Data`.  A `Data` értékeket, amelyek a weboldalakon képek URL-címek listáját tartalmazza.  Másolja és illessze be az eredményül kapott vizuális Keresés URL-címek a böngésző mutatkoznak. Elemek másolását és beillesztését ContentUrl a böngészőben a képek megjelenítése.

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
[Visual Search-válasz](https://docs.microsoft.com/azure/cognitive-services/bing-visual-search/overview#the-response)
