---
title: Az Azure .NET segédmetódus tartalom moderátor SDK |} Microsoft Docs
description: Hogyan állítható vissza a tartalom moderátor ügyfél Azure tartalom moderátor SDK for .NET használatával
services: cognitive-services
author: sanjeev3
manager: mikemcca
ms.service: cognitive-services
ms.component: content-moderator
ms.topic: article
ms.date: 01/04/2018
ms.author: sajagtap
ms.openlocfilehash: 36f2124708731f78f34849d8210ed39ea8f59140
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/23/2018
ms.locfileid: "35346930"
---
# <a name="helper-code-to-return-a-content-moderator-client"></a>Térjen vissza a tartalom moderátor ügyfél segítő kódot

Ez a cikk bemutatja, és mintakódok segítséget nyújtanak az előfizetéshez tartozó tartalom moderátor ügyfelet létrehozni a tartalom moderátor SDK for .NET használatának megkezdése.

A könyvtár használják más quickstarts ebben a szakaszban.

Ez a cikk feltételezi, hogy Ön már ismeri a Visual Studio és a C#.

> [!IMPORTANT]
> Ez az osztály könyvtár csak bemutató célokra szánt kódot tartalmaz.
> Igazítani ezt a kódot, éles használatra, ha módszert egy biztonságos tárolás és a tartalom moderátor előfizetés kulcs használatával.

## <a name="sign-up-for-content-moderator-services"></a>Iratkozzon fel a tartalom moderátor szolgáltatások

Tartalom moderátor-szolgáltatások díjairól a REST API-t vagy az SDK használata előtt be kell egy előfizetési kulcsot.
Tekintse meg a [gyors üzembe helyezés](quick-start.md) megtudhatja, hogyan szerezhet a kulcsot.

## <a name="create-your-visual-studio-project"></a>A Visual Studio-projekt létrehozása

1. Hozzon létre egy új **Class Library (.NET-keretrendszer)** projekt.

   A mintakód I a projekt neve **ModeratorHelper**.

1. Adjon hozzá egy hivatkozást, a **System.Configuration** keretrendszer szerelvényében.

### <a name="install-required-packages"></a>Szükséges csomagok telepítése

A következő NuGet-csomagok telepítése:

- Microsoft.Azure.CognitiveServices.ContentModerator
- Microsoft.Rest.ClientRuntime
- Newtonsoft.Json

### <a name="create-the-content-moderator-client"></a>A tartalom moderátor ügyfél létrehozása

Cserélje le a ModeratorHelper.cs fájl tartalmát az alábbira:

    using Microsoft.CognitiveServices.ContentModerator;

    namespace ModeratorHelper
    {
    /// <summary>
    /// Wraps the creation and configuration of a Content Moderator client.
    /// </summary>
    /// <remarks>This class library contains insecure code. If you adapt this 
    /// code for use in production, use a secure method of storing and using
    /// your Content Moderator subscription key.</remarks>
    public static class Clients
    {
        /// <summary>
        /// The region/location for your Content Moderator account, 
        /// for example, westus.
        /// </summary>
        private static readonly string AzureRegion = "myRegion";

        /// <summary>
        /// The base URL fragment for Content Moderator calls.
        /// </summary>
        private static readonly string AzureBaseURL =
            $"{AzureRegion}.api.cognitive.microsoft.com";

        /// <summary>
        /// Your Content Moderator subscription key.
        /// </summary>
        private static readonly string CMSubscriptionKey = "myKey";

        /// <summary>
        /// Returns a new Content Moderator client for your subscription.
        /// </summary>
        /// <returns>The new client.</returns>
        /// <remarks>The <see cref="ContentModeratorClient"/> is disposable.
        /// When you have finished using the client,
        /// you should dispose of it either directly or indirectly. </remarks>
        public static ContentModeratorClient NewClient()
        {
            // Create and initialize an instance of the Content Moderator API wrapper.
            ContentModeratorClient client = new ContentModeratorClient(new ApiKeyServiceClientCredentials(CMSubscriptionKey));

            client.BaseUrl = AzureBaseURL;
            return client;
        }
    }
    }


> [!IMPORTANT]
> Frissítés a **AzureRegion** és **CMSubscriptionKey** mezők a régió azonosítója és az Előfizetés kulcs értékeivel.

Most már rendelkezik egy gyors módja, ha az előfizetés tartalom moderátor ügyfelet létrehozni.

## <a name="next-steps"></a>További lépések

[Töltse le a Visual Studio megoldás](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/ContentModerator) ennél és egyéb tartalom moderátor quickstarts a .NET-hez, és az integráció a kezdéshez.
