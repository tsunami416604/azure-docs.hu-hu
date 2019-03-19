---
title: 'Gyors útmutató: Keresés a Bing Entity Search SDK rendelkező entitások esetébenC#'
titleSuffix: Azure Cognitive Services
description: Ez a rövid útmutató segítségével keresse meg a Bing Entity Search SDK rendelkező entitások C#.
services: cognitive-services
author: mikedodaro
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-entity-search
ms.topic: quickstart
ms.date: 02/01/2019
ms.author: v-gedod
ms.openlocfilehash: 99b7f05304b48b7d885a80705d05fbe24854150f
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2019
ms.locfileid: "58080641"
---
# <a name="send-a-search-request-with-the-bing-entity-search-sdk-for-c"></a>A Bing Entity Search SDK-keresési kérelem küldéseC#

Kezdje a keresést, a Bing Entity Search SDK rendelkező entitások esetében ez a rövid útmutató segítségével C#. Míg a Bing Entity Search REST API-val kompatibilis szinte bármelyik programozási nyelvével, az SDK biztosít egy egyszerű módja annak, hogy a szolgáltatás integrálása az alkalmazásokba. Ez a minta forráskódja találhatók [GitHub](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/BingSearchv7/BingEntitySearch).


## <a name="prerequisites"></a>Előfeltételek

* A [Visual Studio 2017](https://www.visualstudio.com/downloads/) bármely kiadása.
* A [Json.NET](https://www.newtonsoft.com/json) keretrendszer, amely NuGet-csomagként letölthető.
* Linux/MacOS rendszer esetében az alkalmazás a [Monóval](https://www.mono-project.com/) futtatható.
* A [Bing News Search SDK NuGet-csomagot](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Search.EntitySearch/1.2.0). Ez a csomag is telepíti a következő:
    * Microsoft.Rest.ClientRuntime
    * Microsoft.Rest.ClientRuntime.Azure
    * Newtonsoft.Json

A Visual Studio-projektben a Bing Entity Search SDK hozzáadásához használja a `Manage NuGet Packages` lehetőséget a Megoldáskezelőben, és adja hozzá a `Microsoft.Azure.CognitiveServices.Search.EntitySearch` csomagot.


[!INCLUDE [cognitive-services-bing-news-search-signup-requirements](../../../../includes/cognitive-services-bing-entity-search-signup-requirements.md)]

## <a name="create-and-initialize-an-application"></a>Hozzon létre, és a egy alkalmazás inicializálása

1. Hozzon létre egy új C# konzol megoldást a Visual Studióban. Ezután adja hozzá a következő, a fő kód fájlba.

    ```csharp
    using System;
    using System.Linq;
    using System.Text;
    using Microsoft.Azure.CognitiveServices.Search.EntitySearch;
    using Microsoft.Azure.CognitiveServices.Search.EntitySearch.Models;
    using Newtonsoft.Json;
    ```

## <a name="create-a-client-and-send-a-search-request"></a>Hozzon létre egy ügyfél és a egy keresési kérelmet küldeni

1. Hozzon létre egy új keresési ügyfél. Az előfizetési kulcs hozzáadása hozzon létre egy új `ApiKeyServiceClientCredentials`.

    ```csharp
    var client = new EntitySearchAPI(new ApiKeyServiceClientCredentials("YOUR-ACCESS-KEY"));
    ```

1. Az ügyfél használata `Entities.Search()` függvény használatával keresse meg a lekérdezés:
    
    ```csharp
    var entityData = client.Entities.Search(query: "Satya Nadella");
    ```

## <a name="get-and-print-an-entity-description"></a>GET, és nyomtassa ki az entitás leírása

1. Ha az API-t a keresési eredményeket ad vissza, a fő entitás az első `entityData`.

    ```csharp
    var mainEntity = entityData.Entities.Value.Where(thing => thing.EntityPresentationInfo.EntityScenario == EntityScenario.DominantEntity).FirstOrDefault();
    ```

2. Nyomtassa ki a fő entitás leírása 

    ```csharp
    Console.WriteLine(mainEntity.Description);
    ```

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Egyoldalas webalkalmazás létrehozása](../tutorial-bing-entities-search-single-page-app.md)

* [Mi az a Bing Entity Search API?](../overview.md )