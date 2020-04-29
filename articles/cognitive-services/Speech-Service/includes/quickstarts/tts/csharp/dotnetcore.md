---
title: 'Gyors útmutató: beszédfelismerési beszéd, C# (.NET Core) – beszédfelismerési szolgáltatás'
titleSuffix: Azure Cognitive Services
description: Megtudhatja, hogyan szintetizálhatja a beszédfelismerést a C# nyelvben a .NET Core Windows rendszeren a Speech SDK használatával
services: cognitive-services
author: yinhew
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 04/04/2020
ms.author: yinhew
ms.openlocfilehash: 91e06805b687e66c147b0904175ae20d01387acf
ms.sourcegitcommit: 34a6fa5fc66b1cfdfbf8178ef5cdb151c97c721c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "81274960"
---
> [!NOTE]
> A .NET Core egy nyílt forráskódú, platformfüggetlen .NET-platform, amely implementálja a [.NET Standard](https://docs.microsoft.com/dotnet/standard/net-standard) specifikációt.

## <a name="prerequisites"></a>Előfeltételek

Az első lépések előtt ügyeljen a következőre:

> [!div class="checklist"]
> * [Azure Speech-erőforrás létrehozása](../../../../get-started.md)
> * [Állítsa be a fejlesztési környezetet, és hozzon létre egy üres projektet](../../../../quickstarts/setup-platform.md?tabs=dotnetcore&pivots=programming-language-csharp)

## <a name="add-sample-code"></a>Mintakód hozzáadása

1. Nyissa meg a `Program.cs` fájlt, és cserélje le a benne lévő teljes kódot a következőre.

    [!code-csharp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/csharp/dotnetcore/text-to-speech/helloworld/Program.cs#code)]

1. Ugyanabban a fájlban cserélje le a `YourSubscriptionKey` sztringet az előfizetői azonosítóra.

1. Cserélje le a `YourServiceRegion` sztringet az előfizetéséhez társított [régióra](~/articles/cognitive-services/Speech-Service/regions.md) (ez a `westus` régió, ha az ingyenes próbaverzióra regisztrált).

1. Mentse a projekt módosításait.

## <a name="build-and-run-the-app"></a>Az alkalmazás létrehozása és futtatása

1. Hozza létre az alkalmazást. A menüsávban válassza a **Build** > **Build megoldás**elemet. A kód fordításának hiba nélkül kell végbe mennie.

    ![A Visual Studio képernyőképe, amelyen ki van emelve a Megoldás fordítása lehetőség](~/articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-dotnetcore-windows-05-build.png "Sikeres létrehozás")

1. Indítsa el az alkalmazást. A menüsávban **válassza a hibakeresés** > **indítása hibakeresés**lehetőséget, vagy nyomja le az **F5**billentyűt.

    ![A Visual Studio képernyőképe, amelyen ki van emelve a Hibakeresés indítása lehetőség](~/articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-dotnetcore-windows-06-start-debugging.png "Az alkalmazás elindítása hibakereséshez")

1. Megjelenik egy konzolablak, amely felszólítja, hogy írjon be egy szöveget. Írjon be néhány szót vagy mondatot. A beírt szöveg a beszédfelismerési szolgáltatáshoz lesz továbbítva, és a beszédre lett szintetizálva, amely a beszélőn játszik.

    ![A sikeres szintézis után a konzol kimenetének képernyőképe](~/articles/cognitive-services/Speech-Service/media/sdk/qs-tts-csharp-dotnet-windows-console-output.png "A sikeres szintézis után a konzol kimenete")

## <a name="next-steps"></a>További lépések

[!INCLUDE [Speech synthesis basics](../../text-to-speech-next-steps.md)]

## <a name="see-also"></a>Lásd még

- [Egyéni hang létrehozása](~/articles/cognitive-services/Speech-Service/how-to-custom-voice-create-voice.md)
- [Egyéni hangminták rögzítése](~/articles/cognitive-services/Speech-Service/record-custom-voice-samples.md)
