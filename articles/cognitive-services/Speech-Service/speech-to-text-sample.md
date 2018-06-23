---
title: A beszéd-szöveg minta |} Microsoft Docs
titleSuffix: Microsoft Cognitive Services
description: Íme egy minta beszéd-szöveg.
services: cognitive-services
author: wolfma61
manager: onano
ms.service: cognitive-services
ms.technology: Speech
ms.topic: article
ms.date: 05/07/2018
ms.author: wolfma
ms.openlocfilehash: 9e0b82cc9a034f1e25ef0cc656e3770a166a6ad7
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/23/2018
ms.locfileid: "35349726"
---
# <a name="sample-for-speech-to-text"></a>Minta beszéd-szöveg

> [!NOTE]
> Ez a minta és mások letöltése útmutatásért lásd: [beszéd SDK-példák](samples.md).

[!include[Get a Subscription Key](includes/get-subscription-key.md)]

> [!NOTE]
> Az összes alábbi minták a következő legfelső szintű nyilatkozatokat helyen kell lennie:
>
> [!code-csharp[](~/samples-cognitive-services-speech-sdk/Windows/csharp_samples/speech_recognition_samples.cs#toplevel)]
>
> [!code-cpp[](~/samples-cognitive-services-speech-sdk/Windows/cxx_samples/speech_recognition_samples.cpp#toplevel)]
>
> - - -

## <a name="speech-recognition-using-the-microphone"></a>A Beszédfelismerés a mikrofon használatával

Az alábbi kódrészlet bemutatja, hogyan ismeri fel az alapértelmezett nyelven mikrofon bemeneti (`en-US`).

[!code-csharp[Speech Recognition Using Microphone](~/samples-cognitive-services-speech-sdk/Windows/csharp_samples/speech_recognition_samples.cs#recognitionWithMicrophone)]

[!code-cpp[Speech Recognition Using Microphone](~/samples-cognitive-services-speech-sdk/Windows/cxx_samples/speech_recognition_samples.cpp#SpeechRecognitionWithMicrophone)]

- - -

## <a name="speech-recognition-from-a-file"></a>Beszédfelismerés fájlból

A következő kódrészletet felismeri az alapértelmezett nyelven hangfájl bemeneti (`en-US`), a támogatott formátuma (monó) egyetlen-csatorna WAV / 16 kHz mintavételi sebességet PCM.

[!include[Sample Audio](includes/sample-audio.md)]

[!code-csharp[Speech Recognition From a File](~/samples-cognitive-services-speech-sdk/Windows/csharp_samples/speech_recognition_samples.cs?name=recognitionFromFile)]

[!code-cpp[Speech Recognition From a File](~/samples-cognitive-services-speech-sdk/Windows/cxx_samples/speech_recognition_samples.cpp?name=SpeechRecognitionWithFile)]

- - -

## <a name="speech-recognition-using-a-customized-model"></a>A beszédfelismerés testreszabott modell segítségével

A [egyéni beszéd szolgáltatás (CRIS)](https://www.cris.ai/) lehetővé teszi, hogy a testreszabás, a Microsoft beszéd-szöveg motor az alkalmazáshoz. Az alábbi részlet mutatja be a CRIS modellel; mikrofon a beszéd felismerésére Adja meg a CRIS előfizetés kulcs és a saját telepítési azonosító, mielőtt futtatná azt.

[!code-csharp[Speech Recognition Using a Customized Model](~/samples-cognitive-services-speech-sdk/Windows/csharp_samples/speech_recognition_samples.cs#recognitionCustomized)]

[!code-cpp[Speech Recognition Using a Customized Model](~/samples-cognitive-services-speech-sdk/Windows/cxx_samples/speech_recognition_samples.cpp#SpeechRecognitionUsingCustomizedModel)]

- - -

## <a name="continuous-speech-recognition"></a>Folyamatos beszédfelismerés

[!code-csharp[Continuous Speech Recognition](~/samples-cognitive-services-speech-sdk/Windows/csharp_samples/speech_recognition_samples.cs#recognitionContinuous)]

[!code-cpp[Continuous Speech Recognition](~/samples-cognitive-services-speech-sdk/Windows/cxx_samples/speech_recognition_samples.cpp#SpeechContinuousRecognitionUsingEvents)]

- - -

## <a name="sample-source-code"></a>Forrás mintakód

A legújabb verzióját a mintákat és továbbiakat is speciális minták vannak egy dedikált [GitHub-tárházban](https://github.com/Azure-Samples/cognitive-services-speech-sdk).

## <a name="next-steps"></a>További lépések

- [Leképezési felismerés](./intent.md)

- [Fordítási](./translation.md)
