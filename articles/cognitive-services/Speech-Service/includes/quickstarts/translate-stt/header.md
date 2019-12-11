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
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/10/2019
ms.locfileid: "74980941"
---
Ebben a rövid útmutatóban a [SPEECH SDK](~/articles/cognitive-services/speech-service/speech-sdk.md) használatával interaktív módon fordíthatja le a beszédet az egyik nyelvről a másik nyelvre. Néhány előfeltétel kielégítése után a beszéd – szöveg fordítása csak öt lépést vesz igénybe:
> [!div class="checklist"]
> * Hozzon létre egy ````SpeechConfig```` objektumot az előfizetési kulcsból és régióból.
> * Frissítse a ````SpeechConfig```` objektumot a forrás és a cél nyelvének megadásához.
> * Hozzon létre egy ````TranslationRecognizer```` objektumot a fenti ````SpeechConfig```` objektum használatával.
> * A ````TranslationRecognizer```` objektum használatával a felismerési folyamat elindítható egyetlen kilépéshez.
> * Vizsgálja meg a visszaadott ````TranslationRecognitionResult````.
