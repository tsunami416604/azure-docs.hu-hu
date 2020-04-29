---
author: trevorbye
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 01/31/2020
ms.author: trbye
ms.openlocfilehash: 870dce55e79bf0169f19d31dfec6689c65fce9cd
ms.sourcegitcommit: 34a6fa5fc66b1cfdfbf8178ef5cdb151c97c721c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "81400360"
---
Ebben a rövid útmutatóban a [SPEECH SDK](~/articles/cognitive-services/speech-service/speech-sdk.md) használatával konvertálja a szöveget egy hangfájlba a szintetizált beszédbe. A Text-to-speech szolgáltatás számos lehetőséget kínál a szintetizált hangok számára a [szöveg-beszéd nyelvi támogatás](../../../language-support.md#text-to-speech)alatt. Néhány előfeltétel kielégítése után a beszédfelismerés egy fájlba csak öt lépést vesz igénybe:
> [!div class="checklist"]
> * Hozzon `SpeechConfig` létre egy objektumot az előfizetési kulcs és a régió alapján.
> * Hozzon létre egy hangkonfigurációs objektumot, amely meghatározza a t. WAV-fájlnév.
> * Hozzon `SpeechSynthesizer` létre egy objektumot a fenti konfigurációs objektumok használatával.
> * Az `SpeechSynthesizer` objektum használatával alakítsa át a szöveget a szintetizált beszédbe, és mentse a megadott hangfájlba.
> * Ellenőrizze a `SpeechSynthesizer` visszaadott hibákat.
