---
author: trevorbye
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 01/27/2020
ms.author: trbye
ms.openlocfilehash: 8d70d282ffb1a39f9ffb3eb6addf694ac9d0d060
ms.sourcegitcommit: 34a6fa5fc66b1cfdfbf8178ef5cdb151c97c721c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "81422062"
---
Ebben a rövid útmutatóban a [SPEECH SDK](~/articles/cognitive-services/speech-service/speech-sdk.md) és a Language UNDERSTANDING (Luis) szolgáltatást fogja használni a mikrofonból rögzített hangadatokból való felismeréshez. Pontosabban a Speech SDK használatával rögzítheti a beszédet és a LUIS egy előre elkészített tartományát, hogy azonosítsa az otthoni automatizálás célját, például a fény bekapcsolását és kikapcsolását. 

Néhány előfeltétel kielégítése után a beszédfelismerés felismerése és a szándékok egy mikrofonból való felismerése csak néhány lépést vesz igénybe:

> [!div class="checklist"]
>
> * Hozzon `SpeechConfig` létre egy objektumot az előfizetési kulcs és a régió alapján.
> * Hozzon `IntentRecognizer` létre egy objektumot `SpeechConfig` a fenti objektum használatával.
> * Az `IntentRecognizer` objektum használatával egyetlen lemondás esetén indíthatja el az elismerési folyamatot.
> * Vizsgálja meg `IntentRecognitionResult` a visszaadott értéket.
