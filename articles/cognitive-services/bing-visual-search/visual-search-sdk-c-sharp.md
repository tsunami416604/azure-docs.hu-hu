---
title: 'Gyors útmutató: képelemzések lekérése a C# -Bing Visual Search SDK-val'
titleSuffix: Azure Cognitive Services
description: Megtudhatja, hogyan tölthet fel képeket a Bing Visual Search SDK-val, és hogyan szerezhet be információkat ezzel a rövid útmutatóval.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-web-search
ms.topic: quickstart
ms.date: 12/17/2019
ms.author: aahi
ms.openlocfilehash: b1f5274bcae1f6e59f6dea94beee810a4613d739
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/25/2019
ms.locfileid: "75446611"
---
# <a name="quickstart-get-image-insights-using-the-bing-visual-search-sdk-for-c"></a>Gyors útmutató: képelemzések beolvasása a Bing Visual Search SDK használatávalC#

Ezzel a rövid útmutatóval megkezdheti a képelemzések beolvasását a Bing Visual Search C# szolgáltatásból az SDK használatával. Habár a Bing Visual Search REST API kompatibilis a legtöbb programozási nyelvvel, az SDK egyszerű módszert kínál a szolgáltatás integrálására az alkalmazásokba. A minta forráskódja a [githubon](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/BingSearchv7/BingVisualSearch)található.

## <a name="prerequisites"></a>Előfeltételek

* [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/).
* Linux/MacOS rendszer esetében az alkalmazás a [Monóval](https://www.mono-project.com/) futtatható.
* A NuGet Visual Search csomagot. 
    - A Visual Studio Megoldáskezelőjében kattintson a jobb gombbal a projektre, és válassza a `Manage NuGet Packages` lehetőséget a menüből. Telepítse az `Microsoft.Azure.CognitiveServices.Search.VisualSearch` csomagot. A NuGet-csomagok telepítése a következőket is telepíti:
        - Microsoft.Rest.ClientRuntime
        - Microsoft.Rest.ClientRuntime.Azure
        - Newtonsoft.Json


[!INCLUDE [cognitive-services-bing-visual-search-signup-requirements](../../../includes/cognitive-services-bing-visual-search-signup-requirements.md)]

<a name="client"></a>

## <a name="create-and-initialize-the-application"></a>Az alkalmazás létrehozása és inicializálása

1. Hozzon létre egy új projektet a Visual Studióban. Ezután adja hozzá a következő irányelveket.
    
    ```csharp
    using Microsoft.Azure.CognitiveServices.Search.VisualSearch;
    using Microsoft.Azure.CognitiveServices.Search.VisualSearch.Models;
    ```

2. Hozza létre az ügyfelet az előfizetési kulccsal.
    
    ```csharp
    var client = new VisualSearchClient(new ApiKeyServiceClientCredentials("YOUR-ACCESS-KEY"));
    ```
    
## <a name="send-a-search-request"></a>Keresési kérelem küldése 

1. Hozzon létre egy `FileStream` a lemezképekhez (ebben az esetben `TestImages/image.jpg`). Ezután használja az ügyfelet egy keresési kérelem küldésére `client.Images.VisualSearchMethodAsync()`használatával. 
    
    ```csharp
     System.IO.FileStream stream = new FileStream(Path.Combine("TestImages", "image.jpg"), FileMode.Open);
     // The knowledgeRequest parameter is not required if an image binary is passed in the request body
     var visualSearchResults = client.Images.VisualSearchMethodAsync(image: stream, knowledgeRequest: (string)null).Result;
    ```
    
2. Elemezze az előző lekérdezés eredményeit:

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
        var firstTagResult = visualSearchResults.Tags[0];
        Console.WriteLine($"Visual search tag count: {visualSearchResults.Tags.Count}");
    
        // List of actions in first tag
        if (firstTagResult.Actions.Count > 0)
        {
            var firstActionResult = firstTagResult.Actions[0];
            Console.WriteLine($"First tag action count: {firstTagResult.Actions.Count}");
            Console.WriteLine($"First tag action type: {firstActionResult.ActionType}");
        }
        else
        {
            Console.WriteLine("Couldn't find tag actions!");
        }
    }
    ```

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Egyoldalas Webalkalmazás létrehozása](tutorial-bing-visual-search-single-page-app.md)
