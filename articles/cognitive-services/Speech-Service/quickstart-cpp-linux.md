---
title: 'Rövid útmutató: Beszéd felismerése Linuxon, C++ nyelven a Cognitive Services Speech SDK segítségével'
titleSuffix: Microsoft Cognitive Services
description: A beszédfelismerés elsajátítása Linuxon, C++ nyelven a Cognitive Services Speech SDK segítségével
services: cognitive-services
author: wolfma61
ms.service: cognitive-services
ms.technology: Speech
ms.topic: quickstart
ms.date: 09/24/2018
ms.author: wolfma
ms.openlocfilehash: 98007a11ceadcdddbcd881607f7dda1222d90bc4
ms.sourcegitcommit: 715813af8cde40407bd3332dd922a918de46a91a
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/24/2018
ms.locfileid: "47055917"
---
# <a name="quickstart-recognize-speech-in-c-on-linux-by-using-the-speech-sdk"></a>Rövid útmutató: Beszéd felismerése Linuxon, C++ nyelven a Speech SDK segítségével

[!INCLUDE [Selector](../../../includes/cognitive-services-speech-service-quickstart-selector.md)]

Ebben az útmutatóban egy C++ konzolalkalmazást fog létrehozni az Ubuntu Linux 16.04-es verziójához. A Cognitive Services [Speech SDK](speech-sdk.md) használatával a számítógép mikrofonjába beszélve valós időben konvertálhat át beszédet szöveggé. Az alkalmazást a [Linuxhoz készült Speech SDK-val](https://aka.ms/csspeech/linuxbinary) és a Linux-disztribúció C++ fordítóprogramjával lehet összeállítani (ilyen például a `g++`).

## <a name="prerequisites"></a>Előfeltételek

A rövid útmutató elvégzéséhez szüksége van a Speech Service előfizetői azonosítójára, amelyet ingyenesen is beszerezhet. További részletekért tekintse át a [Speech Service ingyenes kipróbálását](get-started.md) ismertető részt.

## <a name="install-speech-sdk"></a>A Speech SDK telepítése

[!INCLUDE [License Notice](../../../includes/cognitive-services-speech-service-license-notice.md)]

A Cognitive Services Speech SDK jelenlegi verziója az `1.0.0`.

A Linuxhoz készült Speech SDK 64 és 32 bites alkalmazások létrehozásához is használható. A szükséges kódtárak és fejlécfájlok tar-fájlként tölthetők le a https://aka.ms/csspeech/linuxbinary webhelyről.

Az SDK letöltése és telepítése a következőképpen történik:

1. Győződjön meg róla, hogy az SDK függőségei telepítve vannak.

   ```sh
   sudo apt-get update
   sudo apt-get install build-essential libssl1.0.0 libcurl3 libasound2 wget
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

   [!code-cpp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/cpp-linux/helloworld.cpp#code)]

1. Ebben az új fájlban cserélje le a `YourSubscriptionKey` sztringet a Speech Service előfizetői azonosítójára.

1. Cserélje le a `YourServiceRegion` sztringet az előfizetéséhez társított [régióra](regions.md) (ez a `westus` régió, ha az ingyenes próbaverzióra regisztrált).

## <a name="build-the-app"></a>Alkalmazás készítése

> [!NOTE]
> Ügyeljen arra, hogy a lenti parancsokat _egyetlen parancssorként_ írja be. Ennek a legegyszerűbb módja, ha az egyes parancsok mellett található **Másolás** gombbal kimásolja a parancsot, majd beilleszti a felületi parancssorba.

* **x64** (64 bites) rendszeren az alkalmazás létrehozásához futtassa az alábbi parancsot.

  ```sh
  g++ helloworld.cpp -o helloworld -I "$SPEECHSDK_ROOT/include/cxx_api" -I "$SPEECHSDK_ROOT/include/c_api" --std=c++14 -lpthread -lMicrosoft.CognitiveServices.Speech.core -L "$SPEECHSDK_ROOT/lib/x64" -l:libssl.so.1.0.0 -l:libcurl.so.4 -l:libasound.so.2
  ```

* **x86** (32 bites) rendszeren az alkalmazás létrehozásához futtassa az alábbi parancsot.

  ```sh
  g++ helloworld.cpp -o helloworld -I "$SPEECHSDK_ROOT/include/cxx_api" -I "$SPEECHSDK_ROOT/include/c_api" --std=c++14 -lpthread -lMicrosoft.CognitiveServices.Speech.core -L "$SPEECHSDK_ROOT/lib/x86" -l:libssl.so.1.0.0 -l:libcurl.so.4 -l:libasound.so.2
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

1.  A konzolablakban megjelenik egy rendszerüzenet, amely arra kéri, hogy mondjon valamit. Mondjon ki egy angol nyelvű kifejezést vagy mondatot. A rendszer továbbítja a beszédet a Speech Service-be, majd szöveggé alakítja át, amely ugyanabban az ablakban meg is jelenik.

   ```text
   Say something...
   We recognized: What's the weather like?
   ```

[!INCLUDE [Download this sample](../../../includes/cognitive-services-speech-service-speech-sdk-sample-download-h2.md)]
A jelen útmutatóban használt mintát a `quickstart/cpp-linux` mappában találja.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Szándék felismerése beszédből a C++-hoz készült Speech SDK használatával](how-to-recognize-intents-from-speech-cpp.md)

## <a name="see-also"></a>Lásd még

- [Beszéd fordítása](how-to-translate-speech-csharp.md)
- [Akusztikai modellek testreszabása](how-to-customize-acoustic-models.md)
- [Nyelvi modellek testreszabása](how-to-customize-language-model.md)
