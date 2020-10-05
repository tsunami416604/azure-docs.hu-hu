---
title: 'Gyors útmutató: helyesírás-ellenőrzés a C Bing Spell Check SDK-val #'
titleSuffix: Azure Cognitive Services
description: Az Bing Spell Check REST API használatának első lépései a helyesírás és a nyelvtan ellenőrzéséhez.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-spell-check
ms.topic: quickstart
ms.date: 12/16/2019
ms.author: aahi
ms.custom: devx-track-csharp
ms.openlocfilehash: e1e05ca9b63bda3373afb8a090118953d89ad8f9
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/05/2020
ms.locfileid: "88934225"
---
# <a name="quickstart-check-spelling-with-the-bing-spell-check-sdk-for-c"></a>Gyors útmutató: helyesírás-ellenőrzés a C Bing Spell Check SDK-val #

Ezzel a rövid útmutatóval megkezdheti a C# Bing Spell Check SDK-val való helyesírás-ellenőrzést. Habár a Bing Spell Check REST API kompatibilis a legtöbb programozási nyelvvel, az SDK egyszerű módszert kínál a szolgáltatás integrálására az alkalmazásokba. A minta forráskódja a [githubon](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/samples/SpellCheck)található.

## <a name="application-dependencies"></a>Alkalmazásfüggőségek

* A [Visual Studio 2017 vagy újabb](https://visualstudio.microsoft.com/downloads/)verziójának bármely kiadása.
* A Bing Spell Check [NuGet csomag](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Language.SpellCheck)

Ha hozzá szeretné adni a Bing Spell Check SDK-t a projekthez, válassza a **NuGet-csomagok kezelése** **megoldáskezelő** a Visual Studióban lehetőséget. Vegye fel a `Microsoft.Azure.CognitiveServices.Language.SpellCheck` csomagot. A csomag a következő függőségeket is telepíti:

* Microsoft.Rest.ClientRuntime
* Microsoft.Rest.ClientRuntime.Azure
* Newtonsoft.Json

[!INCLUDE [cognitive-services-bing-spell-check-signup-requirements](../../../includes/cognitive-services-bing-spell-check-signup-requirements.md)]

## <a name="create-and-initialize-the-application"></a>Az alkalmazás létrehozása és inicializálása

1. Hozzon létre egy új C# konzolos megoldást a Visual Studióban. Ezután adja hozzá a következő `using` utasítást.
    
    ```csharp
    using System;
    using System.Linq;
    using System.Threading.Tasks;
    using Microsoft.Azure.CognitiveServices.Language.SpellCheck;
    using Microsoft.Azure.CognitiveServices.Language.SpellCheck.Models;
    ```

2. Hozzon létre egy új osztályt. Ezután hozzon létre egy nevű aszinkron függvényt `SpellCheckCorrection()` , amely egy előfizetési kulcsot fogad, és elküldi a helyesírás-ellenőrzési kérést.

3. Új objektum létrehozásával hozza létre az ügyfelet `ApiKeyServiceClientCredentials` . 

    ```csharp
    public static class SpellCheckSample{
        public static async Task SpellCheckCorrection(string key){
            var client = new SpellCheckClient(new ApiKeyServiceClientCredentials(key));
        }
        //...
    }
    ```

## <a name="send-the-request-and-read-the-response"></a>A kérelem elküldése és a válasz elolvasása

1. A fent létrehozott függvényben hajtsa végre a következő lépéseket. Küldje el a helyesírás-ellenőrzési kérelmet az ügyféllel. Adja hozzá a `text` paraméterhez ellenőrizendő szöveget, és állítsa be a módot a következőre: `proof` .  
    
    ```csharp
    var result = await client.SpellCheckerWithHttpMessagesAsync(text: "Bill Gatas", mode: "proof");
    ```

2. Az első helyesírás-ellenőrzés eredményének beolvasása, ha van ilyen. Nyomtassa ki a visszaadott első hibásan beírt szót (tokent), a jogkivonat típusát és a javaslatok számát.

    ```csharp
    var firstspellCheckResult = result.Body.FlaggedTokens.FirstOrDefault();
    
    if (firstspellCheckResult != null)
    {
        Console.WriteLine("SpellCheck Results#{0}", result.Body.FlaggedTokens.Count);
        Console.WriteLine("First SpellCheck Result token: {0} ", firstspellCheckResult.Token);
        Console.WriteLine("First SpellCheck Result Type: {0} ", firstspellCheckResult.Type);
        Console.WriteLine("First SpellCheck Result Suggestion Count: {0} ", firstspellCheckResult.Suggestions.Count);
    }
    ```

3. Kérje le az első javasolt javítást, ha van ilyen. Nyomtassa ki a javaslat pontszámát és a javasolt szót. 

    ```csharp
    var suggestions = firstspellCheckResult.Suggestions;

    if (suggestions?.Count > 0)
    {
        var firstSuggestion = suggestions.FirstOrDefault();
        Console.WriteLine("First SpellCheck Suggestion Score: {0} ", firstSuggestion.Score);
        Console.WriteLine("First SpellCheck Suggestion : {0} ", firstSuggestion.Suggestion);
    }
    ```

## <a name="run-the-application"></a>Az alkalmazás futtatása

Hozza létre és futtassa a projektet. Ha a Visual studiót használja, nyomja le az **F5** billentyűt a fájl hibakereséséhez.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Egyoldalas webalkalmazás létrehozása](tutorials/spellcheck.md)

- [Mi a Bing Spell Check API?](overview.md)
- [Bing Spell Check C# SDK-útmutató](https://docs.microsoft.com/dotnet/api/overview/azure/cognitiveservices/client/bingspellcheck?view=azure-dotnet)
