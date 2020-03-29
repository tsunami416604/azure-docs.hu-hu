---
title: Beszédeszközök SDK dokumentációja
titleSuffix: Azure Cognitive Services
description: A kibocsátási megjegyzések naplót tartalmaznak a hangfrissítésekről, fejlesztésekről, hibajavításokról és a beszédeszközök SDK változásairól. Ez a cikk a beszédeszközök SDK minden egyes kiadásával frissül.
services: cognitive-services
author: wsturman
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 02/12/2020
ms.author: wellsi
ms.openlocfilehash: 29dcb3c0e74482fd6670d1a0983e751043379d6e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80371623"
---
# <a name="release-notes-speech-devices-sdk"></a>Kibocsátási megjegyzések: Beszédeszközök SDK

A következő szakaszok a legutóbbi kiadások változásait sorolják fel.

## <a name="speech-devices-sdk-190"></a>Beszédeszközök SDK 1.9.0:

- Az [Urbetter DDK](https://aka.ms/sdsdk-download-urbetter) (Linux ARM64) kezdeti bináris a rendelkezésre áll.
- Roobo v1 most használja Maven a speech SDK
- A [Speech SDK](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) összetevő frissítése az 1.9.0-s verzióra. További információt a [kibocsátási megjegyzésekben talál.](https://aka.ms/csspeech/whatsnew)

## <a name="speech-devices-sdk-170"></a>Beszédeszközök SDK 1.7.0:

- A Linux ARM most már támogatott.
- A [Roobo v2 DDK](https://aka.ms/sdsdk-download-roobov2) kezdeti bináris fájljai (Linux ARM64) állnak rendelkezésre.
- A Windows-felhasználók használhatják `AudioConfig.fromDefaultMicrophoneInput()` vagy `AudioConfig.fromMicrophoneInput(deviceName)` megadhatják a használni kívánt mikrofont.
- A könyvtár mérete optimalizálva van.
- A többfordulatos felismerés támogatása ugyanazzal a beszéd-/szándékfelismerő objektummal.
- A felismerés leállítása közben előforduló alkalmi lefagyás javítása.
- A mintaalkalmazások mostmár tartalmaznak egy mintaparticipant.properties fájlt a fájl formátumának bemutatására.
- A [Speech SDK](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) összetevő frissítése az 1.7.0-s verzióra. További információt a [kibocsátási megjegyzésekben talál.](https://aka.ms/csspeech/whatsnew)

## <a name="speech-devices-sdk-160"></a>Beszédeszközök SDK 1.6.0:

- Az [Azure Kinect DK](https://azure.microsoft.com/services/kinect-dk/) támogatása Windows és Linux rendszeren közös [mintaalkalmazással](https://aka.ms/sdsdk-download)
- A [Speech SDK](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) összetevő frissítése az 1.6.0-s verzióra. További információt a [kibocsátási megjegyzésekben talál.](https://aka.ms/csspeech/whatsnew)

## <a name="speech-devices-sdk-151"></a>Beszédeszközök SDK 1.5.1:

- A mintaalkalmazásban szerepeljen a [beszélgetési átírás.](conversation-transcription-service.md)
- A [Speech SDK](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) összetevő frissítése az 1.5.1-es verzióra. További információt a [kibocsátási megjegyzésekben talál.](https://aka.ms/csspeech/whatsnew)

## <a name="speech-devices-sdk-150-2019-may-release"></a>Beszédeszközök SDK 1.5.0: 2019-május kiadás

- A Beszédeszközök SDK mostantól GA, és már nem egy kapuzott előzetes verzió.
- A [Speech SDK](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) összetevő frissítése az 1.5.0-s verzióra. További információt a [kibocsátási megjegyzésekben talál.](https://aka.ms/csspeech/whatsnew)
- Az új kulcsszótechnológia jelentős minőségi javulást hoz, lásd: Változások megtörése.
- Új hangfeldolgozási folyamat a terepfelismerés javításához.

**Változások megbontása**

- Az új kulcsszótechnológia miatt minden kulcsszót újra létre kell hozni a továbbfejlesztett kulcsszóportálunkon. A régi kulcsszavak teljes eltávolításához távolítsa el a régi alkalmazást.
  - adb uninstall com.microsoft.cognitiveservices.speech.samples.sdsdkstarterapp

## <a name="speech-devices-sdk-140-2019-apr-release"></a>Beszédeszközök SDK 1.4.0: 2019-április kiadás

- A [Speech SDK](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) összetevő frissítése az 1.4.0-s verzióra. További információt a [kibocsátási megjegyzésekben talál.](https://aka.ms/csspeech/whatsnew)

## <a name="speech-devices-sdk-131-2019-mar-release"></a>Beszédeszközök SDK 1.3.1: 2019-Mar kiadás

- A [Speech SDK](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) összetevő frissítése az 1.3.1-es verzióra. További információt a [kibocsátási megjegyzésekben talál.](https://aka.ms/csspeech/whatsnew)
- Frissített kulcsszókezelés, lásd: Változások megtörése.
- Minta alkalmazás hozzáteszi, nyelvválasztás mind a beszédfelismerés és a fordítás.

**Változások megbontása**

- [A kulcsszó telepítése](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-devices-sdk-create-kws) egyszerűsödött, most már az alkalmazás része, és nincs szükség külön telepítésre az eszközön.
- A kulcsszófelismerés megváltozott, és két esemény támogatott.
  - `RecognizingKeyword,`a beszéderedmény (nem ellenőrzött) kulcsszószöveget tartalmaz.
  - `RecognizedKeyword`, azt jelzi, hogy a kulcsszófelismerés befejeződött az adott kulcsszó felismerésével.

## <a name="speech-devices-sdk-110-2018-nov-release"></a>Beszédeszközök SDK 1.1.0: 2018-nov kiadás

- A [Speech SDK](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) összetevő 1.1.0-s verzióra frissült. További információt a [kibocsátási megjegyzésekben talál.](https://aka.ms/csspeech/whatsnew)
- A Far Field Beszédfelismerés pontossága továbbfejlesztett hangfeldolgozó algoritmusunkkal javult.
- Minta alkalmazás hozzáadott Kínai beszédfelismerés támogatása.

## <a name="speech-devices-sdk-101-2018-oct-release"></a>Beszédeszközök SDK 1.0.1: 2018-október i kiadás

- A [Speech SDK](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) összetevő frissítése az 1.0.1-es verzióra. További információt a [kibocsátási megjegyzésekben talál.](https://aka.ms/csspeech/whatsnew)
- Továbbfejlesztett hangfeldolgozási algoritmusunk javítja a beszédfelismerés pontosságát
- Egy folyamatos felismerés audio session hiba javítva.

**Változások megbontása**

- Ezzel a kiadással számos törési módosítás vezet be. Kérjük, ellenőrizze [ezt az oldalt](https://aka.ms/csspeech/breakingchanges_1_0_0) az API-kkal kapcsolatos részletekért.
- A KWS modellfájlok nem kompatibilisek a Speech Devices SDK 1.0.1-es rendszerrel. A meglévő kulcsszófájlok törlődnek, miután az új kulcsszófájlokat az eszközre írták.

## <a name="speech-devices-sdk-050-2018-aug-release"></a>Beszédeszközök SDK 0.5.0: 2018-augusztus kiadás

- Javította a beszédfelismerés pontosságát azáltal, hogy kijavítottegy hibát a hangfeldolgozó kódban.
- A [Speech SDK](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) összetevő frissítése 0.5.0-s verzióra. További információt a [kibocsátási megjegyzésekben talál.](releasenotes.md#cognitive-services-speech-sdk-050-2018-july-release)

## <a name="speech-devices-sdk-0212733-2018-may-release"></a>Beszédeszközök SDK 0.2.12733: 2018-május kiadás

A Cognitive Services beszédeszközök SDK első nyilvános előzetes kiadása.
