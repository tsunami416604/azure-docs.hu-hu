---
title: 'Gyors útmutató: A .NET-keretrendszerhez – a Content Moderator moderálás ügyfél létrehozása'
titlesuffix: Azure Cognitive Services
description: Content Moderator-ügyfél visszaadása Azure Content Moderator SDK for .NET használatával
services: cognitive-services
author: sanjeev3
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: quickstart
ms.date: 01/10/2019
ms.author: sajagtap
ms.openlocfilehash: 94a16d03e47a9bec29e5e1c4326beab376dd33dd
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/07/2019
ms.locfileid: "55864400"
---
# <a name="quickstart-helper-code-to-return-a-content-moderator-client"></a>Gyors útmutató: A Content Moderator ügyfél visszaadandó segédkódot

Ez a cikk ahhoz biztosít információt és kódmintákat, hogy létrehozhasson egy Content Moderator-ügyfelet az előfizetéséhez a Content Moderator SDK for .NET segítségével.

Ezt a kódtárat a jelen szakasz egyéb rövid útmutatói is használják.

Ez a cikk feltételezi, hogy már ismeri a Visual Studiót és a C# nyelvet.

> [!IMPORTANT]
> A jelen osztálytárban lévő kódok csak bemutatási célra szolgálnak.
> Ha ezt a kódot éles környezetben szeretné alkalmazni, használjon biztonságos módszert a Content Moderator előfizetői azonosítójának tárolásához és használatához.

## <a name="sign-up-for-content-moderator-services"></a>Regisztráció a Content Moderator szolgáltatásaiba

Ahhoz, hogy a REST API-n vagy az SDK-n keresztül használhassa a Content Moderator szolgáltatásait, előbb be kell szereznie egy előfizetői azonosítót.
Tekintse meg a [próbálja ki a Content Moderator, a weben](quick-start.md) gyors útmutató: ismerje meg, hogyan szerezheti be a kulcsot.

## <a name="create-your-visual-studio-project"></a>Visual Studio-projekt létrehozása

1. Hozzon létre egy új **Class Library (.NET Framework)** projektet.

   A mintakódban a projektnek a **ModeratorHelper** nevet adtam.

1. Adjon hozzá egy, a **System.Configuration** keretrendszer-szerelvényre mutató hivatkozást.

### <a name="install-required-packages"></a>Szükséges csomagok telepítése

Telepítse az alábbi NuGet-csomagokat:

- Microsoft.Azure.CognitiveServices.ContentModerator
- Microsoft.Rest.ClientRuntime
- Newtonsoft.Json

### <a name="create-the-content-moderator-client"></a>Content Moderator-ügyfél létrehozása

Cserélje le a ModeratorHelper.cs fájl tartalmát a következő kódra:

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
> Adja meg a régióazonosító és az előfizetői azonosító értékét az **AzureRegion** és a **CMSubscriptionKey** mezőkben.

Most már tudja, hogyan hozhat létre gyorsan Content Moderator-ügyfelet az előfizetéséhez.

## <a name="next-steps"></a>További lépések

[Töltse le a Visual Studio-megoldást](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/ContentModerator) a .NET-es Content Moderator ezen és további rövid útmutatóihoz, hogy nekikezdhessen az integrációnak.
