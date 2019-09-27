---
title: 'Gyors útmutató: Beszéd fordítása C# , (.NET-keretrendszer Windows) – beszédfelismerési szolgáltatás'
titleSuffix: Azure Cognitive Services
description: Ebben a rövid útmutatóban egy .NET-keretrendszerbeli alkalmazást hoz létre a felhasználói beszéd rögzítéséhez, lefordítani egy másik nyelvre, és kiírja a szöveget a parancssorba. Ez az útmutató Windows-felhasználók számára készült.
services: cognitive-services
author: wolfma61
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 08/28/2019
ms.author: erhopf
ms.openlocfilehash: bb710a3e3adb13aa3999c13043c8bb93f6b885f1
ms.sourcegitcommit: e9936171586b8d04b67457789ae7d530ec8deebe
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/27/2019
ms.locfileid: "71327346"
---
# <a name="quickstart-translate-speech-with-the-speech-sdk-for-net-framework-windows"></a>Gyors útmutató: Beszéd fordítása a .NET-keretrendszerhez készült Speech SDK-val (Windows)

A [beszédfelismerést](quickstart-csharp-dotnet-windows.md) és a [beszédfelismerést](quickstart-text-to-speech-dotnet-windows.md)is elérhetővé teszi.

Ebben a rövid útmutatóban egy olyan .NET-keretrendszerbeli alkalmazást fog létrehozni, amely rögzíti a felhasználói beszédet a számítógép mikrofonjában, lefordítja a beszédet, és valós időben írja át a lefordított szöveget a parancssorba. Ez az alkalmazás 32 bites vagy 64 bites Windows rendszeren is futtatható, és a [SPEECH SDK NuGet-csomagjával](https://aka.ms/csspeech/nuget) és a Microsoft Visual Studio 2019-mel készült.

A beszédfelismeréshez elérhető nyelvek teljes listáját a [nyelvi támogatás](language-support.md)című témakörben tekintheti meg.

## <a name="prerequisites"></a>Előfeltételek

Ehhez a rövid útmutatóhoz a következőkre van szükség:

* [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/).
* Egy Azure-előfizetési kulcs a beszédfelismerési szolgáltatáshoz. [Szerezze be az egyiket ingyenesen](get-started.md).

## <a name="create-a-visual-studio-project"></a>Visual Studio-projekt létrehozása

[!INCLUDE [Create project](../../../includes/cognitive-services-speech-service-create-speech-project-vs-csharp.md)]

## <a name="add-sample-code"></a>Mintakód hozzáadása

1. Nyissa meg a **program.cs**, és cserélje le az összes kódot a következőre.

   [!code-csharp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/speech-translation/csharp-dotnet-windows/helloworld/Program.cs#code)]

1. Keresse meg a `YourSubscriptionKey` karakterláncot, és cserélje le az előfizetési kulcsra.

1. Keresse meg a `YourServiceRegion` karakterláncot, és cserélje le az előfizetéshez társított [régióra](regions.md) . Ha például az ingyenes próbaverziós előfizetést használja, akkor a régió `westus`.

1. A menüsávban válassza a **fájl**@no__t – 1**Mentés összes mentése**elemet.

## <a name="build-and-run-the-application"></a>Az alkalmazás fordítása és futtatása

1. A menüsávban válassza a **build** > **Build megoldás** elemet az alkalmazás létrehozásához. A kód fordításának hiba nélkül végbe kell mennie.

1. A **HelloWorld** alkalmazás indításához válassza a **hibakeresés @no__t –** 1**Indítás hibakeresése** lehetőséget (vagy válassza az **F5 billentyűt**).

1. Beszéljen egy angol kifejezéssel vagy mondattal az eszköz mikrofonjában. Az alkalmazás továbbítja a beszédet a Speech Service-nek, amely egy másik nyelven (ebben az esetben a német nyelven) szövegre fordítja a beszédet. A beszédfelismerési szolgáltatás visszaküldi a lefordított szöveget az alkalmazásnak, amely megjeleníti a fordítást az ablakban.

   ![Beszéd fordítás felhasználói felülete](media/sdk/qs-translate-csharp-dotnetcore-windows-output.png)

## <a name="next-steps"></a>További lépések

További minták, mint például a hangfájlok beszédének beolvasása és a lefordított szöveg szintetizált beszédként való kiírása, a GitHubon érhetők el.

> [!div class="nextstepaction"]
> [Minták C# feltárása a githubon](https://aka.ms/csspeech/samples)

## <a name="see-also"></a>Lásd még

- [Custom Speech modell betanítása](how-to-custom-speech-train-model.md)
