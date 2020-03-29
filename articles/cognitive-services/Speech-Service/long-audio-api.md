---
title: Hosszú hang API (előzetes verzió) – beszédfelismerési szolgáltatás
titleSuffix: Azure Cognitive Services
description: Ismerje meg, hogyan készült a Long Audio API a hosszú formátumú szöveg-beszéd aszinkron szintéziséhez.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 01/30/2020
ms.author: dapine
ms.openlocfilehash: 033103e10971be2f6c220ccdb2c3586c7dc2ef05
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "76900247"
---
# <a name="long-audio-api-preview"></a>Hosszú audio API (előzetes verzió)

A Long Audio API-t a hosszú formátumú szöveg-beszéd aszinkron szintézisére tervezték (például: hangoskönyvek). Ez az API nem ad vissza szintetizált hangot valós időben, hanem az elvárás az, hogy a válasz(ok)at lefogja kérni, és felhasználja a kimenet(eke)t, ahogy azok elérhetővé váltak a szolgáltatásból. A beszédfelismerési SDK által használt szöveg-beszéd API-val ellentétben a Hosszú hang API 10 percnél hosszabb szintetizált hangot hozhat létre, így ideális a kiadók és az audiotartalom-platformok számára.

A Long Audio API további előnyei:

* A szolgáltatás által visszaadott szintetizált beszéd neurális hangokat használ, ami biztosítja a hi-fi hangkimeneteket.
* Mivel a valós idejű válaszok nem támogatottak, nincs szükség egy hangvégpont üzembe helyezésére.

> [!NOTE]
> A Hosszú hang API mostantól csak az [egyéni neurális hangot](https://docs.microsoft.com/azure/cognitive-services/speech-service/how-to-custom-voice#custom-neural-voices)támogatja.

## <a name="workflow"></a>Munkafolyamat

A Hosszú hang API használatakor általában egy szövegfájlt vagy szintetizandó fájlokat kell beküldenie, meg kell adnia az állapotot, majd ha az állapot sikeres, letöltheti a hangkimenetet.

Ez az ábra magas szintű áttekintést nyújt a munkafolyamatról.

![Hosszú hang-API munkafolyamat-diagram](media/long-audio-api/long-audio-api-workflow.png)

## <a name="prepare-content-for-synthesis"></a>Tartalom előkészítése a szintézishez

A szövegfájl előkészítésekor győződjön meg arról, hogy:

* Egyszerű szöveges (.txt) vagy SSML-szöveg (.txt)
* UTF-8-ként van kódolva [bájtrendelés-jellel (AJ)](https://www.w3.org/International/questions/qa-utf8-bom.en#bom)
* Egyetlen fájl, nem zip
* Több mint 400 karaktert tartalmaz egyszerű szöveghez vagy 400 [számlázható karaktert](https://docs.microsoft.com/azure/cognitive-services/speech-service/text-to-speech#pricing-note) SSML-szöveghez, és kevesebb mint 10 000 bekezdést
  * Egyszerű szöveg esetén minden bekezdést elválaszt az **Enter/Return** billentyűleütés – [Egyszerű szövegbeviteli példa](https://github.com/Azure-Samples/Cognitive-Speech-TTS/blob/master/CustomVoice-API-Samples/Java/en-US.txt) megtekintése
  * Az SSML-szöveg esetében minden SSML-darab bekezdésnek minősül. Az SSML-darabokat különböző bekezdések választják el egymástól – [SSML szövegbeviteli példa](https://github.com/Azure-Samples/Cognitive-Speech-TTS/blob/master/CustomVoice-API-Samples/Java/SSMLTextInputSample.txt) megtekintése
> [!NOTE]
> A kínai (kontinentális), kínai (hongkongi), kínai (tajvani), japán és koreai nyelv esetében egy szó két karakternek számít. 

## <a name="submit-synthesis-requests"></a>Szintéziskérelmek küldése

A bemeneti tartalom előkészítése után kövesse a [hosszú formátumú hangszintézis rövid útmutatóját](https://aka.ms/long-audio-python) a kérelem elküldéséhez. Ha egynél több bemeneti fájllal rendelkezik, több kérelmet kell benyújtania. Vannak bizonyos korlátozások, amelyeket figyelembe kell venni: 
* Az ügyfél másodpercenként legfeljebb 5 kérést küldhet a kiszolgálónak minden Egyes Azure-előfizetési fiókhoz. Ha túllépi a korlátozást, az ügyfél 429-es hibakódot kap (túl sok kérés). Kérjük, csökkentse a kérelem összegét másodpercenként
* A kiszolgáló minden Egyes Azure-előfizetési fiókhoz legfeljebb 120 kérelmet futtathat és várólistára vehet. Ha túllépi a korlátozást, a kiszolgáló 429-es hibakódot ad vissza (túl sok kérés). Várjon, és ne nyújtson be új kérelmet, amíg egyes kérelmek et nem teljesít.
* A kiszolgáló minden Egyes Azure-előfizetési fiókhoz legfeljebb 20 000 kérelmet fog tartani. Ha túllépi a korlátozást, kérjük, töröljön néhány kérelmet, mielőtt újakat küldene be

## <a name="audio-output-formats"></a>Hangkimeneti formátumok

Támogatjuk a rugalmas hangkimeneti formátumokat. A "concatenateResult" paraméter beállításával bekezdésenként hangkimeneteket hozhat létre, vagy a hangokat egyetlen kimenetbe fűzheti össze. A Long Audio API a következő hangkimeneti formátumokat támogatja:

> [!NOTE]
> Az alapértelmezett hangformátum riff-16khz-16bit-mono-pcm.

* riff-8khz-16bit-mono-pcm
* riff-16khz-16bit-mono-pcm
* riff-24khz-16bit-mono-pcm
* riff-48khz-16bit-mono-pcm
* audio-16khz-32kbitrate-mono-mp3
* audio-16khz-64kbitrate-mono-mp3
* audio-16khz-128kbitrate-mono-mp3
* audio-24khz-48kbitrate-mono-mp3
* audio-24khz-96kbitrate-mono-mp3
* audio-24khz-160kbitrate-mono-mp3

## <a name="quickstarts"></a>Rövid útmutatók

A Long Audio API sikeres futtatását segítő rövid útmutatókat kínálunk. Ez a táblázat a Hosszú hang API-rövid útmutatók listáját tartalmazza, nyelv szerint rendezve.

* [Rövid útmutató: Python](https://aka.ms/long-audio-python)

## <a name="sample-code"></a>Mintakód
A Hosszú audio API mintakódja elérhető a GitHubon.

* [Mintakód: Python](https://github.com/Azure-Samples/Cognitive-Speech-TTS/tree/master/CustomVoice-API-Samples/Python)
* [Mintakód: C #](https://github.com/Azure-Samples/Cognitive-Speech-TTS/tree/master/CustomVoice-API-Samples/CSharp)
* [Mintakód: Java](https://github.com/Azure-Samples/Cognitive-Speech-TTS/blob/master/CustomVoice-API-Samples/Java/)
