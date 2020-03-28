---
title: 'Rövid útmutató: Speech SDK C++ (macOS) platform beállítása – Beszédszolgáltatás'
titleSuffix: Azure Cognitive Services
description: Ezzel az útmutatóval beállíthatja a C++ platformot macOS rendszeren a Beszédszolgáltatás SDK-val.
services: cognitive-services
author: markamos
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 10/14/2019
ms.author: erhopf
ms.openlocfilehash: d0f9bc44145f75d7c3b503fffe43042c650025f1
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75467147"
---
Ez az útmutató bemutatja, hogyan telepítheti a [C++ beszédfelismerési SDK-t](~/articles/cognitive-services/speech-service/speech-sdk.md) macOS 10.13-as és újabb rendszerre.

[!INCLUDE [License Notice](~/includes/cognitive-services-speech-service-license-notice.md)]

## <a name="system-requirements"></a>Rendszerkövetelmények

macOS 10.13 és újabb

## <a name="install-speech-sdk"></a>A Speech SDK telepítése

1. Válasszon egy könyvtárat a Speech SDK fájljainak kicsomagolásához, majd állítsa be úgy a `SPEECHSDK_ROOT` környezeti változót, hogy erre a könyvtárra mutasson. Ez a változó megkönnyíti, hogy a későbbi parancsokban a könyvtárra hivatkozzon. Ha például a kezdőkönyvtárban található `speechsdk` könyvtárat szeretné használni, adjon meg egy, a következőhöz hasonló parancsot:

   ```sh
   export SPEECHSDK_ROOT="$HOME/speechsdk"
   ```

1. Hozza létre a könyvtárat, ha az még nem létezik.

   ```sh
   mkdir -p "$SPEECHSDK_ROOT"
   ```

1. Töltse le `.zip` és bontsa ki a speech SDK keretrendszert tartalmazó archívumot:

   ```sh
   wget -O SpeechSDK-macOS.zip https://aka.ms/csspeech/macosbinary
   unzip SpeechSDK-macOS.zip -d "$SPEECHSDK_ROOT"
   ```

1. Ellenőrizze a kibontott csomag legfelső szintű könyvtárának tartalmát:

   ```sh
   ls -l "$SPEECHSDK_ROOT"
   ```

   A könyvtárlistának tartalmaznia kell a harmadik fél értesítési és licencfájljait, valamint egy `MicrosoftCognitiveServicesSpeech.framework` könyvtárat.

Most továbbléphet az alábbi [következő lépésekre.](#next-steps)

## <a name="next-steps"></a>További lépések

[!INCLUDE [windows](../quickstart-list.md)]
