---
title: 'Oktatóanyag: ImageInsightsToken – Bing Visual Search'
titlesuffix: Azure Cognitive Services
description: A Bing Visual Search SDK használata az ImageInsightsToken által meghatározott képek URL-címének lekérésére.
services: cognitive-services
author: mikedodaro
manager: cgronlun
ms.service: cognitive-services
ms.component: bing-visual-search
ms.topic: tutorial
ms.date: 06/21/2018
ms.author: rosh
ms.openlocfilehash: 06d6bc8e53276b5542210c2843d7221d6fd79c09
ms.sourcegitcommit: f20e43e436bfeafd333da75754cd32d405903b07
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/17/2018
ms.locfileid: "49386434"
---
# <a name="tutorial-bing-visual-search-sdk-imageinsightstoken-and-results"></a>Oktatóanyag: Bing Visual Search SDK – ImageInsightsToken és eredmények
A Visual Search SDK tartalmaz egy beállítást, amely egy korábbi keresés képeit keresi meg online, és visszaadott eredménye egy `ImageInsightsToken`.  Ez a példa egy `ImageInsightsToken` lekérése után felhasználja a jogkivonatot egy későbbi kereséshez.  A kód elküldi az `ImageInsightsToken` adatot a Bing számára, majd visszaad eredményeket, amelyek tartalmazzák a Bing Search URL-címeket és az online talált hasonló képek URL-címeit.

## <a name="prerequisites"></a>Előfeltételek
Visual Studio 2017. Ha szükséges, töltse le az ingyenes közösségi verziót innen: https://www.visualstudio.com/vs/community/.
Az SDK-hívások hitelesítéséhez Cognitive Services API-kulcsra van szükség. Regisztráljon egy ingyenes próbaverzióra. A próbakulcs másodpercenként egy hívással hét napig érvényes. Az éles forgatókönyvekhez vásároljon hozzáférési kulcsot. Lásd még a díjszabási információkat.
A .NET Core SDK- és .NET Core 1.1-alkalmazások futtatásának képessége. A CORE, a keretrendszer és a futtatókörnyezet innen tölthetők le: https://www.microsoft.com/net/download/.

## <a name="application-dependencies"></a>Alkalmazásfüggőségek
Ha a Bing Web Search SDK-val szeretne beállítani egy konzolalkalmazást, keresse meg a NuGet-csomagok kezelése lehetőséget a Visual Studio Megoldáskezelőjében. Adja hozzá a következőket:
* Microsoft.Azure.CognitiveServices.Search.VisualSearch
* Microsoft.Azure.CognitiveServices.Search.ImageSearchpackage csomagok.

A NuGet Web Search SDK-csomag telepítésekor függőségeket is telepít, például:

* Microsoft.Rest.ClientRuntime
* Microsoft.Rest.ClientRuntime.Azure
* Newtonsoft.Json

## <a name="get-the-imageinsightstoken-from-image-search"></a>Az ImageInsightsToken lekérése a képkeresésből

