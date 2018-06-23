---
title: A minta-re történő fordításhoz |} Microsoft Docs
titleSuffix: Microsoft Cognitive Services
description: Íme egy minta beszéd fordításhoz.
services: cognitive-services
author: wolfma61
manager: onano
ms.service: cognitive-services
ms.technology: Speech
ms.topic: article
ms.date: 05/07/2018
ms.author: wolfma
ms.openlocfilehash: a02a89de1d05ec5388a92f14a0fd206e0ee966a6
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/23/2018
ms.locfileid: "35349698"
---
# <a name="sample-for-translation"></a>A fordítás minta

> [!NOTE]
> Ez a minta és mások letöltése útmutatásért lásd: [beszéd SDK-példák](samples.md).

[!include[Get a Subscription Key](includes/get-subscription-key.md)]

> [!NOTE]
> Az összes, az alábbi minta a következő legfelső szintű nyilatkozatokat helyen kell lennie:
>
> [!code-csharp [Using Statements](~/samples-cognitive-services-speech-sdk/Windows/csharp_samples/translation_samples.cs#toplevel)]
>
> - - -

## <a name="translation-using-the-microphone"></a>A fordítás a mikrofon használatával

Az alábbi kódrészlet ismerteti, amelyek bemeneti az angol német, és a lefordított szöveg hang kimenetének is beolvasása. A mikrofon használ.

[!code-csharp[Translation Using Microphone](~/samples-cognitive-services-speech-sdk/Windows/csharp_samples/translation_samples.cs#TranslationWithMicrophoneAsync)]

- - -

## <a name="translation-using-file-input"></a>Fordítási bemeneti fájl használatával

Az alábbi kódrészlet bemutatja, hogyan bemeneti az angol, német és francia lefordítani.
Fájl használja bemeneti adatként.

[!code-csharp[Translation Using File Input](~/samples-cognitive-services-speech-sdk/Windows/csharp_samples/translation_samples.cs#TranslationWithFileAsync)]

- - -

## <a name="sample-source-code"></a>Forrás mintakód

A legújabb verzióját a mintákat és továbbiakat is speciális minták vannak egy dedikált [GitHub-tárházban](https://github.com/Azure-Samples/cognitive-services-speech-sdk).

## <a name="next-steps"></a>További lépések

- [A beszédfelismerés](./speech-to-text-sample.md)

- [Leképezési felismerés](./intent.md)
