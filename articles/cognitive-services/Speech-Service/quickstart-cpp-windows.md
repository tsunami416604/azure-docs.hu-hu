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
ms.date: 08/19/2019
ms.author: wolfma
ms.openlocfilehash: a9c43e1d27a396a2c3e9123ce5ce538296c6870c
ms.sourcegitcommit: 49c4b9c797c09c92632d7cedfec0ac1cf783631b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/05/2019
ms.locfileid: "70381911"
---
# <a name="quickstart-recognize-speech-in-c-on-windows-by-using-the-speech-sdk"></a>Gyors útmutató: Beszédfelismerés felismerése C++ Windows rendszeren a Speech SDK használatával

A [beszédfelismerés](quickstart-text-to-speech-cpp-windows.md) és a [beszéd fordítása](quickstart-translate-speech-cpp-windows.md)is elérhető.

Ha szeretné, válasszon másik programozási nyelvet és környezetet:<br/>
[!INCLUDE [Selector](../../../includes/cognitive-services-speech-service-quickstart-selector.md)]

Ebben az útmutatóban egy C++ konzolalkalmazást fog létrehozni a Windowshoz. A Cognitive Services [Speech SDK](speech-sdk.md) használatával a számítógép mikrofonjába beszélve valós időben konvertálhat át beszédet szöveggé. Az alkalmazás a [SPEECH SDK NuGet csomagjával](https://aka.ms/csspeech/nuget) és a Microsoft Visual Studio 2019-es verziójával (bármely kiadással) készült.

## <a name="prerequisites"></a>Előfeltételek

A rövid útmutató elvégzéséhez szüksége lesz egy Speech Services-előfizetési kulcsra. amelyet ingyenesen is beszerezhet. További részletekért tekintse [meg a Speech Services ingyenes kipróbálása](get-started.md) című témakört.

## <a name="create-a-visual-studio-project"></a>Visual Studio-projekt létrehozása

[!INCLUDE [Quickstart C++ project](../../../includes/cognitive-services-speech-service-quickstart-cpp-create-proj.md)]

## <a name="add-sample-code"></a>Mintakód hozzáadása

1. Nyissa meg a **helloworld.cpp** forrásfájlt.

1. Cserélje le az összes kódot a következő kódrészletre:

   [!code-cpp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/cpp-windows/helloworld/helloworld.cpp#code)]

1. Ugyanabban a fájlban cserélje le a `YourSubscriptionKey` sztringet az előfizetői azonosítóra.

1. Cserélje le a `YourServiceRegion` sztringet az előfizetéséhez társított [régióra](regions.md) (ez a `westus` régió, ha az ingyenes próbaverzióra regisztrált).

1. A menüsávban válassza a **fájl** > **Mentés összes mentése**elemet.

## <a name="build-and-run-the-application"></a>Az alkalmazás fordítása és futtatása

1. Az alkalmazás létrehozásához a > menüsávon **válassza a Build** **Build megoldás** elemet. A kód fordításának hiba nélkül végbe kell mennie.

1. A HelloWorld alkalmazás indításához válassza a hibakeresés indítása hibakeresést (vagy nyomja le az F5 billentyűt). > 

1. Mondjon ki egy angol nyelvű kifejezést vagy mondatot. Az alkalmazás továbbítja a beszédet a Speech Services szolgáltatásnak, amely szöveget ad vissza, és visszaküldi az alkalmazásnak a megjelenítéshez.

   ![A sikeres felismerést követően a konzol kimenete](media/sdk/qs-cpp-windows-08-console-output-release.png)

## <a name="next-steps"></a>További lépések

A GitHubon további minták is elérhetők, például a hangfájlok beszédének olvasása.

> [!div class="nextstepaction"]
> [Minták C++ feltárása a githubon](https://aka.ms/csspeech/samples)

## <a name="see-also"></a>Lásd még

- [Custom Speech modell betanítása](how-to-custom-speech-train-model.md)
