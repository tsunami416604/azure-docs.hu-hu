---
title: 'Gyors útmutató: Beszédfelismerés felismerése C++ , (MacOS) – beszédfelismerési szolgáltatás'
titleSuffix: Azure Cognitive Services
description: Megtudhatja, hogyan ismerheti fel a beszédfelismerést a macOS-ben C++ a Speech SDK használatával
services: cognitive-services
author: wolfma61
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 07/05/2019
ms.author: wolfma
ms.openlocfilehash: 12abb7fb6d5e93e226ae539f8161566e3c30bd65
ms.sourcegitcommit: 4f3f502447ca8ea9b932b8b7402ce557f21ebe5a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/02/2019
ms.locfileid: "71803334"
---
# <a name="quickstart-recognize-speech-in-c-on-macos-by-using-the-speech-sdk"></a>Gyors útmutató: Beszédfelismerés felismerése C++ MacOS rendszeren a Speech SDK használatával

[!INCLUDE [Selector](../../../includes/cognitive-services-speech-service-quickstart-selector.md)]

Ebben a cikkben egy C++ , a MacOS 10,13-es vagy újabb verzióhoz készült Console-alkalmazást hoz létre. A Cognitive Services [SPEECH SDK](speech-sdk.md) segítségével valós időben átmásolhatja a beszédet a Mac mikrofonja által használt szövegbe. Az alkalmazás a [MacOS-hez készült SPEECH SDK](https://aka.ms/csspeech/macosbinary) -val és a Mac alapértelmezett C++ fordítóprogramával (például `g++`) lett létrehozva.

## <a name="prerequisites"></a>Előfeltételek

A rövid útmutató elvégzéséhez szüksége lesz egy Speech Services-előfizetési kulcsra. amelyet ingyenesen is beszerezhet. További részletekért tekintse [meg a Speech Services ingyenes kipróbálása](get-started.md) című témakört.

## <a name="install-speech-sdk"></a>A Speech SDK telepítése

[!INCLUDE [License Notice](../../../includes/cognitive-services-speech-service-license-notice.md)]

A macOS-hez készült Speech SDK letölthető a https://aka.ms/csspeech/macosbinary csomagból tömörített keretrendszerként.

Az SDK letöltése és telepítése a következőképpen történik:

1. Válasszon egy könyvtárat a Speech SDK fájljainak kicsomagolásához, majd állítsa be úgy a `SPEECHSDK_ROOT` környezeti változót, hogy erre a könyvtárra mutasson. Ez a változó megkönnyíti, hogy a későbbi parancsokban a könyvtárra hivatkozzon. Ha például a kezdőkönyvtárban található `speechsdk` könyvtárat szeretné használni, adjon meg egy, a következőhöz hasonló parancsot:

   ```sh
   export SPEECHSDK_ROOT="$HOME/speechsdk"
   ```

1. Hozza létre a könyvtárat, ha az még nem létezik.

   ```sh
   mkdir -p "$SPEECHSDK_ROOT"
   ```

1. Töltse le és csomagolja ki a Speech SDK-keretrendszert tartalmazó `.zip` archívumot:

   ```sh
   wget -O SpeechSDK-macOS.zip https://aka.ms/csspeech/macosbinary
   unzip SpeechSDK-macOS.zip -d "$SPEECHSDK_ROOT"
   ```

1. Ellenőrizze a kibontott csomag legfelső szintű könyvtárának tartalmát:

   ```sh
   ls -l "$SPEECHSDK_ROOT"
   ```

   A címtár listaelemének tartalmaznia kell a harmadik féltől származó, valamint a licencek fájljait, valamint a `MicrosoftCognitiveServicesSpeech.framework` könyvtárat.

## <a name="add-sample-code"></a>Mintakód hozzáadása

1. Hozzon létre egy `helloworld.cpp` nevű C++ forrásfájlt, és illessze be az alábbi kódot.

   [!code-cpp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/cpp-macos/helloworld.cpp#code)]

1. Ebben az új fájlban cserélje le a `YourSubscriptionKey` karakterláncot a Speech Services előfizetési kulcsával.

1. Cserélje le a `YourServiceRegion` sztringet az előfizetéséhez társított [régióra](regions.md) (ez a `westus` régió, ha az ingyenes próbaverzióra regisztrált).

## <a name="build-the-app"></a>Alkalmazás készítése

> [!NOTE]
> Ügyeljen arra, hogy a lenti parancsokat _egyetlen parancssorként_ írja be. Ennek a legegyszerűbb módja, ha az egyes parancsok mellett található **Másolás** gombbal kimásolja a parancsot, majd beilleszti a felületi parancssorba.

* Futtassa az alábbi parancsot az alkalmazás létrehozásához.

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

1. A konzolablakban megjelenik egy rendszerüzenet, amely arra kéri, hogy mondjon valamit. Mondjon ki egy angol nyelvű kifejezést vagy mondatot. A beszéd a beszédfelismerési szolgáltatásokhoz lett továbbítva, és szövegbe kerül, amely ugyanabban az ablakban jelenik meg.

   ```text
   Say something...
   We recognized: What's the weather like?
   ```

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Minták C++ feltárása a githubon](https://aka.ms/csspeech/samples)

## <a name="see-also"></a>Lásd még

- [Akusztikai modellek testreszabása](how-to-customize-acoustic-models.md)
- [Nyelvi modellek testreszabása](how-to-customize-language-model.md)
