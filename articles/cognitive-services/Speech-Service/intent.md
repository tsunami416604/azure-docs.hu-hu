---
title: Leképezési felismerés minta |} Microsoft Docs
titleSuffix: Microsoft Cognitive Services
description: Íme egy minta leképezési felismeréséhez.
services: cognitive-services
author: wolfma61
manager: onano
ms.service: cognitive-services
ms.technology: Speech
ms.topic: article
ms.date: 06/07/2018
ms.author: wolfma
ms.openlocfilehash: 38f7f038a803546adb83245519efc5de0c0d1599
ms.sourcegitcommit: 0c490934b5596204d175be89af6b45aafc7ff730
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/27/2018
ms.locfileid: "37045010"
---
# <a name="sample-for-intent-recognition"></a>A leképezési felismerés minta

> [!NOTE]
> Ez a minta és mások letöltése útmutatásért lásd: [beszéd SDK-példák](samples.md).

> [!NOTE]
> Először szerezze be egy előfizetés kulcs. Más a kognitív beszéd SDK által támogatott szolgáltatásokkal, ellentétben a leképezés felismerés szolgáltatások adott előfizetés kulcs szükséges. [Itt](https://www.luis.ai) található a leképezési felismerés technológiával kapcsolatos további információk, valamint egy előfizetés kulcs beszerzésére vonatkozó információkat. Cserélje le a saját előfizetés kulcs, a szolgáltatás a területet, és a megfelelő helyen, a mintában a leképezési modell AppId.

> [!NOTE]
> Az összes, az alábbi minta a következő legfelső szintű nyilatkozatokat helyen kell lennie:
>
> [!code-cpp[](~/samples-cognitive-services-speech-sdk/Windows/cxx_samples/intent_recognition_samples.cpp#toplevel)]
>
> - - -

## <a name="intent-recognition-using-microphone"></a>Leképezési nevéből felismert tartománynév használatával mikrofon

Az alábbi kódrészlet bemutatja, hogyan ismeri fel az alapértelmezett nyelv a mikrofon bemeneti leképezés (`en-US`).

[!code-cpp[Intent Recognition Using Microphone](~/samples-cognitive-services-speech-sdk/Windows/cxx_samples/intent_recognition_samples.cpp#IntentRecognitionWithMicrophone)]

- - -

## <a name="intent-recognition-using-microphone-in-a-specified-language"></a>Leképezési felismerés mikrofon használatát egy megadott nyelv

Az alábbi kódrészlet bemutatja, hogyan ismeri fel ebben az esetben a megadott nyelven mikrofon bemeneti leképezés német (`de-de`).

[!code-cpp[Intent Recognition Using Microphone In A Specified Language](~/samples-cognitive-services-speech-sdk/Windows/cxx_samples/intent_recognition_samples.cpp#IntentRecognitionWithLanguage)]

- - -

## <a name="intent-recognition-from-a-file"></a>Leképezési felismerés fájlból

A következő kódrészletet felismeri az alapértelmezett nyelven hangfájl leképezés (`en-US`), a támogatott formátuma (monó) egyetlen-csatorna WAV / 16 kHz mintavételi sebességet PCM.

[!include[Sample Audio](includes/sample-audio.md)]

[!code-cpp[Intent Recognition From a File](~/samples-cognitive-services-speech-sdk/Windows/cxx_samples/intent_recognition_samples.cpp#IntentRecognitionWithFile)]

- - -

## <a name="intent-recognition-using-events"></a>Leképezési nevéből felismert tartománynév használatával események

A kódrészletet ismeri fel a cél folyamatos mutatja be. Ez a kód lehetővé teszi, hogy a további adatok, például a köztes eredmények elérésére. 

[!code-cpp[Intent Recognition Using Events](~/samples-cognitive-services-speech-sdk/Windows/cxx_samples/intent_recognition_samples.cpp#IntentContinuousRecognitionUsingEvents)]

- - -

## <a name="sample-source-code"></a>Forrás mintakód

A minták legújabb készletét, tekintse meg a [kognitív szolgáltatások beszéd SDK minta GitHub-tárházban](https://aka.ms/csspeech/samples).

## <a name="next-steps"></a>További lépések

- [Beszédfelismerés](./speech-to-text-sample.md)

- [Fordítás](./translation.md)
