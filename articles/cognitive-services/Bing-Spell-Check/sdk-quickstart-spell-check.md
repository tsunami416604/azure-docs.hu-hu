---
title: 'Rövid útmutató: Helyesírás-ellenőrzés a Bing Helyesírás-ellenőrző SDK c #'
titleSuffix: Azure Cognitive Services
description: Első lépések a Bing Helyesírás-ellenőrző REST API-val a helyesírás és a nyelvhelyesség ellenőrzéséhez.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-spell-check
ms.topic: quickstart
ms.date: 12/16/2019
ms.author: aahi
ms.openlocfilehash: 1cda7032d5bfe58e9f8bcbdb8b18dd597a691441
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "78273531"
---
# <a name="quickstart-check-spelling-with-the-bing-spell-check-sdk-for-c"></a>Rövid útmutató: Helyesírás-ellenőrzés a Bing Helyesírás-ellenőrző SDK c #

Ezzel a rövid útmutatóval megkezdheti a helyesírás-ellenőrzést a Bing Helyesírás-ellenőrző SDK-val C#-hez. Bár a Bing helyesírás-ellenőrzés a legtöbb programozási nyelvvel kompatibilis REST API-val rendelkezik, az SDK egyszerű módot kínál a szolgáltatás alkalmazásokba való integrálására. A minta forráskódja megtalálható a [GitHubon.](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/samples/SpellCheck)

## <a name="application-dependencies"></a>Alkalmazásfüggőségek

* A [Visual Studio 2017-es vagy újabb verzióinak](https://visualstudio.microsoft.com/downloads/)bármely kiadása.
* A Bing helyesírás-ellenőrzés [Nuget csomag](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Language.SpellCheck)

Ha hozzá szeretné adni a Bing helyesírás-ellenőrző SDK-t a projekthez, válassza **a NuGet csomagok kezelése a** Visual **Studióban a Solution Explorer** ből lehetőséget. Vegye fel a `Microsoft.Azure.CognitiveServices.Language.SpellCheck` csomagot. A csomag a következő függőségeket is telepíti:

* Microsoft.Rest.ClientRuntime
* Microsoft.Rest.ClientRuntime.Azure
* Newtonsoft.Json

[!INCLUDE [cognitive-services-bing-spell-check-signup-requirements](../../../includes/cognitive-services-bing-spell-check-signup-requirements.md)]

## <a name="create-and-initialize-the-application"></a>Az alkalmazás létrehozása és inicializálása

1. Hozzon létre egy új C# konzolos megoldást a Visual Studióban. Ezután adja `using` hozzá a következő utasítást.
    
    ```csharp
    using System;
    using System.Linq;
    using System.Threading.Tasks;
    using Microsoft.Azure.CognitiveServices.Language.SpellCheck;
    using Microsoft.Azure.CognitiveServices.Language.SpellCheck.Models;
    ```

2. Hozzon létre egy új osztályt. Ezután hozzon létre egy `SpellCheckCorrection()` aszinkron függvényt hívott, amely egy előfizetési kulcsot vesz, és elküldi a helyesírás-ellenőrzési kérelmet.

3. Új `ApiKeyServiceClientCredentials` objektum létrehozásával hozza létre az ügyfelet. 

    ```csharp
    public static class SpellCheckSample{
        public static async Task SpellCheckCorrection(string key){
            var client = new SpellCheckClient(new ApiKeyServiceClientCredentials(key));
        }
        //...
    }
    ```

## <a name="send-the-request-and-read-the-response"></a>Küldje el a kérést, és olvassa el a választ

1. A fent létrehozott függvényben hajtsa végre a következő lépéseket. Küldje el a helyesírás-ellenőrzési kérelmet az ügyfélnek. Adja hozzá a bejelölendő szöveget a `text` `proof`paraméterhez, és állítsa a módot a beállításra.  
    
    ```csharp
    var result = await client.SpellCheckerWithHttpMessagesAsync(text: "Bill Gatas", mode: "proof");
    ```

2. Szerezd meg az első helyesírás-ellenőrzési eredményt, ha van ilyen. Nyomtassa ki az első hibásan írt szót (jogkivonatot), a jogkivonat típusát és a javaslatok számát.

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

3. Szerezd meg az első javasolt korrekciót, ha van ilyen. Nyomtassa ki a javaslat pontszámát és a javasolt szót. 

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

Készítse el és futtassa a projektet. Visual Studio használata esetén nyomja le az **F5** billentyűt a fájl hibakereséséhez.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Egyoldalas webalkalmazás létrehozása](tutorials/spellcheck.md)

- [Mi a Bing Spell Check API?](overview.md)
- [Bing helyesírás-ellenőrzés C# SDK referenciaútmutató](https://docs.microsoft.com/dotnet/api/overview/azure/cognitiveservices/client/bingspellcheck?view=azure-dotnet)
