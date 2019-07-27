---
title: 'Gyors útmutató: Beszédfelismerés felismerése, .NET-keretrendszer (Windows) – Speech Service'
titleSuffix: Azure Cognitive Services
description: Ezt az útmutatót követve egy diktálási konzolalkalmazást hozhat létre a Windows-hoz készült .NET-keretrendszer és a Speech SDK használatával. Ha elkészült, a számítógép mikrofonjával valós időben konvertálhat át beszédet szöveggé.
services: cognitive-services
author: wolfma61
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 07/05/2019
ms.author: wolfma
ms.openlocfilehash: 47f955734070be4adfe7f58da98265b976e643d5
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/26/2019
ms.locfileid: "68554154"
---
# <a name="quickstart-recognize-speech-with-the-speech-sdk-for-net-framework-windows"></a>Gyors útmutató: Beszéd felismerése a .NET-keretrendszerhez készült Speech SDK-val (Windows)

A gyors útmutató a [szöveg-beszéd](quickstart-text-to-speech-dotnet-windows.md) és a [beszéd fordításához](quickstart-translate-speech-dotnetframework-windows.md)is elérhető.

Ha szükséges, válasszon másik programozási nyelvet és/vagy környezetet:<br/>
[!INCLUDE [Selector](../../../includes/cognitive-services-speech-service-quickstart-selector.md)]

Ezt az útmutatót követve egy diktálási konzolalkalmazást hozhat létre a Windows-hoz készült .NET-keretrendszer és a Speech SDK használatával. Ha elkészült, a számítógép mikrofonjával valós időben konvertálhat át beszédet szöveggé.

Egy gyors bemutatóhoz (a Visual Studio-projekt létrehozása nélkül, az alábbi ábrán látható módon):

Szerezze be a legfrissebb [Cognitive Services SPEECH SDK-mintákat](https://github.com/Azure-Samples/cognitive-services-speech-sdk) a githubról.

## <a name="prerequisites"></a>Előfeltételek

A projekt teljesítéséhez a következők szükségesek:

* [Visual Studio 2017](https://visualstudio.microsoft.com/downloads/)
* A beszédfelismerési szolgáltatáshoz tartozó előfizetési kulcs. [Szerezze be az egyiket ingyenesen](get-started.md).
* Hozzáférés a számítógép mikrofonjához

## <a name="create-a-visual-studio-project"></a>Visual Studio-projekt létrehozása

[!INCLUDE [Create project](../../../includes/cognitive-services-speech-service-create-speech-project-vs-csharp.md)]

## <a name="add-sample-code"></a>Mintakód hozzáadása

1. Nyissa meg a `Program.cs` fájlt, és cserélje le az automatikusan előállított kódot a következő mintára:

    [!code-csharp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/csharp-dotnet-windows/helloworld/Program.cs#code)]

1. Keresse meg és cserélje le `YourSubscriptionKey` a karakterláncot a Speech Services előfizetési kulcsával.

1. Keresse meg és cserélje le a `YourServiceRegion` sztringet az előfizetéséhez társított [régióra](regions.md). Ha például az ingyenes próbaverziót használja, akkor a régió a `westus`.

1. Mentse a projekt módosításait.

## <a name="build-and-run-the-app"></a>Az alkalmazás létrehozása és futtatása

1. A menüsávon válassza a **Létrehozás** > **Megoldás fordítása** elemet. A kód fordításának hiba nélkül végbe kell mennie.

    ![A Visual Studio képernyőképe, amelyen ki van emelve a Megoldás fordítása lehetőség](media/sdk/qs-csharp-dotnet-windows-08-build.png "Sikeres létrehozás")

1. Az alkalmazás elindításához a menüsávon válassza a **Hibakeresés** > **Hibakeresés indítása** elemet, vagy nyomja le az **F5** billentyűt.

    ![A Visual Studio képernyőképe, amelyen ki van emelve a Hibakeresés indítása lehetőség](media/sdk/qs-csharp-dotnet-windows-09-start-debugging.png "Hibakeresés indítása az alkalmazáson")

1. Ekkor megjelenik egy konzolablak, amely arra kéri Önt, hogy beszéljen. Mondjon valamit angolul. A beszédet a beszédfelismerési szolgáltatásoknak továbbítjuk, és valós időben leírjuk a szöveget. Az eredmény a konzolon jelenik meg.

    ![Képernyőkép a konzolról a sikeres felismerést követően](media/sdk/qs-csharp-dotnet-windows-10-console-output.png "A konzol a sikeres felismerést követően")

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Minták C# feltárása a githubon](https://aka.ms/csspeech/samples)

## <a name="see-also"></a>Lásd még

- [Akusztikai modellek testreszabása](how-to-customize-acoustic-models.md)
- [Nyelvi modellek testreszabása](how-to-customize-language-model.md)
