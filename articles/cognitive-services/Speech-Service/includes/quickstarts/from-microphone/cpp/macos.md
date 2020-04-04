---
author: IEvangelist
ms.service: cognitive-services
ms.topic: include
ms.date: 04/03/2020
ms.author: dapine
ms.openlocfilehash: 4a8b91fbcecbc4f763c9a24ef8c2bcedc377e3b0
ms.sourcegitcommit: 0450ed87a7e01bbe38b3a3aea2a21881f34f34dd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/03/2020
ms.locfileid: "80658677"
---
## <a name="prerequisites"></a>Előfeltételek

Mielőtt elkezdene járni:

> [!div class="checklist"]
> * <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesSpeechServices" target="_blank">Azure Speech-erőforrás létrehozása<span class="docon docon-navigate-external x-hidden-focus"></span></a>
> * [A fejlesztői környezet beállítása és üres projekt létrehozása](../../../../quickstarts/setup-platform.md?tabs=macos)
> * Győződjön meg arról, hogy rendelkezik a mikrofonhoz a hangrögzítéshez

## <a name="source-code"></a>Forráskód

Hozzon létre egy *helloworld.cpp*nevű C++ forrásfájlt, és illessze be a következő kódot.

[!code-cpp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/cpp/macos/from-microphone/helloworld.cpp#code)]

[!INCLUDE [replace key and region](../replace-key-and-region.md)]

## <a name="code-explanation"></a>Kód magyarázata

[!INCLUDE [code explanation](../code-explanation.md)]

## <a name="build-the-app"></a>Alkalmazás készítése

> [!NOTE]
> Ügyeljen arra, hogy a lenti parancsokat _egyetlen parancssorként_ írja be. Ennek a legegyszerűbb módja, ha az egyes parancsok mellett található **Másolás** gombbal kimásolja a parancsot, majd beilleszti a felületi parancssorba.

* Futtassa a következő parancsot az alkalmazás létrehozásához.

  ```sh
  g++ helloworld.cpp -o helloworld --std=c++14 -F${SPEECHSDK_ROOT} -framework MicrosoftCognitiveServicesSpeech
  ```

## <a name="run-the-app"></a>Az alkalmazás futtatása

1. Konfigurálja a betöltő kódtárának útvonalát úgy, hogy a Speech SDK kódtárára mutasson.

    ```sh
    export DYLD_FRAMEWORK_PATH="$DYLD_FRAMEWORK_PATH:$SPEECHSDK_ROOT"
    ```

1. Futtassa az alkalmazást.

   ```sh
   ./helloworld
   ```

1. A konzolablakban megjelenik egy rendszerüzenet, amely arra kéri, hogy mondjon valamit. Mondjon ki egy angol nyelvű kifejezést vagy mondatot. A rendszer továbbítja a beszédet a Speech Service-be, majd szöveggé alakítja át, amely ugyanabban az ablakban meg is jelenik.

   ```text
   Say something...
   We recognized: What's the weather like?
   ```

## <a name="next-steps"></a>További lépések

[!INCLUDE [footer](../footer.md)]