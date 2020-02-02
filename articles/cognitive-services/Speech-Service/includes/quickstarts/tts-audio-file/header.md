---
author: IEvangelist
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 01/31/2020
ms.author: dapine
ms.openlocfilehash: cd8da5eb9313685361ca56b56c024c2dfb37276e
ms.sourcegitcommit: fa6fe765e08aa2e015f2f8dbc2445664d63cc591
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/01/2020
ms.locfileid: "76961365"
---
Ebben a rövid útmutatóban a [SPEECH SDK](~/articles/cognitive-services/speech-service/speech-sdk.md) használatával konvertálja a szöveget egy hangfájlba a szintetizált beszédbe. A Text-to-speech szolgáltatás számos lehetőséget kínál a szintetizált hangok számára a [szöveg-beszéd nyelvi támogatás](../../../language-support.md#text-to-speech)alatt. Néhány előfeltétel kielégítése után a beszédfelismerés egy fájlba csak öt lépést vesz igénybe:
> [!div class="checklist"]
> * Hozzon létre egy `SpeechConfig` objektumot az előfizetési kulcsból és régióból.
> * Hozzon létre egy hangkonfigurációs objektumot, amely meghatározza a t. WAV-fájlnév.
> * Hozzon létre egy `SpeechSynthesizer` objektumot a fenti konfigurációs objektumok használatával.
> * A `SpeechSynthesizer` objektum használatával alakítsa át a szöveget a szintetizált beszédbe, és mentse a megadott hangfájlba.
> * Ellenőrizze, hogy a `SpeechSynthesizer` hibákat adott-e vissza.
