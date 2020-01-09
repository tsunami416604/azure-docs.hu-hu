---
title: 'Gyors útmutató C# : SDK-lekérdezések előrejelzési végpontja – Luis'
titleSuffix: Azure Cognitive Services
description: Ebből a rövid útmutatóból megtudhatja, C# hogyan küldhet el egy felhasználót az Azure Cognitive Services Luis alkalmazásnak az SDK használatával, és hogyan kaphat előrejelzést.
author: diberry
manager: nitinme
ms.service: cognitive-services
services: cognitive-services
ms.subservice: language-understanding
ms.topic: quickstart
ms.date: 12/09/2019
ms.author: diberry
ms.openlocfilehash: 37e7224776efa63b39a671a3b3a79ea6c204a9dc
ms.sourcegitcommit: 003e73f8eea1e3e9df248d55c65348779c79b1d6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/02/2020
ms.locfileid: "75611091"
---
# <a name="quickstart-query-v3-prediction-endpoint-with-c-net-sdk"></a>Gyors útmutató: a v3 előrejelzési C# végpont lekérdezése a .net SDK-val

Használja a [NuGet](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Language.LUIS.Runtime/)címen található .net SDK-t, hogy küldjön egy felhasználót Language UNDERSTANDING (Luis), és megkapja a felhasználó szándékának előrejelzését.

A .NET-hez készült Language Understanding (LUIS) előrejelzési ügyféloldali kódtára a következőre használható:

* Előrejelzés beolvasása tárolóhely alapján

