---
title: 'Gyors útmutató: Beszéd átalakítás, szintetizálásához C# (.NET Core) – beszédszolgáltatások'
titleSuffix: Azure Cognitive Services
description: Ismerje meg, hogyan videofájlokban lévő szintetizálásához C# alatt a .NET Core, a Windows, a beszéd SDK-val
services: cognitive-services
author: yinhew
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 6/24/2019
ms.author: yinhew
ms.openlocfilehash: 7b4a018e38ca625e38dc1658a95d3ce0e677f711
ms.sourcegitcommit: c63e5031aed4992d5adf45639addcef07c166224
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/28/2019
ms.locfileid: "67467424"
---
# <a name="quickstart-synthesize-speech-with-the-speech-sdk-for-net-core"></a>Gyors útmutató: A .NET Core szintetizálásához a beszéd, a beszéd SDK-val

Rövid útmutatók érhetők el is [beszédfelismerés](quickstart-csharp-dotnetcore-windows.md) és [tolmácsolás –](quickstart-translate-speech-dotnetcore-windows.md).

Ebben a cikkben létre fog hozni a Windowson egy C# konzolalkalmazást a .NET Core-hoz a Cognitive Services [Speech SDK](speech-sdk.md) használatával. A PC-hangszóró szöveg valós idejű hang, szintetizálásához. Az alkalmazást a [Speech SDK NuGet-csomaggal](https://aka.ms/csspeech/nuget) és a Microsoft Visual Studio 2017-tel (annak bármely kiadásával) lehet összeállítani.

> [!NOTE]
> A .NET Core egy nyílt forráskódú, platformfüggetlen .NET-platform, amely implementálja a [.NET Standard](https://docs.microsoft.com/dotnet/standard/net-standard) specifikációt.

A rövid útmutató elvégzéséhez beszédszolgáltatások előfizetési kulcs szükséges. amelyet ingyenesen is beszerezhet. Lásd: [próbálja ki ingyenesen a beszédszolgáltatások](get-started.md) részleteiről.

## <a name="prerequisites"></a>Előfeltételek

Ehhez a rövid útmutatóhoz a következőkre van szükség:

* [.NET Core SDK](https://dotnet.microsoft.com/download)
* [Visual Studio 2017](https://visualstudio.microsoft.com/downloads/)
* A beszédfelismerési szolgáltatás egy Azure-előfizetés kulcs. [Igényeljen ingyenesen egy](get-started.md).

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

1. A konzolablakban megjelenik, írjon be valamilyen szöveget, és felszólítja. Írjon be néhány szavakat és a egy mondatot. A beírt szöveget a beszédszolgáltatások továbbítani, és a Speech, amely a a hangfelismerő játszik synthesized.

    ![Képernyőfelvétel a konzol kimenete a sikeres összefoglaló után](media/sdk/qs-tts-csharp-dotnet-windows-console-output.png "Konzolkimenetet követően sikeres összefoglaló")

## <a name="next-steps"></a>További lépések

További példákat, például a következőkkel hangfájl, beszédfelismerés szintetizálásához a Githubon érhetők el.

> [!div class="nextstepaction"]
> [Ismerkedés a C# példák a Githubon](https://aka.ms/csspeech/samples)

## <a name="see-also"></a>Lásd még

- [Hangtípust testreszabása](how-to-customize-voice-font.md)
- [Rekord voice-minták](record-custom-voice-samples.md)
