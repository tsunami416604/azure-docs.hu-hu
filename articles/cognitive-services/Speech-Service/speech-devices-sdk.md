---
title: Beszédeszközök SDK - Beszédszolgáltatás
titleSuffix: Azure Cognitive Services
description: Ismerkedés a beszédeszközök SDK-val. A beszédszolgáltatás számos eszközzel és hangforrással működik. A Beszédeszközök SDK egy előre hangolt könyvtár, amely et a célirányosan kialakított mikrofontömb-fejlesztőkészletekkel párosítják.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/11/2020
ms.author: erhopf
ms.openlocfilehash: add0d27ae6a612fed0320b5329d19236b7bbbd01
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79370644"
---
# <a name="what-is-the-speech-devices-sdk"></a>Mi az a beszédeszközök SDK?

A [beszédszolgáltatás](overview.md) számos eszközzel és hangforrással működik. Mostantól a beszédfelismerési alkalmazásokat a következő szintre lépheti az egyező hardverrel és szoftverrel. A Beszédeszközök SDK egy előre hangolt könyvtár, amely et a célirányosan kialakított mikrofontömb-fejlesztőkészletekkel párosítják.

A beszédeszközök SDK segítségével:

- Gyorsan tesztelhetők az új hangforgatókönyvek.
- Könnyebben integrálhatja a felhőalapú beszédszolgáltatást az eszközébe.
- Hozzon létre kivételes felhasználói élményt ügyfelei számára.

A beszédeszközök SDK a [beszédstajk.The](speech-sdk.md)Speech Devices SDK consumes the Speech SDK . Fejlett hangfeldolgozó algoritmusaink segítségével a készülék mikrofontömbjével elküldjük a hangot a [beszédszolgáltatásnak.](overview.md) Pontos, távoli [beszédfelismerést](speech-to-text.md) biztosít a zajelnyomás, a visszhangszűrés, a nyalábformálás és a bőrfútás révén.

A Beszédfelismerési eszközök SDK-val [saját, testreszabott kulcsszót](speech-devices-sdk-create-kws.md)használó környezeti eszközöket is létrehozhat. Az egyéni kulcsszó olyan jelzést ad, amely a márkára vonatkozó felhasználói beavatkozást indítja el.

A beszédeszközök SDK lehetővé teszi a különböző hang-képes forgatókönyvek, mint például [a hang-asszisztensek,](https://aka.ms/bots/speech/va)drive-thru rendelési rendszerek, [beszélgetés átírása](conversation-transcription-service.md), és az intelligens hangszórók. Válaszolhat a felhasználóknak szöveggel, visszaszólhat rájuk alapértelmezett vagy [egyéni hangon,](how-to-customize-voice-font.md)keresési eredményeket adhat meg, más nyelvekre [fordíthat,](speech-translation.md) és így tovább. Bízunk benne, hogy mit épít!

## <a name="get-the-speech-devices-sdk"></a>Beszédeszközök SDK letöltése

### <a name="android"></a>Android

Android esetén az eszközök letöltik az [Android Speech Devices SDK](https://aka.ms/sdsdk-download-android)legújabb verzióját.

### <a name="windows"></a>Windows

A Windows esetében a mintaalkalmazás platformfüggetlen Java-alkalmazásként van megadva. Töltse le a [JRE Speech Devices SDK](https://aka.ms/sdsdk-download-JRE)legújabb verzióját.
Az alkalmazás a Speech SDK csomaggal és az Eclipse Java IDE (v4) 64 bites Windows rendszerrel készült. és 64 bites Java 8 futtatókörnyezetben (JRE) fut.

### <a name="linux"></a>Linux

Linux esetén a mintaalkalmazás egy platformfüggetlen Java-alkalmazásként van megadva. Töltse le a [JRE Speech Devices SDK](https://aka.ms/sdsdk-download-JRE)legújabb verzióját.
Az alkalmazás a Speech SDK csomaggal és az Eclipse Java IDE (v4) 64 bites Linuxon (Ubuntu 16.04, Ubuntu 18.04, Debian 9, RHEL 8, CentOS 8) készült. és 64 bites Java 8 futtatókörnyezetben (JRE) fut.

További binárisok állnak rendelkezésre, hogy támogassa a közelgő eszközök, [Roobo v2 DDK](https://aka.ms/sdsdk-download-roobov2), [Urbetter DDK](https://aka.ms/sdsdk-download-urbetter), és [GGEC hangszóró](https://aka.ms/sdsdk-download-speaker).

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [A beszédeszköz kiválasztása](get-speech-devices-sdk.md)
> [!div class="nextstepaction"]
> [Ingyenes beszédfelismerési szolgáltatás-előfizetési kulcs beszerezni](get-started.md)
