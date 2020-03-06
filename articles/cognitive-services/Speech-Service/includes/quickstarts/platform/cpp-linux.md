---
title: 'Gyors útmutató: Speech C++ SDK (Linux) platform beállítása – beszédfelismerési szolgáltatás'
titleSuffix: Azure Cognitive Services
description: Ezzel az útmutatóval beállíthatja a platformját C++ Linux rendszeren a SPEECH Service SDK-val.
services: cognitive-services
author: markamos
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 10/14/2019
ms.author: erhopf
ms.openlocfilehash: 4f211e4b90dcc8bffa2fbba6fa4783caf846f50c
ms.sourcegitcommit: 021ccbbd42dea64d45d4129d70fff5148a1759fd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/05/2020
ms.locfileid: "78383965"
---
Ez az útmutató bemutatja, hogyan telepítheti a Linux rendszerhez készült [SPEECH SDK](~/articles/cognitive-services/speech-service/speech-sdk.md) -t

[!INCLUDE [License Notice](~/includes/cognitive-services-speech-service-license-notice.md)]

## <a name="system-requirements"></a>Rendszerkövetelmények

Linux (Ubuntu 16,04, Ubuntu 18,04, Debian 9, RHEL 8, CentOS 8)

## <a name="prerequisites"></a>Előfeltételek

A rövid útmutató elvégzéséhez a következőkre lesz szüksége:

* [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/)

* A támogatott linuxos platformokhoz telepíteni kell bizonyos könyvtárakat (`libssl` a Secure Sockets Layer támogatáshoz, és `libasound2` a hangtámogatáshoz). A könyvtárak megfelelő verzióinak telepítéséhez szükséges parancsokért tekintse meg az alábbi disztribúciót.

   * On Ubuntu:

     ```sh
     sudo apt-get update
     sudo apt-get install build-essential libssl1.0.0 libasound2 wget
     ```

   * Debian 9 rendszeren:

     ```sh
     sudo apt-get update
     sudo apt-get install build-essential libssl1.0.2 libasound2 wget
     ```

   * RHEL/CentOS 8 rendszeren:

     ```sh
     sudo yum update
     sudo yum groupinstall "Development tools"
     sudo yum install alsa-lib openssl wget
     ```

> [!NOTE]
> A RHEL/CentOS 8 rendszeren kövesse az [OpenSSL Linux rendszerhez való konfigurálásának](~/articles/cognitive-services/speech-service/how-to-configure-openssl-linux.md)utasításait.

## <a name="install-speech-sdk"></a>A Speech SDK telepítése

A Linuxhoz készült Speech SDK 64 és 32 bites alkalmazások létrehozásához is használható. A szükséges kódtárak és fejlécek a https://aka.ms/csspeech/linuxbinary-ból tölthetők le tar-fájlként.

Az SDK letöltése és telepítése a következőképpen történik:

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

   A könyvtárlistának tartalmaznia kell a harmadik felekkel kapcsolatos közleményt, a licencfájlokat, valamint a fejlécfájlokat (`include`) tartalmazó `.h` könyvtárat, és a kódtárakat tartalmazó `lib` könyvtárat.

   [!INCLUDE [Linux Binary Archive Content](~/includes/cognitive-services-speech-service-linuxbinary-content.md)]

Ezután az alábbi [lépésekkel](#next-steps) léphet tovább.

## <a name="next-steps"></a>Következő lépések

[!INCLUDE [windows](../quickstart-list.md)]
