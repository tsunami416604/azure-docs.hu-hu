---
title: 'Rövid útmutató: Beszédfelolvasás fordítása – Beszédszolgáltatás'
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
ms.openlocfilehash: c5f0a0fe032d18cd4f01aebe9a5c736d6d511a74
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74980480"
---
Ebben a rövid útmutatóban a [beszédfelismerési SDK-t](~/articles/cognitive-services/speech-service/speech-sdk.md) fogja használni a beszéd interaktív fordítására egyik nyelvről a másik nyelvre. Néhány előfeltétel teljesítése után a beszédfelolvasás fordítása csak hat lépést tesz:
> [!div class="checklist"]
> * Hozzon ````SpeechTranslationConfig```` létre egy objektumot az előfizetési kulcsból és a régióból.
> * Frissítse ````SpeechTranslationConfig```` az objektumot a forrás- és célnyelvek megadásához.
> * Frissítse ````SpeechTranslationConfig```` az objektumot a beszédkimeneti hangnevének megadásához.
> * Objektum ````TranslationRecognizer```` létrehozása felülről az ````SpeechTranslationConfig```` objektum használatával.
> * Az ````TranslationRecognizer```` objektum használatával indítsa el a felismerési folyamat egyetlen utterance (kifejezés).
> * Vizsgálja ````TranslationRecognitionResult```` meg a visszaküldötteket.
