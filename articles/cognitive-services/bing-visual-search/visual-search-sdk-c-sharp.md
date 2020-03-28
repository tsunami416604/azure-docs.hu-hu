---
title: 'Rövid útmutató: Képelemzési információk beszereznie az SDK c# számára – Bing visual search'
titleSuffix: Azure Cognitive Services
description: Ebből a rövid útmutatóból megtudhatja, hogy miként tölthet fel képet a Bing Visual Search SDK használatával, és hogyan kaphat róla betekintést.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-web-search
ms.topic: quickstart
ms.date: 12/17/2019
ms.author: aahi
ms.openlocfilehash: b1f5274bcae1f6e59f6dea94beee810a4613d739
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75446611"
---
# <a name="quickstart-get-image-insights-using-the-bing-visual-search-sdk-for-c"></a>Rövid útmutató: Képelemzési adatok beszereznie a Bing Visual Search SDK for C használatával #

Ezzel a rövid útmutatóval megkezdheti a rendszerkép-elemzéseket a Bing Visual Search szolgáltatásból a C# SDK használatával. Bár a Bing Visual Search a legtöbb programozási nyelvvel kompatibilis REST API-val rendelkezik, az SDK segítségével egyszerűen integrálhatja a szolgáltatást az alkalmazásokba. A minta forráskódja megtalálható a [GitHubon.](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/BingSearchv7/BingVisualSearch)

## <a name="prerequisites"></a>Előfeltételek

* [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/).
* Linux/MacOS rendszer esetében az alkalmazás a [Monóval](https://www.mono-project.com/) futtatható.
* A NuGet Vizuális Keresés csomag. 
    - A Visual Studio Megoldáskezelőjében kattintson a jobb gombbal a projektre, és válassza a `Manage NuGet Packages` lehetőséget a menüből. Telepítse az `Microsoft.Azure.CognitiveServices.Search.VisualSearch` csomagot. A NuGet csomagok telepítése a következőket is telepíti:
        - Microsoft.Rest.ClientRuntime
        - Microsoft.Rest.ClientRuntime.Azure
        - Newtonsoft.Json


[!INCLUDE [cognitive-services-bing-visual-search-signup-requirements](../../../includes/cognitive-services-bing-visual-search-signup-requirements.md)]

<a name="client"></a>

## <a name="create-and-initialize-the-application"></a>Az alkalmazás létrehozása és inicializálása

1. A Visual Studióban hozzon létre egy új projektet. Ezután adja hozzá a következő irányelveket.
    
    ```csharp
    using Microsoft.Azure.CognitiveServices.Search.VisualSearch;
    using Microsoft.Azure.CognitiveServices.Search.VisualSearch.Models;
    ```

2. Az ügyfél példányosítsa az előfizetési kulccsal.
    
    ```csharp
    var client = new VisualSearchClient(new ApiKeyServiceClientCredentials("YOUR-ACCESS-KEY"));
    ```
    
## <a name="send-a-search-request"></a>Keresési kérelem küldése 

1. Hozzon `FileStream` létre egy a `TestImages/image.jpg`képeket (ebben az esetben). Ezután az ügyfél segítségével keresési `client.Images.VisualSearchMethodAsync()`kérelmet küldhet a használatával. 
    
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

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Egyoldalas webalkalmazás készítése](tutorial-bing-visual-search-single-page-app.md)
