---
title: Bing Image Search C# ügyféloldali kódtár gyors üzembe helyezése
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 10/21/2020
ms.author: aahi
ms.openlocfilehash: e051eca990ae0aa0b5a79c208a594e1b2332bcb2
ms.sourcegitcommit: d2d1c90ec5218b93abb80b8f3ed49dcf4327f7f4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/16/2020
ms.locfileid: "97612709"
---
Ezzel a rövid útmutatóval elvégezheti az első képkeresést az Bing Image Search ügyféloldali kódtár használatával. 

Az ügyfél keresési könyvtára a REST API burkolója, és ugyanazokat a szolgáltatásokat tartalmazza. 

Létre fog hozni egy C#-alkalmazást, amely egy képkeresési lekérdezést küld, elemzi a JSON-választ, és megjeleníti az első visszaadott rendszerkép URL-címét.

A minta forráskódja további hibakezeléssel és megjegyzésekkel együtt elérhető a [GitHubon](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/BingSearchv7/BingImageSearch).

## <a name="prerequisites"></a>Előfeltételek

* Ha Windows rendszert használ, a [Visual Studio 2017 vagy újabb](https://visualstudio.microsoft.com/vs/whatsnew/) kiadását
* Ha macOS vagy Linux rendszert használ, a [vs Code](https://code.visualstudio.com) -ot a [.net Core-ra telepítette](https://dotnet.microsoft.com/learn/dotnet/hello-world-tutorial/install)
* [Ingyenes Azure-előfizetés](https://azure.microsoft.com/free/dotnet)

[!INCLUDE [cognitive-services-bing-image-search-signup-requirements](~/includes/cognitive-services-bing-image-search-signup-requirements.md)]

Lásd még: [Cognitive Services díjszabása – BING Search API](https://azure.microsoft.com/pricing/details/cognitive-services/search-api/).

## <a name="create-a-console-project"></a>Konzol-projekt létrehozása

Először hozzon létre egy új C#-konzol alkalmazást.

# <a name="visual-studio"></a>[Visual Studio](#tab/visualstudio)

1. Hozzon létre egy *BingImageSearch* nevű új konzolos megoldást a Visual Studióban.
    
1. A [kognitív Image Search NuGet-csomag](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Search.ImageSearch) hozzáadása
    1. Kattintson a jobb gombbal a projektre **megoldáskezelő**.
    1. Válassza a **NuGet-csomagok kezelése** lehetőséget.
    1. Keresse meg és válassza ki a *Microsoft. Azure. CognitiveServices. Search. ImageSearch*, majd telepítse a csomagot.
    
# <a name="vs-code"></a>[VS Code](#tab/vscode)

1. Nyissa meg a terminál ablakot a VS Code-ban.
1. Hozzon létre egy *BingImageSearch* nevű új konzol-projektet a következő kód megadásával a terminál ablakban:
    
    ```bash
    dotnet new console -n BingImageSearch
    ```
1. Nyissa meg a *BingImageSearch* MAPPÁT a vs Code-ban.
1. Adja hozzá a [kognitív Image Search NuGet csomag](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Search.ImageSearch) NuGetPackage a következő kód beírásával a terminál ablakban:

    ```bash
    dotnet add package Microsoft.Azure.CognitiveServices.Search.ImageSearch
    ```

---

## <a name="initialize-the-application"></a>Az alkalmazás inicializálása


1. Cserélje le a `using` *program.cs* összes utasítását a következő kódra:

    ```csharp
    using System;
    using System.Linq;
    using Microsoft.Azure.CognitiveServices.Search.ImageSearch;
    using Microsoft.Azure.CognitiveServices.Search.ImageSearch.Models;
    ```

1. A `Main` projekt metódusában hozzon létre változókat az érvényes előfizetési kulcshoz, a Bing által visszaadott képet, valamint egy keresési kifejezést. Ezután példányosítsa a képkeresési ügyfelet a kulcs használatával.

    ```csharp
    static async Task Main(string[] args)
    {
        //IMPORTANT: replace this variable with your Cognitive Services subscription key
        string subscriptionKey = "ENTER YOUR KEY HERE";
        //stores the image results returned by Bing
        Images imageResults = null;
        // the image search term to be used in the query
        string searchTerm = "canadian rockies";
        
        //initialize the client
        //NOTE: If you're using version 1.2.0 or below for the Bing Image Search client library, 
        // use ImageSearchAPI() instead of ImageSearchClient() to initialize your search client.
        
        var client = new ImageSearchClient(new ApiKeyServiceClientCredentials(subscriptionKey));
    }
    ```
    
## <a name="send-a-search-query-using-the-client"></a>Keresési lekérdezés küldése az ügyfél használatával
    
Még mindig a `Main` metódusban használja az ügyfelet egy lekérdezési szöveg keresésére:
    
```csharp
// make the search request to the Bing Image API, and get the results"
imageResults = await client.Images.SearchAsync(query: searchTerm).Result; //search query
```

## <a name="parse-and-view-the-first-image-result"></a>Az első képkeresési eredmény elemzése és megtekintése

Elemezze a válaszban visszaadott képtalálatokat. 

Ha a válasz keresési eredményeket tartalmaz, tárolja az első eredményt, és nyomtassa ki néhány részletét.

```csharp
if (imageResults != null)
{
    //display the details for the first image result.
    var firstImageResult = imageResults.Value.First();
    Console.WriteLine($"\nTotal number of returned images: {imageResults.Value.Count}\n");
    Console.WriteLine($"Copy the following URLs to view these images on your browser.\n");
    Console.WriteLine($"URL to the first image:\n\n {firstImageResult.ContentUrl}\n");
    Console.WriteLine($"Thumbnail URL for the first image:\n\n {firstImageResult.ThumbnailUrl}");
    Console.WriteLine("Press any key to exit ...");
    Console.ReadKey();
}
```

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Egyoldalas alkalmazás-oktatóanyag a Bing Image Search használatához](../../tutorial-bing-image-search-single-page-app.md)

## <a name="see-also"></a>Lásd még

* [Mi a Bing Image Search?](../../overview.md)  
* [Online interaktív bemutató kipróbálása](https://azure.microsoft.com/services/cognitive-services/bing-image-search-api/)  
* [.NET-minták az Azure Cognitive Services SDK-hoz](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/BingSearchv7)
* [Az Azure Cognitive Services dokumentációja](../../../index.yml)
* [Bing Image Search API – referencia](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference)