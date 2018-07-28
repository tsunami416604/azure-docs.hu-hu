---
title: A beszédfelismerést a REST API-val
description: Diktálás használata a Speech service-ben
titleSuffix: Microsoft Cognitive Services
services: cognitive-services
author: v-jerkin
ms.service: cognitive-services
ms.component: speech-service
ms.topic: article
ms.date: 07/16/2018
ms.author: v-jerkin
ms.openlocfilehash: 15db2d9b872c76d70fd531af07fb55c701e86494
ms.sourcegitcommit: 7ad9db3d5f5fd35cfaa9f0735e8c0187b9c32ab1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/27/2018
ms.locfileid: "39331438"
---
# <a name="recognize-speech-by-using-the-rest-api"></a>A beszédfelismerést a REST API-val

[!include[Selector](../../../includes/cognitive-services-speech-service-how-to-recognize-speech-selector.md)]

A REST API használatával a HTTP POST-kérelmet rövid utterances felismerni használható.

A REST API egy a legegyszerűbb módszer beszédfelismerő ismeri fel, ha nem használja az SDK támogatja.
Választja ki egy HTTP POST-kérelmet a szolgáltatásvégpont átadja a kérelem törzsében lévő teljes utterance (kifejezés).
A felismert szöveget tartalmazó választ kap.

> [!NOTE]
> Kimondott szöveg, legfeljebb 15 másodperc vagy annál kisebb, a REST API használatakor.
> Tekintse meg a [beszéd SDK](how-to-recognize-speech-csharp.md) hosszabb utterances felismeréséhez.

További információ a **Speech to Text** – REST API-t, lásd: [REST API-k](rest-apis.md#speech-to-text). Megtekintés működés közben, töltse le a [REST API minta](https://github.com/Azure-Samples/SpeechToText-REST) a Githubról.

## <a name="next-steps"></a>További lépések

- [Tekintse meg a REST API áttekintése](rest-apis.md)
