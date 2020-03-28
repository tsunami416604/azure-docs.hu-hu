---
author: IEvangelist
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 01/27/2020
ms.author: dapine
ms.openlocfilehash: d59a55a9b6d57d90ac6ae893b05e1064c11eb58b
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76900347"
---
Ebben a rövid útmutatóban a [beszédfelismerési SDK](~/articles/cognitive-services/speech-service/speech-sdk.md) és a nyelvi megértés (LUIS) szolgáltatás segítségével ismerheti fel a mikrofonból rögzített hangadatokból származó szándékokat. Pontosabban a beszédfelismerési SDK használatával fogja rögzíteni a beszédfelismerést, és a LUIS előre összeállított tartományát az otthoni automatizálás imbolygó leképezéseinek azonosításához, például a fény be- és kikapcsolásához. 

Néhány előfeltétel teljesítése után a beszéd felismerése és a mikrofonból származó szándékok azonosítása csak néhány lépést tesz:

> [!div class="checklist"]
>
> * Hozzon `SpeechConfig` létre egy objektumot az előfizetési kulcsból és a régióból.
> * Objektum `IntentRecognizer` létrehozása felülről az `SpeechConfig` objektum használatával.
> * Az `IntentRecognizer` objektum használatával indítsa el a felismerési folyamat egyetlen utterance (kifejezés).
> * Vizsgálja `IntentRecognitionResult` meg a visszaküldötteket.
