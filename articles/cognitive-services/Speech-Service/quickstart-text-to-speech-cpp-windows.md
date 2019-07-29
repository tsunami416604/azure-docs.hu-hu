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
ms.date: 07/05/2019
ms.author: yinhew
ms.openlocfilehash: fdc5e540dd7e84d47aa139768fbfc2614f38b376
ms.sourcegitcommit: 6cff17b02b65388ac90ef3757bf04c6d8ed3db03
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/29/2019
ms.locfileid: "68607711"
---
# <a name="quickstart-synthesize-speech-in-c-on-windows-by-using-the-speech-sdk"></a>Gyors útmutató: Beszédfelismerés a Windows C++ rendszeren a Speech SDK használatával

A gyors útmutatók a [beszédfelismerés](quickstart-cpp-windows.md) és a [beszédfelismerés](quickstart-translate-speech-cpp-windows.md)számára is elérhetők.

Ebben az útmutatóban egy C++ konzolalkalmazást fog létrehozni a Windowshoz. A Cognitive Services [SPEECH SDK](speech-sdk.md) segítségével valós időben szintetizálhatja a beszédet a szövegből, és lejátszhatja a beszédet a számítógép hangszóróján. Az alkalmazás a [SPEECH SDK NuGet csomagjával](https://aka.ms/csspeech/nuget) és a Microsoft Visual Studio 2017-es vagy újabb verziójával (bármely kiadással) készült.

A cikkben ismertetett funkció a [SPEECH SDK 1.5.0](https://www.nuget.org/packages/Microsoft.CognitiveServices.Speech/1.5.0)-ből érhető el.

A beszédfelismeréshez elérhető nyelvek és hangok teljes listáját lásd: [nyelvi támogatás](language-support.md#text-to-speech).

## <a name="prerequisites"></a>Előfeltételek

A rövid útmutató elvégzéséhez szüksége lesz egy Speech Services-előfizetési kulcsra. amelyet ingyenesen is beszerezhet. További részletekért tekintse [meg a Speech Services ingyenes kipróbálása](get-started.md) című témakört.

## <a name="create-a-visual-studio-project"></a>Visual Studio-projekt létrehozása

[!INCLUDE [Quickstart C++ project](../../../includes/cognitive-services-speech-service-quickstart-cpp-create-proj.md)]

## <a name="add-sample-code"></a>Mintakód hozzáadása

1. Nyissa meg a *helloworld.cpp* forrásfájlt. Cserélje le a kezdeti „include” állítás (`#include "stdafx.h"` vagy `#include "pch.h"`) alatt található teljes kódot a következőre:

   [!code-cpp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/text-to-speech/cpp-windows/helloworld/helloworld.cpp#code)]

1. Ugyanabban a fájlban cserélje le a `YourSubscriptionKey` sztringet az előfizetői azonosítóra.

1. Cserélje le a `YourServiceRegion` sztringet az előfizetéséhez társított [régióra](regions.md) (ez a `westus` régió, ha az ingyenes próbaverzióra regisztrált).

1. Mentse a projekt módosításait.

## <a name="build-and-run-the-app"></a>Az alkalmazás létrehozása és futtatása

1. Hozza létre az alkalmazást. A menüsávon válassza a **Létrehozás** > **Megoldás fordítása** elemet. A kód fordításának hiba nélkül kell végbe mennie.

   ![A Visual Studio képernyőképe, amelyen ki van emelve a Megoldás fordítása lehetőség](media/sdk/qs-cpp-windows-06-build.png)

1. Indítsa el az alkalmazást. A menüsávon válassza a **Hibakeresés** > **Hibakeresés indítása** lehetőséget, vagy nyomja le az **F5** billentyűt.

   ![A Visual Studio képernyőképe, amelyen ki van emelve a Hibakeresés indítása lehetőség](media/sdk/qs-cpp-windows-07-start-debugging.png)

1. Megjelenik egy konzolablak, amely felszólítja, hogy írjon be egy szöveget. Írjon be néhány szót vagy mondatot. A beírt szöveg továbbítva lesz a Speech Servicesnek, és a beszédbe lett szintetizálva, amely a beszélőn játszik.

   ![A sikeres szintézis után a konzol kimenetének képernyőképe](media/sdk/qs-tts-cpp-windows-console-output.png)

## <a name="next-steps"></a>További lépések

A GitHubon további példákat is megtudhat, például hogyan mentheti a beszédet egy hangfájlba.

> [!div class="nextstepaction"]
> [Minták C++ feltárása a githubon](https://aka.ms/csspeech/samples)

## <a name="see-also"></a>Lásd még

- [Hangbetűkészletek testreszabása](how-to-customize-voice-font.md)
- [Hangminták rögzítése](record-custom-voice-samples.md)
