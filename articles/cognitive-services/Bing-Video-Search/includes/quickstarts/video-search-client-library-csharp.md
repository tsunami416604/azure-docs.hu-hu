---
title: Bing Video Search C# ügyfélkönyvtár – rövid útmutató
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 03/19/2020
ms.author: aahi
ms.openlocfilehash: d50e1acd104916d68f7fbb84ff568cf4efc0b46b
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "80289755"
---
Ezzel a rövid útmutatóval megkezdheti a hírek keresését a Bing Video Search ügyfélkódtárban a C# számára. Bár a Bing Video Search a legtöbb programozási nyelvvel kompatibilis REST API-val rendelkezik, az ügyfélkódtár egyszerű módot kínál a szolgáltatás alkalmazásokba való integrálására. A minta forráskódja a [GitHubon](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/BingSearchv7/BingVideoSearch) található további jegyzetekkel és funkciókkal.

## <a name="prerequisites"></a>Előfeltételek

* A [Visual Studio 2017-es vagy újabb verzióinak](https://visualstudio.microsoft.com/downloads/)bármely kiadása.
* A Json.NET keretrendszer, amely [NuGet csomagként](https://www.nuget.org/packages/Newtonsoft.Json/)érhető el.

Ha hozzá szeretné adni a Bing Video Search ügyfélkönyvtárat a projekthez, válassza **a NuGet-csomagok kezelése a** Visual **Studióban a Solution Explorer** ből lehetőséget. Vegye fel a `Microsoft.Azure.CognitiveServices.Search.VideoSearch` csomagot.

A [[NuGet Video Search SDK csomag]](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Search.VideoSearch/1.2.0) telepítése a következő függőségeket is telepíti:

* Microsoft.Rest.ClientRuntime
* Microsoft.Rest.ClientRuntime.Azure
* Newtonsoft.Json

[!INCLUDE [cognitive-services-bing-video-search-signup-requirements](~/includes/cognitive-services-bing-video-search-signup-requirements.md)]


## <a name="create-and-initialize-a-project"></a>Projekt létrehozása és inicializálása

1. Hozzon létre egy új C# konzolos megoldást a Visual Studióban. Ezután adja hozzá a következőket a fő kódfájlhoz.

    ```csharp
    using System;
    using System.Collections.Generic;
    using System.Linq;
    using Microsoft.Azure.CognitiveServices.Search.VideoSearch;
    using Microsoft.Azure.CognitiveServices.Search.VideoSearch.Models;
    ```

2. Az ügyfél létrehozása az előfizetési `ApiKeyServiceClientCredentials` kulccsal létrehozott új objektum létrehozásával és a konstruktor hívásával.

    ```csharp
    var client = new VideoSearchAPI(new ApiKeyServiceClientCredentials("YOUR-ACCESS-KEY"));
    ```

## <a name="send-a-search-request-and-process-the-results"></a>Keresési kérelem küldése és az eredmények feldolgozása

1. Az ügyfél segítségével küldjön keresési kérelmet. Használja a "SwiftKey" a keresési lekérdezést.

    ```csharp
    var videoResults = client.Videos.SearchAsync(query: "SwiftKey").Result;
    ```

2. Ha bármilyen eredményt adott vissza, `videoResults.Value[0]`kap az első a . Ezután nyomtassa ki a videó azonosítóját, címét és url-jét.

    ```csharp
    if (videoResults.Value.Count > 0)
    {
        var firstVideoResult = videoResults.Value[0];

        Console.WriteLine($"\r\nVideo result count: {videoResults.Value.Count}");
        Console.WriteLine($"First video id: {firstVideoResult.VideoId}");
        Console.WriteLine($"First video name: {firstVideoResult.Name}");
        Console.WriteLine($"First video url: {firstVideoResult.ContentUrl}");
    }
    else
    {
        Console.WriteLine("Couldn't find video results!");
    }
    ```

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Egyoldalas webalkalmazás létrehozása](../../tutorial-bing-video-search-single-page-app.md)

## <a name="see-also"></a>Lásd még 

* [Mi az a Bing Video Search API?](../../overview.md)
* [Kognitív szolgáltatások .NET SDK-minták](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/BingSearchv7)
