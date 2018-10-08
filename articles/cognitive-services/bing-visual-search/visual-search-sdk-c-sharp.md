---
title: 'Rövid útmutató: A Bing Visual Search SDK C# használata'
titleSuffix: Azure Cognitive Services
description: A Visual Search SDK C# konzolalkalmazás beállítása.
services: cognitive-services
author: mikedodaro
manager: cgronlun
ms.service: cognitive-services
ms.component: bing-web-search
ms.topic: quickstart
ms.date: 05/16/2018
ms.author: v-gedod
ms.openlocfilehash: 938768ad366611b8651adc3d554c44c16a3830ef
ms.sourcegitcommit: ad08b2db50d63c8f550575d2e7bb9a0852efb12f
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/26/2018
ms.locfileid: "47224528"
---
# <a name="quickstart-bing-visual-search-sdk-c"></a>Rövid útmutató: Bing Visual Search SDK C#

A Bing Visual Search SDK a REST API funkcióit használja webes kérelmekhez és elemzési eredményekhez.
A [C# Bing Visual Search SDK mintáinak forráskódja](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/BingSearchv7/BingVisualSearch) elérhető a GitHubon.

A kódforgatókönyvek a következő kategóriákban vannak dokumentálva:
* [Visual Search-ügyfél](#client)
* [A teljes konzolalkalmazás](#complete)
* [Bináris kép a cropArea elemmel](#binary-crop)
* [KnowledgeRequest paraméter](#knowledge-req)
* [Címkék, műveletek és actionType](#tags-actions)
* [A címkék és műveletek száma, valamint az első actionType ](#num-tags-actions)

## <a name="prerequisites"></a>Előfeltételek

* Visual Studio 2017. Szükség esetén innen letöltheti az ingyenes közösségi verziót: https://www.visualstudio.com/vs/community/.
* Az SDK-hívások hitelesítéséhez egy Cognitive Services API-kulcs szükséges. Regisztráljon egy [ingyenes próbaverziós kulcsért](https://azure.microsoft.com/try/cognitive-services/?api=search-api-v7). A próbaverziós kulcs hét napig érvényes, másodpercenként egy hívással. Éles környezeti forgatókönyvekhez [vásároljon hozzáférési kulcsot](https://portal.azure.com/#create/Microsoft.CognitiveServicesBingSearch-v7). [Díjszabási információ](https://azure.microsoft.com/pricing/details/cognitive-services/search-api/visual/).
* .NET Core SDK- és .NET Core 1.1-alapú alkalmazások futtatása. A CORE, a keretrendszer és a futtatókörnyezet itt tölthető le: https://www.microsoft.com/net/download/.

## <a name="application-dependencies"></a>Alkalmazásfüggőségek

Ha a Bing Web Search SDK-val szeretne beállítani egy konzolalkalmazást, keresse meg a `Manage NuGet Packages` lehetőséget a Visual Studióban, a Megoldáskezelőben.  Vegye fel a `Microsoft.Azure.CognitiveServices.Search.VisualSearch` csomagot.

A [NuGet Web Search SDK-csomag](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Search.VisualSearch/1.0) telepítésekor függőségeket is telepít, például a következőket:
* Microsoft.Rest.ClientRuntime
* Microsoft.Rest.ClientRuntime.Azure
* Newtonsoft.Json

<a name="client"></a>

## <a name="visual-search-client"></a>Visual Search-ügyfél
A `VisualSearchAPI` ügyfél egy példányának létrehozásához adja hozzá a using parancsot:

```csharp
using Microsoft.Azure.CognitiveServices.Search.VisualSearch;
using Microsoft.Azure.CognitiveServices.Search.VisualSearch.Models;
```

Ezután példányosítsa az ügyfelet:

```csharp
var client = new WebSearchAPI(new ApiKeyServiceClientCredentials("YOUR-ACCESS-KEY"));
```

Az ügyféllel keressen képeket:

```csharp
 System.IO.FileStream stream = new FileStream(Path.Combine("TestImages", "image.jpg"), FileMode.Open;
 // The knowledgeRequest parameter is not required if an image binary is passed in the request body
 var visualSearchResults = client.Images.VisualSearchMethodAsync(image: stream, knowledgeRequest: (string)null).Result;
```

Elemezze az előző lekérdezés eredményeit:

```csharp
// Visual Search results
if (visualSearchResults.Image?.ImageInsightsToken != null)
{
    Console.WriteLine($"Uploaded image insights token: {visualSearchResults.Image.ImageInsightsToken}");
}
else
{
    Console.WriteLine("Couldn't find image insights token!");
}

// List of tags
if (visualSearchResults.Tags.Count > 0)
{
    var firstTagResult = visualSearchResults.Tags.First();
    Console.WriteLine($"Visual search tag count: {visualSearchResults.Tags.Count}");

    // List of actions in first tag
    if (firstTagResult.Actions.Count > 0)
    {
        var firstActionResult = firstTagResult.Actions.First();
        Console.WriteLine($"First tag action count: {firstTagResult.Actions.Count}");
        Console.WriteLine($"First tag action type: {firstActionResult.ActionType}");
    }
    else
    {
        Console.WriteLine("Couldn't find tag actions!");
    }
```

<a name="complete"></a> 

## <a name="complete-console-application"></a>A teljes konzolalkalmazás

A következő konzolalkalmazás végrehajtja a korábban definiált lekérdezést, és elemzi az eredményeket:

```csharp
using Microsoft.Azure.CognitiveServices.Search.VisualSearch;
using Microsoft.Azure.CognitiveServices.Search.VisualSearch.Models;
using System;
using System.IO;
using System.Linq;

namespace VisualSrchSDK
{
    class Program
    {
        static void Main(string[] args)
        {
            String subKey = "YOUR-SUBSCRIPTION-KEY";

            VisualSearchImageBinary(subKey);
            //VisualSearchImageBinaryWithCropArea(subKey);
            //VisualSearchUrlWithFilters(subKey);
            //VisualSearchInsightsTokenWithCropArea(subKey);
            //VisualSearchUrlWithJson(subKey);

            Console.WriteLine("\r\nAny key to quit...");
            Console.ReadKey();
        }


        // This will send an image binary in the body of the post request and print out the imageInsightsToken, 
        //  the number of tags, the number of actions, and the first actionType.

        public static void VisualSearchImageBinary(string subscriptionKey)
        {
            var client = new VisualSearchAPI(new ApiKeyServiceClientCredentials(subscriptionKey));

            try
            {
                using (System.IO.FileStream stream = new FileStream(Path.Combine("TestImages", "image.jpg"), FileMode.Open))
                {
                    // The knowledgeRequest parameter is not required if an image binary is passed in the request body
                    var visualSearchResults = client.Images.VisualSearchMethodAsync(image: stream, knowledgeRequest: (string)null).Result;
                    Console.WriteLine("\r\nVisual search request with binary of image");

                    if (visualSearchResults == null)
                    {
                        Console.WriteLine("No visual search result data.");
                    }
                    else
                    {
                        // Visual Search results
                        if (visualSearchResults.Image?.ImageInsightsToken != null)
                        {
                            Console.WriteLine($"Uploaded image insights token: {visualSearchResults.Image.ImageInsightsToken}");
                        }
                        else
                        {
                            Console.WriteLine("Couldn't find image insights token!");
                        }

                        // List of tags
                        if (visualSearchResults.Tags.Count > 0)
                        {
                            var firstTagResult = visualSearchResults.Tags.First();
                            Console.WriteLine($"Visual search tag count: {visualSearchResults.Tags.Count}");

                            // List of actions in first tag
                            if (firstTagResult.Actions.Count > 0)
                            {
                                var firstActionResult = firstTagResult.Actions.First();
                                Console.WriteLine($"First tag action count: {firstTagResult.Actions.Count}");
                                Console.WriteLine($"First tag action type: {firstActionResult.ActionType}");
                            }
                            else
                            {
                                Console.WriteLine("Couldn't find tag actions!");
                            }

                        }
                        else
                        {
                            Console.WriteLine("Couldn't find image tags!");
                        }
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

A Bing keresőmintái az SDK különböző funkcióit mutatják be.  Adja hozzá a következő függvényeket a korábban definiált `VisualSrchSDK` osztályhoz.

<a name="binary-crop"></a>

## <a name="image-binary-post-with-croparea"></a>Bináris kép a cropArea elemmel

A következő kód egy bináris képet és egy cropArea-objektumot küld a POST-kérés törzsében.  Ezután kinyomtatja az imageInsightsTokent, a címkék számát, a műveletek számát, valamint az első actionType elemet.

```csharp
public static void VisualSearchImageBinaryWithCropArea(string subscriptionKey)
{
    var client = new VisualSearchAPI(new ApiKeyServiceClientCredentials(subscriptionKey));

    try
    {
        using (FileStream stream = new FileStream(Path.Combine("TestImages", "image.jpg"), FileMode.Open))
        {
            // The ImageInfo struct contains a crop area specifying a region to crop in the uploaded image
            CropArea CropArea = new CropArea(top: (float)0.1, bottom: (float)0.5, left: (float)0.1, right: (float)0.9);
            ImageInfo ImageInfo = new ImageInfo(cropArea: CropArea);
            VisualSearchRequest VisualSearchRequest = new VisualSearchRequest(imageInfo: ImageInfo);

            // The knowledgeRequest here holds additional information about the image, which is passed in in binary form
            var visualSearchResults = client.Images.VisualSearchMethodAsync(image: stream, knowledgeRequest: VisualSearchRequest).Result;
            Console.WriteLine("\r\nVisual search request with binary of image with knowledgeRequest of CropArea");

            if (visualSearchResults == null)
            {
                Console.WriteLine("No visual search result data.");
            }
            else
            {
                // Visual Search results
                if (visualSearchResults.Image?.ImageInsightsToken != null)
                {
                    Console.WriteLine($"Uploaded image insights token: {visualSearchResults.Image.ImageInsightsToken}");
                }
                else
                {
                    Console.WriteLine("Couldn't find image insights token!");
                }

                // List of tags
                if (visualSearchResults.Tags.Count > 0)
                {
                    var firstTagResult = visualSearchResults.Tags.First();
                    Console.WriteLine($"Visual search tag count: {visualSearchResults.Tags.Count}");

                    // List of actions in first tag
                    if (firstTagResult.Actions.Count > 0)
                    {
                        var firstActionResult = firstTagResult.Actions.First();
                        Console.WriteLine($"First tag action count: {firstTagResult.Actions.Count}");
                        Console.WriteLine($"First tag action type: {firstActionResult.ActionType}");
                    }
                    else
                    {
                        Console.WriteLine("Couldn't find tag actions!");
                    }

                }
                else
                {
                    Console.WriteLine("Couldn't find image tags!");
                }
            }
        }
    }

    catch (Exception ex)
    {
        Console.WriteLine("Encountered exception. " + ex.Message);
    }
}
```

<a name="knowledge-req"></a>

## <a name="knowledgerequest-parameter"></a>KnowledgeRequest paraméter

A következő kód egy kép URL-címét, valamint a \"site:www.bing.com\" szűrőt küldi a `knowledgeRequest` paraméterben.  Ezután kinyomtatja az `imageInsightsToken` elemet, a címkék számát, a műveletek számát, valamint az első actionType elemet.

```csharp
public static void VisualSearchUrlWithFilters(string subscriptionKey)
{
    var client = new VisualSearchAPI(new ApiKeyServiceClientCredentials(subscriptionKey));

    try
    {
        // The image can be specified via URL, in the ImageInfo object
        var ImageUrl = "https://images.unsplash.com/photo-1512546148165-e50d714a565a?w=600&q=80";
        ImageInfo ImageInfo = new ImageInfo(url: ImageUrl);

        // Optional filters inside the knowledgeRequest will restrict similar products and images to certain domains
        Filters Filters = new Filters(site: "www.bing.com");
        KnowledgeRequest KnowledgeRequest = new KnowledgeRequest(filters: Filters);

        // An image binary is not necessary here, as the image is specified via URL
        VisualSearchRequest VisualSearchRequest = new VisualSearchRequest(imageInfo: ImageInfo, knowledgeRequest: KnowledgeRequest);
        var visualSearchResults = client.Images.VisualSearchMethodAsync(knowledgeRequest: VisualSearchRequest).Result;
        Console.WriteLine("\r\nVisual search request with url of image and knowledgeRequest based on filters");

        if (visualSearchResults == null)
        {
            Console.WriteLine("No visual search result data.");
        }
        else
        {
            // Visual Search results
            if (visualSearchResults.Image?.ImageInsightsToken != null)
            {
                Console.WriteLine($"Uploaded image insights token: {visualSearchResults.Image.ImageInsightsToken}");
            }
            else
            {
                Console.WriteLine("Couldn't find image insights token!");
            }

            // List of tags
            if (visualSearchResults.Tags.Count > 0)
            {
                var firstTagResult = visualSearchResults.Tags.First();
                Console.WriteLine($"Visual search tag count: {visualSearchResults.Tags.Count}");

                // List of actions in first tag
                if (firstTagResult.Actions.Count > 0)
                {
                    var firstActionResult = firstTagResult.Actions.First();
                    Console.WriteLine($"First tag action count: {firstTagResult.Actions.Count}");
                    Console.WriteLine($"First tag action type: {firstActionResult.ActionType}");
                }
                else
                {
                    Console.WriteLine("Couldn't find tag actions!");
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
```

<a name="tags-actions"></a>

## <a name="tags-actions-and-actiontype"></a>Címkék, műveletek és actionType

A következő kód egy imageinsightstoken elemet és egy cropArea-objektumot küld a knowledgeRequest paraméterben.  Ezután kinyomtatja az imageInsightsTokent, a címkék számát, a műveletek számát, valamint az első actionType elemet.

```csharp
public static void VisualSearchInsightsTokenWithCropArea(string subscriptionKey)
{
    var client = new VisualSearchAPI(new ApiKeyServiceClientCredentials(subscriptionKey));

    try
    {
        // The image can be specified via an insights token, in the ImageInfo object
        var ImageInsightsToken = "bcid_113F29C079F18F385732D8046EC80145*ccid_oV/QcH95*mid_687689FAFA449B35BC11A1AE6CEAB6F9A9B53708*thid_R.113F29C079F18F385732D8046EC80145";

        // An optional crop area can be passed in to define a region of interest in the image
        CropArea CropArea = new CropArea(top: (float)0.1, bottom: (float)0.5, left: (float)0.1, right: (float)0.9);
        ImageInfo ImageInfo = new ImageInfo(imageInsightsToken: ImageInsightsToken, cropArea: CropArea);

        // An image binary is not necessary here, as the image is specified via insights token
        VisualSearchRequest VisualSearchRequest = new VisualSearchRequest(imageInfo: ImageInfo);
        var visualSearchResults = client.Images.VisualSearchMethodAsync(knowledgeRequest: VisualSearchRequest).Result;
        Console.WriteLine("\r\nVisual search request with ImageInsightsToken and knowledgeRequest based on imageInfo of cropArea");

        if (visualSearchResults == null)
        {
            Console.WriteLine("No visual search result data.");
        }
        else
        {
            // Visual Search results
            if (visualSearchResults.Image?.ImageInsightsToken != null)
            {
                Console.WriteLine($"Uploaded image insights token: {visualSearchResults.Image.ImageInsightsToken}");
            }
            else
            {
                Console.WriteLine("Couldn't find image insights token!");
            }

            // List of tags
            if (visualSearchResults.Tags.Count > 0)
            {
                var firstTagResult = visualSearchResults.Tags.First();
                Console.WriteLine($"Visual search tag count: {visualSearchResults.Tags.Count}");

                // List of actions in first tag
                if (firstTagResult.Actions.Count > 0)
                {
                    var firstActionResult = firstTagResult.Actions.First();
                    Console.WriteLine($"First tag action count: {firstTagResult.Actions.Count}");
                    Console.WriteLine($"First tag action type: {firstActionResult.ActionType}");
                }
                else
                {
                    Console.WriteLine("Couldn't find tag actions!");
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
```

<a name="num-tags-actions"></a>

## <a name="number-of-tags-number-of-actions-and-first-actiontype"></a>A címkék és műveletek száma, valamint az első actionType

A következő kód egy kép URL-címét és egy körülvágási területet küld a knowledgeRequest paraméterben.  Ezután kinyomtatja az imageInsightsTokent, a címkék számát, a műveletek számát, valamint az első actionType elemet.

```csharp
public static void VisualSearchUrlWithJson(string subscriptionKey)
{
    var client = new VisualSearchAPI(new ApiKeyServiceClientCredentials(subscriptionKey));

    try
    {

        //The visual search request can be passed in as a JSON string
        //The image is specified via URL in the ImageInfo object, along with a crop area as shown below:
        
        //     "imageInfo": {
        //         "url": "https://images.unsplash.com/photo-1512546148165-e50d714a565a?w=600&q=80",
        //     "cropArea": {
        //           "top": 0.1,
        //           "bottom": 0.5,
        //           "left": 0.1,
        //           "right": 0.9
        //         }
        //     },
        //     "knowledgeRequest": {
        //        "filters": {
        //            "site": "www.bing.com"
        //        }              
        //     }

        var VisualSearchRequestJSON = "{\"imageInfo\":{\"url\":\"https://images.unsplash.com/photo-1512546148165-e50d714a565a?w=600&q=80\",\"cropArea\":{\"top\":0.1,\"bottom\":0.5,\"left\":0.1,\"right\":0.9}},\"knowledgeRequest\":{\"filters\":{\"site\":\"www.bing.com\"}}}";

        // An image binary is not necessary here, as the image is specified by URL in JSON text
        var visualSearchResults = client.Images.VisualSearchMethodAsync(knowledgeRequest: VisualSearchRequestJSON).Result;
        Console.WriteLine("\r\nVisual search request with image url specified by JSON text");

        if (visualSearchResults == null)
        {
            Console.WriteLine("No visual search result data.");
        }
        else
        {
            // Visual Search results
            if (visualSearchResults.Image?.ImageInsightsToken != null)
            {
                Console.WriteLine($"Uploaded image insights token: {visualSearchResults.Image.ImageInsightsToken}");
            }
            else
            {
                Console.WriteLine("Couldn't find image insights token!");
            }

            // List of tags
            if (visualSearchResults.Tags.Count > 0)
            {
                var firstTagResult = visualSearchResults.Tags.First();
                Console.WriteLine($"Visual search tag count: {visualSearchResults.Tags.Count}");

                // List of actions in first tag
                if (firstTagResult.Actions.Count > 0)
                {
                    var firstActionResult = firstTagResult.Actions.First();
                    Console.WriteLine($"First tag action count: {firstTagResult.Actions.Count}");
                    Console.WriteLine($"First tag action type: {firstActionResult.ActionType}");
                }
                else
                {
                    Console.WriteLine("Couldn't find tag actions!");
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
```

## <a name="next-steps"></a>További lépések

[Cognitive services .NET SDK-minták](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/BingSearchv7).
