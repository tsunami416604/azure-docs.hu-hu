---
title: 'Gyors útmutató: beszéd fordítása több nyelvre – beszédfelismerési szolgáltatás'
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
ms.sourcegitcommit: 34a6fa5fc66b1cfdfbf8178ef5cdb151c97c721c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "74981505"
---
Ebben a rövid útmutatóban a [SPEECH SDK](~/articles/cognitive-services/speech-service/speech-sdk.md) használatával interaktív módon fordíthatja le a beszédet az egyik nyelvről a beszédre egy másik nyelven. Néhány előfeltétel kielégítése után a beszédfelismerés több nyelven írt szövegre való fordítása csak hat lépést vesz igénybe:
> [!div class="checklist"]
> * Hozzon ````SpeechTranslationConfig```` létre egy objektumot az előfizetési kulcs és a régió alapján.
> * Az ````SpeechTranslationConfig```` objektum frissítése a beszédfelismerési forrás nyelvének megadásához.
> * Az ````SpeechTranslationConfig```` objektum frissítése több fordítási cél nyelvének megadásához.
> * Hozzon ````TranslationRecognizer```` létre egy objektumot ````SpeechTranslationConfig```` a fenti objektum használatával.
> * Az ````TranslationRecognizer```` objektum használatával egyetlen lemondás esetén indíthatja el az elismerési folyamatot.
> * Vizsgálja meg ````TranslationRecognitionResult```` a visszaadott értéket.
