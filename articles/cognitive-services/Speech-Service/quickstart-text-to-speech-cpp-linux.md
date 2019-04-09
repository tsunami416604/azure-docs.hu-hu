---
title: 'Gyors útmutató: Beszéd, a C++ (Linux) – beszédszolgáltatások szintetizálásához'
titleSuffix: Azure Cognitive Services
description: Ismerje meg, hogyan szintetizálásához beszéd, a C++ Linux rendszeren a Speech SDK-val
services: cognitive-services
author: yinhew
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 4/03/2019
ms.author: yinhew
ms.openlocfilehash: c0981ec993f3717f3ec3d3da987a5977b212fb9f
ms.sourcegitcommit: e43ea344c52b3a99235660960c1e747b9d6c990e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/04/2019
ms.locfileid: "59012233"
---
# <a name="quickstart-synthesize-speech-in-c-on-linux-by-using-the-speech-sdk"></a>Gyors útmutató: Beszéd, a C++, a linuxon futó szintetizálásához a Speech SDK-val

Ez a cikk az Ubuntu Linux 16.04 vagy 18.04 hoz létre egy C++ Konzolalkalmazás. A Cognitive Services használata [beszéd SDK](speech-sdk.md) szintetizálásához beszéd valós idejű szöveg és a speech játsszon a PC-hangszóró. Az alkalmazást a [Linuxhoz készült Speech SDK-val](https://aka.ms/csspeech/linuxbinary) és a Linux-disztribúció C++ fordítóprogramjával lehet összeállítani (ilyen például a `g++`).

## <a name="prerequisites"></a>Előfeltételek

A rövid útmutató elvégzéséhez beszédszolgáltatások előfizetési kulcs szükséges. amelyet ingyenesen is beszerezhet. Lásd: [próbálja ki ingyenesen a beszédszolgáltatások](get-started.md) részleteiről.

## <a name="install-speech-sdk"></a>A Speech SDK telepítése

[!INCLUDE [License Notice](../../../includes/cognitive-services-speech-service-license-notice.md)]

A Cognitive Services Speech SDK jelenlegi verziója az `1.4.0`.

A Linuxhoz készült Speech SDK 64 és 32 bites alkalmazások létrehozásához is használható. A szükséges kódtárai és fejlécfájljai a tar-fájlként letölthető https://aka.ms/csspeech/linuxbinary.

Az SDK letöltése és telepítése a következőképpen történik:

1. Győződjön meg róla, hogy az SDK függőségei telepítve vannak.

   ```sh
   sudo apt-get update
   sudo apt-get install build-essential libssl1.0.0 libasound2 wget
   ```

1. Válasszon egy könyvtárat a Speech SDK fájljainak kicsomagolásához, majd állítsa be úgy a `SPEECHSDK_ROOT` környezeti változót, hogy erre a könyvtárra mutasson. Ez a változó megkönnyíti, hogy a későbbi parancsokban a könyvtárra hivatkozzon. Ha például a kezdőkönyvtárban található `speechsdk` könyvtárat szeretné használni, adjon meg egy, a következőhöz hasonló parancsot:

   ```sh
   export SPEECHSDK_ROOT="$HOME/speechsdk"
   ```

1. Hozza létre a könyvtárat, ha az még nem létezik.

   ```sh
   mkdir -p "$SPEECHSDK_ROOT"
   ```

1. Töltse le és bontsa ki a `.tar.gz` archívumot, amely a Speech SDK bináris fájljait tartalmazza:

   ```sh
   wget -O SpeechSDK-Linux.tar.gz https://aka.ms/csspeech/linuxbinary
   tar --strip 1 -xzf SpeechSDK-Linux.tar.gz -C "$SPEECHSDK_ROOT"
   ```

1. Ellenőrizze a kibontott csomag legfelső szintű könyvtárának tartalmát:

   ```sh
   ls -l "$SPEECHSDK_ROOT"
   ```

   A könyvtárlistának tartalmaznia kell a harmadik felekkel kapcsolatos közleményt, a licencfájlokat, valamint a fejlécfájlokat (`.h`) tartalmazó `include` könyvtárat, és a kódtárakat tartalmazó `lib` könyvtárat.

   [!INCLUDE [Linux Binary Archive Content](../../../includes/cognitive-services-speech-service-linuxbinary-content.md)]

## <a name="add-sample-code"></a>Mintakód hozzáadása

1. Hozzon létre egy `helloworld.cpp` nevű C++ forrásfájlt, és illessze be az alábbi kódot.

   [!code-cpp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/text-to-speech/cpp-linux/helloworld.cpp#code)]

1. Ezt az új fájlt, cserélje le a karakterlánc `YourSubscriptionKey` az beszédszolgáltatások előfizetési kulccsal végzett.

1. Cserélje le a `YourServiceRegion` sztringet az előfizetéséhez társított [régióra](regions.md) (ez a `westus` régió, ha az ingyenes próbaverzióra regisztrált).

## <a name="build-the-app"></a>Alkalmazás készítése

> [!NOTE]
> Ügyeljen arra, hogy a lenti parancsokat _egyetlen parancssorként_ írja be. Ennek a legegyszerűbb módja, ha az egyes parancsok mellett található **Másolás** gombbal kimásolja a parancsot, majd beilleszti a felületi parancssorba.

* **x64** (64 bites) rendszeren az alkalmazás létrehozásához futtassa az alábbi parancsot.

  ```sh
  g++ helloworld.cpp -o helloworld -I "$SPEECHSDK_ROOT/include/cxx_api" -I "$SPEECHSDK_ROOT/include/c_api" --std=c++14 -lpthread -lMicrosoft.CognitiveServices.Speech.core -L "$SPEECHSDK_ROOT/lib/x64" -l:libssl.so.1.0.0 -l:libasound.so.2
  ```

* **x86** (32 bites) rendszeren az alkalmazás létrehozásához futtassa az alábbi parancsot.

  ```sh
  g++ helloworld.cpp -o helloworld -I "$SPEECHSDK_ROOT/include/cxx_api" -I "$SPEECHSDK_ROOT/include/c_api" --std=c++14 -lpthread -lMicrosoft.CognitiveServices.Speech.core -L "$SPEECHSDK_ROOT/lib/x86" -l:libssl.so.1.0.0 -l:libasound.so.2
  ```

## <a name="run-the-app"></a>Az alkalmazás futtatása

1. Konfigurálja a betöltő kódtárának útvonalát úgy, hogy a Speech SDK kódtárára mutasson.

   * **x64** (64 bites) rendszeren adja meg a következő kódot.

     ```sh
     export LD_LIBRARY_PATH="$LD_LIBRARY_PATH:$SPEECHSDK_ROOT/lib/x64"
     ```

   * **x86** (32 bites) rendszeren adja meg ezt a kódot.

     ```sh
     export LD_LIBRARY_PATH="$LD_LIBRARY_PATH:$SPEECHSDK_ROOT/lib/x86"
     ```

1. Futtassa az alkalmazást.

   ```sh
   ./helloworld
   ```

1. A konzolablakban megjelenik egy, valamilyen szöveget megadását kéri. Írjon be néhány szavakat és a egy mondatot. A beírt szöveget a beszédszolgáltatások továbbítani, és a Speech, amely a a hangfelismerő játszik synthesized.

   ```text
   Type some text that you want to speak...
   > hello
   Speech synthesized to speaker for text [hello]
   Press enter to exit...
   ```

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Ismerkedés a C++-mintákat a Githubon](https://aka.ms/csspeech/samples)

## <a name="see-also"></a>Lásd még

- [Hangtípusok testreszabása](how-to-customize-voice-font.md)
- [Hangminták felvétele](record-custom-voice-samples.md)
