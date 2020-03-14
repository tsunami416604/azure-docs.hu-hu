---
title: Speech Devices SDK – beszédfelismerési szolgáltatás
titleSuffix: Azure Cognitive Services
description: Első lépések a beszédfelismerő Devices SDK-val. A beszédfelismerési szolgáltatás számos különböző eszközzel és hangforrással működik. A beszédfelismerés Devices SDK-val egy előre beállított kódtár, amely az régiójával célirányosan fejlesztett, mikrofon tömb szoftverfejlesztői készletekkel.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/11/2020
ms.author: erhopf
ms.openlocfilehash: add0d27ae6a612fed0320b5329d19236b7bbbd01
ms.sourcegitcommit: 512d4d56660f37d5d4c896b2e9666ddcdbaf0c35
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/14/2020
ms.locfileid: "79370644"
---
# <a name="what-is-the-speech-devices-sdk"></a>Mi a Speech Devices SDK?

A [beszédfelismerési szolgáltatás](overview.md) számos különböző eszközzel és hangforrással működik. Most a következő szintre való megfelelést kiváltó hardver- és beszédfelismerés alkalmazásait is igénybe vehet. A beszédfelismerés Devices SDK-val egy előre beállított kódtár, amely az régiójával célirányosan fejlesztett, mikrofon tömb szoftverfejlesztői készletekkel.

A beszédfelismerés eszközök SDK segítségével:

- Gyorsan tesztelheti az új hangalapú forgatókönyvek.
- A felhő alapú beszédszolgáltatás könnyen integrálható az eszköz.
- Hozzon létre egy kivételes felhasználói élmény az ügyfelek számára.

A Speech Devices SDK a [SPEECH SDK](speech-sdk.md)-t használja. Speciális hangfeldolgozási algoritmusok használatával az eszköz mikrofon-tömbje segítségével elküldheti a hanganyagot a [beszédfelismerési szolgáltatásnak](overview.md). A szolgáltatás precízen segíti a széles körű [beszédfelismerést](speech-to-text.md) a zajcsökkentés, az ECHO-lemondás, a beamforming és a visszhang használatával.

A Speech Devices SDK használatával olyan környezeti eszközöket is létrehozhat, amelyek saját [testreszabott kulcsszóval](speech-devices-sdk-create-kws.md)rendelkeznek. Az egyéni kulcsszavak egy olyan végszót biztosítanak, amely a márka egyedi felhasználói beavatkozását indítja el.

A Speech Devices SDK többféle hang-kompatibilis forgatókönyvet tesz lehetővé, például a [hangsegédeket](https://aka.ms/bots/speech/va), a meghajtó-átrendezési rendszereket, a [társalgási átírást](conversation-transcription-service.md)és az intelligens hangszórókat. Válaszolhat a felhasználókra szöveggel, visszaállíthatja őket egy alapértelmezett vagy [Egyéni hangon](how-to-customize-voice-font.md), megadhatja a keresési eredményeket, [lefordíthat](speech-translation.md) más nyelvekre, és így tovább. Kíváncsian várjuk, mit hoz létre!

## <a name="get-the-speech-devices-sdk"></a>Beszédeszközök SDK letöltése

### <a name="android"></a>Android

Android esetén az eszközök az [Android Speech Devices SDK](https://aka.ms/sdsdk-download-android)legújabb verzióját töltik le.

### <a name="windows"></a>Windows

A Windows esetében a minta alkalmazás platformfüggetlen Java-alkalmazásként van megadva. Töltse le a [JRE Speech Devices SDK](https://aka.ms/sdsdk-download-JRE)legújabb verzióját.
Az alkalmazás a Speech SDK csomaggal, valamint az Eclipse Java IDE (v4) a 64 bites Windows rendszeren készült. és 64 bites Java 8 futtatókörnyezetben (JRE) fut.

### <a name="linux"></a>Linux

A Linux rendszerben a minta alkalmazás platformfüggetlen Java-alkalmazásként van megadva. Töltse le a [JRE Speech Devices SDK](https://aka.ms/sdsdk-download-JRE)legújabb verzióját.
Az alkalmazás a Speech SDK csomaggal, valamint az Eclipse Java IDE (v4) a 64 bites Linux rendszeren (Ubuntu 16,04, Ubuntu 18,04, Debian 9, RHEL 8, CentOS 8) készült. és 64 bites Java 8 futtatókörnyezetben (JRE) fut.

További bináris fájlok érhetők el a közelgő eszközök, a [Roobo v2 DDK](https://aka.ms/sdsdk-download-roobov2), a [Urbetter DDK](https://aka.ms/sdsdk-download-urbetter)és a [GGEC-hangszórók](https://aka.ms/sdsdk-download-speaker)támogatásához.

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Beszédfelismerési eszköz kiválasztása](get-speech-devices-sdk.md)
> [!div class="nextstepaction"]
> [Beszédfelismerési szolgáltatás előfizetési kulcsának beszerzése ingyenesen](get-started.md)
