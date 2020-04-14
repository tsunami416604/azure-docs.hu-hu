---
title: 'Rövid útmutató: Beszéd szintetizálása, C# (Windows) – Beszédszolgáltatás'
titleSuffix: Azure Cognitive Services
description: Ez az útmutató a . Ha elkészült, a szövegből szintetizálhatja a beszédet, és valós időben hallhatja a beszédeket a hangszórón.
services: cognitive-services
author: yinhew
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 04/04/2020
ms.author: yinhew
ms.openlocfilehash: 89f491aae91787ec6b67b9aa2daee262f3bab62e
ms.sourcegitcommit: 530e2d56fc3b91c520d3714a7fe4e8e0b75480c8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/14/2020
ms.locfileid: "81275426"
---
## <a name="prerequisites"></a>Előfeltételek

Mielőtt elkezdene, győződjön meg arról, hogy:

> [!div class="checklist"]
> * [Azure-beszédfelismerési erőforrás létrehozása](../../../../get-started.md)
> * [A fejlesztői környezet beállítása és üres projekt létrehozása](../../../../quickstarts/setup-platform.md?tabs=dotnet&pivots=programming-language-csharp)

## <a name="add-sample-code"></a>Mintakód hozzáadása

1. Nyissa **meg Program.cs,** és cserélje le az automatikusan létrehozott kódot erre a mintára:

   [!code-csharp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/csharp/dotnet/text-to-speech/helloworld/Program.cs#code)]

1. Keresse meg `YourSubscriptionKey`a karakterláncot , és cserélje le a beszédszolgáltatás-előfizetési kulcsra.

1. Keresse meg `YourServiceRegion`a karakterláncot , és cserélje le az előfizetéshez társított [régióra.](~/articles/cognitive-services/Speech-Service/regions.md) Ha például az ingyenes próba-előfizetést használja, `westus`a régió a .

1. A menüsorban válassza az > **Összes** **fájlmentése**lehetőséget.

## <a name="build-and-run-the-application"></a>Az alkalmazás fordítása és futtatása

1. A menüsorban válassza a Build megoldás **összeállítása** > **Build Solution** az alkalmazás létrehozásához. A kód fordításának hiba nélkül végbe kell mennie.

1. A **helloworld** alkalmazás elindításához válassza a **Debug** > **Start Debugging** (vagy **az F5**) lehetőséget.

1. Írjon be egy angol kifejezést vagy mondatot. Az alkalmazás továbbítja a szöveget a beszédszolgáltatás, amely elküldi a szintetizált beszéd az alkalmazás játszani a hangszórón.

   ![Beszédszintézis felhasználói felülete](~/articles/cognitive-services/Speech-Service/media/sdk/qs-tts-csharp-dotnet-windows-console-output.png)

## <a name="next-steps"></a>További lépések

[!INCLUDE [Speech synthesis basics](../../text-to-speech-next-steps.md)]

## <a name="see-also"></a>Lásd még

- [Egyéni hang létrehozása](~/articles/cognitive-services/Speech-Service/how-to-custom-voice-create-voice.md)
- [Egyéni hangminták rögzítése](~/articles/cognitive-services/Speech-Service/record-custom-voice-samples.md)
