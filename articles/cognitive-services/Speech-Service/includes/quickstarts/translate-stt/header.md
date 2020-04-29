---
title: 'Gyors útmutató: beszédfelismerési szolgáltatás fordítása'
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
ms.openlocfilehash: d4781808ce8e80f62e86ce1d0c6db9c38b2636d0
ms.sourcegitcommit: 34a6fa5fc66b1cfdfbf8178ef5cdb151c97c721c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "74980941"
---
Ebben a rövid útmutatóban a [SPEECH SDK](~/articles/cognitive-services/speech-service/speech-sdk.md) használatával interaktív módon fordíthatja le a beszédet az egyik nyelvről a másik nyelvre. Néhány előfeltétel kielégítése után a beszéd – szöveg fordítása csak öt lépést vesz igénybe:
> [!div class="checklist"]
> * Hozzon ````SpeechConfig```` létre egy objektumot az előfizetési kulcs és a régió alapján.
> * Az ````SpeechConfig```` objektum frissítése a forrás és a cél nyelvének megadásához.
> * Hozzon ````TranslationRecognizer```` létre egy objektumot ````SpeechConfig```` a fenti objektum használatával.
> * Az ````TranslationRecognizer```` objektum használatával egyetlen lemondás esetén indíthatja el az elismerési folyamatot.
> * Vizsgálja meg ````TranslationRecognitionResult```` a visszaadott értéket.
