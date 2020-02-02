---
author: IEvangelist
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 01/31/2020
ms.author: dapine
ms.openlocfilehash: e9f02f95693552180a0eed1550cc59d8f975416b
ms.sourcegitcommit: fa6fe765e08aa2e015f2f8dbc2445664d63cc591
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/01/2020
ms.locfileid: "76961397"
---
Ebben a rövid útmutatóban a [SPEECH SDK](~/articles/cognitive-services/speech-service/speech-sdk.md) használatával alakítja át a szöveget a szintetizált beszédbe. A Text-to-speech szolgáltatás számos lehetőséget kínál a szintetizált hangok számára a [szöveg-beszéd nyelvi támogatás](../../../language-support.md#text-to-speech)alatt. Néhány előfeltétel kielégítése után a szintetizált beszéd az alapértelmezett hangszórókra való renderelése csak négy lépést vesz igénybe:
> [!div class="checklist"]
> * Hozzon létre egy `SpeechConfig` objektumot az előfizetési kulcsból és régióból.
> * Hozzon létre egy `SpeechSynthesizer` objektumot a fenti `SpeechConfig` objektum használatával.
> * Az `SpeechSynthesizer` objektum használatával beszéljen a szövegről.
> * Győződjön meg arról, hogy a hibákat a `SpeechSynthesisResult` visszaadja.
