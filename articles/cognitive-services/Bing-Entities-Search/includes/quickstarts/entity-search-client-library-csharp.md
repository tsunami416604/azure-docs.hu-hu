---
title: Bing entitáskeresés C# ügyféltár – rövid útmutató
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 03/06/2020
ms.author: aahi
ms.openlocfilehash: 39a6c21ad056980e8c7b146e36a6e185cb3ed95e
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "79136780"
---
Ezzel a rövid útmutatóval megkezdheti az entitások keresését a Bing Entity Search ügyfélkódtárban a C#.Use this quickstart to begin searching for entities with the Bing Entity Search client library for C#. Bár a Bing Entity Search a legtöbb programozási nyelvvel kompatibilis REST API-val rendelkezik, az ügyfélkódtár egyszerű módot kínál a szolgáltatás alkalmazásokba való integrálására. A minta forráskódja megtalálható a [GitHubon.](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/BingSearchv7/BingEntitySearch)


## <a name="prerequisites"></a>Előfeltételek

* A [Visual Studio 2017-es vagy újabb verzióinak](https://www.visualstudio.com/downloads/)bármely kiadása.
* A [Json.NET](https://www.newtonsoft.com/json) keretrendszer, amely NuGet-csomagként letölthető.
* Linux/MacOS rendszer esetében az alkalmazás a [Monóval](https://www.mono-project.com/) futtatható.
* A [Bing News Keresés SDK NuGet csomag](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Search.EntitySearch/1.2.0). A csomag telepítése a következőket is telepíti:
    * Microsoft.Rest.ClientRuntime
    * Microsoft.Rest.ClientRuntime.Azure
    * Newtonsoft.Json

Ha hozzá szeretné adni a Bing Entity Search ügyfélkönyvtárat a Visual Studio-projekthez, `Microsoft.Azure.CognitiveServices.Search.EntitySearch` használja a **NuGet csomagok kezelése** lehetőséget a Solution **Explorer**ből , és adja hozzá a csomagot.


[!INCLUDE [cognitive-services-bing-news-search-signup-requirements](~/includes/cognitive-services-bing-entity-search-signup-requirements.md)]

## <a name="create-and-initialize-an-application"></a>Alkalmazás létrehozása és inicializálása

1. hozzon létre egy új C# konzolos megoldást a Visual Studióban. Ezután adja hozzá a következőket a fő kódfájlhoz.

    ```csharp
    using System;
    using System.Linq;
    using System.Text;
    using Microsoft.Azure.CognitiveServices.Search.EntitySearch;
    using Microsoft.Azure.CognitiveServices.Search.EntitySearch.Models;
    using Newtonsoft.Json;
    ```

## <a name="create-a-client-and-send-a-search-request"></a>Ügyfél létrehozása és keresési kérelem küldése

1. Hozzon létre egy új keresési ügyfelet. Adja hozzá az előfizetési `ApiKeyServiceClientCredentials`kulcsot egy új létrehozásához.

    ```csharp
    var client = new EntitySearchClient(new ApiKeyServiceClientCredentials("YOUR-ACCESS-KEY"));
    ```

1. Az ügyfél függvényének `Entities.Search()` használatával keresse meg a lekérdezést:
    
    ```csharp
    var entityData = client.Entities.Search(query: "Satya Nadella");
    ```

## <a name="get-and-print-an-entity-description"></a>Entitásleírás beszerezni és kinyomtatni

1. Ha az API keresési eredményeket adott `entityData`vissza, a fő entitást a rendszerből kapjuk meg.

    ```csharp
    var mainEntity = entityData.Entities.Value.Where(thing => thing.EntityPresentationInfo.EntityScenario == EntityScenario.DominantEntity).FirstOrDefault();
    ```

2. A fő entitás leírásának nyomtatása 

    ```csharp
    Console.WriteLine(mainEntity.Description);
    ```

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Egyoldalas webalkalmazás létrehozása](../../tutorial-bing-entities-search-single-page-app.md)

* [Mi a Bing Entity Search API?](../../overview.md )