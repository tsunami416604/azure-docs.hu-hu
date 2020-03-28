---
title: Bing News Search C# ügyfélkönyvtár – rövid útmutató
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 03/12/2020
ms.author: aahi
ms.openlocfilehash: fd43fcb1b5fb70862ed1c1fa5111f0893495b437
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "79503887"
---
Ezzel a rövid útmutatóval megkezdheti a hírek keresését a Bing News Search ügyfélkönyvtárában a C# számára. Bár a Bing News Search a legtöbb programozási nyelvvel kompatibilis REST API-val rendelkezik, az ügyfélkódtár egyszerű módot kínál a szolgáltatás alkalmazásokba való integrálására. A minta forráskódja megtalálható a [GitHubon.](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/BingSearchv7/BingNewsSearch)

## <a name="prerequisites"></a>Előfeltételek

* A [Visual Studio 2017-es vagy újabb verzióinak](https://www.visualstudio.com/downloads/)bármely kiadása.
* A [Json.NET](https://www.newtonsoft.com/json) keretrendszer, amely NuGet-csomagként letölthető.
* Linux/MacOS rendszer esetében az alkalmazás a [Monóval](https://www.mono-project.com/) futtatható.

* A [Bing News Keresés SDK NuGet csomag](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Search.NewsSearch/1.2.0). A csomag telepítése a következőket is telepíti:
    * Microsoft.Rest.ClientRuntime
    * Microsoft.Rest.ClientRuntime.Azure
    * Newtonsoft.Json

Ha a Bing News Search ügyféltárhasználatával szeretne beállítani `Manage NuGet Packages` egy konzolalkalmazást, keresse meg a Visual Studio Megoldáskezelőszolgáltatásának beállítását.  Vegye fel a `Microsoft.Azure.CognitiveServices.Search.NewsSearch` csomagot.

[!INCLUDE [cognitive-services-bing-news-search-signup-requirements](~/includes/cognitive-services-bing-news-search-signup-requirements.md)]

## <a name="create-and-initialize-a-project"></a>Projekt létrehozása és inicializálása

1. Hozzon létre egy új C# konzolos megoldást a Visual Studióban. Ezután adja hozzá a következőket a fő kódfájlhoz.
    
    ```csharp
    using System;
    using System.Linq;
    using Microsoft.Azure.CognitiveServices.Search.NewsSearch;
    ```

2. Hozzon létre egy változót az API-kulcshoz, egy keresési kifejezést, majd hozza létre vele a hírkereső ügyfelet.

    ```csharp
    var key = "YOUR-ACCESS-KEY";
    var searchTerm = "Quantum Computing";
    var client = new NewsSearchClient(new ApiKeyServiceClientCredentials(key));
    ```

## <a name="send-a-request-and-parse-the-result"></a>Kérelem küldése és az eredmény elemzése

1. Az ügyfél segítségével keresési kérelmet küldhet a Bing News Search szolgáltatásnak:
    ```csharp
    var newsResults = client.News.SearchAsync(query: searchTerm, market: "en-us", count: 10).Result;
    ```

2. Ha bármilyen eredményt adott vissza, elemezd őket:

    ```csharp
    if (newsResults.Value.Count > 0)
    {
        var firstNewsResult = newsResults.Value[0];
    
        Console.WriteLine($"TotalEstimatedMatches value: {newsResults.TotalEstimatedMatches}");
        Console.WriteLine($"News result count: {newsResults.Value.Count}");
        Console.WriteLine($"First news name: {firstNewsResult.Name}");
        Console.WriteLine($"First news url: {firstNewsResult.Url}");
        Console.WriteLine($"First news description: {firstNewsResult.Description}");
        Console.WriteLine($"First news published time: {firstNewsResult.DatePublished}");
        Console.WriteLine($"First news provider: {firstNewsResult.Provider[0].Name}");
    }
    
    else
    {
        Console.WriteLine("Couldn't find news results!");
    }
    Console.WriteLine("Enter any key to exit...");
    Console.ReadKey();
    ```

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Egyoldalas webalkalmazás létrehozása](../../tutorial-bing-news-search-single-page-app.md)
