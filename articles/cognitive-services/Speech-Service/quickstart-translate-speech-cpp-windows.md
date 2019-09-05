---
title: 'Gyors útmutató: Beszéd fordítása C++ , (Windows) – beszédfelismerési szolgáltatás'
titleSuffix: Azure Cognitive Services
description: Ebben a rövid útmutatóban létrehoz egy C++ alkalmazást, amely rögzíti a felhasználói beszédet, lefordítja egy másik nyelvre, és kiírja a szöveget a parancssorba. Ez az útmutató Windows-felhasználók számára készült.
services: cognitive-services
author: wolfma61
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 08/24/2019
ms.author: erhopf
ms.openlocfilehash: 6ba4e44efc7ff24aa48f9f16840b2248423f7241
ms.sourcegitcommit: 49c4b9c797c09c92632d7cedfec0ac1cf783631b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/05/2019
ms.locfileid: "70382682"
---
# <a name="quickstart-translate-speech-in-c-on-windows-by-using-the-speech-sdk"></a>Gyors útmutató: Beszédfelismerés a Windows C++ rendszeren a Speech SDK használatával

A [beszédfelismerést](quickstart-cpp-windows.md) és a [beszédfelismerést](quickstart-text-to-speech-cpp-windows.md)is elérhetővé teszi.

Ebben a rövid útmutatóban egy olyan C++ alkalmazást fog létrehozni, amely rögzíti a felhasználói beszédet a számítógép mikrofonjában, lefordítja a beszédet, és valós időben írja át a lefordított szöveget a parancssorba. Ez az alkalmazás a [SPEECH SDK NuGet csomagjával](https://aka.ms/csspeech/nuget) és a Microsoft Visual Studio 2019 (bármely kiadással) készült.

A beszédfelismeréshez elérhető nyelvek teljes listáját a [nyelvi támogatás](language-support.md)című témakörben tekintheti meg.

## <a name="prerequisites"></a>Előfeltételek

A rövid útmutató elvégzéséhez szüksége lesz egy Speech Services-előfizetési kulcsra. amelyet ingyenesen is beszerezhet. További részletekért tekintse [meg a Speech Services ingyenes kipróbálása](get-started.md) című témakört.

## <a name="create-a-visual-studio-project"></a>Visual Studio-projekt létrehozása

[!INCLUDE [Quickstart C++ project](../../../includes/cognitive-services-speech-service-quickstart-cpp-create-proj.md)]

## <a name="add-sample-code"></a>Mintakód hozzáadása

1. Nyissa meg a **helloworld.cpp** forrásfájlt.

1. Cserélje le az összes kódot a következő kódrészletre:

   [!code-cpp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/speech-translation/cpp-windows/helloworld/helloworld.cpp?range=2-#code)]

1. Ugyanabban a fájlban cserélje le a `YourSubscriptionKey` sztringet az előfizetői azonosítóra.

1. Cserélje le a `YourServiceRegion` sztringet az előfizetéséhez társított [régióra](regions.md) (ez a `westus` régió, ha az ingyenes próbaverzióra regisztrált).

1. A menüsávban válassza a **fájl** > **Mentés összes mentése**elemet.

## <a name="build-and-run-the-application"></a>Az alkalmazás fordítása és futtatása

1. Az alkalmazás létrehozásához a > menüsávon **válassza a Build** **Build megoldás** elemet. A kód fordításának hiba nélkül végbe kell mennie.

1. A HelloWorld alkalmazás indításához válassza a hibakeresés indítása hibakeresést (vagy nyomja le az F5 billentyűt). > 

1. Mondjon ki egy angol nyelvű kifejezést vagy mondatot. Az alkalmazás elküldi a beszédét a Speech Services szolgáltatásnak, amely szöveget (ebben az esetben francia és német nyelven) fordít le és mutat be. A Speech Services ezután visszaküldi a szöveget az alkalmazásnak a megjelenítéshez.

   ![Konzol kimenete a sikeres beszéd fordítás után](media/sdk/qs-translate-cpp-windows-output.png)

## <a name="next-steps"></a>További lépések

További minták, például a hangfájlok beszédének beolvasása vagy a fordítás szövegének szintetizált beszédbe való bekapcsolása a GitHubon érhető el.

> [!div class="nextstepaction"]
> [Minták C++ feltárása a githubon](https://aka.ms/csspeech/samples)

## <a name="see-also"></a>Lásd még

- [Custom Speech modell betanítása](how-to-custom-speech-train-model.md)
