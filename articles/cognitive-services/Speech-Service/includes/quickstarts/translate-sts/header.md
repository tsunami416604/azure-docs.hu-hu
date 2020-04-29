---
title: 'Gyors útmutató: beszédfelismerési beszéd – beszédfelismerési szolgáltatás'
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
ms.sourcegitcommit: 34a6fa5fc66b1cfdfbf8178ef5cdb151c97c721c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "74980480"
---
Ebben a rövid útmutatóban a [SPEECH SDK](~/articles/cognitive-services/speech-service/speech-sdk.md) használatával interaktív módon fordíthatja le a beszédet az egyik nyelvről a beszédre egy másik nyelven. Néhány előfeltétel kielégítése után a beszédfelismerés csak hat lépést vesz igénybe:
> [!div class="checklist"]
> * Hozzon ````SpeechTranslationConfig```` létre egy objektumot az előfizetési kulcs és a régió alapján.
> * Az ````SpeechTranslationConfig```` objektum frissítése a forrás és a cél nyelvének megadásához.
> * Az ````SpeechTranslationConfig```` objektum frissítése a beszédfelismerési kimeneti hang nevének megadásához.
> * Hozzon ````TranslationRecognizer```` létre egy objektumot ````SpeechTranslationConfig```` a fenti objektum használatával.
> * Az ````TranslationRecognizer```` objektum használatával egyetlen lemondás esetén indíthatja el az elismerési folyamatot.
> * Vizsgálja meg ````TranslationRecognitionResult```` a visszaadott értéket.
