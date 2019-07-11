---
title: SDK - beszédszolgáltatások beszédfelismerési eszközökkel kapcsolatos
titleSuffix: Azure Cognitive Services
description: Első lépések a beszédfelismerő Devices SDK-val. A beszédszolgáltatások együttműködve számos hang forrásai és eszközei. Most a következő szintre való megfelelést kiváltó hardver- és beszédfelismerés alkalmazásait is igénybe vehet. A beszédfelismerés Devices SDK-val egy előre beállított kódtár, amely az régiójával célirányosan fejlesztett, mikrofon tömb szoftverfejlesztői készletekkel.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: erhopf
ms.openlocfilehash: 341f35d04c7f3f591198acbbf05ac32afe84e95c
ms.sourcegitcommit: dad277fbcfe0ed532b555298c9d6bc01fcaa94e2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/10/2019
ms.locfileid: "67718546"
---
# <a name="about-the-speech-devices-sdk"></a>A beszéd-eszközökről SDK

A [beszédszolgáltatások](overview.md) számos hang forrásai és eszközei működnek. Most a következő szintre való megfelelést kiváltó hardver- és beszédfelismerés alkalmazásait is igénybe vehet. A beszédfelismerés Devices SDK-val az régiójával célirányosan fejlesztett, mikrofon tömb szoftverfejlesztői készletekkel pretuned könyvtárból.

A beszédfelismerés eszközök SDK segítségével:

* Gyorsan tesztelheti az új hangalapú forgatókönyvek.
* A felhőalapú beszédszolgáltatások könnyen integrálható az eszközt.
* Hozzon létre egy kivételes felhasználói élmény az ügyfelek számára.

A beszédfelismerési eszköz SDK-t használ a [beszéd SDK](speech-sdk.md). A beszédfelismerés SDK-t használ a hang, a speciális hang feldolgozási algoritmus az eszköz mikrofonját tömbből, která je zpracována küldése a [beszédszolgáltatások](overview.md). Többcsatornás hang pontosabb távolságban mező biztosításához használt [beszédfelismerés](speech-to-text.md) zaj tiltási, echo törlését, beamforming és dereverberation keresztül.

A beszédfelismerés eszközök SDK-t hozhat létre környezeti rendelkező eszközök a saját is használhatja [testre szabott ébresztési word](speech-devices-sdk-create-kws.md) így saját márkáját egyedi érték, amely felhasználói interakció kezdeményezi a köteg.

A beszédfelismerés Devices SDK-val számos célra hang engedélyezve van, például elősegíti a [egyéni hang-és Felhőközpontú virtuális asszisztensek](https://aka.ms/bots/speech/va), a meghajtó-– sorrendjét, rendszerekben [beszélgetés beszédátírási](conversation-transcription-service.md), és intelligens előadók. Vissza a azokat egy alapértelmezett beszél, hogy a felhasználók a szöveges, reagáljon vagy [egyéni beszédfelismerési](how-to-customize-voice-font.md), adja meg a keresési eredmények között, [fordítása](speech-translation.md) az más nyelvekre, és egyéb. Kíváncsian várjuk, mit hoz létre!

## <a name="get-the-speech-devices-sdk"></a>Beszédeszközök SDK letöltése

### <a name="android"></a>Android

Az androidos eszközök legújabb verzióját töltse le a [Speech eszközök SDK Androidra](https://aka.ms/sdsdk-download-android).

### <a name="windows"></a>Windows

A Windows a mintaalkalmazás verzióként többplatformos Java-alkalmazás. Töltse le a legújabb verzióját a [JRE Speech Devices SDK-val](https://aka.ms/sdsdk-download-JRE).
Az alkalmazás össze lett a Speech SDK-csomagot, és az Eclipse a Java IDE (v4) a 64 bites Windows. és 64 bites Java 8 futtatókörnyezetben (JRE) fut.

### <a name="linux"></a>Linux

Linux-mintaalkalmazás verzióként többplatformos Java-alkalmazás. Töltse le a legújabb verzióját a [JRE Speech Devices SDK-val](https://aka.ms/sdsdk-download-JRE).
Az alkalmazás össze lett a Speech SDK-csomagot, és az Eclipse a Java IDE (v4) 64 bites Linux (Ubuntu 16.04, Ubuntu 18.04, Debian 9). és 64 bites Java 8 futtatókörnyezetben (JRE) fut.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Válassza ki a Speech eszközt](get-speech-devices-sdk.md)
>
> [!div class="nextstepaction"]
> [Ingyenes beszédszolgáltatások előfizetési kulcs lekérése](get-started.md)
