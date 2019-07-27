---
title: 'Gyors útmutató: Beszédszintetizátor-beszéd C# (.net Core) – Speech Service'
titleSuffix: Azure Cognitive Services
description: Megtudhatja, hogyan szintetizálhat C# beszédet a .net Core alatt a Windowson a Speech SDK használatával
services: cognitive-services
author: yinhew
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 6/24/2019
ms.author: yinhew
ms.openlocfilehash: b298e66674753e33a28fb3404e36e63b5baa8dff
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/26/2019
ms.locfileid: "68559240"
---
# <a name="quickstart-synthesize-speech-with-the-speech-sdk-for-net-core"></a>Gyors útmutató: Beszéd szintetizálása a .NET Core Speech SDK-val

A gyors útmutatók a [beszédfelismerés](quickstart-csharp-dotnetcore-windows.md) és a [beszédfelismerés](quickstart-translate-speech-dotnetcore-windows.md)számára is elérhetők.

Ebben a cikkben létre fog hozni a Windowson egy C# konzolalkalmazást a .NET Core-hoz a Cognitive Services [Speech SDK](speech-sdk.md) használatával. A beszédet valós időben szintetizálhatja a számítógép hangszóróján. Az alkalmazást a [Speech SDK NuGet-csomaggal](https://aka.ms/csspeech/nuget) és a Microsoft Visual Studio 2017-tel (annak bármely kiadásával) lehet összeállítani.

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

    [!code-csharp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/text-to-speech/csharp-dotnetcore/helloworld/Program.cs#code)]

1. Ugyanabban a fájlban cserélje le a `YourSubscriptionKey` sztringet az előfizetői azonosítóra.

1. Cserélje le a `YourServiceRegion` sztringet az előfizetéséhez társított [régióra](regions.md) (ez a `westus` régió, ha az ingyenes próbaverzióra regisztrált).

1. Mentse a projekt módosításait.

## <a name="build-and-run-the-app"></a>Az alkalmazás létrehozása és futtatása

1. Hozza létre az alkalmazást. A menüsávon válassza a **Létrehozás** > **Megoldás fordítása** elemet. A kód fordításának hiba nélkül kell végbe mennie.

    ![A Visual Studio képernyőképe, amelyen ki van emelve a Megoldás fordítása lehetőség](media/sdk/qs-csharp-dotnetcore-windows-05-build.png "Sikeres létrehozás")

1. Indítsa el az alkalmazást. A menüsávon válassza a **Hibakeresés** > **Hibakeresés indítása** lehetőséget, vagy nyomja le az **F5** billentyűt.

    ![A Visual Studio képernyőképe, amelyen ki van emelve a Hibakeresés indítása lehetőség](media/sdk/qs-csharp-dotnetcore-windows-06-start-debugging.png "Hibakeresés indítása az alkalmazáson")

1. Megjelenik egy konzolablak, amely felszólítja, hogy írjon be egy szöveget. Írjon be néhány szót vagy mondatot. A beírt szöveg továbbítva lesz a Speech Servicesnek, és a beszédbe lett szintetizálva, amely a beszélőn játszik.

    ![A sikeres szintézis után a konzol kimenetének képernyőképe] A (media/sdk/qs-tts-csharp-dotnet-windows-console-output.png "sikeres szintézis után a konzol kimenete")

## <a name="next-steps"></a>További lépések

A GitHubon további minták is elérhetők, például hogyan lehet beszédet szintetizálni egy hangfájlba.

> [!div class="nextstepaction"]
> [Minták C# feltárása a githubon](https://aka.ms/csspeech/samples)

## <a name="see-also"></a>Lásd még

- [Hangbetűkészletek testreszabása](how-to-customize-voice-font.md)
- [Hangminták rögzítése](record-custom-voice-samples.md)
