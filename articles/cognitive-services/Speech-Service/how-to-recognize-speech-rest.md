---
title: A beszédfelismerést a REST API-val
description: Ismerje meg, hogyan használható a Speech to Text API a Cognitive Services beszéd szolgáltatásban.
titleSuffix: Microsoft Cognitive Services
services: cognitive-services
author: erhopf
ms.service: cognitive-services
ms.component: speech-service
ms.topic: article
ms.date: 07/16/2018
ms.author: erhopf
ms.openlocfilehash: 4e87feae7e3321efe7adcdbf1302762f539f034d
ms.sourcegitcommit: c282021dbc3815aac9f46b6b89c7131659461e49
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/12/2018
ms.locfileid: "49167273"
---
# <a name="recognize-speech-by-using-the-rest-api"></a>A beszédfelismerést a REST API-val

[!INCLUDE [Selector](../../../includes/cognitive-services-speech-service-how-to-recognize-speech-selector.md)]

A REST API segítségével rövid utterances felismerni egy HTTP POST-kérelem használatával.

A REST API egy a legegyszerűbb módszer beszédfelismerő ismeri fel, ha nem használ egy nyelv által támogatott a [SDK](speech-sdk.md). Győződjön meg a HTTP POST-kérelmet a szolgáltatásvégpont, és adja át a kérelem törzsében lévő teljes utterance (kifejezés). A felismert szöveget tartalmazó választ kap.

> [!NOTE]
> Beszédmódok korlátozva, 15 másodperc vagy kevesebb, mint a REST API használata esetén.
> Tekintse meg a [beszéd SDK](how-to-recognize-speech-csharp.md) hosszabb utterances felismeréséhez.

További információ a **Speech to Text** – REST API-t, tekintse meg a [REST API-k](rest-apis.md#speech-to-text) cikk. Az API-t működés közben látni, töltse le a [REST API minta](https://github.com/Azure-Samples/SpeechToText-REST) a Githubról.

## <a name="next-steps"></a>További lépések

- Tekintse meg a [REST API – áttekintés](rest-apis.md).
