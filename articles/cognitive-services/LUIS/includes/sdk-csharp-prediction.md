---
title: fájl belefoglalása
description: fájl belefoglalása
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.date: 02/14/2020
ms.topic: include
ms.custom: include file
ms.author: diberry
ms.openlocfilehash: 3bb0b9fb8b268c3e036ffb365eb8d5b80a241269
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77371978"
---
A .NET nyelvismertetési (LUIS) előrejelzési ügyfélkönyvtárával:

* Előrejelzés a horony szerint

[Referenciadokumentáció](https://docs.microsoft.com/dotnet/api/overview/azure/cognitiveservices/client/languageunderstanding?view=azure-dotnet) | [könyvtár forráskódja](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/Language.LUIS.Runtime) | [Előrejelzés imperasorozatos (NuGet)](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Language.LUIS.Runtime/) | [C# minták](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/dotnet/LanguageUnderstanding/predict-with-sdk-3x)

## <a name="prerequisites"></a>Előfeltételek

* Language Understanding (LUIS) portálfiók - [Hozzon létre egyet ingyen](https://www.luis.ai)
* A [.NET Core](https://dotnet.microsoft.com/download/dotnet-core)aktuális verziója .

További dokumentációt keres?

 * [SDK referenciadokumentáció](https://docs.microsoft.com/dotnet/api/overview/azure/cognitiveservices/client/languageunderstanding?view=azure-dotnet)

## <a name="setting-up"></a>Beállítása

### <a name="create-an-environment-variable"></a>Környezeti változó létrehozása

A kulcs és az erőforrás neve használatával hozzon létre két környezeti változót a hitelesítéshez:

* `LUIS_PREDICTION_KEY`- A kérések hitelesítéséhez.
* `LUIS_ENDPOINT_NAME`- A kulcshoz társított erőforrásnév.

Használja az operációs rendszerre vonatkozó utasításokat.

#### <a name="windows"></a>[Windows](#tab/windows)

```console
setx LUIS_PREDICTION_KEY <replace-with-your-resource-key>
setx LUIS_ENDPOINT_NAME <replace-with-your-resource-name>
```

A környezeti változó hozzáadása után indítsa újra a konzolablakot.

#### <a name="linux"></a>[Linux](#tab/linux)

```bash
export LUIS_PREDICTION_KEY=<replace-with-your-resource-key>
export LUIS_ENDPOINT_NAME=<replace-with-your-resource-name>
```

A környezeti változó hozzáadását követően futtassa a `source ~/.bashrc` parancsot a konzolablakban a módosítások érvénybe léptetéséhez.

#### <a name="macos"></a>[Macos](#tab/unix)

A program `.bash_profile`szerkesztése és a környezeti változó hozzáadása:

```bash
export LUIS_PREDICTION_KEY=<replace-with-your-resource-key>
export LUIS_ENDPOINT_NAME=<replace-with-your-resource-name>
```

A környezeti változó hozzáadását követően futtassa a `source .bash_profile` parancsot a konzolablakban a módosítások érvénybe léptetéséhez.

---

### <a name="create-a-new-c-application"></a>Új C# alkalmazás létrehozása

Hozzon létre egy új .NET Core alkalmazást a kívánt szerkesztőben vagy IDE-ben.

1. Egy konzolablakban (például cmd, PowerShell vagy Bash) `new` a dotnet paranccsal `language-understanding-quickstart`hozzon létre egy új konzolalkalmazást a .. Ez a parancs egy egyszerű "Hello World" C# `Program.cs`projektet hoz létre egyetlen forrásfájllal: .

    ```dotnetcli
    dotnet new console -n language-understanding-quickstart
    ```

1. Módosítsa a könyvtárat az újonnan létrehozott alkalmazásmappára.

1. Az alkalmazást a következőkkel hozhatja létre:

    ```dotnetcli
    dotnet build
    ```

    A build kimenetnem tartalmazhat figyelmeztetéseket vagy hibákat.

    ```console
    ...
    Build succeeded.
     0 Warning(s)
     0 Error(s)
    ...
    ```

### <a name="install-the-sdk"></a>Az SDK telepítése

Az alkalmazáskönyvtáron belül telepítse a nyelvismertetési (LUIS) előrejelzési futásidejű ügyféltárat a .NET-hez a következő paranccsal:

```dotnetcli
dotnet add package Microsoft.Azure.CognitiveServices.Language.LUIS.Runtime --version 3.0.0
```

Ha a Visual Studio IDE-t használja, az ügyfélkönyvtár letölthető NuGet csomagként érhető el.

## <a name="object-model"></a>Objektummodell

A Language Understanding (LUIS) előrejelzési futásidejű ügyfél egy [LUISRuntimeClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.runtime.luisruntimeclient?view=azure-dotnet) objektum, amely hitelesíti az Azure-ban, amely tartalmazza az erőforráskulcsot.

Az ügyfél létrehozása után ezzel az ügyféllel érheti el a funkciókat, például a következőket:

* Előrejelzés [átmeneti vagy terméktárolóhely](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.runtime.predictionoperationsextensions.getslotpredictionasync?view=azure-dotnet) szerint
* Előrejelzés [verzió](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.runtime.predictionoperationsextensions.getversionpredictionasync?view=azure-dotnet) szerint


## <a name="code-examples"></a>Kódpéldák

Ezek a kódrészletek bemutatják, hogyan kell a következőket a .NET nyelvismertetési (LUIS) előrejelzési futásidejű ügyféltárával:

* [Előrejelzés a nyíláson belül](#get-prediction-from-runtime)

## <a name="add-the-dependencies"></a>Függőségek hozzáadása

A projekt könyvtárából nyissa meg a *Program.cs* fájlt a kívánt szerkesztőben vagy IDE-ben. Cserélje le `using` a meglévő `using` kódot a következő irányelvekre:

[!code-csharp[Using statements](~/cognitive-services-quickstart-code/dotnet/LanguageUnderstanding/predict-with-sdk-3x/Program.cs?name=snippet_using)]

## <a name="authenticate-the-client"></a>Az ügyfél hitelesítése

1. Változók létrehozása a kulcshoz, névhez és alkalmazásazonosítóhoz:

    Az előrejelzési kulcs kezelésére szolgáló változók, `LUIS_PREDICTION_KEY`amelyeket egy nevű környezeti változóból állítottak le. Ha az alkalmazás elindítása után hozta létre a környezeti változót, a szerkesztőt, az IDE-t vagy a rendszerhéjat futtató rendszernek be kell zárnia, és újra kell töltenie a változó eléréséhez. A metódusok később jönnek létre.

    Hozzon létre egy változót az erőforrás nevének tárolására. `LUIS_ENDPOINT_NAME`

    Hozzon létre egy változót az alkalmazásazonosítóhoz a nevű környezeti változóként. `LUIS_APP_ID` Állítsa be a környezeti változót a nyilvános IoT-alkalmazásra:

    **`df67dcdb-c37d-46af-88e1-8b97951ca1c2`**

    [!code-csharp[Create variables](~/cognitive-services-quickstart-code/dotnet/LanguageUnderstanding/predict-with-sdk-3x/Program.cs?name=snippet_variables)]

1. Hozzon létre egy [ApiKeyServiceClientS objektumot](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.runtime.apikeyserviceclientcredentials?view=azure-dotnet) a kulccsal, és használja azt a végponttal egy [LUISRuntimeClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.runtime.luisruntimeclient?view=azure-dotnet) objektum létrehozásához.

    [!code-csharp[Create LUIS client object](~/cognitive-services-quickstart-code/dotnet/LanguageUnderstanding/predict-with-sdk-3x/Program.cs?name=snippet_create_client)]

## <a name="get-prediction-from-runtime"></a>Előrejelzés beszerezni a futásidejű

Adja hozzá a következő módszert a kérelem létrehozásához az előrejelzési futásórához.

A felhasználói utterance (kifejezés) része az [PredictionRequest](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.runtime.models.predictionrequest?view=azure-dotnet) objektum.

A **GetSlotPredictionAsync** metódusnak több paraméterre van szüksége, például az alkalmazásazonosítóra, a tárolóhely nevére, az előrejelzési kérelem objektumra a kérés teljesítéséhez. A többi lehetőség, például a részletes, az összes szándék megjelenítése és a napló megadása nem kötelező.

[!code-csharp[Create method to get prediction runtime](~/cognitive-services-quickstart-code/dotnet/LanguageUnderstanding/predict-with-sdk-3x/Program.cs?name=snippet_maintask)]

## <a name="main-code-for-the-prediction"></a>Az előrejelzés fő kódja

A következő fő módszerrel kösse össze a változókat és módszereket az előrejelzés lekérése érdekében.

[!code-csharp[Create method to get prediction runtime](~/cognitive-services-quickstart-code/dotnet/LanguageUnderstanding/predict-with-sdk-3x/Program.cs?name=snippet_main)]

## <a name="run-the-application"></a>Az alkalmazás futtatása

Futtassa az `dotnet run` alkalmazást az alkalmazáskönyvtárból származó paranccsal.

```dotnetcli
dotnet run
```

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha végzett az előrejelzésekkel, törölje a munkát ebből a rövid útmutatóból a program.cs fájl és alkönyvtárainak törlésével.
