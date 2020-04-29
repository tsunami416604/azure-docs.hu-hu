---
author: trevorbye
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 01/31/2020
ms.author: trbye
ms.openlocfilehash: 005cf83508d25e8f44190e07336fbb4e444f8e6b
ms.sourcegitcommit: 34a6fa5fc66b1cfdfbf8178ef5cdb151c97c721c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "81400377"
---
Ebben a rövid útmutatóban a [SPEECH SDK](~/articles/cognitive-services/speech-service/speech-sdk.md) használatával alakítja át a szöveget a szintetizált beszédbe. A Text-to-speech szolgáltatás számos lehetőséget kínál a szintetizált hangok számára a [szöveg-beszéd nyelvi támogatás](../../../language-support.md#text-to-speech)alatt. Néhány előfeltétel kielégítése után a szintetizált beszéd az alapértelmezett hangszórókra való renderelése csak négy lépést vesz igénybe:
> [!div class="checklist"]
> * Hozzon `SpeechConfig` létre egy objektumot az előfizetési kulcs és a régió alapján.
> * Hozzon `SpeechSynthesizer` létre egy objektumot `SpeechConfig` a fenti objektum használatával.
> * Az `SpeechSynthesizer` objektum használatával beszéljen a szövegről.
> * Győződjön meg `SpeechSynthesisResult` róla, hogy a visszaadott hibák történtek.
