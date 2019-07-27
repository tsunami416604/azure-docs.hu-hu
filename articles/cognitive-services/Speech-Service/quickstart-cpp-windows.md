---
title: 'Gyors útmutató: Beszédfelismerés felismerése C++ , (Windows) – beszédfelismerési szolgáltatás'
titleSuffix: Azure Cognitive Services
description: Megtudhatja, hogyan ismerheti fel a beszédfelismerést C++ a Windows asztalon a Speech SDK használatával
services: cognitive-services
author: wolfma61
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 07/05/2019
ms.author: wolfma
ms.openlocfilehash: b79d5aeb20301a2e5359ea712017b40ca7279bf0
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/26/2019
ms.locfileid: "68559525"
---
# <a name="quickstart-recognize-speech-in-c-on-windows-by-using-the-speech-sdk"></a>Gyors útmutató: Beszédfelismerés felismerése C++ Windows rendszeren a Speech SDK használatával

A gyors útmutató a [szöveg-beszéd](quickstart-text-to-speech-cpp-windows.md) és a [beszéd fordításához](quickstart-translate-speech-cpp-windows.md)is elérhető.

Ha szükséges, válasszon másik programozási nyelvet és/vagy környezetet:<br/>
[!INCLUDE [Selector](../../../includes/cognitive-services-speech-service-quickstart-selector.md)]

Ebben az útmutatóban egy C++ konzolalkalmazást fog létrehozni a Windowshoz. A Cognitive Services [Speech SDK](speech-sdk.md) használatával a számítógép mikrofonjába beszélve valós időben konvertálhat át beszédet szöveggé. Az alkalmazást a [Speech SDK NuGet-csomaggal](https://aka.ms/csspeech/nuget) és a Microsoft Visual Studio 2017-tel (annak bármely kiadásával) lehet összeállítani.

## <a name="prerequisites"></a>Előfeltételek

A rövid útmutató elvégzéséhez szüksége lesz egy Speech Services-előfizetési kulcsra. amelyet ingyenesen is beszerezhet. További részletekért tekintse [meg a Speech Services ingyenes kipróbálása](get-started.md) című témakört.

## <a name="create-a-visual-studio-project"></a>Visual Studio-projekt létrehozása

[!INCLUDE [Quickstart C++ project](../../../includes/cognitive-services-speech-service-quickstart-cpp-create-proj.md)]

## <a name="add-sample-code"></a>Mintakód hozzáadása

1. Nyissa meg a *helloworld.cpp* forrásfájlt. Cserélje le a kezdeti „include” állítás (`#include "stdafx.h"` vagy `#include "pch.h"`) alatt található teljes kódot a következőre:

   [!code-cpp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/cpp-windows/helloworld/helloworld.cpp#code)]

1. Ugyanabban a fájlban cserélje le a `YourSubscriptionKey` sztringet az előfizetői azonosítóra.

1. Cserélje le a `YourServiceRegion` sztringet az előfizetéséhez társított [régióra](regions.md) (ez a `westus` régió, ha az ingyenes próbaverzióra regisztrált).

1. Mentse a projekt módosításait.

## <a name="build-and-run-the-app"></a>Az alkalmazás létrehozása és futtatása

1. Hozza létre az alkalmazást. A menüsávon válassza a **Létrehozás** > **Megoldás fordítása** elemet. A kód fordításának hiba nélkül kell végbe mennie.

   ![A Visual Studio képernyőképe, amelyen ki van emelve a Megoldás fordítása lehetőség](media/sdk/qs-cpp-windows-06-build.png)

1. Indítsa el az alkalmazást. A menüsávon válassza a **Hibakeresés** > **Hibakeresés indítása** lehetőséget, vagy nyomja le az **F5** billentyűt.

   ![A Visual Studio képernyőképe, amelyen ki van emelve a Hibakeresés indítása lehetőség](media/sdk/qs-cpp-windows-07-start-debugging.png)

1. Megjelenik egy konzolablak, amely arra kéri Önt, hogy mondjon valamit. Mondjon ki egy angol nyelvű kifejezést vagy mondatot. A beszéd a beszédfelismerési szolgáltatásokhoz lett továbbítva, és szövegbe kerül, amely ugyanabban az ablakban jelenik meg.

   ![Képernyőkép a konzolról a sikeres felismerést követően](media/sdk/qs-cpp-windows-08-console-output-release.png)

## <a name="next-steps"></a>További lépések

A GitHubon további minták is elérhetők, például a hangfájlok beszédének olvasása.

> [!div class="nextstepaction"]
> [Minták C++ feltárása a githubon](https://aka.ms/csspeech/samples)

## <a name="see-also"></a>Lásd még

- [Akusztikai modellek testreszabása](how-to-customize-acoustic-models.md)
- [Nyelvi modellek testreszabása](how-to-customize-language-model.md)
