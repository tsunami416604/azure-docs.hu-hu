---
title: 'Gyors útmutató: Beszéd fordítása C++ , (Windows) – beszédfelismerési szolgáltatás'
titleSuffix: Azure Cognitive Services
description: Ebben a rövid útmutatóban egy egyszerű C++ alkalmazást hoz létre a felhasználói beszéd rögzítéséhez, lefordíthatja azt egy másik nyelvre, és kiírja a szöveget a parancssorba. Ez az útmutató Windows-felhasználók számára készült.
services: cognitive-services
author: wolfma61
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 07/05/2019
ms.author: erhopf
ms.openlocfilehash: 379087ca94eee6ce3d45bfd97b4771c5f08d6333
ms.sourcegitcommit: 6cff17b02b65388ac90ef3757bf04c6d8ed3db03
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/29/2019
ms.locfileid: "68607698"
---
# <a name="quickstart-translate-speech-with-the-speech-sdk-for-c"></a>Gyors útmutató: Beszéd fordítása a Speech SDK-valC++

A gyors üzembe helyezési útmutató [](quickstart-cpp-windows.md) a beszédfelismeréshez és a [szöveg-beszéd](quickstart-text-to-speech-cpp-windows.md)megjelenítéshez is elérhető.

Ebben a rövid útmutatóban egy egyszerű C++ alkalmazást fog létrehozni, amely rögzíti a felhasználói beszédet a számítógép mikrofonjában, lefordítja a beszédet, és valós időben írja át a lefordított szöveget a parancssorba. Ez az alkalmazás a 64 bites Windows rendszeren fut, és a [SPEECH SDK NuGet csomaggal](https://aka.ms/csspeech/nuget) és a Microsoft Visual Studio 2017-es vagy újabb verziójával készült.

A beszédfelismeréshez elérhető nyelvek teljes listáját a [nyelvi támogatás](language-support.md)című témakörben tekintheti meg.

## <a name="prerequisites"></a>Előfeltételek

Ehhez a rövid útmutatóhoz a következőkre van szükség:

* [Visual Studio 2017](https://visualstudio.microsoft.com/downloads/) vagy újabb
* Egy Azure-előfizetési kulcs a beszédfelismerési szolgáltatáshoz. [Szerezze be az egyiket ingyenesen](get-started.md).

## <a name="create-a-visual-studio-project"></a>Visual Studio-projekt létrehozása

[!INCLUDE [Quickstart C++ project](../../../includes/cognitive-services-speech-service-quickstart-cpp-create-proj.md)]

## <a name="add-sample-code"></a>Mintakód hozzáadása

1. Nyissa meg a *helloworld.cpp* forrásfájlt. Cserélje le a kezdeti „include” állítás (`#include "stdafx.h"` vagy `#include "pch.h"`) alatt található teljes kódot a következőre:

    [!code-cpp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/speech-translation/cpp-windows/helloworld/helloworld.cpp#code)]

1. Ugyanabban a fájlban cserélje le a `YourSubscriptionKey` sztringet az előfizetői azonosítóra.

1. Cserélje le a `YourServiceRegion` sztringet az előfizetéséhez társított [régióra](regions.md) (ez a `westus` régió, ha az ingyenes próbaverzióra regisztrált).

1. Mentse a projekt módosításait.

## <a name="build-and-run-the-app"></a>Az alkalmazás létrehozása és futtatása

1. Hozza létre az alkalmazást. A menüsávon válassza a **Létrehozás** > **Megoldás fordítása** elemet. A kód fordításának hiba nélkül kell végbe mennie.

   ![A Visual Studio képernyőképe, amelyen ki van emelve a Megoldás fordítása lehetőség](media/sdk/qs-cpp-windows-06-build.png)

1. Indítsa el az alkalmazást. A menüsávon válassza a **Hibakeresés** > **Hibakeresés indítása** lehetőséget, vagy nyomja le az **F5** billentyűt.

   ![A Visual Studio képernyőképe, amelyen ki van emelve a Hibakeresés indítása lehetőség](media/sdk/qs-cpp-windows-07-start-debugging.png)

1. Megjelenik egy konzolablak, amely arra kéri Önt, hogy mondjon valamit. Mondjon ki egy angol nyelvű kifejezést vagy mondatot. A beszédet a beszédfelismerési szolgáltatás továbbítja, lefordítva és szövegbe átirata, amely ugyanabban az ablakban jelenik meg.

   ![A sikeres fordítás után a konzol kimenetének képernyőképe](media/sdk/qs-translate-cpp-windows-output.png)

## <a name="next-steps"></a>További lépések

További minták, mint például a hangfájlok beszédének beolvasása és a lefordított szöveg szintetizált beszédként való kiírása, a GitHubon érhetők el.

> [!div class="nextstepaction"]
> [Minták C++ feltárása a githubon](https://aka.ms/csspeech/samples)

## <a name="see-also"></a>Lásd még

- [Akusztikai modellek testreszabása](how-to-customize-acoustic-models.md)
- [Nyelvi modellek testreszabása](how-to-customize-language-model.md)
