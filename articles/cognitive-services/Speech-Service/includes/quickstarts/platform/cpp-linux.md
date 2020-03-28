---
title: 'Rövid útmutató: Speech SDK C++ (Linux) platform beállítása – Beszédszolgáltatás'
titleSuffix: Azure Cognitive Services
description: Ezzel az útmutatóval beállíthatja a platform C++ Linuxon a beszédszolgáltatás SDK.
services: cognitive-services
author: markamos
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 10/14/2019
ms.author: erhopf
ms.openlocfilehash: 4f211e4b90dcc8bffa2fbba6fa4783caf846f50c
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "78383965"
---
Ez az útmutató bemutatja, hogyan kell telepíteni a [Speech SDK](~/articles/cognitive-services/speech-service/speech-sdk.md) linuxos

[!INCLUDE [License Notice](~/includes/cognitive-services-speech-service-license-notice.md)]

## <a name="system-requirements"></a>Rendszerkövetelmények

Linux (Ubuntu 16.04, Ubuntu 18.04, Debian 9, RHEL 8, CentOS 8)

## <a name="prerequisites"></a>Előfeltételek

A rövid útmutató végrehajtásához a következőkre van szükség:

* [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/)

* A támogatott Linux platformokon`libssl` bizonyos könyvtárak at kell `libasound2` telepíteni (a biztonságos aljzatok réteg támogatásához és a hangtámogatáshoz). Az alábbi terjesztésben tájékatosan tájékasztok a könyvtárak megfelelő verzióinak telepítéséhez szükséges parancsokért.

   * Ubuntu:On Ubuntu:

     ```sh
     sudo apt-get update
     sudo apt-get install build-essential libssl1.0.0 libasound2 wget
     ```

   * A Debian 9-en:

     ```sh
     sudo apt-get update
     sudo apt-get install build-essential libssl1.0.2 libasound2 wget
     ```

   * A RHEL/CentOS 8-on:

     ```sh
     sudo yum update
     sudo yum groupinstall "Development tools"
     sudo yum install alsa-lib openssl wget
     ```

> [!NOTE]
> Az RHEL/CentOS 8 rendszeren kövesse az [OpenSSL linuxos beállítására](~/articles/cognitive-services/speech-service/how-to-configure-openssl-linux.md)vonatkozó utasításokat.

## <a name="install-speech-sdk"></a>A Speech SDK telepítése

A Linuxhoz készült Speech SDK 64 és 32 bites alkalmazások létrehozásához is használható. A szükséges tárak és fejlécfájlok kátrányfájlként tölthetők le a programból. https://aka.ms/csspeech/linuxbinary

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

   A könyvtárlistának tartalmaznia kell a harmadik felekkel kapcsolatos közleményt, a licencfájlokat, valamint a fejlécfájlokat (`.h`) tartalmazó `include` könyvtárat, és a kódtárakat tartalmazó `lib` könyvtárat.

   [!INCLUDE [Linux Binary Archive Content](~/includes/cognitive-services-speech-service-linuxbinary-content.md)]

Most továbbléphet az alábbi [következő lépésekre.](#next-steps)

## <a name="next-steps"></a>További lépések

[!INCLUDE [windows](../quickstart-list.md)]
