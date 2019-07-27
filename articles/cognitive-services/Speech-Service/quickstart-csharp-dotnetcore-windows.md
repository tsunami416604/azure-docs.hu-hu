---
title: 'Gyors útmutató: Beszédfelismerés felismerése C# (.net Core) – Speech Service'
titleSuffix: Azure Cognitive Services
description: Ismerje meg, hogyan ismerheti C# fel a beszédfelismerést a .net Core alatt Windows vagy MacOS rendszeren a Speech SDK használatával
services: cognitive-services
author: wolfma61
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 07/05/2019
ms.author: wolfma
ms.openlocfilehash: 3ba82f2bb3d8325b7cf77f357471dc4f25382ff6
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/26/2019
ms.locfileid: "68559524"
---
# <a name="quickstart-recognize-speech-with-the-speech-sdk-for-net-core"></a>Gyors útmutató: Beszéd felismerése a .NET Core-hoz készült Speech SDK-val

A gyors útmutató a [szöveg-beszéd](quickstart-text-to-speech-dotnetcore.md) és a [beszéd fordításához](quickstart-translate-speech-dotnetcore-windows.md)is elérhető.

Ha szükséges, válasszon másik programozási nyelvet és/vagy környezetet:<br/>
[!INCLUDE [Selector](../../../includes/cognitive-services-speech-service-quickstart-selector.md)]

Ebben a cikkben a .NET Core- C# hoz készült Console-alkalmazást Windows vagy MacOS rendszeren hozza létre a Cognitive Services [Speech SDK](speech-sdk.md)használatával. A számítógép mikrofonjába beszélve valós időben konvertálhat át beszédet szöveggé. Az alkalmazást a [Speech SDK NuGet-csomaggal](https://aka.ms/csspeech/nuget) és a Microsoft Visual Studio 2017-tel (annak bármely kiadásával) lehet összeállítani.

> [!NOTE]
> A .NET Core egy nyílt forráskódú, platformfüggetlen .NET-platform, amely implementálja a [.NET Standard](https://docs.microsoft.com/dotnet/standard/net-standard) specifikációt.

A rövid útmutató elvégzéséhez szüksége lesz egy Speech Services-előfizetési kulcsra. amelyet ingyenesen is beszerezhet. További részletekért tekintse [meg a Speech Services ingyenes kipróbálása](get-started.md) című témakört.

## <a name="prerequisites"></a>Előfeltételek

Ehhez a rövid útmutatóhoz a következőkre van szükség:

* [.NET Core SDK](https://dotnet.microsoft.com/download)
* [Visual Studio 2017](https://visualstudio.microsoft.com/downloads/)
* Egy Azure-előfizetési kulcs a beszédfelismerési szolgáltatáshoz. [Szerezze be az egyiket ingyenesen](get-started.md).

## <a name="create-a-visual-studio-project"></a>Visual Studio-projekt létrehozása

[!INCLUDE [](../../../includes/cognitive-services-speech-service-quickstart-dotnetcore-create-proj.md)]

## <a name="add-sample-code"></a>Mintakód hozzáadása

1. Nyissa meg a `Program.cs` fájlt, és cserélje le a benne lévő teljes kódot a következőre.

    [!code-csharp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/csharp-dotnetcore/helloworld/Program.cs#code)]

1. Ugyanabban a fájlban cserélje le a `YourSubscriptionKey` sztringet az előfizetői azonosítóra.

1. Cserélje le a `YourServiceRegion` sztringet az előfizetéséhez társított [régióra](regions.md) (ez a `westus` régió, ha az ingyenes próbaverzióra regisztrált).

1. Mentse a projekt módosításait.

## <a name="build-and-run-the-app"></a>Az alkalmazás létrehozása és futtatása

1. Hozza létre az alkalmazást. A menüsávon válassza a **Létrehozás** > **Megoldás fordítása** elemet. A kód fordításának hiba nélkül kell végbe mennie.

    ![A Visual Studio képernyőképe, amelyen ki van emelve a Megoldás fordítása lehetőség](media/sdk/qs-csharp-dotnetcore-windows-05-build.png "Sikeres létrehozás")

1. Indítsa el az alkalmazást. A menüsávon válassza a **Hibakeresés** > **Hibakeresés indítása** lehetőséget, vagy nyomja le az **F5** billentyűt.

    ![A Visual Studio képernyőképe, amelyen ki van emelve a Hibakeresés indítása lehetőség](media/sdk/qs-csharp-dotnetcore-windows-06-start-debugging.png "Hibakeresés indítása az alkalmazáson")

1. Megjelenik egy konzolablak, amely arra kéri Önt, hogy mondjon valamit. Mondjon ki egy angol nyelvű kifejezést vagy mondatot. A beszéd a beszédfelismerési szolgáltatásokhoz lett továbbítva, és szövegbe kerül, amely ugyanabban az ablakban jelenik meg.

    ![Képernyőkép a konzolról a sikeres felismerést követően](media/sdk/qs-csharp-dotnetcore-windows-07-console-output.png "A konzol a sikeres felismerést követően")

## <a name="next-steps"></a>További lépések

A GitHubon további minták is elérhetők, például a hangfájlok beszédének olvasása.

> [!div class="nextstepaction"]
> [Minták C# feltárása a githubon](https://aka.ms/csspeech/samples)

## <a name="see-also"></a>Lásd még

- [Akusztikai modellek testreszabása](how-to-customize-acoustic-models.md)
- [Nyelvi modellek testreszabása](how-to-customize-language-model.md)
