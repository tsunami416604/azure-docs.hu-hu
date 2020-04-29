---
title: Bing Entity Search C# ügyféloldali kódtár gyors üzembe helyezése
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 03/06/2020
ms.author: aahi
ms.openlocfilehash: 39a6c21ad056980e8c7b146e36a6e185cb3ed95e
ms.sourcegitcommit: 34a6fa5fc66b1cfdfbf8178ef5cdb151c97c721c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "79136780"
---
Ezzel a rövid útmutatóval megkezdheti az entitások keresését a C# Bing Entity Search ügyféloldali függvénytárával. Habár a Bing Entity Search REST API kompatibilis a legtöbb programozási nyelvvel, az ügyféloldali kódtár egyszerű módszert kínál a szolgáltatás integrálására az alkalmazásokba. A minta forráskódja a [githubon](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/BingSearchv7/BingEntitySearch)található.


## <a name="prerequisites"></a>Előfeltételek

* A [Visual Studio 2017 vagy újabb](https://www.visualstudio.com/downloads/)verziójának bármely kiadása.
* A [Json.NET](https://www.newtonsoft.com/json) keretrendszer, amely NuGet-csomagként letölthető.
* Linux/MacOS rendszer esetében az alkalmazás a [Monóval](https://www.mono-project.com/) futtatható.
* A [BING News Search SDK NuGet csomagja](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Search.EntitySearch/1.2.0). A csomag telepítése a következőket is telepíti:
    * Microsoft.Rest.ClientRuntime
    * Microsoft.Rest.ClientRuntime.Azure
    * Newtonsoft.Json

Ha hozzá szeretné adni a Bing Entity Search ügyféloldali kódtárat a Visual Studio-projekthez, használja a **NuGet-csomagok kezelése** lehetőséget a `Microsoft.Azure.CognitiveServices.Search.EntitySearch` **megoldáskezelő**, és adja hozzá a csomagot.


[!INCLUDE [cognitive-services-bing-news-search-signup-requirements](~/includes/cognitive-services-bing-entity-search-signup-requirements.md)]

## <a name="create-and-initialize-an-application"></a>Alkalmazás létrehozása és inicializálása

1. hozzon létre egy új C# konzolos megoldást a Visual Studióban. Ezután adja hozzá a következőt a fő kódhoz.

    ```csharp
    using System;
    using System.Linq;
    using System.Text;
    using Microsoft.Azure.CognitiveServices.Search.EntitySearch;
    using Microsoft.Azure.CognitiveServices.Search.EntitySearch.Models;
    using Newtonsoft.Json;
    ```

## <a name="create-a-client-and-send-a-search-request"></a>Ügyfél létrehozása és keresési kérelem küldése

1. Hozzon létre egy új keresési ügyfelet. Adja hozzá az előfizetési kulcsot új `ApiKeyServiceClientCredentials`létrehozásához.

    ```csharp
    var client = new EntitySearchClient(new ApiKeyServiceClientCredentials("YOUR-ACCESS-KEY"));
    ```

1. A lekérdezés megkereséséhez használja az ügyfél `Entities.Search()` függvényét:
    
    ```csharp
    var entityData = client.Entities.Search(query: "Satya Nadella");
    ```

## <a name="get-and-print-an-entity-description"></a>Entitás leírásának beolvasása és nyomtatása

1. Ha az API keresési eredményeket adott vissza, szerezze be a fő `entityData`entitást a következőből:.

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