Ez a példa egy `ImageInsightsToken` használatára épül, amely a következő módszerrel szerezhető be.  További információk erről a hívásról: [Image Search SDK C# – rövid útmutató](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/image-search-sdk-quickstart).

A kód rákeres a „Canadian Rockies” (kanadai Sziklás-hegység) lekérdezés eredményeire, és lekér egy ImageInsightsToken jogkivonatot. Kinyomtatja az első kép megállapítási jogkivonatát, a miniatűr URL-címét, és a képtartalom URL-címét.  A módszer által visszaadott `ImageInsightsToken` egy későbbi Visual Search-kérésben újra használható.

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

## <a name="specify-the-imageinsightstoken-for-visual-search-request"></a>Egy Visual Search-kérés ImageInsightsToken jogkivonatának megadása

Ez a példa az előző metódus által visszaadott megállapítási jogkivonatot használja. A következő kód létrehoz egy `ImageInfo` objektumot az `ImageInsightsToken` alapján, és betölti az ImageInfo objektumot a következő helyre: `VisualSearchRequest`. Egy `ImageInsightsToken` megadása a következőben: `ImageInfo`, a következőhöz: `VisualSearchRequest`

```
ImageInfo ImageInfo = new ImageInfo(imageInsightsToken: insightsTok);
```

## <a name="use-visual-search-to-find-images-from-an-imageinsightstoken"></a>Egy ImageInsightsToken jogkivonatból származó képek keresése a Visual Search segítségével

A `VisualSearchRequest` információkat tartalmaz az `ImageInfo` objektumban keresendő képre vonatkozó.  A `VisualSearchMethodAsync` metódus az eredményeket kéri le.
```
// An image binary is not necessary here, as the image is specified by insights token.
VisualSearchRequest VisualSearchRequest = new VisualSearchRequest(ImageInfo);

var visualSearchResults = client.Images.VisualSearchMethodAsync(knowledgeRequest: VisualSearchRequest).Result;
Console.WriteLine("\r\nVisual search request with knowledgeRequest");

```

## <a name="get-the-url-data-from-imagemoduleaction"></a>Az URL-adatok lekérése az ImageModuleAction műveletből
A Visual Search eredményei `ImageTag` objektumok.  Mindegy egyes címke `ImageAction` objektumok listáját tartalmazza.  Mindegy egyes `ImageAction` egy `Data` mezőt tartalmaz, amely a művelet típusától függő értékeket sorolja fel:

A különféle típusokat az alábbi kóddal kérheti le:
```
Console.WriteLine("\r\n" + "ActionType: " + i.ActionType + " -> WebSearchUrl: " + i.WebSearchUrl);

```
A teljes alkalmazás a következőket adja vissza:

* ActionType: MoreSizes -> WebSearchUrl:
* ActionType: VisualSearch -> WebSearchUrl:
* ActionType: ImageById -> WebSearchUrl:
* ActionType: RelatedSearches -> WebSearchUrl:
* ActionType: DocumentLevelSuggestions -> WebSearchUrl:
* ActionType: TopicResults -> WebSearchUrl: https://www.bing.com/cr?IG=3E32CC6CA5934FBBA14ABC3B2E4651F9&CID=1BA795A21EAF6A63175699B71FC36B7C&rd=1&h=BcQifmzdKFyyBusjLxxgO42kzq1Geh7RucVVqvH-900&v=1&r=https%3a%2f%2fwww.bing.com%2fdiscover%2fcanadian%2brocky&p=DevEx,5823.1
* ActionType: ImageResults -> WebSearchUrl: https://www.bing.com/cr?IG=3E32CC6CA5934FBBA14ABC3B2E4651F9&CID=1BA795A21EAF6A63175699B71FC36B7C&rd=1&h=PV9GzMFOI0AHZp2gKeWJ8DcveSDRE3fP2jHDKMpJSU8&v=1&r=https%3a%2f%2fwww.bing.com%2fimages%2fsearch%3fq%3doutdoor&p=DevEx,5831.1

Ahogy az előző listában látható volt, a `TopicResults` és az `ImageResults` típus tartalmazza a kapcsolódó képekre vonatkozó lekérdezéseket. A listában lévő URL-ek a Bing keresési eredményeire mutatnak.


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
További információk ezekről az adattípusokról: [Képek – Visual Search](https://docs.microsoft.com/rest/api/cognitiveservices/bingvisualsearch/images/visualsearch).

## <a name="complete-code"></a>Teljes kód

A következő kód futtatja az előző példákat. Elküldi az `ImageInsightsToken` elemet egy közzétételi kérésben. Ezután kinyomtatja az egyes ActionType típusok Bing keresési URL-címeit. Ha az ActionType értéke `PagesIncluding`, a kód lekéri a `ImageObject` elemeket a `Data` objektumból.  A `Data` tartalmazza az értékek listáját, azaz a weboldalakon lévő képek URL-címeit.  Másolja, majd illessze be a Visual Search által visszaadott URL-eket a böngészőbe az eredmények megjelenítéséhez. Másolja, majd illessze be a ContentUrl elemeket a böngészőbe a képek megjelenítéséhez.

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
[Visual Search válasz](https://docs.microsoft.com/azure/cognitive-services/bing-visual-search/overview#the-response)
