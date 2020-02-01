---
title: Long audio API (előzetes verzió) – Speech Service
titleSuffix: Azure Cognitive Services
description: Ismerje meg, hogy a hosszú hangalapú API hogyan lett kialakítva a hosszú formátumú szöveg és a beszéd közötti aszinkron szintézishez.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 01/30/2020
ms.author: dapine
ms.openlocfilehash: 033103e10971be2f6c220ccdb2c3586c7dc2ef05
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/31/2020
ms.locfileid: "76900247"
---
# <a name="long-audio-api-preview"></a>Long audio API (előzetes verzió)

A hosszú hangalapú API a hosszú formátumú szöveg és a beszéd közötti aszinkron szintézishez lett tervezve (például: hangkönyvek). Ez az API nem adja vissza a szintetizált hangot valós időben, ezért a várt érték az, hogy a válasz (ok) ra fog szavazni, és a kimenet (eke) t a szolgáltatás által elérhetővé tettnek megfelelően használja fel. A Speech SDK által használt szöveg-beszéd API-val ellentétben a hosszú hang-API 10 percnél hosszabb szintetizált hangot is létrehozhat, így a kiadók és a hangtartalom-platformok ideálisak.

A hosszú hang API további előnyei:

* A szolgáltatás által visszaadott szintetizált beszéd neurális hangokat használ, ami biztosítja a magas megbízhatóságú hangkimeneteket.
* Mivel a valós idejű válaszok nem támogatottak, nincs szükség hang-végpont üzembe helyezésére.

> [!NOTE]
> A hosszú hang API mostantól csak az [Egyéni neurális hang](https://docs.microsoft.com/azure/cognitive-services/speech-service/how-to-custom-voice#custom-neural-voices)használatát támogatja.

## <a name="workflow"></a>Munkafolyamat

A hosszú hangalapú API használatakor a rendszer a szintetizált szövegfájlt vagy fájlokat küldi el, lekérdezi az állapotot, majd ha az állapot sikeres, letöltheti az audió kimenetet.

Ez az ábra a munkafolyamat magas szintű áttekintését tartalmazza.

![Hosszú hang-API munkafolyamat-diagram](media/long-audio-api/long-audio-api-workflow.png)

## <a name="prepare-content-for-synthesis"></a>Tartalom előkészítése a szintézishez

A szövegfájl előkészítésekor győződjön meg róla, hogy:

* Egyszerű szöveges (. txt) vagy SSML szöveg (. txt)
* Kódolása [UTF-8, byte Order Mark (BOM)](https://www.w3.org/International/questions/qa-utf8-bom.en#bom) értékkel
* Egyetlen fájl, nem zip
* Több mint 400 karaktert [tartalmaz az egyszerű szöveges vagy a 400](https://docs.microsoft.com/azure/cognitive-services/speech-service/text-to-speech#pricing-note) -es SSML-szövegekhez, és kisebb, mint 10 000 bekezdés
  * Egyszerű szöveg esetén az egyes bekezdéseket az ENTER/Return – [egyszerű szöveges beviteli példa](https://github.com/Azure-Samples/Cognitive-Speech-TTS/blob/master/CustomVoice-API-Samples/Java/en-US.txt) **megadásával** választjuk el.
  * A SSML szövegek esetében az egyes SSML-darabok bekezdésnek tekintendők. A SSML-darabokat különböző bekezdések szerint kell elválasztani – a [SSML szövegének](https://github.com/Azure-Samples/Cognitive-Speech-TTS/blob/master/CustomVoice-API-Samples/Java/SSMLTextInputSample.txt) megjelenítése – példa
> [!NOTE]
> Kínai (anyaországi), Kínai (Hongkong), Kínai (Tajvan), Japán és koreai nyelveken az egyik szó két karakterből áll. 

## <a name="submit-synthesis-requests"></a>Szintézisi kérelmek elküldése

A bemeneti tartalom előkészítése után a kérelem elküldéséhez kövesse a [hosszú formátumú hangszintézis rövid](https://aka.ms/long-audio-python) útmutatóját. Ha egynél több bemeneti fájllal rendelkezik, több kérést is el kell küldenie. Néhány korlátozást figyelembe kell venni: 
* Az ügyfél legfeljebb 5 kérést küldhet a kiszolgálónak másodpercenként az egyes Azure-előfizetési fiókokhoz. Ha meghaladja a korlátozást, akkor az ügyfél 429 hibakódot kap (túl sok kérés). Csökkentse a kérelmek mennyiségét másodpercenként
* A kiszolgáló az egyes Azure-előfizetésekhez tartozó fiókokhoz legfeljebb 120 kérelmet futtathat, és a várólistára helyezhető. Ha meghaladja a korlátozást, a kiszolgáló 429 hibakódot ad vissza (túl sok kérés). Várjon, és ne küldje el az új kérést, amíg néhány kérelem be nem fejeződik
* A kiszolgáló minden egyes Azure-előfizetési fiók esetében 20 000-kérelmeket tart fenn. Ha túllépi a korlátozást, töröljön néhány kérelmet az új adatbázisok elküldése előtt

## <a name="audio-output-formats"></a>Hangkimeneti formátumok

A rugalmas hangkimeneti formátumokat támogatjuk. A "concatenateResult" paraméter beállításával egy adott kimenetet adhat meg egy bekezdésben, vagy összefűzheti a hanganyagokat egy kimenetben. A hosszú hang API a következő hangkimeneti formátumokat támogatja:

> [!NOTE]
> Az alapértelmezett hang formátuma a riff-16khz-16bit-mono-PCM.

* riff-8khz-16bit-mono-PCM
* riff-16khz-16bit-mono-PCM
* riff-24khz-16bit-mono-PCM
* riff-48kHz-16bit-mono-PCM
* hang-16khz-32kbitrate-mono-MP3
* hang-16khz-64kbitrate-mono-MP3
* hang-16khz-128kbitrate-mono-MP3
* hang-24khz-48kbitrate-mono-MP3
* hang-24khz-96kbitrate-mono-MP3
* hang-24khz-160kbitrate-mono-MP3

## <a name="quickstarts"></a>Gyorsútmutatók

A hosszú hangalapú API sikeres futtatását segítő gyors útmutatókat kínálunk. Ez a táblázat a hosszú hangalapú API-gyors útmutatók listáját tartalmazza, nyelv szerint rendezve.

* [Gyors útmutató: Python](https://aka.ms/long-audio-python)

## <a name="sample-code"></a>Mintakód
A hosszú hang API-mintakód elérhető a GitHubon.

* [Mintakód: Python](https://github.com/Azure-Samples/Cognitive-Speech-TTS/tree/master/CustomVoice-API-Samples/Python)
* [Mintakód:C#](https://github.com/Azure-Samples/Cognitive-Speech-TTS/tree/master/CustomVoice-API-Samples/CSharp)
* [Mintakód: Java](https://github.com/Azure-Samples/Cognitive-Speech-TTS/blob/master/CustomVoice-API-Samples/Java/)
