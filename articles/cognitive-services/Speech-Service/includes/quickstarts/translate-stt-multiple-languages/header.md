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
ms.date: 09/20/2019
ms.author: yulili
ms.openlocfilehash: 6c65fd9a504b5f399afa99280ca2a75b476c750c
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/04/2019
ms.locfileid: "73502813"
---
Ebben a rövid útmutatóban a [SPEECH SDK](~/articles/cognitive-services/speech-service/speech-sdk.md) használatával interaktív módon fordíthatja le a beszédet az egyik nyelvről a beszédre egy másik nyelven. Néhány előfeltétel kielégítése után a beszédfelismerés több nyelven írt szövegre való fordítása csak hat lépést vesz igénybe:
> [!div class="checklist"]
> * Hozzon létre egy ````SpeechTranslationConfig```` objektumot az előfizetési kulcsból és régióból.
> * Frissítse a ````SpeechTranslationConfig```` objektumot a beszédfelismerési forrás nyelvének megadásához.
> * A ````SpeechTranslationConfig```` objektum frissítése több fordítási cél nyelvének megadásához.
> * Hozzon létre egy ````TranslationRecognizer```` objektumot a fenti ````SpeechTranslationConfig```` objektum használatával.
> * A ````TranslationRecognizer```` objektum használatával a felismerési folyamat elindítható egyetlen kilépéshez.
> * Vizsgálja meg a visszaadott ````TranslationRecognitionResult````.
