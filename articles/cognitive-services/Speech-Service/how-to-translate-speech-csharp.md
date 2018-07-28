---
title: A beszédfelismerés SDK használata a C# használatával beszédfelismerési fordítása
titleSuffix: Microsoft Cognitive Services
description: Bemutatja, hogyan lefordítani a beszédfelismerés, beszédfelismerési SDK használata a C# használatával.
services: cognitive-services
author: wolfma61
ms.service: cognitive-services
ms.technology: Speech
ms.topic: article
ms.date: 07/16/2018
ms.author: wolfma
ms.openlocfilehash: 928d8dcf9a074eb78c17393d8dc8e11431ef3d55
ms.sourcegitcommit: 7ad9db3d5f5fd35cfaa9f0735e8c0187b9c32ab1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/27/2018
ms.locfileid: "39331436"
---
# <a name="translate-speech-by-using-the-speech-sdk-for-c"></a>A beszédfelismerés SDK használata a C# használatával beszédfelismerési fordítása

[!include[Selector](../../../includes/cognitive-services-speech-service-how-to-translate-speech-selector.md)]

[!include[Intro](../../../includes/cognitive-services-speech-service-how-to-translate-speech-intro.md)]

[!include[Intro - top-level declarations](../../../includes/cognitive-services-speech-service-how-to-toplevel-declarations.md)]

[!code-csharp[Top-level declarations](~/samples-cognitive-services-speech-sdk/samples/csharp/sharedcontent/console/translation_samples.cs#toplevel)]

[!include[Intro - using microphone](../../../includes/cognitive-services-speech-service-how-to-translate-speech-microphone.md)]

[!code-csharp[Translation Using Microphone](~/samples-cognitive-services-speech-sdk/samples/csharp/sharedcontent/console/translation_samples.cs#TranslationWithMicrophoneAsync)]

[!include[Intro - using file](../../../includes/cognitive-services-speech-service-how-to-translate-speech-file.md)]

[!code-csharp[Translation Using File Input](~/samples-cognitive-services-speech-sdk/samples/csharp/sharedcontent/console/translation_samples.cs#TranslationWithFileAsync)]

[!include[Download the sample](../../../includes/cognitive-services-speech-service-speech-sdk-sample-download-h2.md)]
Keresse meg a jelen cikkben lévő kódot az `samples/csharp/sharedcontent/console` mappát.

## <a name="next-steps"></a>További lépések

- [Hogyan ismerhetik fel a beszédfelismerés](how-to-recognize-speech-csharp.md)
- [Hogyan ismerhetik fel a speech leképezések](how-to-recognize-intents-from-speech-csharp.md).
