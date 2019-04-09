---
title: 'Gyors útmutató: Beszéd, a C++ (Windows) – beszédszolgáltatások szintetizálásához'
titleSuffix: Azure Cognitive Services
description: Ismerje meg, hogyan beszéd, a Windows asztalon C++ szintetizálásához a Speech SDK-val
services: cognitive-services
author: yinhew
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 4/03/2019
ms.author: yinhew
ms.openlocfilehash: 732816ce10836d5828e7f325a3eb3fe31627d4f4
ms.sourcegitcommit: e43ea344c52b3a99235660960c1e747b9d6c990e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/04/2019
ms.locfileid: "59012240"
---
# <a name="quickstart-synthesize-speech-in-c-on-windows-by-using-the-speech-sdk"></a>Gyors útmutató: Beszéd, a C++, a Windows szintetizálásához a Speech SDK-val

Ebben az útmutatóban egy C++ konzolalkalmazást fog létrehozni a Windowshoz. A Cognitive Services használata [beszéd SDK](speech-sdk.md) szintetizálásához beszéd valós idejű szöveg és a speech játsszon a PC-hangszóró. Az alkalmazást a [Speech SDK NuGet-csomaggal](https://aka.ms/csspeech/nuget) és a Microsoft Visual Studio 2017-tel (annak bármely kiadásával) lehet összeállítani.

Az ebben a cikkben leírt szolgáltatás érhető el [beszéd SDK 1.4.0-s](https://www.nuget.org/packages/Microsoft.CognitiveServices.Speech/1.4.0).

A beszédszintézishez elérhető nyelvek/beszédhangot teljes listáját lásd: [nyelvi támogatás](language-support.md#text-to-speech).

## <a name="prerequisites"></a>Előfeltételek

A rövid útmutató elvégzéséhez beszédszolgáltatások előfizetési kulcs szükséges. amelyet ingyenesen is beszerezhet. Lásd: [próbálja ki ingyenesen a beszédszolgáltatások](get-started.md) részleteiről.

## <a name="create-a-visual-studio-project"></a>Visual Studio-projekt létrehozása

[!INCLUDE [](../../../includes/cognitive-services-speech-service-quickstart-cpp-create-proj.md)]

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

1. A konzolablakban megjelenik, írjon be valamilyen szöveget, és felszólítja. Írjon be néhány szavakat és a egy mondatot. A beírt szöveget a beszédszolgáltatások továbbítani, és a Speech, amely a a hangfelismerő játszik synthesized.

   ![Képernyőfelvétel a konzol kimenete a sikeres összefoglaló után](media/sdk/qs-tts-cpp-windows-console-output.png)

## <a name="next-steps"></a>További lépések

További példákat, például a következőkkel speech hangfájl, mentse a Githubon érhetők el.

> [!div class="nextstepaction"]
> [Ismerkedés a C++-mintákat a Githubon](https://aka.ms/csspeech/samples)

## <a name="see-also"></a>Lásd még

- [Hangtípusok testreszabása](how-to-customize-voice-font.md)
- [Hangminták felvétele](record-custom-voice-samples.md)
