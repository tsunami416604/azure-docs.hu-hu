---
author: IEvangelist
ms.service: cognitive-services
ms.topic: include
ms.date: 04/03/2020
ms.author: dapine
ms.openlocfilehash: 9e7716acfe95371c9e9734b6962b66c405bd47e4
ms.sourcegitcommit: 0450ed87a7e01bbe38b3a3aea2a21881f34f34dd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/03/2020
ms.locfileid: "80658447"
---
## <a name="prerequisites"></a>Előfeltételek

Mielőtt elkezdene járni:

> [!div class="checklist"]
> * <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesSpeechServices" target="_blank">Azure Speech-erőforrás létrehozása<span class="docon docon-navigate-external x-hidden-focus"></span></a>
> * [A fejlesztői környezet beállítása és üres projekt létrehozása](../../../../quickstarts/setup-platform.md?tabs=windows)
> * Győződjön meg arról, hogy rendelkezik a mikrofonhoz a hangrögzítéshez

## <a name="source-code"></a>Forráskód

Hozzon létre egy *helloworld.cpp*nevű C++ forrásfájlt, és illessze be a következő kódot.

[!code-cpp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/cpp/windows/from-microphone/helloworld/helloworld.cpp#code)]

[!INCLUDE [replace key and region](../replace-key-and-region.md)]

## <a name="code-explanation"></a>Kód magyarázata

[!INCLUDE [code explanation](../code-explanation.md)]

## <a name="build-and-run-app"></a>Alkalmazás létrehozása és futtatása

1. A menüsorban válassza **buildmegoldás összeállítása** > **az** alkalmazás létrehozásához. A kód fordításának hiba nélkül végbe kell mennie.

1. A **helloworld** alkalmazás elindításához válassza a **Debug** > **Start Debugging** (vagy <kbd>az F5)</kbd>billentyűt.

1. Mondjon ki egy angol nyelvű kifejezést vagy mondatot. Az alkalmazás továbbítja a beszéda beszéd a beszédszolgáltatás, amely átírja a szöveget, és elküldi azt vissza az alkalmazás megjelenítésére.

   ![Konzolkimenet a sikeres felismerés után](~/articles/cognitive-services/Speech-Service/media/sdk/qs-cpp-windows-08-console-output-release.png)

## <a name="next-steps"></a>További lépések

[!INCLUDE [footer](../footer.md)]