[Dokumentáció](https://docs.microsoft.com/dotnet/api/overview/azure/cognitiveservices/client/languageunderstanding?view=azure-dotnet) | [könyvtár forráskódja](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/Language.LUIS.Runtime) | [előrejelző futtatókörnyezet-csomag (NuGet)](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Language.LUIS.Runtime/) | [ C# minták](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/dotnet/LanguageUnderstanding/predict-with-sdk-3x)

## <a name="prerequisites"></a>Előfeltételek

* Language Understanding (LUIS) portál fiók – [hozzon létre egyet ingyen](https://www.luis.ai)
* A [.net Core](https://dotnet.microsoft.com/download/dotnet-core)jelenlegi verziója.

További dokumentációt keres?

 * [Az SDK dokumentációja](https://docs.microsoft.com/dotnet/api/overview/azure/cognitiveservices/client/languageunderstanding?view=azure-dotnet)

## <a name="setting-up"></a>Beállítás

### <a name="create-an-environment-variable"></a>Környezeti változó létrehozása

A kulcs és az erőforrás neve alapján hozzon létre két környezeti változót a hitelesítéshez:

* `LUIS_PREDICTION_KEY` – a kérések hitelesítéséhez használt erőforrás-kulcs.
* `LUIS_ENDPOINT_NAME` – a kulcshoz társított erőforrás neve.

Használja az operációs rendszerének utasításait.

#### <a name="windowstabwindows"></a>[Windows](#tab/windows)

```console
setx LUIS_PREDICTION_KEY <replace-with-your-resource-key>
setx LUIS_ENDPOINT_NAME <replace-with-your-resource-name>
```

A környezeti változó hozzáadása után indítsa újra a konzolablak ablakát.

#### <a name="linuxtablinux"></a>[Linux](#tab/linux)

```bash
export LUIS_PREDICTION_KEY=<replace-with-your-resource-key>
export LUIS_ENDPOINT_NAME=<replace-with-your-resource-name>
```

A környezeti változó hozzáadását követően futtassa a `source ~/.bashrc` parancsot a konzolablakban a módosítások érvénybe léptetéséhez.

#### <a name="macostabunix"></a>[macOS](#tab/unix)

Szerkessze `.bash_profile`, és adja hozzá a környezeti változót:

```bash
export LUIS_PREDICTION_KEY=<replace-with-your-resource-key>
export LUIS_ENDPOINT_NAME=<replace-with-your-resource-name>
```

A környezeti változó hozzáadását követően futtassa a `source .bash_profile` parancsot a konzolablakban a módosítások érvénybe léptetéséhez.
***

### <a name="create-a-new-c-application"></a>Új C# alkalmazás létrehozása

Hozzon létre egy új .NET Core-alkalmazást az előnyben részesített szerkesztőben vagy az IDE-ben.

1. A konzol ablakban (például cmd, PowerShell vagy bash) a DotNet `new` paranccsal hozzon létre egy új, `language-understanding-quickstart`nevű Console-alkalmazást. Ez a parancs egy egyszerű ""Helló világ!"alkalmazás" C# projektet hoz létre egyetlen forrásfájlban: `Program.cs`.

    ```dotnetcli
    dotnet new console -n language-understanding-quickstart
    ```

1. Módosítsa a könyvtárat az újonnan létrehozott alkalmazás mappájába.

1. Az alkalmazást az alábbiakkal hozhatja létre:

    ```dotnetcli
    dotnet build
    ```

    A Build kimenete nem tartalmazhat figyelmeztetést vagy hibát.

    ```console
    ...
    Build succeeded.
     0 Warning(s)
     0 Error(s)
    ...
    ```

### <a name="install-the-sdk"></a>Az SDK telepítése

Az alkalmazás könyvtárában telepítse a .NET-hez készült Language Understanding (LUIS) előrejelzési futásidejű ügyféloldali kódtárat a következő paranccsal:

```dotnetcli
dotnet add package Microsoft.Azure.CognitiveServices.Language.LUIS.Runtime --version 3.0.0
```

Ha a Visual Studio IDE-t használja, az ügyféloldali kódtár letölthető NuGet-csomagként érhető el.

## <a name="object-model"></a>Objektummodell

A Language Understanding (LUIS) előrejelzési futtatókörnyezet ügyfele egy [LUISRuntimeClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.runtime.luisruntimeclient?view=azure-dotnet) objektum, amely az Azure-ba hitelesíti az erőforrás kulcsát.

Az ügyfél létrehozása után ezt az ügyfelet használhatja a következő funkciók eléréséhez, többek között:

* Előrejelzés [átmeneti vagy termék-tárolóhely](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.runtime.predictionoperationsextensions.getslotpredictionasync?view=azure-dotnet) alapján
* Előrejelzés [verziója](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.runtime.predictionoperationsextensions.getversionpredictionasync?view=azure-dotnet) szerint


## <a name="code-examples"></a>Kódpéldák

Ezek a kódrészletek azt mutatják be, hogyan végezheti el a következőket a .NET-hez készült Language Understanding (LUIS) előrejelzési futásidejű ügyféloldali kódtár használatával:

* [Előrejelzés tárolóhely alapján](#get-prediction-from-runtime)

## <a name="add-the-dependencies"></a>Függőségek hozzáadása

A projekt könyvtárában nyissa meg a *program.cs* fájlt az előnyben részesített szerkesztőben vagy az ide-ben. Cserélje le a meglévő `using` kódot a következő `using` irányelvekre:

[!code-csharp[Using statements](~/cognitive-services-quickstart-code/dotnet/LanguageUnderstanding/predict-with-sdk-3x/Program.cs?name=snippet_using)]

## <a name="authenticate-the-client"></a>Az ügyfél hitelesítése

1. Hozzon létre változókat a kulcshoz, a névhez és az alkalmazás-AZONOSÍTÓhoz:

    Egy `LUIS_PREDICTION_KEY`nevű környezeti változóból az előrejelzési kulcs kezelésére szolgáló változók. Ha az alkalmazás elindítása után hozta létre a környezeti változót, akkor a változó eléréséhez be kell zárnia és újra kell töltenie a szerkesztőt, az IDE-t vagy a shellt. A metódusok később lesznek létrehozva.

    Hozzon létre egy változót, amely az erőforrás nevét `LUIS_ENDPOINT_NAME`fogja tárolni.

    Hozzon létre egy változót az alkalmazás-AZONOSÍTÓhoz `LUIS_APP_ID`nevű környezeti változóként. Állítsa a környezeti változót a nyilvános IoT alkalmazásra:

    **`df67dcdb-c37d-46af-88e1-8b97951ca1c2`**

    [!code-csharp[Create variables](~/cognitive-services-quickstart-code/dotnet/LanguageUnderstanding/predict-with-sdk-3x/Program.cs?name=snippet_variables)]

1. Hozzon létre egy [ApiKeyServiceClientCredentials](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.runtime.apikeyserviceclientcredentials?view=azure-dotnet) objektumot a kulccsal, és használja a végpontján egy [LUISRuntimeClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.runtime.luisruntimeclient?view=azure-dotnet) objektum létrehozásához.

    [!code-csharp[Create LUIS client object](~/cognitive-services-quickstart-code/dotnet/LanguageUnderstanding/predict-with-sdk-3x/Program.cs?name=snippet_create_client)]

## <a name="get-prediction-from-runtime"></a>Előrejelzés lekérése futtatókörnyezetből

Adja hozzá a következő metódust az előrejelzési futtatókörnyezethez való kérelem létrehozásához.

A felhasználó teljes értéke a [PredictionRequest](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.runtime.models.predictionrequest?view=azure-dotnet) objektum része.

A **GetSlotPredictionAsync** metódusnak több paraméterre van szüksége, például az alkalmazás azonosítója, a tárolóhely neve, az előrejelzési kérelem objektum a kérelem teljesítéséhez. A többi lehetőség, például a részletes, az összes leképezés megjelenítése és a napló megadása nem kötelező.

[!code-csharp[Create method to get prediction runtime](~/cognitive-services-quickstart-code/dotnet/LanguageUnderstanding/predict-with-sdk-3x/Program.cs?name=snippet_maintask)]

## <a name="main-code-for-the-prediction"></a>Az előrejelzés fő kódja

A következő fő módszer használatával összekapcsolhatja a változókat és a metódusokat az előrejelzés beszerzéséhez.

[!code-csharp[Create method to get prediction runtime](~/cognitive-services-quickstart-code/dotnet/LanguageUnderstanding/predict-with-sdk-3x/Program.cs?name=snippet_main)]

## <a name="run-the-application"></a>Az alkalmazás futtatása

Futtassa az alkalmazást az `dotnet run` paranccsal az alkalmazás könyvtárából.

```dotnetcli
dotnet run
```

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha elkészült a jóslatokkal, törölje a munkát ebből a rövid útmutatóból a program.cs fájl és annak alkönyvtárainak törlésével.

## <a name="next-steps"></a>Következő lépések

További információ a [.net SDK](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Language.LUIS.Runtime/) -ról és a [.net-dokumentációról](https://docs.microsoft.com/dotnet/api/overview/azure/cognitiveservices/client/languageunderstanding?view=azure-dotnet).

> [!div class="nextstepaction"]
> [Oktatóanyag: a felhasználói szándékok meghatározására szolgáló LUIS-alkalmazás létrehozása](luis-quickstart-intents-only.md)