---
title: Long audio API (előzetes verzió) – Speech Service
titleSuffix: Azure Cognitive Services
description: ''
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 10/26/2019
ms.author: erhopf
ms.openlocfilehash: b17b7088276aca6d7d8ed8f2d1cb554479b4de2b
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/04/2019
ms.locfileid: "73507637"
---
# <a name="long-audio-api-preview"></a>Long audio API (előzetes verzió)

A hosszú hangalapú API a hosszú formátumú szöveg és a beszéd közötti aszinkron szintézishez lett tervezve (például: hangkönyvek). Ez az API nem adja vissza a szintetizált hangot valós időben, ezért a várt érték az, hogy a válasz (ok) ra fog szavazni, és a kimenet (eke) t a szolgáltatás által elérhetővé tettnek megfelelően használja fel. A Speech SDK által használt szöveg-beszéd API-val ellentétben a hosszú hang-API 10 percnél hosszabb szintetizált hangot is létrehozhat, így a kiadók és a hangtartalom-platformok ideálisak.

A hosszú hang API további előnyei:

* A szolgáltatás által visszaadott szintetizált beszéd neurális hangokat használ, ami biztosítja a magas megbízhatóságú hangkimeneteket.
* Mivel a valós idejű válaszok nem támogatottak, nincs szükség hang-végpont üzembe helyezésére.

## <a name="workflow"></a>Munkafolyamat

A hosszú hangalapú API használatakor a rendszer a szintetizált szövegfájlt vagy fájlokat küldi el, lekérdezi az állapotot, majd ha az állapot sikeres, letöltheti az audió kimenetet.

Ez az ábra a munkafolyamat magas szintű áttekintését tartalmazza.

![Hosszú hang-API munkafolyamat-diagram](media/long-audio-api/long-audio-api-workflow.png)

## <a name="prepare-content-for-synthesis"></a>Tartalom előkészítése a szintézishez

A szövegfájl előkészítésekor győződjön meg róla, hogy:

* Egyszerű szöveges (. txt) vagy SSML szöveg (. txt)
  * Egyszerű szöveg esetén az egyes bekezdéseket az ENTER/Return – [egyszerű szöveges beviteli példa](https://github.com/Azure-Samples/Cognitive-Speech-TTS/blob/master/CustomVoice-API-Samples/Java/en-US.txt) **megadásával** választjuk el.
  * A SSML szövegek esetében az egyes SSML-darabok bekezdésnek tekintendők. A SSML-darabokat különböző bekezdések szerint kell elválasztani – lásd a [SSML szöveges bemeneti példáját](https://github.com/Azure-Samples/Cognitive-Speech-TTS/blob/master/CustomVoice-API-Samples/Java/SSMLTextInputSample.txt). A nyelvi kódokkal kapcsolatban lásd: [Speech szintézis Markup Language (SSML)](speech-synthesis-markup.md)
* Kódolása [UTF-8, byte Order Mark (BOM)](https://www.w3.org/International/questions/qa-utf8-bom.en#bom) értékkel
* Több mint 10 000 karaktert vagy több mint 50 bekezdést tartalmaz
* Egyetlen fájl, nem zip

## <a name="audio-output-formats"></a>Hangkimeneti formátumok

A hosszú hang API a következő hangkimeneti formátumokat támogatja:

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

## <a name="see-also"></a>Lásd még

* [Hosszú hang API-referenciája](https://aka.ms/long-audio-ref)
