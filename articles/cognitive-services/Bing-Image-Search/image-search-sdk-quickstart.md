---
title: 'Gyors útmutató: Keresse meg a képeket, a Bing kép Search SDK és a C# használatával'
description: Ez a rövid útmutató segítségével keresse meg és -rendszerképek keresése a weben a Bing kép Search SDK és a C# segítségével.
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: cgronlun
ms.service: cognitive-services
ms.component: bing-image-search
ms.topic: article
ms.date: 08/28/2018
ms.author: aahi
ms.openlocfilehash: 9e0decb29224b5ad684e1242b8f93e091e08e6b6
ms.sourcegitcommit: e2ea404126bdd990570b4417794d63367a417856
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/14/2018
ms.locfileid: "45579250"
---
# <a name="quickstart-search-for-images-with-the-bing-image-search-sdk-and-c"></a>Gyors útmutató: Keresse meg a Bing kép Search SDK és a C#-lemezképek

Ez a rövid útmutató segítségével győződjön meg arról, az első képkeresési Bing kép Search SDK használatával, amely egy burkoló a API-hoz, és ugyanazokat a szolgáltatásokat tartalmazza. Az egyszerű C# alkalmazás-lemezkép keresési lekérdezést küld, elemzi a JSON-válasz és URL-címét adja vissza az első képet jeleníti meg.

Az ehhez a mintához forráskódja elérhető [a Githubon](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/BingSearchv7/BingImageSearch) további hibakezelést és jegyzetek.

## <a name="prerequisites"></a>Előfeltételek

* Bármely kiadása [Visual Studio 2017](https://visualstudio.microsoft.com/vs/whatsnew/).
* A [Cognitive kép keresési NuGet-csomag](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Search.ImageSearch/1.2.0).

A visual studióban a kép a Bing Search SDK telepítéséhez használja a `Manage NuGet Packages` lehetőség a Visual Studio Megoldáskezelőjében.

[!INCLUDE [cognitive-services-bing-image-search-signup-requirements](../../../includes/cognitive-services-bing-image-search-signup-requirements.md)]


## <a name="create-and-initialize-the-application"></a>Hozzon létre, és az alkalmazás inicializálása

Először hozzon létre egy új C# konzolalkalmazást a Visual Studióban. Ezután adja hozzá a következő csomagokat a projekthez.

```csharp
using System;
using System.Linq;
using Microsoft.Azure.CognitiveServices.Search.ImageSearch;
using Microsoft.Azure.CognitiveServices.Search.ImageSearch.Models;
```

A projekt fő metódusban hozzon létre változókat az érvényes előfizetési kulcs, a kép a Bing és a egy keresési kifejezés által visszaadott eredményeket. Ezután hozza létre a lemezkép keresési ügyfél, a kulcs használatával.

```csharp
//IMPORTANT: replace this variable with your Cognitive Services subscription key
string subscriptionKey = "ENTER YOUR KEY HERE";
//stores the image results returned by Bing
Images imageResults = null;
// the image search term to be used in the query
string searchTerm = "canadian rockies";
//initialize the client
var client = new ImageSearchAPI(new ApiKeyServiceClientCredentials(subscriptionKey));
```

## <a name="send-a-search-query-using-the-client"></a>Az ügyfél keresési lekérdezés küldése

Az ügyfél használja a keresést a lekérdezés szövege:

```csharp
// make the search request to the Bing Image API, and get the results"
imageResults = client.Images.SearchAsync(query: searchTerm).Result; //search query
```

## <a name="parse-and-view-the-first-image-result"></a>Elemezheti és az első rendszerkép-eredmény megtekintése

A kép eredményeket, a válasz elemzése.
A válasz tartalmazza a keresési eredmények, ha az első eredmény tárolja, és nyomtassa ki a részleteket, például a Miniatűr URL-cím, az eredeti URL-CÍMÉT, teljes számával együtt adja vissza a lemezképeket.  

```csharp
if (imageResults != null)
{
    //display the details for the first image result.
    var firstImageResult = imageResults.Value.First();
    Console.WriteLine($"\nTotal number of returned images: {imageResults.Value.Count}\n");
    Console.WriteLine($"Copy the following URLs to view these images on your browser.\n");
    Console.WriteLine($"URL to the first image:\n\n {firstImageResult.ContentUrl}\n");
    Console.WriteLine($"Thumbnail URL for the first image:\n\n {firstImageResult.ThumbnailUrl}");
    Console.ReadKey();
}
```

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Bing – Képkeresés egyoldalas alkalmazás oktatóanyag](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/tutorial-bing-image-search-single-page-app)

## <a name="see-also"></a>Lásd még 

* [Mi az a Bing Képkeresés?](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/overview)  
* [Próbálja ki az online interaktív bemutatót](https://azure.microsoft.com/services/cognitive-services/bing-image-search-api/)  
* [Ingyenesen a Cognitive Services hozzáférési kulcs lekérése](https://azure.microsoft.com/try/cognitive-services/?api=bing-image-search-api)  
* [Az Azure Cognitive Services SDK for .NET-minták](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/BingSearchv7)
* [Az Azure Cognitive Services – dokumentáció](https://docs.microsoft.com/azure/cognitive-services)
* [A Bing Image Search API-referencia](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference)