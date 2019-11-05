---
title: 'Gyors útmutató: beszédfelismerési hangszintézis, C++ (Windows) – Speech Service'
titleSuffix: Azure Cognitive Services
description: Megtudhatja, hogyan szintetizálhatja C++ a beszédfelismerést a Windows asztalon a Speech SDK használatával
services: cognitive-services
author: yinhew
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 08/24/2019
ms.author: yinhew
ms.openlocfilehash: c0666dccbb7f110e122c4632995299a414bf690a
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/04/2019
ms.locfileid: "73503373"
---
## <a name="prerequisites"></a>Előfeltételek

Az első lépések előtt ügyeljen a következőre:

> [!div class="checklist"]
> * [Azure Speech-erőforrás létrehozása](../../../../get-started.md)
> * [A fejlesztési környezet beállítása](../../../../quickstarts/setup-platform.md?tabs=windows)
> * [Üres minta projekt létrehozása](../../../../quickstarts/create-project.md?tabs=windows)

## <a name="add-sample-code"></a>Mintakód hozzáadása

1. Nyissa meg a **helloworld.cpp** forrásfájlt.

1. Cserélje le az összes kódot a következő kódrészletre:

   [!code-cpp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/cpp/windows/text-to-speech/helloworld/helloworld.cpp#code)]

1. Ugyanabban a fájlban cserélje le a `YourSubscriptionKey` sztringet az előfizetői azonosítóra.

1. Cserélje le a `YourServiceRegion` sztringet az előfizetéséhez társított [régióra](~/articles/cognitive-services/Speech-Service/regions.md) (ez a `westus` régió, ha az ingyenes próbaverzióra regisztrált).

1. A menüsávban válassza a **fájl** > **az összes mentése**lehetőséget.

## <a name="build-and-run-the-application"></a>Az alkalmazás fordítása és futtatása

1. A menüsávban válassza a **build** > Build **megoldás** elemet az alkalmazás létrehozásához. A kód fordításának hiba nélkül végbe kell mennie.

1. A **HelloWorld** alkalmazás **indításához válassza a hibakeresés > ** a **hibakeresés indítása** (vagy az **F5**billentyű lenyomása) lehetőséget.

1. Írjon be egy angol kifejezést vagy mondatot. Az alkalmazás továbbítja a szöveget a beszédfelismerési szolgáltatásoknak, amelyek szintetizált beszédet küldenek az alkalmazásnak a beszélőn való lejátszáshoz.

   ![Konzol kimenete a sikeres beszéd-szintézis után](~/articles/cognitive-services/Speech-Service/media/sdk/qs-tts-cpp-windows-console-output.png)

## <a name="next-steps"></a>További lépések

[!INCLUDE [footer](./footer.md)]

## <a name="see-also"></a>Lásd még

- [Egyéni hang létrehozása](~/articles/cognitive-services/Speech-Service/how-to-custom-voice-create-voice.md)
- [Egyéni hangminták rögzítése](~/articles/cognitive-services/Speech-Service/record-custom-voice-samples.md)
