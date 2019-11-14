---
title: Tudnivalók a Speech Devices SDK-Speech szolgáltatásról
titleSuffix: Azure Cognitive Services
description: Első lépések a beszédfelismerő Devices SDK-val. A beszédfelismerési szolgáltatás együttműködik számos különböző hang forrásai és eszközei. A beszédfelismerés Devices SDK-val egy előre beállított kódtár, amely az régiójával célirányosan fejlesztett, mikrofon tömb szoftverfejlesztői készletekkel.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: erhopf
ms.openlocfilehash: 31d1a60cc6c1316dcfccb4f9ba2571708fddd991
ms.sourcegitcommit: a107430549622028fcd7730db84f61b0064bf52f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/14/2019
ms.locfileid: "74072381"
---
# <a name="about-the-speech-devices-sdk"></a>Tudnivalók a Speech Devices SDK-ról

A [beszédfelismerési szolgáltatások](overview.md) számos különböző eszközzel és hangforrással működnek. Most a következő szintre való megfelelést kiváltó hardver- és beszédfelismerés alkalmazásait is igénybe vehet. A beszédfelismerés Devices SDK-val az régiójával célirányosan fejlesztett, mikrofon tömb szoftverfejlesztői készletekkel pretuned könyvtárból.

A beszédfelismerés eszközök SDK segítségével:

- Gyorsan tesztelheti az új hangalapú forgatókönyvek.
- Könnyebben integrálhatja a felhőalapú beszédfelismerési szolgáltatásokat az eszközre.
- Hozzon létre egy kivételes felhasználói élmény az ügyfelek számára.

A beszédfelismerési eszköz SDK-t használ a [beszéd SDK](speech-sdk.md). A Speech SDK használatával küldi el a speciális hangfeldolgozási algoritmus által feldolgozott hanganyagot az eszköz mikrofon-tömbből a [Speech servicesbe](overview.md). Többcsatornás hang pontosabb távolságban mező biztosításához használt [beszédfelismerés](speech-to-text.md) zaj tiltási, echo törlését, beamforming és dereverberation keresztül.

A beszédfelismerési eszközök SDK-val olyan környezeti eszközöket is létrehozhat, amelyek saját [testreszabott kulcsszóval](speech-devices-sdk-create-kws.md) rendelkeznek, így a felhasználói beavatkozást kezdeményező Cue egyedi a márka számára.

A Speech Devices SDK számos hang-kompatibilis forgatókönyvet támogat, például a [hangsegédeket](https://aka.ms/bots/speech/va), a meghajtó-átrendezési rendszereket, a [társalgási átírást](conversation-transcription-service.md)és az intelligens hangszórókat. Vissza a azokat egy alapértelmezett beszél, hogy a felhasználók a szöveges, reagáljon vagy [egyéni beszédfelismerési](how-to-customize-voice-font.md), adja meg a keresési eredmények között, [fordítása](speech-translation.md) az más nyelvekre, és egyéb. Kíváncsian várjuk, mit hoz létre!

## <a name="get-the-speech-devices-sdk"></a>Beszédeszközök SDK letöltése

### <a name="android"></a>Android

Android-eszközök esetén töltse le az [Android Speech Devices SDK](https://aka.ms/sdsdk-download-android)legújabb verzióját.

### <a name="windows"></a>Windows

Windows rendszeren a minta alkalmazás platformfüggetlen Java-alkalmazásként van megadva. Töltse le a [JRE Speech Devices SDK](https://aka.ms/sdsdk-download-JRE)legújabb verzióját.
Az alkalmazás a Speech SDK csomaggal, valamint az Eclipse Java IDE (v4) a 64 bites Windows rendszeren készült. és 64 bites Java 8 futtatókörnyezetben (JRE) fut.

### <a name="linux"></a>Linux

Linuxos környezetben a minta alkalmazás platformfüggetlen Java-alkalmazásként van megadva. Töltse le a [JRE Speech Devices SDK](https://aka.ms/sdsdk-download-JRE)legújabb verzióját.
Az alkalmazás a Speech SDK csomaggal, valamint az Eclipse Java IDE (v4) a 64 bites Linux rendszeren (Ubuntu 16,04, Ubuntu 18,04, Debian 9) készült. és 64 bites Java 8 futtatókörnyezetben (JRE) fut.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Beszédfelismerési eszköz kiválasztása](get-speech-devices-sdk.md)
> [!div class="nextstepaction"]
> [Beszédfelismerési szolgáltatás előfizetési kulcsának beszerzése ingyenesen](get-started.md)
