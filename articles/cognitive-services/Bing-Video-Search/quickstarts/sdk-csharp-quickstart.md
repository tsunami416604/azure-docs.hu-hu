---
title: 'Gyors útmutató: videók keresése a C# -Bing Video Search SDK használatával'
titleSuffix: Azure Cognitive Services
description: Ezzel a rövid útmutatóval videó-keresési kéréseket küldhet a Bing Video Search C#SDK-val.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-video-search
ms.topic: quickstart
ms.date: 06/26/2019
ms.author: aahi
ms.openlocfilehash: d29aef614b8308dfeba8da7925bd2880c25fe72d
ms.sourcegitcommit: f523c8a8557ade6c4db6be12d7a01e535ff32f32
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/22/2019
ms.locfileid: "74383760"
---
# <a name="quickstart-perform-a-video-search-with-the-bing-video-search-sdk-for-c"></a>Gyors útmutató: videós keresés végrehajtása a Bing Video Search SDK-valC#

Ezzel a rövid útmutatóval megkezdheti a Bing Video Search SDK-val C#kapcsolatos hírek keresését. Habár a Bing Video Search REST API kompatibilis a legtöbb programozási nyelvvel, az SDK egyszerű módszert kínál a szolgáltatás integrálására az alkalmazásokba. A minta forráskódja a [githubon](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/BingSearchv7/BingVideoSearch) található további megjegyzésekkel és szolgáltatásokkal.

## <a name="prerequisites"></a>Előfeltételek

* A [Visual Studio 2017 vagy újabb](https://visualstudio.microsoft.com/downloads/)verziójának bármely kiadása.
* A Json.NET keretrendszer, [amely NuGet-csomagként](https://www.nuget.org/packages/Newtonsoft.Json/)érhető el.

Ha hozzá szeretné adni a Bing Video Search SDK-t a projekthez, válassza a **NuGet-csomagok kezelése** **megoldáskezelő** a Visual Studióban lehetőséget. Vegye fel a `Microsoft.Azure.CognitiveServices.Search.VideoSearch` csomagot.

A [[NuGet Video Search SDK-csomag]](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Search.VideoSearch/1.2.0) telepítése a következő függőségeket is telepíti:

* Microsoft.Rest.ClientRuntime
* Microsoft.Rest.ClientRuntime.Azure
* Newtonsoft.Json

[!INCLUDE [cognitive-services-bing-video-search-signup-requirements](../../../../includes/cognitive-services-bing-video-search-signup-requirements.md)]


## <a name="create-and-initialize-a-project"></a>Projekt létrehozása és inicializálása

1. hozzon létre C# egy új konzolos megoldást a Visual Studióban. Ezután adja hozzá a következőt a fő kódhoz.

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

2. Ha bármilyen eredményt adott vissza, szerezze be az elsőt `videoResults.Value[0]`. Ezután nyomtassa ki a videó AZONOSÍTÓját, címét és URL-címét.

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

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Egyoldalas Webalkalmazás létrehozása](../tutorial-bing-video-search-single-page-app.md)

## <a name="see-also"></a>Lásd még 

* [Mi a Bing Video Search API?](../overview.md)
* [Cognitive services .NET SDK-minták](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/BingSearchv7)
