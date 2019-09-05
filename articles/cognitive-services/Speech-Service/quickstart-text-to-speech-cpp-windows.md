---
title: 'Gyors útmutató: Beszédszintetizátor-beszéd, C++ (Windows) – beszédfelismerési szolgáltatás'
titleSuffix: Azure Cognitive Services
description: Megtudhatja, hogyan szintetizálhatja C++ a beszédfelismerést a Windows asztalon a Speech SDK használatával
services: cognitive-services
author: yinhew
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 08/24/2019
ms.author: yinhew
ms.openlocfilehash: 686b21d3e02266af77687778c32f0d1ca6d55154
ms.sourcegitcommit: 49c4b9c797c09c92632d7cedfec0ac1cf783631b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/05/2019
ms.locfileid: "70383075"
---
# <a name="quickstart-synthesize-speech-in-c-on-windows-by-using-the-speech-sdk"></a>Gyors útmutató: Beszédfelismerés a Windows C++ rendszeren a Speech SDK használatával

A [beszédfelismerés](quickstart-cpp-windows.md) és a [beszéd fordítása](quickstart-translate-speech-cpp-windows.md)is elérhető.

Ebben az útmutatóban egy C++ konzolalkalmazást fog létrehozni a Windowshoz. A Cognitive Services [SPEECH SDK](speech-sdk.md) segítségével valós időben szintetizálhatja a beszédet a szövegből, és lejátszhatja a beszédet a számítógép hangszóróján. Az alkalmazás a [SPEECH SDK NuGet csomagjával](https://aka.ms/csspeech/nuget) és a Microsoft Visual Studio 2019-es verziójával (bármely kiadással) készült.

A beszédfelismeréshez elérhető nyelvek és hangok teljes listáját lásd: [nyelvi támogatás](language-support.md#text-to-speech).

## <a name="prerequisites"></a>Előfeltételek

A rövid útmutató elvégzéséhez szüksége lesz egy Speech Services-előfizetési kulcsra. amelyet ingyenesen is beszerezhet. További részletekért tekintse [meg a Speech Services ingyenes kipróbálása](get-started.md) című témakört.

## <a name="create-a-visual-studio-project"></a>Visual Studio-projekt létrehozása

[!INCLUDE [Quickstart C++ project](../../../includes/cognitive-services-speech-service-quickstart-cpp-create-proj.md)]

## <a name="add-sample-code"></a>Mintakód hozzáadása

1. Nyissa meg a **helloworld.cpp** forrásfájlt.

1. Cserélje le az összes kódot a következő kódrészletre:

   [!code-cpp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/text-to-speech/cpp-windows/helloworld/helloworld.cpp#code)]

1. Ugyanabban a fájlban cserélje le a `YourSubscriptionKey` sztringet az előfizetői azonosítóra.

1. Cserélje le a `YourServiceRegion` sztringet az előfizetéséhez társított [régióra](regions.md) (ez a `westus` régió, ha az ingyenes próbaverzióra regisztrált).

1. A menüsávban válassza a **fájl** > **Mentés összes mentése**elemet.

## <a name="build-and-run-the-application"></a>Az alkalmazás fordítása és futtatása

1. Az alkalmazás létrehozásához a > menüsávon **válassza a Build** **Build megoldás** elemet. A kód fordításának hiba nélkül végbe kell mennie.

1. A HelloWorld alkalmazás indításához válassza a hibakeresés indítása hibakeresést (vagy nyomja le az F5 billentyűt). > 

1. Írjon be egy angol kifejezést vagy mondatot. Az alkalmazás továbbítja a szöveget a beszédfelismerési szolgáltatásoknak, amelyek szintetizált beszédet küldenek az alkalmazásnak a beszélőn való lejátszáshoz.

   ![Konzol kimenete a sikeres beszéd-szintézis után](media/sdk/qs-tts-cpp-windows-console-output.png)

## <a name="next-steps"></a>További lépések

A GitHubon további példákat is megtudhat, például hogyan mentheti a beszédet egy hangfájlba.

> [!div class="nextstepaction"]
> [Minták C++ feltárása a githubon](https://aka.ms/csspeech/samples)

## <a name="see-also"></a>Lásd még

- [Egyéni hang létrehozása](how-to-custom-voice-create-voice.md)
- [Egyéni hangminták rögzítése](record-custom-voice-samples.md)
