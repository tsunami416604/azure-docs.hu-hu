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
ms.openlocfilehash: 24fa3882a65bf6605444a139ad5d4ee42800a8ef
ms.sourcegitcommit: b5ac31eeb7c4f9be584bb0f7d55c5654b74404ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/23/2018
ms.locfileid: "42744929"
---
# <a name="recognize-speech-by-using-the-rest-api"></a>A beszédfelismerést a REST API-val

[!include[Selector](../../../includes/cognitive-services-speech-service-how-to-recognize-speech-selector.md)]

A REST API használatával a HTTP POST-kérelmet rövid utterances felismerni használható.

A REST API-t a legegyszerűbb módja a beszédfelismerést használatakor nem támogatja a [SDK](speech-sdk.md).
Meghatározhat egy HTTP POST kérelmet a szolgáltatásvégpont, adja át a kérelem; törzsében lévő teljes utterance (kifejezés) és a egy választ, amely tartalmazza a felismert szöveget kap.

> [!NOTE]
> Kimondott szöveg, legfeljebb 15 másodperc vagy annál kisebb, a REST API használatakor.
> Tekintse meg a [beszéd SDK](how-to-recognize-speech-csharp.md) hosszabb utterances felismeréséhez.

További információ a **Speech to Text** – REST API-t, lásd: [REST API-k](rest-apis.md#speech-to-text). Megtekintés működés közben, töltse le a [REST API minta](https://github.com/Azure-Samples/SpeechToText-REST) a Githubról.

## <a name="next-steps"></a>További lépések

- [Tekintse meg a REST API áttekintése](rest-apis.md)
