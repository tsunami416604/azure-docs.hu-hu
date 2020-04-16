---
author: trevorbye
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 01/27/2020
ms.author: trbye
ms.openlocfilehash: 8d70d282ffb1a39f9ffb3eb6addf694ac9d0d060
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81422062"
---
Ebben a rövid útmutatóban a [beszédfelismerési SDK](~/articles/cognitive-services/speech-service/speech-sdk.md) és a nyelvi megértés (LUIS) szolgáltatás segítségével ismerheti fel a mikrofonból rögzített hangadatokból származó szándékokat. Pontosabban a beszédfelismerési SDK használatával fogja rögzíteni a beszédfelismerést, és a LUIS előre összeállított tartományát az otthoni automatizálás imbolygó leképezéseinek azonosításához, például a fény be- és kikapcsolásához. 

Néhány előfeltétel teljesítése után a beszéd felismerése és a mikrofonból származó szándékok azonosítása csak néhány lépést tesz:

> [!div class="checklist"]
>
> * Hozzon `SpeechConfig` létre egy objektumot az előfizetési kulcsból és a régióból.
> * Objektum `IntentRecognizer` létrehozása felülről az `SpeechConfig` objektum használatával.
> * Az `IntentRecognizer` objektum használatával indítsa el a felismerési folyamat egyetlen utterance (kifejezés).
> * Vizsgálja `IntentRecognitionResult` meg a visszaküldötteket.
