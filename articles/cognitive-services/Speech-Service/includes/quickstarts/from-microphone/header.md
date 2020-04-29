---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 04/02/2020
ms.author: trbye
ms.openlocfilehash: 36243fd5db76a4706318f41b3e2cb3f557c17189
ms.sourcegitcommit: 34a6fa5fc66b1cfdfbf8178ef5cdb151c97c721c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "81400814"
---
Ebben a rövid útmutatóban a [SPEECH SDK](~/articles/cognitive-services/speech-service/speech-sdk.md) használatával interaktív módon ismeri fel a mikrofon bemenetét, és beolvassa a szöveges átírást a rögzített hangból. Egyszerűen integrálhatja ezt a funkciót az alkalmazásaiba vagy eszközeibe az általános felismerési feladatokhoz, például a beszélgetések átírásához. Emellett összetettebb integrációk is használhatók, például a bot Framework és a Speech SDK együttes használata a hangsegédek létrehozásához.

Néhány előfeltétel kielégítése után a mikrofonból való beszéd felismerése csak négy lépést vesz igénybe:

> [!div class="checklist"]
> * Hozzon `SpeechConfig` létre egy objektumot az előfizetési kulcs és a régió alapján.
> * Hozzon `SpeechRecognizer` létre egy objektumot `SpeechConfig` a fenti objektum használatával.
> * Az `SpeechRecognizer` objektum használatával egyetlen lemondás esetén indíthatja el az elismerési folyamatot.
> * Vizsgálja meg `SpeechRecognitionResult` a visszaadott értéket.
