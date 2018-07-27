---
title: Minta Szándékfelismerés
titleSuffix: Microsoft Cognitive Services
description: Íme egy minta szándékának felismerése.
services: cognitive-services
author: wolfma61
ms.service: cognitive-services
ms.technology: Speech
ms.topic: article
ms.date: 07/16/2018
ms.author: wolfma
ms.openlocfilehash: a0d1575787b83b95a52a2097f59a06e3c5b5bc6f
ms.sourcegitcommit: 068fc623c1bb7fb767919c4882280cad8bc33e3a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/27/2018
ms.locfileid: "39285105"
---
# <a name="sample-for-intent-recognition"></a>Minta szándékfelismerés

Először szerezze be egy előfizetési kulcsot. Szakembereket más a Cognitive Speech SDK által támogatott szolgáltatások a szándék felismerés szolgáltatások szükséges egy adott előfizetési kulcsot. [Itt](https://www.luis.ai) talál további információt szeretne a szándékának felismerése, valamint egy előfizetési kulcsot beszerezni információ. Cserélje le a saját Language Understanding előfizetési kulcs, a [régió az előfizetés](regions.md), és a minták a megfelelő helyen a szándék modell alkalmazásazonosítója.

## <a name="top-level-declarations"></a>Legfelső szintű nyilatkozatok

Az összes, az alábbi minta a következő legfelső szintű nyilatkozatokat helyén kell lennie:

[!code-csharp[Top-level declarations](~/samples-cognitive-services-speech-sdk/samples/csharp/sharedcontent/console/intent_recognition_samples.cs#toplevel)]

[!code-cpp[Top-level declarations](~/samples-cognitive-services-speech-sdk/samples/cpp/windows/console/samples/intent_recognition_samples.cpp#toplevel)]

[!code-java[Top-level declarations](~/samples-cognitive-services-speech-sdk/samples/java/jre/console/src/com/microsoft/cognitiveservices/speech/samples/console/IntentRecognitionSamples.java#toplevel)]

## <a name="intent-recognition-using-microphone"></a>Mikrofon használatával szándékfelismerés

Az alábbi kódrészlet bemutatja, hogyan szándékfelismerés mikrofon bemeneti az alapértelmezett nyelven (`en-US`).

[!code-csharp[Intent Recognition Using Microphone](~/samples-cognitive-services-speech-sdk/samples/csharp/sharedcontent/console/intent_recognition_samples.cs#intentRecognitionWithMicrophone)]

[!code-cpp[Intent Recognition Using Microphone](~/samples-cognitive-services-speech-sdk/samples/cpp/windows/console/samples/intent_recognition_samples.cpp#IntentRecognitionWithMicrophone)]

[!code-java[Intent Recognition Using Microphone](~/samples-cognitive-services-speech-sdk/samples/java/jre/console/src/com/microsoft/cognitiveservices/speech/samples/console/IntentRecognitionSamples.java#IntentRecognitionWithMicrophone)]

## <a name="intent-recognition-using-microphone-in-a-specified-language"></a>A megadott nyelvre a mikrofon használatával szándékfelismerés

Az alábbi kódrészlet bemutatja, hogyan szándékfelismerés mikrofon bemeneti a megadott nyelvre, ebben az esetben a német (`de-de`).

[!code-csharp[Intent Recognition Using Microphone In A Specified Language](~/samples-cognitive-services-speech-sdk/samples/csharp/sharedcontent/console/intent_recognition_samples.cs#intentRecognitionWithLanguage)]

[!code-cpp[Intent Recognition Using Microphone In A Specified Language](~/samples-cognitive-services-speech-sdk/samples/cpp/windows/console/samples/intent_recognition_samples.cpp#IntentRecognitionWithLanguage)]

[!code-java[Intent Recognition Using Microphone In A Specified Language](~/samples-cognitive-services-speech-sdk/samples/java/jre/console/src/com/microsoft/cognitiveservices/speech/samples/console/IntentRecognitionSamples.java#IntentRecognitionWithLanguage)]

## <a name="intent-recognition-from-a-file-using-events"></a>Egy fájlból, események használatával szándékfelismerés

A kódrészlet azt mutatja be, hogyan szándékfelismerés az alapértelmezett nyelven (`en-US`) folyamatos. Ez a kód lehetővé teszi, hogy további információkhoz, például a köztes eredményeket. Hangfájl bemeneti forrása, a támogatott formátum: egyetlen csatornát (mono) WAV / 16 KHz mintavételi arány PCM.

[!code-csharp[Intent Recognition Using Events From a File](~/samples-cognitive-services-speech-sdk/samples/csharp/sharedcontent/console/intent_recognition_samples.cs#intentContinuousRecognitionWithFile)]

[!code-cpp[Intent Recognition Using Events From a File](~/samples-cognitive-services-speech-sdk/samples/cpp/windows/console/samples/intent_recognition_samples.cpp#IntentContinuousRecognitionWithFile)]

[!code-java[Intent Recognition Using Events From a File](~/samples-cognitive-services-speech-sdk/samples/java/jre/console/src/com/microsoft/cognitiveservices/speech/samples/console/IntentRecognitionSamples.java#IntentContinuousRecognitionWithFile)]

[!include[Download the sample](../../../includes/cognitive-services-speech-service-speech-sdk-sample-download-h2.md)]

## <a name="next-steps"></a>További lépések

- [Beszédfelismerés](./speech-to-text-sample.md)

- [Fordítás](./translation.md)
