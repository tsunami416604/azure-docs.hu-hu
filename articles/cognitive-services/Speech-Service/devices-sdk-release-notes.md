---
title: Beszédfelismerési eszközök SDK-dokumentációja
titleSuffix: Azure Cognitive Services
description: Kibocsátási megjegyzések – a legutóbbi kiadásokban megváltoztatott változások
services: cognitive-services
author: wsturman
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/10/2019
ms.author: wellsi
ms.openlocfilehash: ba1db1ccba6a1849756e75c9b9f7078371da5bfb
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/04/2019
ms.locfileid: "73464746"
---
# <a name="release-notes-of-cognitive-services-speech-devices-sdk"></a>Cognitive Services Speech Devices SDK kibocsátási megjegyzései
A következő fejezetek a legutóbbi kiadások változásait sorolja fel.

## <a name="speech-devices-sdk-160"></a>Beszédfelismerési eszközök SDK-1.6.0:

*   Az [Azure Kinect DK](https://azure.microsoft.com/services/kinect-dk/) támogatása a Windows és a Linux rendszerben a gyakori [minta alkalmazással](https://aka.ms/sdsdk-download)
*   Frissítette a [SPEECH SDK](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) összetevőjét a 1.6.0 verzióra. További információt a [kibocsátási megjegyzései](https://aka.ms/csspeech/whatsnew)című témakörben talál.

## <a name="speech-devices-sdk-151"></a>Beszédfelismerési eszközök SDK 1.5.1:

*   Belefoglalja a [beszélgetés átírását](conversation-transcription-service.md) a minta alkalmazásba.
*   Frissítette a [SPEECH SDK](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) összetevőt a 1.5.1-es verzióra. További információt a [kibocsátási megjegyzései](https://aka.ms/csspeech/whatsnew)című témakörben talál.

## <a name="cognitive-services-speech-devices-sdk-150-2019-may-release"></a>Cognitive Services Speech Devices SDK 1.5.0:2019 – májusi kiadás

*   A Speech Devices SDK mostantól már nem egy nem elérhető előzetes verzió.
*   Frissítette a [SPEECH SDK](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) összetevőt a 1.5.0-es verzióra. További információt a [kibocsátási megjegyzései](https://aka.ms/csspeech/whatsnew)című témakörben talál.
*   Az új kulcsszó-technológia jelentős minőségbeli újdonságokat eredményez, lásd: a változtatások megszakítása.
*   Új hangfeldolgozási folyamat a továbbfejlesztett távoli mezők felismeréséhez.

**Változtatások megszakítása**

*   Az új kulcsszó-technológia miatt minden kulcsszót újra létre kell hozni a továbbfejlesztett kulcsszavas portálon. Ha teljesen el szeretné távolítani a régi kulcsszavakat az eszközről, távolítsa el a régi alkalmazást.
    - ADB uninstall com. microsoft. coginitiveservices. Speech. Samples. sdsdkstarterapp

## <a name="cognitive-services-speech-devices-sdk-140-2019-apr-release"></a>Cognitive Services Speech Devices SDK 1.4.0:2019 – Apr kiadás

* Frissítette a [SPEECH SDK](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) összetevőjét a 1.4.0 verzióra. További információt a [kibocsátási megjegyzései](https://aka.ms/csspeech/whatsnew)című témakörben talál.

## <a name="cognitive-services-speech-devices-sdk-131-2019-mar-release"></a>Cognitive Services Speech Devices SDK 1.3.1:2019 – Mar kiadás

* Frissítette a [SPEECH SDK](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) összetevőt a 1.3.1-es verzióra. További információt a [kibocsátási megjegyzései](https://aka.ms/csspeech/whatsnew)című témakörben talál.
*   Frissített kulcsszavas kezelés: a módosítások megszakítása.
*   A minta alkalmazás a beszédfelismerés és a fordítás nyelvének megválasztását is hozzáadja.

**Változtatások megszakítása**

*   [A kulcsszó telepítése](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-devices-sdk-create-kws) egyszerűsített, mostantól az alkalmazás része, és nincs szükség külön telepítésre az eszközön.
*   A kulcsszó-felismerés módosult, és két esemény támogatott.
    - RecognizingKeyword azt jelzi, hogy a beszédfelismerési eredmény tartalmaz (nem ellenőrzött) kulcsszavas szöveget.
    - A RecognizedKeyword azt jelzi, hogy a kulcsszó-felismerés befejeződött, és felismerte az adott kulcsszót.


## <a name="cognitive-services-speech-devices-sdk-110-2018-nov-release"></a>Cognitive Services Speech Devices SDK 1.1.0:2018 – Nov kiadás

* Frissítette a [SPEECH SDK](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) összetevőt a 1.1.0 verzióra. További információt a [kibocsátási megjegyzései](https://aka.ms/csspeech/whatsnew)című témakörben talál.
* A továbbfejlesztett hangfeldolgozási algoritmussal továbbfejlesztettük a beszédfelismerési pontosságot.
* Példa a kínai beszédfelismerési támogatás hozzáadására.

## <a name="cognitive-services-speech-devices-sdk-101-2018-oct-release"></a>Cognitive Services Speech Devices SDK 1.0.1:2018 – Oct kiadás

* Frissítette a [SPEECH SDK](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) összetevőt a 1.0.1-es verzióra. További információt a [kibocsátási megjegyzései](https://aka.ms/csspeech/whatsnew)című témakörben talál.
* A beszédfelismerés pontossága továbbfejlesztett hangfeldolgozási algoritmussal bővült  
* Egy folyamatos felismerési hang-munkamenet hibája javítva lett.

**Változtatások megszakítása**

* Ezzel a kiadással számos feltörési változást vezetünk be. Az API-kkal kapcsolatos részletekért tekintse meg [ezt a lapot](https://aka.ms/csspeech/breakingchanges_1_0_0) .
* A KWS-modell fájljai nem kompatibilisek a Speech Devices SDK 1.0.1-vel. A meglévő kulcsszavas fájlokat a rendszer az új Kulcsszóválasztó fájlok az eszközre való írása után törli.

## <a name="cognitive-services-speech-devices-sdk-050-2018-aug-release"></a>Cognitive Services Speech Devices SDK 0.5.0:2018 – Aug kiadás

* Javította a beszédfelismerés pontosságát azáltal, hogy kijavít egy hibát a hangfeldolgozási kódban.
* Frissítette a [SPEECH SDK](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) összetevőjét a 0.5.0 verzióra. További információt a [kibocsátási megjegyzései](releasenotes.md#cognitive-services-speech-sdk-050-2018-july-release)című témakörben talál.

## <a name="cognitive-services-speech-devices-sdk-0212733-2018-may-release"></a>Cognitive Services Speech Devices SDK 0.2.12733:2018 – májusi kiadás

A Cognitive Services Speech Devices SDK első nyilvános előzetes kiadása.
