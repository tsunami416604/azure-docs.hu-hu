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
ms.date: 09/20/2019
ms.author: yulili
ms.openlocfilehash: 805193c08e57ddc18dfca0a78c6b58cf895baec1
ms.sourcegitcommit: 5aefc96fd34c141275af31874700edbb829436bb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/04/2019
ms.locfileid: "74817594"
---
Ebben a rövid útmutatóban a [SPEECH SDK](~/articles/cognitive-services/speech-service/speech-sdk.md) használatával interaktív módon fordíthatja le a beszédet az egyik nyelvről a beszédre egy másik nyelven. Néhány előfeltétel kielégítése után a beszédfelismerés csak hat lépést vesz igénybe:
> [!div class="checklist"]
> * Hozzon létre egy ````SpeechTranslationConfig```` objektumot az előfizetési kulcsból és régióból.
> * Frissítse a ````SpeechTranslationConfig```` objektumot a forrás és a cél nyelvének megadásához.
> * Frissítse a ````SpeechTranslationConfig```` objektumot a beszédfelismerési kimeneti hang nevének megadásához.
> * Hozzon létre egy ````TranslationRecognizer```` objektumot a fenti ````SpeechTranslationConfig```` objektum használatával.
> * A ````TranslationRecognizer```` objektum használatával a felismerési folyamat elindítható egyetlen kilépéshez.
> * Vizsgálja meg a visszaadott ````TranslationRecognitionResult````.
