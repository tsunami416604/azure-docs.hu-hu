---
title: 'Gyors útmutató: Speech C++ SDK (MacOS) platform beállítása – beszédfelismerési szolgáltatás'
titleSuffix: Azure Cognitive Services
description: Ezzel az útmutatóval beállíthatja a platformot C++ MacOS rendszeren a SPEECH Service SDK használatával.
services: cognitive-services
author: markamos
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 10/14/2019
ms.author: erhopf
ms.openlocfilehash: d0f9bc44145f75d7c3b503fffe43042c650025f1
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/25/2019
ms.locfileid: "75467147"
---
Ez az útmutató bemutatja, hogyan telepítheti a [SPEECH SDK](~/articles/cognitive-services/speech-service/speech-sdk.md) C++ -t MacOS 10,13-es vagy újabb verzióra.

[!INCLUDE [License Notice](~/includes/cognitive-services-speech-service-license-notice.md)]

## <a name="system-requirements"></a>Rendszerkövetelmények

macOS 10,13 és újabb verziók

## <a name="install-speech-sdk"></a>A Speech SDK telepítése

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

   A címtár listaelemének tartalmaznia kell a harmadik féltől származó és a licenceket, valamint a `MicrosoftCognitiveServicesSpeech.framework` könyvtárat.

Ezután az alábbi [lépésekkel](#next-steps) léphet tovább.

## <a name="next-steps"></a>Következő lépések

[!INCLUDE [windows](../quickstart-list.md)]
