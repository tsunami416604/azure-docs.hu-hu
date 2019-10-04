---
title: 'Gyors útmutató: Entitások keresése a Bing Entity Search SDK-valC#'
titleSuffix: Azure Cognitive Services
description: Ezzel a rövid útmutatóval megkeresheti az Bing Entity Search SDK- C#val rendelkező entitásokat.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-entity-search
ms.topic: quickstart
ms.date: 07/15/2019
ms.author: aahi
ms.openlocfilehash: 20f76c0adfcbd756c71769979214ea975cb5d6d9
ms.sourcegitcommit: 4b647be06d677151eb9db7dccc2bd7a8379e5871
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/19/2019
ms.locfileid: "68360587"
---
# <a name="send-a-search-request-with-the-bing-entity-search-sdk-for-c"></a>Keresési kérelem küldése a Bing Entity Search SDK-valC#

Ezzel a rövid útmutatóval megkezdheti az entitások keresését C#a Bing Entity Search SDK-val. Habár a Bing Entity Search REST API kompatibilis a legtöbb programozási nyelvvel, az SDK egyszerű módszert kínál a szolgáltatás integrálására az alkalmazásokba. A minta forráskódja a [githubon](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/BingSearchv7/BingEntitySearch)található.


## <a name="prerequisites"></a>Előfeltételek

* A [Visual Studio 2017 vagy újabb](https://www.visualstudio.com/downloads/)verziójának bármely kiadása.
* A [Json.NET](https://www.newtonsoft.com/json) keretrendszer, amely NuGet-csomagként letölthető.
* Linux/MacOS rendszer esetében az alkalmazás a [Monóval](https://www.mono-project.com/) futtatható.
* A [BING News Search SDK NuGet csomagja](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Search.EntitySearch/1.2.0). A csomag telepítése a következőket is telepíti:
    * Microsoft.Rest.ClientRuntime
    * Microsoft.Rest.ClientRuntime.Azure
    * Newtonsoft.Json

Ha hozzá szeretné adni a Bing Entity Search SDK-t a Visual Studio-projekthez, használja a **NuGet-csomagok kezelése** lehetőséget `Microsoft.Azure.CognitiveServices.Search.EntitySearch` a **megoldáskezelő**, és adja hozzá a csomagot.


[!INCLUDE [cognitive-services-bing-news-search-signup-requirements](../../../../includes/cognitive-services-bing-entity-search-signup-requirements.md)]

## <a name="create-and-initialize-an-application"></a>Alkalmazás létrehozása és inicializálása

1. hozzon létre C# egy új konzolos megoldást a Visual Studióban. Ezután adja hozzá a következőt a fő kódhoz.

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
    var client = new EntitySearchAPI(new ApiKeyServiceClientCredentials("YOUR-ACCESS-KEY"));
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
> [Egyoldalas webalkalmazás létrehozása](../tutorial-bing-entities-search-single-page-app.md)

* [Mi a Bing Entity Search API?](../overview.md )