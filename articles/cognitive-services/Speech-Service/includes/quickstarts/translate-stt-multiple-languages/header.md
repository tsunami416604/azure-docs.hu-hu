---
title: 'Rövid útmutató: Beszéd fordítása több nyelvre – Beszédszolgáltatás'
titleSuffix: Azure Cognitive Services
description: TBD
services: cognitive-services
author: yulin-li
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 12/09/2019
ms.author: yulili
ms.openlocfilehash: 8c8cbc4e4f531d7a06ae3a33c33df9264c2cc6f2
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74981505"
---
Ebben a rövid útmutatóban a [beszédfelismerési SDK-t](~/articles/cognitive-services/speech-service/speech-sdk.md) fogja használni a beszéd interaktív fordítására egyik nyelvről a másik nyelvre. Néhány előfeltétel teljesítése után a beszéd több nyelven történő szövegre fordítása csak hat lépést tesz:
> [!div class="checklist"]
> * Hozzon ````SpeechTranslationConfig```` létre egy objektumot az előfizetési kulcsból és a régióból.
> * Frissítse ````SpeechTranslationConfig```` az objektumot a beszédfelismerés forrásnyelvének megadásához.
> * Frissítse ````SpeechTranslationConfig```` az objektumot, hogy több fordítási célnyelvet adjon meg.
> * Objektum ````TranslationRecognizer```` létrehozása felülről az ````SpeechTranslationConfig```` objektum használatával.
> * Az ````TranslationRecognizer```` objektum használatával indítsa el a felismerési folyamat egyetlen utterance (kifejezés).
> * Vizsgálja ````TranslationRecognitionResult```` meg a visszaküldötteket.
