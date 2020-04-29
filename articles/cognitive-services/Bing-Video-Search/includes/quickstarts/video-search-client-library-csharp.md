---
title: Bing Video Search C# ügyféloldali kódtár gyors üzembe helyezése
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 03/19/2020
ms.author: aahi
ms.openlocfilehash: d50e1acd104916d68f7fbb84ff568cf4efc0b46b
ms.sourcegitcommit: 34a6fa5fc66b1cfdfbf8178ef5cdb151c97c721c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "80289755"
---
Ezzel a rövid útmutatóval megkezdheti a C#-hoz készült Bing Video Search ügyféloldali kódtár híreinek keresését. Habár a Bing Video Search REST API kompatibilis a legtöbb programozási nyelvvel, az ügyféloldali kódtár egyszerű módszert kínál a szolgáltatás integrálására az alkalmazásokba. A minta forráskódja a [githubon](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/BingSearchv7/BingVideoSearch) található további megjegyzésekkel és szolgáltatásokkal.

## <a name="prerequisites"></a>Előfeltételek

* A [Visual Studio 2017 vagy újabb](https://visualstudio.microsoft.com/downloads/)verziójának bármely kiadása.
* A Json.NET keretrendszer, [amely NuGet-csomagként](https://www.nuget.org/packages/Newtonsoft.Json/)érhető el.

Ha hozzá szeretné adni a Bing Video Search ügyféloldali kódtárat a projekthez, válassza a **NuGet-csomagok kezelése** **megoldáskezelő** a Visual Studióban lehetőséget. Vegye fel a `Microsoft.Azure.CognitiveServices.Search.VideoSearch` csomagot.

A [[NuGet Video Search SDK-csomag]](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Search.VideoSearch/1.2.0) telepítése a következő függőségeket is telepíti:

* Microsoft.Rest.ClientRuntime
* Microsoft.Rest.ClientRuntime.Azure
* Newtonsoft.Json

[!INCLUDE [cognitive-services-bing-video-search-signup-requirements](~/includes/cognitive-services-bing-video-search-signup-requirements.md)]


## <a name="create-and-initialize-a-project"></a>Projekt létrehozása és inicializálása

1. Hozzon létre egy új C# konzolos megoldást a Visual Studióban. Ezután adja hozzá a következőt a fő kódhoz.

    ```csharp
    using System;
    using System.Collections.Generic;
    using System.Linq;
    using Microsoft.Azure.CognitiveServices.Search.VideoSearch;
    using Microsoft.Azure.CognitiveServices.Search.VideoSearch.Models;
    ```

2. Hozza létre az ügyfelet egy új `ApiKeyServiceClientCredentials` objektum létrehozásával az előfizetési kulccsal, és hívja meg a konstruktort.

    ```csharp
    var client = new VideoSearchAPI(new ApiKeyServiceClientCredentials("YOUR-ACCESS-KEY"));
    ```

## <a name="send-a-search-request-and-process-the-results"></a>Keresési kérelem küldése és az eredmények feldolgozása

1. Egy keresési kérelem küldéséhez használja az ügyfelet. A keresési lekérdezéshez használja a "SwiftKey" kifejezést.

    ```csharp
    var videoResults = client.Videos.SearchAsync(query: "SwiftKey").Result;
    ```

2. Ha bármilyen eredményt adott vissza, szerezze be az elsőt `videoResults.Value[0]`a következővel:. Ezután nyomtassa ki a videó AZONOSÍTÓját, címét és URL-címét.

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
> [Egyoldalas Webalkalmazás létrehozása](../../tutorial-bing-video-search-single-page-app.md)

## <a name="see-also"></a>Lásd még 

* [Mi az a Bing Video Search API?](../../overview.md)
* [A kognitív szolgáltatások .NET SDK-minták](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/BingSearchv7)
