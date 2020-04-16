---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 04/02/2020
ms.author: trbye
ms.openlocfilehash: 36243fd5db76a4706318f41b3e2cb3f557c17189
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81400814"
---
Ebben a rövid útmutatóban a [beszédfelismerési SDK](~/articles/cognitive-services/speech-service/speech-sdk.md) segítségével interaktívan felismerheti a mikrofonbemenetből érkező beszédeket, és leszeretné szerezni a rögzített hang szövegátírását. Ezt a funkciót egyszerűen integrálhatja alkalmazásaiba vagy eszközeibe a gyakori felismerési feladatokhoz, például a beszélgetések átírásához. Összetettebb integrációkhoz is használható, például a Robot keretrendszer és a beszédsdk használatával hangasszisztensek létrehozásához.

Néhány előfeltétel teljesítése után a mikrofonból érkező beszéd felismerése csak négy lépést tesz:

> [!div class="checklist"]
> * Hozzon `SpeechConfig` létre egy objektumot az előfizetési kulcsból és a régióból.
> * Objektum `SpeechRecognizer` létrehozása felülről az `SpeechConfig` objektum használatával.
> * Az `SpeechRecognizer` objektum használatával indítsa el a felismerési folyamat egyetlen utterance (kifejezés).
> * Vizsgálja `SpeechRecognitionResult` meg a visszaküldötteket.
