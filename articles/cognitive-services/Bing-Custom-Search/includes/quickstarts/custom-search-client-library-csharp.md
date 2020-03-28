---
title: Bing egyéni keresés C# ügyfélkönyvtárának rövid útmutatója
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 02/27/2020
ms.author: aahi
ms.openlocfilehash: ba80d1396b30b61bdfe4c121220429f5a7d994b0
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "79486058"
---
Ismerkedés a C#-hoz tartozó Bing egyéni keresés ügyféltárával. Az alábbi lépésekkel telepítheti a csomagot, és kipróbálhatja az alapvető feladatok példakódját. A Bing egyéni keresési API lehetővé teszi, hogy személyre szabott, hirdetésmentes keresési élményeket hozzon létre az Ön számára fontos témakörökhöz. A minta forráskódja megtalálható a [GitHubon.](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/BingSearchv7/BingCustomWebSearch)

A C# bing egyéni keresési ügyféltár használatával:
* Keresse meg a keresési eredményeket az interneten a Bing egyéni keresés példányából.

[Referenciadokumentációkönyvtár](https://docs.microsoft.com/dotnet/api/overview/azure/cognitiveservices/client/bingcustomsearch?view=azure-dotnet) | [forráskódjának](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/Search.BingCustomSearch) | [csomagja (NuGet)](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Search.CustomSearch/1.2.0) | [mintái](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples)


## <a name="prerequisites"></a>Előfeltételek

- Bing egyéni keresési példánya. További információ: További információ [az első Bing egyéni keresési példány létrehozása](../../quick-start.md) című témakörben.
- Microsoft [.NET Core](https://www.microsoft.com/net/download/core)
- A [Visual Studio 2017-es vagy újabb verzióinak](https://www.visualstudio.com/downloads/) bármely kiadása
- Linux/MacOS rendszer esetében az alkalmazás a [Monóval](https://www.mono-project.com/) futtatható.
- A [Bing egyéni keresés](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Search.CustomSearch/1.2.0) Nuget csomag. 
    - A Visual Studio **Megoldáskezelőjében** kattintson a jobb gombbal a projektre, és válassza a menü **NuGet csomagok kezelése parancsát.** Telepítse az `Microsoft.Azure.CognitiveServices.Search.CustomSearch` csomagot. A NuGet Custom Search csomag telepítésekor a következő szerelvények is települnek:
        - Microsoft.Rest.ClientRuntime
        - Microsoft.Rest.ClientRuntime.Azure
        - Newtonsoft.Json

[!INCLUDE [cognitive-services-bing-custom-search-prerequisites](~/includes/cognitive-services-bing-custom-search-signup-requirements.md)]


## <a name="create-and-initialize-the-application"></a>Az alkalmazás létrehozása és inicializálása

1. Hozzon létre egy új C# konzolalkalmazást a Visual Studióban. Ez után adja hozzá a projekthez az alábbi csomagokat.

    ```csharp
    using System;
    using System.Linq;
    using Microsoft.Azure.CognitiveServices.Search.CustomSearch;
    ```

2. Az alkalmazás fő módszerében példányosítsa meg a keresési ügyfelet az API-kulccsal.

    ```csharp
    var client = new CustomSearchAPI(new ApiKeyServiceClientCredentials("YOUR-SUBSCRIPTION-KEY"));
    ```

## <a name="send-the-search-request-and-receive-a-response"></a>A keresési kérelem elküldése és válasz fogadása
    
1. Keresési lekérdezés küldése az ügyfél `SearchAsync()` metódusával, és mentse a választ. Ügyeljen arra, `YOUR-CUSTOM-CONFIG-ID` hogy cserélje ki a példány konfigurációs azonosítóját (az azonosítót a [Bing egyéni keresési portálon](https://www.customsearch.ai/)találja). Ez a példa az "Xbox" kifejezésre keres.

    ```csharp
    // This will look up a single query (Xbox).
    var webData = client.CustomInstance.SearchAsync(query: "Xbox", customConfig: Int32.Parse("YOUR-CUSTOM-CONFIG-ID")).Result;
    ```

2. A `SearchAsync()` metódus egy `WebData` objektumot ad vissza. Használja az objektumot a `WebPages` talált objektumok on-átitatására. Ez a kód megtalálja az első weboldal eredményt, és megjeleníti a weboldal `Name` és `URL` tulajdonságát.

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
    ```

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Egyéni keresési webalkalmazás létrehozása](../../tutorials/custom-search-web-page.md)
