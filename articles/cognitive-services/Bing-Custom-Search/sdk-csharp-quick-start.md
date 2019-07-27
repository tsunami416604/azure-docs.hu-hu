---
title: 'Gyors útmutató: A Bing Custom Search végpont meghívása C# az SDK használatával | Microsoft Docs'
titleSuffix: Azure Cognitive Services
description: Ezzel a rövid útmutatóval megkezdheti a Bing Custom Search példány keresési eredményeinek C# kérését az SDK használatával.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-custom-search
ms.topic: quickstart
ms.date: 07/26/2019
ms.author: scottwhi
ms.openlocfilehash: c7ac6d051c8333a6329a3c2ed238d78fb9da4a30
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/26/2019
ms.locfileid: "68565715"
---
# <a name="quickstart-call-your-bing-custom-search-endpoint-using-the-c-sdk"></a>Gyors útmutató: Bing Custom Search végpont meghívása az C# SDK használatával 

Ezzel a rövid útmutatóval megkezdheti a Bing Custom Search-példány keresési eredményeinek C# kérelmezését az SDK használatával. Habár a Bing Custom Search REST API kompatibilis a legtöbb programozási nyelvvel, a Bing Custom Search SDK egyszerű módszert kínál a szolgáltatás integrálására az alkalmazásokba. A minta forráskódja a [githubon](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/BingSearchv7/BingCustomWebSearch)található.

## <a name="prerequisites"></a>Előfeltételek

- Egy Bing Custom Search példány. Lásd [: gyors útmutató: További információért hozza létre az](quick-start.md) első Bing Custom Search-példányát.
- Microsoft [.net Core](https://www.microsoft.com/net/download/core)
- A [Visual Studio 2017-es vagy újabb](https://www.visualstudio.com/downloads/) verziójának bármely kiadása
- Linux/MacOS rendszer esetében az alkalmazás a [Monóval](https://www.mono-project.com/) futtatható.
- A [Bing Custom Search](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Search.CustomSearch/1.2.0) NuGet-csomag. 
    - **Megoldáskezelő** a Visual Studióban kattintson a jobb gombbal a projektre, és válassza a **NuGet-csomagok kezelése** menüpontot a menüből. Telepítse az `Microsoft.Azure.CognitiveServices.Search.CustomSearch` csomagot. A NuGet Custom Search csomag telepítésekor a következő szerelvények is települnek:
        - Microsoft.Rest.ClientRuntime
        - Microsoft.Rest.ClientRuntime.Azure
        - Newtonsoft.Json

[!INCLUDE [cognitive-services-bing-news-search-prerequisites](../../../includes/cognitive-services-bing-custom-search-signup-requirements.md)]


## <a name="create-and-initialize-the-application"></a>Az alkalmazás létrehozása és inicializálása

1. Hozzon létre C# egy új Console-alkalmazást a Visual Studióban. Ez után adja hozzá a projekthez az alábbi csomagokat.

    ```csharp
    using System;
    using System.Linq;
    using Microsoft.Azure.CognitiveServices.Search.CustomSearch;
    ```

2. Az alkalmazás fő metódusában hozza létre a keresési ügyfelet az API-kulccsal.

    ```csharp
    var client = new CustomSearchAPI(new ApiKeyServiceClientCredentials("YOUR-SUBSCRIPTION-KEY"));
    ```

## <a name="send-the-search-request-and-receive-a-response"></a>A keresési kérelem elküldése és válasz fogadása
    
1. Küldjön egy keresési lekérdezést az ügyfél `SearchAsync()` metódusának használatával, és mentse a választ. Ne felejtse el lecserélni `YOUR-CUSTOM-CONFIG-ID` a példánya konfigurációs azonosítóját (az azonosítót a [Bing Custom Search portálon](https://www.customsearch.ai/)találja). Ez a példa az "Xbox" kifejezést keresi.

    ```csharp
    // This will look up a single query (Xbox).
    var webData = client.CustomInstance.SearchAsync(query: "Xbox", customConfig: Int32.Parse("YOUR-CUSTOM-CONFIG-ID")).Result;
    ```

2. A `SearchAsync()` metódus egy `WebData` objektumot ad vissza. Az objektum használatával megismételheti `WebPages` a talált objektumokat. Ez a kód megtalálja az első weboldal eredményt, és megjeleníti a weboldal `Name` és `URL` tulajdonságát.

    ```csharp
    if (webData?.WebPages?.Value?.Count > 0)
    {
        // find the first web page
        var firstWebPagesResult = webData.WebPages.Value.FirstOrDefault();

        if (firstWebPagesResult != null)
        {
            Console.WriteLine("Number of webpage results {0}", webData.WebPages.Value.Count);
            Console.WriteLine("First web page name: {0} ", firstWebPagesResult.Name);
            Console.WriteLine("First web page URL: {0} ", firstWebPagesResult.Url);
        }
        else
        {
            Console.WriteLine("Couldn't find web results!");
        }
    }
    else
    {
        Console.WriteLine("Didn't see any Web data..");
    }
    ```csharp

## Next steps

> [!div class="nextstepaction"]
> [Build a Custom Search web app](./tutorials/custom-search-web-page.md)
