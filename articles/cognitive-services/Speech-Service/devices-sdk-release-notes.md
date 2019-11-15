---
title: Beszédfelismerési eszközök SDK-dokumentációja
titleSuffix: Azure Cognitive Services
description: A kibocsátási megjegyzésekben a frissítések, a javítások, a hibajavítások és a Speech Devices SDK módosításai szerepelnek. Ez a cikk a Speech Device SDK minden kiadásával frissül.
services: cognitive-services
author: wsturman
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 11/13/2019
ms.author: wellsi
ms.openlocfilehash: 3a74f3eb9aece3535f5505d69833a074c2dd0ed2
ms.sourcegitcommit: a170b69b592e6e7e5cc816dabc0246f97897cb0c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/14/2019
ms.locfileid: "74091203"
---
# <a name="release-notes-speech-devices-sdk"></a>Kibocsátási megjegyzések: beszédfelismerési eszközök SDK

A következő fejezetek a legutóbbi kiadások változásait sorolja fel.

## <a name="speech-devices-sdk-170"></a>Beszédfelismerési eszközök SDK-1.7.0:

*   A Linux ARM mostantól támogatott.
*   A Roobov2 kezdeti bináris fájljai (Linux ARM64) vannak megadva.
*   A Windows-felhasználók a AudioConfig. fromDefaultMicrophoneInput () vagy a AudioConfig. fromMicrophoneInput (deviceName) használatával határozzák meg a használni kívánt mikrofont.
*   A könyvtár mérete optimalizált.
*   Több-turn felismerés támogatása ugyanazzal a beszédfelismerési/szándék-felismerő objektummal.
*   Javítsa ki az alkalmi lefagyást, amely az elismerés leállításakor következik be.
*   A minta alkalmazások mostantól egy minta résztvevő. properties fájlt tartalmaznak a fájl formátumának bemutatásához.
*   Frissítette a [SPEECH SDK](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) összetevőjét a 1.7.0 verzióra. További információt a [kibocsátási megjegyzései](https://aka.ms/csspeech/whatsnew)című témakörben talál.

## <a name="speech-devices-sdk-160"></a>Speech Devices SDK 1.6.0:

- Az [Azure Kinect DK](https://azure.microsoft.com/services/kinect-dk/) támogatása a Windows és a Linux rendszerben a gyakori [minta alkalmazással](https://aka.ms/sdsdk-download)
- Frissítette a [SPEECH SDK](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) összetevőjét a 1.6.0 verzióra. További információt a [kibocsátási megjegyzései](https://aka.ms/csspeech/whatsnew)című témakörben talál.

## <a name="speech-devices-sdk-151"></a>Beszédfelismerési eszközök SDK 1.5.1:

- Belefoglalja a [beszélgetés átírását](conversation-transcription-service.md) a minta alkalmazásba.
- Frissítette a [SPEECH SDK](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) összetevőt a 1.5.1-es verzióra. További információt a [kibocsátási megjegyzései](https://aka.ms/csspeech/whatsnew)című témakörben talál.

## <a name="speech-devices-sdk-150-2019-may-release"></a>Beszédfelismerési eszközök SDK 1.5.0:2019 – májusi kiadás

- A Speech Devices SDK mostantól már nem egy nem elérhető előzetes verzió.
- Frissítette a [SPEECH SDK](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) összetevőt a 1.5.0-es verzióra. További információt a [kibocsátási megjegyzései](https://aka.ms/csspeech/whatsnew)című témakörben talál.
- Az új kulcsszó-technológia jelentős minőségbeli újdonságokat eredményez, lásd: a változtatások megszakítása.
- Új hangfeldolgozási folyamat a továbbfejlesztett távoli mezők felismeréséhez.

**Használhatatlanná tévő változásai**

- Az új kulcsszó-technológia miatt minden kulcsszót újra létre kell hozni a továbbfejlesztett kulcsszavas portálon. Ha teljesen el szeretné távolítani a régi kulcsszavakat az eszközről, távolítsa el a régi alkalmazást.
  - ADB uninstall com. microsoft. coginitiveservices. Speech. Samples. sdsdkstarterapp

## <a name="speech-devices-sdk-140-2019-apr-release"></a>Speech Devices SDK 1.4.0:2019 – Apr kiadás

- Frissítette a [SPEECH SDK](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) összetevőjét a 1.4.0 verzióra. További információt a [kibocsátási megjegyzései](https://aka.ms/csspeech/whatsnew)című témakörben talál.

## <a name="speech-devices-sdk-131-2019-mar-release"></a>Speech Devices SDK 1.3.1:2019 – Mar kiadás

- Frissítette a [SPEECH SDK](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) összetevőt a 1.3.1-es verzióra. További információt a [kibocsátási megjegyzései](https://aka.ms/csspeech/whatsnew)című témakörben talál.
- Frissített kulcsszavas kezelés: a módosítások megszakítása.
- A minta alkalmazás a beszédfelismerés és a fordítás nyelvének megválasztását is hozzáadja.

**Használhatatlanná tévő változásai**

- [A kulcsszó telepítése](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-devices-sdk-create-kws) egyszerűsített, mostantól az alkalmazás része, és nincs szükség külön telepítésre az eszközön.
- A kulcsszó-felismerés módosult, és két esemény támogatott.
  - RecognizingKeyword azt jelzi, hogy a beszédfelismerési eredmény tartalmaz (nem ellenőrzött) kulcsszavas szöveget.
  - A RecognizedKeyword azt jelzi, hogy a kulcsszó-felismerés befejeződött, és felismerte az adott kulcsszót.

## <a name="speech-devices-sdk-110-2018-nov-release"></a>Speech Devices SDK 1.1.0:2018 – november kiadás

- Frissítette a [SPEECH SDK](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) összetevőt a 1.1.0 verzióra. További információt a [kibocsátási megjegyzései](https://aka.ms/csspeech/whatsnew)című témakörben talál.
- A továbbfejlesztett hangfeldolgozási algoritmussal továbbfejlesztettük a beszédfelismerési pontosságot.
- Példa a kínai beszédfelismerési támogatás hozzáadására.

## <a name="speech-devices-sdk-101-2018-oct-release"></a>Speech Devices SDK 1.0.1:2018 – Oct kiadás

- Frissítette a [SPEECH SDK](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) összetevőt a 1.0.1-es verzióra. További információt a [kibocsátási megjegyzései](https://aka.ms/csspeech/whatsnew)című témakörben talál.
- A beszédfelismerés pontossága továbbfejlesztett hangfeldolgozási algoritmussal bővült
- Egy folyamatos felismerési hang-munkamenet hibája javítva lett.

**Használhatatlanná tévő változásai**

- Ebben a kiadásban bevezetett kompatibilitástörő változások számos. Az API-kkal kapcsolatos részletekért tekintse meg [ezt a lapot](https://aka.ms/csspeech/breakingchanges_1_0_0) .
- A KWS-modell fájljai nem kompatibilisek a Speech Devices SDK 1.0.1-vel. A meglévő kulcsszavas fájlokat a rendszer az új Kulcsszóválasztó fájlok az eszközre való írása után törli.

## <a name="speech-devices-sdk-050-2018-aug-release"></a>Speech Devices SDK 0.5.0:2018 – Aug kiadás

- Javította a beszédfelismerés pontosságát azáltal, hogy kijavít egy hibát a hangfeldolgozási kódban.
- Frissítette a [SPEECH SDK](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) összetevőjét a 0.5.0 verzióra. További információt a [kibocsátási megjegyzései](releasenotes.md#cognitive-services-speech-sdk-050-2018-july-release)című témakörben talál.

## <a name="speech-devices-sdk-0212733-2018-may-release"></a>Speech Devices SDK 0.2.12733:2018 – májusi kiadás

A Cognitive Services Speech Devices SDK első nyilvános előzetes kiadása.
