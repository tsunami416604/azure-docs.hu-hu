---
title: 'Gyors útmutató: beszédfelismerési hangszintézis, C# (Windows) – Speech Service'
titleSuffix: Azure Cognitive Services
description: Ez az útmutató egy szöveg-beszédes konzol alkalmazás létrehozására használható a .NET-keretrendszer for Windows és a Speech SDK használatával. Ha elkészült, a beszédet a szövegből is szintetizálhatja, és valós időben hallhatja a beszédet a beszélőn.
services: cognitive-services
author: yinhew
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 08/27/2019
ms.author: yinhew
ms.openlocfilehash: 38dac73d253245fce86ac419c7a4eb3a106df3f0
ms.sourcegitcommit: 6c01e4f82e19f9e423c3aaeaf801a29a517e97a0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/04/2019
ms.locfileid: "74818219"
---
## <a name="prerequisites"></a>Előfeltételek

Az első lépések előtt ügyeljen a következőre:

> [!div class="checklist"]
> * [Azure Speech-erőforrás létrehozása](../../../../get-started.md)
> * [A fejlesztési környezet beállítása](../../../../quickstarts/setup-platform.md?tabs=dotnet)
> * [Üres minta projekt létrehozása](../../../../quickstarts/create-project.md?tabs=dotnet)

## <a name="add-sample-code"></a>Mintakód hozzáadása

1. Nyissa meg a **program.cs** , és cserélje le az automatikusan generált kódot a következő mintára:

   [!code-csharp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/csharp/dotnet/text-to-speech/helloworld/Program.cs#code)]

1. Keresse meg a `YourSubscriptionKey`karakterláncot, és cserélje le a Speech Service előfizetési kulcsára.

1. Keresse meg a `YourServiceRegion`karakterláncot, és cserélje le az előfizetéséhez tartozó [régióra](~/articles/cognitive-services/Speech-Service/regions.md) . Ha például az ingyenes próbaverziós előfizetést használja, akkor a régió `westus`.

1. A menüsávban válassza a **fájl** > **az összes mentése**lehetőséget.

## <a name="build-and-run-the-application"></a>Az alkalmazás fordítása és futtatása

1. Az alkalmazás létrehozásához a menüsávon válassza a **build** > **Build megoldás** elemet. A kód fordításának hiba nélkül végbe kell mennie.

1. A **HelloWorld** alkalmazás indításához válassza a hibakeresés > a **hibakeresés megkezdése** (vagy az **F5**kiválasztása **) lehetőséget.**

1. Írjon be egy angol kifejezést vagy mondatot. Az alkalmazás továbbítja a szöveget a beszédfelismerési szolgáltatásnak, amely szintetizált beszédet küld az alkalmazásnak a beszélőn való lejátszáshoz.

   ![Speech szintézis felhasználói felület](~/articles/cognitive-services/Speech-Service/media/sdk/qs-tts-csharp-dotnet-windows-console-output.png)

## <a name="next-steps"></a>Következő lépések

[!INCLUDE [footer](./footer.md)]

## <a name="see-also"></a>Lásd még:

- [Egyéni hang létrehozása](~/articles/cognitive-services/Speech-Service/how-to-custom-voice-create-voice.md)
- [Egyéni hangminták rögzítése](~/articles/cognitive-services/Speech-Service/record-custom-voice-samples.md)
