---
author: IEvangelist
ms.service: cognitive-services
ms.topic: include
ms.date: 04/02/2020
ms.author: dapine
ms.openlocfilehash: 3775690802c89805ccf9df1ee6d6717a8818213f
ms.sourcegitcommit: 0450ed87a7e01bbe38b3a3aea2a21881f34f34dd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/03/2020
ms.locfileid: "80658420"
---
Ebben a rövid útmutatóban a [beszédfelismerési SDK](~/articles/cognitive-services/speech-service/speech-sdk.md) segítségével interaktívan felismerheti a mikrofonbemenetből érkező beszédeket, és leszeretné szerezni a rögzített hang szövegátírását. Ezt a funkciót egyszerűen integrálhatja alkalmazásaiba vagy eszközeibe a gyakori felismerési feladatokhoz, például a beszélgetések átírásához. Összetettebb integrációkhoz is használható, például a Robot keretrendszer és a beszédsdk használatával hangasszisztensek létrehozásához.

Néhány előfeltétel teljesítése után a mikrofonból érkező beszéd felismerése csak négy lépést tesz:

> [!div class="checklist"]
> * Hozzon `SpeechConfig` létre egy objektumot az előfizetési kulcsból és a régióból.
> * Objektum `SpeechRecognizer` létrehozása felülről az `SpeechConfig` objektum használatával.
> * Az `SpeechRecognizer` objektum használatával indítsa el a felismerési folyamat egyetlen utterance (kifejezés).
> * Vizsgálja `SpeechRecognitionResult` meg a visszaküldötteket.
