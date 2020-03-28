---
title: 'Rövid útmutató: Beszédfelismerés mikrofonból, C++ (Windows) – Beszédszolgáltatás'
titleSuffix: Azure Cognitive Services
description: Megtudhatja, hogy miként ismerheti fel a beszédfelismerést a C++ rendszerben a Windows Asztalon a Beszéd SDK használatával
services: cognitive-services
author: wolfma61
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 12/17/2019
ms.author: wolfma
ms.openlocfilehash: 416c2893ebf07cd638f3f1c06c709db2586245d1
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "78925358"
---
## <a name="prerequisites"></a>Előfeltételek

Mielőtt elkezdene járni:

> [!div class="checklist"]
> * [Azure-beszédfelismerési erőforrás létrehozása](../../../../get-started.md)
> * [A fejlesztői környezet beállítása és üres projekt létrehozása](../../../../quickstarts/setup-platform.md?tabs=windows)
> * Győződjön meg arról, hogy rendelkezik a mikrofonhoz a hangrögzítéshez

## <a name="add-sample-code"></a>Mintakód hozzáadása

1. Nyissa meg a **helloworld.cpp** forrásfájlt.

1. Cserélje le az összes kódot a következő kódrészletre:

   [!code-cpp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/cpp/windows/from-microphone/helloworld/helloworld.cpp#code)]

1. Ugyanabban a fájlban cserélje le a `YourSubscriptionKey` sztringet az előfizetői azonosítóra.

1. Cserélje le `YourServiceRegion` a karakterláncot az előfizetéshez társított [régió](https://aka.ms/speech/sdkregion) `westus` **régiórégió régióból** származó régióra (például az ingyenes próba-előfizetéshez).

1. A menüsorban válassza az > **Összes** **fájlmentése**lehetőséget.

> [!NOTE]
> A beszédsdk alapértelmezés szerint felismeri a nyelv en-us használatát, olvassa el a forrásnyelv megadása a [beszéd szöveghez című témakört](../../../../how-to-specify-source-language.md) a forrásnyelv kiválasztásával kapcsolatos információkért.

## <a name="build-and-run-the-application"></a>Az alkalmazás fordítása és futtatása

1. A menüsorban válassza **buildmegoldás összeállítása** > **az** alkalmazás létrehozásához. A kód fordításának hiba nélkül végbe kell mennie.

1. A **helloworld** alkalmazás elindításához válassza a **Debug** > **Start Debugging** (vagy **az F5)** billentyűt.

1. Mondjon ki egy angol nyelvű kifejezést vagy mondatot. Az alkalmazás továbbítja a beszéda beszéd a beszédszolgáltatás, amely átírja a szöveget, és elküldi azt vissza az alkalmazás megjelenítésére.

   ![Konzolkimenet a sikeres felismerés után](~/articles/cognitive-services/Speech-Service/media/sdk/qs-cpp-windows-08-console-output-release.png)

## <a name="next-steps"></a>További lépések

[!INCLUDE [footer](./footer.md)]