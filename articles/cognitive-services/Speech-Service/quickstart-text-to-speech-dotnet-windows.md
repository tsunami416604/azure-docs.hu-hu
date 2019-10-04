---
title: 'Gyors útmutató: Beszédszintetizátor beszéd, .NET-keretrendszer (Windows) – Speech Service'
titleSuffix: Azure Cognitive Services
description: Ez az útmutató egy szöveg-beszédes konzol alkalmazás létrehozására használható a .NET-keretrendszer for Windows és a Speech SDK használatával. Ha elkészült, a beszédet a szövegből is szintetizálhatja, és valós időben hallhatja a beszédet a beszélőn.
services: cognitive-services
author: yinhew
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 08/27/2019
ms.author: yinhew
ms.openlocfilehash: 1a411455e4a6dea22e092cdfc8e70ee23b656435
ms.sourcegitcommit: e9936171586b8d04b67457789ae7d530ec8deebe
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/27/2019
ms.locfileid: "71327457"
---
# <a name="quickstart-synthesize-speech-with-the-speech-sdk-for-net-framework-windows"></a>Gyors útmutató: Beszéd szintézise a .NET-keretrendszerhez készült Speech SDK (Windows) használatával

A [beszédfelismerés](quickstart-csharp-dotnet-windows.md) és a [beszéd fordítása](quickstart-translate-speech-dotnetframework-windows.md)is elérhető.

Ez az útmutató egy szöveg-beszédes konzol alkalmazás létrehozására használható a .NET-keretrendszer for Windows és a Speech SDK használatával. Ha elkészült, a beszédet a szövegből is szintetizálhatja, és valós időben hallhatja a beszédet a beszélőn.

Egy gyors bemutatóhoz (a Visual Studio-projekt létrehozása nélkül, a jelen cikkben leírtak szerint) szerezze be a legfrissebb [Cognitive Services SPEECH SDK-mintákat](https://github.com/Azure-Samples/cognitive-services-speech-sdk) a githubról.

## <a name="prerequisites"></a>Előfeltételek

A projekt teljesítéséhez a következők szükségesek:

* [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/).
* A beszédfelismerési szolgáltatáshoz tartozó előfizetési kulcs. [Szerezze be az egyiket ingyenesen](get-started.md).
* Egy beszélő (vagy fülhallgató) érhető el.

## <a name="create-a-visual-studio-project"></a>Visual Studio-projekt létrehozása

[!INCLUDE [Create project](../../../includes/cognitive-services-speech-service-create-speech-project-vs-csharp.md)]

## <a name="add-sample-code"></a>Mintakód hozzáadása

1. Nyissa meg a **program.cs** , és cserélje le az automatikusan generált kódot a következő mintára:

   [!code-csharp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/text-to-speech/csharp-dotnet-windows/helloworld/Program.cs#code)]

1. Keresse meg a `YourSubscriptionKey` karakterláncot, és cserélje le a Speech Services előfizetési kulcsára.

1. Keresse meg a `YourServiceRegion` karakterláncot, és cserélje le az előfizetéshez társított [régióra](regions.md) . Ha például az ingyenes próbaverziós előfizetést használja, akkor a régió `westus`.

1. A menüsávban válassza a **fájl**@no__t – 1**Mentés összes mentése**elemet.

## <a name="build-and-run-the-application"></a>Az alkalmazás fordítása és futtatása

1. A menüsávban válassza a **build** > **Build megoldás** elemet az alkalmazás létrehozásához. A kód fordításának hiba nélkül végbe kell mennie.

1. A **HelloWorld** alkalmazás indításához válassza a **hibakeresés @no__t –** 1**Indítás hibakeresése** lehetőséget (vagy válassza az **F5 billentyűt**).

1. Írjon be egy angol kifejezést vagy mondatot. Az alkalmazás továbbítja a szöveget a beszédfelismerési szolgáltatásoknak, amelyek szintetizált beszédet küldenek az alkalmazásnak a beszélőn való lejátszáshoz.

   ![Speech szintézis felhasználói felület](media/sdk/qs-tts-csharp-dotnet-windows-console-output.png)

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Minták C# feltárása a githubon](https://aka.ms/csspeech/samples)

## <a name="see-also"></a>Lásd még

- [Egyéni hang létrehozása](how-to-custom-voice-create-voice.md)
- [Egyéni hangminták rögzítése](record-custom-voice-samples.md)
