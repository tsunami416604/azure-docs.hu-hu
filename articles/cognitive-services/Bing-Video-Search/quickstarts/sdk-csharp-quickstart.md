---
title: 'Gyors útmutató: Keressen videókat a Bing Video Search SDK használatávalC#'
titleSuffix: Azure Cognitive Services
description: Ez a rövid útmutató segítségével a Bing Video Search SDK használatával videókeresési-kérelmeket küldjön C#.
services: cognitive-services
author: mikedodaro
manager: cgronlun
ms.service: cognitive-services
ms.subservice: bing-video-search
ms.topic: quickstart
ms.date: 01/31/2019
ms.author: rosh
ms.openlocfilehash: 820c6a57a4a8266b68c5a245c2c2cf316aa071a9
ms.sourcegitcommit: ba035bfe9fab85dd1e6134a98af1ad7cf6891033
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/01/2019
ms.locfileid: "55569496"
---
# <a name="quickstart-perform-a-video-search-with-the-bing-video-search-sdk-for-c"></a>Gyors útmutató: Hajtsa végre a videókeresési Bing Video Search készült SDK-valC#

Ez a rövid útmutató segítségével kezdeni a keresést hírkeresés a Bing Video Search SDK- C#. Míg a Bing Video Search REST API-val kompatibilis szinte bármelyik programozási nyelvével, az SDK biztosít egy egyszerű módja annak, hogy a szolgáltatás integrálása az alkalmazásokba. Ez a minta forráskódja találhatók [GitHub](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/BingSearchv7/BingVideoSearch). További jegyzetek és szolgáltatásokat tartalmazza.

## <a name="prerequisites"></a>Előfeltételek

* A [Visual Studio 2017](https://visualstudio.microsoft.com/downloads/) bármely kiadása.
* A Json.NET keretrendszert, a rendelkezésre álló [NuGet-csomagként](https://www.nuget.org/packages/Newtonsoft.Json/).

A Bing Video Search SDK hozzáadása a projekthez, keresse meg a `Manage NuGet Packages` lehetőség a Visual Studio Megoldáskezelőjében.  Vegye fel a `Microsoft.Azure.CognitiveServices.Search.VideoSearch` csomagot.

Telepíti a [[NuGet Video Search SDK csomagot]](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Search.VideoSearch/1.2.0) is telepíti a következő függőségeket:

* Microsoft.Rest.ClientRuntime
* Microsoft.Rest.ClientRuntime.Azure
* Newtonsoft.Json

[!INCLUDE [cognitive-services-bing-video-search-signup-requirements](../../../../includes/cognitive-services-bing-video-search-signup-requirements.md)]


## <a name="create-and-initialize-a-project"></a>Projekt létrehozása és inicializálása

1. Hozzon létre egy új C# konzol megoldást a Visual Studióban. Ezután adja hozzá a következő, a fő kód fájlba.

    ```csharp
    using System;
    using System.Collections.Generic;
    using System.Linq;
    using Microsoft.Azure.CognitiveServices.Search.VideoSearch;
    using Microsoft.Azure.CognitiveServices.Search.VideoSearch.Models;
    ```

2. Hozza létre az ügyfél hozzon létre egy új `ApiKeyServiceClientCredentials` az előfizetési kulccsal végzett objektumot, és konstruktorának hívása.

    ```csharp
    var client = new VideoSearchAPI(new ApiKeyServiceClientCredentials("YOUR-ACCESS-KEY"));
    ```

## <a name="send-a-search-request-and-process-the-results"></a>Egy keresési kérelmet küld, és az eredmények feldolgozásához

1. Az ügyfél segítségével egy keresési kérelmet küld. A keresési lekérdezés "SwiftKey" használja.

    ```csharp
    var videoResults = client.Videos.SearchAsync(query: "SwiftKey").Result;
    ```

2. Eredményt adott vissza, ha lekérése az elsőt `videoResults.Value[0]`. Ezután nyomtassa ki a videó azonosítója, a jogcímre és az URL-címét.

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
> [Hozzon létre egy egyoldalas webalkalmazást](../tutorial-bing-video-search-single-page-app.md)

## <a name="see-also"></a>Lásd még 

* [Mi az a Bing Videókeresési API?](../overview.md)
* [Cognitive services .NET SDK-minták](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/BingSearchv7)
