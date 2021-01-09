---
author: trevorbye
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 01/27/2020
ms.author: trbye
ms.openlocfilehash: 4725a1a9cf2cb74655a37ac27a0a86f10d7f4bb9
ms.sourcegitcommit: e46f9981626751f129926a2dae327a729228216e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/08/2021
ms.locfileid: "98052847"
---
Ebben a rövid útmutatóban a [SPEECH SDK](~/articles/cognitive-services/speech-service/speech-sdk.md) és a Language UNDERSTANDING (Luis) szolgáltatást fogja használni a mikrofonból rögzített hangadatokból való felismeréshez. Pontosabban a Speech SDK használatával rögzítheti a beszédet és a LUIS egy előre elkészített tartományát, hogy azonosítsa az otthoni automatizálás célját, például a fény bekapcsolását és kikapcsolását. 

Néhány előfeltétel kielégítése után a beszédfelismerés felismerése és a szándékok egy mikrofonból való felismerése csak néhány lépést vesz igénybe:

> [!div class="checklist"]
>
> * Hozzon létre egy `SpeechConfig` objektumot az előfizetési kulcs és a régió alapján.
> * Hozzon létre egy `IntentRecognizer` objektumot a `SpeechConfig` fenti objektum használatával.
> * Az `IntentRecognizer` objektum használatával egyetlen lemondás esetén indíthatja el az elismerési folyamatot.
> * Vizsgálja meg a `IntentRecognitionResult` visszaadott értéket.

