---
title: 'Gyors útmutató: Ellenőrizze a helyesírást, a Bing helyesírás-ellenőrzése SDK forC#'
titlesuffix: Azure Cognitive Services
description: Ismerkedés a Bing Spell Check REST API használatával a helyesírás-és nyelvtani.
services: cognitive-services
author: mikedodaro
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-spell-check
ms.topic: quickstart
ms.date: 02/20/2019
ms.author: v-gedod
ms.openlocfilehash: ae558c40a3eb30cb239b19a59542d9d83d5a9566
ms.sourcegitcommit: 24906eb0a6621dfa470cb052a800c4d4fae02787
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/27/2019
ms.locfileid: "56886145"
---
# <a name="quickstart-check-spelling-with-the-bing-spell-check-sdk-for-c"></a>Gyors útmutató: Ellenőrizze a helyesírást, a Bing helyesírás-ellenőrzése SDK forC#

Ez a rövid útmutató segítségével megkezdheti a helyesírás-ellenőrzést, a Bing helyesírás-ellenőrzése SDK for C#. Míg a Bing Spell Check REST API-val kompatibilis szinte bármelyik programozási nyelvével, az SDK biztosít egy egyszerű módja annak, hogy a szolgáltatás integrálása az alkalmazásokba. Ez a minta forráskódja találhatók [GitHub](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/samples/SpellCheck).

## <a name="application-dependencies"></a>Alkalmazásfüggőségek

* A [Visual Studio 2017](https://visualstudio.microsoft.com/downloads/) bármely kiadása.
* A Bing Spell Check [NuGet-csomag](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Language.SpellCheck)

A Bing helyesírás-ellenőrzése SDK hozzáadása a projekthez, kattintson a `Manage NuGet Packages` a Visual Studio Megoldáskezelőjében. Vegye fel a `Microsoft.Azure.CognitiveServices.Language.SpellCheck` csomagot. A csomag is települ a következő függőségeket:

* Microsoft.Rest.ClientRuntime
* Microsoft.Rest.ClientRuntime.Azure
* Newtonsoft.Json

[!INCLUDE [cognitive-services-bing-spell-check-signup-requirements](../../../includes/cognitive-services-bing-spell-check-signup-requirements.md)]

## <a name="create-and-initialize-the-application"></a>Az alkalmazás létrehozása és inicializálása

1. Hozzon létre egy új C# konzol megoldást a Visual Studióban. Ezután adja hozzá a következő `using` utasítást.
    
    ```csharp
    using System;
    using System.Linq;
    using System.Threading.Tasks;
    using Microsoft.Azure.CognitiveServices.Language.SpellCheck;
    using Microsoft.Azure.CognitiveServices.Language.SpellCheck.Models;
    ```

2. Hozzon létre egy új osztályt. Ezután hozzon létre egy aszinkron a hívott függvény `SpellCheckCorrection()` , amely vesz igénybe egy előfizetési kulcsot, és a helyesírás-ellenőrzési kérést küld.

3. Hozza létre az ügyfél hozzon létre egy új `ApiKeyServiceClientCredentials` objektum. 

    ```csharp
    public static class SpellCheckSample{
        public static async Task SpellCheckCorrection(string key){
            var client = new SpellCheckClient(new ApiKeyServiceClientCredentials(key));
        }
        //...
    }
    ```

## <a name="send-the-request-and-read-the-response"></a>A kérelem elküldéséhez és a válasz olvasása

1. A fent létrehozott függvényben hajtsa végre az alábbi lépéseket. A az ügyféllel helyesírás-ellenőrzés kérés küldése. Adja hozzá a szöveget a telepítőprogram ellenőrzi, hogy a `text` paramétert, és állítsa a módot `proof`.  
    
    ```csharp
    var result = await client.SpellCheckerWithHttpMessagesAsync(text: "Bill Gatas", mode: "proof");
    ```

2. Első helyesírás-ellenőrzés eredménye akkor kaphat, ha van ilyen. Nyomtatás az első helytelenül írt szó (token) adott vissza, a jogkivonat típusa és javaslatok számát.

    ```csharp
    if (firstspellCheckResult != null){
        var firstspellCheckResult = result.Body.FlaggedTokens.FirstOrDefault();
    
        Console.WriteLine("SpellCheck Results#{0}", result.Body.FlaggedTokens.Count);
        Console.WriteLine("First SpellCheck Result token: {0} ", firstspellCheckResult.Token);
        Console.WriteLine("First SpellCheck Result Type: {0} ", firstspellCheckResult.Type);
        Console.WriteLine("First SpellCheck Result Suggestion Count: {0} ", firstspellCheckResult.Suggestions.Count);
    }
    ```

3. Az első javasolt javítás kaphat, ha van ilyen. Nyomtassa ki a javaslat pontszám, és a javasolt szóra. 

    ```csharp
            var suggestions = firstspellCheckResult.Suggestions;

            if (suggestions?.Count > 0)
            {
                var firstSuggestion = suggestions.FirstOrDefault();
                Console.WriteLine("First SpellCheck Suggestion Score: {0} ", firstSuggestion.Score);
                Console.WriteLine("First SpellCheck Suggestion : {0} ", firstSuggestion.Suggestion);
            }
}

## Next steps

> [!div class="nextstepaction"]
> [Create a single page web-app](tutorials/spellcheck.md)

- [What is the Bing Spell Check API?](overview.md)
- [Bing Spell Check C# SDK reference guide](https://docs.microsoft.com/dotnet/api/overview/azure/cognitiveservices/client/bingspellcheck?view=azure-dotnet)