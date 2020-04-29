---
title: 'Gyors útmutató: beszédfelismerési beszéd, C# (Windows) – Speech Service'
titleSuffix: Azure Cognitive Services
description: Ez az útmutató egy szöveg-beszédes konzol alkalmazás létrehozására használható a .NET-keretrendszer for Windows és a Speech SDK használatával. Ha elkészült, a beszédet a szövegből is szintetizálhatja, és valós időben hallhatja a beszédet a beszélőn.
services: cognitive-services
author: yinhew
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 04/04/2020
ms.author: yinhew
ms.openlocfilehash: 89f491aae91787ec6b67b9aa2daee262f3bab62e
ms.sourcegitcommit: 34a6fa5fc66b1cfdfbf8178ef5cdb151c97c721c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "81275426"
---
## <a name="prerequisites"></a>Előfeltételek

Az első lépések előtt ügyeljen a következőre:

> [!div class="checklist"]
> * [Azure Speech-erőforrás létrehozása](../../../../get-started.md)
> * [Állítsa be a fejlesztési környezetet, és hozzon létre egy üres projektet](../../../../quickstarts/setup-platform.md?tabs=dotnet&pivots=programming-language-csharp)

## <a name="add-sample-code"></a>Mintakód hozzáadása

1. Nyissa meg a **program.cs** , és cserélje le az automatikusan generált kódot a következő mintára:

   [!code-csharp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/csharp/dotnet/text-to-speech/helloworld/Program.cs#code)]

1. Keresse meg a `YourSubscriptionKey`karakterláncot, és cserélje le a beszédfelismerési szolgáltatás előfizetési kulcsára.

1. Keresse meg a `YourServiceRegion`karakterláncot, és cserélje le az előfizetéséhez tartozó [régióra](~/articles/cognitive-services/Speech-Service/regions.md) . Ha például az ingyenes próba-előfizetést használja, a régió a következő `westus`:.

1. A menüsávban válassza a **fájl** > **Mentés összes mentése**elemet.

## <a name="build-and-run-the-application"></a>Az alkalmazás fordítása és futtatása

1. Az alkalmazás létrehozásához a menüsávon válassza a **Build** > **Build megoldás** elemet. A kód fordításának hiba nélkül végbe kell mennie.

1.  > A **HelloWorld** alkalmazás **indításához válassza a hibakeresés****indítása hibakeresést** (vagy válassza az **F5 billentyűt**).

1. Írjon be egy angol kifejezést vagy mondatot. Az alkalmazás továbbítja a szöveget a beszédfelismerési szolgáltatásnak, amely szintetizált beszédet küld az alkalmazásnak a beszélőn való lejátszáshoz.

   ![Speech szintézis felhasználói felület](~/articles/cognitive-services/Speech-Service/media/sdk/qs-tts-csharp-dotnet-windows-console-output.png)

## <a name="next-steps"></a>További lépések

[!INCLUDE [Speech synthesis basics](../../text-to-speech-next-steps.md)]

## <a name="see-also"></a>Lásd még

- [Egyéni hang létrehozása](~/articles/cognitive-services/Speech-Service/how-to-custom-voice-create-voice.md)
- [Egyéni hangminták rögzítése](~/articles/cognitive-services/Speech-Service/record-custom-voice-samples.md)
