---
title: 'Gyors útmutató: beszédfelismerési hangszintézis C# (.net Core) – Speech Service'
titleSuffix: Azure Cognitive Services
description: Megtudhatja, hogyan szintetizálhat C# beszédet a .net Core alatt a Windowson a Speech SDK használatával
services: cognitive-services
author: yinhew
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 6/24/2019
ms.author: yinhew
ms.openlocfilehash: 670d32a93df9e2a0363163079b50c7306f81975e
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/04/2019
ms.locfileid: "73502911"
---
> [!NOTE]
> A .NET Core egy nyílt forráskódú, platformfüggetlen .NET-platform, amely implementálja a [.NET Standard](https://docs.microsoft.com/dotnet/standard/net-standard) specifikációt.

## <a name="prerequisites"></a>Előfeltételek

Az első lépések előtt ügyeljen a következőre:

> [!div class="checklist"]
> * [Azure Speech-erőforrás létrehozása](../../../../get-started.md)
> * [A fejlesztési környezet beállítása](../../../../quickstarts/setup-platform.md?tabs=dotnetcore)
> * [Üres minta projekt létrehozása](../../../../quickstarts/create-project.md?tabs=dotnetcore)
## <a name="add-sample-code"></a>Mintakód hozzáadása

1. Nyissa meg a `Program.cs` fájlt, és cserélje le a benne lévő teljes kódot a következőre.

    [!code-csharp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/csharp/dotnetcore/text-to-speech/helloworld/Program.cs#code)]

1. Ugyanabban a fájlban cserélje le a `YourSubscriptionKey` sztringet az előfizetői azonosítóra.

1. Cserélje le a `YourServiceRegion` sztringet az előfizetéséhez társított [régióra](~/articles/cognitive-services/Speech-Service/regions.md) (ez a `westus` régió, ha az ingyenes próbaverzióra regisztrált).

1. Mentse a projekt módosításait.

## <a name="build-and-run-the-app"></a>Az alkalmazás létrehozása és futtatása

1. Hozza létre az alkalmazást. A menüsávon válassza a **Létrehozás** > **Megoldás fordítása** elemet. A kód fordításának hiba nélkül kell végbe mennie.

    ![Képernyőkép a Visual Studio alkalmazásról, kiemelve a megoldás kiemelése lehetőséggel](~/articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-dotnetcore-windows-05-build.png "Sikeres létrehozás")

1. Indítsa el az alkalmazást. A menüsávon válassza a **Hibakeresés** > **Hibakeresés indítása** lehetőséget, vagy nyomja le az **F5** billentyűt.

    ![Képernyőkép a Visual Studio alkalmazásról, a hibakeresés elindítása lehetőség kiemelve](~/articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-dotnetcore-windows-06-start-debugging.png "Az alkalmazás elindítása hibakereséshez")

1. Megjelenik egy konzolablak, amely felszólítja, hogy írjon be egy szöveget. Írjon be néhány szót vagy mondatot. A beírt szöveg továbbítva lesz a Speech Servicesnek, és a beszédbe lett szintetizálva, amely a beszélőn játszik.

    ![A sikeres szintézis után a konzol kimenetének képernyőképe](~/articles/cognitive-services/Speech-Service/media/sdk/qs-tts-csharp-dotnet-windows-console-output.png "A sikeres szintézis után a konzol kimenete")

## <a name="next-steps"></a>További lépések

[!INCLUDE [footer](./footer.md)]

## <a name="see-also"></a>Lásd még

- [Egyéni hang létrehozása](~/articles/cognitive-services/Speech-Service/how-to-custom-voice-create-voice.md)
- [Egyéni hangminták rögzítése](~/articles/cognitive-services/Speech-Service/record-custom-voice-samples.md)
