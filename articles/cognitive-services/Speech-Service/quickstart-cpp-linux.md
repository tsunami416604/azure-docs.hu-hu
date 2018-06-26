---
title: A C++ és a Linux beszéd SDK gyors üzembe helyezés |} Microsoft Docs
titleSuffix: Microsoft Cognitive Services
description: Get információkat és a kód minták segítségével gyorsan Ismerkedés a beszédfelismerés SDK használata a Linux és C++ kognitív szolgáltatásban.
services: cognitive-services
author: wolfma61
manager: onano
ms.service: cognitive-services
ms.technology: Speech
ms.topic: article
ms.date: 06/07/2018
ms.author: wolfma
ms.openlocfilehash: cee70ba585f93dda3249fc5b39f25fb613b57a45
ms.sourcegitcommit: 6eb14a2c7ffb1afa4d502f5162f7283d4aceb9e2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/25/2018
ms.locfileid: "36753664"
---
# <a name="quickstart-for-c-and-linux"></a>A C++ és a Linux gyors üzembe helyezés

A szolgáltatások kognitív beszéd SDK aktuális verziója `0.4.0`.

A Linux kognitív szolgáltatások beszéd SDK 64 bites és 32 bites alkalmazások kiépítését érhető el. A szükséges fájlokat a bont-fájlként is letölthetők https://aka.ms/csspeech/linuxbinary.

> [!NOTE]
> Ha a C++ és a Windows egy gyors üzembe helyezés van szüksége, lépjen [Itt](quickstart-cpp-windows.md).
> Ha a C# és a Windows egy gyors üzembe helyezés van szüksége, lépjen [Itt](quickstart-csharp-windows.md).

[!include[Get a Subscription Key](includes/get-subscription-key.md)]

> [!NOTE]
> Ezek az utasítások azt feltételezik, Ubuntu 16.04 (x86 vagy x64) a számítógépen futtatja.
> Egy másik Ubuntu verziót, vagy egy másik Linux terjesztési akkor való igazításának lehetősége szükséges lépéseket.

## <a name="prerequisites"></a>Előfeltételek

[!include[Ubuntu Prerequisites](includes/ubuntu1604-prerequisites.md)]

## <a name="getting-the-binary-package"></a>A bináris csomag beolvasása

[!include[License Notice](includes/license-notice.md)]

1. Válasszon ki egy könyvtárat (abszolút elérési út), hol szeretné elhelyezni a beszédfelismerés SDK bináris fájljait és a fejlécek.
   Válasszon az elérési út például `speechsdk` a kezdőkönyvtár alatt:

   ```sh
   export SPEECHSDK_ROOT="$HOME/speechsdk"
   ```

1. Ha még nem létezik a következő könyvtár létrehozásakor:

   ```sh
   mkdir -p "$SPEECHSDK_ROOT"
   ```

1. Töltse le, és bontsa ki a `.tar.gz` a beszédfelismerés SDK bináris fájljait archív:

   ```sh
   wget -O SpeechSDK-Linux.tar.gz https://aka.ms/csspeech/linuxbinary
   tar --strip 1 -xzf SpeechSDK-Linux.tar.gz -C "$SPEECHSDK_ROOT"
   ```

1. A legfelső szintű könyvtár a kibontott csomag tartalmának ellenőrzése:

   ```sh
   ls -l "$SPEECHSDK_ROOT"
   ```

   Külső értesítés és licencfájlok, kell megjelennie, valamint egy `include` könyvtárhoz, fejlécek és egy `lib` a tárak könyvtárába.

   [!include[Linux Binary Archive Content](includes/linuxbinary-content.md)]

## <a name="sample-code"></a>Mintakód

A következő kódot a mikrofon az angol nyelvű beszéd felismeri.
Elhelyezése nevű fájl `quickstart-linux.cpp`:

[!code-cpp[Quickstart Code](~/samples-cognitive-services-speech-sdk/Linux/quickstart-linux/quickstart-linux.cpp#code)]

Cserélje le a kódban az Előfizetés kulcs szerezte be azt.

## <a name="building"></a>Létrehozás

> [!NOTE]
> Ügyeljen arra, hogy másolja és illessze be a build parancsok alább, egy _egysoros_.

* A következő parancsot hozhat létre az alkalmazást egy x64 a gépet:

  ```sh
  g++ quickstart-linux.cpp -o quickstart-linux -I "$SPEECHSDK_ROOT/include/cxx_api" -I "$SPEECHSDK_ROOT/include/c_api" --std=c++14 -lpthread -lMicrosoft.CognitiveServices.Speech.core -L "$SPEECHSDK_ROOT/lib/x64" -l:libssl.so.1.0.0 -l:libcurl.so.4 -l:libasound.so.2
  ```

* A következő parancsot hozhat létre az alkalmazást egy x86 a gépet:

  ```sh
  g++ quickstart-linux.cpp -o quickstart-linux -I "$SPEECHSDK_ROOT/include/cxx_api" -I "$SPEECHSDK_ROOT/include/c_api" --std=c++14 -lpthread -lMicrosoft.CognitiveServices.Speech.core -L "$SPEECHSDK_ROOT/lib/x86" -l:libssl.so.1.0.0 -l:libcurl.so.4 -l:libasound.so.2
  ```

## <a name="running"></a>Fut

Az alkalmazás futtatásához lesz szüksége a betöltő könyvtárelérési utat, mutasson a beszédfelismerés SDK-könyvtár konfigurálása.

* Egy x64 a gépen, futtatja:

  ```sh
  export LD_LIBRARY_PATH="$LD_LIBRARY_PATH:$SPEECHSDK_ROOT/lib/x64"
  ```

* Egy x86 a gépen, futtatja:

  ```sh
  export LD_LIBRARY_PATH="$LD_LIBRARY_PATH:$SPEECHSDK_ROOT/lib/x86"
  ```

Az alkalmazás futtatásához a következőképpen:

```sh
./quickstart-linux
```

Ha minden megfelelően működik, következőhöz hasonló kimenetnek kell megjelennie:

```text
Say something...
We recognized: What's the weather
```

## <a name="downloading-the-sample"></a>A minta letöltése

A minták legújabb készletét, tekintse meg a [kognitív szolgáltatások beszéd SDK minta GitHub-tárházban](https://aka.ms/csspeech/samples).

## <a name="next-steps"></a>További lépések

* Látogasson el a [minták lap](samples.md) további példák.
