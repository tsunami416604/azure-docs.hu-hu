---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 04/04/2020
ms.author: trbye
ms.openlocfilehash: 885dc178c6c0bfed5c49b8d9e88792a1072b0f10
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81400529"
---
## <a name="prerequisites"></a>Előfeltételek

Mielőtt elkezdene, győződjön meg arról, hogy:

> [!div class="checklist"]
> * [Azure Speech-erőforrás létrehozása](../../../../get-started.md)
> * [A fejlesztői környezet beállítása és üres projekt létrehozása](../../../../quickstarts/setup-platform.md?tabs=windows&pivots=programming-language-cpp)

[!INCLUDE [Audio input format](~/articles/cognitive-services/speech-service/includes/audio-input-format-chart.md)]

## <a name="add-sample-code"></a>Mintakód hozzáadása

1. Nyissa meg a **helloworld.cpp** forrásfájlt.

1. Cserélje le az összes kódot a következő kódrészletre:
   
   [!code-cpp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/cpp/windows/from-file/helloworld/helloworld.cpp#code)]

1. Ugyanabban a fájlban cserélje le a `YourSubscriptionKey` sztringet az előfizetői azonosítóra.

1. Cserélje le `YourServiceRegion` a karakterláncot az előfizetéshez társított [régió](https://aka.ms/speech/sdkregion) `westus` **régiórégió régióból** származó régióra (például az ingyenes próba-előfizetéshez).

1. Cserélje le `whatstheweatherlike.wav` a karakterláncot a saját fájlnevére.

1. A menüsorban válassza az > **Összes** **fájlmentése**lehetőséget.

> [!NOTE]
> A beszédsdk alapértelmezés szerint felismeri a nyelv en-us használatát, olvassa el a forrásnyelv megadása a [beszéd szöveghez című témakört](../../../../how-to-specify-source-language.md) a forrásnyelv kiválasztásával kapcsolatos információkért.

## <a name="build-and-run-the-application"></a>Az alkalmazás fordítása és futtatása

1. A menüsorban válassza **buildmegoldás összeállítása** > **az** alkalmazás létrehozásához. A kód fordításának hiba nélkül végbe kell mennie.

1. A **helloworld** alkalmazás elindításához válassza a **Debug** > **Start Debugging** (vagy **az F5)** billentyűt.

1. A hangfájl továbbítása a beszédfelismerési szolgáltatásba kerül, és a fájl első kimondott szöveggé lesz átírva, amely ugyanabban az ablakban jelenik meg.

   ```text
   Recognizing first result...
   We recognized: What's the weather like?
   ```

## <a name="next-steps"></a>További lépések

[!INCLUDE [Speech recognition basics](../../speech-to-text-next-steps.md)]