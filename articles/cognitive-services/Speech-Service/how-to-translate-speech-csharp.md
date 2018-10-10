---
title: A beszédfelismerés SDK használata a C# használatával beszédfelismerési fordítása
titleSuffix: Microsoft Cognitive Services
description: Bemutatja, hogyan lefordítani a beszédfelismerés, beszédfelismerési SDK használata a C# használatával.
services: cognitive-services
author: wolfma61
ms.service: cognitive-services
ms.component: Speech
ms.topic: article
ms.date: 09/24/2018
ms.author: wolfma
ms.openlocfilehash: 2e69dabb3be0aada952fe3acba4d4c0b30f62945
ms.sourcegitcommit: 55952b90dc3935a8ea8baeaae9692dbb9bedb47f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2018
ms.locfileid: "48887615"
---
# <a name="translate-speech-with-the-cognitive-services-speech-sdk-for-c"></a>A C# lefordítja a beszéd, a Cognitive Services beszéd SDK-val

[!INCLUDE [Selector](../../../includes/cognitive-services-speech-service-how-to-translate-speech-selector.md)]

[!INCLUDE [Introduction](../../../includes/cognitive-services-speech-service-how-to-translate-speech-intro.md)]

[!INCLUDE [Introduction to top-level declarations](../../../includes/cognitive-services-speech-service-how-to-toplevel-declarations.md)]

[!code-csharp[Top-level declarations](~/samples-cognitive-services-speech-sdk/samples/csharp/sharedcontent/console/translation_samples.cs#toplevel)]

[!INCLUDE [Introduction to using a microphone](../../../includes/cognitive-services-speech-service-how-to-translate-speech-microphone.md)]

[!code-csharp[Translation from a microphone](~/samples-cognitive-services-speech-sdk/samples/csharp/sharedcontent/console/translation_samples.cs#TranslationWithMicrophoneAsync)]

[!INCLUDE [Introduction to using a file](../../../includes/cognitive-services-speech-service-how-to-translate-speech-file.md)]

[!code-csharp[Translation from file input](~/samples-cognitive-services-speech-sdk/samples/csharp/sharedcontent/console/translation_samples.cs#TranslationWithFileAsync)]

[!INCLUDE [Download the sample](../../../includes/cognitive-services-speech-service-speech-sdk-sample-download-h2.md)]
Keresse meg a kódot, amely ebben a cikkben a konzol samples/csharp/sharedcontent mappában szolgál.

## <a name="next-steps"></a>További lépések

- [Beszéd felismerése](how-to-recognize-speech-csharp.md)
- [A beszédfelismerés leképezések felismerése](how-to-recognize-intents-from-speech-csharp.md)
