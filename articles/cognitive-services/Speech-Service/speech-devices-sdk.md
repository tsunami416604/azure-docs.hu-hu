---
title: Tudnivalók a Speech Devices SDK-Speech szolgáltatásról
titleSuffix: Azure Cognitive Services
description: Ismerkedés a Speech Devices SDK-val. A beszédfelismerési szolgáltatás számos különböző eszközzel és hangforrással működik. A Speech Devices SDK egy előre beállított könyvtár, amely a célnak megfelelő, a mikrofon tömbje.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: erhopf
ms.openlocfilehash: 7f6793cab3d2d7cc54252bae3a7393f836256bdd
ms.sourcegitcommit: 6c01e4f82e19f9e423c3aaeaf801a29a517e97a0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/04/2019
ms.locfileid: "74815533"
---
# <a name="about-the-speech-devices-sdk"></a>A Beszédeszközök SDK ismertetése

A [beszédfelismerési szolgáltatás](overview.md) számos különböző eszközzel és hangforrással működik. Most a következő szintre állíthatja a beszédfelismerési alkalmazásokat a megfelelő hardverekkel és szoftverekkel. A Speech Devices SDK egy előre beállított könyvtár, amely a célzott, a mikrofonos tömb fejlesztői csomagjaival párosítva van.

A Speech Devices SDK a következőket nyújtja:

- Gyorsan tesztelheti az új hang-forgatókönyveket.
- Könnyebben integrálhatja a felhőalapú beszédfelismerési szolgáltatást az eszközre.
- Kivételes felhasználói élményt hozhat létre ügyfelei számára.

A Speech Devices SDK a [SPEECH SDK](speech-sdk.md)-t használja. A Speech SDK használatával küldi el a speciális hangfeldolgozási algoritmus által feldolgozott hanganyagot az eszköz mikrofon-tömbből a [Speech Service](overview.md)-be. Többcsatornás [hangfelismerést](speech-to-text.md) használ, hogy pontosabb, sokkal részletesebb beszédfelismerést biztosítson a zajcsökkentéssel, az ECHO megszakításával, a beamforming és a devisszaverődéssel kapcsolatban.

A beszédfelismerési eszközök SDK-val olyan környezeti eszközöket is létrehozhat, amelyek saját [testreszabott kulcsszóval](speech-devices-sdk-create-kws.md) rendelkeznek, így a felhasználói beavatkozást kezdeményező Cue egyedi a márka számára.

A Speech Devices SDK számos hang-kompatibilis forgatókönyvet támogat, például a [hangsegédeket](https://aka.ms/bots/speech/va), a meghajtó-átrendezési rendszereket, a [társalgási átírást](conversation-transcription-service.md)és az intelligens hangszórókat. Válaszolhat a felhasználókra szöveggel, visszaállíthatja őket egy alapértelmezett vagy [Egyéni hangon](how-to-customize-voice-font.md), megadhatja a keresési eredményeket, [lefordíthat](speech-translation.md) más nyelvekre, és így tovább. Várjuk, hogy lássuk, mit épít!

## <a name="get-the-speech-devices-sdk"></a>Beszédeszközök SDK letöltése

### <a name="android"></a>Android

Android-eszközök esetén töltse le az [Android Speech Devices SDK](https://aka.ms/sdsdk-download-android)legújabb verzióját.

### <a name="windows"></a>Windows

Windows rendszeren a minta alkalmazás platformfüggetlen Java-alkalmazásként van megadva. Töltse le a [JRE Speech Devices SDK](https://aka.ms/sdsdk-download-JRE)legújabb verzióját.
Az alkalmazás a Speech SDK csomaggal, valamint az Eclipse Java IDE (v4) a 64 bites Windows rendszeren készült. és 64 bites Java 8 futtatókörnyezetben (JRE) fut.

### <a name="linux"></a>Linux

Linuxos környezetben a minta alkalmazás platformfüggetlen Java-alkalmazásként van megadva. Töltse le a [JRE Speech Devices SDK](https://aka.ms/sdsdk-download-JRE)legújabb verzióját.
Az alkalmazás a Speech SDK csomaggal, valamint az Eclipse Java IDE (v4) a 64 bites Linux rendszeren (Ubuntu 16,04, Ubuntu 18,04, Debian 9) készült. és 64 bites Java 8 futtatókörnyezetben (JRE) fut.

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Beszédfelismerési eszköz kiválasztása](get-speech-devices-sdk.md)
> [!div class="nextstepaction"]
> [Beszédfelismerési szolgáltatás előfizetési kulcsának beszerzése ingyenesen](get-started.md)
